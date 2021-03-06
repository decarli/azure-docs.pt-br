---
title: Banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente
description: O banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente permite que você Bring Your Own Key (BYOK) para proteção de dados em repouso e permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 7c54b3010b42d56ffa9b701b76c7aef51095404c
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028654"
---
# <a name="azure-database-for-mysql-data-encryption-with-customer-managed-key"></a>Banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em contato com AskAzureDBforMySQL@service.microsoft.com.

O banco de dados do Azure para MySQL Data Encryption com chave gerenciada pelo cliente permite que você Bring Your Own Key (BYOK) para proteção de dados em repouso e permite que as organizações implementem a separação de tarefas no gerenciamento de chaves e dados. Com a criptografia gerenciada pelo cliente, você é responsável por e em um controle total do ciclo de vida de uma chave (criação de chave, carregamento, rotação, exclusão), permissões de uso de chave e auditoria de operações em chaves.

Para o banco de dados do Azure para MySQL, a criptografia é definida no nível do servidor. Com essa forma de criptografia de dados, a chave é usada na criptografia da DEK (chave de criptografia de banco de dado), que é uma chave assimétrica gerenciada pelo cliente, armazenada em um [akv (Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md)de Propriedade do cliente), um sistema de gerenciamento de chaves externas baseado em nuvem. O AKV é altamente disponível e fornece armazenamento seguro escalonável para chaves de criptografia RSA, opcionalmente apoiada por HSMs (módulos de segurança de hardware) validados pelo FIPS 140-2 nível 2. Ele não permite acesso direto a uma chave armazenada, mas fornece serviços de criptografia/descriptografia usando a chave para as entidades autorizadas. A chave pode ser gerada pelo Key Vault, importada ou [transferida para o Key Vault de um dispositivo HSM local](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MySQL dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="benefits"></a>Benefícios

A criptografia de dados para o Azure Database para MySQL oferece os seguintes benefícios:

* Maior transparência, controle granular e gerenciamento para a chave de criptografia.
* Gerenciamento central e organização de chaves hospedando-as em Azure Key Vault.
* Capacidade de implementar a separação de tarefas no gerenciamento de chaves e dados dentro da organização.
* Gerenciamento de chaves separado do gerenciamento de dados em uma organização, de modo que Key Vault administrador possa revogar permissões de acesso de chave para tornar o banco de dados criptografado inacessível.
* Maior confiança de seus clientes finais, pois Azure Key Vault é projetada de modo que a Microsoft não possa ver nem extrair as chaves de criptografia.

## <a name="terminology-and-description"></a>Descrição e terminologia

**DEK (chave de criptografia de dados)** – uma chave de aes256s simétrica usada para criptografar uma partição ou um bloco de dados. Criptografar cada bloco de dados com uma chave diferente torna os ataques de análise de criptografia mais difíceis. O acesso a DEKs é necessário pelo provedor de recursos ou instância do aplicativo que criptografa e descriptografa um bloco específico. Quando um DEK é substituído por uma nova chave, somente os dados em seu bloco associado devem ser criptografados novamente com a nova chave.

Chave de **criptografia de chave (Kek)** -uma chave de criptografia usada para criptografar as chaves de criptografia de dados. O uso de uma chave de criptografia de chave que nunca deixa Key Vault, permite que as próprias chaves de criptografia de dados sejam criptografadas e controladas. A entidade que tem acesso ao KEK pode ser diferente da entidade que requer o DEK. Uma vez que o KEK é necessário para descriptografar os DEKs, o KEK é efetivamente um ponto único pelo qual os DEKs podem ser efetivamente excluídos pela exclusão do KEK.

As chaves de criptografia de dados (DEK), criptografadas com as chaves de criptografia de chave, são armazenadas separadamente e apenas uma entidade com acesso à chave de criptografia de chave pode descriptografar essas chaves de criptografia de dados. Para obter mais informações, consulte [segurança em criptografia em repouso](../security/fundamentals/encryption-atrest.md).

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Como funciona a criptografia de dados com a chave gerenciada pelo cliente

![Visão geral de traga sua própria chave](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Para que um servidor MySQL seja capaz de usar chaves gerenciadas pelo cliente armazenadas em AKV para criptografia do DEK, um administrador de Key Vault precisa conceder os seguintes direitos de acesso ao servidor usando sua identidade exclusiva:

* **Get** -para recuperar a parte pública e as propriedades da chave no Key Vault
* **wrapKey** -para poder CRIPTOGRAFAr DEK
* **unwrapKey** -para ser capaz de descriptografar DEK

Key Vault administrador também pode [habilitar o registro em log de eventos de auditoria de Key Vault](../azure-monitor/insights/azure-key-vault.md), para que possam ser auditados posteriormente.

Quando o servidor é configurado para usar a chave gerenciada pelo cliente que é armazenada no Key Vault, o servidor envia o DEK para a Key Vault para criptografias. Key Vault retorna o DEK criptografado, que é armazenado no banco de dados do usuário. Da mesma forma, quando necessário, o servidor envia DEK protegidas para a Key Vault para descriptografia. Os auditores podem usar Azure Monitor para examinar Key Vault logs do AuditEvent, se o registro em log estiver habilitado.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Requisitos para a configuração da criptografia de dados para o Azure Database para MySQL

### <a name="requirements-for-configuring-akv"></a>Requisitos para configurar o AKV

* Key Vault e o banco de dados do Azure para MySQL devem pertencer ao mesmo locatário do AAD (Azure Active Directory). Não há suporte para interações de servidor e Key Vault entre locatários. A movimentação de recursos posteriormente exige que você reconfigure a criptografia de dados. Saiba mais sobre como mover recursos.
* O recurso de exclusão reversível deve ser habilitado no Key Vault, para proteger contra a exclusão de dados de chave acidental (ou Key Vault). Os recursos excluídos por software são mantidos por 90 dias, a menos que sejam recuperados ou limpos pelo cliente enquanto isso. As ações de recuperação e limpeza têm suas próprias permissões associadas em uma política de acesso Key Vault. O recurso de exclusão reversível está desativado por padrão e pode ser habilitado por meio do PowerShell ou da CLI. Ele não pode ser habilitado via portal do Azure.
* Conceda ao banco de dados do Azure para MySQL acesso ao Key Vault com as permissões **Get, wrapKey, unwrapKey** usando sua identidade gerenciada exclusiva. Ao usar portal do Azure, a identificação exclusiva é criada automaticamente quando a criptografia de dados é habilitada no MySQL. Consulte [Configurar a criptografia de dados para MySQL](howto-data-encryption-portal.md) para obter instruções passo a passo detalhadas ao usar portal do Azure.

* Ao usar o firewall com AKV, você deve habilitar *a opção permitir que serviços confiáveis da Microsoft ignorem o firewall*.

### <a name="requirements-for-configuring-customer-key"></a>Requisitos para configurar a chave do cliente

* A chave gerenciada pelo cliente a ser usada para criptografar o DEK só pode ser assimétrica, RSA 2028.
* A data de ativação da chave (se definido) deve ser uma data e uma hora no passado. A data de validade (se definido) deve ser uma data e hora futura.
* A chave deve estar no estado *habilitado* .
* Se você estiver importando uma chave existente para o Key Vault, certifique-se de fornecê-la nos formatos de arquivo com suporte (`.pfx`, `.byok``.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Recomendações ao usar a criptografia de dados usando a chave gerenciada pelo cliente

### <a name="recommendation-for-configuring-akv"></a>Recomendação para configurar o AKV

* Defina um bloqueio de recurso no Key Vault para controlar quem pode excluir esse recurso crítico e impedir a exclusão acidental ou não autorizada. Saiba mais sobre bloqueios de recursos.
* Habilitar a auditoria e os relatórios em todas as chaves de criptografia: Key Vault fornece logs que são fáceis de injetar em outras ferramentas de gerenciamento de eventos e informações de segurança. Azure Monitor Log Analytics é um exemplo de um serviço que já está integrado.

* Verifique se o Key Vault e o banco de dados do Azure para MySQL residem na mesma região para garantir um acesso mais rápido para operações de encapsulamento/desencapsulamento DEK.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Recomendação para configurar a chave gerenciada pelo cliente

* Mantenha uma cópia da chave gerenciada pelo cliente (KEK) em um local seguro ou a caução para o serviço de caução.

* Se a chave for gerada no Key Vault, crie um backup de chave antes de usar a chave em AKV pela primeira vez. O backup pode ser restaurado somente para um Azure Key Vault. Saiba mais sobre o comando [backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) .

## <a name="inaccessible-customer-managed-key-condition"></a>Condição de chave gerenciada pelo cliente inacessível

Quando a criptografia de dados é configurada com a chave gerenciada pelo cliente no Azure Key Vault (AKV), o acesso contínuo a essa chave é necessário para que o servidor permaneça online. Se o servidor perder o acesso à chave gerenciada pelo cliente em AKV, em 10 minutos, o servidor começará a negar todas as conexões com a mensagem de erro correspondente e alterará o estado do servidor para **inacessível**. A única ação permitida em um banco de dados no estado inacessível é excluí-lo.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Revogação de acesso de chave acidental do Azure Key Vault (AKV)

Pode acontecer que alguém com direitos de acesso suficientes para o Key Vault desabilita acidentalmente o acesso do servidor à chave por:

* Key Vault revogando as permissões Get, wrapKey, unwrapKey do servidor
* excluindo a chave
* excluindo o Key Vault
* alterando as regras de firewall de Key Vault

* excluindo a identidade gerenciada do servidor no Azure Active Directory

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Monitoramento da chave gerenciada pelo cliente no Key Vault

Para monitorar o estado do banco de dados e habilitar o alerta para perda de acesso ao protetor do TDE, configure os seguintes recursos do Azure:

* [Azure Resource Health](../service-health/resource-health-overview.md) -um banco de dados inacessível que perdeu o acesso à chave do cliente aparecerá como "inacessível" depois que a primeira conexão com o banco de dados for negada.
* [Log de atividades](../service-health/alerts-activity-log-service-notifications.md) – quando o acesso à chave do cliente no Key Vault gerenciado pelo cliente falha, as entradas são adicionadas ao log de atividades. A criação de alertas para esses eventos permitirá que você reinstale o acesso assim que possível.

* Os [grupos de ações](../azure-monitor/platform/action-groups.md) podem ser definidos para enviar notificações e alertas com base em suas preferências, por exemplo, email/SMS/Push/voz, aplicativo lógico, webhook, ITSM ou runbook de automação.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Restaurar e replicar com a chave gerenciada do cliente no Key Vault

Depois que um banco de dados do Azure para MySQL é criptografado com a chave gerenciada do cliente armazenada no Key Vault, qualquer cópia recém-criada do servidor (por meio da operação de restauração local ou geográfica ou por meio de réplicas de leitura) também é criptografada com a chave gerenciada do mesmo cliente. No entanto, eles podem ser alterados para refletir a chave gerenciada do novo cliente para criptografia. Quando a chave gerenciada pelo cliente é alterada, os backups antigos do servidor serão iniciados usando a chave mais recente.

Para evitar problemas ao estabelecer a configuração da criptografia de dados gerenciada pelo cliente durante a restauração ou a criação da réplica de leitura, é importante seguir estas etapas no servidor mestre e restaurações/réplica:

* Inicie o processo de restauração ou leitura de réplica do banco de dados mestre do Azure para MySQL.
* O servidor recém-criado (restaurado/réplica) é mantido como um estado inacessível, já que sua identidade exclusiva ainda não recebeu permissões para o Azure Key Vault (AKV)
* No servidor restaurado/de réplica, revalide a chave gerenciada pelo cliente nas configurações de criptografia de dados para garantir que o servidor recém-criado receba permissões de encapsulamento/desencapsulamento para a chave armazenada em AKV.

* Ambas as etapas acima devem ser feitas para garantir que a criptografia de dados seja preservada no mestre, bem como no servidor restaurado/de réplica.

## <a name="next-steps"></a>Próximos passos

Saiba como [Configurar a criptografia de dados com a chave gerenciada pelo cliente para o banco de dado do Azure para MySQL usando o portal do Azure](howto-data-encryption-portal.md).

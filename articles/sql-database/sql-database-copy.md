---
title: Copiar um banco de dados
description: Crie uma cópia consistente transacionalmente de um banco de dados SQL do Azure existente no mesmo servidor ou em um servidor diferente.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: e1df345fb9a89972ad1857a937c22d6e10ad1fba
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289400"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Fazer uma cópia consistente transicionalmente de um banco de dados SQL do Azure

O banco de dados SQL do Azure fornece vários métodos para criar uma cópia transacionalmente consistente de um banco de dados SQL do Azure existente ([banco de dados individual](sql-database-single-database.md)) no mesmo servidor ou em um servidor diferente. Você pode copiar um Banco de Dados SQL usando o Portal do Azure, o PowerShell ou o T-SQL.

## <a name="overview"></a>Visão Geral

Uma cópia do banco de dados é um instantâneo do banco de dados de origem no momento da solicitação de cópia. Você pode selecionar o mesmo servidor ou um servidor diferente. Além disso, você pode optar por manter sua camada de serviço e o tamanho da computação ou usar um tamanho de computação diferente dentro da mesma camada de serviço (edição). Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Neste ponto, é possível atualizar ou fazer o downgrade para qualquer edição. Os logons, os usuários e as permissões podem ser gerenciados independentemente. A cópia é criada usando a tecnologia de replicação geográfica e, após a conclusão da propagação, o link de replicação geográfica é encerrado automaticamente. Todos os requisitos para usar a replicação geográfica se aplicam à operação de cópia do banco de dados. Consulte [visão geral da replicação geográfica ativa](sql-database-active-geo-replication.md) para obter detalhes.

> [!NOTE]
> [Backups de banco de dados automatizados](sql-database-automated-backups.md) são usados quando você cria uma cópia de banco de dados.

## <a name="logins-in-the-database-copy"></a>Logons na cópia do banco de dados

Quando você copia um banco de dados no mesmo servidor do Banco de Dados SQL, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.  

Quando você copia um banco de dados para um servidor do Banco de Dados SQL diferente, a entidade de segurança no novo servidor torna-se a proprietária do banco de dados no novo banco de dados. Se você usar [usuários de banco de dados independente](sql-database-manage-logins.md) para acesso aos dados, garanta que os bancos de dados primários e secundários sempre tenham as mesmas credenciais de usuário. Portanto, depois que a cópia for concluída, será possível acessá-la imediatamente com as mesmas credenciais.

Se você usar o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), será possível acabar completamente com a necessidade de gerenciar credenciais na cópia. No entanto, quando você copia o banco de dados para um novo servidor, o acesso baseado em logon geralmente não funciona, porque os logons não existirão no novo servidor. Para saber como gerenciar logons quando você copia um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre)](sql-database-geo-replication-security-config.md).

Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados. Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Cópia do banco de dados usando o Portal do Azure

Para copiar um banco de dados usando o Portal do Azure, abra a página do banco de dados e clique em **Copiar**.

   ![Cópia do banco de dados](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Cópia de banco de dados usando o PowerShell

Para copiar um banco de dados, use os exemplos a seguir.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para o PowerShell, use o cmdlet [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) .

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell (RM) ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. O módulo AzureRM continuará a receber correções de bugs até pelo menos dezembro de 2020.  Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. Para obter mais informações sobre sua compatibilidade, consulte [apresentando o novo módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente depois que a solicitação é aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, remova o banco de dados de destino usando o cmdlet [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

A cópia do banco de dados é uma operação assíncrona, mas o banco de dados de destino é criado imediatamente depois que a solicitação é aceita. Se você precisar cancelar a operação de cópia enquanto ainda estiver em andamento, remova o banco de dados de destino usando o comando [AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete) .

* * *

Para ver um script de exemplo completo, consulte [Copiar um banco de dados para um novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="rbac-roles-to-manage-database-copy"></a>Funções de RBAC para gerenciar cópia de banco de dados

Para criar uma cópia de banco de dados, você precisará estar nas seguintes funções

- Proprietário da assinatura ou ter
- SQL Server função colaborador ou
- Função personalizada nos bancos de dados de origem e de destino com a seguinte permissão:

   Microsoft. SQL/servidores/bancos de dados/ler Microsoft. SQL/servidores/bancos de dados/gravar

Para cancelar uma cópia de banco de dados, você precisará estar nas seguintes funções

- Proprietário da assinatura ou ter
- SQL Server função colaborador ou
- Função personalizada nos bancos de dados de origem e de destino com a seguinte permissão:

   Microsoft. SQL/servidores/bancos de dados/ler Microsoft. SQL/servidores/bancos de dados/gravar

Para gerenciar a cópia de banco de dados usando portal do Azure, você também precisará das seguintes permissões:

   Microsoft. Resources/subscriptions/Resources/Read Microsoft. Resources/subscriptions/Resources/inscrições/Write Microsoft. Resources/Implantations/Read Microsoft. Resources/Implantations/Write Microsoft. Resources/Implantations/operationstatuses/Read

Se você quiser ver as operações em implantações no grupo de recursos no portal, operações em vários provedores de recursos, incluindo operações SQL, você precisará dessas funções RBAC adicionais:

   Microsoft. Resources/subscriptions/resourcegroups/implantações/operações/ler Microsoft. Resources/subscriptions/resourcegroups/Implantations/operationstatuses/Read

## <a name="copy-a-database-by-using-transact-sql"></a>Cópia de banco de dados usando o Transact-SQL

Faça logon no banco de dados mestre com o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem-sucedida, os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager. Para saber mais sobre logons e como se conectar ao servidor, confira [Gerenciar logons](sql-database-manage-logins.md).

Inicie a cópia do banco de dados de origem com a instrução [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . A execução dessa instrução inicia o processo de cópia do banco de dados. Como a cópia um banco de dados é um processo assíncrono, a instrução CREATE DATABASE retorna antes da conclusão da cópia do banco de dados.

### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor

Faça logon no banco de dados mestre com o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Para que a cópia do banco de dados seja bem-sucedida, os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager.

Esse comando copia o Banco de dados 1 para um novo banco de dados chamado Database2 (Banco de dados 2) no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino, o servidor do Banco de Dados SQL em que o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha do proprietário do banco de dados de origem no servidor do Banco de Dados SQL de origem. O logon no servidor de destino também deve ser membro da função dbmanager ou ser o logon principal no nível de servidor.

Esse comando copia o Database1 no servidor 1- para um novo banco de dados chamado Database2 no servidor 2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Os firewalls de servidores devem ser configurados para permitir a conexão de entrada do IP do cliente que emite o comando de cópia T-SQL.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copiar um banco de dados SQL para uma assinatura diferente

Você pode usar as etapas descritas na seção anterior para copiar seu banco de dados para um servidor de banco de dados SQL em uma assinatura diferente. Certifique-se de usar um logon que tenha o mesmo nome e senha que o proprietário do banco de dados de origem e ele é membro da função dbmanager ou é o logon da entidade de segurança no nível do servidor. 

> [!NOTE]
> O [portal do Azure](https://portal.azure.com) não oferece suporte à cópia para uma assinatura diferente porque o portal chama a API do ARM e usa os certificados de assinatura para acessar os dois servidores envolvidos na replicação geográfica.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Monitorar o andamento da operação de cópia

Monitore o processo de cópia consultando as exibições sys.databases e sys.dm_database_copies. Enquanto a cópia estiver em andamento, a coluna **state_desc** da exibição sys.databases para o novo banco de dados é definida como **COPYING**.

* Se a cópia falhar, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **SUSPECT**. Execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
* Se a cópia for bem-sucedida, a coluna **state_desc** da exibição sys.databases para o novo banco de dados será definida como **ONLINE**. A cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.

> [!NOTE]
> Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, a execução da instrução DROP DATABASE no banco de dados de origem também cancelará o processo de cópia.

> [!IMPORTANT]
> Se você precisar criar uma cópia com um SLO substancialmente menor do que a origem, o banco de dados de destino poderá não ter recursos suficientes para concluir o processo de propagação e isso poderá fazer com que a operação de cópia falhe. Nesse cenário, use uma solicitação de restauração geográfica para criar uma cópia em um servidor diferente e/ou em uma região diferente. Consulte [recuperar um banco de dados SQL do Azure usando backups de banco de dados](sql-database-recovery-using-backups.md#geo-restore) para obter mais informações.

## <a name="resolve-logins"></a>Resolver logons

Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para logons no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx). Veja também [Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).

Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se tornará o proprietário do novo banco de dados e receberá um novo identificador de segurança (SID). Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados, poderá fazer logon no novo banco de dados.

Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor do Banco de Dados SQL diferente, confira [How to manage Azure SQL database security after disaster recovery](sql-database-geo-replication-security-config.md) (Como gerenciar a segurança do Banco de Dados SQL do Azure após a recuperação de desastre).

## <a name="database-copy-errors"></a>Erros de cópia de banco de dados

Os erros a seguir podem ser encontrados durante a cópia de um banco de dados no Banco de Dados SQL do Azure. Para saber mais, confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md).

| Código do erro | Gravidade | Description |
| ---:| ---:|:--- |
| 40635 |16 |O cliente com endereço IP “%.&#x2a;ls” está desabilitado temporariamente. |
| 40637 |16 |A criação da cópia do banco de dados está desabilitada no momento. |
| 40561 |16 |Falha na cópia do banco de dados. O banco de dados de origem ou de destino não existe. |
| 40562 |16 |Falha na cópia do banco de dados. O banco de dados de origem foi descartado. |
| 40563 |16 |Falha na cópia do banco de dados. O banco de dados de destino foi descartado. |
| 40564 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40565 |16 |Falha na cópia do banco de dados. Não é permitida mais de uma cópia simultânea do banco de dados com a mesma origem. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40566 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40567 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente. |
| 40568 |16 |Falha na cópia do banco de dados. O banco de dados de origem tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40569 |16 |Falha na cópia do banco de dados. O banco de dados de destino tornou-se indisponível. Remova o banco de dados de destino e tente novamente. |
| 40570 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |
| 40571 |16 |Falha na cópia do banco de dados devido a um erro interno. Remova o banco de dados de destino e tente novamente mais tarde. |

## <a name="next-steps"></a>Próximos passos

- Para obter informações sobre logons, consulte [Gerenciar logons](sql-database-manage-logins.md) e [Como gerenciar a segurança de Banco de Dados SQL do Azure após a recuperação de desastres](sql-database-geo-replication-security-config.md).
- Para exportar um banco de dados, consulte [Exportar o banco de dados para um BACPAC](sql-database-export.md).

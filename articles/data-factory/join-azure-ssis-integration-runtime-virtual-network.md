---
title: Unir o runtime de integração do Azure-SSIS a uma rede virtual
description: Saiba como unir um tempo de execução de integração do Azure-SSIS a uma rede virtual do Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: fec34c54971878178b2a5ea4548ad20d3b51b104
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119870"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir o runtime de integração do Azure-SSIS a uma rede virtual

Ao usar o SQL Server Integration Services (SSIS) no Azure Data Factory, você deve unir seu IR (Integration Runtime) do Azure-SSIS a uma rede virtual do Azure nos seguintes cenários:

- Você deseja se conectar a armazenamentos de dados locais de pacotes do SSIS que são executados no Azure-SSIS IR sem configurar ou gerenciar um IR autohospedado como proxy. 

- Você deseja hospedar o SSISDB (banco de dados de catálogo do SSIS) em um banco de dados SQL do Azure com regras de firewall de IP/pontos de extremidade de serviço de rede virtual ou uma instância gerenciada com um ponto de extremidade privado. 

- Você deseja se conectar aos recursos do Azure configurados com pontos de extremidade de serviço de rede virtual de pacotes do SSIS que são executados no seu Azure-SSIS IR.

- Você deseja se conectar a armazenamentos de dados/recursos configurados com regras de firewall de IP de pacotes do SSIS que são executados no seu Azure-SSIS IR.

Data Factory permite que você ingresse seu Azure-SSIS IR em uma rede virtual criada por meio do modelo de implantação clássico ou do modelo de implantação de Azure Resource Manager.

> [!IMPORTANT]
> A rede virtual clássica está sendo preterida, portanto, use a rede virtual Azure Resource Manager em vez disso.  Se você já usa a rede virtual clássica, alterne para a rede virtual Azure Resource Manager assim que possível.

O tutorial [Configurando um tempo de execução de integração (ir) do Azure-SQL Server Integration Services (SSIS) para ingressar em uma rede virtual](tutorial-deploy-ssis-virtual-network.md) mostra as etapas mínimas por meio de portal do Azure. Este artigo expande o tutorial e descreve todas as tarefas opcionais:

- Se você estiver usando a rede virtual (clássica).
- Se você trazer seus próprios endereços IP públicos para o Azure-SSIS IR.
- Se você usar seu próprio servidor DNS (sistema de nomes de domínio).
- Se você usar um NSG (grupo de segurança de rede) na sub-rede.
- Se você usar o Azure ExpressRoute ou uma rota definida pelo usuário (UDR).
- Se você usar Azure-SSIS IR personalizadas.
- Se você usar o provisionamento do Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Acessar armazenamentos de dados locais

Se seus pacotes SSIS acessarem armazenamentos de dados locais, você poderá ingressar seu Azure-SSIS IR em uma rede virtual que esteja conectada à rede local. Ou você pode configurar e gerenciar um IR auto-hospedado como proxy para seu Azure-SSIS IR. Para obter mais informações, consulte [configurar um ir auto-hospedado como um proxy para um Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Ao ingressar seu Azure-SSIS IR em uma rede virtual, lembre-se destes pontos importantes: 

- Se nenhuma rede virtual estiver conectada à sua rede local, primeiro crie uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu Azure-SSIS ir ingresse. Em seguida, configure uma [conexão de gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) site a site ou conexão de [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) dessa rede virtual para sua rede local. 

- Se uma rede virtual Azure Resource Manager já estiver conectada à sua rede local no mesmo local que o Azure-SSIS IR, você poderá unir o IR à rede virtual. 

- Se uma rede virtual clássica já estiver conectada à sua rede local em um local diferente do seu Azure-SSIS IR, você poderá criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu Azure-SSIS ir ingresse. Em seguida, configure uma conexão entre [rede virtual clássica e Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se uma rede virtual Azure Resource Manager já estiver conectada à sua rede local em um local diferente do seu Azure-SSIS IR, você poderá primeiro criar uma [rede virtual Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para sua Azure-SSIS ir para ingressar. Em seguida, configure uma conexão de rede virtual de Azure Resource Manager para Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedando o catálogo do SSIS no banco de dados SQL

Se você hospedar seu catálogo do SSIS em um banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual, certifique-se de ingressar seu Azure-SSIS IR na mesma rede virtual e sub-rede.

Se você hospedar seu catálogo do SSIS em uma instância gerenciada com um ponto de extremidade privado, certifique-se de ingressar seu Azure-SSIS IR na mesma rede virtual, mas em uma sub-rede diferente da instância gerenciada. Para unir seu Azure-SSIS IR a uma rede virtual diferente da instância gerenciada, recomendamos o emparelhamento de rede virtual (que é limitado à mesma região) ou uma conexão da rede virtual à rede virtual. Para obter mais informações, consulte [conectar seu aplicativo à instância gerenciada do banco de dados SQL do Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Se seus pacotes do SSIS acessam recursos do Azure que dão suporte a [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) e você deseja proteger o acesso a esses recursos de Azure-SSIS ir, você pode unir seu Azure-SSIS ir a uma sub-rede de rede virtual configurada para pontos de extremidade de serviço de rede virtual e adicionar uma regra de rede virtual aos recursos relevantes do Azure para permitir o acesso da mesma sub-rede.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Acesso a fontes de dados protegidas pela regra de firewall IP

Se seus pacotes SSIS acessarem armazenamentos de dados/recursos que permitem apenas endereços IP públicos estáticos específicos e você quiser proteger o acesso a esses recursos de Azure-SSIS IR, você pode colocar seus próprios [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) para Azure-SSIS ir ao adicioná-lo a uma rede virtual e, em seguida, adicionar uma regra de firewall IP aos recursos relevantes para permitir o acesso a partir desses endereços IP.

Em todos os casos, a rede virtual pode ser implantada somente por meio do modelo de implantação Azure Resource Manager.

As seções a seguir apresentam mais detalhes. 

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Configure sua rede virtual para atender a estes requisitos: 

- Verifique se `Microsoft.Batch` é um provedor registrado na assinatura da sub-rede da sua rede virtual que hospeda o Azure-SSIS IR. Se você usar uma rede virtual clássica, adicione também `MicrosoftAzureBatch` à função de colaborador da máquina virtual clássica para essa rede virtual. 

- Certifique-se de ter as permissões necessárias. Para obter mais informações, consulte [configurar permissões](#perms).

- Selecione a sub-rede correta para hospedar o IV de SSIS do Azure. Para obter mais informações, consulte [selecionar a sub-rede](#subnet). 

- Se você colocar seus próprios endereços IP públicos para o Azure-SSIS IR, consulte [selecionar os endereços IP públicos estáticos](#publicIP)

- Se você usar seu próprio servidor DNS (sistema de nomes de domínio) na rede virtual, consulte [Configurar o servidor DNS](#dns_server). 

- Se você usar um NSG (grupo de segurança de rede) na sub-rede, consulte [configurar um NSG](#nsg). 

- Se você usar o Azure ExpressRoute ou uma UDR (rota definida pelo usuário), consulte [usar o Azure expressroute ou um UDR](#route). 

- Verifique se o grupo de recursos da rede virtual (ou o grupo de recursos endereços IP públicos se você colocar seus próprios endereços IP públicos) pode criar e excluir determinados recursos de rede do Azure. Para obter mais informações, consulte [Configurar o grupo de recursos](#resource-group). 

- Se você personalizar seu Azure-SSIS IR conforme descrito em [instalação personalizada para Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), seus nós de Azure-SSIS ir obterão endereços IP privados de um intervalo predefinido de 172.16.0.0 para 172.31.255.255. Portanto, certifique-se de que os intervalos de endereços IP privados de suas redes virtuais ou locais não colidem com esse intervalo.

Este diagrama mostra as conexões necessárias para seu Azure-SSIS IR:

![IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Configurar permissões

O usuário que cria a Azure-SSIS IR deve ter as seguintes permissões:

- Se você ingressar no seu SSIS IR em uma rede virtual do Azure Resource Manager, terá duas opções:

  - Use a função de colaborador de rede interna. Essa função vem com a permissão _Microsoft.Network/\*_ , que tem um escopo muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a permissão _Microsoft.Network/virtualNetworks/\*/join/action_. Se você também quiser colocar seus próprios endereços IP públicos para Azure-SSIS IR ao se juntar a uma rede virtual Azure Resource Manager, inclua a permissão _Microsoft. Network/publicIPAddresses/*/Join/Action_ na função.

- Se você estiver unindo o IR do SSIS a uma rede virtual clássica, recomendamos que você use a função de colaborador de máquina virtual clássica interna. Caso contrário, você terá que definir uma função personalizada que inclua a permissão para ingressar na rede virtual.

### <a name="subnet"></a>Selecione a Sub-rede.

Ao escolher uma sub-rede: 

- Não selecione GatewaySubnet para implantar um Azure-SSIS IR. Ele é dedicado a gateways de rede virtual. 

- Verifique se a sub-rede selecionada tem espaço de endereço disponível suficiente para o Azure-SSIS IR usar. Deixe os endereços IP disponíveis pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP em cada sub-rede. Esses endereços não podem ser usados. O primeiro e o último endereços IP das sub-redes são reservados para a conformidade do protocolo e mais três endereços são usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Não use uma sub-rede que esteja exclusivamente ocupada por outros serviços do Azure (por exemplo, instância gerenciada do banco de dados SQL, serviço de aplicativo e assim por diante). 

### <a name="publicIP"></a>Selecione os endereços IP públicos estáticos

Se você quiser colocar seus próprios endereços IP públicos estáticos para Azure-SSIS IR ao se juntar a uma rede virtual, verifique se eles atendem aos seguintes requisitos:

- São fornecidos exatamente dois não utilizados que ainda não estão associados a outros recursos do Azure. O extra será usado quando atualizar periodicamente seu Azure-SSIS IR.

- Eles devem ser estáticos de tipo padrão. Consulte [SKUs do endereço IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) para obter mais detalhes.

- Eles devem ter um nome DNS. Se você não tiver fornecido um nome DNS ao criá-los, poderá fazer isso em portal do Azure.

![IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Eles e a rede virtual devem estar na mesma assinatura e na mesma região.

### <a name="dns_server"></a>Configurar o servidor DNS 
Se você precisar usar seu próprio servidor DNS em uma rede virtual unida pelo seu Azure-SSIS IR para resolver o nome do host privado, verifique se ele também pode resolver nomes de host do Azure globais (por exemplo, um blob de armazenamento do Azure chamado `<your storage account>.blob.core.windows.net`). 

Uma abordagem recomendada é a seguinte: 

-   Configure o DNS personalizado para encaminhar solicitações para o DNS do Azure. Você pode encaminhar registros DNS não resolvidos para o endereço IP dos resolvedores recursivos do Azure (168.63.129.16) em seu próprio servidor DNS. 

Para obter mais informações, consulte [resolução de nomes que usa seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Use um FQDN (nome de domínio totalmente qualificado) para o nome do host privado, por exemplo, use `<your_private_server>.contoso.com` em vez de `<your_private_server>`, já que Azure-SSIS IR não acrescentará automaticamente seu próprio sufixo DNS.

### <a name="nsg"></a>Configurar um NSG
Se você precisar implementar um NSG para a sub-rede usada pelo seu Azure-SSIS IR, permita o tráfego de entrada e de saída por meio das seguintes portas: 

-   **Requisito de entrada de Azure-SSIS IR**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Destination port range | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se você unir o IR a uma rede virtual do Resource Manager) <br/><br/>10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)| O serviço de Data Factory usa essas portas para se comunicar com os nós de sua Azure-SSIS IR na rede virtual. <br/><br/> Se você criar ou não um NSG no nível de sub-rede, Data Factory sempre configurará um NSG no nível das NICs (placas de interface de rede) conectadas às máquinas virtuais que hospedam o Azure-SSIS IR. Somente o tráfego de entrada dos endereços IP do Data Factory nas portas especificadas é permitido pelo NSG no nível do adaptador de rede. Mesmo se você abrir essas portas para o tráfego de Internet no nível de sub-rede, o tráfego de endereços IP que não são Data Factory endereços IP é bloqueado no nível de NIC. |
| Entrada | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Adicional Essa regra só é necessária quando o suporte da Microsoft solicita que o cliente seja aberto para solução de problemas avançada e pode ser fechado logo após a solução de problemas. A marca de serviço **CorpNetSaw** permite que somente as estações de trabalho de acesso seguro na rede corporativa da Microsoft usem a área de trabalho remota. E essa marca de serviço não pode ser selecionada no portal e só está disponível por meio de Azure PowerShell ou CLI do Azure. <br/><br/> No nível de NIC NSG, a porta 3389 é aberta por padrão e permitimos que você controle a porta 3389 no nível de sub-rede NSG, enquanto Azure-SSIS IR não permitiu que a porta 3389 de saída por padrão na regra de firewall do Windows em cada nó IR para proteção. |
||||||||

-   **Requisito de saída de Azure-SSIS IR**

| Direção | Protocolo de transporte | Origem | Intervalo de portas de origem | Destino | Destination port range | Comentários |
|---|---|---|---|---|---|---|
| Saída | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós de seu Azure-SSIS IR na rede virtual usam essa porta para acessar os serviços do Azure, como o armazenamento do Azure e os hubs de eventos do Azure. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | Adicional Os nós de seu Azure-SSIS IR na rede virtual usam essa porta para baixar uma lista de certificados revogados da Internet. Se você bloquear esse tráfego, poderá experimentar o downgrade de desempenho ao iniciar o IR e perder a capacidade de verificar a lista de certificados revogados para o uso do certificado. Se você quiser restringir ainda mais o destino a determinados FQDNs, consulte a seção **usar o Azure ExpressRoute ou UDR**|
| Saída | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Adicional Essa regra só é necessária quando os nós de sua Azure-SSIS IR na rede virtual acessam um SSISDB hospedado pelo servidor do banco de dados SQL. Se a política de conexão do servidor do banco de dados SQL estiver definida como **proxy** em vez de **redirecionar**, somente a porta 1433 será necessária. <br/><br/> Essa regra de segurança de saída não é aplicável a um SSISDB hospedado por sua instância gerenciada na rede virtual ou no servidor de banco de dados do Azure configurado com o ponto de extremidade privado. |
| Saída | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Adicional Essa regra só é necessária quando os nós de sua Azure-SSIS IR na rede virtual acessam um SSISDB hospedado por sua instância gerenciada na rede virtual ou no servidor de banco de dados do Azure configurado com o ponto de extremidade privado. Se a política de conexão do servidor do banco de dados SQL estiver definida como **proxy** em vez de **redirecionar**, somente a porta 1433 será necessária. |
| Saída | TCP | VirtualNetwork | * | Armazenamento | 445 | Adicional Essa regra só é necessária quando você deseja executar o pacote do SSIS armazenado nos arquivos do Azure. |
||||||||

### <a name="route"></a>Usar o Azure ExpressRoute ou UDR
Se você quiser inspecionar o tráfego de saída de Azure-SSIS IR, poderá rotear o tráfego iniciado de Azure-SSIS IR para o dispositivo de firewall local por meio do túnel de força [do ExpressRoute do Azure](https://azure.microsoft.com/services/expressroute/) (anunciando uma rota BGP, 0.0.0.0/0, para a rede virtual) ou para a NVA (solução de virtualização de rede) como um firewall ou [Firewall do Azure](https://docs.microsoft.com/azure/firewall/) por meio de [UDRs](../virtual-network/virtual-networks-udr-overview.md). 

![Cenário de NVA para Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Você precisa fazer as coisas abaixo para fazer todo o cenário funcionar
   -   O tráfego de entrada entre os serviços de gerenciamento do lote do Azure e o Azure-SSIS IR não pode ser roteado via dispositivo de firewall.
   -   O dispositivo de firewall deve permitir o tráfego de saída exigido pelo Azure-SSIS IR.

O tráfego de entrada entre os serviços de gerenciamento do lote do Azure e o Azure-SSIS IR não pode ser roteado para o dispositivo de firewall, caso contrário, o tráfego será interrompido devido a um problema de roteamento assimétrico. As rotas devem ser definidas para o tráfego de entrada para que o tráfego possa responder da mesma forma que chega. Você pode definir UDRs específicos para rotear o tráfego entre os serviços de gerenciamento do lote do Azure e o Azure-SSIS IR com o tipo do próximo salto como **Internet**.

Por exemplo, se o Azure-SSIS IR estiver localizado em `UK South` e você quiser inspecionar o tráfego de saída por meio do firewall do Azure, você obterá primeiro uma lista de intervalos de serviço de `BatchNodeManagement.UKSouth` de marca de serviço do [link de download do intervalo de IP de marcas de serviços](https://www.microsoft.com/download/details.aspx?id=56519) ou por meio da API de descoberta de marca de [serviço](https://aka.ms/discoveryapi). Em seguida, aplique os seguintes UDRs de rotas de intervalo de IP relacionadas com o tipo do próximo salto como **Internet** junto com a rota 0.0.0.0/0 com o tipo do próximo salto como **dispositivo virtual**.

![Configurações de UDR do lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Essa abordagem gera um custo de manutenção adicional. Verifique regularmente o intervalo de IP e adicione novos intervalos de IP em seu UDR para evitar a interrupção do Azure-SSIS IR. É recomendável verificar o intervalo de IP mensalmente, porque quando o novo IP aparece na marca de serviço, o IP levará outro mês para entrar em vigor. 

Para que o dispositivo de firewall permita o tráfego de saída, você precisa permitir a saída para as portas abaixo como requisito nas regras de saída do NSG.
-   Porta 443 com destino como serviços de nuvem do Azure.

    Se você usar o Firewall do Azure, poderá especificar a regra de rede com a marca de serviço AzureCloud, caso contrário, poderá permitir o destino como tudo no dispositivo de firewall.

-   Porta 80 com destino como sites de download de CRL.

    Você deve permitir os FQDNs abaixo que são usados como CRL (lista de certificados revogados) baixar sites de certificados para fins de gerenciamento de Azure-SSIS IR:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Se você estiver usando certificados que têm uma CRL diferente, será recomendável incluí-los também. Você pode ler isso para entender mais sobre a [lista de certificados revogados](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Se você não permitir esse tráfego, poderá experimentar o downgrade de desempenho ao iniciar Azure-SSIS IR e perder a capacidade de verificar a lista de certificados revogados para o uso do certificado, o que não é recomendado no ponto de vista de segurança.

-   Porta 1433, 11000-11999 com destino como SQL do Azure (necessário somente quando os nós de sua Azure-SSIS IR na rede virtual acessam um SSISDB hospedado pelo servidor do banco de dados SQL).

    Se você usar o Firewall do Azure, poderá especificar a regra de rede com a marca de serviço do Azure SQL; caso contrário, você poderá permitir o destino como uma URL específica do SQL do Azure no dispositivo de firewall.

-   Porta 445 com destino como armazenamento do Azure (necessário somente quando você executa o pacote do SSIS armazenado nos arquivos do Azure).

    Se você usar o Firewall do Azure, poderá especificar a regra de rede com a marca de serviço de armazenamento; caso contrário, você poderá permitir o destino como uma URL de armazenamento de arquivos do Azure específica no dispositivo de firewall.

> [!NOTE]
> Para o SQL e o armazenamento do Azure, se você configurar pontos de extremidade de serviço de rede virtual em sua sub-rede, o tráfego entre Azure-SSIS IR e o SQL do Azure na mesma região \ armazenamento do Azure na mesma região ou região emparelhada será roteado para Microsoft Azure rede de backbone diretamente em vez de seu dispositivo de firewall.

Se você não precisar da capacidade de inspecionar o tráfego de saída de Azure-SSIS IR, poderá simplesmente aplicar a rota para forçar todo o tráfego para o próximo salto tipo **Internet**:

-   Em um cenário do Azure ExpressRoute, você pode aplicar uma rota 0.0.0.0/0 com o tipo do próximo salto como **Internet** na sub-rede que hospeda o Azure-SSIS ir. 
-   Em um cenário de NVA, você pode modificar a rota 0.0.0.0/0 existente aplicada na sub-rede que hospeda o Azure-SSIS IR do tipo do próximo salto como **dispositivo virtual** para a **Internet**.

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Especificar a rota com o próximo salto tipo **Internet** não significa que todo o tráfego passará pela Internet. Desde que o endereço de destino seja para um dos serviços do Azure, o Azure roteia o tráfego diretamente para o serviço pela rede de backbone do Azure, em vez de rotear o tráfego para a Internet.

### <a name="resource-group"></a>Configurar o grupo de recursos

O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
- Um Azure Load Balancer, com o nome *\<Guid >-azurebatch-cloudserviceloadbalancer*.
- Um endereço IP público do Azure, com o nome *\<Guid >-azurebatch-cloudservicepublicip*.
- Um grupo de segurança de trabalho de rede, com o nome *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Agora você pode colocar seus próprios endereços IP públicos estáticos para Azure-SSIS IR. Nesse cenário, criaremos apenas o Azure Load Balancer e o grupo de segurança de rede no mesmo grupo de recursos que os endereços IP públicos estáticos em vez da rede virtual.

Esses recursos serão criados quando o Azure-SSIS IR for iniciado. Eles serão excluídos quando o Azure-SSIS IR for interrompido. Se você colocar seus próprios endereços IP públicos estáticos para Azure-SSIS IR, eles não serão excluídos quando o Azure-SSIS IR parar. Para evitar o bloqueio da Azure-SSIS IR de parar, não reutilize esses recursos de rede em seus outros recursos. 

Certifique-se de que você não tem nenhum bloqueio de recurso no grupo de recursos/assinatura ao qual a rede virtual/seus endereços IP públicos estáticos pertencem. Se você configurar um bloqueio somente leitura/exclusão, iniciar e parar seu Azure-SSIS IR falhará ou ele deixará de responder.

Certifique-se de que você não tem uma política do Azure que impede que os seguintes recursos sejam criados sob o grupo de recursos/assinatura ao qual a rede virtual/seus endereços IP públicos estáticos pertencem: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> PERGUNTAS FREQUENTES

- Como posso proteger o endereço IP público exposto no meu Azure-SSIS IR para conexão de entrada? É possível remover o endereço IP público?
 
  No momento, um endereço IP público será criado automaticamente quando o Azure-SSIS IR ingressar em uma rede virtual. Temos um NSG no nível da NIC para permitir que apenas os serviços de gerenciamento do lote do Azure inligados-se conectem ao seu Azure-SSIS IR. Você também pode especificar um NSG no nível de sub-rede para a proteção de entrada.

  Se você não quiser que qualquer endereço IP público seja exposto, considere [configurar um ir auto-hospedado como proxy para seu Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) em vez de ingressar o Azure-SSIS ir em uma rede virtual, se isso se aplicar ao seu cenário.
 
- Posso adicionar o endereço IP público do meu Azure-SSIS IR à lista de permissões do firewall para minhas fontes de dados?

  Agora você pode colocar seus próprios endereços IP públicos estáticos para Azure-SSIS IR. Nesse caso, você pode adicionar seus endereços IP à lista de permissões do firewall para suas fontes de dados. Você também pode considerar outras opções abaixo para proteger o acesso a dados do seu Azure-SSIS IR dependendo do seu cenário:

  - Se sua fonte de dados estiver no local, depois de conectar uma rede virtual à sua rede local e ingressar seu Azure-SSIS IR na sub-rede da rede virtual, você poderá adicionar o intervalo de endereços IP privado dessa sub-rede à lista de permissões do firewall para sua fonte de dados .
  - Se sua fonte de dados for um serviço do Azure que dá suporte a pontos de extremidade de serviço de rede virtual, você poderá configurar um ponto de extremidade de serviço de rede virtual em sua sub-rede de rede virtual e ingressar seu Azure-SSIS IR nessa sub-rede. Em seguida, você pode adicionar uma regra de rede virtual com essa sub-rede ao firewall para sua fonte de dados.
  - Se sua fonte de dados for um serviço de nuvem não Azure, você poderá usar um UDR para rotear o tráfego de saída de seu Azure-SSIS IR para um firewall do NVA/Azure por meio de um endereço IP público estático. Em seguida, você pode adicionar o endereço IP público estático do seu firewall do NVA/Azure à lista de permissões do firewall para sua fonte de dados.
  - Se nenhuma das opções acima atender às suas necessidades, considere [configurar um ir auto-hospedado como proxy para seu Azure-SSIS ir](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Em seguida, você pode adicionar o endereço IP público estático do computador que hospeda o IR auto-hospedado à lista de permissões do firewall para sua fonte de dados.

- Por que preciso fornecer dois endereços públicos estáticos se eu quiser trazer o meu próprio para Azure-SSIS IR?

  Azure-SSIS IR é automaticamente atualizado regularmente. Novos nós são criados durante a atualização e os antigos serão excluídos. No entanto, para evitar o tempo de inatividade, os nós antigos não serão excluídos até que os novos estejam prontos. Portanto, seu primeiro endereço IP público estático usado pelos nós antigos não pode ser liberado imediatamente e precisamos de seu segundo endereço IP público estático para criar os novos nós.

- Eu trouxe meus próprios endereços IP públicos estáticos para Azure-SSIS IR, mas porque ele ainda não consegue acessar minhas fontes de dados?

  - Confirme se os dois endereços IP públicos estáticos foram adicionados à lista de permissões do firewall para suas fontes de dados. Cada vez que o Azure-SSIS IR é atualizado, seu endereço IP público estático é alternado entre os dois trazidos por você. Se você adicionar apenas um deles à lista de permissões, o acesso a dados para seu Azure-SSIS IR será interrompido após sua atualização.
  - Se sua fonte de dados for um serviço do Azure, verifique se você o configurou com pontos de extremidade de serviço de rede virtual. Se esse for o caso, o tráfego de Azure-SSIS IR para sua fonte de dados mudará para usar os endereços IP privados gerenciados pelos serviços do Azure e adicionar seus próprios endereços IP públicos estáticos à lista de permissões do firewall para sua fonte de dados não entrará em vigor.

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (Interface do usuário do Data Factory)

Esta seção mostra como unir um Azure-SSIS IR existente a uma rede virtual (clássica ou Azure Resource Manager) usando a interface do usuário portal do Azure e Data Factory. 

Antes de ingressar seu Azure-SSIS IR à rede virtual, você precisa configurar corretamente a rede virtual. Siga as etapas na seção que se aplica ao seu tipo de rede virtual (clássica ou Azure Resource Manager). Em seguida, siga as etapas na terceira seção para ingressar seu Azure-SSIS IR na rede virtual. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configurar uma rede virtual Azure Resource Manager

Use o portal para configurar uma rede virtual Azure Resource Manager antes de tentar adicionar uma Azure-SSIS IR a ela.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. Entre no [portal do Azure](https://portal.azure.com). 

1. Escolha **Mais serviços**. Filtre e selecione **Redes virtuais**. 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **Rede virtual**, selecione **Propriedades**. 

1. Selecione o botão de cópia para que a **ID DO RECURSO** copie a ID do recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu à esquerda, selecione **sub-redes**. Verifique se o número de endereços disponíveis é maior do que os nós em seu Azure-SSIS IR. 

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.) 

   1. No portal do Azure, no menu à esquerda, selecione **assinaturas**. 

   1. Selecione sua assinatura. 

   1. À esquerda, selecione **provedores de recursos**e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois. 

### <a name="configure-a-classic-virtual-network"></a>Configurar uma rede virtual clássica

Use o portal para configurar uma rede virtual clássica antes de tentar ingressar em um Azure-SSIS IR a ela. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. Entre no [portal do Azure](https://portal.azure.com). 

1. Escolha **Mais serviços**. Filtre e selecione **Redes virtuais (clássicas)** . 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **Rede virtual (clássica)** , selecione **Propriedades**. 

   ![ID do recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão de cópia para que **ID DO RECURSO** copie a ID do recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu à esquerda, selecione **sub-redes**. Verifique se o número de endereços disponíveis é maior do que os nós em seu Azure-SSIS IR. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Una **MicrosoftAzureBatch** à função **Colaborador da Máquina Virtual Clássica** para a rede virtual. 

   1. No menu à esquerda, selecione **controle de acesso (iam)** e selecione a guia **atribuições de função** . 

       !["Controle de Acesso" e botões "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecione **Adicionar atribuição de função**.

   1. Na página **Adicionar atribuição de função** , para **função**, selecione **colaborador de máquina virtual clássica**. Na caixa **selecionar** , Cole **ddbf3205-c6bd-46ae-8127-60eb93363864**e, em seguida, selecione **lote do Microsoft Azure** na lista de resultados da pesquisa. 

       ![Resultados da pesquisa na página "Adicionar atribuição de função"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecione **salvar** para salvar as configurações e fechar a página. 

       ![Salvar as configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Confirme se você visualiza o **Lote do Microsoft Azure** na lista de colaboradores. 

       ![Confirme o acesso ao lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor do lote do Azure. Se você já tiver uma conta do lote do Azure em sua assinatura, sua assinatura será registrada para o lote do Azure. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.) 

   1. No portal do Azure, no menu à esquerda, selecione **assinaturas**. 

   1. Selecione sua assinatura. 

   1. À esquerda, selecione **provedores de recursos**e confirme se **Microsoft. batch** é um provedor registrado. 

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual

Depois de configurar sua rede virtual Azure Resource Manager ou rede virtual clássica, você pode unir o Azure-SSIS IR à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, somente esses navegadores da Web dão suporte à interface do usuário do Data Factory. 

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **Data factories**. Se você não vir **fábricas de dados** no menu, selecione **mais serviços**e, na seção **inteligência + análise** , selecione **Data factories**. 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione o data factory com o Azure-SSIS IR na lista. Você verá a home page do seu data factory. Selecione o bloco **criar & monitor** . Você visualiza a interface do usuário do Data Factory em uma guia separada. 

   ![Página inicial do data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface de usuário do Data Factory, alterne para a guia **Editar**, selecione **Conexões** e alterne para a guia **Runtimes de integração**. 

   ![Guia "runtimes de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o Azure-SSIS IR estiver em execução, na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **parar** para seu Azure-SSIS ir. Não é possível editar seu Azure-SSIS IR até que você o interrompa. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista de **tempos de execução de integração** , na coluna **ações** , selecione o botão **Editar** para seu Azure-SSIS ir. 

   ![Editar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de instalação do Integration Runtime, avance pelas seções **configurações gerais** e **configurações do SQL** selecionando o botão **Avançar** . 

1. Na seção **Configurações avançadas** : 

   1. Selecione a **opção Selecionar uma VNet para sua Azure-SSIS Integration Runtime para ingressar, permitir que o ADF crie determinados recursos de rede e, opcionalmente, traga sua própria caixa de seleção de endereços IP públicos estáticos** . 

   1. Para **Assinatura**, selecione a assinatura do Azure que possui sua rede virtual.

   1. Para **Local**, o mesmo local do seu runtime de integração é selecionado.

   1. Para **tipo**, selecione o tipo de sua rede virtual: clássica ou Azure Resource Manager. Recomendamos que você selecione um Azure Resource Manager rede virtual, pois as redes virtuais clássicas serão preteridas em breve.

   1. Para **Nome da VNet**, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada com ponto de extremidades privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Para **Nome da sub-rede**, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o servidor de banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto de extremidade privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para Azure-SSIS IR.

   1. Marque a caixa de seleção **colocar endereços IP públicos estáticos para seu Azure-SSIS Integration Runtime** para escolher se deseja trazer seus próprios endereços IP públicos estáticos para Azure-SSIS ir, para que você possa permiti-los no firewall para suas fontes de dados.

      Se você marcar a caixa de seleção, conclua as etapas a seguir.

      1. Para o **primeiro endereço IP público estático**, selecione o primeiro endereço IP público estático que [atende aos requisitos](#publicIP) para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.
      
      1. Para o **segundo endereço IP público estático**, selecione o segundo endereço IP público estático que [atende aos requisitos](#publicIP) para seu Azure-SSIS ir. Se você não tiver nenhum, clique em **criar novo** link para criar endereços IP públicos estáticos em portal do Azure e, em seguida, clique no botão atualizar aqui, para que você possa selecioná-los.

   1. Selecione **validação de VNet**. Se a validação for bem-sucedida, selecione **continuar**. 

   ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na seção **Resumo** , examine todas as configurações de seu Azure-SSIS ir. Em seguida, selecione **Atualizar**.

1. Inicie sua Azure-SSIS IR selecionando o botão **Iniciar** na coluna **ações** para sua Azure-SSIS ir. Leva cerca de 20 a 30 minutos para iniciar o Azure-SSIS IR que une uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Antes de poder ingressar seu Azure-SSIS IR em uma rede virtual, você precisa configurar a rede virtual. Para configurar automaticamente as permissões e as configurações de rede virtual para seu Azure-SSIS IR para ingressar na rede virtual, adicione o seguinte script:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um IR do Azure-SSIS e uni-lo a uma rede virtual

Você pode criar um IR do Azure-SSIS e uni-lo a uma rede virtual ao mesmo tempo. Para obter o script completo e as instruções, consulte [criar um Azure-SSIS ir](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unir um IR do Azure-SSIS existente a uma rede virtual

O artigo [criar um Azure-SSIS ir](create-azure-ssis-integration-runtime.md) mostra como criar um Azure-SSIS ir e associá-lo a uma rede virtual no mesmo script. Se você já tiver um Azure-SSIS IR, siga estas etapas para associá-lo à rede virtual: 
1. Interrompa o IR do Azure-SSIS. 
1. Configure o IR do Azure-SSIS para unir à rede virtual. 
1. Inicie o IR do Azure-SSIS. 

### <a name="define-the-variables"></a>Defina as variáveis

```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Interrompa o IR do Azure-SSIS

Você precisa parar o Azure-SSIS IR antes de poder associá-lo a uma rede virtual. Esse comando libera todos os nós e para a cobrança:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar as configurações de rede virtual para o IR do Azure-SSIS a unir

Para definir as configurações para a rede virtual que o Azure-SSIS ingressará, use este script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configure o IR do Azure-SSIS

Para ingressar seu Azure-SSIS IR em uma rede virtual, execute o comando `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Inicie o IR do Azure-SSIS

Para iniciar o Azure-SSIS IR, execute o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Esse comando demora de 20 a 30 minutos para concluir.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre Azure-SSIS IR, consulte os seguintes artigos: 
- [Azure-SSIS ir](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais gerais sobre o IRs, incluindo Azure-SSIS IR. 
- [Tutorial: implantar pacotes do SSIS no Azure](tutorial-create-azure-ssis-runtime-portal.md). Este tutorial fornece instruções passo a passo para criar seu Azure-SSIS IR. Ele usa o Banco de Dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Crie um Azure-SSIS ir](create-azure-ssis-integration-runtime.md). Este artigo se expande no tutorial. Ele fornece instruções sobre como usar o banco de dados SQL do Azure com pontos de extremidade de serviço de rede virtual ou instância gerenciada em uma rede virtual para hospedar o catálogo do SSIS. Ele mostra como unir seu Azure-SSIS IR a uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre seu Azure-SSIS IR. Ele fornece descrições de status para as informações retornadas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou excluir seu Azure-SSIS IR. Adicionalmente, mostra como escalar horizontalmente o IR do Azure-SSIS adicionando nós.
---
title: Usar Zonas de Disponibilidade no AKS (serviço kubernetes do Azure)
description: Saiba como criar um cluster que distribui nós entre zonas de disponibilidade no serviço de kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 3790511bf3f71cdeb01853e4051a013719502d9f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605082"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Criar um cluster AKS (serviço de kubernetes do Azure) que usa Zonas de Disponibilidade

Um cluster AKS (serviço de kubernetes do Azure) distribui recursos como os nós e o armazenamento entre seções lógicas da infraestrutura de computação do Azure subjacente. Esse modelo de implantação garante que os nós sejam executados em domínios de falha e de atualização separados em um único datacenter do Azure. Os clusters AKS implantados com esse comportamento padrão fornecem um alto nível de disponibilidade para proteger contra uma falha de hardware ou um evento de manutenção planejada.

Para fornecer um nível mais alto de disponibilidade para seus aplicativos, os clusters AKS podem ser distribuídos entre zonas de disponibilidade. Essas zonas são data centers separados fisicamente em uma determinada região. Quando os componentes do cluster são distribuídos entre várias zonas, o cluster AKS é capaz de tolerar uma falha em uma dessas zonas. Seus aplicativos e operações de gerenciamento continuam disponíveis mesmo se um datacenter inteiro tiver um problema.

Este artigo mostra como criar um cluster AKS e distribuir os componentes do nó entre zonas de disponibilidade.

## <a name="before-you-begin"></a>Antes de começar

Você precisa do CLI do Azure versão 2.0.76 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade de região

Atualmente, os clusters AKS podem ser criados usando zonas de disponibilidade nas seguintes regiões:

* Centro dos EUA
* Leste dos EUA 2
* Leste dos EUA
* França Central
* Leste do Japão
* Europa do Norte
* Sudeste Asiático
* Sul do Reino Unido
* Europa Ocidental
* Oeste dos EUA 2

As seguintes limitações se aplicam quando você cria um cluster AKS usando zonas de disponibilidade:

* Você só pode habilitar zonas de disponibilidade quando o cluster é criado.
* As configurações de zona de disponibilidade não podem ser atualizadas depois que o cluster é criado. Você também não pode atualizar um cluster de zona existente e de não disponibilidade para usar zonas de disponibilidade.
* Você não pode desabilitar zonas de disponibilidade para um cluster AKS depois que ele tiver sido criado.
* O tamanho do nó (SKU da VM) selecionado deve estar disponível em todas as zonas de disponibilidade.
* Clusters com zonas de disponibilidade habilitadas exigem o uso de balanceadores de carga standard do Azure para distribuição entre zonas.
* Você deve usar o kubernetes versão 1.13.5 ou superior para implantar balanceadores de carga padrão.

Os clusters AKS que usam zonas de disponibilidade devem usar a SKU *padrão* do Azure Load Balancer, que é o valor padrão para o tipo de balanceador de carga. Este tipo de balanceador de carga só pode ser definido no momento da criação do cluster. Para obter mais informações e as limitações do balanceador de carga padrão, consulte [limitações de SKU standard do Azure Load Balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações de discos do Azure

Os volumes que usam o Azure Managed disks não são recursos zonais no momento. O pods reagendado em uma zona diferente da zona original não pode anexar novamente os discos anteriores. É recomendável executar cargas de trabalho sem estado que não exijam armazenamento persistente que possa surgir em problemas de zona.

Se você precisar executar cargas de trabalho com estado, use os e Tolerations em suas especificações pod para dizer ao agendador kubernetes para criar pods na mesma zona que os discos. Como alternativa, use o armazenamento baseado em rede, como arquivos do Azure, que podem ser anexados a pods à medida que são agendados entre as zonas.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral de Zonas de Disponibilidade para clusters AKS

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As zonas são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha.

Para obter mais informações, consulte [o que são zonas de disponibilidade no Azure?][az-overview].

Os clusters AKS implantados usando zonas de disponibilidade podem distribuir nós em várias zonas em uma única região. Por exemplo, um cluster na região *leste dos EUA 2* pode criar nós em todas as três zonas de disponibilidade no *leste dos EUA 2*. Essa distribuição de recursos de cluster AKS melhora a disponibilidade do cluster, pois eles são resilientes à falha de uma zona específica.

![Distribuição de nó AKS entre zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Em uma interrupção de zona, os nós podem ser rebalanceados manualmente ou usando o dimensionador automático do cluster. Se uma única zona ficar indisponível, seus aplicativos continuarão a ser executados.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster AKS entre zonas de disponibilidade

Quando você cria um cluster usando o comando [AZ AKs Create][az-aks-create] , o parâmetro `--zones` define em quais nós de agente de zonas são implantados. Os componentes do plano de controle AKS para seu cluster também são distribuídos entre zonas na configuração mais alta disponível quando você define o parâmetro `--zones` no momento da criação do cluster.

Se você não definir zonas para o pool de agente padrão ao criar um cluster AKS, os componentes do plano de controle AKS para o cluster não usarão zonas de disponibilidade. Você pode adicionar pools de nós adicionais usando o comando [AZ AKs nodepool Add][az-aks-nodepool-add] e especificar `--zones` para esses novos nós, no entanto, os componentes do plano de controle permanecem sem reconhecimento de zona de disponibilidade. Você não pode alterar o reconhecimento de zona para um pool de nós ou os componentes do plano de controle AKS depois que eles são implantados.

O exemplo a seguir cria um cluster AKS chamado *myAKSCluster* no grupo de recursos chamado *MyResource*Group. Um total de *3* nós são criados-um agente na zona *1*, um em *2*e, em seguida, um em *3*. Os componentes do plano de controle AKS também são distribuídos entre zonas na configuração mais alta disponível, já que elas são definidas como parte do processo de criação do cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Leva alguns minutos para o cluster do AKS ser criado.

## <a name="verify-node-distribution-across-zones"></a>Verificar a distribuição de nós entre zonas

Quando o cluster estiver pronto, liste os nós de agente no conjunto de dimensionamento para ver em qual zona de disponibilidade eles estão implantados.

Primeiro, obtenha as credenciais do cluster AKS usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, use o comando [kubectl descrevem][kubectl-describe] para listar os nós no cluster. Filtre o valor *Failure-Domain.beta.kubernetes.Io/Zone* , conforme mostrado no exemplo a seguir:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A saída de exemplo a seguir mostra os três nós distribuídos entre a região especificada e as zonas de disponibilidade, como *eastus2-1* para a primeira zona de disponibilidade e *eastus2-2* para a segunda zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

À medida que você adiciona nós adicionais a um pool de agentes, a plataforma do Azure distribui automaticamente as VMs subjacentes entre as zonas de disponibilidade especificadas.

## <a name="next-steps"></a>Próximas etapas

Este artigo detalha como criar um cluster AKS que usa zonas de disponibilidade. Para obter mais considerações sobre clusters altamente disponíveis, consulte [práticas recomendadas para continuidade dos negócios e recuperação de desastres no AKs][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

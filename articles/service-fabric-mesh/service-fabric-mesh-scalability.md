---
title: Escalabilidade de aplicativos de malha de Service Fabric do Azure
description: Uma das vantagens de implantar aplicativos em Service Fabric malha é a capacidade de dimensionar facilmente seus serviços, seja manualmente ou com políticas de dimensionamento automático.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459006"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Escalonar aplicativos de Malha do Service Fabric

Uma das principais vantagens da implantação de aplicativos em Service Fabric malha é a capacidade de você facilmente dimensionar seus serviços para dentro ou para fora. Isso deve ser usado para lidar com quantidades diferentes de carga em seus serviços ou para melhorar a disponibilidade. Você pode realizar manualmente a expansão ou redução horizontal de seus serviços ou definir políticas de dimensionamento automático.

## <a name="manual-scaling-instances"></a>Instâncias de colocação em escala manuais

No modelo de implantação para o recurso de aplicativo, cada serviço tem uma propriedade *replicaCount* que pode ser usada para definir o número de vezes que você deseja que esse serviço seja implantado. Um aplicativo pode consistir em vários serviços, cada um deles com um número exclusivo de *replicaCount*, que são implantados e gerenciados em conjunto. Para dimensionar o número de réplicas de serviço, modifique o valor *replicaCount* para cada serviço que você deseja reduzir horizontalmente no modelo de implantação ou no arquivo de parâmetros. Em seguida, atualize o aplicativo.

Para exemplos de instâncias de serviços de escalonamento manualmente, consulte [Dimensionar manualmente seus serviços de entrada ou saída](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Instâncias de serviço de dimensionamento automático
O dimensionamento automático é uma capacidade adicional do Microsoft Azure Service Fabric que permite que você dimensione dinamicamente o número de suas instâncias de serviço (dimensionamento horizontal). O dimensionamento automático fornece grande elasticidade e permite o provisionamento ou remoção de instâncias de serviço com base na utilização de CPU ou de memória.  O dimensionamento automático permite que você execute o número correto de instâncias de serviço para sua carga de trabalho e otimizar para custos.

Uma política de escala automática é definida por serviço no arquivo de recurso de serviço. Cada política de dimensionamento consiste em duas partes:

- Um gatilho de dimensionamento, que descreve quando o dimensionamento do serviço será executado. Há três fatores que determinam quando o serviço será dimensionado. *Limite inferior de carga* é um valor que determina quando o serviço será dimensionado horizontalmente. Se a carga média de todas as instâncias das partições for menor do que esse valor, o serviço será reduzido horizontalmente. *Limite de carga superior* é um valor que determina quando o serviço será escalado horizontalmente. Se a carga média de todas as instâncias da partição for maior que esse valor, o serviço será escalado horizontalmente. O *intervalo de dimensionamento* determina com que frequência (em segundos) o gatilho será verificado. Depois que o gatilho for verificado, se for necessário escalar, o mecanismo será aplicado. Se não for necessário escalar, nenhuma ação será tomada. Em ambos os casos, o gatilho não será verificado novamente antes do intervalo de escala expirar.

- O mecanismo de dimensionamento descreve como o dimensionamento será executado quando ele for disparado. O *Incremento de Escala* determina quantas instâncias serão adicionadas ou removidas quando o mecanismo for disparado. O *Número máximo de instâncias* define o limite superior para o dimensionamento. Se o número de instâncias da partição atingir esse limite, o serviço não será escalado horizontalmente, independentemente da carga. *Número mínimo de instâncias* define o limite inferior para escala. Se o número de instâncias da partição atingir esse limite, o serviço não será reduzido horizontalmente, independentemente da carga.

Para saber como definir uma política de dimensionamento automático para seu serviço, leia [serviços de dimensionamento automático](service-fabric-mesh-howto-auto-scale-services.md).

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre o modelo de aplicativo, consulte [recursos de Service Fabric](service-fabric-mesh-service-fabric-resources.md)

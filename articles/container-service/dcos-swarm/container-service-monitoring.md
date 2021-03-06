---
title: (PRETERIDO) Monitorar cluster de DC/OS do Azure – Datadog
description: Monitore um cluster do Serviço de Contêiner do Azure com o Datadog. Use a interface do usuário da Web do DC/OS para implantar os agentes Datadog para seu cluster.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 07/28/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 530092dfabacb0b07f4002a82078dd3535cd7e8f
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275245"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>(PRETERIDO) Monitorar um cluster de DC/OS do Serviço de Contêiner do Azure com Datadog

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste artigo, implantaremos agentes do Datadog em todos os nós de agente em seu cluster do Serviço de Contêiner do Azure. Você precisará de uma conta no Datadog para fazer esta configuração. 

## <a name="prerequisites"></a>Pré-requisitos
[Implantar](container-service-deployment.md) e [conectar](../container-service-connect.md) um cluster configurado pelo Serviço de Contêiner do Azure. Explorar a [interface do usuário do Marathon](container-service-mesos-marathon-ui.md). Vá para [https://datadoghq.com](https://datadoghq.com) para configurar uma conta do Datadog. 

## <a name="datadog"></a>Datadog
O Datadog é um serviço de monitoramento que reúne dados de monitoramento de seus contêineres em seu cluster do Serviço de Contêiner do Azure. O Datadog tem um Painel de Integração do Docker, onde você pode ver as métricas específicas em seus contêineres. As métricas obtidas de seus contêineres são organizadas por CPU, Memória, Rede e E/S. O Datadog divide as métricas em contêineres e em imagens. A seguir, um exemplo da aparência da interface do usuário de uso da CPU.

![Interface do usuário do Datadog](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>Configurar uma implantação do Datadog com Marathon
Estas etapas mostram como configurar e implantar aplicativos do Datadog no cluster com Marathon. 

Acesse a interface do usuário do DC/OS via [http://localhost:80/](http://localhost:80/). Na interface do usuário do DC/OS, navegue até o “Universo”, que é o canto inferior esquerdo, procure "Datadog" e clique em "Instalar".

![Pacote do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog1.png)

Agora, para concluir a configuração, será necessária uma conta do Datadog ou de uma conta de avaliação gratuita. Depois de fazer logon no site do Datadog, olhe à sua esquerda e vá para Integrations -> depois [APIs](https://app.datadoghq.com/account/settings#api). 

![Chave de API do Datadog](./media/container-service-monitoring/datadog2.png)

Em seguida, insira sua chave de API na configuração do Datadog no Universo do DC/OS. 

![Configuração do Datadog no Universo DC/OS](./media/container-service-monitoring/datadog3.png) 

Nas configuração acima, as instâncias são configuradas como 10000000 para que sempre que um novo nó for adicionado ao cluster, o Datadog implante automaticamente um agente para o novo nó. Essa é uma solução temporária. Depois de instalar o pacote, você deverá navegar de volta para o site do Datadog e localizar “[Dashboards](https://app.datadoghq.com/dash/list).” A partir daí, você verá os painéis Custom e Integration. O [painel de Docker](https://app.datadoghq.com/screen/integration/docker) terá todas as métricas de contêiner de que você precisa para monitorar seu cluster. 


---
title: Enviar alertas de integridade do serviço do Azure com o PagerDuty
description: Obtenha notificações personalizadas sobre eventos de integridade do serviço na instância do PagerDuty.
ms.topic: article
ms.date: 06/10/2019
ms.openlocfilehash: 746113622b746949a0fae3fd0d7f1f9c7d170707
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551645"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Enviar alertas de integridade do serviço do Azure com PagerDuty usando WebHooks

Este artigo mostra como configurar notificações de integridade do serviço do Azure por meio do PagerDuty usando um webhook. Usando o tipo de integração personalizada do Microsoft Azure [PagerDuty](https://www.pagerduty.com/), você pode adicionar alertas de Integridade do Serviço com facilidade a serviços do PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criando uma URL de integração de integridade do serviço no PagerDuty
1.  Verifique se você se inscreveu e entrou em sua conta do [PagerDuty](https://www.pagerduty.com/).

1.  Navegue para a seção **Serviços** do PagerDuty.

    ![A seção “Serviços” do PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecione **Adicionar Novo Serviço** ou abra um serviço existente configurado.

1.  Nas **Configurações de Integração**, selecione o seguinte:

    a. **Tipo de Integração**: Microsoft Azure

    b. **Nome da Integração**: \<Nome\>

    ![As “Configurações de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Preencha todos os outros campos obrigatórios e selecione **Adicionar**.

1.  Abra essa nova integração e copie e salve a **URL de Integração**.

    ![A “URL de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta usando o PagerDuty no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 de [Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Defina na lista de **Ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

1. Selecione **Salvar** quando concluir a criação do alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

1. Na seção **Configurações**, selecione **Grupos de ação**.

1. Encontre e selecione o grupo de ação que você deseja editar.

1. Adicione à lista de **Ações**:

    a. **Tipo de ação:** *webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

1. Selecione **Salvar** quando concluir a atualização do grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de integridade do serviço que você deseja enviar. Encontre um conteúdo de webhook de integridade do serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Você deverá receber um `202 Accepted` com uma mensagem que contém a “ID do evento”.

1. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximos passos
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema do webhook de alertas de log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../azure-monitor/platform/service-notifications.md).
- Saiba mais sobre [grupos de ação](../azure-monitor/platform/action-groups.md).
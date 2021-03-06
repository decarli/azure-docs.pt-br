---
title: Publicar aplicativo-LUIS
titleSuffix: Azure Cognitive Services
description: Quando terminar de compilar e testar seu aplicativo LUIS ativo, disponibilize-o ao seu aplicativo cliente publicando-o no ponto de extremidade.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 676c6d15c4f439543a3ed74627001725632fecfa
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554835"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publicar seu aplicativo ativo, treinado em um ponto de extremidade de preparo ou produção

Quando você terminar de criar, treinar e testar seu aplicativo LUIS ativo, disponibilize-o para o aplicativo cliente publicando-o no ponto de extremidade. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publicação

1. Para publicar no ponto de extremidade, selecione **Publicar** no painel superior direito. 

    ![Botão publicar na parte superior, barra de navegação direita](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Selecione as configurações para o ponto de extremidade de previsão publicado e, em seguida, selecione **publicar**.

    ![Selecione Publicar configurações e, em seguida, clique no botão publicar](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Slots de publicação

Selecione o slot correto quando a janela pop-up for exibida: 

* Preparação
* Produção 

Usando os dois slots de publicação, isso permite que você tenha duas versões diferentes do seu aplicativo disponíveis nos pontos de extremidade publicados ou na mesma versão em dois pontos de extremidade diferentes. 

### <a name="publishing-regions"></a>Regiões de publicação

O aplicativo é publicado em todas as regiões associadas aos recursos de ponto de extremidade de previsão de LUIS adicionados no portal do LUIS da página **gerenciar** ->  **[recursos do Azure](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** . 

Por exemplo, para um aplicativo criado em [www.Luis.ai](https://www.luis.ai), se você criar um recurso de Luis em duas regiões, **westus** e **lesteus**, e adicioná-los ao aplicativo como recursos, o aplicativo será publicado em ambas as regiões. Para obter mais informações sobre as regiões do LUIS, consulte [Regiões](luis-reference-regions.md).

> [!TIP]
> Há 3 regiões de criação. Você deve criar a região na qual pretende publicar. Se você precisar publicar em todas as regiões, será necessário gerenciar o processo de criação e o modelo treinado resultante em todas as três regiões de criação. 


## <a name="configuring-publish-settings"></a>Definindo configurações de publicação

Depois de selecionar o slot, defina as configurações de publicação para:

* Análise de sentimento
* Correção ortográfica-somente ponto de extremidade de previsão v2
* Preparação da fala 

Depois de publicar, essas configurações estarão disponíveis para análise na página **gerenciar** configurações de **publicação** da seção. Você pode alterar as configurações com cada publicação. Se você cancelar uma publicação, as alterações feitas durante a publicação também serão canceladas. 

### <a name="when-your-app-is-published"></a>Quando seu aplicativo é publicado

Quando seu aplicativo for publicado com êxito, uma notificação de êxito aparecerá na parte superior do navegador. A notificação também inclui um link para os pontos de extremidade. 

Se você precisar da URL do ponto de extremidade, selecione o link. Você também pode acessar as URLs do ponto de extremidade selecionando **gerenciar** no menu superior e, em seguida, selecione **recursos do Azure** no menu à esquerda. 

## <a name="sentiment-analysis"></a>Análise de sentimento

<a name="enable-sentiment-analysis"></a>

A análise de sentimento permite ao LUIS fazer a integração com a [Análise de Texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) para fornecer análise de frase-chave e sentimento. 

Você não precisa fornecer uma chave de Análise de Texto e não é cobrando nenhum encargo para esse serviço na sua conta do Azure. 

Dados de sentimento são uma pontuação entre 1 e 0 indicando o sentimento positivo (mais próximo de 1) ou negativo (mais próximo de 0) dos dados. O rótulo de sentimento de `positive`, `neutral` e `negative` é por cultura com suporte. Atualmente, somente inglês dá suporte a rótulos de sentimento. 

Para obter mais informações sobre a resposta do ponto de extremidade JSON com análise de sentimento, veja [Análise de sentimento](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Correção ortográfica

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

As correções de ortografia são feitas antes da previsão de expressão do usuário LUIS. Você pode ver quaisquer alterações no expressão original, incluindo a ortografia, na resposta.

## <a name="speech-priming"></a>Preparação da fala

A desobstrução de fala é o processo de usar o envio do modelo LUIS para os serviços de fala antes da conversão de texto em fala. Isso permite que o serviço de fala forneça a conversão de fala com mais precisão para seu modelo. Isso permite o bot Speech e solicitações LUIS e respostas em uma chamada fazendo uma chamada de fala e voltando uma resposta LUIS. Ele fornece menos latência geral.

## <a name="next-steps"></a>Próximos passos

* Consulte [Gerenciar chaves](./luis-how-to-azure-subscription.md) para adicionar a chave de assinatura do Azure ao LUIS e saber como definir a chave de Verificação Ortográfica do Bing e incluir todas as intenções nos resultados.
* Veja [Treinar e testar seu aplicativo](luis-interactive-test.md) para obter instruções sobre como testar seu aplicativo publicado no console de teste.


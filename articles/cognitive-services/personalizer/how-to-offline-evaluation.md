---
title: Como executar a avaliação offline – personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como usar a avaliação offline para medir a eficácia de seu aplicativo e analisar seu loop de aprendizado.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: c2aec0db2d1f9865188f2749a0eeb765a14d04ed
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73952984"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analise seu loop de aprendizagem com uma avaliação offline

Saiba como concluir uma avaliação offline e entender os resultados.

As avaliações offline permitem que você meça a eficiência do personalizador em comparação com o comportamento padrão do aplicativo, saiba quais recursos estão contribuindo com a personalização e descubra automaticamente novos valores de aprendizado de máquina.

Leia sobre [avaliações offline](concepts-offline-evaluation.md) para saber mais.


## <a name="prerequisites"></a>pré-requisitos

* Um loop personalizado configurado
* O loop personalizador deve ter uma quantidade representativa de dados – como um aproximada, é recomendável pelo menos 50.000 eventos em seus logs para obter resultados significativos de avaliação. Opcionalmente, talvez você também tenha exportado anteriormente arquivos da _política de aprendizado_, que podem ser comparados e testados na mesma avaliação.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Etapas para iniciar uma nova avaliação offline

1. No [portal do Azure](https://azure.microsoft.com/free/), localize o recurso de personalização.
1. Na portal do Azure, vá para a seção **avaliações** e selecione **criar avaliação**.
    ![na portal do Azure, vá para a seção * * avaliações * * e selecione * * criar avaliação * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configure os seguintes valores:

    * Um nome de avaliação
    * Data de início e de término – essas são as datas no passado, que especificam o intervalo de dados a ser usado na avaliação. Esses dados devem estar presentes nos logs, conforme especificado no valor de [retenção de dados](how-to-settings.md) .
    * Descoberta de otimização definida como **Sim**

    ![Escolher configurações de avaliação offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Inicie a avaliação selecionando **OK**. 

## <a name="results"></a>Resultados

As avaliações podem levar muito tempo para serem executadas, dependendo da quantidade de dados a serem processados, do número de políticas de aprendizado para comparação e se uma otimização foi solicitada.

Depois de concluído, você pode selecionar a avaliação na lista de avaliações. 

As comparações das políticas de aprendizado incluem:

* **Política online**: a política de aprendizado atual usada no Personalizador
* **Linha de base**: o padrão do aplicativo (conforme determinado pela primeira ação enviada em chamadas de classificação),
* **Política aleatória**: um comportamento de classificação imaginário que sempre retorna a opção aleatória de ações dos fornecidos.
* **Políticas personalizadas**: políticas de aprendizado adicionais carregadas ao iniciar a avaliação.
* **Política otimizada**: se a avaliação foi iniciada com a opção de descobrir uma política otimizada, ela também será comparada e você poderá baixá-la ou torná-la a política de aprendizado online, substituindo a atual.

![Gráfico de resultados das configurações de avaliação offline](./media/offline-evaluation/evaluation-results.png)

Eficácia de [recursos](concepts-features.md) para ações e contexto.

## <a name="next-steps"></a>Próximas etapas

* Saiba [como funcionam as avaliações offline](concepts-offline-evaluation.md).

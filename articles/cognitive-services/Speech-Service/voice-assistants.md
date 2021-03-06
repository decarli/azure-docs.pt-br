---
title: Assistentes de voz – serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para assistentes de voz usando o kit de desenvolvimento de software de fala (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: d687d1d353c1734c5d98121f658003afde2eb182
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74812913"
---
# <a name="about-voice-assistants"></a>Sobre assistentes de voz

Os assistentes de voz que usam o serviço de fala permitem que os desenvolvedores criem interfaces de conversação naturais e humanas para seus aplicativos e experiências.

O serviço assistente de voz fornece uma interação rápida e confiável entre um dispositivo e uma implementação de assistente que usa (1) o canal de fala de linha direta da estrutura de bot ou (2) o serviço de comandos personalizados integrados (versão prévia) para a conclusão da tarefa.

Os aplicativos se conectam ao serviço assistente de voz com o kit de desenvolvimento de software de fala (SDK).

   ![Diagrama conceitual do fluxo do serviço de orquestração do assistente de voz](media/voice-assistants/overview.png "O fluxo do assistente de voz")

## <a name="choosing-an-assistant-solution"></a>Escolhendo uma solução de assistente

A primeira etapa para criar um assistente de voz é decidir o que ele deve fazer. O serviço de fala fornece várias soluções complementares para a criação de interações com o assistente. Se você quiser a flexibilidade e a versatilidade que o canal de [fala de linha direta](direct-line-speech.md) da estrutura de bot fornece ou a simplicidade de [comandos personalizados (versão prévia)](custom-commands.md) para cenários simples, selecionar as ferramentas certas o ajudará a começar.

| Se você desejar... | Em seguida, considere... | Por exemplo,... |
|-------------------|------------------|----------------|
|Conversa aberta com integração de habilidades robusta e controle de implantação completo | O canal de [fala de linha direta](direct-line-speech.md) da estrutura de bot | <ul><li>"Preciso ir para Seattle"</li><li>"Que tipo de pizza posso encomendar?"</li></ul>
|Comando e controle ou conversa orientada a tarefas com criação simplificada e Hospedagem | [Comandos personalizados (versão prévia)](custom-commands.md) | <ul><li>"Ativar a luz de sobrecarga"</li><li>"Torná-lo 5 graus mais quente"</ul>

É recomendável [direcionar a linha de fala](direct-line-speech.md) como a melhor opção padrão se você ainda não tiver certeza do que deseja que seu assistente manipule. Ele oferece integração com um conjunto avançado de ferramentas e auxílios de criação, como a [solução de assistente virtual e modelo empresarial](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) e o [serviço de QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) para criar padrões comuns e usar suas fontes de conhecimento existentes.

Os [comandos personalizados (versão prévia)](custom-commands.md) fornecem uma experiência simplificada de criação e hospedagem especificamente adaptada para cenários de controle e comando de linguagem natural.

   ![Comparação de soluções do assistente](media/voice-assistants/assistant-solution-comparison.png "Comparação de soluções do assistente")

## <a name="core-features"></a>Principais recursos

Se você escolher comandos de [fala de linha direta](direct-line-speech.md) ou [personalizados (versão prévia)](custom-commands.md) para criar suas interações com o assistente, poderá usar um conjunto avançado de recursos de personalização para personalizar seu assistente para sua marca, produto e personalidade.

| Categoria | Recursos |
|----------|----------|
|[Palavra-chave personalizada](speech-devices-sdk-create-kws.md) | Os usuários podem iniciar conversas com assistentes com uma palavra-chave personalizada como "Ei contoso". Um aplicativo faz isso com um mecanismo de palavra-chave personalizado no SDK de fala, que pode ser configurado com uma palavra-chave personalizada [que você pode gerar aqui](speech-devices-sdk-create-kws.md). Os assistentes de voz podem usar a verificação de palavra-chave do lado do serviço para melhorar a precisão da ativação da palavra-chave (versus o dispositivo sozinho).
|[Fala para texto](speech-to-text.md) | Os assistentes de voz convertem áudio em tempo real em texto reconhecido usando a [conversão de fala em texto](speech-to-text.md) do serviço de fala. Esse texto está disponível, como é transcrita, tanto para a implementação do assistente quanto para o aplicativo cliente.
|[Conversão de texto em fala](text-to-speech.md) | As respostas textuais do assistente são sintetizadas usando a [conversão de texto em fala](text-to-speech.md) do serviço de fala. Essa síntese é disponibilizada para o aplicativo cliente como um fluxo de áudio. A Microsoft oferece a capacidade de criar sua própria voz TTS multifuncional personalizada de alta qualidade que dá uma voz à sua marca. Para saber mais, [entre em contato conosco](mailto:mstts@microsoft.com).

## <a name="getting-started-with-voice-assistants"></a>Introdução aos assistentes de voz

Oferecemos guias de início rápido projetados para que você execute códigos em menos de 10 minutos. Esta tabela inclui uma lista de guias de início rápido do assistente de voz, organizadas por idioma.

| Início Rápido | Plataforma | Referência da API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente de voz está disponível no GitHub. Esses exemplos abrangem o aplicativo cliente para se conectar ao assistente em várias linguagens de programação populares.

* [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Ativar voz de seu assistente com o SDK de fala,C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Tutorial

Um tutorial sobre como [habilitar por voz o assistente usando o SDK de fala e o canal de fala de linha direta](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalização

Os assistentes de voz criados com o uso do serviço de fala podem usar a gama completa de opções de personalização disponíveis para [a](speech-to-text.md)seleção de [texto para fala](text-to-speech.md)e de [palavra-chave personalizada](speech-devices-sdk-create-kws.md).

> [!NOTE]
> As opções de personalização variam por idioma/localidade (consulte [idiomas com suporte](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de referência

* [SDK da fala](speech-sdk-reference.md)
* [Serviço de Bot do Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximos passos

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obtenha o SDK de fala](speech-sdk.md)
* [Saiba mais sobre comandos personalizados (versão prévia)](custom-commands.md)
* [Saiba mais sobre a Direct line Speech](direct-line-speech.md)

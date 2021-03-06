---
title: Sobre o SDK de fala-serviço de fala
titleSuffix: Azure Cognitive Services
description: O SDK (Speech Software Development Kit) oferece aos aplicativos acesso nativo às funções do serviço de fala, facilitando o desenvolvimento de software. Este artigo fornece detalhes adicionais sobre o SDK para Windows, Linux e Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: wolfma
ms.openlocfilehash: ea879cbfc3b9b1d1a627add52f26a473aca53cdf
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759868"
---
# <a name="about-the-speech-sdk"></a>Sobre o SDK de Fala

O SDK (Software Development Kit) de Fala fornece o acesso de aplicativos às funções do serviço de Fala, tornando mais fácil desenvolver um software habilitado para fala. Atualmente, os SDKs fornecem acesso a voz **-para-texto**, conversão de **texto em fala**, **tradução de fala**, **reconhecimento de intenção**e **canal de fala de linha direta da estrutura de bot**.

Você pode facilmente capturar áudio de um microfone, ler de um fluxo ou acessar arquivos de áudio do armazenamento com o SDK de fala. O SDK de Fala é compatível com WAV/PCM 16 bits, 16 kHz/8 kHz, áudio de canal único para o reconhecimento de fala. Formatos de áudio adicionais têm suporte usando o [ponto de extremidade REST de fala para texto](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) ou o [serviço de transcrição do lote](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats).

Uma visão geral sobre os recursos e as plataformas com suporte pode ser encontrada na [página de entrada](https://aka.ms/csspeech)de documentação.

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Obter o SDK

### <a name="windows"></a>Windows

> [!WARNING]
> O SDK de fala dá suporte ao Windows 10 ou versões posteriores. **Não há suporte para**versões anteriores do Windows.

Para o Windows, suportamos os seguintes idiomas:

* C# (UWP e .NET), C++: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. O pacote inclui bibliotecas de clientes de 32 e 64 bits e bibliotecas gerenciadas (.NET). O SDK pode ser instalado no Visual Studio usando NuGet, [Microsoft. cognitivaservices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech).

* Java: Você pode fazer referência e usar a versão mais recente do nosso pacote Speech SDK Maven, que suporta apenas o Windows x64. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.8.0` como uma dependência.

### <a name="linux"></a>Linux

> [!NOTE]
> Atualmente, há suporte apenas para Ubuntu 16, 4, Ubuntu 18, 4 e Debian 9 nas seguintes arquiteturas de destino:
> - x86, x64 e ARM64 para C++ desenvolvimento
> - x64 e ARM64 para Java
> - x64 para .NET Core e Python

Verifique se você tem as bibliotecas necessárias instaladas executando os seguintes comandos de Shell:

No Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

No Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: você pode referenciar e usar a versão mais recente do nosso pacote NuGet do SDK de Fala. Para referenciar o SDK, adicione a seguinte referência de pacote ao seu projeto:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.8.0" />
  ```

* Java: você pode referenciar e usar a versão mais recente do nosso pacote Maven do SDK de Fala. Em seu projeto Maven, adicione `https://csspeechstorage.blob.core.windows.net/maven/`como um repositório adicional e faça referência`com.microsoft.cognitiveservices.speech:client-sdk:1.7.0` como uma dependência.

* C++: Baixar o SDK como um [pacote. tar](https://aka.ms/csspeech/linuxbinary) e descompacte os arquivos em um diretório de sua escolha. A tabela a seguir mostra a estrutura da pasta do SDK:

  |Caminho|Description|
  |-|-|
  |`license.md`|Licença|
  |`ThirdPartyNotices.md`|Avisos de terceiros|
  |`include`|Arquivos de cabeçalho C e C++|
  |`lib/x64`|Biblioteca x64 nativa para vinculação ao seu aplicativo|
  |`lib/x86`|Biblioteca x86 nativa para vinculação ao seu aplicativo|

  Para criar um aplicativo, copie ou mova os binários necessários (e bibliotecas) para o seu ambiente de desenvolvimento. Incluí-las conforme necessário no processo de compilação.

### <a name="android"></a>Android

O SDK do Java para Android é empacotado como um [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões necessárias do Android. Está hospedado em um repositório Maven em `https://csspeechstorage.blob.core.windows.net/maven/` como pacote `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`.

Para consumir o pacote do seu projeto do Android Studio, faça as seguintes alterações:

* No arquivo build.gradle no nível do projeto, inclua o seguinte na seção `repository`:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* No arquivo build.gradle de nível de módulo, inclua o seguinte na seção `dependencies`:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.7.0'
  ```

O SDK do Java também faz parte do [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Próximos passos

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
* [Veja como reconhecer fala em C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)

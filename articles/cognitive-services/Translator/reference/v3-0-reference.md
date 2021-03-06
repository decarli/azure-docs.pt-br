---
title: Referência da API de texto do tradutor V3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do tradutor V3.0. A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 11/14/2019
ms.author: swmachan
ms.openlocfilehash: 172bf452cc5197db95e0e1e55c7c687971194899
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123049"
---
# <a name="translator-text-api-v30"></a>API de Tradução de Texto v3.0

## <a name="whats-new"></a>O que há de novo?

A versão 3 do API de tradução de texto fornece uma API de Web modernos baseados em JSON. Ela melhora o uso e o desempenho por meio da consolidação dos recursos existentes em menos operações, além de fornecer novos recursos.

 * Transliteração para converter texto em um idioma de um script para outro script.
 * Tradução para vários idiomas em uma solicitação.
 * Detecção de idioma, tradução e transliteração em uma solicitação.
 * Dicionário para pesquisar traduções alternativas de um termo, para encontrar traduções traseiras e exemplos que mostram os termos usados no contexto.
 * Resultados de detecção de idioma mais informativos.

## <a name="base-urls"></a>URLs base

O Microsoft Translator é distribuído a partir de vários locais de datacenter. Atualmente, eles estão localizados em 10 [geografias do Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Américas:** Leste dos EUA, Sul EUA Central, Oeste EUA Central e oeste dos EUA 2 
* **Pacífico Asiático:** Sul da Coreia, leste do Japão, Sudeste Asiático e leste da Austrália
* **Europa:** Europa Setentrional e Europa Ocidental

As solicitações para a API de Tradução de Texto da Microsoft são na maioria dos casos, tratadas pelo datacenter mais próximo que originou a solicitação. No caso de uma falha de datacenter, a solicitação pode ser roteada para fora da geografia do Azure.

Para forçar a manipulação da solicitação por uma geografia do Azure específica, altere o ponto de extremidade global na solicitação de API para o ponto de extremidade regional desejado:

|DESCRIÇÃO|Geografia do Azure|URL base|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Pacífico Asiático|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autenticação

Assine API de Tradução de Texto ou [Serviços cognitivas multiatendimento](https://azure.microsoft.com/pricing/details/cognitive-services/) nos serviços cognitivas do Azure e use sua chave de assinatura (disponível no portal do Azure) para autenticar. 

Há três cabeçalhos que você pode usar para autenticar sua assinatura. Esta tabela descreve como cada um é usado:

|headers|DESCRIÇÃO|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Use com a assinatura dos Serviços Cognitivos se você estiver passando a chave secreta*.<br/>O valor é a chave secreta do Azure da sua assinatura para a API de Tradução de Texto.|
|Autorização|*Use com a assinatura dos Serviços Cognitivos se você estiver passando um token de autenticação.*<br/>O valor é o token de portador: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Use com a assinatura de vários serviços cognitivas se você estiver passando uma chave secreta de vários serviços.*<br/>O valor é a região da assinatura de vários serviços. Esse valor é opcional quando não estiver usando uma assinatura de vários serviços.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o cabeçalho `Ocp-Apim-Subscription-Key`. Adicione o cabeçalho `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` à sua solicitação.

### <a name="authorization-token"></a>Token de autorização
Como alternativa, você pode trocar sua chave secreta por um token de acesso. Esse token é incluído em cada solicitação como o cabeçalho `Authorization`. Para obter um token de autorização, faça uma solicitação `POST` à URL a seguir:

| Ambiente     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Estas são as solicitações de exemplo para obter um token de acordo com uma chave secreta:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Uma solicitação bem-sucedida retorna o token de acesso codificado como texto sem formatação no corpo da resposta. O token válido é passado para o serviço de Tradução como um token de portador na Autorização.

```http
Authorization: Bearer <Base64-access_token>
```

Um token de autenticação é válido por 10 minutos. O token deve ser reutilizado ao fazer várias chamadas para as APIs do tradutor. No entanto, se o seu programa fizer solicitações para a API do tradutor durante um longo período de tempo, o programa deverá solicitar um novo token de acesso em intervalos regulares (por exemplo, a cada 8 minutos).

### <a name="multi-service-subscription"></a>Assinatura de vários serviços

A última opção de autenticação é usar a assinatura de vários serviços de um serviço cognitiva. Isso permite que você use uma única chave secreta para autenticar solicitações para vários serviços. 

Ao usar uma chave secreta de vários serviços, você deve incluir dois cabeçalhos de autenticação com sua solicitação. O primeiro passa a chave secreta, o segundo especifica a região associada à sua assinatura. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura de API de texto de vários serviços. A região selecionada é a única região que você pode usar para a tradução de texto ao usar a chave de assinatura de vários serviços e deve ser a mesma região que você selecionou quando se inscreveu para sua assinatura de vários serviços por meio do portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, `japaneast`, `japanwest`, `koreacentral`, `northcentralus`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, `westus2`e `southafricanorth`.

Se você passar a chave secreta na cadeia de caracteres de consulta com o parâmetro `Subscription-Key`, então você deverá especificar a região com o parâmetro de consulta `Subscription-Region`.

Se você usar um token de portador, você deve obter o token do ponto de extremidade de região: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Erros

Uma resposta de erro padrão é um objeto JSON com o par de nome/valor chamado `error`. O valor também é um objeto JSON com propriedades:

  * `code`: um código de erro definido pelo servidor.
  * `message`: uma cadeia de caracteres fornecendo uma representação legível do erro.

Por exemplo, um cliente com uma assinatura de avaliação gratuita receberia o seguinte erro após a cota livre esgotar:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns são:

| Código | DESCRIÇÃO |
|:----|:-----|
| 400000| Uma das entradas de solicitação não é válida.|
| 400001| O parâmetro "scope" é inválido.|
| 400002| O parâmetro "category" é inválido.|
| 400003| Um especificador de linguagem está ausente ou inválido.|
| 400004| Um especificador de script de destino ("To script") está ausente ou é inválido.|
| 400005| Um texto de entrada está faltando ou é inválido.|
| 400006| A combinação de idioma e script não é válida.|
| 400018| Um especificador de script de origem ("From script") está ausente ou é inválido.|
| 400019| Não há suporte para um dos idiomas especificados.|
| 400020| Um dos elementos na matriz de texto de entrada não é válido.|
| 400021| O parâmetro da versão da API está ausente ou é inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| O idioma de origem (campo "De") não é válido.|
| 400036| O idioma de destino (campo "Para") está ausente ou é inválido.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O ID de rastreio do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está ausente ou é inválido.|
| 400050| O texto de entrada é muito longo. Exibir [limites de solicitação](../request-limits.md).|
| 400064| O parâmetro "translation" está ausente ou é inválido.|
| 400070| O número de scripts de destino (parâmetro ToScript) não corresponde ao número de idiomas de destino (para o parâmetro To).|
| 400071| O valor não é válido para o TextType.|
| 400072| A matriz de texto de entrada possui muitos elementos.|
| 400073| O parâmetro de script não é válido.|
| 400074| O corpo da solicitação não é válido como JSON.|
| 400075| A combinação de pares de idiomas e categorias não é válida.|
| 400077| O tamanho máximo da solicitação foi excedido. Exibir [limites de solicitação](../request-limits.md).|
| 400079| O sistema personalizado solicitado para tradução entre e para o idioma não existe.|
| 400080| A transliteração não tem suporte para o idioma ou o script.|
| 401000| A solicitação não está autorizada porque as credenciais estão ausentes ou são inválidas.|
| 401015| "As credenciais fornecidas são para a Speech API. Esta solicitação requer credenciais para a API de texto. Use uma assinatura para API de Tradução de Texto ".|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a assinatura excedeu sua cota gratuita.|
| 405000| O método de solicitação não é suportado para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está sendo preparado. Tente novamente em alguns minutos.|
| 408002| A solicitação atingiu o tempo limite aguardando o fluxo de entrada. O cliente não produziu uma solicitação no momento em que o servidor foi preparado para aguardar. O cliente pode repetir a solicitação sem modificações em nenhum momento posterior.|
| 415000| O cabeçalho Content-Type está ausente ou é inválido.|
| 429000, 429001, 429002| O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.|
| 500000| Erro inesperado. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, informe-o com data / hora do erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|

## <a name="metrics"></a>Métricas 
As métricas permitem que você exiba as informações de uso e disponibilidade do tradutor em portal do Azure, na seção métricas, conforme mostrado na captura de tela abaixo. Para obter mais informações, consulte [métricas de dados e plataforma](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Métricas do Tradutor](../media/translatormetrics.png)

Esta tabela lista as métricas disponíveis com a descrição de como elas são usadas para monitorar chamadas à API de tradução.

| Métricas | DESCRIÇÃO |
|:----|:-----|
| TotalCalls| Número total de chamadas de API.|
| TotalTokenCalls| Número total de chamadas à API por meio do serviço de token usando o token de autenticação.|
| SuccessfulCalls| Número de chamadas com êxito.|
| TotalErrors| Número de chamadas com resposta de erro.|
| BlockedCalls| Número de chamadas que excederam a taxa ou o limite de cota.|
| ServerErrors| Número de chamadas com erro interno do servidor (5XX).|
| ClientErrors| Número de chamadas com erro do lado do cliente (4XX).|
| Latência| Duração para concluir a solicitação em milissegundos.|
| CharactersTranslated| Número total de caracteres na solicitação de texto de entrada.|

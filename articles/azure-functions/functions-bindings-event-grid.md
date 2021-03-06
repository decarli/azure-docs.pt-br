---
title: Gatilho de Grade de Eventos para o Azure Functions
description: Entenda como manipular a Grade de Eventos no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: 8062428ae63a572b81a5432c8b29910fe8422e24
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547448"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Gatilho de Grade de Eventos para o Azure Functions

Este artigo explica como manipular com eventos de [Grade de Eventos](../event-grid/overview.md) no Azure Functions. Para obter detalhes sobre como lidar com mensagens de grade de eventos em um ponto de extremidade HTTP, leia [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md).

A Grade de Eventos é um serviço do Azure que envia solicitações HTTP para notificá-lo sobre eventos que acontecem nos *publicadores*. Um publicador é o serviço ou recurso que origina o evento. Por exemplo, uma conta de armazenamento de Blobs do Azure é um publicador, e [uma exclusão ou upload de blob é um evento](../storage/blobs/storage-blob-event-overview.md). Alguns [serviços do Azure têm suporte interno para publicar eventos na Grade de Eventos](../event-grid/overview.md#event-sources).

Os *manipuladores* de eventos recebem e processam eventos. O Azure Functions é um dos vários serviços do[Azure que possuem suporte interno para manipular eventos da Grande de Eventos](../event-grid/overview.md#event-handlers). Neste artigo, você aprende a usar um gatilho de Grade de Eventos para invocar uma função quando um evento é recebido da Grade de Eventos.

Se preferir, você pode usar um gatilho HTTP para manipular eventos de grade de eventos; consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md). No momento, não é possível usar um gatilho da Grade de Eventos para um aplicativo do Azure Functions quando o evento é entregue no [esquema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). Em vez disso, use um gatilho HTTP.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

O gatilho de grade de eventos é fornecido no [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pacote NuGet, versão 2. x. O código-fonte do pacote está no repositório GitHub [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pacotes - Functions 1. x

O gatilho de grade de eventos é fornecido no [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) pacote NuGet, versão 1. x. O código-fonte do pacote está no repositório GitHub [azure-functions-eventgrid-extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para um exemplo de gatilho HTTP, consulte [receber eventos para um ponto de extremidade http](../event-grid/receive-events.md).

### <a name="c-2x-and-higher"></a>C#(2. x e superior)

O exemplo a seguir mostra uma [função do C#](functions-dotnet-class-library.md) que associa para `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Para obter mais informações, confira Pacotes, [Atributos](#attributes), [Configuração](#configuration) e [Uso](#usage).

### <a name="version-1x"></a>Versão 1.x

O exemplo a seguir mostra um funções 1. x [função C#](functions-dotnet-class-library.md) que associa a `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função de script de C#](functions-reference-csharp.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x-and-higher"></a>C#script (versão 2. x e superior)

Aqui está um exemplo que é associado a `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Para obter mais informações, confira Pacotes, [Atributos](#attributes), [Configuração](#configuration) e [Uso](#usage).

### <a name="version-1x"></a>Versão 1.x

Aqui está o código de script de 1. x C# funções que associa a `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir mostra uma associação de gatilho em um arquivo *function.json* e uma [função Python](functions-reference-python.md) que usa a associação.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Aqui está o código Python:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

Esta seção contém os seguintes exemplos:

* [Gatilho de grade de eventos, parâmetro de cadeia de caracteres](#event-grid-trigger-string-parameter)
* [Gatilho de grade de eventos, parâmetro POJO](#event-grid-trigger-pojo-parameter)

Os exemplos a seguir mostram a associação de gatilho em um arquivo *function.JSON* e [funções Java](functions-reference-java.md) que usam a associação e imprimem um evento, primeiro recebendo o evento como ```String``` e o segundo como um POJO.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

### <a name="event-grid-trigger-string-parameter"></a>Gatilho de grade de eventos, parâmetro de cadeia de caracteres

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Gatilho de grade de eventos, parâmetro POJO

Este exemplo usa o POJO a seguir, que representa as propriedades de nível superior de uma grade de eventos:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Na chegada, o conteúdo JSON do evento fica sem serialização no POJO ```EventSchema``` para uso pela função. Isso permite que a função acesse as propriedades do evento de forma orientada a objeto.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

No [biblioteca de runtime de funções Java](/java/api/overview/azure/functions/runtime), use o `EventGridTrigger` anotação em parâmetros cujo valor virá do EventGrid. Parâmetros com essas anotações fazem com que a função seja executada quando um evento é recebido.  Essa anotação pode ser usada com tipos nativos do Java, POJOs ou valores que permitem valor nulos usando `Optional<T>`.

---

## <a name="attributes"></a>Atributos

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [bibliotecas de classes de C#](functions-dotnet-class-library.md), utilize o atributo [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs).

Aqui está um atributo `EventGridTrigger` em uma assinatura de método:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Para ver um exemplo completo, confira o exemplo de C#.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) permite configurar declarativamente uma associação de grade de eventos fornecendo valores de configuração. Consulte as seções [exemplo](#example) e [configuração](#configuration) para obter mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json*. Não há parâmetros ou propriedades do construtor para definir o atributo `EventGridTrigger`.

|Propriedade function.json |Description|
|---------|---------|
| **tipo** | Obrigatório – deve ser definido como `eventGridTrigger`. |
| **direction** | Obrigatório – deve ser definido como `in`. |
| **name** | Obrigatório - o nome da variável usado no código de função para o parâmetro que recebe os dados de eventos. |

## <a name="usage"></a>Uso

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Em funções v1 se você tentar associar ao `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador exibirá uma mensagem "substituído" e avisá-lo para usar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para usar o tipo mais recente, fazer referência a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet empacotar e qualificar totalmente o `EventGridEvent` nome do tipo, prefixando-o com `Microsoft.Azure.EventGrid.Models`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

No Azure Functions 1. x, você pode usar os seguintes tipos de parâmetro para o gatilho de grade de eventos:

* `JObject`
* `string`

No Azure Functions 2. x e superior, você também tem a opção de usar o seguinte tipo de parâmetro para o gatilho de grade de eventos:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Define propriedades para os campos comuns a todos os tipos de eventos.

> [!NOTE]
> Em funções v1 se você tentar associar ao `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, o compilador exibirá uma mensagem "substituído" e avisá-lo para usar `Microsoft.Azure.EventGrid.Models.EventGridEvent` em vez disso. Para usar o tipo mais recente, fazer referência a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet empacotar e qualificar totalmente o `EventGridEvent` nome do tipo, prefixando-o com `Microsoft.Azure.EventGrid.Models`. Para obter informações sobre como fazer referência a pacotes do NuGet em uma função de script C#, consulte [pacotes usando o NuGet](functions-reference-csharp.md#using-nuget-packages)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A instância da grade de eventos está disponível por meio do parâmetro configurado na propriedade `name` do arquivo *Function. JSON* .

# <a name="pythontabpython"></a>[Python](#tab/python)

A instância da grade de eventos está disponível por meio do parâmetro configurado na propriedade `name` do arquivo *Function. JSON* , digitada como `func.EventGridEvent`.

# <a name="javatabjava"></a>[Java](#tab/java)

A instância de evento da grade de eventos está disponível por meio do parâmetro associado ao atributo `EventGridTrigger`, digitado como um `EventSchema`. Consulte o [exemplo](#example) para obter mais detalhes.

---

## <a name="event-schema"></a>Esquema do evento

Os dados de uma Grade de Eventos são recebidos como um objeto JSON no corpo de uma solicitação HTTP. O JSON é semelhante ao exemplo a seguir:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

O exemplo mostrado é uma matriz de um elemento. A Grade de Eventos envia sempre uma matriz e pode enviar mais de um evento na matriz. O runtime invoca sua função uma vez para cada elemento da matriz.

As propriedades de nível superior nos dados JSON de evento serão as mesmas entre todos os tipos de eventos, enquanto os conteúdos da propriedade `data` estiverem especificados para cada tipo de evento. O exemplo mostrado é para um evento de armazenamento de Blobs.

Para obter explicações sobre as propriedades comuns e específicas de evento, consulte [Propriedades do evento](../event-grid/event-schema.md#event-properties) na documentação da Grade de Eventos.

O tipo `EventGridEvent` define apenas as propriedades de nível superior; a propriedade `Data` é um `JObject`.

## <a name="create-a-subscription"></a>Criar uma assinatura

Para iniciar o recebimento de solicitações HTTP de Grade de Eventos, crie uma assinatura na Grade de Eventos que especifique a URL do ponto de extremidade que invoca a função.

### <a name="azure-portal"></a>Portal do Azure

Para as funções que você desenvolve no Portal do Azure com o gatilho de Grade de Eventos, selecione **Adicionar assinatura da Grade de Eventos**.

![Criar assinatura no portal](media/functions-bindings-event-grid/portal-sub-create.png)

Ao selecionar esse link, o portal abrirá a página **Criar Assinatura de Evento**  com a URL do ponto de extremidade preenchida.

![URL do ponto de extremidade preenchida](media/functions-bindings-event-grid/endpoint-url.png)

Para obter mais informações sobre como criar assinaturas usando o Portal do Azure, consulte [Criar evento personalizado - Portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Grade de Eventos.

### <a name="azure-cli"></a>Azure CLI

Para criar uma assinatura usando [a CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), use o comando [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create).

O comando requer a URL do ponto de extremidade que invoca a função. O exemplo a seguir mostra o padrão de URL específico da versão:

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>runtime versão 1.x

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A chave do sistema é uma chave de autorização que deve ser incluída na URL do ponto de extremidade para um gatilho de Grade de Eventos. A seção a seguir explica como obter a chave do sistema.

Apresentamos aqui um exemplo que assina em uma conta de armazenamento de Blobs (com um espaço reservado para a chave do sistema):

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>runtime versão 1.x

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Para obter mais informações sobre como criar uma assinatura, consulte o [Guia de início rápido do armazenamento de blobs](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) ou outros guias de início rápido da Grade de Eventos.

### <a name="get-the-system-key"></a>Obter a chave do sistema

Você pode obter a chave do sistema usando a seguinte API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Tempo de execução da versão 2. x (e superior)

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>runtime versão 1.x

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Esta é uma API de administração, por isso, requer sua [chave mestre](functions-bindings-http-webhook.md#authorization-keys) do aplicativo. Não confunda a chave do sistema (para invocar uma função de gatilho de grade de eventos) com a chave mestra (para executar tarefas administrativas no aplicativo de funções). Ao assinar em um tópico da Grade de Eventos, certifique-se de usar a chave do sistema.

Aqui, está um exemplo da resposta que fornece a chave do sistema:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

Você pode obter a chave mestra para seu aplicativo de função na guia **Configurações do aplicativo de função** no portal.

> [!IMPORTANT]
> A chave mestra fornece acesso de administrador para seu aplicativo de funções. Não compartilhe essa chave com terceiros ou distribua-a em aplicativos clientes nativos.

Para obter mais informações, consulte [Chaves de autorização](functions-bindings-http-webhook.md#authorization-keys) no artigo de referência de gatilho HTTP.

Como alternativa, você mesmo pode enviar uma HTTP PUT para especificar o valor da chave.

## <a name="local-testing-with-viewer-web-app"></a>Teste local com o aplicativo Web visualizador

Para testar um gatilho de Grade de Eventos localmente, você deve receber solicitações HTTP de Grade de Eventos entre suas origens na nuvem para sua máquina local. Uma maneira de fazer isso é capturar solicitações online e manualmente reenviá-las em sua máquina local:

1. [Criar um aplicativo Web visualizador](#create-a-viewer-web-app) que captura as mensagens de evento.
1. [Criar uma assinatura da Grade de Eventos](#create-an-event-grid-subscription) que envia eventos para o aplicativo visualizador.
1. [Gerar uma solicitação](#generate-a-request) e copiar o corpo da solicitação do aplicativo visualizador.
1. [Postar manualmente a solicitação](#manually-post-the-request) para a URL localhost da sua função de gatilho da Grade de Eventos.

Quando terminar de testar, você poderá usar a mesma assinatura para a produção atualizando o ponto de extremidade. Use o comando da CLI do Azure[az eventgrid event-subscription update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update).

### <a name="create-a-viewer-web-app"></a>Criar um aplicativo Web visualizador

Para simplificar as mensagens de evento de captura, implante um [aplicativo Web predefinido](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de evento. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Selecione **Implantar no Azure** para implantar a solução na sua assinatura. No portal do Azure, forneça os valores para os parâmetros.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você verá o site, mas nenhum evento ainda estará publicado.

![Exibir novo site](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

Crie uma assinatura da Grade de Eventos do tipo que você deseja testar e forneça a ela a URL do aplicativo Web como o ponto de extremidade para a notificação de eventos. O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`. Portanto, a URL completa é `https://<your-site-name>.azurewebsites.net/api/updates`

Para obter mais informações sobre como criar assinaturas usando o portal do Azure, confira [Criar um evento personalizado – portal do Azure](../event-grid/custom-event-quickstart-portal.md) na documentação da Grade de Eventos.

### <a name="generate-a-request"></a>Gerar uma solicitação

Dispare um evento que gerará tráfego HTTP para o ponto de extremidade do aplicativo Web.  Por exemplo, se você criou uma assinatura de armazenamento de Blobs, faça upload ou exclua um blob. Quando uma solicitação for exibida no aplicativo Web, copie o corpo da solicitação.

A solicitação de validação de assinatura será recebida primeiro. Ignore quaisquer solicitações de validação e copie a solicitação de evento.

![Copiar o corpo da solicitação do aplicativo Web](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>Postar manualmente a solicitação

Execute sua função de Grade de Eventos localmente.

Use uma ferramenta como [Postman](https://www.getpostman.com/) ou [curl](https://curl.haxx.se/docs/httpscripting.html) para criar uma solicitação HTTP POST:

* Defina um cabeçalho `Content-Type: application/json`.
* Defina um cabeçalho `aeg-event-type: Notification`.
* Cole os dados RequestBin no corpo da solicitação.
* Poste para a URL da sua função de gatilho de grade de eventos.
  * Para 2. x e superior, use o seguinte padrão:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Para uso de 1. x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

O parâmetro `functionName` deverá ser o nome especificado no atributo `FunctionName`.

As capturas de tela a seguir mostram os cabeçalhos e o corpo da solicitação em Postman:

![Cabeçalhos em Postman](media/functions-bindings-event-grid/postman2.png)

![Corpo da solicitação em Postman](media/functions-bindings-event-grid/postman.png)

A função de gatilho da Grade de Eventos executa e mostra logs semelhantes ao exemplo a seguir:

![Amostra de logs da função de gatilho de Grade de Eventos](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Saiba mais sobre a Grade de Eventos](../event-grid/overview.md)

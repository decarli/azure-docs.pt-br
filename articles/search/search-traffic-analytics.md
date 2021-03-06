---
title: Relatório sobre dados de análise de tráfego de pesquisa
titleSuffix: Azure Cognitive Search
description: Habilite a análise de tráfego de pesquisa para Pesquisa Cognitiva do Azure, colete telemetria e eventos iniciados pelo usuário usando Application Insights e, em seguida, analise as descobertas em um relatório Power BI.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 53fd02856a805f8bb5d7261cc9e6e32861b2b4fd
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426998"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Implementar análise de tráfego de pesquisa no Azure Pesquisa Cognitiva

Análise de tráfego de pesquisa é um padrão de implementação de um loop de comentários para seu serviço de pesquisa. O objetivo é coletar telemetria em eventos de clique iniciados pelo usuário e entradas de teclado. Usando essas informações, você pode determinar a eficácia de sua solução de pesquisa, incluindo termos de pesquisa populares, taxa de clickthrough e quais entradas de consulta resultam em zero resultados.

Esse padrão usa uma dependência de [Application insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) (um recurso de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/)) para coletar dados do usuário. Você também precisará adicionar instrumentação ao seu código de cliente, conforme descrito neste artigo. Por fim, você precisará de um mecanismo de relatório para analisar os dados. É recomendável Power BI, mas você pode usar qualquer ferramenta que se conecte ao Application Insights.

> [!NOTE]
> O padrão descrito neste artigo é para cenários avançados e dados cliques gerados pelo seu cliente. Como alternativa, você pode relatar informações de log geradas pelo serviço de pesquisa. Para obter mais informações sobre relatórios de log de serviço, consulte [Monitor de consumo de recursos e atividade de consulta](search-monitor-usage.md).

## <a name="identify-relevant-search-data"></a>Identificar dados de pesquisa relevantes

Para ter métricas de pesquisa úteis, é necessário registrar alguns sinais dos usuários do aplicativo de pesquisa. Esses sinais indicam o conteúdo no qual os usuários estão interessados e que eles consideraram relevantes para suas necessidades.

Há dois sinais necessários para a Análise do Tráfego de Pesquisa:

+ Eventos de pesquisa gerados pelo usuário: somente consultas de pesquisa iniciadas por um usuário são interessantes. Solicitações de pesquisa usadas para preencher facetas, conteúdo adicional ou qualquer informação interna, não são importantes e distorcem e influenciam seus resultados.

+ Eventos de clique gerados pelo usuário: por cliques neste documento, nos referimos a um usuário que seleciona um resultado de pesquisa específico retornado de uma consulta de pesquisa. Um clique geralmente significa que um documento é um resultado relevante para uma consulta de pesquisa específica.

Ao vincular a pesquisa e clicar em eventos com uma ID de correlação, é possível analisar os comportamentos dos usuários em seu aplicativo. Essas informações de pesquisa são impossíveis de obter apenas com os logs de tráfego de pesquisa.

## <a name="add-search-traffic-analytics"></a>Adicionar análise de tráfego de pesquisa

Os sinais mencionados na seção anterior devem ser coletados do aplicativo de pesquisa à medida que o usuário interage com ele. O Application Insights é uma solução de monitoramento extensível, disponível para várias plataformas e com opções flexíveis de instrumentação. O uso de Application Insights permite que você tire proveito dos relatórios de pesquisa de Power BI criados pelo Azure Pesquisa Cognitiva para facilitar a análise dos dados.

Na página do [portal](https://portal.azure.com) do serviço de pesquisa cognitiva do Azure, a página Pesquisa análise de tráfego contém uma folha de consulta para seguir esse padrão de telemetria. Também é possível selecionar ou criar um recurso do Application Insights e ver os dados necessários, tudo em um só lugar.

![Instruções da Análise do Tráfego de Pesquisa][1]

## <a name="1---select-a-resource"></a>1 - Selecionar um recurso

Você precisa selecionar um recurso do Application Insights para uso ou criar um, caso você não tenha um. Você pode usar um recurso que já está sendo usado para registrar os eventos personalizados necessários.

Ao criar um novo recurso do Application Insights, todos os tipos de aplicativo são válidos para esse cenário. Selecione aquele que melhor se adapta a plataforma que você está usando.

Você precisa da chave de instrumentação para criar o cliente de telemetria para seu aplicativo. Você pode obtê-la no painel do portal do Application Insights, ou você pode obtê-la na página da Análise do Tráfego de Pesquisa, selecionando a instância que você quer usar.

## <a name="2---add-instrumentation"></a>2 - Adicionar instrumentação

Esta etapa é onde você instrumenta seu próprio aplicativo de pesquisa, usando o recurso Application Insights criado na etapa acima. Há quatro etapas nesse processo:

**Etapa 1: criar um cliente de telemetria**

Esse é o objeto que envia eventos para o recurso de Application Insights.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

Para outras plataformas e linguagens, consulte a [lista completa](https://docs.microsoft.com/azure/application-insights/app-insights-platforms).

**Etapa 2: solicitar uma ID de pesquisa para correlação**

Para correlacionar as solicitações de pesquisa com cliques, é necessário ter uma ID de correlação que relaciona esses dois eventos distintos. O Azure Pesquisa Cognitiva fornece uma ID de pesquisa quando você a solicita com um cabeçalho:

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**Etapa 3: eventos de pesquisa de log**

Sempre que uma solicitação de pesquisa é emitida por um usuário, você deve registrá-la como um evento de pesquisa com o esquema a seguir em um evento personalizado do Application Insights:

**SearchServiceName**: (cadeia de caracteres) nome do serviço de pesquisa **searchId**: (GUID) identificador exclusivo da consulta de pesquisa (vem na resposta de pesquisa) IndexName: (String **) índice do**serviço de pesquisa a ser consultado **QueryTerms**: (cadeia de caracteres) termos de pesquisa inseridos pelo usuário **ResultCount**: (int) número de documentos que foram retornados (vem na resposta de pesquisa) **ScoringProfile**: (String) nome do perfil de Pontuação usado, se houver

> [!NOTE]
> Solicite a contagem de consultas geradas pelo usuário adicionando $count=true à consulta de pesquisa. Confira mais informações [aqui](https://docs.microsoft.com/rest/api/searchservice/search-documents#request)
>

> [!NOTE]
> Lembre-se de registrar apenas as consultas de pesquisa geradas pelos usuários.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**Etapa 4: eventos de clique em log**

Sempre que um usuário clica em um documento, esse é um sinal que deve ser registrado para fins de análise de pesquisa. Use os eventos personalizados do Application Insights para registrar esses eventos com o esquema a seguir:

**ServiceName**: (cadeia de caracteres) nome do serviço de pesquisa **SearchId**: (guid) identificador exclusivo da consulta de pesquisa relacionada **DocId**: (cadeia de caracteres) identificador do documento **Position**: (int) classificação do documento na página de resultados da pesquisa

> [!NOTE]
> Position refere-se à ordem cardinal em seu aplicativo. Você tem a liberdade para definir esse número, desde que seja sempre o mesmo, para permitir a comparação.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Analisar no Power BI

Depois de instrumentar seu aplicativo e verificar se seu aplicativo está conectado corretamente ao Application Insights, você baixa um modelo de relatório predefinido para analisar dados no Power BI desktop. O relatório contém gráficos predefinidos e tabelas úteis para analisar os dados adicionais capturados para análise de tráfego de pesquisa. 

1. No painel de navegação à esquerda do painel de Pesquisa Cognitiva do Azure, em **configurações**, clique em **análise de tráfego de pesquisa**.

2. Na página **Análise de tráfego de pesquisa**, na etapa 3, clique em **Obter o Power BI Desktop** para instalar o Power BI.

   ![Obter Power BI relatórios](./media/search-traffic-analytics/get-use-power-bi.png "Obter Power BI relatórios")

2. Na mesma página, clique em **baixar Power bi relatório**.

3. O relatório é aberto no Power BI Desktop e você é solicitado a se conectar ao Application Insights e fornecer credenciais. Você pode encontrar informações de conexão nas páginas de portal do Azure para o recurso de Application Insights. Para credenciais, forneça o mesmo nome de usuário e senha que você usa para entrar no Portal.

   ![Conectar-se ao Application Insights](./media/search-traffic-analytics/connect-to-app-insights.png "Conectar-se ao Application Insights")

4. Clique em **Carregar**.

O relatório contém gráficos e tabelas que ajudam você a tomar decisões mais inteligentes a fim de melhorar o desempenho e a relevância da pesquisa.

As métricas incluem os seguintes itens:

* Pesquisar o volume e os pares de documentos de termo mais populares: termos que resultam no mesmo documento clicado, ordenados por cliques.
* Pesquisa sem cliques: termos das principais consultas que não registraram nenhum clique

A captura de tela a seguir mostra os relatórios e gráficos internos para análise de tráfego de pesquisa.

![Painel de Power BI para Pesquisa Cognitiva do Azure](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Painel de Power BI para Pesquisa Cognitiva do Azure")

## <a name="next-steps"></a>Próximos passos
Instrumente seu aplicativo de pesquisa para obter dados informativos e avançados sobre o serviço de pesquisa.

Você pode encontrar mais informações sobre o [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) e visitar a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/) para saber mais sobre suas diferentes camadas de serviço.

Saiba mais sobre como criar relatórios incríveis. Consulte [introdução ao Power bi desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes.

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png

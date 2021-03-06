---
title: Novos comunicados de recursos
titleSuffix: Azure Cognitive Search
description: Anúncios de recursos novos e aprimorados, incluindo uma renomeação de serviço de Azure Search para o Azure Pesquisa Cognitiva.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a9c64f4bafa770de9bbbd97ae9a0295b72d81f54
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719816"
---
# <a name="whats-new-in-azure-cognitive-search"></a>O que há de novo no Azure Pesquisa Cognitiva

Conheça o que há de novo no serviço. Marque esta página para manter-se atualizado com o serviço.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Novo nome do serviço

Azure Search agora é renomeado para o **Azure pesquisa cognitiva** para refletir o uso expandido (ainda opcional) de habilidades cognitivas e processamento de ia em operações principais. As versões de API, pacotes NuGet, namespaces e pontos de extremidade não são alterados. As soluções de pesquisa novas e existentes não são afetadas pela alteração do nome do serviço.

## <a name="feature-announcements"></a>Anúncios de recursos

### <a name="january-2020"></a>Janeiro de 2020

+ [As chaves de criptografia gerenciadas pelo cliente](search-security-manage-encryption-keys.md) agora estão disponíveis para o público geral. Se você estiver usando o REST, poderá acessar o recurso usando `api-version=2019-05-06`. Para código gerenciado, o pacote correto ainda é o [SDK do .NET versão 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , embora o recurso esteja fora de visualização. 

+ O acesso privado a um serviço de pesquisa está disponível por meio de dois mecanismos:

  + Você pode restringir o acesso a endereços IP específicos usando a API REST de gerenciamento `api-version=2019-10-01-Preview` para criar o serviço. A API de visualização tem novas propriedades **IpRule** e **NetworkRuleSet** na [API CreateOrUpdate](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate). Esse recurso de visualização está disponível em regiões selecionadas. Para obter mais informações, consulte [como usar a API REST de gerenciamento](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Atualmente disponível por meio de uma visualização de acesso limitado, você pode provisionar um serviço de Azure Search que dá suporte ao ponto de extremidade privado do Azure para conexões de clientes na mesma rede virtual. Para obter mais informações, consulte [criar um ponto de extremidade privado para uma conexão segura](service-create-private-endpoint.md).

### <a name="december-2019"></a>Dezembro de 2019

+ [Criar aplicativo (versão prévia)](search-create-app-portal.md) é um novo assistente no portal que gera um arquivo HTML para download. O arquivo vem com um script incorporado que renderiza um aplicativo Web de estilo "localhost" operacional, associado a um índice em seu serviço de pesquisa. As páginas são configuráveis no assistente e podem conter uma barra de pesquisa, área de resultados, navegação de barra lateral e suporte a consultas typeahead. Você pode modificar o HTML offline para estender ou personalizar o fluxo de trabalho ou a aparência.

+ [Criar um ponto de extremidade privado para conexões seguras (visualização)](service-create-private-endpoint.md) explica como configurar um link privado para conexões seguras para o serviço de pesquisa. Esse recurso de visualização está disponível mediante solicitação e usa o [link privado do Azure](../private-link/private-link-overview.md) e a [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) como parte da solução.

### <a name="november-2019---ignite-conference"></a>2019 de novembro-conferência Ignite

+ O [enriquecimento incremental (visualização)](cognitive-search-incremental-indexing-conceptual.md) adiciona Caching e statefullness a um pipeline de enriquecimento para que você possa trabalhar em etapas ou fases específicas sem perder o conteúdo que já foi processado. Anteriormente, qualquer alteração em um pipeline de enriquecimento exigia uma recompilação completa. Com o enriquecimento incremental, a saída da análise dispendiosa, especialmente a análise de imagens, é preservada.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [extração de documentos (visualização)](cognitive-search-skill-document-extraction.md) é uma habilidade cognitiva usada durante a indexação que permite extrair o conteúdo de um arquivo de dentro de um conferente. Anteriormente, a quebra de documento ocorreu apenas antes da execução do concodificador. Com a adição dessa habilidade, você também pode executar essa operação dentro da execução do conconjunto de habilidades.

+ A [conversão de texto (visualização)](cognitive-search-skill-text-translation.md) é uma habilidade cognitiva usada durante a indexação que avalia o texto e, para cada registro, retorna o texto traduzido para o idioma de destino especificado.

+ Os [modelos de Power bi](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) podem iniciar suas visualizações e a análise de conteúdo aprimorado em uma loja de conhecimento em Power bi área de trabalho. Este modelo foi projetado para projeções de tabela do Azure criadas por meio do [Assistente de importação de dados](knowledge-store-create-portal.md).

+ [Azure data Lake Storage Gen2 (visualização](search-howto-index-azure-data-lake-storage.md)), [Cosmos DB API Gremlin (visualização)](search-howto-index-cosmosdb.md)e [Cosmos DB API do Cassandra (versão prévia)](search-howto-index-cosmosdb.md) agora têm suporte em indexadores. Você pode se inscrever usando [este formulário](https://aka.ms/azure-cognitive-search/indexer-preview). Você receberá um email de confirmação quando tiver sido aceito no programa de visualização.

### <a name="july-2019"></a>Julho de 2019

+ Geralmente disponível na [nuvem do Azure governamental](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Atualizações de serviço

Os [comunicados de atualização de serviço](https://azure.microsoft.com/updates/?product=search&status=all) do Azure pesquisa cognitiva podem ser encontrados no site do Azure.
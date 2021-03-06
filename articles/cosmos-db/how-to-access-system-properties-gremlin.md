---
title: Acessar as propriedades do documento do sistema por meio do Grafo do Azure Cosmos DB
description: Saiba como ler e gravar as propriedades do documento do sistema do Cosmos DB por meio da API do Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: e762674936ab2fbdf198ca67f79acfa545127f02
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755067"
---
# <a name="system-document-properties"></a>Propriedades do documento do sistema

O Azure Cosmos DB tem [propriedades do sistema](https://docs.microsoft.com/rest/api/cosmos-db/databases) tais como ```_ts```, ```_self```, ```_attachments```, ```_rid``` e ```_etag``` em todos os documentos. Além disso, o mecanismo do Gremlin adiciona as propriedades ```inVPartition``` e ```outVPartition``` nas bordas. Por padrão, essas propriedades estão disponíveis para passagem. No entanto, é possível incluir propriedades específicas ou todas elas em passagem de Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Etag

Esta propriedade é usada para controle de simultaneidade otimista. Se o aplicativo precisar dividir a operação em passagens diferentes, poderá usar a propriedade eTag para evitar a perda de dados em gravações simultâneas.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Vida útil (TTL)

Se a coleção tiver a expiração de documento habilitada e os documentos tiverem a propriedade ```ttl``` definida neles, então, essa propriedade estará disponível em passagem de Gremlin como uma propriedade de vértice ou borda regular. ```ProjectionStrategy``` não é necessário para habilitar a exposição da propriedade de vida útil.

O vértice criado com a passagem abaixo será excluído automaticamente em **123 segundos**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Próximas etapas
* [Simultaneidade otimista do Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [TTL (vida útil)](time-to-live.md) no Azure Cosmos DB

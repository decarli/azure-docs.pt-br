---
title: Junções em Apache Hive clientes potenciais para o erro OutOfMemory-Azure HDInsight
description: Lidando com erros de OutOfMemory "limite de sobrecarga de GC excedido"
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ab334dfb15044fd0734a107c12003ca2c1f86906
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75895180"
---
# <a name="scenario-joins-in-apache-hive-leads-to-an-outofmemory-error-in-azure-hdinsight"></a>Cenário: junções em Apache Hive leva a um erro de OutOfMemory no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao usar componentes de consulta interativa em clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O comportamento padrão para junções de Apache Hive é carregar todo o conteúdo de uma tabela na memória para que uma junção possa ser executada sem a necessidade de executar uma etapa de mapeamento/redução. Se a tabela do hive for muito grande para caber na memória, a consulta poderá falhar.

## <a name="cause"></a>Causa

Ao executar junções no hive do tamanho suficiente, o seguinte erro é encontrado:

```
Caused by: java.lang.OutOfMemoryError: GC overhead limit exceeded error.
```

## <a name="resolution"></a>Resolução

Impedir que o hive carregue tabelas na memória em junções (em vez de executar uma etapa de mapeamento/redução) definindo o seguinte valor de configuração do hive:

```
hive.auto.convert.join=false
```

## <a name="next-steps"></a>Próximos passos

Se a configuração desse valor não resolver o problema, visite um dos seguintes...

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).

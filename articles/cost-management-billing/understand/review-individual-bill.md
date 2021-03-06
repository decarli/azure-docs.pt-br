---
title: Examinar sua fatura individual do Azure
description: Saiba como entender seu uso de recursos e sua fatura e como verificar os encargos de sua assinatura individual do Azure.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75984121"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Tutorial: Examinar sua fatura individual do Azure

Este artigo ajuda você a entender e examinar sua fatura do Azure. Para cada período de cobrança, você normalmente recebe uma fatura por email. A fatura é uma representação de sua conta do Azure. As mesmas informações de custo na fatura estão disponíveis no portal do Azure. Neste tutorial, você comparará sua fatura com o arquivo de uso diário detalhado e com a análise de custos no portal do Azure.

Este tutorial se aplica somente aos clientes do Azure com uma assinatura individual. As assinaturas individuais comuns são aquelas com tarifas pagas conforme o uso adquiridas diretamente do site do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Comparar encargos faturados com o arquivo de uso
> * Comparar encargos e uso na análise de custo

## <a name="prerequisites"></a>Prerequisites

Você precisa ter uma conta de cobrança paga do *Programa Microsoft Online Services*. A conta é criada quando você se inscreve no Azure por meio do site do Azure. Por exemplo, se você tiver uma [conta com tarifas pagas conforme o uso](https://azure.microsoft.com/offers/ms-azr-0003p/) ou se for um [assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

As faturas das [Contas Gratuitas do Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) são criadas somente quando o valor de crédito mensal é ultrapassado.

É necessário que você tenha assinado o Azure há mais de 30 dias. O Azure cobra você no final do período de faturamento.

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Comparar encargos faturados com o arquivo de uso

<a name="charges"></a>

A primeira etapa para comparar o uso e os custos é baixar seus arquivos de fatura e uso. O arquivo CSV de uso detalhado mostra os encargos por período de cobrança e de uso diário. Ele não inclui nenhuma informação de impostos. Para baixar os arquivos, você precisa ser um administrador da conta ou ter a função de Proprietário.

No portal do Azure, digite *assinaturas* na caixa de pesquisa e clique em [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Navegar para assinaturas](./media/review-individual-bill/navigate-subscriptions.png)

Na lista de assinaturas, clique na assinatura.

Em **Cobrança**, selecione **Faturas**.

Na lista de faturas, procure aquela que você deseja baixar e clique no símbolo de download. Talvez seja necessário alterar o período para exibir as faturas mais antigas. Pode levar alguns minutos para gerar o arquivo de detalhes de uso e a fatura.

![Captura de tela que mostra os períodos de cobrança, a opção de download e as cobranças totais para cada período de cobrança](./media/review-individual-bill/download-invoice.png)

Na janela Baixar Uso + Encargos, clique em **Baixar CSV** e em **Baixar fatura**.

![Captura de tela que mostra a página Baixar fatura e uso](./media/review-individual-bill/usageandinvoice.png)

Caso a mensagem **Não disponível** apareça, há vários motivos pelos quais você não vê os detalhes de uso ou uma fatura:

- Faz menos de 30 dias que você se inscreveu no Azure.
- Não há uso no período de cobrança.
- A fatura ainda não foi gerada. Aguarde até o final do período de cobrança.
- Você não tem permissão para exibir faturas. Talvez você não veja as faturas antigas a menos que seja o Administrador da Conta. Para saber mais sobre como obter acesso a informações de cobrança, consulte [Gerenciar o acesso à cobrança do Azure usando funções](../manage/manage-billing-access.md).
- Se você tiver uma Avaliação Gratuita ou uma quantidade de crédito mensal com sua assinatura que você não excedeu, você não receberá uma fatura, a menos que tenha um Contrato de Cliente da Microsoft.

Em seguida, examine os encargos. Sua fatura mostra valores dos impostos e seus encargos de uso.

![Exemplo de fatura do Azure](./media/review-individual-bill/invoice-usage-charge.png)

Abra o arquivo de uso CSV que você baixou. No final do arquivo, some o valor de todos os itens na coluna *Custo*.

![Exemplo de arquivo de uso com o custo somado](./media/review-individual-bill/usage-file-usage-charges.png)

 O valor do *Custo* somado deve corresponder precisamente ao custo dos *encargos de uso* em sua fatura.

Os encargos de uso são exibidos no nível do medidor. Os termos na lista a seguir têm o mesmo significado na fatura e no arquivo de uso detalhado. Por exemplo, o ciclo de faturamento na fatura é o mesmo que o período de faturamento mostrado no arquivo de uso detalhado.

| Fatura (PDF) | Uso detalhado (CSV)|
| --- | --- |
|Ciclo de cobrança | BillingPeriodStartDate BillingPeriodEndDate |
|Nome |Categoria de medidor |
|Type |Subcategoria de medidor |
|Recurso |MeterName |
|Região |MeterRegion |
|Consumido | Quantidade |
|Incluso |Quantidade incluída |
|Faturável |Quantidade de excesso |
|Tarifa | EffectivePrice|
| Valor | Custo |

A seção **Encargos de Uso** da sua fatura mostra o valor total (custo) de cada medidor que foi consumido durante o período de cobrança. Por exemplo, a imagem a seguir mostra um encargo de uso do serviço do Armazenamento do Microsoft Azure para o recurso *Discos P10*.

![Encargos de uso da fatura](./media/review-individual-bill/invoice-usage-charges.png)

No arquivo de uso CSV, filtre por *MeterName* o recurso correspondente mostrado na fatura. Em seguida, some o valor do *Custo* para os itens na coluna. Veja um exemplo.

![Valor do arquivo de uso somado para MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

O valor do *Custo* somado deve corresponder precisamente ao custo dos *encargos de uso* do recurso individual cobrado em sua fatura.

Para obter mais informações, confira [Entender sua fatura do Azure](understand-invoice.md) e [Entender o uso detalhado do Azure](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Comparar encargos e uso na análise de custo

A análise de custo no portal do Azure também pode lhe ajudar a verificar seus encargos. Para ter uma visão geral rápida do uso e dos encargos faturados, selecione sua assinatura na [Página de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no portal do Azure. Em seguida, clique em **Análise de custo** e, na lista modos de exibição, clique em **Detalhes da fatura**.

![Exemplo mostrando a seleção de detalhes da fatura](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Em seguida, na lista intervalo de datas, selecione um período para a fatura. Adicione um filtro para o número de fatura e, em seguida, selecione o InvoiceNumber que corresponde ao de sua fatura. A análise de custo mostra os detalhes de custo dos itens faturados.

![Exemplo mostrando detalhes de custos faturados na análise de custo](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

Os custos mostrados na análise de custo devem corresponder precisamente ao custo dos *encargos de uso* do recurso individual cobrado em sua fatura.

![Encargos de uso da fatura](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Serviços externos cobrados separadamente

Serviços externos ou encargos de Marketplace são para recursos que foram criados por fornecedores de software de terceiros. Esses recursos estão disponíveis para uso no Azure Marketplace. Por exemplo, um Barracuda Firewall é um recurso do Azure Marketplace oferecido por terceiros. Todos os encargos para o firewall e seus medidores correspondentes aparecem como cobranças de serviços externos.

As taxas de serviço externas são cobradas separadamente. Os encargos não aparecem na fatura do Azure. Para saber mais, confira [Entender os encargos dos serviços externos do Azure](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Recursos cobrados por medidores de uso

O Azure não fatura diretamente com base no custo do recurso. Os encargos de um recurso são calculados usando um ou mais medidores. Medidores são usados para rastrear o uso de um recurso durante todo o seu tempo de vida. Esses medidores são usados para calcular a conta.

Quando você cria um recurso do Azure, como uma máquina virtual, ele tem uma ou mais instâncias de medidor criadas. Medidores são usados para rastrear o uso do recurso ao longo do tempo. Cada medidor emite registros de uso usados pelo Azure para calcular a fatura.

Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para rastrear seu uso:

- Calcular horas
- Horas de endereço IP
- Transferência de dados em
- Transferência de dados
- Disco gerenciado padrão
- Operações de disco gerenciado padrão
- Disco IO padrão
- Leitura de blob padrão do bloco IO
- Escrita de Blob Padrão de Bloqueio de E/S
- Bloqueio de blobs padrão IO-Block

Quando a VM é criada, cada medidor começa a emitir registros de uso. Esse uso e o preço do medidor são rastreados no sistema de medição do Azure.

Você pode ver os medidores que foram usados para calcular sua fatura no arquivo CSV de uso.

## <a name="payment"></a>Pagar sua fatura

Se você tiver configurado um cartão de crédito como forma de pagamento, o pagamento será cobrado automaticamente em até 10 dias após o término do período de cobrança. Na sua declaração de cartão de crédito, o item de linha diria **MSFT Azure**.

Para alterar o cartão de crédito cobrado, confira [Adicionar, atualizar ou remover um cartão de crédito do Azure](../manage/change-credit-card.md).

Se você [pagar pela fatura](../manage/pay-by-invoice.md), envie seu pagamento para o local listado na parte inferior da fatura.

Para verificar o status do seu pagamento, [crie um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Comparar encargos faturados com o arquivo de uso
> * Comparar encargos e uso na análise de custo

Conclua o guia de início rápido para começar a usar a análise de custo.

> [!div class="nextstepaction"]
> [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md)

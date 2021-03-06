---
title: Otimizar custos do Azure Databricks com uma pré-compra
description: Saiba como é possível pagar antecipadamente os encargos do Azure Databricks com capacidade reservada para economizar dinheiro.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: bb3aa7970f993ec5e0ceb50d10cc0907ed225c2f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995865"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Otimizar custos do Azure Databricks com uma pré-compra

É possível economizar os custos de DBU (unidade do Azure Databricks) quando você compra antecipadamente DBCU (unidades de confirmação do Azure Databricks) por um ou três anos. É possível usar os DBCUs comprados previamente a qualquer momento durante a vigência da compra. Diferentemente das VMs, as unidades compradas previamente não expiram por hora e você as usa a qualquer momento durante a vigência da compra.

Qualquer uso do Azure Databricks é deduzido das DBUs compradas previamente de maneira automática. Não é necessário reimplantar ou atribuir um plano comprado previamente aos workspaces do Azure Databricks para o uso da DBU para obter os descontos de compra prévia.

O desconto de compra prévia aplica-se apenas ao uso da DBU. Outros encargos, como computação, armazenamento e rede, são cobrados separadamente.

## <a name="determine-the-right-size-to-buy"></a>Determinar o tamanho ser a ser comprado

A compra prévia do Databricks aplica-se a todas as cargas de trabalho e camadas do Databricks. Pense na compra prévia como um pool de unidades de confirmação do Databricks pagas previamente. O uso é deduzido do pool, independentemente da carga de trabalho ou camada. O uso é deduzido na seguinte razão:

| **Carga de trabalho** | **Razão do aplicativo DBU – Camada Standard** | **Razão do aplicativo DBU – Camada Premium** |
| --- | --- | --- |
| Análise de dados | 0,4 | 0,55 |
| Engenharia de Dados | 0.15 | 0,30 |
| Engenharia de Dados Leve | 0,07 | 0,22 |

Por exemplo, quando uma quantidade de Análise de Dados – Camada Standard é consumida, as unidades de confirmação do Databricks compradas previamente são deduzidas em 0,4 unidades.

Antes de comprar, calcule a quantidade total de DBU consumida para diferentes cargas de trabalho e camadas. Use as razões anteriores a serem normalizadas quanto à DBCU e execute uma projeção de uso total durante o próximo ano ou os próximos três anos.

## <a name="purchase-databricks-commit-units"></a>Comprar unidades de confirmação do Databricks

É possível comprar planos do Databricks no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Para comprar capacidade reservada, é necessário ter a função proprietário para pelo menos uma assinatura empresarial.

- Você deve estar em uma função de proprietário para pelo menos um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou contrato de cliente da Microsoft ou uma assinatura individual com tarifas pagas conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P).
- Para assinaturas EA, a opção Adicionar instâncias reservadas deve ser habilitada no portal de EA. Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA para a assinatura.
- Para as assinaturas Enterprise, a opção **Adicionar Instâncias Reservadas** deve estar habilitada no [Portal EA](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deverá ser Administrador de EA da assinatura.

**Para comprar:**

1. Vá para o [Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selecione uma assinatura. Use a lista **Assinatura** para selecionar a assinatura usada para pagar a capacidade reservada. A forma de pagamento da assinatura é cobrado nos custos iniciais da capacidade reservada. Os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como excedente.
1. Selecione um escopo. Use a lista **Escopo** para selecionar um escopo da assinatura:
    - **Escopo de grupo de recursos único** — aplica o desconto de reserva apenas aos recursos correspondentes no grupo de recursos selecionado.
    - **Escopo de assinatura única** — aplica o desconto de reserva apenas aos recursos correspondentes na assinatura selecionada.
    - **Escopo compartilhado** — aplica o desconto de reserva aos recursos correspondentes em assinaturas qualificadas que estão no contexto de cobrança. Para clientes do Contrato Enterprise, o contexto de cobrança é o registro.
1. Selecione quantas unidades de confirmação do Azure Databricks você deseja comprar e conclua a compra.


![Exemplo mostrando a compra do Azure Databricks no portal do Azure](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Alterar escopo e propriedade

É possível realizar os seguintes tipos de alterações em uma reserva após a compra:

- Atualizar o escopo de reserva
- Acesso baseado em função

Não é possível dividir ou mesclar a unidade de confirmação do Databricks antes da compra. Para obter mais informações sobre como gerenciar reservas, confira [Gerenciar reservas após a compra](manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Cancelamentos e trocas

Não há suporte para cancelamentos e trocas nos planos de compra prévia do Databricks. Todas as compras são definitivas.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco.

Se você tiver dúvidas ou precisar de ajuda, [crie uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre as Reservas do Azure, consulte os seguintes artigos:
  - [O que são Reservas do Azure?](save-compute-costs-reservations.md)
  - [Noções básicas sobre como o desconto de DBCU de compra prévia do Azure Databricks é aplicado](reservation-discount-databricks.md)
  - [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)

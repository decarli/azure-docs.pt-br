---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180032"
---
Depois que o observador for criado, o evento `AnchorLocated` será acionado para cada âncora solicitada. Esse evento é acionado quando uma âncora é localizada ou se a âncora não puder ser localizada. Se essa situação ocorrer, o motivo será indicado no status. Após todas as âncoras de um observador serem processadas, sejam elas encontradas ou não, o evento `LocateAnchorsCompleted` será acionado. Há um limite de 35 identificadores por observador. 

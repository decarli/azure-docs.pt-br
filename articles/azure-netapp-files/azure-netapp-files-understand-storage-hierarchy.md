---
title: O que é a hierarquia de armazenamento do Azure NetApp Files | Microsoft Docs
description: Descreve a hierarquia de armazenamento, incluindo contas do Azure NetApp Files, pools de capacidade e volumes.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522878"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>O que é a hierarquia de armazenamento do Azure NetApp Files

Antes de criar um volume no Azure NetApp Files, você deve adquirir e configurar um pool de capacidade provisionada.  Para configurar um pool de capacidade, é preciso ter uma conta do NetApp. Entender a hierarquia de armazenamento ajuda a configurar e gerenciar os recursos do Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Contas do NetApp

- Uma conta do NetApp serve como um agrupamento administrativo de pools de capacidade constituintes.  
- Uma conta da NetApp não é igual à sua conta geral de armazenamento do Azure. 
- Uma conta do NetApp é regional no escopo.   
- Você pode ter várias contas da NetApp em uma região, mas cada conta do NetApp é vinculada a apenas uma única região.

## <a name="capacity_pools"></a>Pools de capacidade

- Um pool de capacidade é medido pela sua capacidade provisionada.  
- A capacidade é provisionada pelos SKUs fixos comprados (por exemplo, uma capacidade de 4 TB).
- Um pool de capacidade só pode ter um nível de serviço.  
- Cada pool de capacidade pode pertencer a apenas uma conta do NetApp. No entanto, é possível ter vários pools de capacidade dentro de uma conta do NetApp.  
- Os pools de capacidade não podem ser movidos pelas contas do NetApp.   
  Por exemplo, no [Diagrama conceitual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, o Pool de capacidade 1 não pode ser movido da conta do NetApp do Leste dos EUA para a conta do NetApp do Oeste dos EUA 2.  
- Um pool de capacidade não pode ser excluído até que todos os volumes dentro do pool de capacidade tenham sido excluídos.

## <a name="volumes"></a>Volumes

- Um volume é medido pelo consumo da capacidade lógica e é dimensionável. 
- O consumo de capacidade de um volume conta contra a capacidade provisionada do pool desse volume.
- Cada volume pertence a apenas um pool, mas um pool pode conter vários volumes. 
- Não é possível mover um volume entre pools de capacidade. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  Por exemplo, no [Diagrama conceitual da hierarquia de armazenamento](#conceptual_diagram_of_storage_hierarchy) abaixo, você não pode mover volumes do Pool de Capacidade 1 para o Pool de Capacidade 2.
- Um volume não pode ser excluído até que todos os seus instantâneos tenham sido excluídos.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Diagrama conceitual da hierarquia de armazenamento 
O exemplo a seguir mostra as relações da assinatura do Azure, contas do NetApp, pools de capacidade e volumes.   

![Diagrama conceitual da hierarquia de armazenamento](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Próximas etapas

- [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Registro no Azure NetApp Files](azure-netapp-files-register.md)

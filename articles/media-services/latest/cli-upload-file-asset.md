---
title: Exemplo de Script de CLI do Azure - Carregar um arquivo para um contêiner | Microsoft Docs
description: Este artigo mostra como usar o script de CLI do Azure para carregar um arquivo local em um contêiner de armazenamento.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: d6720200da341ee466339edde6dded02dd4fddc6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970101"
---
# <a name="cli-example-upload-a-local-file-to-a-container"></a>Exemplo de CLI: Carregar um arquivo local para um contêiner 

O script de CLI do Azure neste artigo mostra como carregar um arquivo local para um contêiner de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos 

[Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Script de exemplo

```Azure CLI 
#!/bin/bash
# Update the following variables for your own settings:
storageAccountName=build2018storage
assetContainer="asset-4c834446-7e55-4760-9a25-f2d4fb1f4657"
localFile="..\Media\ignite-short.mp4"
blobName="ignite-short.mp4"
sasToken="?sv=2015-07-08&sr=c&sig=u1uy9OIeXnZUEN62hE0bDgg%2FPXYgRDNGnQxE%2BSi51dM%3D&se=2018-04-29T18:42:02Z&sp=rwl"
# Use the az storage modules to upload a local file to the container using the SAS URL from previous step.
# If you are logged in already to the subscription with access to the storage account, you do not need to use the --sas-token at all. Just eliminate it below.
# The container name is in the SAS URL path, and should be set with the -c option.
# Use the -f option to point to a local file on your machine.
# Use the -n option to name the blob in storage.
# Use the --account-name option to point to the storage account name to use 
# Use the --sas-token option to place the SAS token after the query string from previous step. 
# NOTE that the SAS Token is only good for up to 24 hours max. 
#
az storage blob upload \
    -c $assetContainer \
    -f $localFile \
    -n $blobName \
    --account-name $storageAccountName \
    --sas-token $sasToken \
echo "press  [ENTER]  to continue."
read continue
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral dos Serviços de Mídia](media-services-overview.md)

---
title: 'Início Rápido: Criar um aplicativo do HoloLens com DirectX'
description: Neste Início Rápido, você aprenderá a criar um aplicativo do HoloLens usando as Âncoras Espaciais.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 01a9a8239e2997335f5123d63cfc664027cffa02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75376351"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Início Rápido: Criar um aplicativo do HoloLens com Âncoras Espaciais do Azure no C++/WinRT e DirectX

Este Início Rápido aborda como criar um aplicativo do HoloLens usando as [Âncoras Espaciais do Azure](../overview.md) em C++/WinRT e DirectX. As Âncoras Espaciais do Azure são um serviço de desenvolvedor multiplataforma para você criar experiências de realidade misturada usando objetos que persistem sua localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo do HoloLens que pode salvar e lembrar de uma âncora espacial.

Você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configurar o identificador e a chave de conta das Âncoras Espaciais
> * Implantar e executar em um dispositivo do HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Para concluir este início rápido, certifique-se de que:
- Um computador Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> instalado com a carga de trabalho de **desenvolvimento da Plataforma Universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** . Além disso, será necessário instalar o <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e o <a href="https://git-lfs.github.com/">Git LFS</a>.
- A [VSIX (Extensão do Visual Studio) C++/WinRT](https://aka.ms/cppwinrt/vsix) para o Visual Studio deve ser instalada do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Um dispositivo do HoloLens com o [modo de desenvolvedor](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) habilitado. Este artigo requer um dispositivo do HoloLens com a [atualização do Windows 10 de outubro de 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecida como RS5). Para atualizar para a versão mais recente no HoloLens, abra o aplicativo de **Configurações**, acesse **Atualização e Segurança** e, em seguida, selecione o botão **Verificar se há atualizações**.
- Seu aplicativo deve definir a funcionalidade **spatialPerception** no manifesto do AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra o `HoloLens\DirectX\SampleHoloLens.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar a chave e o identificador da conta

A próxima etapa é configurar o aplicativo para usar o identificador e a chave de conta. Você os copiou em um editor de texto ao [configurar o recurso Âncoras Espaciais](#create-a-spatial-anchors-resource).

Abra o `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador de conta.

## <a name="deploy-the-app-to-your-hololens"></a>Implantar o aplicativo no HoloLens

Altere a **Configuração da Solução** para **Lançamento**, altere **Plataforma da Solução** para **x86** e selecione **Dispositivo** entre as opções de destino de implantação.

Se estiver usando o HoloLens 2, use o **ARM64** como a **Plataforma de Solução**, em vez de **x86**.

![Configuração do Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre nele e conecte-o ao computador usando um cabo USB.

Selecione **Depurar** > **Iniciar depuração** para implantar seu aplicativo e iniciar a depuração.

Siga as instruções no aplicativo para colocar uma âncora e fazer recall dela.

No Visual Studio, pare o aplicativo selecionando **Parar Depuração** ou pressionando **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar as Âncoras Espaciais nos dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)

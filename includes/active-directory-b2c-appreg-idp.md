---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: dc75d78f2e73d1aa42f5fc84d39a8b22d6a121bb
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642627"
---
Para registrar um aplicativo em seu locatário Azure AD B2C, você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada **de registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicativos](#tab/applications/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
1. Insira um nome para o aplicativo. Por exemplo, *testapp1*.
1. Para **aplicativo Web/API Web**, selecione **Sim**.
1. Para **URL de resposta**, insira `https://jwt.ms`
1. Selecione **Criar**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione **novo registro**.
1. Insira um **nome** para o aplicativo. Por exemplo, *testapp1*.
1. Selecione **contas em qualquer diretório organizacional ou qualquer provedor de identidade**.
1. Em **URI de redirecionamento**, selecione **Web**e, em seguida, insira `https://jwt.ms` na caixa de texto URL.
1. Em **permissões**, marque a caixa de seleção *conceder consentimento de administrador às permissões OpenID e offline_access* .
1. Selecione **Registrar**.

Depois que o registro do aplicativo for concluído, habilite o fluxo de concessão implícita:

1. Em **gerenciar**, selecione **autenticação**.
1. Selecione **experimentar a nova experiência** (se mostrado).
1. Em **concessão implícita**, marque as caixas de seleção **tokens de acesso** e **tokens de ID** .
1. Selecione **Salvar**.
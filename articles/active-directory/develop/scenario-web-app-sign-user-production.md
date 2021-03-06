---
title: Mover o aplicativo Web que conecta os usuários à produção – plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo Web que faz logon de usuários (mover para produção)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 88ad32a289d499cd385f8f3095113a4b329b80f4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701528"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Aplicativo Web que conecta usuários: mover para produção

Agora que você sabe como obter um token para chamar APIs da Web, saiba como movê-la para produção.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Próximos passos

### <a name="scenario-for-calling-web-apis"></a>Cenário para chamar APIs Web

Depois que seu aplicativo Web entra em usuários, ele pode chamar APIs da Web em nome dos usuários conectados. Chamar APIs da Web do aplicativo Web é o objeto do seguinte cenário:

> [!div class="nextstepaction"]
> [Aplicativo Web que chama as APIs Web](scenario-web-app-call-api-overview.md)

### <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Aprofunde-se: tutorial do aplicativo Web ASP.NET Core

Saiba mais sobre outras maneiras de conectar usuários com este ASP.NET Core tutorial: 

> [!div class="nextstepaction"]
> [Habilite seus aplicativos Web para conectar usuários e chamar APIs com a plataforma de identidade da Microsoft para desenvolvedores](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Este tutorial progressivo tem código pronto para produção para um aplicativo Web, incluindo como adicionar entrada com contas no:

- Sua organização
- Várias organizações
- Contas corporativas ou de estudante ou contas pessoais da Microsoft
- [B2C do AD do Azure](https://aka.ms/aadb2c)
- Nuvens nacionais

### <a name="sample-code-java-web-app"></a>Código de exemplo: aplicativo Web Java

Saiba mais sobre o aplicativo Web Java deste exemplo no GitHub: 

> [!div class="nextstepaction"]
> [Um aplicativo Web Java que conecta usuários com a plataforma de identidade da Microsoft e chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

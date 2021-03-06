---
title: Obter um token para uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web que exigem a aquisição de um token para o aplicativo.
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1c3d6f8d47152d70bdeaabbbc6d7b81187291857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701800"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API Web que chama APIs da Web: adquirir um token para o aplicativo

Depois de criar um objeto de aplicativo cliente, use-o para adquirir um token que você pode usar para chamar uma API da Web.

## <a name="code-in-the-controller"></a>Código no controlador

Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Ele chama uma API downstream chamada *ToDoList*.

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()` é semelhante ao cenário em [uma API Web que chama APIs da Web: configuração de aplicativo](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancia `IConfidentialClientApplication` com um cache que contém informações para apenas uma conta. A conta é fornecida pelo método de `GetAccountIdentifier`.

O método `GetAccountIdentifier` usa as declarações associadas à identidade do usuário para quem a API Web recebeu o token Web JSON (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: chamar uma API](scenario-web-api-call-api-call-api.md)

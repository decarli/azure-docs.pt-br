---
title: Configurar aplicativo de página única-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f72b2b85fcaae4e0a21e4bbe2f3af79359ca2fa6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701902"
---
# <a name="single-page-application-code-configuration"></a>Aplicativo de página única: configuração de código

Saiba como configurar o código para seu aplicativo de página única (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Bibliotecas MSAL que dão suporte ao fluxo implícito

A plataforma de identidade da Microsoft fornece as seguintes bibliotecas de MSAL (biblioteca de autenticação da Microsoft) para dar suporte ao fluxo implícito usando práticas de segurança recomendadas pelo setor:  

| Biblioteca MSAL | Description |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca JavaScript simples para uso em qualquer aplicativo Web do lado do cliente criado por meio de estruturas JavaScript ou SPA, como angular, Vue. js e reaja. js. |
| ![angular MSAL](media/sample-v2-code/logo_angular.png) <br/> [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper da biblioteca MSAL. js principal para simplificar o uso em aplicativos de página única que são criados por meio da estrutura angular. Esta biblioteca está em visualização e tem [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) com determinadas versões e navegadores angulares. |

## <a name="application-code-configuration"></a>Configuração de código do aplicativo

Em uma biblioteca MSAL, as informações de registro do aplicativo são passadas como configuração durante a inicialização da biblioteca.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Para obter mais informações sobre as opções configuráveis, consulte [inicializando aplicativo com MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Entrada e saída](scenario-spa-sign-in.md)

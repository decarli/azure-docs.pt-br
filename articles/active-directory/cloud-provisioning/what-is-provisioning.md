---
title: O que é provisionamento de identidade com o Azure AD? | Microsoft Docs
description: Descreve a visão geral do provisionamento de identidade.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 166fb9320672e63b8c53717133dc61aa93f57a62
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868636"
---
# <a name="what-is-identity-provisioning"></a>O que é provisionamento de identidade?

Hoje, as empresas e corporações estão se tornando cada vez mais uma mistura de aplicativos locais e na nuvem.  Os usuários precisam ter acesso local e na nuvem a aos aplicativos. É necessário ter uma única identidade entre esses vários aplicativos (no local, bem como na nuvem).

O provisionamento é o processo de criação de um objeto com base em determinadas condições, mantendo o objeto atualizado e excluindo o objeto quando as condições não são mais atendidas. Por exemplo, quando um novo usuário ingressa em sua organização, esse usuário é inserido no sistema de RH.  Nesse ponto, o provisionamento pode criar uma conta de usuário correspondente na nuvem, no Active Directory e em diferentes aplicativos que o usuário precisa acessar.  Isso permite que o usuário inicie o trabalho e tenha acesso aos aplicativos e sistemas de que precisa, já no primeiro dia. 

![provisionamento em nuvem](media/what-is-provisioning/cloud1.png)

Com relação ao Azure Active Directory, o provisionamento pode ser dividido nos cenários principais a seguir.  

- **[Provisionamento impulsionado por RH](#hr-driven-provisioning)**  
- **[Provisionamento de aplicativos](#app-provisioning)**  
- **[Provisionamento de diretório](#directory-provisioning)** 

## <a name="hr-driven-provisioning"></a>Provisionamento impulsionado por RH

![provisionamento em nuvem](media/what-is-provisioning/cloud2.png)

O provisionamento de RH para a nuvem envolve a criação de objetos (usuários, funções, grupos, etc.) com base nas informações que estão em seu sistema de RH.  

O cenário mais comum seria quando um novo funcionário ingressasse na sua empresa e fosse inserido no sistema de RH.  Quando isso ocorre, ele é provisionado na nuvem.  Nesse caso, o Azure AD.  O provisionamento do RH pode abranger os cenários a seguir. 

- **Contratação de novos funcionários** – quando um novo funcionário é adicionado ao Cloud HR, uma conta de usuário é criada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e outros aplicativos SaaS compatíveis com o Azure AD, com write-back do endereço de email para o Cloud HR.
- **Atualizações de perfil e atributo de funcionário** – quando um registro de funcionário é atualizado no Cloud HR (como seu nome, cargo ou gerente), sua conta de usuário será atualizada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e outros aplicativos SaaS compatíveis com o Azure AD.
- **Rescisão de funcionário** – quando um funcionário é rescindido no Cloud HR, sua conta de usuário será desabilitada automaticamente no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e outros aplicativos SaaS compatíveis com o Azure AD.
- **Recontratação de funcionário** – quando um funcionário é recontratado no Cloud HR, a conta antiga do funcionário poderá ser reativada ou reprovisionada automaticamente (dependendo da sua preferência) no Active Directory, no Azure Active Directory e, opcionalmente, no Office 365 e outros aplicativos SaaS compatíveis com o Azure AD.


## <a name="app-provisioning"></a>Provisionamento de aplicativos

![provisionamento em nuvem](media/what-is-provisioning/cloud3.png)

O provisionamento de aplicativos envolve o provisionamento de usuários e funções nos aplicativos aos quais o usuário precisa ter acesso.  

O cenário mais comum é quando um usuário do Azure AD é provisionado no O365 ou Salesforce.

## <a name="directory-provisioning"></a>Provisionamento de diretório

![provisionamento em nuvem](media/what-is-provisioning/cloud4.png)

O provisionamento local envolve o provisionamento de fontes locais (como Active Directory) para o Azure AD.  

O cenário mais comum seria quando um usuário do AD (Active Directory) fosse provisionado no Azure AD.

Isso foi realizado pela Sincronização do Azure AD Connect, pelo provisionamento do Azure AD Connect e pelo Microsoft Identity Manager. 
 
## <a name="next-steps"></a>Próximas etapas 

- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Instalar o provisionamento de nuvem](how-to-install.md)
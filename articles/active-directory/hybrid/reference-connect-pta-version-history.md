---
title: 'Autenticação de passagem do Azure AD: histórico de lançamento de versão | Microsoft Docs'
description: Este artigo lista todas as versões do agente de autenticação de passagem do Azure AD
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 11/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c5d0efe8e662544dc69356c6b17dd7eca6f3a50
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786445"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Agente de autenticação de passagem do Azure AD: histórico de lançamento de versão 
 
Os agentes instalados localmente que habilitam a autenticação de passagem são atualizados regularmente para fornecer novos recursos. Este artigo lista as versões e os recursos que são adicionados quando uma nova funcionalidade é introduzida. Os agentes de autenticação de passagem são atualizados automaticamente quando uma nova versão é liberada. 

Aqui estão os tópicos relacionados: 

- [Entrada do usuário com a autenticação de passagem do Azure AD](how-to-connect-pta.md) 
- [Instalação do agente de autenticação de passagem do Azure AD](how-to-connect-pta-quick-start.md) 



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Status de liberação 
1/22/2019: liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Suporte adicionado para canais confiáveis do barramento de serviço para adicionar outra camada de resiliência de conexão para conexões de saída 
- Impor o TLS 1,2 durante o registro do agente 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Status de liberação 
4/10/2018: liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Suporte à conexão de soquete da Web 
- Definir TLS 1,2 como o protocolo padrão para o agente 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status de liberação 
1/31/2018: liberado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 

- Correção de um bug que causou vazamentos de memória no agente. 
- Atualização da versão do barramento de serviço do Azure, que inclui uma correção de bug para problemas de tempo limite do conector. 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status de liberação 
11/26/2017: liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Suporte adicionado para conexões baseadas no WebSocket entre o agente e os serviços do Azure AD para melhorar a resiliência de conexão 

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Status de liberação 
11/25/2017: liberado para download  
### <a name="fixed-issues"></a>Problemas corrigidos 
- Correção de bugs relacionados ao cache DNS para cenários de proxy padrão 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Status de liberação 
10/17/2017: liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Funcionalidade de cache DNS adicionada para conexões de saída para adicionar resiliência de falhas de DNS 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Status de liberação 
08/31/2017: liberado para download  
### <a name="new-features-and-improvements"></a>Novos recursos e aprimoramentos 
- Versão GA do agente de autenticação de passagem do Azure AD 

## <a name="next-steps"></a>Próximos passos

- [Entrada do usuário com autenticação de passagem do Azure Active Directory](how-to-connect-pta.md)
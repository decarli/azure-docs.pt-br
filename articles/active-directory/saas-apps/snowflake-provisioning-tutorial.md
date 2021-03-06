---
title: 'Tutorial: Configurar o floco de neve para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para floco de neve.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f9ce85f4-0992-4bc6-8276-4b2efbce8dcb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 04d9ec8cad2404466d2df649df4d5c461768b76f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693530"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Tutorial: Configurar floco de neve para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em flocos de neve e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos em flocos de neve.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Atualmente, esse conector está em versão prévia pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD.
* [Um locatário de floco de neve](https://www.Snowflake.com/pricing/).
* Uma conta de usuário no floco de neve com permissões de administrador.

## <a name="assigning-users-to-snowflake"></a>Atribuindo usuários ao floco de neve

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao floco de neve. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao floco de neve seguindo estas instruções:
* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-snowflake"></a>Dicas importantes para atribuir usuários ao floco de neve

* É recomendável que um único usuário do Azure AD seja atribuído a flocos de neve para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao floco de neve, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-snowflake-for-provisioning"></a>Configurar floco de neve para provisionamento

Antes de configurar o floco de neve para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no floco de neve.

> [!NOTE]
> Essa integração está em visualização privada no floco de neve hoje. Se você quiser habilitar esse recurso em sua conta do floco de neve, entre em contato com seu representante de vendas do floco de neve.

1. Entre no console de administração do floco de neve. Insira a consulta mostrada abaixo no espaço de trabalho realçada e clique em **executar**.

    ![Console de administração do floco de neve](media/Snowflake-provisioning-tutorial/image00.png)

2.  Um token de acesso SCIM será gerado para seu locatário de floco de neve. Para recuperá-lo, clique no link realçado abaixo.

    ![Floco de neve adicionar SCIM](media/Snowflake-provisioning-tutorial/image01.png)

3. Copie o valor de token gerado e clique em **concluído**. Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo floco de neve na portal do Azure.

    ![Floco de neve adicionar SCIM](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="add-snowflake-from-the-gallery"></a>Adicionar o floco de neve da Galeria

Para configurar o floco de neve para o provisionamento automático de usuário com o Azure AD, você precisará addfloco de neve da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o floco de neve da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **floco**de neve, selecione **floco de neve** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Floco de neve na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-snowflake"></a>Configurando o provisionamento automático de usuário para floco de neve 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos em flocos de neve com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o floco de neve, seguindo as instruções fornecidas no [tutorial de logon único do floco de neve](Snowflake-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para floco de neve no Azure AD:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **floco de neve**.

    ![O link de floco de neve na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção credenciais de administrador, insira `https://<Snowflake Account URL>/scim/v2` a URL de locatário. Um exemplo da URL do locatário:`https://acme.snowflakecomputing.com/scim/v2`

6. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao floco de neve. Se a conexão falhar, verifique se sua conta de floco de neve tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção - **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de Notificação](common/provisioning-notification-email.png)

8. Clique em **Salvar**.

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para floco de neve**.

    ![Mapeamentos de usuário do floco de neve](media/Snowflake-provisioning-tutorial/user-mapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o floco de neve na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no floco de neve para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do floco de neve](media/Snowflake-provisioning-tutorial/user-attribute.png)

11. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para floco de neve**.

    ![Mapeamentos de grupo de flocos de neve](media/Snowflake-provisioning-tutorial/group-mapping.png)

12. Examine os atributos de grupo que são sincronizados do Azure AD para o floco de neve na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em floco de neve para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do grupo floco de neve](media/Snowflake-provisioning-tutorial/group-attribute.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para floco de neve, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar no floco de neve escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

    Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no floco de neve.

    Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* Os tokens SCIM gerados por floco de neve expiram em 6 meses. Lembre-se de que eles precisam ser atualizados antes de expirarem para permitir que as sincronizações de provisionamento continuem funcionando. 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximas etapas
* [Saiba como fazer revisão de logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md).
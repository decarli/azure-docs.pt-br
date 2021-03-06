---
title: Ingressar uma VM CoreOS para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e ingressar uma máquina virtual CoreOS em um domínio gerenciado Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: ddf6c9238cabedfbdeeb8056864072edc543c342
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712619"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ingressar uma máquina virtual CoreOS em um domínio gerenciado Azure AD Domain Services

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado do AD DS do Azure também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM CoreOS a um domínio gerenciado AD DS do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância do Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é membro do grupo de *administradores do Azure AD DC* no locatário do Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Criar e conectar-se a uma VM do Linux CoreOS

Se você tiver uma VM do CoreOS Linux existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM do CoreOS Linux ou desejar criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [PowerShell do Azure](../virtual-machines/linux/quick-create-powershell.md)

Ao criar a VM, preste atenção às configurações de rede virtual para garantir que a VM possa se comunicar com o domínio gerenciado AD DS do Azure:

* Implante a VM na mesma rede virtual, ou emparelhada, na qual você habilitou Azure AD Domain Services.
* Implante a VM em uma sub-rede diferente da instância do Azure AD Domain Services.

Depois que a VM for implantada, siga as etapas para se conectar à VM usando SSH.

## <a name="configure-the-hosts-file"></a>Configurar o arquivo de hosts

Para certificar-se de que o nome do host da VM esteja configurado corretamente para o domínio gerenciado, edite o arquivo */etc/hosts* e defina o nome do host:

```console
sudo vi /etc/hosts
```

No arquivo *hosts* , atualize o endereço *localhost* . No exemplo a seguir:

* *aadds.contoso.com* é o nome de domínio DNS do seu domínio gerenciado AD DS do Azure.
* *Coreos* é o nome do host da VM Coreos que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 coreos coreos.aadds.contoso.com
```

Quando terminar, salve e saia do arquivo de *hosts* usando o comando `:wq` do editor.

## <a name="configure-the-sssd-service"></a>Configurar o serviço SSSD

Atualize a configuração do */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Especifique seu próprio nome de domínio gerenciado AD DS do Azure para os seguintes parâmetros:

* *domínios* em todas as letras maiúsculas
* *[Domain/AADDS]* em que AADDS está em todas as letras maiúsculas
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* em todas as letras maiúsculas

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDS.CONTOSO.COM

[domain/AADDS.CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aadds.contoso.com
ldap_search_base = dc=aadds.contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aadds.contoso.com
krb5_realm = AADDS.CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Ingresse a VM no domínio gerenciado

Com o arquivo de configuração SSSD atualizado, agora ingresse a máquina virtual no domínio gerenciado.

1. Primeiro, use o comando `adcli info` para verificar se você pode ver informações sobre o domínio gerenciado do Azure AD DS. O exemplo a seguir obtém informações para o domínio *AADDS. CONTOSO.COM*. Especifique seu próprio nome de domínio gerenciado AD DS do Azure em letras MAIÚSCULAs:

    ```console
    sudo adcli info AADDS.CONTOSO.COM
    ```

   Se o comando `adcli info` não conseguir localizar seu domínio gerenciado do Azure AD DS, examine as seguintes etapas de solução de problemas:

    * Verifique se o domínio está acessível da VM. Tente `ping aadds.contoso.com` para ver se uma resposta positiva é retornada.
    * Verifique se a VM está implantada no mesmo ou em uma rede virtual emparelhada na qual o domínio gerenciado do Azure AD DS está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado AD DS do Azure.

1. Agora, ingresse a VM no domínio gerenciado AD DS do Azure usando o comando `adcli join`. Especifique um usuário que pertença ao grupo de *Administradores de DC do AAD* . Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Novamente, o nome de domínio gerenciado do AD DS do Azure deve ser inserido em letras MAIÚSCULAs. No exemplo a seguir, a conta chamada `contosoadmin@aadds.contoso.com` é usada para inicializar o Kerberos. Insira sua própria conta de usuário que seja membro do grupo de *Administradores de DC do AAD* .

    ```console
    sudo adcli join -D AADDS.CONTOSO.COM -U contosoadmin@AADDS.CONTOSO.COM -K /etc/krb5.keytab -H coreos.aadds.contoso.com -N coreos
    ```

    O comando `adcli join` não retorna nenhuma informação quando a VM ingressou com êxito no domínio gerenciado do Azure AD DS.

1. Para aplicar a configuração de ingresso no domínio, inicie o serviço SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Entrar na VM usando uma conta de domínio

Para verificar se a VM foi unida com êxito ao domínio gerenciado AD DS do Azure, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório base foi criado e se a associação de grupo do domínio foi aplicada.

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o comando `ssh -l`, como `contosoadmin@aadds.contoso.com` e, em seguida, insira o endereço da VM, como *Coreos.aadds.contoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com coreos.aadds.contoso.com
    ```

1. Agora, verifique se as associações de grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver suas associações de grupo no domínio gerenciado AD DS do Azure.

## <a name="next-steps"></a>Próximos passos

Se você tiver problemas para conectar a VM ao domínio gerenciado AD DS do Azure ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

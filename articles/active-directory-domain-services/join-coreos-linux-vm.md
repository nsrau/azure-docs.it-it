---
title: Aggiungere una macchina virtuale CoreOS a Azure AD Domain Services | Microsoft Docs
description: Informazioni su come configurare e aggiungere una macchina virtuale CoreOS a un dominio gestito Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/14/2019
ms.author: iainfou
ms.openlocfilehash: 4cdc2fff05270a296d9c4c9151f73cadeb2a1cfc
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754382"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Aggiungere una macchina virtuale CoreOS a un dominio gestito Azure AD Domain Services

Per consentire agli utenti di accedere a macchine virtuali (VM) in Azure usando un unico set di credenziali, è possibile aggiungere macchine virtuali a un dominio gestito di Azure Active Directory Domain Services (AD DS). Quando si aggiunge una macchina virtuale a un dominio gestito di Azure AD DS, è possibile usare gli account utente e le credenziali del dominio per l'accesso e la gestione dei server. Vengono inoltre applicate le appartenenze ai gruppi dal dominio gestito Azure AD DS per consentire il controllo dell'accesso ai file o ai servizi nella macchina virtuale.

Questo articolo illustra come aggiungere una macchina virtuale CoreOS a un dominio gestito di Azure AD DS.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Creare e connettersi a una macchina virtuale Linux CoreOS

Se si dispone di una macchina virtuale CoreOS Linux esistente in Azure, connettersi con SSH, quindi continuare con il passaggio successivo per [iniziare a configurare la macchina virtuale](#configure-the-hosts-file).

Se è necessario creare una VM Linux CoreOS o si vuole creare una macchina virtuale di test da usare con questo articolo, è possibile usare uno dei metodi seguenti:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando si crea la VM, prestare attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale sia in grado di comunicare con il dominio gestito di Azure AD DS:

* Distribuire la macchina virtuale nella stessa rete virtuale con peering, in cui è stata abilitata Azure AD Domain Services.
* Distribuire la macchina virtuale in una subnet diversa da quella dell'istanza di Azure AD Domain Services.

Dopo aver distribuito la macchina virtuale, seguire la procedura per connettersi alla macchina virtuale tramite SSH.

## <a name="configure-the-hosts-file"></a>Configurare il file hosts

Per assicurarsi che il nome host della macchina virtuale sia configurato correttamente per il dominio gestito, modificare *il file hosts e impostare il nome* host:

```console
sudo vi /etc/hosts
```

Nel file *host* aggiornare l'indirizzo *localhost* . Nell'esempio seguente:

* *contoso.com* è il nome di dominio DNS del dominio gestito di Azure AD DS.
* *CoreOS* è il nome host della macchina virtuale CoreOS che si sta aggiungendo al dominio gestito.

Aggiornare questi nomi con valori personalizzati:

```console
127.0.0.1 coreos coreos.contoso.com
```

Al termine, salvare e chiudere il file *host* usando il comando `:wq` dell'editor.

## <a name="configure-the-sssd-service"></a>Configurare il servizio SSSD

Aggiornare la configurazione di */etc/SSSD/SSSD.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Specificare il nome di dominio gestito di Azure AD DS per i parametri seguenti:

* *domini* in lettere maiuscole
* *[dominio/contoso]* dove contoso è in lettere maiuscole
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* in lettere maiuscole

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Aggiungere la macchina virtuale al dominio gestito

Con il file di configurazione SSSD aggiornato, aggiungere la macchina virtuale al dominio gestito.

1. Usare prima di tutto il comando `adcli info` per verificare che sia possibile visualizzare informazioni sul dominio gestito Azure AD DS. Nell'esempio seguente vengono ottenute informazioni per il dominio *contoso.com*. Specificare il proprio nome di dominio gestito di Azure AD DS in tutte le lettere maiuscole:

    ```console
    sudo adcli info CONTOSO.COM
    ```

   Se il comando `adcli info` non riesce a trovare il dominio gestito Azure AD DS, esaminare i passaggi per la risoluzione dei problemi seguenti:

    * Verificare che il dominio sia raggiungibile dalla macchina virtuale. Provare `ping contoso.com` per verificare se viene restituita una risposta positiva.
    * Verificare che la macchina virtuale sia distribuita nello stesso o in una rete virtuale con peering in cui è disponibile il dominio gestito di Azure AD DS.
    * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate in modo che puntino ai controller di dominio del dominio gestito Azure AD DS.

1. A questo punto, aggiungere la macchina virtuale al dominio gestito Azure AD DS usando il comando `adcli join`. Specificare un utente appartenente al gruppo di *amministratori di AAD DC* . Se necessario, [aggiungere un account utente a un gruppo in Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Anche in questo caso, è necessario immettere il nome di dominio gestito Azure AD DS in tutti i caratteri maiuscoli. Nell'esempio seguente viene usato l'account denominato `contosoadmin@contoso.com` per inizializzare Kerberos. Immettere il proprio account utente che è un membro del gruppo *AAD DC Administrators* .

    ```console
    sudo adcli join -D CONTOSO.COM -U contosoadmin@CONTOSO.COM -K /etc/krb5.keytab -H coreos.contoso.com -N coreos
    ```

    Il comando `adcli join` non restituisce alcuna informazione quando la macchina virtuale è stata aggiunta correttamente al dominio gestito di Azure AD DS.

1. Per applicare la configurazione di aggiunta al dominio, avviare il servizio SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Accedere alla macchina virtuale usando un account di dominio

Per verificare che la macchina virtuale sia stata aggiunta correttamente al dominio gestito di Azure AD DS, avviare una nuova connessione SSH usando un account utente di dominio. Verificare che sia stata creata una home directory e che sia stata applicata l'appartenenza al gruppo dal dominio.

1. Creare una nuova connessione SSH dalla console di. Usare un account di dominio appartenente al dominio gestito usando il comando `ssh -l`, ad esempio `contosoadmin@contoso.com` e quindi immettere l'indirizzo della macchina virtuale, ad esempio *CoreOS.contoso.com*. Se si usa la Azure Cloud Shell, usare l'indirizzo IP pubblico della macchina virtuale anziché il nome DNS interno.

    ```console
    ssh -l contosoadmin@CONTOSO.com coreos.contoso.com
    ```

1. Verificare ora che le appartenenze ai gruppi siano state risolte correttamente:

    ```console
    id
    ```

    Le appartenenze ai gruppi verranno visualizzate dal dominio gestito di Azure AD DS.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi durante la connessione della macchina virtuale al dominio gestito di Azure AD DS o l'accesso con un account di dominio, vedere [risoluzione dei](join-windows-vm.md#troubleshoot-domain-join-issues)problemi di aggiunta a un dominio.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

---
title: Aggiunta di una macchina virtuale CoreOS a Servizi di dominio Azure AD . Documenti Microsoft
description: Informazioni su come configurare e aggiungere una macchina virtuale CoreOS a un dominio gestito di Servizi di dominio Azure AD.
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
ms.openlocfilehash: b97b542d11e405bab00519c68d2365dada6b6c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298871"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Aggiungere una macchina virtuale CoreOS a un dominio gestito di Servizi di dominio Azure ADJoin a CoreOS virtual machine to an Azure AD Domain Services managed domain

Per consentire agli utenti di accedere alle macchine virtuali (VM) in Azure usando un singolo set di credenziali, è possibile aggiungere macchine virtuali a un dominio gestito di Servizi di dominio Azure Active Directory.To let users sign in to virtual machines (VMs) in Azure using a single set of credentials, you can join VMs to an Azure Active Directory Domain Services (AD DS) managed domain. Quando si aggiunge una macchina virtuale a un dominio gestito di Azure AD DS, è possibile usare gli account utente e le credenziali del dominio per accedere e gestire i server. Vengono applicate anche le appartenenze ai gruppi del dominio gestito di Servizi di dominio Active Directory di Azure per consentire di controllare l'accesso a file o servizi nella macchina virtuale.

Questo articolo illustra come aggiungere una macchina virtuale CoreOS a un dominio gestito di Azure AD DS.This article shows you how to join a CoreOS VM to an Azure AD DS managed domain.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, seguire la prima esercitazione per [creare e configurare un'istanza di Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Un account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.A user account that's a part of the Azure AD DS managed domain.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Creare e connettersi a una macchina virtuale CoreOS LinuxCreate and connect to a CoreOS Linux VM

Se si dispone di una macchina virtuale CoreOS Linux esistente in Azure, connettersi a essa tramite SSH, quindi continuare con il passaggio successivo per [avviare la configurazione della macchina virtuale.](#configure-the-hosts-file)

Se è necessario creare una macchina virtuale CoreOS Linux o si vuole creare una macchina virtuale di test da usare con questo articolo, è possibile usare uno dei metodi seguenti:If you need to create a CoreOS Linux VM, or want to create a test VM for use with this article, you can use one of the following methods:

* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di AzureAzure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando si crea la macchina virtuale, prestare attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale possa comunicare con il dominio gestito di Servizi di dominio Active Directory di Azure:When you create the VM, previene attenzione alle impostazioni della rete virtuale per assicurarsi che la macchina virtuale possa comunicare con il dominio gestito di Azure AD DS:When you create the VM, pre-attention to the virtual network settings to make sure that the VM can communicate

* Distribuire la macchina virtuale nella stessa rete virtuale con peered in cui è stato abilitato Servizi di dominio Azure AD.
* Distribuire la macchina virtuale in una subnet diversa rispetto all'istanza di Servizi di dominio Azure AD.

Dopo aver distribuito la macchina virtuale, seguire i passaggi per connettersi alla macchina virtuale usando SSH.

## <a name="configure-the-hosts-file"></a>Configurare il file hosts

Per assicurarsi che il nome host della macchina virtuale sia configurato correttamente per il dominio gestito, modificare il file */etc/hosts* e impostare il nome host:

```console
sudo vi /etc/hosts
```

Nel file *hosts,* aggiornare l'indirizzo *localhost.* Nell'esempio seguente:

* *aaddscontoso.com* è il nome di dominio DNS del dominio gestito di Servizi di dominio Active Directory di Azure.For is the DNS domain name of your Azure AD DS managed domain.
* *coreos* è il nome host della macchina virtuale CoreOS che si sta aggiungendo al dominio gestito.

Aggiornare questi nomi con i propri valori:Update these names with your own values:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Al termine, salvare *hosts* e uscire `:wq` dal file hosts utilizzando il comando dell'editor.

## <a name="configure-the-sssd-service"></a>Configurare il servizio SSSD

Aggiornare la configurazione SSSD */etc/sssd/sssd.conf.*

```console
sudo vi /etc/sssd/sssd.conf
```

Specificare il proprio nome di dominio gestito di Azure AD DS per i parametri seguenti:Specify your own Azure AD DS managed domain name for the following parameters:

* *domini* in ALL UPPER CASE
* *[dominio/AADDS]* dove AADDS è IN TUTTI MAIUSCOLE
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* in ALL UPPER CASE

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Aggiungere la macchina virtuale al dominio gestitoJoin the VM to the managed domain

Con il file di configurazione SSSD aggiornato, aggiungere ora la macchina virtuale al dominio gestito.

1. In primo `adcli info` luogo, usare il comando per verificare che sia possibile visualizzare informazioni sul dominio gestito di Servizi di dominio Active Directory di Azure.First, use the command to verify you can see information about the Azure AD DS managed domain. Nell'esempio riportato di seguito vengono ottenute informazioni per il dominio *AADDSCONTOSO.COM*. Specificare il proprio nome di dominio gestito di Azure AD DS in TUTTI MAIUSCOLE:Specify your own Azure AD DS managed domain name in ALL UPPERCASE:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Se `adcli info` il comando non riesce a trovare il dominio gestito di Azure AD DS, esaminare la procedura di risoluzione dei problemi seguente:If the command can't find your Azure AD DS managed domain, review the following troubleshooting steps:

    * Assicurarsi che il dominio sia raggiungibile dalla macchina virtuale. Prova `ping aaddscontoso.com` a vedere se viene restituita una risposta positiva.
    * Verificare che la macchina virtuale sia distribuita nella stessa rete virtuale di peered in cui è disponibile il dominio gestito di Servizi di dominio Active Directory di Azure.Check that the VM is deployed to the same, or a peered, virtual network in which the Azure AD DS managed domain is available.
    * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate in modo che puntino ai controller di dominio del dominio gestito di Servizi di dominio Active Directory di Azure.

1. Aggiungere ora la macchina virtuale al dominio `adcli join` gestito di Servizi di dominio Active Directory di Azure usando il comando. Specificare un utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.Specify a user that's a part of the Azure AD DS managed domain. Se necessario, [aggiungere un account utente a un gruppo in Azure AD.](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)

    Anche in questo caso, il nome di dominio gestito di Azure AD DS deve essere immesso in TUTTI maiuscole. Nell'esempio seguente, l'account denominato `contosoadmin@aaddscontoso.com` viene utilizzato per inizializzare Kerberos. Immettere il proprio account utente che fa parte del dominio gestito di Servizi di dominio Active Directory di Azure.Enter your own user account that's a part of the Azure AD DS managed domain.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Il `adcli join` comando non restituisce alcuna informazione quando la macchina virtuale è stata aggiunta correttamente al dominio gestito di Azure AD DS.The command doesn't return any information when the VM has successfully joined to the Azure AD DS managed domain.

1. Per applicare la configurazione di aggiunta a un dominio, avviare il servizio SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Accedere alla macchina virtuale usando un account di dominioSign in to the VM using a domain account

Per verificare che la macchina virtuale sia stata aggiunta correttamente al dominio gestito di Servizi di dominio Active Directory di Azure, avviare una nuova connessione SSH usando un account utente di dominio. Verificare che sia stata creata una home directory e che venga applicata l'appartenenza al gruppo dal dominio.

1. Creare una nuova connessione SSH dalla console. Usare un account di dominio che `ssh -l` appartiene al `contosoadmin@aaddscontoso.com` dominio gestito utilizzando il comando , ad esempio e quindi immettere l'indirizzo della macchina virtuale, ad esempio *coreos.aaddscontoso.com*. Se si usa Azure Cloud Shell, usare l'indirizzo IP pubblico della macchina virtuale anziché il nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Verificare ora che le appartenenze ai gruppi vengano risolte correttamente:

    ```console
    id
    ```

    Dovrebbero essere visualizzate le appartenenze ai gruppi dal dominio gestito di Servizi di dominio Active Directory di Azure.You should see your group memberships from the Azure AD DS managed domain.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi durante la connessione della macchina virtuale al dominio gestito di Servizi di dominio Active Directory di Azure o l'accesso con un account di dominio, vedere [Risoluzione dei problemi](join-windows-vm.md#troubleshoot-domain-join-issues)relativi all'aggiunta a un dominio.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md

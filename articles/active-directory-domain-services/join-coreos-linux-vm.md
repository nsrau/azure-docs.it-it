---
title: 'Azure Active Directory Domain Services: Aggiungere una VM Linux CoreOS | Microsoft Docs'
description: Aggiungere una macchina virtuale CoreOS Linux ad Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: dc76d9a0d492d8ef0e37c0c34173216ff4c75164
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990574"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale CoreOS Linux a un dominio gestito
Questo articolo illustra come aggiungere una macchina virtuale CoreOS Linux a un dominio gestito di Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:
1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](tutorial-create-instance.md).
4. Assicurarsi di aver configurato gli indirizzi IP del dominio gestito come server DNS per la rete virtuale. Per altre informazioni, vedere la [procedura per aggiornare le impostazioni DNS per la rete virtuale di Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).
5. Completare i passaggi necessari per [sincronizzare le password nel dominio gestito di Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale CoreOS Linux
Eseguire il provisioning di una macchina virtuale CoreOS in Azure, usando uno dei metodi seguenti:
* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Questo articolo usa l'immagine della macchina virtuale **CoreOS Linux (Stable)** in Azure.

> [!IMPORTANT]
> * Distribuire la macchina virtuale nella **stessa rete virtuale in cui è stato abilitato Azure AD Domain Services**.
> * Selezionare una **subnet diversa** da quella in cui è stato abilitato Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connettersi in remoto alla macchina virtuale Linux di cui è stato appena effettuato il provisioning
È stato eseguito il provisioning della macchina virtuale CoreOS in Azure. L'attività successiva consiste nel connettersi in remoto alla macchina virtuale usando l'account amministratore locale creato durante il provisioning della macchina virtuale.

Seguire le istruzioni riportate nell'articolo [come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurare il file con estensione hosts nella macchina virtuale Linux
Nel terminale SSH modificare il file /etc/hosts e aggiornare l'indirizzo IP e il nome host della macchina virtuale.

```console
sudo vi /etc/hosts
```

Nel file con estensione hosts immettere il valore seguente:

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

Qui, "contoso.com" è il nome di dominio DNS del dominio gestito. "contoso-coreos" è il nome host della macchina virtuale CoreOS da aggiungere al dominio gestito.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurare il servizio SSSD nella macchina virtuale Linux
Successivamente, aggiornare il file di configurazione SSSD in ("/etc/sssd/sssd.conf") affinché corrisponda all'esempio seguente:

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

Sostituire "CONTOSO". COM "con il nome di dominio DNS del dominio gestito. Assicurarsi di specificare il nome di dominio con la maiuscola nel file di configurazione.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

```console
sudo adcli join -D CONTOSO.COM -U bob@CONTOSO.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Risoluzione dei problemi:** se *adcli* non riesce a trovare il dominio gestito:
>   * Verificare che il dominio sia raggiungibile dalla macchina virtuale (provare a effettuare il ping).
>   * Verificare che la macchina virtuale sia stata effettivamente distribuita nella stessa rete virtuale in cui è disponibile il dominio gestito.
>   * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate affinché puntino ai controller di dominio del dominio gestito.

Avviare il servizio SSSD. Nel terminale SSH digitare il comando seguente:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Verificare l'aggiunta a un dominio
Verificare se la macchina è stata aggiunta correttamente al dominio gestito. Connettersi alla macchina virtuale CoreOS aggiunta al dominio tramite una connessione SSH diversa. Usare un account utente di dominio e quindi verificare se l'account utente viene risolto correttamente.

1. Nel terminale SSH digitare il comando seguente per connettere la macchina virtuale CoreOS aggiunta al dominio con SSH. Usare un account di dominio appartenente al dominio gestito, in questo caso bob@CONTOSO.COM.
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-coreos.contoso.com
    ```

2. Nel terminale SSH digitare il comando seguente per verificare se la home directory dell'utente è stata inizializzata correttamente.
    
    ```console
    pwd
    ```

3. Nel terminale SSH digitare il comando seguente per verificare se i membri del gruppo sono stati risolti correttamente.
   
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi di aggiunta al dominio
Vedere l'articolo [Risoluzione dei problemi dell'aggiunta a un dominio](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Contenuto correlato
* [Servizi di dominio Azure AD: introduzione](tutorial-create-instance.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

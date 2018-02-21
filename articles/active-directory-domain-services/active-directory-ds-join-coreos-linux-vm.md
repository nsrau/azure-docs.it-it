---
title: 'Azure Active Directory Domain Services: aggiungere una macchina virtuale CoreOS Linux a un dominio gestito | Microsoft Docs'
description: Aggiungere una macchina virtuale CoreOS Linux ad Azure AD Domain Services
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 790ad85df0dbf68674e2b9c6254858100ddfd0fd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale CoreOS Linux a un dominio gestito
Questo articolo illustra come aggiungere una macchina virtuale CoreOS Linux a un dominio gestito di Azure AD Domain Services.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:
1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Assicurarsi di aver configurato gli indirizzi IP del dominio gestito come server DNS per la rete virtuale. Per altre informazioni, vedere la [procedura per aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-dns.md).
5. Completare i passaggi necessari per [sincronizzare le password nel dominio gestito di Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale CoreOS Linux
Eseguire il provisioning di una macchina virtuale CoreOS in Azure, usando uno dei metodi seguenti:
* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Questo articolo usa l'immagine della macchina virtuale **CoreOS Linux (Stable)** in Azure.

> [!IMPORTANT]
> * Distribuire la macchina virtuale nella **stessa rete virtuale in cui è stato abilitato Azure AD Domain Services**.
> * Selezionare una **subnet diversa** da quella in cui è stato abilitato Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connettersi in remoto alla macchina virtuale Linux di cui è stato appena effettuato il provisioning
È stato eseguito il provisioning della macchina virtuale CoreOS in Azure. L'attività successiva consiste nel connettersi in remoto alla macchina virtuale usando l'account amministratore locale creato durante il provisioning della macchina virtuale.

Seguire le istruzioni nell'articolo [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurare il file con estensione hosts nella macchina virtuale Linux
Nel terminale SSH modificare il file /etc/hosts e aggiornare l'indirizzo IP e il nome host della macchina virtuale.

```
sudo vi /etc/hosts
```

Nel file con estensione hosts immettere il valore seguente:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
In questo caso, "contoso100.com" è il nome di dominio DNS del dominio gestito. "contoso-coreos" è il nome host della macchina virtuale CoreOS da aggiungere al dominio gestito.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurare il servizio SSSD nella macchina virtuale Linux
Successivamente, aggiornare il file di configurazione SSSD in ("/etc/sssd/sssd.conf") affinché corrisponda all'esempio seguente:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Sostituire "CONTOSO100.COM" con il nome di dominio DNS del dominio gestito. Assicurarsi di specificare il nome di dominio con la maiuscola nel file di configurazione.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Risoluzione dei problemi:** se *adcli* non riesce a trovare il dominio gestito:
  * Verificare che il dominio sia raggiungibile dalla macchina virtuale (provare a effettuare il ping).
  * Verificare che la macchina virtuale sia stata effettivamente distribuita nella stessa rete virtuale in cui è disponibile il dominio gestito.
  * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate affinché puntino ai controller di dominio del dominio gestito.
>

Avviare il servizio SSSD. Nel terminale SSH digitare il comando seguente:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Verificare l'aggiunta a un dominio
Verificare se la macchina è stata aggiunta correttamente al dominio gestito. Connettersi alla macchina virtuale CoreOS aggiunta al dominio tramite una connessione SSH diversa. Usare un account utente di dominio e quindi verificare se l'account utente viene risolto correttamente.

1. Nel terminale SSH digitare il comando seguente per connettere la macchina virtuale CoreOS aggiunta al dominio con SSH. Usare un account di dominio appartenente al dominio gestito, in questo caso bob@CONTOSO100.COM.
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. Nel terminale SSH digitare il comando seguente per verificare se la home directory dell'utente è stata inizializzata correttamente.
    ```
    pwd
    ```

3. Nel terminale SSH digitare il comando seguente per verificare se i membri del gruppo sono stati risolti correttamente.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi di aggiunta al dominio
Vedere l'articolo [Risoluzione dei problemi dell'aggiunta a un dominio](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshooting-domain-join) .

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---
title: 'Azure Active Directory Domain Services: aggiungere una macchina virtuale CentOS a un dominio gestito | Microsoft Docs'
description: Aggiungere una macchina virtuale CentOS Linux ad Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: f7095e82605d12221ba7beb0c48fd3eda75a2e7b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Aggiungere una macchina virtuale CentOS Linux a un dominio gestito
Questo articolo illustra come aggiungere una macchina virtuale CentOS Linux a un dominio gestito di Azure AD Domain Services.

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:
1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](active-directory-ds-getting-started.md).
4. Assicurarsi di aver configurato gli indirizzi IP del dominio gestito come server DNS per la rete virtuale. Per altre informazioni, vedere la [procedura per aggiornare le impostazioni DNS per la rete virtuale di Azure](active-directory-ds-getting-started-dns.md).
5. Completare i passaggi necessari per [sincronizzare le password nel dominio gestito di Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale CentOS Linux
Eseguire il provisioning di una macchina virtuale CentOS in Azure, usando uno dei metodi seguenti:
* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuire la macchina virtuale nella **stessa rete virtuale in cui è stato abilitato Azure AD Domain Services**.
> * Selezionare una **subnet diversa** da quella in cui è stato abilitato Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Connettersi in remoto alla macchina virtuale Linux di cui è stato appena effettuato il provisioning
È stato eseguito il provisioning della macchina virtuale CentOS Linux in Azure. L'attività successiva consiste nel connettersi in remoto alla macchina virtuale usando l'account amministratore locale creato durante il provisioning della macchina virtuale.

Seguire le istruzioni nell'articolo [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurare il file con estensione hosts nella macchina virtuale Linux
Nel terminale SSH modificare il file /etc/hosts e aggiornare l'indirizzo IP e il nome host della macchina virtuale.

```
sudo vi /etc/hosts
```

Nel file con estensione hosts immettere il valore seguente:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
In questo caso, "contoso100.com" è il nome di dominio DNS del dominio gestito. "contoso-centos" è il nome host della macchina virtuale CentOS da aggiungere al dominio gestito.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installare i pacchetti necessari nella macchina virtuale Linux
Installare quindi i pacchetti necessari per l'aggiunta a un dominio nella macchina virtuale. Nel terminale SSH digitare il comando seguente per installare i pacchetti richiesti:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

1. Individuare il dominio gestito di Servizi di dominio AAD. Nel terminale SSH digitare il comando seguente:

    ```
    sudo realm discover CONTOSO100.COM
    ```

    > [!NOTE]
    > **Risoluzione dei problemi:** se *realm discover* non riesce a trovare il dominio gestito:  
      * Verificare che il dominio sia raggiungibile dalla macchina virtuale (provare a effettuare il ping).  
      * Verificare che la macchina virtuale sia stata effettivamente distribuita nella stessa rete virtuale in cui è disponibile il dominio gestito. 
      * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate affinché puntino ai controller di dominio del dominio gestito.  
      >

2. Inizializzare Kerberos. Nel terminale SSH digitare il comando seguente:

    > [!TIP]
    > * Specificare l'utente che appartiene al gruppo "AAD DC Administrators".
    > * Specificare il nome di dominio in lettere maiuscole; in caso contrario kinit avrà esito negativo.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Aggiungere il computer al dominio. Nel terminale SSH digitare il comando seguente:

    > [!TIP]
    > Usare lo stesso account utente specificato nel passaggio precedente ("kinit").
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Quando il computer viene aggiunto correttamente al dominio gestito, dovrebbe essere visualizzato un messaggio che indica che il computer è stato registrato correttamente nell'area di autenticazione.


## <a name="verify-domain-join"></a>Verificare l'aggiunta a un dominio
Verificare se la macchina è stata aggiunta correttamente al dominio gestito. Connettersi alla macchina virtuale CentOS aggiunta al dominio tramite una connessione SSH diversa. Usare un account utente di dominio e quindi verificare se l'account utente viene risolto correttamente.

1. Nel terminale SSH digitare il comando seguente per connettere la macchina virtuale CentOS aggiunta al dominio con SSH. Usare un account di dominio appartenente al dominio gestito, in questo caso bob@CONTOSO100.COM.
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
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
Vedere l'articolo [Risoluzione dei problemi dell'aggiunta a un dominio](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) .

## <a name="related-content"></a>Contenuti correlati
* [Guida introduttiva di Azure AD Domain Services](active-directory-ds-getting-started.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Installazione di Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)

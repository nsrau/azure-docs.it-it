---
title: 'Azure Active Directory Domain Services: Aggiungere una macchina virtuale Ubuntu a un dominio gestito | Microsoft Docs'
description: Aggiungere una macchina virtuale Ubuntu Linux ad Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 80dbb4f3d0c8b993beab5f6344d6034d6c2b6895
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990587"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Aggiungere una macchina virtuale Ubuntu a un dominio gestito in Azure
Questo articolo illustra come aggiungere una macchina virtuale Ubuntu Linux a un dominio gestito di Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Per eseguire le attività elencate in questo articolo sono necessari gli elementi seguenti:  
1. Una **sottoscrizione di Azure**valida.
2. Una **directory di Azure AD** sincronizzata con una directory locale o con una directory solo cloud.
3. **Servizi di dominio Azure AD** devono essere abilitati per la directory di Azure AD. Se non è stato fatto, eseguire tutte le attività descritte nella [guida introduttiva](tutorial-create-instance.md).
4. Assicurarsi di aver configurato gli indirizzi IP del dominio gestito come server DNS per la rete virtuale. Per altre informazioni, vedere la [procedura per aggiornare le impostazioni DNS per la rete virtuale di Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).
5. Completare i passaggi necessari per [sincronizzare le password nel dominio gestito di Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Eseguire il provisioning di una macchina virtuale Ubuntu Linux
Eseguire il provisioning di una macchina virtuale Ubuntu Linux in Azure, usando uno dei metodi seguenti:
* [Portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Interfaccia della riga di comando di Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Distribuire la macchina virtuale nella **stessa rete virtuale in cui è stato abilitato Azure AD Domain Services**.
> * Selezionare una **subnet diversa** da quella in cui è stato abilitato Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Connettersi in remoto alla macchina virtuale Ubuntu Linux
È stato eseguito il provisioning della macchina virtuale Ubuntu Linux in Azure. L'attività successiva consiste nel connettersi in remoto alla macchina virtuale usando l'account amministratore locale creato durante il provisioning della macchina virtuale.

Seguire le istruzioni riportate nell'articolo [come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurare il file con estensione hosts nella macchina virtuale Linux
Nel terminale SSH modificare il file /etc/hosts e aggiornare l'indirizzo IP e il nome host della macchina virtuale.

```console
sudo vi /etc/hosts
```

Nel file con estensione hosts immettere il valore seguente:

```console
127.0.0.1 contoso-ubuntu.contoso.com contoso-ubuntu
```

Qui, "contoso.com" è il nome di dominio DNS del dominio gestito. "contoso-ubuntu" è il nome host della macchina virtuale Ubuntu da aggiungere al dominio gestito.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installare i pacchetti necessari nella macchina virtuale Linux
Installare quindi i pacchetti necessari per l'aggiunta a un dominio nella macchina virtuale. Eseguire la procedura seguente:

1.  Nel terminale SSH digitare il comando seguente per scaricare gli elenchi dei pacchetti dai repository. Questo comando aggiorna gli elenchi dei pacchetti per ottenere informazioni sulle versioni più recenti dei pacchetti e le relative dipendenze.

    ```console
    sudo apt-get update
    ```

2. Digitare il comando seguente per installare i pacchetti richiesti:

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante l'installazione di Kerberos, si visualizza una schermata rosa. L'installazione del pacchetto "krb5-user" richiede il nome dell'area di autenticazione (TUTTE LETTERE MAIUSCOLE). L'installazione scrive le sezioni [area di autenticazione] e [dominio_area di autenticazione] in /etc/krb5.conf.

    > [!TIP]
    > Se il nome del dominio gestito è contoso.com, immettere CONTOSO.COM come area di autenticazione. Tenere presente che il nome dell'area di autenticazione deve essere specificato in MAIUSCOLO.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configurare le impostazioni NTP (Network Time Protocol) nella macchina virtuale Linux
La data e l'ora della macchina virtuale Ubuntu devono essere sincronizzate con il dominio gestito. Aggiungere il nome host NTP del proprio dominio gestito nel file /etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

Nel file ntp.conf immettere il valore seguente e salvare il file:

```console
server contoso.com
```

Qui, "contoso.com" è il nome di dominio DNS del dominio gestito.

Ora sincronizzare data e ora della macchina virtuale Ubuntu con il server NTP, quindi avviare il servizio NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Aggiungere la macchina virtuale Linux al dominio gestito
Ora che i pacchetti sono installati nella macchina virtuale Linux, l'attività successiva consiste nell'aggiungere la macchina virtuale al dominio gestito.

1. Individuare il dominio gestito di Servizi di dominio AAD. Nel terminale SSH digitare il comando seguente:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   > [!NOTE]
   > **Risoluzione dei problemi:** Se *realm discover* non riesce a trovare il dominio gestito:
   >   * Verificare che il dominio sia raggiungibile dalla macchina virtuale (provare a effettuare il ping).
   >   * Verificare che la macchina virtuale sia stata effettivamente distribuita nella stessa rete virtuale in cui è disponibile il dominio gestito.
   >   * Verificare che le impostazioni del server DNS per la rete virtuale siano state aggiornate affinché puntino ai controller di dominio del dominio gestito.

2. Inizializzare Kerberos. Nel terminale SSH digitare il comando seguente:

    > [!TIP]
    > * Verificare che l'utente specificato appartenga al gruppo AAD DC Administrators. Se necessario, [aggiungere un account utente a un gruppo in Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)
    > * Specificare il nome di dominio in lettere maiuscole; in caso contrario kinit avrà esito negativo.
    >

    ```console
    kinit bob@CONTOSO.COM
    ```

3. Aggiungere il computer al dominio. Nel terminale SSH digitare il comando seguente:

    > [!TIP]
    > Usare lo stesso account utente specificato nel passaggio precedente ("kinit").
    >
    > Se la macchina virtuale non è in grado di accedere al dominio, verificare che il gruppo di sicurezza di rete della macchina virtuale consenta il traffico Kerberos in uscita sulla porta TCP + UDP 464 alla subnet della rete virtuale per il dominio gestito di Azure AD DS.

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM' --install=/
    ```

Quando il computer viene aggiunto correttamente al dominio gestito, dovrebbe essere visualizzato un messaggio che indica che il computer è stato registrato correttamente nell'area di autenticazione.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aggiornare la configurazione SSSD e riavviare il servizio
1. Nel terminale SSH digitare il comando seguente: Aprire il file sssd.conf e apportare le modifiche seguenti:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Commentare la riga **use_fully_qualified_names = True** e salvare il file.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Riavviare il servizio SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurare la creazione automatica della home directory
Per abilitare la creazione automatica della Home Directory dopo aver eseguito l'accesso agli utenti, digitare i comandi seguenti nel terminale PuTTy:

```console
sudo vi /etc/pam.d/common-session
```

Aggiungere la riga seguente nel file sotto la linea "session optional pam_sss.so" e salvarlo:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Verificare l'aggiunta a un dominio
Verificare se la macchina è stata aggiunta correttamente al dominio gestito. Connettersi alla macchina virtuale Ubuntu aggiunta al dominio tramite una connessione SSH diversa. Usare un account utente di dominio e quindi verificare se l'account utente viene risolto correttamente.

1. Nel terminale SSH digitare il comando seguente per connettere la macchina virtuale Ubuntu aggiunta al dominio con SSH. Usare un account di dominio appartenente al dominio gestito, in questo caso bob@CONTOSO.COM.
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-ubuntu.contoso.com
    ```

2. Nel terminale SSH digitare il comando seguente per verificare se la home directory dell'utente è stata inizializzata correttamente.
    
    ```console
    pwd
    ```

3. Nel terminale SSH digitare il comando seguente per verificare se i membri del gruppo sono stati risolti correttamente.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Concedere i privilegi sudo al gruppo "Amministratori di AAD DC"
È possibile concedere ai membri del gruppo "Amministratori di AAD DC" i privilegi amministrativi per la macchina virtuale Ubuntu. Il file di sudo si trova in /etc/sudoers. I membri dei gruppi AD aggiunti in sudoers possono eseguire sudo.

1. Nel terminale SSH assicurarsi di avere eseguito l'accesso con privilegi di superuser. È possibile usare l'account amministratore locale specificato durante la creazione della macchina virtuale. Eseguire il seguente comando:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Aggiungere la voce seguente al file /etc/sudoers e salvarlo:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. È ora possibile accedere come membro del gruppo "AAD DC Administrators" e avere i privilegi amministrativi per la macchina virtuale.


## <a name="troubleshooting-domain-join"></a>Risoluzione dei problemi di aggiunta al dominio
Vedere l'articolo [Risoluzione dei problemi dell'aggiunta a un dominio](join-windows-vm.md#troubleshoot-domain-join-issues) .


## <a name="related-content"></a>Contenuto correlato
* [Servizi di dominio Azure AD: introduzione](tutorial-create-instance.md)
* [Aggiungere una macchina virtuale Windows Server a un dominio gestito di Servizi di dominio Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)
* [Come accedere a una macchina virtuale che esegue Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

---
title: Reimpostare la password e la chiave SSH di VM Linux dall&quot;interfaccia della riga di comando | Microsoft Docs
description: Come usare l&quot;estensione VMAccess dall&quot;interfaccia della riga di comando di Azure per reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a6f4ce009048fd5f81c2f1d2982e4011e4de70ee
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Come reimpostare la password o la chiave SSH di una VM Linux, correggere la configurazione SSH e verificare la coerenza dei dischi che utilizzano l'estensione VMAccess
Se non è possibile connettersi a una macchina virtuale Linux su Azure perché si è dimenticata la password o una chiave SSH (Secure Shell) non è valida o per un problema di configurazione di SSH, usare l'estensione VMAccessForLinux con l'interfaccia della riga di comando di Azure per reimpostare la password o la chiave SSH, correggere la configurazione SSH e verificare la coerenza del disco. 

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Con l'interfaccia della riga di comando di Azure, per accedere ai comandi si usa il comando **azure vm extension set** dell'interfaccia della riga di comando (Bash, terminale, prompt dei comandi). Per informazioni dettagliate sull'uso dell'estensione, eseguire **azure help vm extension set** .

Con l’interfaccia della riga di comando di Azure è possibile eseguire le attività seguenti:

* [Reimpostare la password](#pwresetcli)
* [Reimpostare la chiave SSH](#sshkeyresetcli)
* [Reimpostare la password e la chiave SSH](#resetbothcli)
* [Creare un nuovo account utente sudo](#createnewsudocli)
* [Reimpostare la configurazione SSH](#sshconfigresetcli)
* [Eliminare un utente](#deletecli)
* [Visualizzare lo stato dell'estensione VMAccess](#statuscli)
* [Verificare la coerenza dei dischi aggiunti](#checkdisk)
* [Ripristinare i dischi aggiunti nella VM Linux](#repairdisk)

## <a name="prerequisites"></a>Prerequisiti
Sarà necessario eseguire le operazioni seguenti:

* Sarà necessario [installare l'interfaccia della riga di comando di Azure](../../../cli-install-nodejs.md) e [connettersi alla proprio sottoscrizione](../../../xplat-cli-connect.md) per usare le risorse di Azure associate al proprio account.
* Impostare la modalità corretta per il modello di distribuzione classico digitando quanto segue al prompt dei comandi:
    ``` 
        azure config mode asm
    ```
* Procurarsi una nuova password o un set di chiavi SSH, se si desidera reimpostare l'una o l'altro. Queste non saranno necessarie se si vuole reimpostare la configurazione di SSH.

## <a name="pwresetcli"></a>Reimpostare la password
1. Creare un file denominato PrivateConf.json nel computer locale con queste righe. Sostituire **myUserName** e **myP@ssW0rd** con il proprio nome utente e password e impostare la data di scadenza.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Reimpostare la chiave SSH
1. Creare un file denominato PrivateConf.json con questo contenuto. Sostituire i valori **myUserName** e **mySSHKey** con le proprie informazioni.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Reimpostare sia la password sia la chiave SSH
1. Creare un file denominato PrivateConf.json con questo contenuto. Sostituire i valori **myUserName**, **mySSHKey** e **myP@ssW0rd** con le proprie informazioni.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Creare un nuovo account utente sudo

Se si dimentica il nome utente, è possibile usare VMAccess per crearne uno nuovo con privilegi sudo. In questo caso, il nome utente e la password esistenti non verranno modificati.

Per creare un nuovo utente sudo con accesso tramite password, usare lo script in [Reimpostare la password](#pwresetcli) e specificare il nuovo nome utente.

Per creare un nuovo utente sudo con accesso tramite chiave SSH, usare lo script in [Reimpostare la chiave SSH](#sshkeyresetcli) e specificare il nuovo nome utente.

È inoltre possibile usare [Reimpostare la password e la chiave SSH](#resetbothcli) per creare un nuovo utente con accesso tramite password e chiave SSH.

## <a name="sshconfigresetcli"></a>Reimpostare la configurazione SSH
Se la configurazione SSH è in uno stato indesiderato, si potrebbe perdere anche l'accesso alla macchina virtuale. È possibile usare l'estensione VMAccess per reimpostare la configurazione allo stato predefinito. A tale scopo, è sufficiente impostare la chiave "reset_ssh" su "True". L'estensione riavvia il server SSH, apre la porta SSH nella VM e ripristina la configurazione SSH predefinita. L'account utente (nome, password o chiavi SSH) non verrà modificato.

> [!NOTE]
> Il file di configurazione SSH che viene reimpostato si trova in /etc/ssh/sshd_config.
> 
> 

1. Creare un file denominato PrivateConf.json con questo contenuto.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Eliminare un utente
Se si desidera eliminare un account utente senza accedere alla macchina virtuale direttamente, è possibile usare questo script.

1. Creare un file denominato PrivateConf.json con questo contenuto, sostituendo il nome utente da rimuovere in **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Visualizzare lo stato dell'estensione VMAccess
Per visualizzare lo stato dell'estensione VMAccess, eseguire questo comando.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Verificare la coerenza dei dischi aggiunti
Per eseguire fsck su tutti i dischi nella macchina virtuale Linux, è necessario eseguire le operazioni seguenti:

1. Creare un file denominato PublicConf.json con questo contenuto. Il controllo del disco accetta un valore booleano che indica se controllare o meno i dischi collegati alla macchina virtuale. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Riparare i dischi
Per ripristinare i dischi che presentano problemi di montaggio o errori di configurazione di montaggio, usare l'estensione VMAccess per reimpostare la configurazione di montaggio nella macchina virtuale Linux. Sostituire il nome del disco in **myDisk**.

1. Creare un file denominato PublicConf.json con questo contenuto. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Eseguire questo comando, sostituendo il nome della macchina virtuale in **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Passaggi successivi
* Se per reimpostare la password o la chiave SSH, correggere la configurazione SSH e verificare la coerenza dei dischi si vogliono usare cmdlet Azure PowerShell o modelli di Azure Resource Manager, vedere la [documentazione dell'estensione VMAccess in GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Per reimpostare la password o la chiave SSH di una VM Linux distribuita con il modello di distribuzione classica è anche possibile usare il [portale di Azure](https://portal.azure.com) . Non è attualmente possibile usare il portale per eseguire queste operazioni per una VM Linux distribuita con il modello di distribuzione Resource Manager.
* Per altre informazioni sull'uso di estensioni VM per macchine virtuali di Azure vedere [Informazioni sulle estensioni e sulle funzionalità delle macchine virtuali](../../virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).



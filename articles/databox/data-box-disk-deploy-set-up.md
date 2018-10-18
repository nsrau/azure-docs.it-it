---
title: Configurare Microsoft Azure Data Box Disk | Microsoft Docs
description: Usare questa esercitazione per imparare a configurare Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 24de05a60c3cda47a1968bb1f83f9cb91e4e03de
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091199"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Esercitazione: Disimballare, collegare e sbloccare Azure Data Box Disk

Questa esercitazione descrive come disimballare, collegare e sbloccare Azure Data Box Disk.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Disimballare il Data Box Disk.
> * Connettersi ai dischi e ottenere la passkey
> * Sbloccare i dischi nel client Windows
> * Sbloccare i dischi nel client Linux

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Sia stata completata l'[Esercitazione: Ordinare Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Sono stati ricevuti i dischi e lo stato del processo nel portale viene aggiornato a **Recapitato**.
3. Sia disponibile un computer client in cui è possibile installare lo strumento di sblocco di Data Box Disk. Il computer client deve:
    - Eseguire un [sistema operativo supportato](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Avere altro [software richiesto](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) installato se è un client Windows.  

## <a name="unpack-your-disks"></a>Disimballare i dischi.

 Eseguire la procedura seguente per disimballare i dischi.

1. I Data Box Disk vengono spediti in una piccola scatola di spedizione. Aprire la scatola ed estrarre il contenuto. Controllare che la scatola contenga da 1 a 5 dischi SSD (Solid State Drive) e un cavo di collegamento USB per disco. Ispezionare la scatola per accertare eventuali prove di manomissione o qualsiasi altro danno evidente. 

    ![Imballaggio di spedizione del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Non aprire la scatola di spedizione se risulta manomessa o notevolmente danneggiata. Contattare il supporto tecnico Microsoft per valutare se i dischi funzionano correttamente o è necessaria una spedizione sostitutiva.
3. Verificare che la scatola contenga una custodia trasparente contenente un'etichetta indirizzo (sotto l'etichetta corrente) per la spedizione di ritorno. Se l'etichetta è stata persa o è danneggiata, è sempre possibile scaricarne e stamparne una nuova dal portale di Azure. 

    ![Etichetta indirizzo di Azure Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Conservare la scatola e la protezione in schiuma per la spedizione di ritorno dei dischi.

## <a name="connect-to-disks-and-get-the-passkey"></a>Connettersi ai dischi e ottenere la passkey 

1. Usare il cavo in dotazione per collegare il disco al computer client che esegue un sistema operativo supportato, come indicato nei prerequisiti. 

    ![Collegamento del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Nel portale di Azure passare a **Generale > Dettagli dispositivo**. Fare clic sull'icona di copia per copiare la passkey. Questa passkey verrà usata per sbloccare i dischi.

    ![Passkey di sblocco per Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

A seconda se si è connessi a un client Windows o Linux, la procedura per sbloccare i dischi è diversa.

## <a name="unlock-disks-on-windows-client"></a>Sbloccare i dischi nel client Windows

Eseguire la procedura seguente per collegare e sbloccare i dischi.
     
1. Nel portale di Azure passare a **Generale > Dettagli dispositivo**. 
2. Scaricare il set di strumenti di Data Box Disk corrispondente al client Windows. Questo set include tre strumenti di Data Box Disk: sblocco, convalida e divisione della copia. 

    In questa procedura si userà solo lo strumento di sblocco di Data Box Disk. Gli altri due strumenti saranno usati in seguito.

    > [!div class="nextstepaction"]
    > [Scaricare il set di strumenti di Data Box Disk per Windows](http://aka.ms/databoxdisktoolswin)         

3. Estrarre il set di strumenti sullo stesso computer che verrà usato per la copia dei dati. 
4. Aprire una finestra del prompt dei comandi o eseguire Windows PowerShell come amministratore sullo stesso computer.
5. (Facoltativo) Per verificare se il computer in uso per sbloccare il disco soddisfa i requisiti del sistema operativo, eseguire il comando di verifica del sistema. Di seguito è riportato un output di esempio. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Eseguire `DataBoxDiskUnlock.exe` e specificare la passkey ottenuta in [Connettersi ai dischi e ottenere la passkey](#Connect-to-disks-and-get-the-passkey). Viene visualizzata la lettera di unità assegnata al disco. Di seguito è riportato un output di esempio.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Ripetere la procedura di sblocco per gli eventuali reinserimenti futuri del disco. Usare il comando `help` se occorre assistenza con lo strumento di sblocco di Data Box Disk.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Dopo avere sbloccato il disco è possibile visualizzarne il contenuto.    

    ![Contenuto del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Sbloccare i dischi nel client Linux

1. Nel portale di Azure passare a **Generale > Dettagli dispositivo**. 
2. Scaricare il set di strumenti di Data Box Disk corrispondente al client Linux.  

    > [!div class="nextstepaction"]
    > [Scaricare il set di strumenti di Data Box Disk per Linux](http://aka.ms/databoxdisktoolslinux) 

3. Nel client Linux aprire un terminale. Passare alla cartella in cui è stato scaricato il software. Modificare le autorizzazioni dei file in modo che sia possibile eseguire questi file. Digitare il comando seguente: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Di seguito è riportato un output di esempio. Dopo aver eseguito il comando chmod, è possibile verificare che le autorizzazioni per i file siano cambiate eseguendo il comando `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Eseguire lo script in modo da installare tutti i file binari necessari per il software di sblocco di Data Box Disk. Usare `sudo` per eseguire il comando come utente root. Al termine dell'installazione dei file binari, verrà visualizzata una nota a questo proposito nel terminale.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Lo script controllerà prima di tutto se nel computer client è in esecuzione un sistema operativo supportato. Di seguito è riportato un output di esempio. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Digitare `y` per continuare l'installazione. I pacchetti installati dallo script sono: 
    - **epel-release** - Repository che contiene i tre pacchetti seguenti. 
    - **dislocker e fuse-dislocker** - Questa utilità consente di decrittografare i dischi crittografati con BitLocker. 
    - **ntfs-3g** - Pacchetto di supporto per il montaggio di volumi NTFS. 
 
    Al termine dell'installazione dei pacchetti, il terminale visualizzerà una notifica a tale proposito.     
    ```
    Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
    ruby-libs.x86 64 0:1.8.7.374-5.el6 
    Complete! 
    Loaded plugins: fastestmirror, refresh-packagekit, security 
    Setting up Remove Process 
    Resolving Dependencies 
    --> Running transaction check 
    ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
    Dependencies Resolved 
    Package        Architecture        Version        Repository        Size 
    Removing:  epel-release        noarch         6-8        @extras        22 k 
    Transaction Summary                                 
    Remove        1 Package(s) 
    Installed size: 22 k 
    Downloading Packages: 
    Running rpmcheckdebug 
    Running Transaction Test 
    Transaction Test Succeeded 
    Running Transaction 
    Erasing : epel-release-6-8.noarch 
    Verifying : epel-release-6-8.noarch 
    Removed: 
    epel-release.noarch 0:6-8 
    Complete! 
    Dislocker is installed by the script. 
    OpenSSL is already installed.
    ```

6. Eseguire lo strumento di sblocco di Data Box Disk. Specificare la passkey dal portale di Azure ottenuta in [Connettersi ai dischi e ottenere la passkey](#Connect-to-disks-and-get-the-passkey). Facoltativamente, specificare un elenco di volumi crittografati con BitLocker da sbloccare. La passkey e l'elenco dei volumi devono essere specificati racchiusi tra virgolette singole. 

    Digitare il comando seguente.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’ /Volumes:’<list of volumes>’`         

    Di seguito è riportato l'output di esempio. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’ /Volumes:’/dev/sdbl’ 
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Viene visualizzato il punto di montaggio per il volume in cui è possibile copiare i dati.

7. Ripetere la procedura di sblocco per gli eventuali reinserimenti futuri del disco. Usare il comando `help` se occorre assistenza con lo strumento di sblocco di Data Box Disk. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Di seguito è riportato l'output di esempio. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Dopo aver sbloccato il disco, è possibile passare al punto di montaggio e visualizzare il contenuto del disco. Ora si è pronti per copiare i dati nelle cartelle *BlockBlob* o *PageBlob*. 

    ![Contenuto del Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Disimballare il Data Box Disk.
> * Connettersi ai dischi e ottenere la passkey
> * Sbloccare i dischi nel client Windows
> * Sbloccare i dischi nel client Linux


Passare all'esercitazione successiva per informazioni su come copiare dati sul Data Box Disk.

> [!div class="nextstepaction"]
> [Copiare i dati sul Data Box Disk](./data-box-disk-deploy-copy-data.md)


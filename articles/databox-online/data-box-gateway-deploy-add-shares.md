---
title: Trasferire i dati con Azure Data Box Gateway | Microsoft Docs
description: Informazioni su come aggiungere condivisioni e connettersi a esse in un dispositivo Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 0ceff87cd3075d517ee1c0027e19dbf423e44f5c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108756"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Esercitazione: Trasferire i dati con Azure Data Box Gateway (anteprima)


## <a name="introduction"></a>Introduzione

Questo articolo illustra come aggiungere condivisioni e connettersi a esse nel dispositivo Data Box Gateway. Una volta aggiunte le condivisioni, il dispositivo Data Box Gateway può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 10 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione

> [!IMPORTANT]
> - Data Box Gateway è in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 
 
## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere le condivisioni al Data Box Gateway, verificare che:

* Sia stato effettuato il provisioning di un dispositivo virtuale e ci si sia connessi a esso come illustrato in [Effettuare il provisioning di Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) o in [Effettuare il provisioning di Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md). 

    Il dispositivo virtuale sia stato attivato come descritto in [Connettere, configurare e attivare Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) e possa essere usato per creare le condivisioni e trasferire i dati.


## <a name="add-a-share"></a>Aggiungere una condivisione

Eseguire i passaggi seguenti nel [portale di Azure](https://portal.azure.com/) per creare una condivisione.

1. Tornare al portale di Azure. Passare a **Tutte le risorse** e cercare la propria risorsa Data Box Gateway.
    
2. Nell'elenco filtrato delle risorse selezionare la risorsa Data Box Gateway e quindi passare a **Panoramica**. Fare clic su **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.
   
   ![Aggiungere una condivisione](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione. 

   I nomi di condivisioni possono contenere solo numeri, lettere minuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.
    
5. Selezionare un **Tipo** per la condivisione. Il tipo può essere SMB o NFS. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS. 

6. È necessario specificare un account di archiviazione in cui risiederà la condivisione. Nell'account di archiviazione viene creato un contenitore con il nome della condivisione, se non esiste già. Se esiste già un contenitore, viene usato quello. 
    
7. In **Servizio di archiviazione** selezionare BLOB in blocchi, BLOB di pagine o File. Il tipo di servizio scelto dipende dal formato che si vuole applicare ai dati che risiederanno in Azure. In questo esempio si vuole che i dati vengano trasferiti in Azure come BLOB in blocchi, quindi è questa l'opzione da selezionare. Se si sceglie BLOB di pagine, occorre verificare che i dati siano allineati su 512 byte. VHDX è sempre allineato su 512 byte.
   
8. Questo passaggio varia a seconda che si stia creando una condivisione SMB o NFS. 
     
    - **Se si crea una condivisione SMB**: nel campo "Utente locale con tutti i privilegi" selezionare **Crea nuovo** o **Usa esistente**. Se si crea un nuovo utente locale, compilare i campi **Nome utente**, **Password** e **Conferma password**. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.
    
        ![Aggiunta di una condivisione SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se si seleziona **Consenti solo operazioni di lettura** per i dati di questa condivisione, sarà possibile specificare utenti di sola lettura.
        
    - **Se si crea una condivisione NFS**: occorre specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione.

        ![Aggiunta di una condivisione NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Fare clic su **Crea** per creare la condivisione. 
    
    Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo aver creato la condivisione con le impostazioni specificate, il pannello **Condivisioni** viene aggiornato per includere la nuova condivisione. 
    
    ![Elenco aggiornato delle condivisioni](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Connettersi alla condivisione

Per connettersi alle condivisioni, eseguire questa procedura nel client Windows Server connesso al Data Box Gateway.


1. Aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando richiesto, immettere la password della condivisione. Di seguito è illustrato un esempio di output di questo comando.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Fare clic su **OK**. Si apre Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. Selezionare e fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
 
    ![Connettersi alla condivisione SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    I dati vengono scritti in queste condivisioni man mano che vengono generati e il dispositivo ne esegue il push nel cloud.

### <a name="connect-to-an-nfs-share"></a>Connettersi a una condivisione NFS

Eseguire questa procedura nel client Linux connesso al Data Box Edge.

1. Verificare che nel client sia installato il client NFSv4. Per installare il client NFS, usare il comando seguente:

   `sudo apt-get install nfs-common`

    Per altre informazioni, vedere [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installare il client NFSv4).

2. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS creata nel dispositivo Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Prima di configurare i montaggi, verificare che le directory che fungeranno da punti di montaggio nel computer locale siano già state create e non contengano file o sottocartelle.

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione nel dispositivo Data Box Gateway. L'indirizzo IP del dispositivo virtuale è `10.10.10.60` e la condivisione `mylinuxshare2` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Per la versione di anteprima valgono le avvertenze seguenti:
> - Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo. 
> - Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione


Passare all'esercitazione successiva per informazioni su come amministrare il Data Box Gateway.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un Data Box Gateway](https://aka.ms/dbg-docs)



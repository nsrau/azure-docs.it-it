---
title: Trasferire i dati con Azure Data Box Edge | Microsoft Docs
description: Informazioni su come aggiungere condivisioni e connettersi a esse in un dispositivo Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 61d427c22f2ac57627ac04a91748e4e6cf8c4e55
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165359"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Esercitazione: Trasferire i dati con Azure Data Box Edge (anteprima)

Questa esercitazione illustra come aggiungere e condivisioni e connettersi a esse nel dispositivo Data Box Edge. Una volta aggiunte le condivisioni, il dispositivo Data Box Edge può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 10 minuti. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione

> [!IMPORTANT]
> Data Box Edge è disponibile in anteprima. Rivedere le [condizioni del servizio per l'anteprima di Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) prima di ordinare e distribuire la soluzione. 
 
## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere le condivisioni al Data Box Edge, verificare che:

* Il dispositivo fisico sia stato installato come da istruzioni in [Installare un Data Box Edge](data-box-edge-deploy-install.md). 

    Il dispositivo fisico sia stato attivato come descritto in [Connettere, configurare e attivare Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md). Il dispositivo sia pronto per la creazione di condivisioni e il trasferimento di dati.


## <a name="add-a-share"></a>Aggiungere una condivisione

Eseguire i passaggi seguenti nel [portale di Azure](https://portal.azure.com/) per creare una condivisione.

1. Tornare al portale di Azure. Passare a **Tutte le risorse** e cercare la propria risorsa Data Box Edge.
    
2. Nell'elenco filtrato delle risorse selezionare la risorsa Data Box Edge e quindi passare a **Panoramica**. Fare clic su **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.
   
   ![Aggiungere una condivisione](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione. 

   I nomi di condivisioni possono contenere solo numeri, lettere minuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.
    
    1. Selezionare un **Tipo** per la condivisione. Il tipo può essere SMB o NFS. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. 

    2. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS. 

    3. È necessario specificare un account di archiviazione in cui risiederà la condivisione. Nell'account di archiviazione viene creato un contenitore con il nome della condivisione, se non esiste già. Se esiste già un contenitore, viene usato quello. 
    
    4. In **Servizio di archiviazione** selezionare BLOB in blocchi, BLOB di pagine o File. Il tipo di servizio scelto dipende dal formato che si vuole applicare ai dati che risiederanno in Azure. In questo esempio si vuole che i dati vengano trasferiti in Azure come BLOB in blocchi, quindi è questa l'opzione da selezionare. Se si sceglie BLOB di pagine, verificare che i dati siano allineati su 512 byte. Ad esempio, VHDX è sempre allineato su 512 byte.
   
    5. Questo passaggio varia a seconda che si stia creando una condivisione SMB o NFS. 
     
        - **Se si crea una condivisione SMB**: nel campo "Utente locale con tutti i privilegi" selezionare **Crea nuovo** o **Usa esistente**. Se si crea un nuovo utente locale, compilare i campi **Nome utente**, **Password** e **Conferma password**. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.

            Se si seleziona **Consenti solo operazioni di lettura** per i dati di questa condivisione, sarà possibile specificare utenti di sola lettura.

            ![Aggiunta di una condivisione SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **Se si crea una condivisione NFS**: occorre specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione.

            ![Aggiunta di una condivisione NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Fare clic su **Crea** per creare la condivisione. 
    
    Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo aver creato la condivisione con le impostazioni specificate, il pannello **Condivisioni** viene aggiornato per includere la nuova condivisione. 
    
    ![Elenco aggiornato delle condivisioni](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Connettersi alla condivisione

È ora possibile connettersi a una o più condivisioni create nel passaggio precedente. A seconda che sia disponibile una condivisione SMB o NFS, la procedura può essere diversa. 

### <a name="connect-to-an-smb-share"></a>Connettersi a una condivisione SMB

Per connettersi alle condivisioni, eseguire questa procedura nel client Windows Server connesso al Data Box Edge.


1. Aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando richiesto, immettere la password della condivisione. Di seguito è illustrato un esempio di output di questo comando.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Fare clic su **OK**. Si apre Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. Selezionare e fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
 
    ![Connettersi alla condivisione SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    I dati vengono scritti in queste condivisioni man mano che vengono generati e il dispositivo ne esegue il push nel cloud.

### <a name="connect-to-an-nfs-share"></a>Connettersi a una condivisione NFS

Eseguire questa procedura nel client Linux connesso al Data Box Edge.

1. Verificare che nel client sia installato il client NFSv4. Per installare il client NFS, usare il comando seguente:

   `sudo apt-get install nfs-common`

    Per altre informazioni, vedere [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installare il client NFSv4).

2. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS creata nel dispositivo Data Box Edge:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Prima di montare le condivisioni, verificare che le directory che fungeranno da punti di montaggio nel computer locale siano già state create. Queste directory non devono contenere file o sottocartelle.

    L'esempio seguente mostra come connettersi da NFS a una condivisione nel dispositivo Data Box Edge. L'indirizzo IP del dispositivo è `10.10.10.60`. La condivisione `mylinuxshare2` è montata nella macchina virtuale Ubuntu. Il punto di montaggio della condivisione è `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Per la versione di anteprima valgono le avvertenze seguenti:
> - Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo. 
> - Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Edge, ad esempio:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione


Passare all'esercitazione successiva per informazioni su come trasformare i dati con Data Box Edge.

> [!div class="nextstepaction"]
> [Trasformare dati con Data Box Edge](./data-box-edge-deploy-configure-compute.md)



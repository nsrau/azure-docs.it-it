---
title: Esercitazione sul trasferimento di dati in condivisioni con Azure Data Box Edge | Microsoft Docs
description: Informazioni su come aggiungere condivisioni e connettersi a esse in un dispositivo Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 701ae5e70612b89c28e3092571e26e4f06389af0
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924622"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Esercitazione: Trasferire i dati con Azure Data Box Edge

Questa esercitazione illustra come aggiungere e connettersi per condividere sul dispositivo Data Box Edge. Dopo aver aggiunto le condivisioni, Data Box Edge può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione

 
## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere le condivisioni al Data Box Edge, verificare che:

- Il dispositivo fisico sia stato installato come descritto in [Installare Azure Data Box Edge](data-box-edge-deploy-install.md).

- Il dispositivo fisico sia stato attivato come descritto in [Connettere, configurare e attivare Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Aggiungere una condivisione

Per creare una condivisione, eseguire la procedura seguente:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare la risorsa Data Box Edge e quindi passare a **Panoramica**. Il dispositivo dovrebbe essere online.

   ![Dispositivo online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.

   ![Aggiungere una condivisione](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. Nel riquadro **Aggiungi condivisione** eseguire le operazioni seguenti:

    a. Nella casella **Nome**, specificare un nome univoco per la condivisione.  
    Il nome condivisione può contenere solo lettere minuscole, numeri e trattini. Deve avere lunghezza compresa tra 3 e 63 caratteri e iniziare con una lettera o un numero. I trattini devono essere preceduti e seguiti da una lettera o un numero.
    
    b. Selezionare un **Tipo** per la condivisione.  
    Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux.  
    Le opzioni rimanenti variano leggermente a seconda che si scelgano condivisioni SMB o NFS. 

    c. Specificare l'account di archiviazione in cui risiederà la condivisione. 

    
    d. Nell'elenco a discesa **Servizio di archiviazione**, selezionare **BLOB in blocchi**, **BLOB di pagine** o **File**.  
    Il tipo di servizio selezionato dipende dal formato che si vuole applicare ai dati da usare in Azure. In questo esempio si seleziona **BLOB in blocchi** perché si vogliono archiviare i dati come BLOB in blocchi in Azure. Se si seleziona **BLOB di pagine**, verificare che i dati siano allineati su 512 byte. Ad esempio, VHDX è sempre allineato su 512 byte.

    e. Creare un nuovo contenitore BLOB o usarne uno esistente nell'elenco a discesa. Se si crea un contenitore BLOB, specificarne il nome. Se non esiste già un contenitore, viene creato nell'account di archiviazione con il nome della condivisione appena creato.
   
    f. A seconda che sia stata creata una condivisione SMB o una condivisione NFS, eseguire una delle operazioni seguenti: 
     
    - **Condivisione SMB**: Sotto **Utente locale con tutti i privilegi**, selezionare **Crea nuovo** oppure **Usa esistente**. Se si crea un nuovo utente locale, immettere un nome utente e password e quindi confermare la password. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.

        Se si seleziona la casella di controllo **Consenti solo operazioni di lettura** per i dati di questa condivisione, è possibile specificare utenti di sola lettura.

        ![Aggiunta di una condivisione SMB](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Condivisione NFS**: Immettere l'indirizzo IP dei client autorizzati ad accedere alla condivisione.

        ![Aggiunta di una condivisione NFS](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Selezionare **Crea** per creare la condivisione.
    
    Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il riquadro **Condivisioni** viene aggiornato e includerà la nuova condivisione.
    

## <a name="connect-to-the-share"></a>Connettersi alla condivisione

È ora possibile connettersi a una o più delle condivisioni create nel passaggio precedente. A seconda che sia disponibile una condivisione SMB o NFS, la procedura può variare.

### <a name="connect-to-an-smb-share"></a>Connettersi a una condivisione SMB

Nel client Windows Server connesso al dispositivo Data Box Edge, connettersi a una condivisione SMB immettendo i comandi:


1. In una finestra di comando digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Quando richiesto, immettere la password per la condivisione.  
   Di seguito è illustrato un esempio di output di questo comando.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Sulla tastiera, premere Windows + R.

4. Nella finestra **Esegui**, specificare il `\\<device IP address>`e quindi selezionare **OK**.  
   Viene visualizzato Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. In Esplora file, fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
 
    ![Connettersi alla condivisione SMB](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    I dati vengono scritti in queste condivisioni man mano che vengono generati e il dispositivo ne esegue il push nel cloud.

### <a name="connect-to-an-nfs-share"></a>Connettersi a una condivisione NFS

Nel client Linux connesso al dispositivo Data Box Edge, eseguire la seguente procedura:

1. Verificare che nel client sia installato il client NFSv4. Per installare il client NFS, usare il comando seguente:

   `sudo apt-get install nfs-common`

    Per altre informazioni, vedere [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installare il client NFSv4).

2. Dopo l'installazione del client NFS, montare la condivisione NFS creata nel dispositivo Data Box Edge usando il comando seguente:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > L'uso dell'opzione `sync` durante il montaggio di condivisioni migliora le velocità di trasferimento dei file di grandi dimensioni.
    > Prima di montare la condivisione, verificare che le directory che fungeranno da punti di montaggio nel computer locale siano già state create. Queste directory non devono contenere file o sottocartelle.

    L'esempio seguente mostra come connettersi da NFS a una condivisione nel dispositivo Data Box Edge. L'indirizzo IP del dispositivo è `10.10.10.60`. La condivisione `mylinuxshare2` è montata nella macchina virtuale Ubuntu. Il punto di montaggio della condivisione è `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> A questa versione si applicano le indicazioni seguenti:
> - Dopo aver creato un file nella condivisione, non è possibile rinominarlo. 
> - L'eliminazione di un file da una condivisione non comporta l'eliminazione della voce nell'account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati i seguenti argomenti relativi a Data Box Edge:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione

Per informazioni su come trasformare i dati con Data Box Edge, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Trasformare dati con Data Box Edge](./data-box-edge-deploy-configure-compute.md)



---
title: Trasferire i dati con Azure Data Box Gateway | Microsoft Docs
description: Informazioni su come aggiungere condivisioni e connettersi a esse in un dispositivo Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: d930b1db48e3a5c4bda96f0b7d80a9c9f24d53d9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400650"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Esercitazione: Trasferire i dati con Azure Data Box Gateway


## <a name="introduction"></a>Introduzione

Questo articolo illustra come aggiungere condivisioni in Data Box Gateway e connettersi a esse. Dopo l'aggiunta delle condivisioni, il dispositivo Data Box Gateway può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 10 minuti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione


## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere le condivisioni al Data Box Gateway, verificare che:

- Sia stato effettuato il provisioning di un dispositivo virtuale e sia stata stabilita la connessione a tale dispositivo come illustrato in [Effettuare il provisioning di Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) o in [Effettuare il provisioning di Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md).

- Il dispositivo virtuale sia stato attivato come descritto nell'articolo su come [connettersi e attivare Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md).

- Il dispositivo sia pronto per la creazione di condivisioni e il trasferimento di dati.

## <a name="add-a-share"></a>Aggiungere una condivisione

Per creare una condivisione, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare la risorsa Data Box Gateway e quindi passare a **Panoramica**. Il dispositivo dovrebbe essere online. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.
   
   ![Aggiungere una condivisione](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. In **Aggiungi condivisione** seguire questa procedura:

    1. Specificare un nome univoco per la condivisione. I nomi delle condivisioni possono contenere solo lettere minuscole, numeri e trattini. Il nome della condivisione deve avere una lunghezza compresa tra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.
    
    2. Selezionare un **Tipo** per la condivisione. Il tipo può essere SMB o NFS. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS.

    3. Specificare l'account di archiviazione in cui risiederà la condivisione. Se non esiste già un contenitore, viene creato nell'account di archiviazione con il nome della condivisione appena creato. Se esiste già un contenitore, viene usato quello.
    
    4. In **Servizio di archiviazione** selezionare BLOB in blocchi, BLOB di pagine o File. Il tipo di servizio scelto dipende dal formato che si vuole applicare ai dati che risiederanno in Azure. In questo esempio si vuole che i dati vengano trasferiti in Azure come BLOB in blocchi, quindi è questa l'opzione da selezionare. Se si sceglie BLOB di pagine, occorre verificare che i dati siano allineati su 512 byte. Ad esempio, VHDX è sempre allineato su 512 byte.
   
    5. Questo passaggio varia a seconda che si stia creando una condivisione SMB o NFS.
     
    - **Condivisione SMB**: in **Utente locale con tutti i privilegi** selezionare **Crea nuovo** oppure **Usa esistente**. Se si crea un nuovo utente locale, immettere un **nome utente** e una **password** e quindi **confermare la password**. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questo contesto, è possibile modificarle usando Esplora file.
    
        ![Aggiunta di una condivisione SMB](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Se si seleziona la casella di controllo **Consenti solo operazioni di lettura** per i dati di questa condivisione, è possibile specificare utenti di sola lettura.
        
    - **Condivisione NFS**: immettere gli indirizzi IP dei client autorizzati ad accedere alla condivisione.

        ![Aggiunta di una condivisione NFS](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selezionare **Crea** per creare la condivisione.
    
    Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il riquadro **Condivisioni** viene aggiornato e includerà la nuova condivisione.
    
    ![Riquadro Condivisioni aggiornato](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Connettersi alla condivisione

È ora possibile connettersi a una o più delle condivisioni create nel passaggio precedente. A seconda che sia disponibile una condivisione SMB o NFS, la procedura può variare.

### <a name="connect-to-an-smb-share"></a>Connettersi a una condivisione SMB

Nel client Windows Server connesso a Data Box Gateway, connettersi a una condivisione SMB immettendo i comandi riportati di seguito.


1. In una finestra di comando digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando richiesto, immettere la password della condivisione. Di seguito è illustrato un esempio di output di questo comando.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Premere i tasti Windows + R. 
3. Nella finestra **Esegui** specificare `\\<device IP address>` e quindi selezionare **OK**. Viene visualizzato Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. In Esplora file, fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
 
    ![Connettersi alla condivisione SMB](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    I dati vengono scritti in queste condivisioni man mano che vengono generati e il dispositivo ne esegue il push nel cloud.

### <a name="connect-to-an-nfs-share"></a>Connettersi a una condivisione NFS

Nel client Linux connesso al dispositivo Data Box Edge, eseguire la seguente procedura:

1. Verificare che nel client sia installato il client NFSv4. Per installare il client NFS, usare il comando seguente:

   `sudo apt-get install nfs-common`

    Per altre informazioni, vedere [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installare il client NFSv4).

2. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS creata nel dispositivo Data Box Gateway:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Prima di configurare i montaggi, verificare che le directory che fungeranno da punti di montaggio nel computer locale siano già state create e non contengano file o sottocartelle.

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione nel dispositivo Data Box Gateway. L'indirizzo IP del dispositivo virtuale è `10.10.10.60` e la condivisione `mylinuxshare2` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> A questa versione si applicano le indicazioni seguenti:
> - Dopo aver creato un file nelle condivisioni, non è possibile rinominarlo.
> - Se si elimina un file da una condivisione, la voce corrispondente nell'account di archiviazione non viene eliminata.
> - Se si usa `rsync` per copiare i dati, l'opzione `rsync -a` non è supportata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi a Data Box Gateway, ad esempio:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione


Passare all'esercitazione successiva per informazioni su come amministrare il Data Box Gateway.

> [!div class="nextstepaction"]
> [Usare l'interfaccia utente Web locale per amministrare un Data Box Gateway](https://aka.ms/dbg-docs)



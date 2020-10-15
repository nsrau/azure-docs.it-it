---
title: Esercitazione sul trasferimento di dati in condivisioni con Azure Stack Edge Pro con GPU | Microsoft Docs
description: Informazioni su come aggiungere condivisioni e connettersi a esse in un dispositivo Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: cd288926e6f7a3f454be2788479b5c9d414b167a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440290"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Esercitazione: Trasferire dati tramite condivisioni con Azure Stack Edge Pro con GPU

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questa esercitazione illustra come aggiungere condivisioni e connettersi a esse sul dispositivo Azure Stack Edge Pro. Dopo che le condivisioni sono state aggiunte, Azure Stack Edge Pro può trasferire i dati in Azure.

Per completare questa procedura sono necessari circa 10 minuti.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Aggiungere una condivisione
> * Connettersi alla condivisione

## <a name="prerequisites"></a>Prerequisiti

Prima di aggiungere le condivisioni ad Azure Stack Edge Pro, verificare che:

* Il dispositivo fisico sia stato installato come descritto in [Installare Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).

* Il dispositivo fisico sia stato attivato come descritto in [Attivare il dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

## <a name="add-a-share"></a>Aggiungere una condivisione

Per creare una condivisione, eseguire la procedura seguente:

1. Nel [portale di Azure](https://portal.azure.com/) selezionare la risorsa Azure Stack Edge e quindi passare a **Panoramica**. Il dispositivo dovrebbe essere online.

   ![Dispositivo online](./media/azure-stack-edge-j-series-deploy-add-shares/device-online-1.png)

2. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi del dispositivo.

   ![Aggiungere una condivisione](./media/azure-stack-edge-j-series-deploy-add-shares/select-add-share-1.png)

3. Nel riquadro **Aggiungi condivisione** eseguire le operazioni seguenti:

    a. Nella casella **Nome**, specificare un nome univoco per la condivisione.  
    Il nome della condivisione può contenere solo lettere, numeri e trattini. Deve avere lunghezza compresa tra 3 e 63 caratteri e iniziare con una lettera o un numero. I trattini devono essere preceduti e seguiti da una lettera o un numero.
    
    b. Selezionare un **Tipo** per la condivisione.  
    Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux.  
    Le opzioni rimanenti variano leggermente a seconda che si scelgano condivisioni SMB o NFS. 

    c. Specificare l'account di archiviazione in cui risiederà la condivisione.

    d. Nell'elenco a discesa **Servizio di archiviazione**, selezionare **BLOB in blocchi**, **BLOB di pagine** o **File**.  
    Il tipo di servizio selezionato dipende dal formato che si vuole applicare ai dati da usare in Azure. In questo esempio si seleziona **BLOB in blocchi** perché si vogliono archiviare i dati come BLOB in blocchi in Azure. Se si seleziona **BLOB di pagine**, verificare che i dati siano allineati su 512 byte. Ad esempio, VHDX è sempre allineato su 512 byte.

   > [!IMPORTANT]
   > Verificare che per l'account di archiviazione di Azure in uso non siano impostati criteri di immutabilità se lo si usa con un dispositivo Azure Stack Edge Pro o Data Box Gateway. Per altre informazioni, vedere [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

    e. Creare un nuovo contenitore BLOB o usarne uno esistente nell'elenco a discesa. Se si crea un contenitore BLOB, specificarne il nome. Se non esiste già un contenitore, viene creato nell'account di archiviazione con il nome della condivisione appena creato.
   
    f. A seconda che sia stata creata una condivisione SMB o una condivisione NFS, eseguire una delle operazioni seguenti: 
     
    - **Condivisione SMB**: Sotto **Utente locale con tutti i privilegi**, selezionare **Crea nuovo** oppure **Usa esistente**. Se si crea un nuovo utente locale, immettere un nome utente e password e quindi confermare la password. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.
    Se si seleziona la casella di controllo **Consenti solo operazioni di lettura** per i dati di questa condivisione, è possibile specificare utenti di sola lettura.
    
        ![Aggiunta di una condivisione SMB](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-smb-1.png)
   
    - **Condivisione NFS**: Immettere l'indirizzo IP dei client autorizzati ad accedere alla condivisione.

        ![Aggiunta di una condivisione NFS](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-nfs-1.png)
   
4. Selezionare **Crea** per creare la condivisione.
    
    Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il riquadro **Condivisioni** viene aggiornato e includerà la nuova condivisione.
    

## <a name="connect-to-the-share"></a>Connettersi alla condivisione

È ora possibile connettersi a una o più delle condivisioni create nel passaggio precedente. A seconda che sia disponibile una condivisione SMB o NFS, la procedura può variare.

Il primo passaggio consiste nell'assicurarsi che il nome del dispositivo possa essere risolto quando si usa una condivisione SMB o NFS.

### <a name="modify-host-file-for-name-resolution"></a>Modificare il file host per la risoluzione dei nomi

A questo punto si aggiungeranno l'indirizzo IP del dispositivo e il nome descrittivo del dispositivo definiti nell'interfaccia utente Web locale del dispositivo a:

- Il file host nel client OPPURE
- Il file host nel server DNS

> [!IMPORTANT]
> Si consiglia di modificare il file host nel server DNS per la risoluzione dei nomi di dispositivo.

Nel client Windows usato per la connessione al dispositivo, seguire questa procedura:

1. Avviare **Blocco note** come amministratore e quindi aprire il file **hosts** che si trova in `C:\Windows\System32\Drivers\etc`.

    ![File hosts di Esplora risorse](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-1.png)


2. Aggiungere la voce seguente al file **hosts** sostituendo i valori con quelli appropriati per il dispositivo: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    È possibile ottenere l'indirizzo IP del dispositivo dalla pagina **Rete** e il nome descrittivo del dispositivo dalla pagina **Dispositivo** nell'interfaccia utente Web locale. Lo screenshot seguente del file hosts mostra la voce:

    ![File hosts di Esplora risorse 2](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Connettersi a una condivisione SMB

Nel client Windows Server connesso al dispositivo Azure Stack Edge Pro connettersi a una condivisione SMB immettendo i comandi:


1. In una finestra di comando digitare:

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > È possibile connettersi a una condivisione SMB solo con il nome del dispositivo e non tramite l'indirizzo IP del dispositivo.

2. Quando richiesto, immettere la password per la condivisione.  
   Di seguito è illustrato un esempio di output di questo comando.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. Sulla tastiera, premere Windows + R.

4. Nella finestra **Esegui**, specificare il `\\<device name>`e quindi selezionare **OK**.  

    ![Finestra di dialogo Esegui di Windows](media/azure-stack-edge-j-series-deploy-add-shares/run-window-1.png)

   Viene visualizzato Esplora file. Ora devono essere visualizzate le condivisioni create come cartelle. In Esplora file, fare doppio clic su una condivisione (cartella) per visualizzarne il contenuto.
 
    ![Connettersi alla condivisione SMB](./media/azure-stack-edge-j-series-deploy-add-shares/file-explorer-smbshare-1.png)

    I dati vengono scritti in queste condivisioni man mano che vengono generati e il dispositivo ne esegue il push nel cloud.

### <a name="connect-to-an-nfs-share"></a>Connettersi a una condivisione NFS

Nel client Linux connesso al dispositivo Azure Stack Edge Pro seguire questa procedura:

1. Verificare che nel client sia installato il client NFSv4. Per installare il client NFS, usare il comando seguente:

   `sudo apt-get install nfs-common`

    Per altre informazioni, vedere [Install NFSv4 client](https://help.ubuntu.com/community/NFSv4Howto) (Installare il client NFSv4).

2. Dopo l'installazione del client NFS, montare la condivisione NFS creata nel dispositivo Azure Stack Edge Pro usando il comando seguente:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    È possibile ottenere l'indirizzo IP del dispositivo dalla pagina **Rete** dell'interfaccia utente Web locale.

    > [!IMPORTANT]
    > L'uso dell'opzione `sync` durante il montaggio di condivisioni migliora le velocità di trasferimento dei file di grandi dimensioni.
    > Prima di montare la condivisione, verificare che le directory che fungeranno da punti di montaggio nel computer locale siano già state create. Queste directory non devono contenere file o sottocartelle.

    L'esempio seguente mostra come connettersi da NFS a una condivisione nel dispositivo Azure Stack Edge Pro. L'indirizzo IP del dispositivo è `10.10.10.60`. La condivisione `mylinuxshare2` è montata nella macchina virtuale Ubuntu. Il punto di montaggio della condivisione è `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> A questa versione si applicano le indicazioni seguenti:
> - Dopo aver creato un file nella condivisione, non è possibile rinominarlo. 
> - L'eliminazione di un file da una condivisione non comporta l'eliminazione della voce nell'account di archiviazione di Azure.
> - Quando si usa `rsync` per la copia su NFS, usare il flag `--inplace`. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati i seguenti argomenti relativi ad Azure Stack Edge Pro:

> [!div class="checklist"]
> * Aggiungere una condivisione
> * Connettersi alla condivisione

Per informazioni su come trasformare i dati con Azure Stack Edge Pro, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Trasformare i dati con Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)



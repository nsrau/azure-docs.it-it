---
title: Copiare dati in Microsoft Azure Data Box tramite NFS| Microsoft Docs
description: Informazioni su come copiare i dati in Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/20/2018
ms.author: alkohli
ms.openlocfilehash: 7ba6bc2cf3cf5286719bc6da519aabb364302af3
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550364"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Esercitazione: Copiare dati in Azure Data Box tramite NFS 

Questa esercitazione descrive come connettersi al computer host e copiarne i dati usando l'Interfaccia utente Web locale, quindi preparare il Data Box per la spedizione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi al Data Box
> * Copiare i dati nel Data Box
> * Preparare il Data Box per la spedizione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Sia stato ricevuto il Data Box e lo stato dell'ordine nel portale sia **Recapitato**.
3. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile disporre di una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà. 

## <a name="connect-to-data-box"></a>Connettersi al Data Box

In base all'account di archiviazione selezionato, Data Box crea fino a:
- Tre condivisioni per ogni account di archiviazione associato per GPv1 e GPv2.
- Una condivisione per l'account di archiviazione Premium o BLOB. 

Nelle condivisioni per BLOB di pagine e BLOB in blocchi, le entità di primo livello sono contenitori e le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure le entità di primo livello sono condivisioni, mentre le entità di secondo livello sono file.

Si consideri l'esempio seguente. 

- Account di archiviazione: *Mystoracct*
- Condivisione per BLOB in blocchi: *Mystoracct_BlockBlob/my-container/blob*
- Condivisione per BLOB di pagine: *Mystoracct_PageBlob/my-container/blob*
- Condivisione per File: *Mystoracct_AzFile/my-share*

Se si usa un computer host Linux, eseguire la procedura seguente per configurare il Data Box in modo da consentire l'accesso ai client NFS.

1. Specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS** fare clic su **Accesso client NFS**. 

    ![Configurare l'accesso dei client NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Specificare l'indirizzo IP del client NFS e fare clic su **Aggiungi**. Per configurare l'accesso per più client NFS, ripetere questa procedura. Fare clic su **OK**.

    ![Configurare l'accesso dei client NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Verificare che nel computer host Linux sia installata una [versione supportata](data-box-system-requirements.md) del client NFS. Usare la versione specifica della distribuzione Linux in uso. 

3. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS nel dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione del Data Box. L'indirizzo IP del dispositivo Data Box è `10.161.23.130` e la condivisione `Mystoracct_Blob` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

## <a name="copy-data-to-data-box"></a>Copiare i dati nel Data Box

Una volta stabilita la connessione alle condivisioni del Data Box, è necessario copiare i dati. Prima di procedere con la copia, esaminare le considerazioni seguenti:

- Assicurarsi di copiare i dati nelle condivisioni corrispondenti al formato dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella condivisione per i BLOB in blocchi. Se il formato dei dati non corrisponde al tipo di condivisione appropriato, il caricamento dei dati in Azure non riuscirà.
-  Durante la copia dei dati assicurarsi che le dimensioni dei dati siano conformi ai valori descritti nei [limiti per il servizio di archiviazione di Azure e per Azure Data Box](data-box-limits.md). 
- Se i dati caricati dal Data Box vengono caricati contemporaneamente da altre applicazioni all'esterno del Data Box, è possibile che si verifichino errori del processo di caricamento e il danneggiamento dei dati.
- È consigliabile non usare SMB e NFS contemporaneamente né copiare gli stessi dati nella stessa destinazione finale in Azure. In questi casi il risultato finale non può essere determinato.

Se si usa un computer host Linux, usare un'utilità di copia simile a Robocopy. Alcune soluzioni alternative disponibili in Linux sono [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) e [Ultracopier](https://ultracopier.first-world.info/).  

Il comando `cp` è una delle opzioni migliori per copiare una directory. Per altre informazioni sulla sintassi, vedere [cp man-pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se si usa l'opzione rsync per una copia multithread, seguire queste indicazioni:

 - Installare il pacchetto **CIFS Utils** o **NFS Utils** in base al file system usato dal client Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Installare **Rsync** e **Parallel** (varia in base alla versione distribuita di Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Creare un punto di montaggio.

    `sudo mkdir /mnt/databox`

 - Montare il volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Eseguire il mirroring della struttura di directory delle cartelle.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Copiare i file. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     dove j specifica il numero di parallelizzazione, X = numero di copie parallele

     È consigliabile iniziare con 16 copie parallele e aumentare il numero di thread in base alle risorse disponibili.

## <a name="prepare-to-ship"></a>Preparare per la spedizione

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Connettersi al Data Box
> * Copiare i dati nel Data Box
> * Preparare il Data Box per la spedizione

Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)


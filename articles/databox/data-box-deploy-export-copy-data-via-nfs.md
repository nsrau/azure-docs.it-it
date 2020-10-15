---
title: Esercitazione per copiare dati da Azure Data Box tramite NFS | Microsoft Docs
description: Informazioni su come copiare dati da Azure Data Box tramite NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: alkohli
ms.openlocfilehash: bd8e6d4175c57bd31c3fd83bf6f9669d2b65ffb2
ms.sourcegitcommit: 487a9f5272300d60df2622c3d13e794d54680f90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91660845"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs-preview"></a>Esercitazione: Copiare dati da Azure Data Box tramite NFS (anteprima)

Questa esercitazione descrive come connettersi al computer host e copiare i dati dall'interfaccia utente Web locale del dispositivo Data Box in un server dati locale tramite NFS. I dati del dispositivo Data Box vengono esportati dall'account di archiviazione di Azure.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati da Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. È stato inserito l'ordine per Azure Data Box.
    - Per un ordine di importazione, vedere [Esercitazione: Ordinare Azure Data Box](data-box-deploy-ordered.md).
    - Per un ordine di esportazione, vedere [Esercitazione: Ordinare Azure Data Box](data-box-deploy-export-ordered.md).
2. Aver ricevuto Data Box e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Sia disponibile un computer host in cui copiare i dati dal dispositivo Data Box. Il computer host deve:
   * Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
   * Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà.

## <a name="connect-to-data-box"></a>Connettersi al Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Se si usa un computer host Linux, eseguire la procedura seguente per configurare il Data Box in modo da consentire l'accesso ai client NFS. Il dispositivo Data Box accetta la connessione di un massimo di 5 client NFS alla volta.

1. Specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione:

    1.  Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS** fare clic su **Accesso client NFS**. 

        ![Aprire Accesso client NFS](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Per aggiungere un client NFS, specificare l'indirizzo IP del client e fare clic su **Aggiungi**. Il dispositivo Data Box accetta la connessione di un massimo di 5 client NFS alla volta. Al termine dell'operazione, fare clic su **OK**.

         ![Aggiungere un client NFS](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Verificare che nel computer host Linux sia installata una [versione supportata](data-box-system-requirements.md) del client NFS. Usare la versione specifica della distribuzione Linux in uso. 

3. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS nel dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione del Data Box. L'indirizzo IP del dispositivo Data Box è `10.161.23.130` e la condivisione `Mystoracct_Blob` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Per i client Mac, è necessario aggiungere un'altra opzione in questo modo: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.

## <a name="copy-data-from-data-box"></a>Copiare i dati da Data Box

Dopo aver stabilito la connessione alle condivisioni Data Box, il passaggio successivo consiste nel copiare i dati.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 È ora possibile iniziare la copia dei dati. Se si usa un computer host Linux, usare un'utilità di copia simile a Robocopy. Alcune soluzioni alternative disponibili in Linux sono [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) e [Ultracopier](https://ultracopier.first-world.info/).  

Il comando `cp` è una delle opzioni migliori per copiare una directory. Per altre informazioni sulla sintassi, vedere [cp man-pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se si usa l'opzione rsync per una copia multithread, seguire queste indicazioni:

* Installare il pacchetto **CIFS Utils** o **NFS Utils** in base al file system usato dal client Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installare **Rsync** e **Parallel** (varia in base alla versione distribuita di Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Creare un punto di montaggio.

    `sudo mkdir /mnt/databox`

* Montare il volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Eseguire il mirroring della struttura di directory delle cartelle.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Copiare i file.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     dove j specifica il numero di parallelizzazione, X = numero di copie parallele

     È consigliabile iniziare con 16 copie parallele e aumentare il numero di thread in base alle risorse disponibili.

> [!IMPORTANT]
> Non sono supportati i tipi di file Linux seguenti: collegamenti simbolici, file di caratteri, file di blocco, socket e pipe. Questi tipi di file causeranno errori durante la fase **Prepara per la spedizione**.

Al termine della copia, passare a **Dashboard** e verificare lo spazio occupato e lo spazio disponibile nel dispositivo.

È ora possibile procedere con la spedizione del dispositivo Data Box a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati da Data Box

Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-export-picked-up.md)

---
title: Copiare i dati in Microsoft Azure Data Box | Microsoft Docs
description: Informazioni su come copiare i dati in Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0204445464a9d61b4e25be1d71373ce8394b32f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957672"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Esercitazione: Copiare i dati in Azure Data Box 

Questa esercitazione descrive come connettersi al computer host e copiarne i dati usando l'Interfaccia utente Web locale, quindi preparare il Data Box per la spedizione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi al Data Box
> * Copiare i dati nel Data Box
> * Preparare il Data Box per la spedizione

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Sia stata completata l'[Esercitazione: Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Sia stato ricevuto il Data Box e lo stato dell'ordine nel portale sia **Recapitato**.
3. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile disporre di una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE, ma la velocità dell'operazione di copia ne risentirà. 

## <a name="connect-to-data-box"></a>Connettersi al Data Box

In base all'account di archiviazione selezionato, Data Box crea fino a:
- Tre condivisioni per ogni account di archiviazione associato (GPv1 e GPv2).
- Una condivisione per l'account di archiviazione Premium o BLOB. 

Nelle condivisioni per BLOB di pagine e BLOB in blocchi, le entità di primo livello sono contenitori e le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure, le entità di primo livello sono condivisioni e le entità di secondo livello sono file.

Si consideri l'esempio seguente. 

- Account di archiviazione: *Mystoracct*
- Condivisione per BLOB in blocchi: *Mystoracct_BlockBlob/my-container/blob*
- Condivisione per BLOB di pagine: *Mystoracct_BlockBlob/my-container/blob*
- Condivisione per File: *Mystoracct_AzFile/my-share*

La procedura da eseguire per la connessione e la copia può variare a seconda che il Data Box sia connesso a un computer host Windows Server o a un host Linux.

### <a name="connect-via-smb"></a>Connettersi tramite SMB 

Se si usa un computer host Windows Server, eseguire la procedura seguente per connettersi al Data Box.

1. Innanzitutto è necessario autenticare e avviare una sessione. Selezionare **Connetti e copia**. Fare clic su **Ottieni credenziali** per ottenere le credenziali di accesso per le condivisioni associate all'account di archiviazione. 

    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Nella finestra di dialogo Accedi alla condivisione e copia i dati copiare il **nome utente** e la **password** corrispondenti alla condivisione. Fare clic su **OK**.
    
    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Accedere alle condivisioni associate all'account di archiviazione (Mystoracct nell'esempio che segue). Usare il percorso `\\<IP of the device>\ShareName` per accedere alle condivisioni. A seconda del formato dei dati, connettersi alle condivisioni (usando il nome condivisione) all'indirizzo seguente: 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Per connettersi alle condivisioni dal computer host, aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Quando richiesto, immettere la password della condivisione. L'esempio seguente mostra la connessione a una condivisione tramite il comando precedente.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Fare clic su **OK**. Si apre Esplora file.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. Ora le condivisioni dovrebbero essere visualizzate come cartelle. Creare una cartella per i file da copiare (in questo caso modelli). A volte sulle cartelle potrebbe essere visualizzata una croce grigia. Questa croce non denota una condizione di errore. Le cartelle vengono contrassegnate dall'applicazione per indicarne lo stato.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Connettersi tramite NFS 

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

### <a name="copy-data-via-smb"></a>Copiare i dati tramite SMB

Dopo aver stabilito la connessione alla condivisione SMB, iniziare la copia dei dati. 

È possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy, per copiare i dati. È possibile avviare più processi di copia tramite Robocopy. Usare il comando seguente:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Gli attributi sono descritti nella tabella seguente.
    
|Attributo  |Descrizione  |
|---------|---------|
|/e     |Copia le sottodirectory, incluse le directory vuote.         |
|/r:     |Specifica il numero di tentativi per le copie non riuscite.         |
|/w:     |Specifica il tempo di attesa tra i tentativi, in secondi.         |
|/is     |Include gli stessi file.         |
|/nfl     |Specifica che i nomi dei file non devono essere inseriti nei log.         |
|/ndl    |Specifica che i nomi di directory non devono essere inseriti nei log.        |
|/np     |Specifica che lo stato dell'operazione di copia (il numero di file o directory copiati finora) non deve essere visualizzato. La visualizzazione dello stato riduce notevolmente le prestazioni.         |
|/MT     | Specifica l'uso del multithreading (consigliati 32 o 64 thread). Questa opzione non può essere usata con i file crittografati. Potrebbe essere necessario separare i file crittografati da quelli non crittografati. Tuttavia, la copia a thread singolo riduce notevolmente le prestazioni.           |
|/fft     | Usare questa opzione per ridurre la granularità del timestamp per qualsiasi file system.        |
|/b     | Copia i file in modalità di backup.        |
|/z    | Copia i file in modalità di riavvio (usare se l'ambiente è instabile). Questa opzione riduce la velocità effettiva a causa dell'aumento delle operazioni di registrazione.      |
| /zb     | Usa la modalità di riavvio. Se l'accesso viene negato, questa opzione usa la modalità di backup. Questa opzione riduce la velocità effettiva a causa delle operazioni di checkpoint.         |
|/efsraw     | Copia tutti i file crittografati in modalità RAW EFS. Usare solo con i file crittografati.         |
|log+:<LogFile>| Accoda l'output al file di log esistente.|    
 
L'esempio seguente mostra l'output del comando robocopy per la copia dei file nel Data Box.
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

Per ottimizzare le prestazioni, usare i parametri robocopy seguenti durante la copia dei dati.

|    Piattaforma    |    Prevalentemente file di piccole dimensioni < 512 KB                           |    Prevalentemente file di medie dimensioni 512 KB-1 MB                      |    Prevalentemente file di grandi dimensioni > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 sessioni di Robocopy <br> 16 thread per sessione    |    3 sessioni di Robocopy <br> 16 thread per sessione    |    2 sessioni di Robocopy <br> 24 thread per sessione    |  |


Per altre informazioni sul comando Robocopy, vedere [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy e alcuni esempi).

Aprire la cartella di destinazione per visualizzare e verificare i file copiati. In caso di errori durante il processo di copia, scaricare i file di log degli errori per la risoluzione dei problemi.

Per assicurare l'integrità dei dati, il checksum viene calcolato inline durante la copia dei dati. Al termine della copia, verificare lo spazio occupato e lo spazio disponibile nel dispositivo.
    
   ![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>Copiare i dati tramite NFS

Se si usa un computer host Linux, usare un'utilità di copia simile a Robocopy. Alcune soluzioni alternative disponibili in Linux sono [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) e [Ultracopier](https://ultracopier.first-world.info/).  

Il comando cp è una delle opzioni migliori per copiare una directory. Per altre informazioni sulla sintassi, vedere [cp man-pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se si usa l'opzione rsync per una copia multithread, seguire queste indicazioni:

 - Installare il pacchetto **CIFS Utils** o **NFS Utils** in base al file system usato dal client Linux.

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

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

Il passaggio finale consiste nel preparare il dispositivo per la spedizione. In questo passaggio tutte le condivisioni del dispositivo vengono portate offline. Una volta avviata la preparazione del dispositivo per la spedizione, le condivisioni non sono più accessibili.
1. Selezionare **Prepara per la spedizione** e fare clic su **Avvia preparazione**. 
   
    ![Preparare per la spedizione 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Se il checksum non è stato abilitato, viene visualizzata l'opzione per abilitarlo. Per preservare l'integrità dei dati, è consigliabile eseguire la convalida del checksum. Selezionando **Abilita checksum** viene attivato il calcolo del checksum, la cui durata dipende dalle dimensioni dei dati. Fare clic su **Avvia preparazione**.
    1. Le condivisioni del dispositivo vengono portate offline e il dispositivo viene bloccato durante la preparazione per la spedizione.
        
        ![Preparare per la spedizione 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. Completata la preparazione, lo stato del dispositivo viene aggiornato a *Pronto per la spedizione*. 
        
        ![Preparare per la spedizione 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Scaricare l'elenco dei file (manifesto) copiati in questo processo. In seguito è possibile usare questo elenco per verificare i file caricati in Azure.
        
        ![Preparare per la spedizione 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Arrestare il dispositivo. Passare alla pagina **Arresta o riavvia** e fare clic su **Arresta**. Alla richiesta di conferma fare clic su **OK** per continuare.
4. Rimuovere i cavi. Il passaggio successivo consiste nella spedizione del dispositivo a Microsoft.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Connettersi al Data Box
> * Copiare i dati nel Data Box
> * Preparare il Data Box per la spedizione

Passare all'esercitazione successiva per informazioni su come configurare e copiare i dati nel Data Box.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)


---
title: Esercitazione per copiare dati tramite SMB in Azure Data Box | Microsoft Docs
description: Informazioni su come copiare dati in Azure Data Box tramite SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d86da3013a3cb4573556bc14ea1e6a0fbab72623
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70240401"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Esercitazione: Copiare dati in Azure Data Box tramite SMB

::: zone-end

::: zone target="chromeless"

# <a name="copy-data-to-azure-data-box"></a>Copiare i dati in Azure Data Box

::: zone-end

::: zone target="docs"

Questa esercitazione descrive come connettersi al computer host e copiarne i dati usando l'interfaccia utente Web locale.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati nel Data Box


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Aver ricevuto Data Box e che lo stato dell'ordine nel portale sia **Recapitati**.
3. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà.

## <a name="connect-to-data-box"></a>Connettersi al Data Box

In base all'account di archiviazione selezionato, Data Box crea fino a:
- Tre condivisioni per ogni account di archiviazione associato per GPv1 e GPv2.
- Una condivisione per l'archiviazione Premium.
- Una condivisione per l'account di archiviazione BLOB.

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure le entità di primo livello sono condivisioni, mentre le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Se si usa un computer host Windows Server, eseguire le operazioni seguenti per connettersi a Data Box.

1. Innanzitutto è necessario autenticare e avviare una sessione. Selezionare **Connetti e copia**. Fare clic su **Ottieni credenziali** per ottenere le credenziali di accesso per le condivisioni associate all'account di archiviazione. 

    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Nella finestra di dialogo Accedi alla condivisione e copia i dati copiare il **nome utente** e la **password** corrispondenti alla condivisione. Fare clic su **OK**.
    
    ![Ottenere le credenziali delle condivisioni 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Per accedere alle condivisioni associate all'account di archiviazione (*devicemanagertest1* nell'esempio seguente) dal computer host, aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    A seconda del formato dei dati, i percorsi delle condivisioni sono i seguenti:
    - BLOB in blocchi di Azure: `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - BLOB di pagine di Azure: `\\10.126.76.172\devicemanagertest1_PageBlob`
    - File di Azure: `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. Quando richiesto, immettere la password della condivisione. L'esempio seguente mostra la connessione a una condivisione tramite il comando precedente.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Fare clic su **OK** per aprire Esplora file.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Le condivisioni verranno ora visualizzate come cartelle.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)    

    **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.
    
Se si usa un client Linux, usare il comando seguente per montare la condivisione SMB. Il parametro "vers" di seguito è la versione di SMB supportata dall'host Linux. Collegare la versione appropriata nel comando seguente. Per le versioni di SMB supportate da Data Box,vedere [File system supportati per client Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

    `sudo mount -t nfs -o vers=2.1 10.126.76.172:/devicemanagertest1_BlockBlob /home/databoxubuntuhost/databox`
    


## <a name="copy-data-to-data-box"></a>Copiare i dati nel Data Box

Dopo aver stabilito la connessione alle condivisioni Data Box, il passaggio successivo consiste nel copiare i dati. Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

- Assicurarsi di copiare i dati nelle condivisioni corrispondenti al formato dei dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella condivisione per i BLOB in blocchi. Copiare i dischi rigidi virtuali nel BLOB di pagine. Se il formato dei dati non corrisponde al tipo di condivisione appropriato, il caricamento dei dati in Azure non riuscirà.
-  Durante la copia dei dati, assicurarsi che le dimensioni dei dati siano conformi ai limiti di dimensione descritti in [Limiti per l'archiviazione di Azure e per Azure Data Box](data-box-limits.md).
- Se i dati caricati dal Data Box vengono caricati contemporaneamente da altre applicazioni all'esterno del Data Box, è possibile che si verifichino errori del processo di caricamento e il danneggiamento dei dati.
- È consigliabile:
    - Non usare contemporaneamente SMB e NFS.
    - Copiare gli stessi dati nella stessa destinazione finale in Azure. 
     
  In questi casi, non è possibile prevedere il risultato finale.
- Creare sempre una cartella per i file che si intende copiare nella condivisione e quindi copiare i file in questa cartella. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.

Dopo aver stabilito la connessione alla condivisione SMB, avviare la copia dei dati. È possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy, per copiare i dati. È possibile avviare più processi di copia tramite Robocopy. Usare il comando seguente:
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Gli attributi sono descritti nella tabella seguente.
    
|Attributo  |DESCRIZIONE  |
|---------|---------|
|/e     |Copia le sottodirectory, incluse le directory vuote.         |
|/r:     |Specifica il numero di tentativi per le copie non riuscite.         |
|/w:     |Specifica il tempo di attesa tra i tentativi, in secondi.         |
|/is     |Include gli stessi file.         |
|/nfl     |Specifica che i nomi dei file non vengono registrati.         |
|/ndl    |Specifica che i nomi delle directory non vengono registrati.        |
|/np     |Specifica che lo stato dell'operazione di copia (il numero di file o directory copiati finora) non deve essere visualizzato. La visualizzazione dello stato riduce notevolmente le prestazioni.         |
|/MT     | Specifica l'uso del multithreading (consigliati 32 o 64 thread). Questa opzione non può essere usata con i file crittografati. Potrebbe essere necessario separare i file crittografati da quelli non crittografati. Tuttavia, la copia a thread singolo riduce notevolmente le prestazioni.           |
|/fft     | Usare questa opzione per ridurre la granularità del timestamp per qualsiasi file system.        |
|/b     | Copia i file in modalità di backup.        |
|/z    | Copia i file in modalità di riavvio (usare se l'ambiente è instabile). Questa opzione riduce la velocità effettiva a causa dell'aumento delle operazioni di registrazione.      |
| /zb     | Usa la modalità di riavvio. Se l'accesso viene negato, questa opzione usa la modalità di backup. Questa opzione riduce la velocità effettiva a causa delle operazioni di checkpoint.         |
|/efsraw     | Copia tutti i file crittografati in modalità RAW EFS. Usare solo con i file crittografati.         |
|log+:\<LogFile>| Accoda l'output al file di log esistente.|    
 
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
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sessioni di Robocopy <br> 16 thread per sessione    |    3 sessioni di Robocopy <br> 16 thread per sessione    |    2 sessioni di Robocopy <br> 24 thread per sessione    |


Per altre informazioni sul comando Robocopy, vedere [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy e alcuni esempi).

Aprire la cartella di destinazione per visualizzare e verificare i file copiati. In caso di errori durante il processo di copia, scaricare i file di log degli errori per la risoluzione dei problemi. Per altre informazioni, vedere [Visualizzare i log degli errori durante la copia dei dati in Data Box](data-box-logs.md#view-error-log-during-data-copy). Per un elenco dettagliato degli errori durante la copia dei dati, vedere [Risolvere i problemi di Data Box](data-box-troubleshoot.md).

Per assicurare l'integrità dei dati, il checksum viene calcolato inline durante la copia dei dati. Al termine della copia, verificare lo spazio occupato e lo spazio disponibile nel dispositivo.
    
   ![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

È possibile copiare i dati dal server di origine nel dispositivo Data Box tramite SMB, NFS, REST o il servizio copia dei dati oppure nei dischi gestiti.

In ogni caso assicurarsi che i nomi delle condivisioni e delle cartelle e le dimensioni dei dati siano conformi alle linee guida descritte nei [limiti dei servizi Archiviazione di Azure e Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Copiare i dati tramite SMB

1. Se si usa un host Windows, eseguire il comando seguente per connettersi alle condivisioni SMB:

    `\\<IP address of your device>\ShareName`

2. Per ottenere le credenziali di accesso alle condivisioni, passare alla pagina **Connetti e copia** nell'interfaccia utente Web locale di Data Box.
3. Per copiare i dati nelle condivisioni, usare uno strumento di copia file compatibile con SMB, ad esempio Robocopy. 

Per le istruzioni dettagliate, vedere [Esercitazione: Copiare dati in Azure Data Box tramite SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Copiare i dati tramite NFS

1. Se si usa un host NFS, eseguire il comando seguente per montare le condivisioni NFS nel dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Per ottenere le credenziali di accesso alle condivisioni, passare alla pagina **Connetti e copia** nell'interfaccia utente Web locale di Data Box.
3. Per copiare i dati, usare il comando `cp` o `rsync`.

Per le istruzioni dettagliate, vedere [Esercitazione: Copiare dati in Azure Data Box tramite NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Copiare dati tramite REST

1. Per copiare i dati usando l'archivio BLOB di Data Box tramite API REST, è possibile connettersi tramite *http* o *https*.
2. Per copiare dati nell'archivio BLOB di Data Box, è possibile usare AzCopy.

Per le istruzioni dettagliate, vedere [Esercitazione: Copiare dati nell'archiviazione BLOB di Azure Data Box tramite API REST](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Copiare dati tramite il servizio di copia dei dati

1. Per copiare i dati usando il servizio di copia dei dati è necessario creare un processo. Nell'interfaccia utente Web locale del dispositivo Data Box passare a **Gestisci > Copia dati > Crea**. 
2. Immettere i parametri e creare un processo.

Per le istruzioni dettagliate, vedere [Esercitazione: Usare il servizio di copia dei dati per copiare i dati in Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Copiare i dati nei dischi gestiti

1. Quando si ordina il dispositivo Data Box, è necessario avere selezionato i dischi gestiti come destinazione di archiviazione.
2. È possibile connettersi a Data Box tramite condivisioni SMB o NFS.
3. È quindi possibile copiare i dati tramite gli strumenti SMB o NFS.

Per le istruzioni dettagliate, vedere [Esercitazione: Usare Data Box per importare dati come dischi gestiti in Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end


::: zone target="docs"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati nel Data Box


Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)

::: zone-end


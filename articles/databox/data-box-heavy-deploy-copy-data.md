---
title: Esercitazione per copiare dati tramite SMB in Azure Data Box Heavy | Microsoft Docs
description: Informazioni su come copiare dati in Azure Data Box Heavy tramite SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 1c45e06159e4c2850efa2d3ab3290647961fb7e1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592426"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Esercitazione: Copiare i dati in Azure Data Box Heavy tramite SMB

Questa esercitazione descrive come connettersi al computer host e copiarne i dati usando l'interfaccia utente Web locale.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Connettersi a Data Box Heavy
> * Copiare i dati in Data Box Heavy


## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Aver ricevuto Data Box Heavy e che lo stato dell'ordine nel portale sia **Recapitato**.
3. Avere un computer host con i dati da copiare in Data Box Heavy. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. Per una velocità di copia più veloce, è possibile utilizzare in parallelo due connessioni da 40 GbE (una per nodo). Se non si dispone di una connessione da 40 GbE, si consiglia di avere almeno due connessioni da 10 GbE (una per nodo).

## <a name="connect-to-data-box-heavy-shares"></a>Connettersi alle condivisioni di Data Box Heavy

In base all'account di archiviazione selezionato, Data Box Heavy crea fino a:
- Tre condivisioni per ogni account di archiviazione associato per GPv1 e GPv2.
- Una condivisione per l'archiviazione Premium.
- Una condivisione per l'account di archiviazione BLOB.

Queste condivisioni vengono create in entrambi i nodi del dispositivo.

Nelle condivisioni di BLOB in blocchi e BLOB di pagine:
- Le entità di primo livello sono contenitori.
- Le entità di secondo livello sono BLOB.

Nelle condivisioni per File di Azure:
- Le entità di primo livello sono condivisioni.
- Le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box Heavy e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

I passaggi per connettersi usando un client Windows o Linux sono diversi.

> [!NOTE]
> Seguire la stessa procedura per connettersi a entrambi i nodi del dispositivo in parallelo.

### <a name="connect-on-a-windows-system"></a>Connettersi in un sistema Windows

Se si usa un computer host Windows Server, eseguire le operazioni seguenti per connettersi a Data Box Heavy.

1. Innanzitutto è necessario autenticare e avviare una sessione. Selezionare **Connetti e copia**. Fare clic su **Ottieni credenziali** per ottenere le credenziali di accesso per le condivisioni associate all'account di archiviazione.

    ![Ottenere le credenziali delle condivisioni 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. Nella finestra di dialogo Accedi alla condivisione e copia i dati copiare il **nome utente** e la **password** corrispondenti alla condivisione. Fare clic su **OK**.
    
    ![Ottenere le credenziali delle condivisioni 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Per accedere alle condivisioni associate all'account di archiviazione (*databoxe2etest* nell'esempio seguente) dal computer host, aprire una finestra di comando. Al prompt dei comandi digitare:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    A seconda del formato dei dati, i percorsi delle condivisioni sono i seguenti:
    - BLOB in blocchi di Azure: `\\10.100.10.100\databoxe2etest_BlockBlob`
    - BLOB di pagine di Azure: `\\10.100.10.100\databoxe2etest_PageBlob`
    - File di Azure: `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Quando richiesto, immettere la password della condivisione. L'esempio seguente mostra la connessione a una condivisione tramite il comando precedente.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Premere WINDOWS+R. Nella finestra **Esegui** specificare `\\<device IP address>`. Fare clic su **OK** per aprire Esplora file.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Le condivisioni verranno ora visualizzate come cartelle.
    
    ![Connettersi a una condivisione tramite Esplora file 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.
    
### <a name="connect-on-a-linux-system"></a>Connettersi in un sistema Linux

Se si usa un client Linux, usare il comando seguente per montare la condivisione SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

Il parametro `vers` è la versione di SMB supportata dall'host Linux. Immettere la versione appropriata nel comando precedente.

Per le versioni di SMB supportate da Data Box Heavy,vedere [File system supportati per client Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Copiare i dati in Data Box Heavy

Dopo aver stabilito la connessione alle condivisioni Data Box Heavy, il passaggio successivo consiste nel copiare i dati.

### <a name="copy-considerations"></a>Considerazioni sulla copia

Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

- Assicurarsi di copiare i dati nelle condivisioni corrispondenti al formato dei dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella condivisione per i BLOB in blocchi. Copiare i dischi rigidi virtuali nel BLOB di pagine.

    Se il formato dei dati non corrisponde al tipo di condivisione appropriato, il caricamento dei dati in Azure non riuscirà.
-  Durante la copia dei dati, assicurarsi che le dimensioni dei dati siano conformi ai limiti di dimensione descritti in [Limiti per l'archiviazione di Azure e per Data Box Heavy](data-box-heavy-limits.md).
- Se i dati caricati da Data Box Heavy vengono caricati contemporaneamente da altre applicazioni all'esterno di Data Box Heavy, è possibile che si verifichino errori del processo di caricamento e il danneggiamento dei dati.
- È consigliabile:
    - Non usare contemporaneamente SMB e NFS.
    - Copiare gli stessi dati nella stessa destinazione finale in Azure.
     
  In questi casi, non è possibile prevedere il risultato finale.
- Creare sempre una cartella per i file che si intende copiare nella condivisione e quindi copiare i file in questa cartella. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.

Dopo aver stabilito la connessione alla condivisione SMB, avviare la copia dei dati.

1. È possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy, per copiare i dati. È possibile avviare più processi di copia tramite Robocopy. Usare il comando seguente:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Gli attributi sono descritti nella tabella seguente.
    
    |Attributo  |DESCRIZIONE  |
    |---------|---------|
    |/e      |Copia le sottodirectory, incluse le directory vuote.         |
    |/r:     |Specifica il numero di tentativi per le copie non riuscite.         |
    |/w:     |Specifica il tempo di attesa tra i tentativi, in secondi.         |
    |/is     |Include gli stessi file.         |
    |/nfl    |Specifica che i nomi dei file non vengono registrati.         |
    |/ndl    |Specifica che i nomi delle directory non vengono registrati.        |
    |/np     |Specifica che lo stato dell'operazione di copia (il numero di file o directory copiati finora) non deve essere visualizzato. La visualizzazione dello stato riduce notevolmente le prestazioni.         |
    |/MT     | Specifica l'uso del multithreading (consigliati 32 o 64 thread). Questa opzione non può essere usata con i file crittografati. Potrebbe essere necessario separare i file crittografati da quelli non crittografati. Tuttavia, la copia a thread singolo riduce notevolmente le prestazioni.           |
    |/fft    | Usare questa opzione per ridurre la granularità del timestamp per qualsiasi file system.        |
    |/b      | Copia i file in modalità di backup.        |
    |/z      | Copia i file in modalità di riavvio (usare se l'ambiente è instabile). Questa opzione riduce la velocità effettiva a causa dell'aumento delle operazioni di registrazione.      |
    | /zb    | Usa la modalità di riavvio. Se l'accesso viene negato, questa opzione usa la modalità di backup. Questa opzione riduce la velocità effettiva a causa delle operazioni di checkpoint.         |
    |/efsraw | Copia tutti i file crittografati in modalità RAW EFS. Usare solo con i file crittografati.         |
    |log+:\<LogFile>| Accoda l'output al file di log esistente.|
    
 
    L'esempio seguente mostra l'output del comando robocopy per copiare i file su Data Box Heavy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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
    ```       

2. Per ottimizzare le prestazioni, usare i parametri robocopy seguenti durante la copia dei dati. (I numeri riportati di seguito rappresentano gli scenari di casi ottimali).

    | Piattaforma    | Prevalentemente file di piccole dimensioni < 512 KB    | Prevalentemente file di medie dimensioni 512 KB-1 MB  | Prevalentemente file di grandi dimensioni > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sessioni di Robocopy <br> 24 thread per sessione | 6 sessioni di Robocopy <br> 16 thread per sessione | 6 sessioni di Robocopy <br> 16 thread per sessione |


    Per altre informazioni sul comando Robocopy, vedere [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy e alcuni esempi).

3. Aprire la cartella di destinazione per visualizzare e verificare i file copiati.

    ![Visualizzare i file copiati](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Durante la copia dei dati:

    - I nomi dei file, le dimensioni e il formato vengono convalidati per garantire che soddisfino i limiti di archiviazione e degli oggetti di Azure, nonché le convenzioni di denominazione dei file e dei contenitori di Azure.
    - Viene calcolato il checksum inline per garantire l'integrità dei dati.

    In caso di errori durante il processo di copia, scaricare i file di log degli errori per la risoluzione dei problemi. Selezionare l'icona della freccia per scaricare i file degli errori.

    ![Scaricare i file degli errori](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Per altre informazioni, vedere [Visualizzare i log degli errori durante la copia dei dati in Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Per un elenco dettagliato degli errori durante la copia dei dati, vedere [Risolvere i problemi di Data Box Heavy](data-box-troubleshoot.md).

5. Aprire il file degli errori nel Blocco note. Il file degli errori seguente indica che i dati non sono allineati correttamente.

    ![Aprire il file degli errori](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    Per un BLOB di pagine i dati devono essere allineati a 512 byte. Dopo la rimozione di questi dati, l'errore si risolve come mostrato nella schermata seguente.

    ![Errore risolto](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Al termine della copia, passare alla pagina **Visualizza dashboard**. Verificare lo spazio occupato e lo spazio disponibile nel dispositivo.
    
    ![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Ripetere i passaggi precedenti per copiare i dati nel secondo nodo del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box Heavy, ad esempio:

> [!div class="checklist"]
> * Connettersi a Data Box Heavy
> * Copiare i dati in Data Box Heavy


Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box Heavy a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box Heavy a Microsoft](./data-box-heavy-deploy-picked-up.md)


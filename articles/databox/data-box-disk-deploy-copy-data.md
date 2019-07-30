---
title: Esercitazione per copiare dati in Azure Data Box Disk | Microsoft Docs
description: Usare questa esercitazione per informazioni su come copiare i dati in Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 336cc7dae00d06e38e4be8671f1cb11ed73e5edc
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414635"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Esercitazione: Copiare dati in Azure Data Box Disk ed eseguire la verifica

Questa esercitazione descrive come copiare dati dal computer host e quindi generare i checksum per la verifica dell'integrità.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Copiare i dati sul Data Box Disk.
> * Verificare i dati

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:
- Aver completato l'esercitazione descritta in [Esercitazione: Installare e configurare Azure Data Box Disk](data-box-disk-deploy-set-up.md).
- I dischi vengono sbloccati e connessi a un computer client.
- Il computer client usato per copiare i dati nei dischi deve eseguire un [sistema operativo supportato](data-box-disk-system-requirements.md##supported-operating-systems-for-clients).
- Il tipo di archiviazione scelto per i dati corrisponda a uno dei [tipi di archiviazione supportati](data-box-disk-system-requirements.md#supported-storage-types-for-upload).
- Esaminare i limiti dei dischi gestiti in [Limiti delle dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits).


## <a name="copy-data-to-disks"></a>Copiare i dati sui dischi

Prima di copiare i dati sui dischi, esaminare le considerazioni seguenti:

- È responsabilità dell'utente assicurarsi di copiare i dati nelle cartelle corrispondenti al formato dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella cartella per i BLOB in blocchi. Se il formato dei dati non corrisponde alla cartella appropriata (tipo di archiviazione), il caricamento dei dati in Azure avrà negativo.
- Durante la copia dei dati assicurarsi che la dimensione dei dati sia conforme ai valori descritti nei [limiti delle risorse di archiviazione e di Data Box Disk in Azure](data-box-disk-limits.md).
- Se i dati caricati dal Data Box Disk vengono caricati contemporaneamente da altre applicazioni all'esterno del Data Box Disk, è possibile che si verifichino errori del processo di caricamento e il danneggiamento di dati.

   > [!IMPORTANT]
   >  Se sono stati specificati i dischi gestiti come una delle destinazioni di archiviazione durante la creazione dell'ordine, la sezione seguente è applicabile.

- In tutte le cartelle create preventivamente e in Data Box Disk può essere presente un solo disco gestito con un determinato nome in un gruppo di risorse. I dischi rigidi virtuali caricati nelle cartelle create preventivamente devono quindi avere nomi univoci. Verificare che il nome assegnato non corrisponda a un disco gestito già esistente in un gruppo di risorse. Se i dischi rigidi virtuali hanno lo stesso nome, un solo disco rigido virtuale verrà convertito in un disco gestito con tale nome. Gli altri dischi rigidi virtuali verranno caricati come BLOB di pagine nell'account di archiviazione di staging.
- Copiare sempre i dischi rigidi virtuali in una delle cartelle create preventivamente. Se vengono copiati all'esterno di queste cartelle o in una cartella creata dall'utente, i dischi rigidi virtuali verranno caricati nell'account di archiviazione di Azure come BLOB di pagine e non come dischi gestiti.
- Per la creazione di dischi gestiti possono essere caricati solo dischi rigidi virtuali a dimensione fissa. I dischi rigidi virtuali dinamici o differenze e i file VHDX non sono supportati.


Eseguire la procedura seguente per connettersi e copiare i dati dal computer sul Data Box Disk.

1. Visualizzare il contenuto dell'unità sbloccata. L'elenco delle cartelle e delle sottocartelle create preventivamente nell'unità varia in base alle opzioni selezionate al momento dell'ordine di Data Box Disk.

    |Destinazione di archiviazione selezionata  |Tipo di account di archiviazione|Tipo di account di archiviazione di staging |Cartelle e sottocartelle  |
    |---------|---------|---------|------------------|
    |Account di archiviazione     |Utilizzo generico v1 o v2                 | ND | BlockBlob <br> PageBlob <br> AzureFile        |
    |Account di archiviazione     |Account di archiviazione BLOB         | ND | BlockBlob        |
    |Dischi gestiti     |ND | Utilizzo generico v1 o v2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |Account di archiviazione <br> Dischi gestiti     |Utilizzo generico v1 o v2 | Utilizzo generico v1 o v2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |Account di archiviazione <br> Dischi gestiti    |Account di archiviazione BLOB | Utilizzo generico v1 o v2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    Di seguito è riportato uno screenshot di esempio di un ordine in cui è stato specificato un account di archiviazione per utilizzo generico v2:

    ![Contenuto dell'unità disco](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copiare i dati che devono essere importati come BLOB in blocchi nella cartella *BlockBlob*. Analogamente, copiare i dati di tipo VHD/VHDX nella cartella *PageBlob* e i dati nella cartella *AzureFile*.

    Viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nelle cartelle BlockBlob e PageBlob. Tutti i file nelle cartelle BlockBlob e PageBlob vengono copiati in un contenitore predefinito `$root` sotto l'account di Archiviazione di Azure. Tutti i file nel contenitore `$root` vengono sempre caricati come BLOB in blocchi.

   Copiare i file in una cartella all'interno della cartella *AzureFile*. Una sottocartella all'interno di *AzureFile* determina la creazione di una condivisione file. I file copiati direttamente nella cartella *AzureFile* hanno esito negativo e vengono caricati come BLOB in blocchi.

    Se nella directory radice esistono file e cartelle, è necessario spostarli in un'altra cartella prima di iniziare la copia dei dati.

    > [!IMPORTANT]
    > Tutti i contenitori, i BLOB e i nomi file devono essere conformi alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se queste regole non vengono rispettate, il caricamento di dati in Azure avrà esito negativo.

3. Quando si copiano file, assicurarsi che la dimensione non superi ~4,7 TiB per i BLOB in blocchi, ~8 TiB per i BLOB di pagine e ~1 TiB per File di Azure. 
4. È possibile usare il trascinamento della selezione con Esplora file per copiare i dati. È anche possibile usare qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy per copiare i dati. È possibile avviare più processi di copia usando il seguente comando di Robocopy:

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    I parametri e le opzioni del comando sono riportati nella tabella seguente:
    
    |Parametri/opzioni  |DESCRIZIONE |
    |--------------------|------------|
    |Source (Sorgente)            | Specifica il percorso della directory di origine.        |
    |Destination       | Specifica il percorso della directory di destinazione.        |
    |/E                  | Copia le sottodirectory, incluse le directory vuote. |
    |/MT[:N]             | Crea copie multi-thread con N thread, dove N è un numero intero compreso tra 1 e 128. <br>Il valore predefinito per N è 8.        |
    |/R: \<N>             | Specifica il numero di tentativi per le copie non riuscite. Il valore predefinito di N è 1.000.000 (un milione di tentativi).        |
    |/W: \<N>             | Specifica il tempo di attesa tra i tentativi, in secondi. Il valore predefinito di N è 30 (tempo di attesa di 30 secondi).        |
    |/NFL                | Specifica che i nomi dei file non devono essere inseriti nei log.        |
    |/NDL                | Specifica che i nomi di directory non devono essere inseriti nei log.        |
    |/FFT                | Presuppone i tempi dei file FAT (precisione di due secondi).        |
    |/Log:\<Log File>     | Scrive l'output di stato nel file di log sovrascrivendo il file di log esistente.         |

    È possibile usare più dischi in parallelo con più processi in esecuzione su ogni disco.

6. Controllare lo stato della copia quando il processo è in corso. L'esempio seguente mostra l'output del comando robocopy per copiare i file sul Data Box Disk.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    Per ottimizzare le prestazioni, usare i parametri robocopy seguenti durante la copia dei dati.

    |    Piattaforma    |    Prevalentemente file di piccole dimensioni < 512 KB                           |    Prevalentemente file di medie dimensioni 512 KB-1 MB                      |    Prevalentemente file di grandi dimensioni > 1 MB                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    4 sessioni* di Robocopy <br> 16 thread per sessione    |    2 sessioni* di Robocopy <br> 16 thread per sessione    |    2 sessioni* di Robocopy <br> 16 thread per sessione    |
    
    **Ogni sessione di Robocopy può contenere al massimo 7.000 directory e 150 milioni di file.*
    
    >[!NOTE]
    > I parametri consigliati in precedenza si basano sull'ambiente usato per test interni.
    
    Per altre informazioni sul comando Robocopy, vedere [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy e alcuni esempi).

6. Aprire la cartella di destinazione per visualizzare e verificare i file copiati. In caso di errori durante il processo di copia, scaricare i file di log per la risoluzione dei problemi. I file di log si trovano nella posizione specificata nel comando Robocopy.
 
### <a name="split-and-copy-data-to-disks"></a>Dividere e copiare i dati sui dischi

Scegliere questa procedura facoltativa quando si usano più dischi e si ha un set di dati di grandi dimensioni che deve essere suddiviso e copiato tra tutti i dischi. Lo strumento di divisione della copia di Data Box aiuta a dividere e copiare i dati in un computer Windows.

>[!IMPORTANT]
> Lo strumento di divisione della copia di Data Box consente anche di convalidare i dati. Se si usa tale strumento per copiare i dati, è possibile ignorare il [passaggio di convalida](#validate-data).
> Lo strumento di divisione della copia non è supportato con dischi gestiti.

1. Nel computer Windows, assicurarsi di avere scaricato ed estratto in una cartella locale lo strumento di divisione della copia di Data Box. Questo strumento è stato scaricato quando è stato scaricato il set di strumenti di Data Box Disk per Windows.
2. Aprire Esplora file. Prendere nota dell'unità di origine dati e le lettere di unità assegnate al Data Box Disk. 

     ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identificare i dati di origine da copiare. Ad esempio, in questo caso:
    - Sono stati identificati i seguenti dati di BLOB in blocchi.

         ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Sono stati identificati i seguenti dati di BLOB di pagine.

         ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Passare alla cartella in cui è stato estratto il software. Individuare il file `SampleConfig.json` in tale cartella. Si tratta di un file di sola lettura che è possibile modificare e salvare.

   ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Modificare il file `SampleConfig.json`.
 
   - Specificare un nome per il processo. Viene creata una cartella nel Data Box Disk che infine diventa il contenitore nell'account di archiviazione di Azure associato a tali dischi. Il nome del processo deve seguire le convenzioni di denominazione di contenitori di Azure. 
   - Specificare un percorso di origine prendendo nota del formato del percorso nel file `SampleConfigFile.json`. 
   - Immettere le lettere di unità corrispondenti ai dischi di destinazione. I dati vengono prelevati dal percorso di origine e copiati tra più dischi.
   - Specificare un percorso per i file di log. Per impostazione predefinita, viene inviato alla directory corrente in cui si trova il file `.exe`.

     ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Per convalidare il formato di file, passare a `JSONlint`. Salvare il file con il nome `ConfigFile.json`. 

     ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Aprire una finestra del prompt dei comandi. 

8. Eseguire `DataBoxDiskSplitCopy.exe`. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Premere INVIO per continuare lo script.

    ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Dopo aver diviso e copiato il set di dati, viene visualizzato il riepilogo dello strumento Copia suddivisa per la sessione di copia. Di seguito è riportato un output di esempio.

    ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Verificare che i dati siano stati suddivisi tra i dischi di destinazione. 
 
    ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Se si esamina ulteriormente il contenuto dell'unità `n:`, si noterà che le due sottocartelle vengono create in corrispondenza dei dati in formato BLOB in blocchi e BLOB di pagine.
    
     ![Divisione della copia dati](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Se la sessione di copia non riesce, per recuperarla e riprenderla usare il comando seguente:

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

Se vengono visualizzati errori quando si usa lo strumento di divisione della copia dati, passare alla procedura di [risoluzione degli errori dello strumento di divisione della copia dati](data-box-disk-troubleshoot-data-copy.md).

Dopo aver completato la copia dei dati, è possibile passare alla convalida dei dati. Se è stato usato lo strumento di divisione della copia, ignorare la convalida perché viene eseguita dallo strumento stesso, quindi passare all'esercitazione successiva.


## <a name="validate-data"></a>Convalidare i dati

Se non si è usato lo strumento di divisione della copia per copiare i dati, sarà necessario convalidare i dati. Seguire questa procedura per verificare i dati.

1. Eseguire `DataBoxDiskValidation.cmd` per la convalida dei checksum nella cartella *DataBoxDiskImport* dell'unità.
    
    ![Output dello strumento di convalida di Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Scegliere l'opzione appropriata. **Si consiglia di convalidare sempre i file e generare i checksum selezionando l'opzione 2**. A seconda della dimensione dei dati, questo passaggio potrebbe richiedere del tempo. Dopo aver completato lo script, uscire dalla finestra di comando. Se si verificano errori durante la convalida e la generazione dei checksum, si riceverà una notifica e anche un collegamento ai log degli errori.

    ![Output del checksum](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Reimpostare lo strumento tra due esecuzioni.
    > - Usare l'opzione 1 nel caso di set di dati di grandi dimensioni che contengono file piccoli (~ KB). Con questa opzione viene eseguita solo la convalida dei file perché la generazione dei checksum può richiedere molto tempo e le prestazioni potrebbero risentirne negativamente.

3. Se si usano più dischi, eseguire il comando per ciascun disco.

Se si verificano errori durante la convalida, vedere la [risoluzione degli errori di convalida](data-box-disk-troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Copiare i dati sul Data Box Disk.
> * Verificare l'integrità dei dati.

Passare all'esercitazione successiva per informazioni su come restituire Data Box Disk e verificare il caricamento dei dati in Azure.

> [!div class="nextstepaction"]
> [Spedizione di Azure Data Box a Microsoft](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-disks"></a>Copiare i dati sui dischi

Eseguire la procedura seguente per connettersi e copiare i dati del computer in Data Box Disk.

1. Visualizzare il contenuto dell'unità sbloccata. L'elenco delle cartelle e delle sottocartelle create preventivamente nell'unità varia in base alle opzioni selezionate al momento dell'ordine di Data Box Disk.
2. Copiare i dati in cartelle che corrispondono al formato dei dati appropriato. Ad esempio, copiare i dati non strutturati nella cartella *BlockBlob*, i dati VHD o VHDX nella cartella *PageBlob* e i file in *AzureFile*. Se il formato dei dati non corrisponde alla cartella appropriata (tipo di archiviazione), in un passaggio successivo il caricamento dei dati in Azure avrà negativo.

    - Viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nelle cartelle BlockBlob e PageBlob. Tutti i file nelle cartelle *BlockBlob* e *PageBlob* vengono copiati in un contenitore predefinito $root nell'account di archiviazione di Azure. 
    - Tutti i file inclusi nel contenitore $root vengono sempre caricati come BLOB in blocchi.
    - Copiare i file in una cartella all'interno della cartella *AzureFile*. Una sottocartella all'interno di *AzureFile* determina la creazione di una condivisione file. I file copiati direttamente nella cartella *AzureFile* hanno esito negativo e vengono caricati come BLOB in blocchi.
    - Se nella directory radice esistono file e cartelle, è necessario spostarli in un'altra cartella prima di iniziare la copia dei dati.
    - Se una delle destinazioni di archiviazione dell'ordine è Managed Disks, vedere le convenzioni di denominazione per i [dischi gestiti](data-box-disk-limits.md#managed-disk-naming-conventions).

    > [!IMPORTANT]
    > Tutti i contenitori, i BLOB e i file devono essere conformi alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) e ai [limiti di dimensioni degli oggetti di Azure](data-box-disk-limits.md#azure-object-size-limits). Se queste regole e limitazioni non vengono rispettate, il caricamento di dati in Azure avrà esito negativo.

3. Per copiare i dati, usare il trascinamento della selezione con Esplora file o qualsiasi strumento di copia file compatibile con SMB, ad esempio Robocopy. È possibile avviare più processi di copia usando il comando seguente:

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. Aprire la cartella di destinazione per visualizzare e verificare i file copiati. In caso di errori durante il processo di copia, scaricare i file di log per la risoluzione dei problemi. I file di log si trovano nella posizione specificata nel comando Robocopy.

Usare la procedura facoltativa di [divisione e copia](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) se si usano più dischi ed è presente un set di dati di grandi dimensioni che è necessario dividere e copiare tra tutti i dischi.

## <a name="validate-data"></a>Convalidare i dati

Per verificare i dati, eseguire la procedura seguente.

1. Eseguire `DataBoxDiskValidation.cmd` per la convalida dei checksum nella cartella *DataBoxDiskImport* dell'unità.
2. Usare l'opzione 2 per convalidare i file e generare i checksum. A seconda della dimensione dei dati, questo passaggio potrebbe richiedere del tempo. Se si verificano errori durante la convalida e la generazione dei checksum, si riceverà una notifica e anche un collegamento ai log degli errori.

    Se si verificano errori durante la convalida, vedere la [risoluzione degli errori di convalida](data-box-disk-troubleshoot.md).

::: zone-end

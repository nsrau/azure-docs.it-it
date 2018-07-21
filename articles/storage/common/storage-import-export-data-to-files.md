---
title: Uso di Importazione/Esportazione di Azure per trasferire dati in File di Azure | Microsoft Docs
description: Informazioni su come creare processi di importazione nel portale di Azure per trasferire dati in File di Azure.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.openlocfilehash: 4f48097fa1ece66dd9e20a7a7939ac43cb0f48b4
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113478"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usare il servizio Importazione/Esportazione di Azure per importare i dati in File di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per importare in tutta sicurezza grandi quantità di dati in File di Azure. Per importare dati, il servizio richiede la spedizione di unità disco supportate contenenti i dati a un data center di Azure.  

Il servizio Importazione/Esportazione supporta solo l'importazione di dati di File di Azure in Archiviazione di Azure. L'esportazione di dati di File di Azure non è supportata.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di importazione per trasferire dati in File di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti. È necessario:

- Avere una sottoscrizione di Azure attiva da usare con il servizio Importazione/Esportazione.
- Avere almeno un account di archiviazione di Azure. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md). Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).
- Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks). 
- Predisporre un sistema Windows con una [versione del sistema operativo supportata](storage-import-export-requirements.md#supported-operating-systems).
- [Scaricare WAImportExport versione 2](https://www.microsoft.com/download/details.aspx?id=55280) nel sistema Windows. Decomprimere la cartella predefinita `waimportexport`. Ad esempio, `C:\WaImportExport`.
- Avere un account FedEx o DHL. 
    - L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
    - Generare un numero di tracciabilità per il processo di esportazione.
    - Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
    - Se non si dispone di un account del vettore, passare a:
        - [Creare un account FedEX](https://www.fedex.com/en-us/create-account.html), o 
        - [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Passaggio 1: Preparare le unità

Questo passaggio genera un file journal. Il file journal archivia le informazioni di base, come numero di serie delle unità, chiave di crittografia e dettagli relativi all'account di archiviazione.

Per preparare le unità, eseguire le operazioni seguenti.

1. Connettere le unità disco al sistema Windows tramite connettori SATA.
2. Creare un singolo volume NTFS in ogni unità. Assegnare una lettera di unità al volume. Non usare punti di montaggio.
3. Modificare il file *dataset.csv* nella cartella radice in cui si trova lo strumento. A seconda che si voglia importare un file o una cartella oppure entrambi, aggiungere nel file *dataset.csv* voci simili a quelle mostrate negli esempi seguenti.  

    - **Per importare un file**: nell'esempio seguente i dati da copiare si trovano nell'unità C:. Il file *MyFile1.txt* viene copiato nella radice *MyAzureFileshare1*. Se *MyAzureFileshare1* non esiste, viene creata nell'account di archiviazione di Azure. La struttura di cartelle viene mantenuta.

        ```
            BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
        ```
    - **Per importare una cartella**: tutti i file e le cartelle all'interno di *MyFolder2* vengono copiati in modo ricorsivo nella condivisione file. La struttura di cartelle viene mantenuta.

        ```
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
        ```
    Nello stesso file è possibile creare più voci, che corrispondono alle cartelle o ai file importati. 

        ```
            "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
            "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            "F:\MyFolder3\MyFile3.txt","MyAzureFileshare2/",file,rename,"None",None 
            
        ```
    Ulteriori informazioni su [come preparare un file CSV del set di dati](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Modificare il file *driveset.csv* nella cartella radice in cui si trova lo strumento. Aggiungere nel file *driveset.csv* voci simili a quelle mostrate negli esempi seguenti. Il file driveset include l'elenco dei dischi e delle lettere di unità corrispondenti in modo che lo strumento possa individuare correttamente l'elenco dei dischi da preparare.

    Questo esempio presuppone che siano collegati due dischi e che vengano creati i volumi NTFS di base G:\ e H:\. H:\ non è crittografato, mentre G:\ è già crittografato. Lo strumento formatta e crittografa solo il disco che ospita H:\ (e non G:\).

    - **Per un disco non crittografato**: specificare *Crittografa* per abilitare la crittografia BitLocker nel disco.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        H,Format,SilentMode,Encrypt,
        ```
    
    - **Per un disco già crittografato**: specificare *AlreadyEncrypted* e immettere la chiave BitLocker.

        ```
        DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
        G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
        ```

    Nello stesso file è possibile creare più voci, corrispondenti a più unità. Ulteriori informazioni su [come preparare un file CSV del driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5.  Usare l'opzione `PrepImport` per copiare e preparare i dati nell'unità disco. Per la prima sessione di copia per copiare directory e/o file con una nuova sessione di copia, eseguire il comando seguente:

        ```
        .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
        ```

    Di seguito viene mostrato un esempio di importazione.
  
        ```
        .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
        ```
 
6. Per ogni esecuzione della riga di comando, viene creato un file journal con il nome specificato con il parametro `/j:`. Ogni unità preparata ha un file journal che deve essere caricato quando si crea il processo di importazione. Le unità prive di file journal non vengono elaborate.

    > [!IMPORTANT]
    > - Non modificare i dati nelle unità disco o nel file journal dopo aver completato la preparazione dei dischi.

Per altri esempi, passare a [Esempi per i file journal](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Passaggio 2: Creare un processo di importazione 

Per creare un processo di importazione nel portale di Azure, eseguire le operazioni seguenti.
1. Accedere all'indirizzo https://portal.azure.com/.
2. Passare a **Tutti i servizi > Archiviazione > Processi di importazione/esportazione**. 

    ![Passare a Processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Fare clic su **Crea Processi di importazione/esportazione**.

    ![Fare clic su Processo di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **Nozioni di base**:

    - Selezionare **Importa in Azure**.
    - Immettere un nome descrittivo per il processo di importazione. Usare questo nome per tenere traccia dei processi mentre sono in corso e una volta completati.
        -  Questo nome può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura.
        -  Il nome deve iniziare con una lettera e non può contenere spazi. 
    - Selezionare una sottoscrizione.
    - Selezionare un gruppo di risorse. 

        ![Creare il processo di importazione - Passaggio 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **Dettagli processo**:
    
    - Caricare i file journal creati in [Passaggio 1: Preparare le unità](#step-1-prepare-the-drives). 
    - Selezionare l'account di archiviazione in cui verranno importati i dati. 
    - La località di consegna viene immessa automaticamente in base all'area dell'account di archiviazione selezionato.
   
       ![Creare il processo di importazione - Passaggio 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **Informazioni sul mittente della spedizione**:

    - Selezionare il vettore nell'elenco a discesa.
    - Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. 
    - Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area.

        > [!TIP] 
        > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

       ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. In **Riepilogo**:

    - Fornire l'indirizzo di spedizione del data center di Azure per rispedire i dischi ad Azure. Verificare che il nome del processo e l'indirizzo completo siano riportati sull'etichetta per la spedizione.
    - Fare clic su **OK** per completare la creazione del processo di importazione.

        ![Creare il processo di importazione - Passaggio 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Passaggio 3: Spedire le unità al data center di Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Passaggio 4: Aggiornare il processo con informazioni di tracciabilità della spedizione

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passaggio 5: Verificare il caricamento dei dati in Azure

Tracciare il processo fino al completamento. Una volta completato il processo, verificare che i dati siano caricati in Azure. Eliminare i dati locali solo dopo aver verificato che il caricamento ha avuto esito positivo.

## <a name="samples-for-journal-files"></a>Esempi per i file journal

Per **aggiungere altre unità**, creare un nuovo file driveset ed eseguire il comando come indicato di seguito. 

Per le sessioni di copia successive in unità disco diverse rispetto a quelle specificate nel file *InitialDriveset.csv*, specificare un nuovo file *CSV* driveset e immetterlo come valore per il parametro `AdditionalDriveSet`. Usare il **medesimo file journal** e indicare un **nuovo ID di sessione**. Il formato del file CSV AdditionalDriveset corrisponde a quello del file InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Di seguito viene mostrato un esempio di importazione.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Per aggiungere altri dati allo stesso file driveset, usare il comando PrepImport per le sessioni di copia successive per copiare altri file o directory.

Per le sessioni di copia successive negli stessi dischi rigidi specificati nel file *InitialDriveset.csv*, immettere lo **stesso nome di file journal** e un **nuovo ID sessione**. Non è necessario fornire la chiave dell'account di archiviazione.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Di seguito viene mostrato un esempio di importazione.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)



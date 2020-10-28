---
title: Uso di Importazione/Esportazione di Azure per trasferire dati in File di Azure | Microsoft Docs
description: Informazioni su come creare processi di importazione nel portale di Azure per trasferire dati in File di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 1cd1145411fbf4ec4441d612f9552997704f9e5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782400"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usare il servizio Importazione/Esportazione di Azure per importare i dati in File di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per importare in tutta sicurezza grandi quantità di dati in File di Azure. Per importare dati, il servizio richiede la spedizione di unità disco supportate contenenti i dati a un data center di Azure.  

Il servizio Importazione/Esportazione supporta solo l'importazione di dati di File di Azure in Archiviazione di Azure. L'esportazione di dati di File di Azure non è supportata.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di importazione per trasferire dati in File di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti. È necessario:

- Avere una sottoscrizione di Azure attiva da usare con il servizio Importazione/Esportazione.
- Avere almeno un account di archiviazione di Azure. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md). Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-account-create.md).
- Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks).
- Predisporre un sistema Windows con una [versione del sistema operativo supportata](storage-import-export-requirements.md#supported-operating-systems).
- [Scaricare WAImportExport versione 2](https://aka.ms/waiev2) nel sistema Windows. Decomprimere la cartella predefinita `waimportexport`. Ad esempio, `C:\WaImportExport`
- Avere un account FedEx o DHL. Se si vuole usare un vettore diverso da FedEx/DHL, contattare Azure Data Box team operativo all'indirizzo `adbops@microsoft.com` .  
    - L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
    - Generare un numero di tracciabilità per il processo di esportazione.
    - Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
    - Se non si dispone di un account del vettore, passare a:
        - [Creare un account FedEx](https://www.fedex.com/en-us/create-account.html)o
        - [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Passaggio 1: Preparare le unità

Questo passaggio genera un file journal. Il file journal archivia le informazioni di base, come numero di serie delle unità, chiave di crittografia e dettagli relativi all'account di archiviazione.

Per preparare le unità, eseguire le operazioni seguenti.

1. Connettere le unità disco al sistema Windows tramite connettori SATA.
2. Creare un singolo volume NTFS in ogni unità. Assegnare una lettera di unità al volume. Non usare punti di montaggio.
3. Modificare il file *dataset.csv* nella cartella radice in cui si trova lo strumento. A seconda che si voglia importare un file o una cartella oppure entrambi, aggiungere nel file *dataset.csv* voci simili a quelle mostrate negli esempi seguenti.  

   - **Per importare un file** : nell'esempio seguente, i dati da copiare si trovano nell'unità F:. Il file *MyFile1.txt* viene copiato nella radice *MyAzureFileshare1* . Se *MyAzureFileshare1* non esiste, viene creata nell'account di archiviazione di Azure. La struttura di cartelle viene mantenuta.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Per importare una cartella** : tutti i file e le cartelle all'interno di *MyFolder2* vengono copiati in modo ricorsivo nella condivisione file. La struttura di cartelle viene mantenuta.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Nello stesso file è possibile creare più voci, che corrispondono alle cartelle o ai file importati.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Ulteriori informazioni su [come preparare un file CSV del set di dati](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Modificare il file *driveset.csv* nella cartella radice in cui si trova lo strumento. Aggiungere nel file *driveset.csv* voci simili a quelle mostrate negli esempi seguenti. Il file driveset include l'elenco dei dischi e delle lettere di unità corrispondenti in modo che lo strumento possa individuare correttamente l'elenco dei dischi da preparare.

    Questo esempio presuppone che siano collegati due dischi e che vengano creati i volumi NTFS di base G:\ e H:\. H:\ non è crittografato, mentre G:\ è già crittografato. Lo strumento formatta e crittografa solo il disco che ospita H:\ (e non G:\).

   - **Per un disco non crittografato** : specificare *Crittografa* per abilitare la crittografia BitLocker nel disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Per un disco già crittografato** : specificare *AlreadyEncrypted* e immettere la chiave BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Nello stesso file è possibile creare più voci, corrispondenti a più unità. Ulteriori informazioni su [come preparare un file CSV del driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Usare l'opzione `PrepImport` per copiare e preparare i dati nell'unità disco. Per la prima sessione di copia per copiare directory e/o file con una nuova sessione di copia, eseguire il comando seguente:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

   Di seguito viene mostrato un esempio di importazione.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Per ogni esecuzione della riga di comando, viene creato un file journal con il nome specificato con il parametro `/j:`. Ogni unità preparata ha un file journal che deve essere caricato quando si crea il processo di importazione. Le unità prive di file journal non vengono elaborate.

    > [!IMPORTANT]
    > - Non modificare i dati nelle unità disco o nel file journal dopo aver completato la preparazione dei dischi.

Per altri esempi, passare a [Esempi per i file journal](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Passaggio 2: Creare un processo di importazione

### <a name="portal"></a>[Portale](#tab/azure-portal)

Per creare un processo di importazione nel portale di Azure, eseguire le operazioni seguenti.
1. Accedere all'indirizzo https://portal.azure.com/.
2. Passare a **Tutti i servizi > Archiviazione > Processi di importazione/esportazione** .

    ![Passare a Processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Fare clic su **Crea processo di importazione/esportazione** .

    ![Fare clic su Processo di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **Nozioni di base** :

    - Selezionare **Importa in Azure** .
    - Immettere un nome descrittivo per il processo di importazione. Usare questo nome per tenere traccia dei processi mentre sono in corso e una volta completati.
        -  Questo nome può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura.
        -  Il nome deve iniziare con una lettera e non può contenere spazi.
    - Selezionare una sottoscrizione.
    - Selezionare un gruppo di risorse.

        ![Creare il processo di importazione - Passaggio 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **Dettagli processo** :

    - Caricare i file journal creati in [Passaggio 1: Preparare le unità](#step-1-prepare-the-drives).
    - Selezionare l'account di archiviazione in cui verranno importati i dati.
    - La località di consegna viene popolata automaticamente in base all'area dell'account di archiviazione selezionato.

       ![Creare il processo di importazione - Passaggio 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **Informazioni sul mittente della spedizione** :

    - Selezionare il vettore nell'elenco a discesa. Se si vuole usare un vettore diverso da FedEx/DHL, scegliere un'opzione esistente nell'elenco a discesa. Contattare Azure Data Box team operativo in `adbops@microsoft.com`  con le informazioni relative al vettore che si intende usare.
    - Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione.
    - Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area.

        > [!TIP]
        > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

       ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. In **Riepilogo** :

    - Fornire l'indirizzo di spedizione del data center di Azure per rispedire i dischi ad Azure. Verificare che il nome del processo e l'indirizzo completo siano riportati sull'etichetta per la spedizione.
    - Fare clic su **OK** per completare la creazione del processo di importazione.

        ![Creare il processo di importazione - Passaggio 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare la procedura seguente per creare un processo di importazione nell'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Creare un processo

1. Usare il comando [AZ Extension Add](/cli/azure/extension#az_extension_add) per aggiungere l'estensione [AZ Import-Export](/cli/azure/ext/import-export/import-export) :

    ```azurecli
    az extension add --name import-export
    ```

1. È possibile usare un gruppo di risorse esistente o crearne uno. Per creare un gruppo di risorse, eseguire il comando [AZ Group create](/cli/azure/group#az_group_create) :

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. È possibile usare un account di archiviazione esistente o crearne uno. Per creare un account di archiviazione, eseguire il comando [AZ storage account create](/cli/azure/storage/account#az_storage_account_create) :

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Per ottenere un elenco delle posizioni in cui è possibile spedire i dischi, usare il comando [AZ Import-Export Location list](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list) :

    ```azurecli
    az import-export location list
    ```

1. Usare il comando [AZ Import-Export Location Show](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) per ottenere i percorsi per l'area geografica:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Eseguire il comando [AZ Import-Export create](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) seguente per creare un processo di importazione:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.


1. Usare il comando [AZ Import-Export List](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) per visualizzare tutti i processi per il gruppo di risorse myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Per aggiornare il processo o annullare il processo, eseguire il comando [AZ Import-Export Update](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update) :

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Passaggio 3: Spedire le unità al data center di Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Passaggio 4: Aggiornare il processo con informazioni di tracciabilità della spedizione

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passaggio 5: Verificare il caricamento dei dati in Azure

Tracciare il processo fino al completamento. Una volta completato il processo, verificare che i dati siano caricati in Azure. Eliminare i dati locali solo dopo aver verificato che il caricamento ha avuto esito positivo.

## <a name="samples-for-journal-files"></a>Esempi per i file journal

Per **aggiungere altre unità** , creare un nuovo file driveset ed eseguire il comando come indicato di seguito.

Per le sessioni di copia successive in unità disco diverse rispetto a quelle specificate nel file *InitialDriveset.csv* , specificare un nuovo file *CSV* driveset e immetterlo come valore per il parametro `AdditionalDriveSet`. Usare il **medesimo file journal** e indicare un **nuovo ID di sessione** . Il formato del file CSV AdditionalDriveset corrisponde a quello del file InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Di seguito viene mostrato un esempio di importazione.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Per aggiungere altri dati allo stesso file driveset, usare il comando PrepImport per le sessioni di copia successive per copiare altri file o directory.

Per le sessioni di copia successive negli stessi dischi rigidi specificati nel file *InitialDriveset.csv* , immettere lo **stesso nome di file journal** e un **nuovo ID sessione** . Non è necessario fornire la chiave dell'account di archiviazione.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Di seguito viene mostrato un esempio di importazione.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)
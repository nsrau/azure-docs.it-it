---
title: Uso di Importazione/Esportazione di Azure per trasferire dati in BLOB di Azure | Microsoft Docs
description: Informazioni su come creare processi di importazione ed esportazione nel portale di Azure per trasferire dati da e verso BLOB di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 06/06/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 72a91fefc26e9c0b6d5a91223119815c4fcb9551
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808591"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usare il servizio Importazione/Esportazione di Azure per trasferire dati in Archiviazione BLOB di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per importare in tutta sicurezza grandi quantità di dati nell'archiviazione BLOB di Azure. Per importare dati in BLOB di Azure, il servizio richiede la spedizione di unità disco crittografate contenenti i dati a un data center di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di importazione per trasferire dati in Archiviazione BLOB di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti per questo servizio. È necessario:

- Avere una sottoscrizione di Azure attiva che possa essere usata per il servizio Importazione/Esportazione.
- Avere almeno un account di archiviazione di Azure con un contenitore di archiviazione. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md). 
    - Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-quickstart-create-account.md). 
    - Per informazioni sul contenitore di archiviazione, passare a [Creare un contenitore di archiviazione](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
- Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks). 
- Predisporre un sistema Windows con una [versione del sistema operativo supportata](storage-import-export-requirements.md#supported-operating-systems). 
- Abilitare BitLocker nel sistema Windows. Vedere [How to enable BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker).
- [Scaricare WAImportExport versione 1](https://aka.ms/waiev1) nel sistema Windows. Decomprimere la cartella predefinita `waimportexportv1`. Ad esempio: `C:\WaImportExportV1`.
- Avere un account FedEx o DHL. Se si desidera usare un operatore diverso da FedEx o DHL, contattare il team di finestra operazioni sui dati di Azure all'indirizzo `adbops@microsoft.com`.  
    - L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
    - Generare un numero di tracciabilità per il processo di esportazione.
    - Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
    - Se non si dispone di un account del vettore, passare a:
        - [Creare un account FedEX](https://www.fedex.com/en-us/create-account.html), o 
        - [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passaggio 1: Preparare le unità

Questo passaggio genera un file journal. Il file journal archivia le informazioni di base, come numero di serie delle unità, chiave di crittografia e dettagli relativi all'account di archiviazione. 

Per preparare le unità, eseguire le operazioni seguenti.

1.  Connettere le unità disco al sistema Windows tramite connettori SATA.
1.  Creare un singolo volume NTFS in ogni unità. Assegnare una lettera di unità al volume. Non usare punti di montaggio.
2.  Abilitare la crittografia BitLocker nel volume NTFS. Se si usa un sistema Windows Server, seguire le istruzioni fornite in [How to enable BitLocker on Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker in Windows Server 2012 R2).
3.  Copiare i dati nel volume crittografato. Usare il trascinamento della selezione o Robocopy o uno strumento di copia simile.
4.  Aprire una finestra di PowerShell o della riga di comando con privilegi amministrativi. Per passare alla directory della cartella decompressa, eseguire il comando seguente:
    
    `cd C:\WaImportExportV1`
5.  Per ottenere la chiave BitLocker dell'unità, eseguire il comando seguente:
    
    `manage-bde -protectors -get <DriveLetter>:`
6.  Per preparare il disco, eseguire il comando seguente. **A seconda delle dimensioni dei dati, l'operazione può richiedere da diverse ore a più giorni.** 

    ```
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite 
    ```
    Viene creato un file journal nella stessa cartella in cui è stato eseguito lo strumento. Vengono creati altri due file, un file *XML* (cartella in cui è stato eseguito lo strumento) e un file *drive-manifest.xml* (cartella in cui si trovano i dati).
    
    I parametri usati vengono descritti nella tabella seguente:

    |Opzione  |Descrizione  |
    |---------|---------|
    |/j:     |Nome del file journal, con estensione jrn. Viene generato un file journal per ogni unità. È consigliabile usare il numero di serie del disco come nome del file journal.         |
    |/id:     |ID sessione. Usare un numero di sessione univoco per ogni istanza del comando.      |
    |/t:     |Lettera di unità del disco da spedire. Ad esempio, l'unità `D`.         |
    |/bk:     |Chiave di BitLocker per l'unità. La sua password numerica dall'output di `manage-bde -protectors -get D:`      |
    |/srcdir:     |Lettera di unità del disco da spedire seguita da `:\`. Ad esempio: `D:\`.         |
    |/dstdir:     |Nome del contenitore di destinazione in Archiviazione di Azure.         |
    |/blobtype:     |Questa opzione specifica il tipo di blob che si desidera importare i dati. Per i BLOB in blocchi, si tratta `BlockBlob` e per i BLOB di pagine, è `PagaBlob`.         |
    |/skipwrite:     |Opzione che specifica che non sono presenti nuovi dati da copiare e che è necessario preparare i dati esistenti nel disco.          |
    |/enablecontentmd5:     |Se abilitata, l'opzione assicura che MD5 viene calcolato e impostare come `Content-md5` proprietà in ogni blob. Usare questa opzione solo se si desidera utilizzare il `Content-md5` campo dopo che i dati vengono caricati in Azure. <br> Questa opzione non riguarda il controllo di integrità dei dati (che si verifica per impostazione predefinita). L'impostazione di aumentare il tempo impiegato per il caricamento dei dati nel cloud.          |
7. Ripetere il passaggio precedente per ogni disco che deve essere spedito. Viene creato un file journal con il nome specificato per ogni esecuzione della riga di comando.
    
    > [!IMPORTANT]
    > - Insieme al file journal, viene creato anche un file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` nella stessa cartella in cui si trova lo strumento. Il file XML viene usato al posto del file journal durante la creazione di un processo se il file journal è troppo grande. 

## <a name="step-2-create-an-import-job"></a>Passaggio 2: Creare un processo di importazione

Per creare un processo di importazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere all'indirizzo https://portal.azure.com/.
2. Passare a **Tutti i servizi > Archiviazione > Processi di importazione/esportazione**. 
    
    ![Passare a Processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Fare clic su **Crea Processi di importazione/esportazione**.

    ![Fare clic su Crea processo di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **Nozioni di base**:

   - Selezionare **Importa in Azure**.
   - Immettere un nome descrittivo per il processo di importazione. Usare il nome per tenere traccia dello stato dei processi.
       - Il nome può contenere solo lettere minuscole, numeri e segni meno.
       - Il nome deve iniziare con una lettera e non può contenere spazi.
   - Selezionare una sottoscrizione.
   - Immettere o selezionare un gruppo di risorse.  

     ![Creare il processo di importazione - Passaggio 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. In **Dettagli processo**:

    - Caricare i file journal delle unità ottenuti durante il passaggio di preparazione delle unità. Se è stato usato `waimportexport.exe version1`, caricare un file per ogni unità preparata. Se le dimensioni del file journal superano 2 MB, è possibile usare il file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` creato insieme al file journal. 
    - Selezionare l'account di archiviazione di destinazione in cui si troveranno i dati. 
    - La località di consegna viene popolata automaticamente in base all'area dell'account di archiviazione selezionato.
   
   ![Creare il processo di importazione - Passaggio 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. In **Informazioni sul mittente della spedizione**:

   - Selezionare il vettore nell'elenco a discesa. Se si desidera usare un operatore diverso da FedEx o DHL, scegliere un'opzione esistente dall'elenco a discesa. Finestra operazioni dei dati di contatto Azure team a `adbops@microsoft.com` con le informazioni riguardanti il corriere si intende usare.
   - Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. In assenza di un numero di account, creare un account di vettore [FedEx](https://www.fedex.com/us/oadr/) o [DHL](https://www.dhl.com/).
   - Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area. 
        
       > [!TIP] 
       > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

     ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)
   
5. In **Riepilogo**:

   - Esaminare le informazioni sul processo fornite nel riepilogo. Prendere nota del nome del processo e dell'indirizzo di spedizione del data center di Azure per rispedire i dischi ad Azure. Queste informazioni verranno usate successivamente sull'etichetta indirizzo.
   - Fare clic su **OK** per creare il processo di importazione.

     ![Creare il processo di importazione - Passaggio 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives"></a>Passaggio 3: Spedire le unità 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]


## <a name="step-4-update-the-job-with-tracking-information"></a>Passaggio 4: Aggiornare il processo con informazioni di verifica

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passaggio 5: Verificare il caricamento dei dati in Azure

Tracciare il processo fino al completamento. Una volta completato il processo, verificare che i dati siano caricati in Azure. Eliminare i dati locali solo dopo aver verificato che il caricamento ha avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)



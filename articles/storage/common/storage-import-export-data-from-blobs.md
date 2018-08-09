---
title: Uso di Importazione/Esportazione di Azure per esportare dati da BLOB di Azure | Microsoft Docs
description: Informazioni su come creare processi di esportazione nel portale di Azure per trasferire dati da BLOB di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 07/17/2018
ms.author: alkohli
ms.component: common
ms.openlocfilehash: a7456bedeb6bc870ad5d6892fe697fb2dfcd3bc1
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527805"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Usare il servizio Importazione/Esportazione di Azure per esportare dati da Archiviazione BLOB di Azure
Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per esportare in tutta sicurezza grandi quantità di dati da Archiviazione BLOB di Azure. Il servizio richiede la spedizione di unità vuote al data center di Azure. Il servizio esporta i dati dall'account di archiviazione alle unità e quindi rispedisce le unità.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di esportazione per trasferire dati da Archiviazione BLOB di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti per questo servizio. È necessario:

- Avere una sottoscrizione di Azure attiva che possa essere usata per il servizio Importazione/Esportazione.
- Avere almeno un account di archiviazione di Azure. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md). Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).
- Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks).
- Avere un account FedEx o DHL.  
    - L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
    - Generare un numero di tracciabilità per il processo di esportazione.
    - Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati. 
    - Se non si dispone di un account del vettore, passare a:
        - [Creare un account FedEX](https://www.fedex.com/en-us/create-account.html), o 
        - [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Passaggio 1: Creare un processo di esportazione

Per creare un processo di esportazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere all'indirizzo https://portal.azure.com/.
2. Passare a **Tutti i servizi > Archiviazione > Processi di importazione/esportazione**. 

    ![Passare a Processi di importazione/esportazione](./media/storage-import-export-data-from-blobs/export-from-blob1.png)

3. Fare clic su **Crea Processi di importazione/esportazione**.

    ![Fare clic su Processo di importazione/esportazione](./media/storage-import-export-data-from-blobs/export-from-blob2.png)

4. In **Nozioni di base**:
    
    - Selezionare **Esporta da Azure**. 
    - Immettere un nome descrittivo per il processo di esportazione. Usare il nome scelto per tenere traccia dello stato dei processi. 
        - Il nome può contenere solo lettere minuscole, numeri, trattini e caratteri di sottolineatura.
        - Il nome deve iniziare con una lettera e non può contenere spazi. 
    - Selezionare una sottoscrizione.
    - Immettere o selezionare un gruppo di risorse.

        ![Nozioni di base](./media/storage-import-export-data-from-blobs/export-from-blob3.png) 
    
3. In **Dettagli processo**:

    - Selezionare l'account di archiviazione in cui si trovano i dati da esportare. Usare un account di archiviazione vicino rispetto a dove ci si trova.
    - La località di consegna viene immessa automaticamente in base all'area dell'account di archiviazione selezionato. 
    - Specificare i dati BLOB da esportare dall'account di archiviazione in una o più unità vuote. 
    - Scegliere **Esporta tutti** per i dati BLOB nell'account di archiviazione.
    
         ![Esporta tutti](./media/storage-import-export-data-from-blobs/export-from-blob4.png) 

    - È possibile specificare i contenitori e i BLOB da esportare.
        - **Per specificare un BLOB da esportare**: usare il selettore **Uguale a**. Specificare il percorso relativo del BLOB, iniziando con il nome del contenitore. Utilizzare *$root* per specificare il contenitore radice.
        - **Per specificare tutti i BLOB che iniziano con un prefisso**: usare il selettore **Inizia con**. Specificare il prefisso, iniziando con una barra '/'. Il prefisso può essere il prefisso del nome del contenitore, il nome del contenitore completo o il nome del contenitore completo seguito dal prefisso del nome BLOB. I percorsi BLOB devono essere specificati in un formato valido per evitare errori durante l'elaborazione, come mostrato in questo screenshot. Per altre informazioni, vedere [Esempi di percorsi BLOB validi](#examples-of-valid-blob-paths). 
   
           ![Esportare contenitori e BLOB selezionati](./media/storage-import-export-data-from-blobs/export-from-blob5.png) 

    - È possibile eseguire l'esportazione dal file elenco di BLOB.

        ![Esportare dal file elenco di BLOB](./media/storage-import-export-data-from-blobs/export-from-blob6.png)  
   
   > [!NOTE]
   > Se il BLOB da esportare è in uso durante la copia dei dati, il servizio Importazione/Esportazione di Azure acquisisce uno snapshot del BLOB e copia lo snapshot.
 

4. In **Informazioni sul mittente della spedizione**:

    - Selezionare il vettore nell'elenco a discesa.
    - Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. 
    - Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area.

        > [!TIP] 
        > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.
   
5. In **Riepilogo**:

    - Esaminare i dettagli del processo.
    - Annotare il nome del processo e le informazioni sul mittente della spedizione per spedire i dischi ad Azure. 

        > [!NOTE] 
        > Inviare sempre i dischi al data center indicato nel portale di Azure. Se i dischi vengono spediti al data center errato, non verrà elaborato il processo.

    - Fare clic su **OK** per completare la creazione del processo di esportazione.

## <a name="step-2-ship-the-drives"></a>Passaggio 2: Spedire le unità

In caso di dubbi sul numero di unità necessarie, passare a [Controllare il numero di unità](#check-the-number-of-drives). Se si conosce il numero di unità, procedere alla spedizione delle unità.

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Passaggio 3: Aggiornare il processo con informazioni di tracciabilità della spedizione

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]


## <a name="step-4-receive-the-disks"></a>Passaggio 4: Ricevere i dischi
Quando il dashboard segnala che il processo è completo, i dischi vengono spediti all'utente e il numero di tracciabilità per la spedizione è disponibile nel portale.

1. Dopo aver ricevuto le unità con i dati esportati, è necessario ottenere le chiavi BitLocker per sbloccare le unità. Passare al processo di esportazione nel portale di Azure. Fare clic sulla scheda **Importazione/Esportazione**. 
2. Selezionare e fare clic sul processo di esportazione nell'elenco. Passare a **Chiavi BitLocker** e copiare le chiavi.
   
   ![Visualizzare le chiavi BitLocker per il processo di esportazione](./media/storage-import-export-service/export-job-bitlocker-keys.png)

3. Usare le chiavi BitLocker per sbloccare i dischi.

L'esportazione è stata completata. A questo punto, è possibile eliminare il processo. Altrimenti, verrà eliminato automaticamente dopo 90 giorni.


## <a name="check-the-number-of-drives"></a>Controllare il numero di unità

Questo passaggio *facoltativo* aiuta a determinare il numero di unità necessarie per il processo di esportazione. Eseguire questo passaggio in un sistema Windows con una [versione supportata del sistema operativo](storage-import-export-requirements.md#supported-operating-systems).

1. [Scaricare WAImportExport versione 1](https://www.microsoft.com/en-us/download/details.aspx?id=42659) nel sistema Windows. 
2. Decomprimere la cartella predefinita `waimportexportv1`. Ad esempio: `C:\WaImportExportV1`.
3. Aprire una finestra di PowerShell o della riga di comando con privilegi amministrativi. Per passare alla directory della cartella decompressa, eseguire il comando seguente:
    
    `cd C:\WaImportExportV1`

4. Per controllare il numero di dischi necessari per i BLOB selezionati, eseguire il comando seguente:

    `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    I parametri vengono descritti nella tabella seguente:
    
    |Parametro della riga di comando|DESCRIZIONE|  
    |--------------------------|-----------------|  
    |**/logdir:**|facoltativo. Directory dei log. in cui vengono scritti file di log dettagliati. Se non è specificato, come directory dei log viene usata la directory corrente.|  
    |**/sn:**|Richiesto. Il nome dell'account di archiviazione per il processo di esportazione.|  
    |**/sk:**|Obbligatorio solo se non è specificata una firma di accesso condiviso del contenitore. Chiave dell'account per l'account di archiviazione per il processo di esportazione.|  
    |**/csas:**|Obbligatorio solo se non è specificata una chiave dell'account di archiviazione. Firma di accesso condiviso del contenitore per l'elenco dei BLOB da esportare nel processo di esportazione.|  
    |**/ExportBlobListFile:**|Richiesto. Percorso del file XML contenente l'elenco dei percorsi o dei prefissi dei percorsi BLOB per i BLOB da esportare. Formato di file usato nell'elemento `BlobListBlobPath` nell'operazione [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) dell'API REST del servizio Importazione/Esportazione.|  
    |**/DriveSize:**|Richiesto. Dimensione delle unità da usare per un processo di esportazione, *ad esempio* 500 GB o 1,5 TB.|  

    Vedere [Esempio di comando PreviewExport](#example-of-previewexport-command).
 
5. Controllare che sia possibile eseguire operazioni di lettura/scrittura nelle unità che verranno spedite per il processo di esportazione.

### <a name="example-of-previewexport-command"></a>Esempio di comando PreviewExport

L'esempio seguente illustra il comando `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
Il file dell'elenco del BLOB di esportazione può contenere nomi e prefissi BLOB, come illustrato di seguito:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

Lo strumento Importazione/Esportazione di Azure elenca tutti i BLOB da esportare e calcola come comprimerli in unità della dimensione specificata, tenendo conto dell'eventuale sovraccarico necessario, quindi stima il numero di unità richieste per contenere i BLOB e le informazioni sull'uso delle unità.  
  
Di seguito è riportato un esempio dell'output, senza log informativi:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```

## <a name="examples-of-valid-blob-paths"></a>Esempi di percorsi BLOB validi

La tabella seguente mostra alcuni esempi di percorsi BLOB validi:
   
   | Selettore | Percorso BLOB | DESCRIZIONE |
   | --- | --- | --- |
   | Starts With |/ |Esporta tutti i BLOB nell'account di archiviazione |
   | Starts With |/$root/ |Esporta tutti i BLOB nel contenitore radice |
   | Starts With |/book |Esporta tutti i BLOB in qualsiasi contenitore che inizia con il prefisso **book** |
   | Starts With |/music/ |Esporta tutti i BLOB nel contenitore **music** |
   | Starts With |/music/love |Esporta nel contenitore **music** tutti i BLOB che iniziano con il prefisso **love** |
   | Equal To |$root/logo.bmp |Esporta il BLOB **logo.bmp** nel contenitore radice |
   | Equal To |videos/story.mp4 |Esporta il BLOB **story.mp4** nel contenitore **video** |

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)



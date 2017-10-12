---
title: "Spostare dati con l'attività di copia | Microsoft Docs"
description: "Informazioni sullo spostamento di dati in pipeline di Data Factory: migrazione di dati tra archivi cloud e tra archivi locali e cloud. Usare l'attività di copia."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3334230097c3c0932fd732ff3f4e9c0aa35dbcc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-by-using-copy-activity"></a>Spostare dati con l'attività di copia
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-data-movement-activities.md)
> * [Versione 2 - Anteprima](../copy-activity-overview.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Data Factory, che è disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Attività di copia nella versione 2](../copy-activity-overview.md).

## <a name="overview"></a>Panoramica
In Azure Data Factory è possibile usare l'attività di copia per copiare i dati da archivi dati locali e cloud. Dopo la copia, è possibile trasformare o analizzare ulteriormente i dati. L'attività di copia può essere usata anche per pubblicare risultati di trasformazione e analisi per Business Intelligence (BI) e l'uso delle applicazioni.

![Ruolo dell'attività di copia](media/data-factory-data-movement-activities/copy-activity.png)

L'attività di copia è un servizio [disponibile a livello globale](#global)sicuro, affidabile e scalabile. Questo articolo fornisce informazioni dettagliate sullo spostamento dei dati in Data factory e sull'attività di copia.

Prima di tutto, verrà illustrato come avviene la migrazione dei dati tra due archivi dati cloud e tra un archivio dati locale e un archivio dati cloud.

> [!NOTE]
> Per informazioni sulle attività in generale, vedere l'articolo [Pipeline e attività in Azure Data Factory](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiare dati tra due archivi dati cloud
Nei casi in cui sia l'archivio dati di origine che l'archivio dati sink si trovano nel cloud, l'attività di copia viene sottoposta alle fasi seguenti per copiare i dati dall'origine al sink. Il servizio su cui si basa l'attività di copia:

1. Legge i dati dall'archivio dati di origine.
2. Esegue la serializzazione/deserializzazione, compressione/decompressione, il mapping di colonne e la conversione dei tipi. Esegue tali operazioni sulla base delle configurazioni del set di dati di input, del set di dati di output e dell'attività di copia.
3. Scrive i dati nell'archivio dati di destinazione.

Il servizio sceglie automaticamente l'area ottimale per eseguire lo spostamento dei dati. Questa area è in genere quella più vicino all'archivio dati sink.

![Copia da cloud a cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiare dati tra un archivio dati locale e un archivio dati cloud
Per spostare in modo sicuro i dati tra gli archivi dati locali e un archivio dati cloud, è necessario installare il gateway di gestione dati sul computer locale. Il Gateway di gestione dati è un agente che consente lo spostamento e l'elaborazione ibridi dei dati. Il Gateway di gestione dati può essere installato sullo stesso computer dell'archivio dati o su un computer separato che è in grado di accedere all'archivio dati.

In questo scenario, il Gateway di gestione dati esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo. I dati non transitano attraverso il servizio Data Factory di Azure. Gateway di gestione dati scrive direttamente i dati nell'archivio di destinazione.

![Copia da locale a cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Per un'introduzione e una procedura dettagliata, vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) . Per informazioni dettagliate sull'agente, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md) .

È anche possibile spostare dati da/da archivi dati supportati ospitati su macchine virtuali IaaS di Azure tramite il Gateway di gestione dati. In questo caso, è possibile installare il Gateway di gestione dati sulla stessa macchina virtuale dell'archivio dati o su una macchina virtuale separata con accesso all'archivio dati.

## <a name="supported-data-stores-and-formats"></a>Archivi dati e formati supportati
L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Data Factory supporta gli archivi dati seguenti. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

> [!NOTE] 
> Per spostare dati da e verso un archivio dati che non è supportato dall'attività di copia, usare l' **attività personalizzata** in Data Factory con la logica personalizzata per copiare o spostare i dati. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gli archivi dati contrassegnati da un asterisco (*) possono essere locali o in IaaS di Azure e richiederanno l'installazione del [Gateway di gestione dati](data-factory-data-management-gateway.md) in un computer IaaS locale o in Azure.

### <a name="supported-file-formats"></a>Formati di file supportati
È possibile usare l'attività di copia per **copiare i file così come sono** tra due archivi di dati basati su file ed è possibile saltare la [sezione formato](data-factory-create-datasets.md) sia nella definizione del set di dati di input che in quello di output. I dati vengono copiati in modo efficiente senza serializzazione/deserializzazione.

L'attività di copia esegue anche operazioni di lettura e scrittura in formati specifici, ovvero **Text, JSON, Avro, ORC e Parquet** e sono supportati i codec di compressione **GZip, Deflate, BZip2 e ZipDeflate**. Vedere [Formati di compressione e file supportati](data-factory-supported-file-and-compression-formats.md) per i dettagli.

È possibile ad esempio eseguire queste attività di copia:

* Copiare i dati nell'istanza locale di SQL Server e scrivere in Azure Data Lake Store nel formato ORC.
* Copiare i file in formato testo (CSV) dal file system locale e scrivere nel BLOB di Azure nel formato Avro.
* Copiare i file compressi dal file system locale e decomprimerli in Azure Data Lake Store.
* Copiare i dati in formato testo (CSV) con compressione GZip dal BLOB di Azure e scrivere nel database SQL Azure.

## <a name="global"></a>Spostamento dei dati disponibile a livello globale
Azure Data Factory è disponibile solo nelle seguenti aree: Stati Uniti occidentali, Stati Uniti orientali ed Europa settentrionale. Tuttavia, il servizio alla base dell'attività di copia è disponibile a livello globale nelle aree geografiche seguenti. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Per la disponibilità del servizio Data Factory e lo spostamento dei dati in un'area, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services) .

### <a name="copy-data-between-cloud-data-stores"></a>Copiare dati tra archivi dati cloud
Quando sia gli archivi dati di origine che gli archivi dati sink risiedono nel cloud, Data Factory usa una distribuzione del servizio nell'area più vicina al sink nella stessa area geografica per spostare i dati. Consultare la tabella seguente per il mapping:

| Geografia degli archivi dati di destinazione | Area dell'archivio dati di destinazione | Area usata per lo spostamento dei dati |
|:--- |:--- |:--- |
| Stati Uniti | Stati Uniti orientali | Stati Uniti orientali |
| &nbsp; | Stati Uniti orientali 2 | Stati Uniti orientali 2 |
| &nbsp; | Stati Uniti centrali | Stati Uniti centrali |
| &nbsp; | Stati Uniti centro-settentrionali | Stati Uniti centro-settentrionali |
| &nbsp; | Stati Uniti centro-meridionali | Stati Uniti centro-meridionali |
| &nbsp; | Stati Uniti centro-occidentali | Stati Uniti centro-occidentali |
| &nbsp; | Stati Uniti occidentali | Stati Uniti occidentali |
| &nbsp; | Stati Uniti occidentali 2 | Stati Uniti occidentali |
| Canada | Canada orientale | Canada centrale |
| &nbsp; | Canada centrale | Canada centrale |
| Brasile | Brasile meridionale | Brasile meridionale |
| Europa | Europa settentrionale | Europa settentrionale |
| &nbsp; | Europa occidentale | Europa occidentale |
| Regno Unito | Regno Unito occidentale | Regno Unito meridionale |
| &nbsp; | Regno Unito meridionale | Regno Unito meridionale |
| Asia/Pacifico | Asia sudorientale | Asia sudorientale |
| &nbsp; | Asia orientale | Asia sudorientale |
| Australia | Australia orientale | Australia orientale |
| &nbsp; | Australia sudorientale | Australia sudorientale |
| India | India centrale | India centrale |
| &nbsp; | India occidentale | India centrale |
| &nbsp; | India meridionale | India centrale |
| Giappone | Giappone orientale | Giappone orientale |
| &nbsp; | Giappone occidentale | Giappone orientale |
| Corea | Corea centrale | Corea centrale |
| &nbsp; | Corea meridionale | Corea centrale |

In alternativa, è possibile indicare esplicitamente l'area del servizio Data Factory da usare per eseguire la copia specificando la proprietà `executionLocation` in `typeProperties` nell'attività di copia. I valori supportati per questa proprietà sono elencati nella colonna **Area usata per lo spostamento dei dati** precedente. Si noti che i dati vengono trasferiti in rete attraverso tale area durante la copia. Ad esempio, per eseguire la copia tra archivi di Azure in Corea è possibile specificare `"executionLocation": "Japan East"` per instradare i dati tramite l'area del Giappone (vedere l'[esempio JSON](#by-using-json-scripts) come riferimento).

> [!NOTE]
> Se l'area dell'archivio dati di destinazione non è nell'elenco precedente o non è rilevabile, per impostazione predefinita l'attività di copia non viene completata invece di passare attraverso un'area alternativa, a meno che non sia specificato `executionLocation`. L'elenco di aree supportate verrà ampliato nel tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiare dati tra un archivio dati locale e un archivio dati cloud
Quando i dati vengono copiati da archivi locali (o macchina virtuale Azure/IaaS) ad archivi cloud, lo spostamento dei dati viene eseguito dal [Gateway di gestione dati](data-factory-data-management-gateway.md) su un computer locale o una macchina virtuale. I dati non passano attraverso il servizio nel cloud, a meno che non si usi la funzionalità di [Copia di staging](data-factory-copy-activity-performance.md#staged-copy) . In questo caso, i dati passano attraverso l'archiviazione BLOB di Azure temporanea prima che vengano scritto nell'archivio dati sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Creare una pipeline con attività di copia
È possibile creare una pipeline con l'attività di copia in alcuni modi:

### <a name="by-using-the-copy-wizard"></a>Con la copia guidata
La copia guidata di Data Factory aiuta a creare una pipeline con l'attività di copia. Questa pipeline consente di copiare dati dalle origini supportate nelle destinazioni *senza scrivere definizioni JSON* per i servizi collegati, i set di dati e le pipeline. Per informazioni dettagliate sulla procedura guidata, vedere [Copia guidata di Data Factory](data-factory-copy-wizard.md) .  

### <a name="by-using-json-scripts"></a>Con gli script JSON
È possibile usare l'Editor di Data Factory nel portale di Azure, in Visual Studio o in Azure PowerShell per creare una definizione JSON per una pipeline con l'attività di copia. A questo punto, è possibile eseguire la distribuzione in modo da creare la pipeline in Data Factory. Per un'esercitazione con istruzioni dettagliate, vedere [Esercitazione: Copiare dati da un archivio BLOB al database SQL usando Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .    

Per tutti i tipi di attività sono disponibili proprietà JSON come nome, descrizione, tabelle di input e output e criteri. Le proprietà disponibili nella sezione `typeProperties` dell'attività variano per ogni tipo di attività.

Per l'attività di copia, la sezione `typeProperties` varia a seconda dei tipi di origine e sink. Fare clic su un'origine o un sink nella sezione relativa [alle origini e ai sink supportati](#supported-data-stores-and-formats) per informazioni sulle proprietà supportate dall'attività di copia per l'archivio dati.

Di seguito è riportata una definizione JSON di esempio:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
La pianificazione definita nel set di dati di output determina quando viene eseguita l'attività (ad esempio **daily**: frequency: **day** e interval: **1**). L'attività esegue la copia dei dati da un set di dati di input (**origine**) in un set di dati di output (**sink**).

È possibile specificare più di un set di dati di input per l'attività di copia. Vengono usati per verificare le dipendenze prima dell'esecuzione dell'attività. Tuttavia, vengono copiati solo i dati dal primo set di dati al set di dati di destinazione. Per altre informazioni, vedere [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Vedere l'articolo [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Vengono anche elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

## <a name="fault-tolerance"></a>Tolleranza di errore
Per impostazione predefinita, l'attività di copia interromperà la copia dei dati e restituirà un errore quando rileva dati incompatibili tra l'origine e il sink; è possibile chiedere in modo esplicito di ignorare e registrare le righe incompatibili, quindi copiare solo i dati compatibili perché la copia abbia esito positivo. Per altri dettagli, vedere la [Tolleranza di errore dell'attività di copia: ignorare le righe incompatibili](data-factory-copy-activity-fault-tolerance.md).

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Vedere [Azure Data Factory: considerazioni sulla sicurezza dello spostamento dei dati](data-factory-data-movement-security-considerations.md) che descrive l'infrastruttura di sicurezza usata dai servizi di spostamento dei dati in Azure Data Factory per proteggere i dati.

## <a name="scheduling-and-sequential-copy"></a>Pianificazione e copia sequenziale
Vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md) per informazioni dettagliate sul funzionamento della pianificazione e dell'esecuzione in Data Factory. È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Vedere la sezione [Copiare in sequenza](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="type-conversions"></a>Conversioni di tipi
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi sink con il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET.
2. Conversione dal tipo .NET al tipo di sink nativo.

Il mapping da un sistema di tipo nativo a un tipo .NET per un archivio dati è disponibile nell'articolo corrispondente sull'archivio dati. Fare clic sul link specifico della [Archivi dati e formati supportati](#supported-data-stores) . È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.

## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni sull'attività di copia, vedere [Copiare i dati dall'archiviazione BLOB di Azure al database SQL di Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Per informazioni sullo spostamento dei dati da un archivio di dati locale a un archivio di dati cloud, vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

---
title: Archivi dati supportati nella condivisione dati di AzureSupported data stores in Azure Data Share
description: Informazioni sugli archivi dati supportati per l'uso della condivisione dati di Azure.Learn about the data stores that are supported for use Azure Data Share.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501799"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati nella condivisione dati di AzureSupported data stores in Azure Data Share

Condivisione dati di Azure offre una condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e verso archivi dati diversi. I provider di dati possono condividere i dati da un tipo di archivio dati e i consumer di dati possono scegliere in quale archivio dati ricevere i dati. 

In this article, you'll learn about the rich set of Azure data stores that are supported in Azure Data Share. È inoltre possibile trovare informazioni sulle combinazioni di archivi dati che possono essere sfruttate dai provider di dati e dai consumer di dati. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quali archivi dati sono supportati in Condivisione dati di Azure? 

The below table details the supported data sources for Azure Data Share. 

| Archivio dati | Condivisione basata su istantanee | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| database SQL di Azure |Anteprima pubblica | |
| Azure Synapse Analytics (formerly Azure SQL DW) |Anteprima pubblica | |
| Esplora dati di Azure | |Anteprima pubblica |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

Condivisione dati di Azure offre ai consumatori di dati la flessibilità quando si decide su un archivio dati di accettare i dati. Ad esempio, i dati condivisi dal database SQL di Azure possono essere ricevuti in Azure Data Lake Store Gen2, Database SQL di Azure o Analisi synapse di Azure.For example, data being shared from Azure SQL Database can be received into Azure Data Lake Store Gen2, Azure SQL Database or Azure Synapse Analytics. I clienti possono scegliere il formato in cui ricevere i dati durante la configurazione di una condivisione dati ricevuta. 

Nella tabella seguente vengono descritte in dettaglio le diverse combinazioni e scelte degli utenti dei dati per l'accettazione e la configurazione della condivisione dati. Per ulteriori informazioni su come configurare i mapping dei set di dati, vedere [come configurare i mapping](how-to-configure-mapping.md)dei set di dati.

|  | Archiviazione BLOB di Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | database SQL di Azure | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| database SQL di Azure | ✓ | | ✓| ✓| ✓|
| Azure Synapse Analytics (formerly Azure SQL DW) | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazioneShare from a storage account
Condivisione dati di Azure supporta la condivisione di file, cartelle e file system da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori da Archiviazione BLOB di Azure.It also supports sharing of blobs, folders and containers from Azure Blob Storage. Solo il BLOB in blocchi è attualmente supportato. Quando le cartelle vengono condivise nella condivisione basata su snapshot, il consumer di dati può scegliere di creare una copia completa dei dati di condivisione o sfruttare la funzionalità snapshot incrementale per copiare solo i file nuovi o aggiornati. I file esistenti con lo stesso nome verranno sovrascritti.

## <a name="share-from-a-sql-based-source"></a>Condividere da un'origine basata su SQLShare from a SQL-based source
Condivisione dati di Azure supporta la condivisione di tabelle o viste dal database SQL di Azure e da Azure Synapse Analytics (in precedenza Azure SQL DW). Gli utenti dei dati possono scegliere di accettare i dati in Azure Data Lake Store Gen2 o Azure Blob Storage come file csv o di parquet. Si noti che per impostazione predefinita, i formati di file sono csv. Il consumatore di dati può scegliere di ricevere i dati in formato parquet, se lo si desidera. Questa operazione può essere eseguita nelle impostazioni di mapping del set di dati quando si ricevono i dati. 

Quando si accettano dati in Azure Data Lake Store Gen2 o Archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione. 

Un consumer di dati può scegliere di ricevere dati in una tabella di propria scelta. In questo scenario, se la tabella di destinazione non esiste già, Azure Data Share crea la tabella SQL con lo schema di origine. Se esiste già una tabella di destinazione con lo stesso nome, verrà eliminata e sovrascritta con lo snapshot completo più recente. Quando si esegue il mapping della tabella di destinazione, è possibile specificare uno schema alternativo e un nome di tabella. Gli snapshot incrementali non sono attualmente supportati. 

La condivisione da origini basate su SQL dispone di prerequisiti correlati alle regole e alle autorizzazioni del firewall. Si prega di fare riferimento alla sezione prerequisiti del [tutorial condividere i dati](share-your-data.md) per i dettagli.

## <a name="share-from-azure-data-explorer"></a>Condividere da Esplora dati di Azure
Condivisione dati di Azure supporta la possibilità di condividere i database sul posto dai cluster di Azure Data Explorer.Azure Data Share supports the ability to share databases in-place from Azure Data Explorer clusters. Il provider di dati può essere condiviso a livello di database o cluster. Quando vengono condivisi a livello di database, i consumer di dati saranno in grado di accedere solo ai database specifici condivisi dal provider di dati. Quando viene condiviso a livello di cluster, il consumer di dati può accedere a tutti i database dal cluster del provider, inclusi eventuali database futuri creati dal provider di dati.

Per accedere ai database condivisi, i consumer di dati devono disporre di un proprio cluster di Azure Data Explorer.To access shared databases, data consumer needs to have its own Azure Data Explorer cluster. Il cluster di Azure Data Explorer del consumer di dati deve essere individuato nello stesso data center di Azure del cluster di Azure Data Explorer del provider di dati. Quando viene stabilita la relazione di condivisione, La condivisione dati di Azure crea un collegamento simbolico tra i cluster di Azure Data Explorer del provider e del consumer.

Azure Data Explorer supporta due modalità di inserimento dei dati: batch e streaming. I dati ricevuti dal batch nel database condiviso verranno visualizzati da pochi secondi a pochi minuti sul lato consumer di dati. I dati ricevuti dallo streaming potrebbero richiedere fino a 24 ore per essere visualizzati sul lato consumer di dati. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).

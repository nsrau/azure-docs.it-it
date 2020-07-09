---
title: Archivi dati supportati nella condivisione dati di Azure
description: Informazioni sugli archivi dati supportati per l'uso di condivisione dati di Azure.
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 8263e2b2d3d8d029cd28f6c8ea6c556fb9fe23be
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108295"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati nella condivisione dati di Azure

La condivisione di dati di Azure offre una condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e in archivi dati diversi. I provider di dati possono condividere dati da un tipo di archivio dati e i relativi consumer di dati possono scegliere l'archivio dati in cui ricevere i dati. 

Questo articolo illustra il set completo di archivi dati di Azure supportati nella condivisione dati di Azure. È inoltre possibile trovare informazioni sulle combinazioni di archivi dati che possono essere utilizzati da provider di dati e consumer di dati. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quali archivi dati sono supportati nella condivisione dati di Azure? 

La tabella seguente illustra in dettaglio le origini dati supportate per la condivisione di dati di Azure. 

| Archivio dati | Condivisione basata su snapshot | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| database SQL di Azure |Anteprima pubblica | |
| Azure sinapsi Analytics (in precedenza Azure SQL DW) |Anteprima pubblica | |
| Esplora dati di Azure | |Anteprima pubblica |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

La condivisione di dati di Azure offre la flessibilità dei consumer di dati per la scelta di un archivio dati in cui accettare i dati. Ad esempio, i dati condivisi dal database SQL di Azure possono essere ricevuti in Azure Data Lake Store Gen2, nel database SQL di Azure o in Azure sinapsi Analytics. I clienti possono scegliere il formato in cui ricevere i dati durante la configurazione di una condivisione dati ricevuta. 

Nella tabella seguente vengono illustrate le diverse combinazioni e scelte che i consumer di dati hanno per l'accettazione e la configurazione della condivisione di dati. Per ulteriori informazioni su come configurare i mapping dei set di dati, vedere [come configurare i mapping dei set di dati](how-to-configure-mapping.md).

| Archivio dati | Archiviazione BLOB di Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | database SQL di Azure | Azure Synapse Analytics | Esplora dati di Azure
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| database SQL di Azure | ✓ | | ✓ | ✓ | ✓ ||
| Azure sinapsi Analytics (in precedenza Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ ||
| Esplora dati di Azure |||||| ✓ |

## <a name="share-from-a-storage-account"></a>Condividi da un account di archiviazione
Condivisione dati di Azure supporta la condivisione di file, cartelle e file System da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori dall'archiviazione BLOB di Azure. Attualmente è supportato solo il BLOB in blocchi. Quando i file System, i contenitori o le cartelle sono condivisi nella condivisione basata su snapshot, il consumer di dati può scegliere di eseguire una copia completa dei dati di condivisione o sfruttare la funzionalità di snapshot incrementale per copiare solo i file nuovi o aggiornati. Gli snapshot incrementali si basano sull'ora dell'Ultima modifica dei file. I file esistenti con lo stesso nome verranno sovrascritti.

## <a name="share-from-a-sql-based-source"></a>Condividi da un'origine basata su SQL
La condivisione di dati di Azure supporta la condivisione di tabelle o viste dal database SQL di Azure e da Azure sinapsi Analytics (in precedenza Azure SQL DW). I consumer di dati possono scegliere di accettare i dati nel Azure Data Lake Store Gen2 o nell'archiviazione BLOB di Azure come file CSV o parquet. Si noti che per impostazione predefinita, i formati di file sono CSV. Se lo si desidera, il consumer di dati può scegliere di ricevere i dati in formato parquet. Questa operazione può essere eseguita nelle impostazioni di mapping del set di dati durante la ricezione dei dati. 

Quando si accettano dati in Azure Data Lake Store Gen2 o nell'archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione. 

Un consumer di dati può scegliere di ricevere i dati in una tabella di propria scelta. In questo scenario, se la tabella di destinazione non esiste già, la condivisione di dati di Azure crea la tabella SQL con lo schema di origine. Se una tabella di destinazione esiste già con lo stesso nome, verrà eliminata e sovrascritta con lo snapshot completo più recente. Quando si esegue il mapping della tabella di destinazione, è possibile specificare uno schema e un nome di tabella alternativi. Gli snapshot incrementali non sono attualmente supportati. 

La condivisione da origini basate su SQL include prerequisiti relativi a regole e autorizzazioni del firewall. Per informazioni dettagliate, vedere la sezione prerequisiti dell'esercitazione [condividere i dati](share-your-data.md) .

## <a name="share-from-azure-data-explorer"></a>Condividere da Esplora dati di Azure
La condivisione di dati di Azure supporta la possibilità di condividere i database sul posto da Azure Esplora dati cluster. Il provider di dati può condividere a livello di database o di cluster. Quando è condiviso a livello di database, il consumer di dati sarà in grado di accedere solo ai database specifici condivisi dal provider di dati. Quando viene condiviso a livello di cluster, il consumer di dati può accedere a tutti i database dal cluster del provider, inclusi eventuali database futuri creati dal provider di dati.

Per accedere ai database condivisi, il consumer di dati deve avere il proprio cluster Esplora dati di Azure. Il cluster di Azure Esplora dati del consumer di dati deve trovarsi nella stessa data center di Azure del cluster di Azure Esplora dati del provider di dati. Quando si stabilisce una relazione di condivisione, condivisione dati di Azure crea un collegamento simbolico tra i cluster di Azure Esplora dati del provider e del consumer.

Azure Esplora dati supporta due modalità di inserimento dei dati: batch e streaming. I dati ricevuti da batch nel database condiviso verranno visualizzati tra pochi secondi e pochi minuti sul lato consumer dati. I dati ricevuti dallo streaming potrebbero richiedere fino a 24 ore per essere visualizzati sul lato utente dati. 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, procedere con l'esercitazione sulla [condivisione dei dati](share-your-data.md).

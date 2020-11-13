---
title: Archivi dati supportati in Condivisione dati di Azure
description: Informazioni sugli archivi dati supportati per l'uso di condivisione dati di Azure.
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 47c484268573334057e6b4dd14bbae849f9ce774
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577222"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Archivi dati supportati in Condivisione dati di Azure

La condivisione di dati di Azure offre una condivisione dei dati aperta e flessibile, inclusa la possibilità di condividere da e in archivi dati diversi. I provider di dati possono condividere dati da un tipo di archivio dati e i relativi consumer di dati possono scegliere l'archivio dati in cui ricevere i dati. 

Questo articolo illustra il set completo di archivi dati di Azure supportati nella condivisione dati di Azure. È inoltre possibile trovare informazioni sulle combinazioni di archivi dati che possono essere utilizzati da provider di dati e consumer di dati. 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Quali archivi dati sono supportati nella condivisione dati di Azure? 

La tabella seguente illustra in dettaglio le origini dati supportate per la condivisione di dati di Azure. 

| Archivio dati | Condivisione basata su snapshot (snapshot completo) | Condivisione basata su snapshot (snapshot incrementale) | Condivisione sul posto 
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Archiviazione BLOB di Azure |✓ |✓ | |
| Azure Data Lake Storage Gen1 |✓ |✓ | |
| Azure Data Lake Storage Gen2 |✓ |✓ ||
| database SQL di Azure |✓ | | |
| Azure sinapsi Analytics (in precedenza Azure SQL DW) |✓ | | |
| Pool SQL di Azure sinapsi Analytics (area di lavoro) | Anteprima pubblica | | |
| Esplora dati di Azure | | |✓ |

## <a name="data-store-support-matrix"></a>Matrice di supporto dell'archivio dati

La condivisione di dati di Azure offre la flessibilità dei consumer di dati per la scelta di un archivio dati in cui accettare i dati. Ad esempio, i dati condivisi dal database SQL di Azure possono essere ricevuti in Azure Data Lake Store Gen2, nel database SQL di Azure o in Azure sinapsi Analytics. I clienti possono scegliere il formato in cui ricevere i dati durante la configurazione di una condivisione dati ricevuta. 

Nella tabella seguente vengono illustrate le diverse combinazioni e scelte che i consumer di dati hanno per l'accettazione e la configurazione della condivisione di dati. Per ulteriori informazioni su come configurare i mapping dei set di dati, vedere [come configurare i mapping dei set di dati](how-to-configure-mapping.md).

| Archivio dati | Archiviazione BLOB di Azure | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | database SQL di Azure | Azure sinapsi Analytics (in precedenza Azure SQL DW) | Pool SQL di Azure sinapsi Analytics (area di lavoro) | Esplora dati di Azure
|:--- |:--- |:--- |:--- |:--- |:--- |:--- | :--- |
| Archiviazione BLOB di Azure | ✓ || ✓ |||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ |||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ |||
| database SQL di Azure | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Azure sinapsi Analytics (in precedenza Azure SQL DW) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Pool SQL di Azure sinapsi Analytics (area di lavoro) | ✓ | | ✓ | ✓ | ✓ | ✓ ||
| Esplora dati di Azure ||||||| ✓ |

## <a name="share-from-a-storage-account"></a>Condividi da un account di archiviazione
Condivisione dati di Azure supporta la condivisione di file, cartelle e file System da Azure Data Lake Gen1 e Azure Data Lake Gen2. Supporta anche la condivisione di BLOB, cartelle e contenitori dall'archiviazione BLOB di Azure. Attualmente è supportato solo il BLOB in blocchi. Quando i file System, i contenitori o le cartelle sono condivisi nella condivisione basata su snapshot, il consumer di dati può scegliere di eseguire una copia completa dei dati di condivisione o sfruttare la funzionalità di snapshot incrementale per copiare solo i file nuovi o aggiornati. Gli snapshot incrementali si basano sull'ora dell'Ultima modifica dei file. I file esistenti con lo stesso nome verranno sovrascritti.

Per informazioni dettagliate, vedere [condividere e ricevere dati dall'archivio BLOB di Azure e Azure Data Lake storage](how-to-share-from-storage.md) .

## <a name="share-from-a-sql-based-source"></a>Condividi da un'origine basata su SQL
La condivisione di dati di Azure supporta la condivisione di tabelle o viste dal database SQL di Azure e da Azure sinapsi Analytics (in precedenza Azure SQL DW) e la condivisione di tabelle dal pool SQL di Azure sinapsi Analytics (area di lavoro). I consumer di dati possono scegliere di accettare i dati in Azure Data Lake Storage Gen2 o nell'archiviazione BLOB di Azure come file con estensione CSV o parquet, oltre che nel database SQL di Azure e in Azure sinapsi Analytics come tabelle.

Quando si accettano dati in Azure Data Lake Store Gen2 o nell'archiviazione BLOB di Azure, gli snapshot completi sovrascrivono il contenuto del file di destinazione, se già esistente.
Quando i dati vengono ricevuti nella tabella e se la tabella di destinazione non esiste già, la condivisione di dati di Azure crea la tabella SQL con lo schema di origine. Se una tabella di destinazione esiste già con lo stesso nome, verrà eliminata e sovrascritta con lo snapshot completo più recente. Gli snapshot incrementali non sono attualmente supportati.

Per informazioni dettagliate, fare riferimento a [condividere e ricevere dati dal database SQL di Azure e da Azure sinapsi Analytics](how-to-share-from-sql.md) .

## <a name="share-from-azure-data-explorer"></a>Condividere da Esplora dati di Azure
La condivisione di dati di Azure supporta la possibilità di condividere i database sul posto da Azure Esplora dati cluster. Il provider di dati può condividere a livello di database o di cluster. Quando è condiviso a livello di database, il consumer di dati sarà in grado di accedere solo ai database specifici condivisi dal provider di dati. Quando viene condiviso a livello di cluster, il consumer di dati può accedere a tutti i database dal cluster del provider, inclusi eventuali database futuri creati dal provider di dati.

Per accedere ai database condivisi, il consumer di dati deve avere il proprio cluster Esplora dati di Azure. Il cluster di Azure Esplora dati del consumer di dati deve trovarsi nella stessa data center di Azure del cluster di Azure Esplora dati del provider di dati. Quando si stabilisce una relazione di condivisione, condivisione dati di Azure crea un collegamento simbolico tra i cluster di Azure Esplora dati del provider e del consumer. I dati inseriti con la modalità batch nel cluster di Azure Esplora dati di origine vengono visualizzati nel cluster di destinazione entro pochi secondi per pochi minuti.

Per informazioni dettagliate, vedere [condividere e ricevere dati da Azure Esplora dati](/azure/data-explorer/data-share) . 

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come iniziare a condividere i dati, continuare con l'esercitazione [condividere i dati](share-your-data.md) .

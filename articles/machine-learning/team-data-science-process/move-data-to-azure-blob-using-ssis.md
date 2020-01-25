---
title: Spostare i dati di archiviazione BLOB con connettori SSIS - Processo di data science per i team
description: Informazioni su come spostare i dati da e verso archiviazione BLOB di Azure usando SQL Server Integration Services Feature Pack per Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 77bfd9d5bcae7bedd673354e32464d5f59bdc9b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720872"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Spostare dati da o verso un archivio BLOB di Azure con connettori SSIS
[SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornisce componenti per la connessione ad Azure, il trasferimento di dati tra Azure e origini dati locali e l'elaborazione di dati archiviati in Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Dopo che i clienti hanno spostato i dati locali nel cloud, possono accedere ai dati da qualsiasi servizio di Azure per sfruttare tutte le potenzialità della suite di tecnologie Azure. I dati possono essere usati in seguito, ad esempio, in Azure Machine Learning o in un cluster HDInsight.

Esempi per l'uso di queste risorse di Azure sono disponibili nelle procedure dettagliate relative a [SQL](sql-walkthrough.md) e [HDInsight](hive-walkthrough.md) .

Per informazioni sugli scenari classici che usano SSIS per soddisfare esigenze aziendali comuni in scenari di integrazione dei dati ibridi, vedere il blog relativo ai [vantaggi offerti da SQL Server Integration Services Feature Pack for Azure](https://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx) .

> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Informazioni di base su BLOB di Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le attività descritte in questo articolo, è necessario avere una sottoscrizione di Azure e un account di archiviazione di Azure configurato. Per caricare o scaricare dati, sono necessari il nome e la chiave dell'account di archiviazione di Azure.

* Per configurare una **sottoscrizione di Azure**, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un **account di archiviazione** e per ottenere informazioni sull'account e sulla chiave, vedere [informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).

Per usare i **connettori SSIS**è necessario scaricare:

* **SQL Server 2014 o 2016 Standard (o versioni successive)** : l'installazione include SQL Server Integration Services.
* **Microsoft SQL Server 2014 o 2016 Integration Services Feature Pack per Azure**: questi connettori possono essere scaricati rispettivamente dalle pagine [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> SSIS viene installato con SQL Server, ma non è incluso nella versione Express. Per informazioni sulle applicazioni incluse nelle diverse edizioni di SQL Server, vedere [Edizioni di SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Per materiale di formazione su SSIS, vedere [Risorse pratiche per la formazione su SSIS](https://www.microsoft.com/sql-server/training-certification)

Per informazioni su come iniziare subito a usare SSIS per creare semplici pacchetti di estrazione, trasformazione e caricamento (ETL), vedere [Esercitazione SSIS: Creazione di un pacchetto ETL semplice](https://msdn.microsoft.com/library/ms169917.aspx).

## <a name="download-nyc-taxi-dataset"></a>Scaricare il set di dati NYC Taxi
Questo esempio usa un set di dati disponibile pubblicamente, ovvero il set di dati [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) . Il set di dati è costituito da circa 173 milioni di corse su taxi a NYC nell'anno 2013. Sono disponibili due tipi di dati: i dettagli sul tragitto e i dettagli sul costo del tragitto. Poiché è presente un file per ogni mese, sono presenti 24 file, ognuno dei quali è di circa 2 GB decompressi.

## <a name="upload-data-to-azure-blob-storage"></a>Caricare dati nell'archivio BLOB di Azure
Per spostare i dati usando il Feature Pack di SSIS da una posizione locale a un archivio BLOB di Azure, usare un'istanza dell' [**Azure Blob Upload Task**](https://msdn.microsoft.com/library/mt146776.aspx), illustrata qui:

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

I parametri usati dall'attività sono descritti di seguito:

| Campo | Description |
| --- | --- |
| **AzureStorageConnection** |Specifica una gestione connessione di archiviazione di Azure esistente o ne crea una nuova che fa riferimento a un account di archiviazione di Azure che punta alla posizione in cui sono ospitati i file BLOB. |
| **BlobContainer** |Specifica il nome del contenitore BLOB che include i file caricati come BLOB. |
| **BlobDirectory** |Specifica la directory BLOB in cui viene archiviato il file caricato come BLOB in blocchi. La directory BLOB è una struttura gerarchica virtuale. Se il BLOB esiste già, verrà sostituito. |
| **LocalDirectory** |Specifica la directory locale che include i file da caricare. |
| **FileName** |Specifica un filtro per i nomi per la selezione di file con il modello di nomi specificato. Ad esempio, MySheet\*.xls\* include file, quali MySheet001.xls e MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Specifica un filtro basato su un intervallo di tempo. Sono inclusi i file modificati dopo *TimeRangeFrom* e prima di *TimeRangeTo*. |

> [!NOTE]
> Le credenziali di **AzureStorageConnection** devono essere corrette e **BlobContainer** deve essere presente prima del tentativo di trasferimento.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Scaricare i dati da un archivio BLOB di Azure
Per scaricare i dati di Archiviazione BLOB di Azure in un archivio locale con SSIS, usare un'istanza di [Azure Blob Download Task](https://msdn.microsoft.com/library/mt146779.aspx).

## <a name="more-advanced-ssis-azure-scenarios"></a>Altri scenari avanzati di SSIS-Azure
Il Feature Pack di SSIS consente di gestire flussi più complessi creando pacchetti di attività. Ad esempio, è possibile eseguire il feed dei dati BLOB direttamente in un cluster HDInsight, il cui output può essere scaricato di nuovo in un BLOB e quindi in un archivio locale. SSIS può eseguire processi Hive e Pig in un cluster HDInsight usando connessioni SSIS aggiuntive:

* Per eseguire uno script Hive in un cluster Azure HDInsight con SSIS, usare [Azure HDInsight Hive Task](https://msdn.microsoft.com/library/mt146771.aspx).
* Per eseguire uno script Pig in un cluster Azure HDInsight con SSIS, usare [Azure HDInsight Pig Task](https://msdn.microsoft.com/library/mt146781.aspx).


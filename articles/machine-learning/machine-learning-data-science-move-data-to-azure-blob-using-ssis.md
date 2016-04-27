<properties
	pageTitle="Spostare i dati in o da un archivio BLOB di Azure mediante connettori SSIS | Microsoft Azure"
	description="Spostare i dati in o da un archivio BLOB di Azure mediante connettori SSIS."
	services="machine-learning,storage"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="bradsev" />

# Spostare i dati in o da un archivio BLOB di Azure mediante connettori SSIS

[SQL Server Integration Services Feature Pack for Azure](https://msdn.microsoft.com/library/mt146770.aspx) fornisce componenti per la connessione ad Azure, il trasferimento di dati tra Azure e origini dati locali e l'elaborazione di dati archiviati in Azure.

Collegamenti a indicazioni sulle tecnologie usate per spostare i dati in e/o da un archivio BLOB di Azure sono disponibili qui:

[AZURE.INCLUDE [selettore dello strumento di archiviazione BLOB](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Introduzione

Dopo che i clienti hanno spostato i dati locali sul cloud, potranno accedere ai dati da qualsiasi servizio di Azure, per sfruttare le capacità complete della famiglia di tecnologie Azure. È ad esempio possibile usare i dati in Azure Machine Learning o in un cluster HDInsight.

Questo sarà in genere il primo passaggio per le esercitazioni dettagliate su [SQL](machine-learning-data-science-process-sql-walkthrough.md) e [HDInsight](machine-learning-data-science-process-hive-walkthrough.md).

Per informazioni sugli scenari classici che usano SSIS per soddisfare esigenze aziendali comuni in scenari di integrazione dei dati ibridi, vedere il blog relativo ai [vantaggi offerti da SQL Server Integration Services Feature Pack for Azure](http://blogs.msdn.com/b/ssis/archive/2015/06/25/doing-more-with-sql-server-integration-services-feature-pack-for-azure.aspx).

> [AZURE.NOTE] Per un'introduzione completa dell'archiviazione BLOB di Azure, vedere [Informazioni di base sui BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) e [Servizio BLOB di Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Prerequisiti

Per eseguire le attività illustrate in questo articolo, è necessario avere configurato una sottoscrizione di Azure e un account di archiviazione di Azure. Per caricare o scaricare dati, è necessario conoscere il nome dell'account di archiviazione di Azure e la chiave dell'account.

- Per configurare una **sottoscrizione di Azure**, vedere [Versione di prova gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
- Per istruzioni sulla creazione di un **account di archiviazione** e su come ottenere le informazioni sull'account e sulla chiave, vedere [Informazioni sugli account di archiviazione di Azure](../storage/storage-create-storage-account.md).


Per usare i **connettori SSIS** è necessario scaricare:

- **SQL Server 2014 o 2016 Standard (o versioni successive)**: l'installazione include SQL Server Integration Services.
- **Microsoft SQL Server 2014 o 2016 Pacchetto delle caratteristiche del servizio di integrazione per Azure**: disponibili, rispettivamente per il download nelle pagine di [SQL Server 2014 Servizi di integrazione](http://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Servizi di integrazione](https://www.microsoft.com/download/details.aspx?id=49492).

> [AZURE.NOTE] SSIS viene installato con SQL Server, ma non è incluso nella versione Express. Per informazioni sulle applicazioni incluse nelle diverse edizioni di SQL Server, vedere [Edizioni di SQL Server](http://www.microsoft.com/it-IT/server-cloud/products/sql-server-editions/)

Per materiale di formazione su SSIS, vedere [Risorse pratiche per la formazione su SSIS](http://www.microsoft.com/download/details.aspx?id=20766)

Per informazioni su come iniziare subito a usare SSIS per creare semplici pacchetti di estrazione, trasformazione e caricamento (ETL), vedere [Esercitazione SSIS: Creazione di un pacchetto ETL semplice](https://msdn.microsoft.com/library/ms169917.aspx).

## Scaricare il set di dati NYC Taxi  
Questo esempio usa un set di dati disponibile pubblicamente, ovvero il set di dati [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). Il set di dati è costituito da circa 173 milioni di corse su taxi a NYC nell'anno 2013. Sono disponibili due tipi di dati: i dettagli sul tragitto e i dettagli sul costo del tragitto. Poiché è disponibile un file per ogni mese, sono presenti 24 file in tutto, ognuno dei quali ha dimensioni pari a circa 2 GB, senza compressione.


## Caricare dati nell'archivio BLOB di Azure
Per spostare i dati usando il Feature Pack di SSIS da una posizione locale a un archivio BLOB di Azure, usare un'istanza dell'[**Azure Blob Upload Task**](https://msdn.microsoft.com/library/mt146776.aspx), illustrata di seguito:

![configure-data-science-vm](./media/machine-learning-data-science-move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)


I parametri usati dall'attività sono descritti di seguito:


Campo|Descrizione|
----------------------|----------------|
**AzureStorageConnection**|Specifica un'istanza esistente di Gestione connessione dell'archiviazione di Azure o crea una nuova istanza che fa riferimento a un account di archiviazione di Azure che punta alla posizione in cui sono ospitati i file BLOB.|
**BlobContainer**|Specifica il nome del contenitore BLOB che includerà i file caricati come BLOB.|
**BlobDirectory**|Specifica la directory BLOB in cui i file caricati verranno archiviati come BLOB in blocchi. La directory BLOB è una struttura gerarchica virtuale. Se il BLOB esiste già, verrà sostituito.|
**LocalDirectory**|Specifica la directory locale che include i file da caricare.|
**FileName**|Specifica un filtro per i nomi per la selezione di file con il modello di nomi specificato. Ad esempio, MySheet*.xls* include file quali MySheet001.xls e MySheetABC.xlsx.|
**TimeRangeFrom/TimeRangeTo**|Specifica un filtro basato su un intervallo di tempo. Verranno inclusi i file modificati dopo *TimeRangeFrom* e prima di *TimeRangeTo*.|


> [AZURE.NOTE] Prima di provare a eseguire il trasferimento, è necessario assicurarsi che le credenziali **AzureStorageConnection** siano corrette e che **BlobContainer** esista.

## Scaricare i dati da un archivio BLOB di Azure

Per scaricare i dati da un archivio BLOB di Azure a un archivio locale con SSIS, usare un'istanza di [Azure Blob Upload Task](https://msdn.microsoft.com/library/mt146779.aspx).

##Altri scenari avanzati di SSIS-Azure
Si noti che il Feature Pack di SSIS consente la gestione di flussi più complessi mediante la creazione di pacchetti di attività. Ad esempio, è possibile eseguire il feed dei dati BLOB direttamente in un cluster HDInsight, il cui output può essere scaricato di nuovo in un BLOB e quindi in un archivio locale. SSIS può eseguire processi Hive e Pig in un cluster HDInsight usando connessioni SSIS aggiuntive:

- Per eseguire uno script Hive in un cluster Azure HDInsight con SSIS, usare [Azure HDInsight Hive Task](https://msdn.microsoft.com/library/mt146771.aspx).
- Per eseguire uno script Pig in un cluster Azure HDInsight con SSIS, usare [Azure HDInsight Pig Task](https://msdn.microsoft.com/library/mt146781.aspx).

<!---HONumber=AcomDC_0413_2016-->
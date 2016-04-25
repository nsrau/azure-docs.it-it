<properties
   pageTitle="Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake| Microsoft Azure"
   description="Usare lo strumento AdlCopy per copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/05/2016"
   ms.author="nitinme"/>

# Copiare i dati da BLOB di Archiviazione di Azure ad Archivio Data Lake

Archivio Data Lake di Azure fornisce uno strumento da riga di comando, [AdlCopy](http://aka.ms/downloadadlcopy), per copiare dati da **BLOB di Archiviazione di Azure ad Archivio Data Lake**. Non è possibile usare lo strumento AdlCopy per copiare dati da Archivio Data Lake ai BLOB di Archiviazione di Azure.

È possibile usare lo strumento AdlCopy in due modi:

* **Autonomo**, in cui lo strumento usa le risorse di Archivio Data Lake per eseguire l'attività.
* **Tramite un account di Analisi Data Lake**, in cui le unità assegnate all'account di Analisi Data Lake vengono usate per eseguire l'operazione di copia. È consigliabile scegliere questa opzione se si intende eseguire le attività di copia in modo prevedibile.

##Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup).
- Un contenitore **BLOB di Archiviazione di Azure** con alcuni dati.
- **Un account di Analisi Azure Data Lake (facoltativo)**. Per istruzioni su come creare un account di Archivio Data Lake, vedere [Introduzione ad Analisi Azure Data Lake con il portale di Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
- **Lo strumento AdlCopy**. Installare lo strumento AdlCopy da [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Sintassi dello strumento AdlCopy

Usare la sintassi seguente per usare lo strumento AdlCopy

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

I parametri nella sintassi sono descritti di seguito:

| Opzione | Descrizione |
|-----------|------------|
| Sorgente | Specifica il percorso dei dati di origine nel BLOB di Archiviazione di Azure. L'origine può essere un contenitore BLOB o un BLOB |
| Dest | Specifica la destinazione di Archivio Data Lake in cui eseguire la copia. |
| SourceKey | Specifica la chiave di accesso alle risorse di archiviazione per l'origine BLOB di Archiviazione di Azure. |
| Account | **Facoltativo**. Scegliere questa opzione se si vuole usare l'account di Analisi Azure Data Lake per eseguire il processo di copia. Se si usa l'opzione /Account nella sintassi ma non si specifica un account di Analisi Data Lake, AdlCopy usa un account predefinito per eseguire il processo. Inoltre, se si sceglie questa opzione, è necessario aggiungere l'origine (BLOB di Archiviazione di Azure) e la destinazione (Archivio Azure Data Lake) come origini dati per l'account di Analisi Data Lake. |
| Unità | Specifica il numero di unità di Analisi Data Lake che verranno usate per il processo di copia. Questa opzione è obbligatoria se si usa l'opzione **/Account** per specificare l'account di Analisi Data Lake.                                                                                                                                                                                                                                                                                                                                               



## Usare lo strumento AdlCopy in modalità autonoma

1. Aprire un prompt dei comandi e passare alla directory in cui è installato AdlCopy, in genere `%HOMEPATH%\Documents\adlcopy`.

2. Eseguire il comando seguente per copiare un BLOB specifico dal contenitore di origine a un'istanza di Archivio Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Ad esempio:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

	Verrà richiesto di immettere le credenziali per la sottoscrizione di Azure in cui si trova l'account di Archivio Data Lake. L'output visualizzato sarà simile al seguente:

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. È inoltre possibile copiare tutti i BLOB da un contenitore all'account di Archivio Data Lake tramite il comando seguente:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Ad esempio:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==



## Usare AdlCopy con un account di Analisi Data Lake

È anche possibile usare l'account di Analisi Data Lake per eseguire il processo AdlCopy per copiare dati da BLOB di Archiviazione di Azure ad Archivio Data Lake. In genere si sceglie questa opzione quando i dati da spostare sono nell'ordine di gigabyte e terabyte e si vuole una velocità effettiva delle prestazioni migliore e prevedibile.

Per usare l'account di Analisi Data Lake con AdlCopy, l'origine (BLOB di Archiviazione di Azure) e la destinazione (Archivio Azure Data Lake) devono essere aggiunte come origini dati per l'account di Analisi Data Lake. Per istruzioni sull'aggiunta di origini dati aggiuntive all'account di Analisi Data Lake, vedere [Gestire Analisi Data Lake tramite il portale di Azure](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Eseguire il comando seguente:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Ad esempio:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2

## Fatturazione

* Se si usa lo strumento AdlCopy in modalità autonoma, verranno fatturati i costi di uscita per lo spostamento dei dati, se l'account di origine di Archiviazione di Azure non si trova nella stessa area di Archivio Data Lake.

* Se si usa lo strumento AdlCopy con l'account di Analisi Data Lake, verranno applicati i [Prezzi di Analisi Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) standard.

## Considerazioni sull'uso di AdlCopy

* AdlCopy non supporta la copia dei dati da origini che complessivamente contengono più di 1000 file e cartelle. Un approccio alternativo può essere la distribuzione di file e cartelle in sottocartelle diverse e l'uso del percorso per tali sottocartelle come origine.

## Passaggi successivi

- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0413_2016-->
---
title: Trasformazione dell'origine nel mapping del flusso di datiSource transformation in mapping data flow
description: Informazioni su come impostare una trasformazione di origine nel mapping del flusso di dati.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: e4f1369aa850a244128da470aee81b2efc2e09be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413200"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Trasformazione dell'origine nel mapping del flusso di datiSource transformation in mapping data flow 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Una trasformazione origine configura l'origine dati per il flusso di dati. Quando si progettano flussi di dati, il primo passaggio sarà sempre la configurazione di una trasformazione di origine. Per aggiungere un'origine, fare clic sulla casella **Aggiungi origine** nell'area di disegno del flusso di dati.

Ogni flusso di dati richiede almeno una trasformazione di origine, ma è possibile aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire tali origini con una trasformazione join, ricerca o unione.

Ogni trasformazione di origine è associata esattamente a un set di dati di Data Factory.Each source transformation is associated with exactly one Data Factory dataset. Il set di dati definisce la forma e la posizione dei dati da scrivere o da cui si desidera leggere o da cui si desidera leggere. Se si usa un set di dati basato su file, è possibile usare caratteri jolly ed elenchi di file nell'origine per lavorare con più di un file alla volta.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Connettori di origine supportati nel mapping del flusso di datiSupported source connectors in mapping data flow

Il mapping del flusso di dati segue un approccio ELT (Extract, Load, Transform) e funziona con i set di dati di *gestione temporanea* che sono tutti in Azure.Mapping Data Flow follows an extract, load, transform (ELT) approach and works with staging datasets that are all in Azure. Attualmente è possibile usare i set di dati seguenti in una trasformazione di origine:Currently the following datasets can be used in a source transformation:
    
* [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, testo, parità)Azure Blob Storage (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, testo, parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, testo, parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB di AzureAzure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Le impostazioni specifiche di questi connettori si trovano nella scheda **Opzioni di origine.** Le informazioni su queste impostazioni si trovano nella documentazione del connettore. 

Azure Data Factory ha accesso a oltre [90 connettori nativi.](connector-overview.md) Per includere i dati di queste altre origini nel flusso di dati, usare l'attività di copia per caricare i dati in una delle aree di gestione temporanea supportate.

## <a name="source-settings"></a>Impostazioni origine

Dopo aver aggiunto un'origine, configurarla tramite la scheda **Impostazioni di origine.** Qui è possibile selezionare o creare il set di dati in corrispondenza dei punti di origine. È inoltre possibile selezionare le opzioni di schema e campionamento per i dati.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

**Connessione di prova:** Verificare se il servizio di spark del flusso di dati può connettersi correttamente al servizio collegato usato nel set di dati di origine. Per abilitare questa funzionalità, è necessario che la modalità di debug sia attivata.

**Deriva dello schema:** [Schema Drift](concepts-data-flow-schema-drift.md) è la capacità della factory di dati di gestire in modo nativo gli schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche alle colonne.

* Selezionare la casella **Consenti deriva schema** se le colonne di origine verranno modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* Scegliendo **Indulse i tipi di colonna derivia** verrà indicato la data factory di rilevare e definire i tipi di dati per ogni nuova colonna individuata. Con questa funzione disattivata, tutte le colonne alla deriva saranno di tipo stringa.

**Convalida schema:** Se è selezionata l'opzione Convalida schema, il flusso di dati non verrà eseguito se i dati di origine in ingresso non corrispondono allo schema definito del set di dati.

**Salta conteggio righe:** Il campo Salta conteggio righe specifica quante righe ignorare all'inizio del set di dati.

**Campionamento:** Abilitare il campionamento per limitare il numero di righe dall'origine. Utilizzare questa impostazione quando si testano o si di esempio dati dall'origine a scopo di debug.

**Righe multilinea:** Selezionare righe su più righe se il file di testo di origine contiene valori stringa che si estendono su più righe, ovvero nuove righe all'interno di un valore. Questa impostazione è disponibile solo nei set di dati DelimitedText.This setting is only available in DelimitedText datasets.

Per convalidare la corretta configurazione dell'origine, attivare la modalità di debug e recuperare un'anteprima dei dati. Per ulteriori informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando la modalità di debug è attivata, la configurazione del limite di riga nelle impostazioni di debug sovrascriverà l'impostazione di campionamento nell'origine durante l'anteprima dei dati.

## <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dai dati di origine. Per la maggior parte dei tipi di set di dati, ad esempio SQL e Parquet, la proiezione in un'origine è fissa per riflettere lo schema definito in un set di dati. Quando i file di origine non sono fortemente tipti (ad esempio, file flat csv anziché file di parquet), è possibile definire i tipi di dati per ogni campo nella trasformazione di origine.

![Impostazioni nella scheda Proiezione](media/data-flow/source3.png "Proiezione")

Se il file di testo non dispone di uno schema definito, selezionare **Rileva tipo di dati** in modo che Data Factory campioni e dedurrà i tipi di dati. Selezionare **Definisci formato predefinito** per rilevare automaticamente i formati dati predefiniti.

**Reimposta schema** reimposta la proiezione su ciò che è definito nel set di dati di riferimento.

È possibile modificare i tipi di dati della colonna in una trasformazione di colonna derivata del flusso in basso. Utilizzare una trasformazione di selezione per modificare i nomi delle colonne.

### <a name="import-schema"></a>Importa schema

Il pulsante **Importa schema** nella scheda **Proiezione** consente di utilizzare un cluster di debug attivo per creare una proiezione dello schema. Disponibile in ogni tipo di origine, l'importazione dello schema in questo campo sostituirà la proiezione definita nel set di dati. L'oggetto dataset non verrà modificato.

Ciò è utile in set di dati come Avro e CosmosDB che supportano strutture di dati complesse che non richiedono l'esistenza di definizioni di schema nel set di dati.

## <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origineOptimize the source transformation

Nella scheda **Ottimizza** per la trasformazione di origine potrebbe essere visualizzato un tipo di partizione di **origine.** Questa opzione è disponibile solo quando l'origine è Database SQL di Azure.This option is available only when your source is Azure SQL Database. Ciò è dovuto al fatto che Data Factory tenta di rendere parallele le connessioni per eseguire query di grandi dimensioni sull'origine del database SQL.

![Impostazioni della partizione di origine](media/data-flow/sourcepart3.png "partizionamento")

Non è necessario partizionare i dati nell'origine del database SQL, ma le partizioni sono utili per le query di grandi dimensioni. È possibile basare la partizione su una colonna o una query.

### <a name="use-a-column-to-partition-data"></a>Usare una colonna per partizionare i datiUse a column to partition data

Dalla tabella di origine, selezionare una colonna su cui eseguire la partizione. Impostare anche il numero di partizioni.

### <a name="use-a-query-to-partition-data"></a>Usare una query per partizionare i datiUse a query to partition data

È possibile scegliere di partizionare le connessioni in base a una query. Immettere il contenuto di un predicato WHERE. Ad esempio, immettere anno > 1980.

Per ulteriori informazioni sull'ottimizzazione all'interno del mapping del flusso di dati, vedere la [scheda Ottimizza](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passaggi successivi

Iniziare a creare una [trasformazione di colonna derivata](data-flow-derived-column.md) e una [trasformazione di selezione.](data-flow-select.md)

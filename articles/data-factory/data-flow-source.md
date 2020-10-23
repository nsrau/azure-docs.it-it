---
title: Trasformazione origine nel flusso di dati di mapping
description: Informazioni su come configurare una trasformazione di origine nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 0efcc82617c75089ff2bf396133f9fbc7ec701ea
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107692"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Trasformazione origine nel flusso di dati di mapping 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una trasformazione origine configura l'origine dati per il flusso di dati. Quando si progettano i flussi di dati, il primo passaggio consiste sempre nella configurazione di una trasformazione di origine. Per aggiungere un'origine, fare clic sulla casella **Aggiungi origine** nell'area di disegno del flusso di dati.

Ogni flusso di dati richiede almeno una trasformazione di origine, ma è possibile aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire tali origini insieme a una trasformazione join, Lookup o Union.

Ogni trasformazione di origine è associata esattamente a un set di dati o a un servizio collegato. Il set di dati definisce la forma e il percorso dei dati da cui si desidera eseguire la scrittura o la lettura. Se si usa un set di dati basato su file, è possibile usare i caratteri jolly e gli elenchi di file nell'origine per lavorare con più di un file alla volta.

## <a name="inline-datasets"></a>Set di impostazioni di set di righe

La prima decisione da prendere durante la creazione di una trasformazione di origine è se le informazioni sull'origine sono definite all'interno di un oggetto DataSet o nella trasformazione di origine. La maggior parte dei formati è disponibile solo in uno o nell'altro. Per informazioni su come usare un connettore specifico, fare riferimento al documento del connettore appropriato.

Quando un formato è supportato sia per inline che per un oggetto DataSet, sono disponibili entrambi i vantaggi. Gli oggetti DataSet sono entità riutilizzabili che possono essere utilizzate in altri flussi di dati e attività quali la copia. Queste sono particolarmente utili quando si usa uno schema finalizzato. I set di dati non sono basati su Spark e talvolta potrebbe essere necessario eseguire l'override di determinate impostazioni o proiezione dello schema nella trasformazione di origine.

I set di dati inline sono consigliati quando si utilizzano schemi flessibili, istanze di origine monouso o origini con parametri. Se l'origine è fortemente parametrizzata, i set di dati inline consentono di non creare un oggetto "fittizio". I set di dati inline sono basati su Spark e le relative proprietà sono native per il flusso di dati.

Per usare un set di dati inline, selezionare il formato desiderato nel selettore del **tipo di origine** . Anziché selezionare un set di dati di origine, selezionare il servizio collegato a cui si desidera connettersi.

![Set di dati inline](media/data-flow/inline-selector.png "Set di dati inline")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Tipi di origine supportati

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente i set di dati seguenti possono essere utilizzati in una trasformazione di origine:

| Connettore | Formato | Set di dati/inline |
| --------- | ------ | -------------- |
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (anteprima)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model (anteprima)](format-common-data-model.md#source-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (anteprima)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Istanza gestita SQL di Azure (anteprima)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Le impostazioni specifiche di questi connettori si trovano nella scheda **Opzioni di origine** . Esempi di script di flusso di dati e informazioni su queste impostazioni sono disponibili nella documentazione del connettore. 

Azure Data Factory può accedere a più di [90 connettori nativi](connector-overview.md). Per includere dati da tali origini nel flusso di dati, usare l'attività di copia per caricare i dati in una delle aree di gestione temporanea supportate.

## <a name="source-settings"></a>Impostazioni origine

Dopo aver aggiunto un'origine, configurare tramite la scheda **impostazioni di origine** . Qui è possibile selezionare o creare il set di dati a cui si riportano i punti di origine. È anche possibile selezionare le opzioni relative allo schema e al campionamento per i dati. 

I valori di sviluppo per i parametri del set di dati possono essere configurati nelle [impostazioni di debug](concepts-data-flow-debug-mode.md) (richiede l'accensione della modalità di debug).

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

**Nome flusso di output:** Nome della trasformazione di origine.

**Tipo di origine:** Scegliere se si desidera utilizzare un set di dati inline o un oggetto DataSet esistente.

**Test connessione:** Verificare se il servizio Spark del flusso di dati è in grado di connettersi al servizio collegato usato nel set di dati di origine. Per abilitare questa funzionalità, è necessario attivare la modalità di debug.

**Schema Drift:** la [deriva dello schema](concepts-data-flow-schema-drift.md) è data factory capacità di gestire in modo nativo schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne.

* Controllare la casella **Consenti la deviazione dello schema** se le colonne di origine vengono modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* La scelta di **dedurre i tipi di colonna** derivati indicherà data factory per rilevare e definire i tipi di dati per ogni nuova colonna individuata. Se questa funzionalità è disattivata, tutte le colonne in sequenza saranno di tipo stringa.

**Convalida schema:** Se Convalida schema è selezionata, il flusso di dati non verrà eseguito se i dati di origine in ingresso non corrispondono allo schema definito del set di dati.

**Ignora conteggio righe:** Il campo Skip line count specifica il numero di righe da ignorare all'inizio del set di dati.

**Campionamento:** Abilitare il campionamento per limitare il numero di righe dall'origine. Usare questa impostazione quando si testano o si campionano i dati dall'origine a scopo di debug.

Per convalidare che l'origine sia configurata correttamente, attivare la modalità di debug e recuperare un'anteprima dei dati. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando la modalità di debug è attivata, la configurazione del limite di righe nelle impostazioni di debug sovrascriverà l'impostazione di campionamento nell'origine durante l'anteprima dei dati.

## <a name="source-options"></a>Opzioni origine

La scheda Opzioni di origine contiene impostazioni specifiche per il connettore e il formato scelti. Per ulteriori informazioni ed esempi, fare riferimento alla [documentazione del connettore](#supported-sources)pertinente.

## <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dei dati di origine. Per la maggior parte dei tipi di set di dati, ad esempio SQL e parquet, la proiezione in un'origine è fissa per riflettere lo schema definito in un set di dati. Quando i file di origine non sono fortemente tipizzati, ad esempio file con estensione CSV flat anziché file parquet, è possibile definire i tipi di dati per ogni campo nella trasformazione di origine.

![Impostazioni nella scheda proiezione](media/data-flow/source3.png "Proiezione")

Se nel file di testo non è definito alcuno schema, selezionare **rileva tipo di dati** in modo che data factory campionare e dedurre i tipi di dati. Selezionare **Definisci il formato predefinito** per rilevare automaticamente i formati di dati predefiniti.

**Reimposta schema Reimposta** la proiezione su ciò che viene definito nel set di dati a cui si fa riferimento.

È possibile modificare i tipi di dati delle colonne in una trasformazione di colonna derivata da un flusso inattivo. Utilizzare una trasformazione seleziona per modificare i nomi delle colonne.

### <a name="import-schema"></a>Importa schema

Il pulsante **Importa schema** nella scheda **proiezione** consente di usare un cluster di debug attivo per creare una proiezione dello schema. Disponibile in ogni tipo di origine, l'importazione dello schema qui sostituirà la proiezione definita nel set di dati. L'oggetto DataSet non verrà modificato.

Questa operazione è utile nei set di dati come Avro e Azure Cosmos DB che supportano strutture di dati complesse non richiedono la presenza di definizioni dello schema nel set di dati. Per i set di righe in linea, questo è l'unico modo per fare riferimento ai metadati della colonna senza la deriva dello schema.

## <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origine

La scheda **ottimizza** consente di modificare le informazioni sulle partizioni a ogni passaggio della trasformazione. Nella maggior parte dei casi, l' **utilizzo del partizionamento corrente** viene ottimizzato per la struttura di partizionamento ideale per un'origine.

Se si esegue la lettura da un'origine del database SQL di Azure, il partizionamento di **origine** personalizzato leggerà probabilmente i dati più velocemente. ADF leggerà query di grandi dimensioni rendendo le connessioni al database in parallelo. Questo partizionamento di origine può essere eseguito su una colonna o utilizzando una query.

![Impostazioni partizione di origine](media/data-flow/sourcepart3.png "partizionamento")

Per ulteriori informazioni sull'ottimizzazione all'interno del flusso di dati di mapping, vedere la [scheda Optimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passaggi successivi

Iniziare a compilare il flusso di dati con una [trasformazione colonna derivata](data-flow-derived-column.md) e una [trasformazione selezione](data-flow-select.md).

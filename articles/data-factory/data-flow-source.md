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
ms.openlocfilehash: 80280702748040e12d1d3d048644e6a16c926256
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832382"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Trasformazione origine nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una trasformazione origine configura l'origine dati per il flusso di dati. Quando si progettano i flussi di dati, il primo passaggio consiste sempre nella configurazione di una trasformazione di origine. Per aggiungere un'origine, selezionare la casella **Aggiungi origine** nell'area di disegno del flusso di dati.

Ogni flusso di dati richiede almeno una trasformazione di origine, ma è possibile aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire tali origini insieme a una trasformazione join, Lookup o Union.

Ogni trasformazione di origine è associata esattamente a un set di dati o a un servizio collegato. Il set di dati definisce la forma e il percorso dei dati da cui si desidera eseguire la scrittura o la lettura. Se si usa un set di dati basato su file, è possibile usare i caratteri jolly e gli elenchi di file nell'origine per lavorare con più di un file alla volta.

## <a name="inline-datasets"></a>Set di impostazioni di set di righe

La prima decisione da effettuare quando si crea una trasformazione di origine è se le informazioni sull'origine sono definite all'interno di un oggetto DataSet o nella trasformazione di origine. La maggior parte dei formati è disponibile solo in uno o nell'altro. Per informazioni su come usare un connettore specifico, vedere il documento connettore appropriato.

Quando un formato è supportato sia per inline che per un oggetto DataSet, sono disponibili entrambi i vantaggi. Gli oggetti DataSet sono entità riutilizzabili che possono essere utilizzate in altri flussi di dati e attività, ad esempio la copia. Queste entità riutilizzabili sono particolarmente utili quando si utilizza uno schema con protezione avanzata. I set di impostazioni non sono basati su Spark. In alcuni casi potrebbe essere necessario eseguire l'override di determinate impostazioni o proiezione dello schema nella trasformazione di origine.

I set di dati inline sono consigliati quando si utilizzano schemi flessibili, istanze di origine monouso o origini con parametri. Se l'origine è fortemente parametrizzata, i set di dati inline non consentono di creare un oggetto "fittizio". I set di dati inline sono basati su Spark e le relative proprietà sono native per il flusso di dati.

Per usare un set di dati inline, selezionare il formato desiderato nel selettore del **tipo di origine** . Anziché selezionare un set di dati di origine, selezionare il servizio collegato a cui si desidera connettersi.

![Screenshot che mostra la selezione inline.](media/data-flow/inline-selector.png "Screenshot che mostra la selezione inline.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Tipi di origine supportati

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente, i set di dati seguenti possono essere utilizzati in una trasformazione di origine.

| Connettore | Formato | Set di dati/inline |
| --------- | ------ | -------------- |
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (anteprima)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model (anteprima)](format-common-data-model.md#source-properties)<br>[Testo delimitato](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta (anteprima)](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Istanza gestita SQL di Azure (anteprima)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ | 
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Le impostazioni specifiche di questi connettori si trovano nella scheda **Opzioni di origine** . Esempi di script di flusso di dati e informazioni su queste impostazioni sono disponibili nella documentazione del connettore.

Azure Data Factory ha accesso a più di [90 connettori nativi](connector-overview.md). Per includere dati da tali origini nel flusso di dati, usare l'attività di copia per caricare i dati in una delle aree di gestione temporanea supportate.

## <a name="source-settings"></a>Impostazioni origine

Dopo aver aggiunto un'origine, configurare tramite la scheda **impostazioni di origine** . Qui è possibile selezionare o creare il set di dati a cui si riportano i punti di origine. È anche possibile selezionare le opzioni relative allo schema e al campionamento per i dati.

È possibile configurare i valori di sviluppo per i parametri del set di dati nelle [impostazioni di debug](concepts-data-flow-debug-mode.md). (La modalità di debug deve essere attivata).

![Screenshot che mostra la scheda Impostazioni di origine.](media/data-flow/source1.png "Screenshot che mostra la scheda Impostazioni di origine.")

**Nome del flusso di output**: nome della trasformazione di origine.

**Tipo di origine**: scegliere se si desidera utilizzare un set di dati inline o un oggetto DataSet esistente.

**Test connessione**: verificare se il servizio Spark del flusso di dati è in grado di connettersi al servizio collegato usato nel set di dati di origine. Per abilitare questa funzionalità, è necessario attivare la modalità di debug.

**Schema Drift**: la [deriva dello schema](concepts-data-flow-schema-drift.md) è la capacità di data factory di gestire in modo nativo gli schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne.

* Selezionare la casella di controllo **Consenti la deriva dello schema** se le colonne di origine vengono modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* Selezionando **deduci tipi di colonna** derivati viene indicato data factory di rilevare e definire i tipi di dati per ogni nuova colonna individuata. Se questa funzionalità è disattivata, tutte le colonne in sequenza saranno di tipo stringa.

**Convalida schema:** Se **convalida schema** è selezionata, il flusso di dati non verrà eseguito se i dati di origine in ingresso non corrispondono allo schema definito del set di dati.

**Ignora conteggio** righe: il campo **Skip line count** specifica il numero di righe da ignorare all'inizio del set di dati.

**Campionamento**: abilitare il **campionamento** per limitare il numero di righe dall'origine. Usare questa impostazione quando si testano o si campionano i dati dall'origine a scopo di debug.

Per convalidare che l'origine sia configurata correttamente, attivare la modalità di debug e recuperare un'anteprima dei dati. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando la modalità di debug è attivata, la configurazione del limite di righe nelle impostazioni di debug sovrascriverà l'impostazione di campionamento nell'origine durante l'anteprima dei dati.

## <a name="source-options"></a>Opzioni origine

La scheda **Opzioni di origine** contiene impostazioni specifiche per il connettore e il formato scelti. Per ulteriori informazioni ed esempi, vedere la [documentazione del connettore](#supported-sources)pertinente.

## <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dei dati di origine. Per la maggior parte dei tipi di set di dati, ad esempio SQL e parquet, la proiezione in un'origine è fissa per riflettere lo schema definito in un set di dati. Quando i file di origine non sono fortemente tipizzati, ad esempio file flat. csv anziché file parquet, è possibile definire i tipi di dati per ogni campo nella trasformazione di origine.

![Screenshot che mostra le impostazioni nella scheda proiezione.](media/data-flow/source3.png "Screenshot che mostra le impostazioni nella scheda proiezione.")

Se nel file di testo non è definito alcuno schema, selezionare **rileva tipo di dati** in modo che data factory campionare e dedurre i tipi di dati. Selezionare **Definisci il formato predefinito** per rilevare automaticamente i formati di dati predefiniti.

**Reimposta schema Reimposta** la proiezione su ciò che viene definito nel set di dati a cui si fa riferimento.

È possibile modificare i tipi di dati delle colonne in una trasformazione colonna derivata downstream. Utilizzare una trasformazione seleziona per modificare i nomi delle colonne.

### <a name="import-schema"></a>Importa schema

Selezionare il pulsante **Importa schema** nella scheda **proiezione** per usare un cluster di debug attivo per creare una proiezione dello schema. È disponibile in ogni tipo di origine. L'importazione dello schema qui sostituirà la proiezione definita nel set di dati. L'oggetto DataSet non verrà modificato.

L'importazione dello schema è utile nei set di dati come Avro e Azure Cosmos DB che supportano strutture di dati complesse che non richiedono la presenza di definizioni dello schema nel set di dati. Per i set di righe inline, l'importazione dello schema è l'unico modo per fare riferimento ai metadati della colonna senza la deriva dello schema.

## <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origine

La scheda **ottimizza** consente di modificare le informazioni sulle partizioni a ogni passaggio della trasformazione. Nella maggior parte dei casi, l' **utilizzo del partizionamento corrente** viene ottimizzato per la struttura di partizionamento ideale per un'origine.

Se si esegue la lettura da un'origine del database SQL di Azure, il partizionamento di **origine** personalizzato leggerà probabilmente i dati più velocemente. Data Factory leggerà le query di grandi dimensioni rendendo le connessioni al database in parallelo. Questo partizionamento di origine può essere eseguito su una colonna o utilizzando una query.

![Screenshot che mostra le impostazioni della partizione di origine.](media/data-flow/sourcepart3.png "Screenshot che mostra le impostazioni della partizione di origine.")

Per ulteriori informazioni sull'ottimizzazione all'interno del flusso di dati di mapping, vedere la [scheda Optimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passaggi successivi

Iniziare a compilare il flusso di dati con una [trasformazione colonna derivata](data-flow-derived-column.md) e una [trasformazione selezione](data-flow-select.md).

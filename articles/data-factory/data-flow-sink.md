---
title: Trasformazione sink nel flusso di dati di mapping
description: Informazioni su come configurare una trasformazione sink nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/17/2020
ms.openlocfilehash: d45f5d5d1d61372ed959334519aa865c22d70748
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832501"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trasformazione sink nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dopo aver completato la trasformazione dei dati, scriverli in un archivio di destinazione usando la trasformazione sink. Ogni flusso di dati richiede almeno una trasformazione sink, ma è possibile scrivere in tutti i sink necessari per completare il flusso di trasformazione. Per scrivere in sink aggiuntivi, creare nuovi flussi tramite nuovi rami e divisioni condizionali.

Ogni trasformazione sink è associata esattamente a un oggetto Azure Data Factory DataSet o a un servizio collegato. La trasformazione sink determina la forma e la posizione dei dati in cui si desidera scrivere.

## <a name="inline-datasets"></a>Set di impostazioni di set di righe

Quando si crea una trasformazione sink, scegliere se le informazioni sul sink sono definite all'interno di un oggetto DataSet o nella trasformazione sink. La maggior parte dei formati è disponibile solo in uno o nell'altro. Per informazioni su come usare un connettore specifico, vedere il documento connettore appropriato.

Quando un formato è supportato sia per inline che per un oggetto DataSet, sono disponibili entrambi i vantaggi. Gli oggetti DataSet sono entità riutilizzabili che possono essere utilizzate in altri flussi di dati e attività, ad esempio la copia. Queste entità riutilizzabili sono particolarmente utili quando si utilizza uno schema con protezione avanzata. I set di impostazioni non sono basati su Spark. In alcuni casi potrebbe essere necessario eseguire l'override di determinate impostazioni o proiezione dello schema nella trasformazione sink.

I set di impostazioni di set di righe sono consigliati quando si utilizzano schemi flessibili, istanze di sink unidirezionali o sink con parametri. Se il sink è fortemente parametrizzato, i set di righe inline consentono di non creare un oggetto "fittizio". I set di dati inline sono basati su Spark e le relative proprietà sono native per il flusso di dati.

Per usare un set di dati inline, selezionare il formato desiderato nel selettore del **tipo di sink** . Anziché selezionare un set di dati sink, è necessario selezionare il servizio collegato a cui si desidera connettersi.

![Screenshot che mostra la selezione inline.](media/data-flow/inline-selector.png "Screenshot che mostra la selezione inline.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Tipi di sink supportati

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente, i set di dati seguenti possono essere utilizzati in una trasformazione di origine.

| Connettore | Formato | Set di dati/inline |
| --------- | ------ | -------------- |
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (anteprima)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (anteprima)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (anteprima)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Istanza gestita SQL di Azure (anteprima)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Cosmos DB (API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Le impostazioni specifiche di questi connettori si trovano nella scheda **Impostazioni** . Esempi di script di flusso di dati e informazioni su queste impostazioni sono disponibili nella documentazione del connettore.

Azure Data Factory ha accesso a più di [90 connettori nativi](connector-overview.md). Per scrivere i dati nelle altre origini dal flusso di dati, usare l'attività di copia per caricare i dati da un sink supportato.

## <a name="sink-settings"></a>Impostazioni sink

Dopo aver aggiunto un sink, configurare tramite la scheda **sink** . Qui è possibile selezionare o creare il set di dati in cui il sink scrive. È possibile configurare i valori di sviluppo per i parametri del set di dati nelle [impostazioni di debug](concepts-data-flow-debug-mode.md). (La modalità di debug deve essere attivata).

Il video seguente illustra alcune opzioni di sink diverse per i tipi di file delimitati da testo.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Screenshot che mostra le impostazioni del sink.](media/data-flow/sink-settings.png "Screenshot che mostra le impostazioni del sink.")

**Schema Drift**: la [deriva dello schema](concepts-data-flow-schema-drift.md) è la capacità di data factory di gestire in modo nativo gli schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne. Abilitare **Consenti Drift schema** per scrivere colonne aggiuntive sopra a quanto definito nello schema dei dati sink.

**Convalida schema**: se è selezionata l'opzione Convalida schema, il flusso di dati avrà esito negativo se una colonna dello schema di origine in ingresso non viene trovata nella proiezione di origine o se i tipi di dati non corrispondono. Usare questa impostazione per applicare che i dati di origine soddisfano il contratto della proiezione definita. È utile negli scenari di origine del database per segnalare che i nomi o i tipi di colonna sono stati modificati.

## <a name="cache-sink"></a>Sink della cache

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]
 
Un *sink della cache* si verifica quando un flusso di dati scrive i dati nella cache Spark anziché in un archivio dati. Nei flussi di dati di mapping, è possibile fare riferimento a questi dati nello stesso flusso più volte usando una *ricerca nella cache*. Questa operazione è utile quando si desidera fare riferimento ai dati come parte di un'espressione, ma non si desidera aggiungervi esplicitamente le colonne. Esempi comuni in cui un sink della cache può essere utile per cercare un valore massimo in un archivio dati e i codici di errore corrispondenti a un database di messaggi di errore. 

Per scrivere in un sink della cache, aggiungere una trasformazione sink e selezionare **cache** come tipo di sink. A differenza di altri tipi di sink, non è necessario selezionare un set di dati o un servizio collegato perché non si sta scrivendo in un archivio esterno. 

![Selezionare il sink della cache](media/data-flow/select-cache-sink.png "Selezionare il sink della cache")

Nelle impostazioni del sink è possibile specificare facoltativamente le colonne chiave del sink della cache. Questi vengono usati come condizioni di corrispondenza quando si usa la `lookup()` funzione in una ricerca nella cache. Se si specificano colonne chiave, non è possibile usare la `outputs()` funzione in una ricerca nella cache. Per ulteriori informazioni sulla sintassi di ricerca nella cache, vedere [ricerche memorizzate nella cache](concepts-data-flow-expression-builder.md#cached-lookup).

![Colonne chiave del sink della cache](media/data-flow/cache-sink-key-columns.png "Colonne chiave del sink della cache")

Se, ad esempio, si specifica una singola colonna chiave di `column1` in un sink della cache denominato `cacheExample` , la chiamata a `cacheExample#lookup()` avrà un parametro che consente di specificare la riga nel sink della cache in base alla quale trovare la corrispondenza. La funzione restituisce una singola colonna complessa con le sottocolonne per ogni colonna mappata.

> [!NOTE]
> Un sink della cache deve trovarsi in un flusso di dati completamente indipendente da qualsiasi trasformazione che vi fa riferimento tramite una ricerca nella cache. Un sink della cache deve anche il primo sink scritto. 

## <a name="field-mapping"></a>Mapping campi

Analogamente a una trasformazione Select, nella scheda **mapping** del sink è possibile decidere quali colonne in entrata verranno scritte. Per impostazione predefinita, viene eseguito il mapping di tutte le colonne di input, incluse le colonne trascinate. Questo comportamento è noto come *mapping di automapping*.

Quando si disattiva il mapping di autonomi, è possibile aggiungere mapping basati su colonne fisse o mapping basati su regole. Con i mapping basati su regole è possibile scrivere espressioni con criteri di ricerca. Il mapping fisso esegue il mapping dei nomi delle colonne logiche e fisiche. Per ulteriori informazioni sul mapping basato su regole, vedere la pagina relativa ai [modelli di colonna nel flusso di dati di mapping](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Ordinamento di sink personalizzato

Per impostazione predefinita, i dati vengono scritti in più sink in un ordine non deterministico. Il motore di esecuzione scrive i dati in parallelo quando la logica di trasformazione viene completata e l'ordinamento del sink può variare a seconda dell'esecuzione. Per specificare l'ordinamento di un sink esatto, abilitare l' **ordinamento del sink personalizzato** nella scheda **generale** del flusso di dati. Se abilitati, i sink vengono scritti in sequenza in ordine crescente.

![Screenshot che mostra l'ordinamento di sink personalizzato.](media/data-flow/custom-sink-ordering.png "Screenshot che mostra l'ordinamento di sink personalizzato.")

> [!NOTE]
> Quando si utilizzano le [ricerche memorizzate nella cache](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder#cached-lookup), assicurarsi che l'ordine dei sink includa i sink memorizzati nella cache impostati su 1, il più basso (o primo) nell'ordinamento.

![Ordinamento di sink personalizzato](media/data-flow/cache-2.png "Ordinamento di sink personalizzato")

## <a name="error-row-handling"></a>Gestione delle righe con errori

Quando si scrive nei database, alcune righe di dati potrebbero non riuscire a causa di vincoli impostati dalla destinazione. Per impostazione predefinita, l'esecuzione di un flusso di dati avrà esito negativo al primo errore ottenuto. In alcuni connettori è possibile scegliere di **continuare in** caso di errore che consente il completamento del flusso di dati anche se le singole righe contengono errori. Attualmente questa funzionalità è disponibile solo nel database SQL di Azure. Per altre informazioni, vedere [gestione delle righe con errori nel database SQL di Azure](connector-azure-sql-database.md#error-row-handling).

## <a name="data-preview-in-sink"></a>Anteprima dati in sink

Quando si recupera un'anteprima dei dati in un cluster di debug, non vengono scritti dati nel sink. Verrà restituito uno snapshot dell'aspetto dei dati, ma non verrà scritto alcun elemento nella destinazione. Per testare la scrittura dei dati nel sink, eseguire un debug della pipeline dall'area di disegno della pipeline.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato creato il flusso di dati, aggiungere un' [attività flusso di dati alla pipeline](concepts-data-flow-overview.md).

---
title: Trasformazione sink nel flusso di dati di mapping
description: Informazioni su come configurare una trasformazione sink nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/27/2020
ms.openlocfilehash: 06d70012756694dca1fad8fa90db0293bb106bf9
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828144"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trasformazione sink nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dopo aver completato la trasformazione dei dati, scriverli in un archivio di destinazione usando la trasformazione sink. Ogni flusso di dati richiede almeno una trasformazione sink, ma è possibile scrivere in tutti i sink necessari per completare il flusso di trasformazione. Per scrivere in sink aggiuntivi, creare nuovi flussi tramite nuovi rami e divisioni condizionali.

Ogni trasformazione sink è associata esattamente a un oggetto Azure Data Factory DataSet o a un servizio collegato. La trasformazione sink determina la forma e la posizione dei dati in cui si desidera scrivere.

## <a name="inline-datasets"></a>Set di impostazioni di set di righe

Quando si crea una trasformazione sink, scegliere se le informazioni sul sink sono definite all'interno di un oggetto DataSet o nella trasformazione sink. La maggior parte dei formati è disponibile solo in uno o nell'altro. Per informazioni su come usare un connettore specifico, fare riferimento al documento del connettore appropriato.

Quando un formato è supportato sia per inline che per un oggetto DataSet, sono disponibili entrambi i vantaggi. Gli oggetti DataSet sono entità riutilizzabili che possono essere utilizzate in altri flussi di dati e attività quali la copia. Queste sono particolarmente utili quando si usa uno schema finalizzato. I set di impostazioni non sono basati su Spark e talvolta potrebbe essere necessario eseguire l'override di determinate impostazioni o proiezione dello schema nella trasformazione sink.

I set di impostazioni di set di righe sono consigliati quando si utilizzano schemi flessibili, istanze di sink monouso o sink con parametri. Se il sink è fortemente parametrizzato, i set di impostazioni inline consentono di non creare un oggetto "fittizio". I set di dati inline sono basati su Spark e le relative proprietà sono native per il flusso di dati.

Per usare un set di dati inline, selezionare il formato desiderato nel selettore del **tipo di sink** . Anziché selezionare un set di dati sink, è necessario selezionare il servizio collegato a cui si desidera connettersi.

![Set di dati inline](media/data-flow/inline-selector.png "Set di dati inline")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Tipi di sink supportati

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente i set di dati seguenti possono essere utilizzati in una trasformazione di origine:

| Connettore | Formato | Set di dati/inline |
| --------- | ------ | -------------- |
| [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (anteprima)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br> ✓/- <br> ✓/- <br>✓/✓<br> ✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [JSON](format-json.md#mapping-data-flow-properties) <br> [Avro](format-avro.md#mapping-data-flow-properties) <br> [Testo delimitato](format-delimited-text.md#mapping-data-flow-properties) <br> [Delta (anteprima)](format-delta.md) <br> [ORC](format-orc.md#mapping-data-flow-properties)<br/> [Parquet](format-parquet.md#mapping-data-flow-properties)  <br> [Common Data Model (anteprima)](format-common-data-model.md#sink-properties) | ✓/- <br> ✓/- <br> ✓/- <br> -/✓ <br>✓/✓<br> ✓/- <br> -/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure CosmosDB (API SQL)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Le impostazioni specifiche di questi connettori si trovano nella scheda **Impostazioni** . Esempi di script di flusso di dati e informazioni su queste impostazioni sono disponibili nella documentazione del connettore. 

Azure Data Factory può accedere a più di [90 connettori nativi](connector-overview.md). Per scrivere i dati nelle altre origini dal flusso di dati, usare l'attività di copia per caricare i dati da un sink supportato.

## <a name="sink-settings"></a>Impostazioni sink

Una volta aggiunto un sink, configurare tramite la scheda **sink** . Qui è possibile selezionare o creare il set di dati in cui il sink scrive. I valori di sviluppo per i parametri del set di dati possono essere configurati nelle [impostazioni di debug](concepts-data-flow-debug-mode.md) (richiede l'accensione della modalità di debug).

Di seguito è riportato un video che illustra una serie di opzioni di sink diverse per i tipi di file delimitati da testo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Impostazioni sink](media/data-flow/sink-settings.png "Impostazioni sink")

**Schema Drift:** la [deriva dello schema](concepts-data-flow-schema-drift.md) è data factory capacità di gestire in modo nativo schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne. Enable **schema Drift consente** di scrivere colonne aggiuntive oltre a quanto definito nello schema dei dati sink.

**Convalida schema:** Se Convalida schema è selezionata, il flusso di dati avrà esito negativo se una colonna dello schema di origine in ingresso non viene trovata nella proiezione di origine o se i tipi di dati non corrispondono. Usare questa impostazione per applicare che i dati di origine soddisfano il contratto della proiezione definita. È molto utile negli scenari di origine del database per segnalare che i nomi o i tipi di colonna sono stati modificati.

## <a name="field-mapping"></a>Mapping campi

Analogamente a una trasformazione Select, nella scheda **mapping** del sink è possibile decidere quali colonne in entrata verranno scritte. Per impostazione predefinita, viene eseguito il mapping di tutte le colonne di input, incluse le colonne trascinate. Questa operazione è nota come **mapping automatico**.

Quando si disattiva il mapping automatico, sarà possibile aggiungere mapping basati su colonne fisse o mapping basati su regole. I mapping basati su regole consentono di scrivere espressioni con criteri di ricerca, mentre il mapping fisso eseguirà il mapping dei nomi delle colonne logiche e fisiche. Per ulteriori informazioni sul mapping basato su regole, vedere la pagina relativa ai [modelli di colonna nel flusso di dati di mapping](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Ordinamento di sink personalizzato

Per impostazione predefinita, i dati vengono scritti in più sink in un ordine non deterministico. Il motore di esecuzione scriverà i dati in parallelo quando la logica di trasformazione viene completata e l'ordinamento del sink può variare a seconda dell'esecuzione. Per specificare l'ordinamento del sink esatto, abilitare l' **ordinamento del sink personalizzato** nella scheda generale del flusso di dati. Se abilitati, i sink verranno scritti in sequenza in ordine crescente.

![Ordinamento di sink personalizzato](media/data-flow/custom-sink-ordering.png "Ordinamento di sink personalizzato")

## <a name="data-preview-in-sink"></a>Anteprima dati in sink

Quando si recupera un'anteprima dei dati in un cluster di debug, non vengono scritti dati nel sink. Verrà restituito uno snapshot dell'aspetto dei dati, ma non verrà scritto alcun elemento nella destinazione. Per testare la scrittura dei dati nel sink, eseguire un debug della pipeline dall'area di disegno della pipeline.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato creato il flusso di dati, aggiungere un' [attività flusso di dati alla pipeline](concepts-data-flow-overview.md).

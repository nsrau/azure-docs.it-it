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
ms.date: 12/12/2019
ms.openlocfilehash: 1c65a456270cdca345504c07b927a7ef7e1f725b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440285"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trasformazione sink nel flusso di dati di mapping

Dopo aver trasformato i dati, è possibile affondare i dati in un set di dati di destinazione. Ogni flusso di dati richiede almeno una trasformazione sink, ma è possibile scrivere in tutti i sink necessari per completare il flusso di trasformazione. Per scrivere in sink aggiuntivi, creare nuovi flussi tramite nuovi rami e divisioni condizionali.

Ogni trasformazione sink è associata esattamente a un set di dati Data Factory. Il set di dati definisce la forma e la posizione dei dati in cui si desidera scrivere.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Connettori sink supportati nel flusso di dati di mapping

Attualmente i set di impostazioni seguenti possono essere utilizzati in una trasformazione sink:
    
* [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Analisi delle sinapsi di Azure](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Le impostazioni specifiche di questi connettori si trovano nella scheda **Impostazioni** . le informazioni su queste impostazioni sono disponibili nella documentazione del connettore. 

Azure Data Factory ha accesso a oltre [90 connettori nativi](connector-overview.md). Per scrivere i dati nelle altre origini dal flusso di dati, usare l'attività di copia per caricare i dati da una delle aree di gestione temporanea supportate dopo il completamento del flusso di dati.

## <a name="sink-settings"></a>Impostazioni sink

Una volta aggiunto un sink, configurare tramite la scheda **sink** . Qui è possibile selezionare o creare il set di dati in cui viene scritto il sink 

![Impostazioni sink](media/data-flow/sink-settings.png "Impostazioni sink")

**Schema Drift:** la [deriva dello schema](concepts-data-flow-schema-drift.md) è data factory capacità di gestire in modo nativo schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne. Enable **schema Drift consente** di scrivere colonne aggiuntive oltre a quanto definito nello schema dei dati sink.

**Convalida schema:** Se è selezionata l'opzione Convalida schema, il flusso di dati avrà esito negativo se non viene trovata alcuna colonna nello schema definito del set di dati.

## <a name="field-mapping"></a>Mapping dei campi

Analogamente a una trasformazione Select, nella scheda **mapping** del sink è possibile decidere quali colonne in entrata verranno scritte. Per impostazione predefinita, viene eseguito il mapping di tutte le colonne di input, incluse le colonne trascinate. Questa operazione è nota come **mapping automatico**.

Quando si disattiva il mapping automatico, sarà possibile aggiungere mapping basati su colonne fisse o mapping basati su regole. I mapping basati su regole consentono di scrivere espressioni con criteri di ricerca, mentre il mapping fisso eseguirà il mapping dei nomi delle colonne logiche e fisiche. Per ulteriori informazioni sul mapping basato su regole, vedere la pagina relativa ai [modelli di colonna nel flusso di dati di mapping](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="data-preview-in-sink"></a>Anteprima dati in sink

Quando si recupera un'anteprima dei dati in un cluster di debug, non vengono scritti dati nel sink. Verrà restituito uno snapshot dell'aspetto dei dati, ma non verrà scritto alcun elemento nella destinazione. Per testare la scrittura dei dati nel sink, eseguire un debug della pipeline dall'area di disegno della pipeline.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato creato il flusso di dati, aggiungere un' [attività flusso di dati alla pipeline](concepts-data-flow-overview.md).

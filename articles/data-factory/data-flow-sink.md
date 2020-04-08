---
title: Trasformazione sink nel mapping del flusso di datiSink transformation in mapping data flow
description: Informazioni su come configurare una trasformazione sink nel mapping del flusso di dati.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 8941c097fbb2d05c3a28be87d216b7a2679ebc68
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804895"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trasformazione sink nel mapping del flusso di datiSink transformation in mapping data flow

Dopo aver trasformato i dati, è possibile inserirla in un set di dati di destinazione. Ogni flusso di dati richiede almeno una trasformazione sink, ma è possibile scrivere in tutti i sink necessari per completare il flusso di trasformazione. Per scrivere in sink aggiuntivi, creare nuovi flussi tramite nuovi rami e suddivisioni condizionali.

Ogni trasformazione sink è associata esattamente a un set di dati di Data Factory.Each sink transformation is associated with exactly one Data Factory dataset. Il set di dati definisce la forma e la posizione dei dati in cui si desidera scrivere.

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>Connettori sink supportati nel mapping del flusso di datiSupported sink connectors in mapping data flow

Attualmente è possibile usare i set di dati seguenti in una trasformazione sink:Currently the following datasets can be used in a sink transformation:
    
* [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, Avro, testo, parità)Azure Blob Storage (JSON, Avro, Text, Parquet)
* [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, Avro, testo, parquet)
* [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, Avro, testo, parquet)
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [CosmosDB di AzureAzure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Le impostazioni specifiche di questi connettori si trovano nella scheda **Impostazioni.** Le informazioni su queste impostazioni si trovano nella documentazione del connettore. 

Azure Data Factory ha accesso a oltre [90 connettori nativi.](connector-overview.md) Per scrivere dati in tali altre origini dal flusso di dati, usare l'attività di copia per caricare i dati da una delle aree di gestione temporanea supportate dopo il completamento del flusso di dati.

## <a name="sink-settings"></a>Impostazioni del lavandino

Dopo aver aggiunto un sink, configurarlo tramite la scheda **Sink.** Qui è possibile selezionare o creare il set di dati in cui scrive il sink. Di seguito è riportato un video che spiega una serie di diverse opzioni Sink per i tipi di file delimitati da testo:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![Impostazioni del lavandino](media/data-flow/sink-settings.png "Impostazioni sink")

**Deriva dello schema:** [Schema Drift](concepts-data-flow-schema-drift.md) è la capacità della factory di dati di gestire in modo nativo gli schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche alle colonne. Abilitare **Consenti alla deriva dello schema** di scrivere colonne aggiuntive sopra ciò che è definito nello schema dei dati sink.

**Convalida schema:** Se è selezionata l'opzione Convalida schema, il flusso di dati avrà esito negativo se una colonna dello schema di origine in ingresso non viene trovata nella proiezione di origine o se i tipi di dati non corrispondono. Utilizzare questa impostazione per imporre che i dati di origine soddisfino il contratto della proiezione definita. È molto utile negli scenari di origine del database per segnalare che i nomi o i tipi di colonna sono stati modificati.

## <a name="field-mapping"></a>Mapping campi

Analogamente a una trasformazione Seleziona, nella scheda **Mapping** del sink è possibile decidere quali colonne in ingresso verranno scritte. Per impostazione predefinita, vengono mappate tutte le colonne di input, incluse le colonne con deriva. Questa operazione è nota come **mapping automatico**.

Quando si disattiva il mapping automatico, è possibile aggiungere mapping fissi basati su colonne o basati su regole. I mapping basati su regole consentono di scrivere espressioni con criteri di ricerca, mentre il mapping fisso esiterà i nomi di colonna logici e fisici. Per ulteriori informazioni sul mapping basato su regole, vedere Modelli di colonna nel mapping del [flusso di dati](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Ordinamento del lavandino personalizzato

Per impostazione predefinita, i dati vengono scritti in più sink in un ordine non deterministico. Il motore di esecuzione scriverà i dati in parallelo quando la logica di trasformazione viene completata e l'ordine dei sink può variare ogni esecuzione. Per specificare e ordinare esattamente i sink, abilitare **l'ordinamento del sink personalizzato** nella scheda Generale del flusso di dati. Se abilitati, i sink verranno scritti in sequenza in ordine crescente.

![Ordinamento del lavandino personalizzato](media/data-flow/custom-sink-ordering.png "Ordinamento del lavandino personalizzato")

## <a name="data-preview-in-sink"></a>Anteprima dei dati nel sink

Quando si recupera un'anteprima dei dati in un cluster di debug, nessun dato verrà scritto nel sink. Verrà restituito uno snapshot dell'aspetto dei dati, ma non verrà scritto nulla nella destinazione. Per testare la scrittura di dati nel sink, eseguire un debug della pipeline dal canvas della pipeline.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il flusso di dati, aggiungere [un'attività Flusso di dati alla pipeline.](concepts-data-flow-overview.md)

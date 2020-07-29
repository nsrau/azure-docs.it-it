---
title: Tipi di dati supportati-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sui tipi di dati supportati in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 9d29d83ed92ee0fa2217bb1c27fbf6c2fbb3584c
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170887"
---
# <a name="supported-data-types"></a>Tipi di dati supportati

Nella tabella seguente sono elencati i tipi di dati supportati da Azure Time Series Insights Gen2

| Tipo di dati | Descrizione | Esempio | Nome della colonna proprietà in parquet
|---|---|---|---|
| **bool** | Tipo di dati con uno dei due stati: `true` o `false`. | `"isQuestionable" : true` | isQuestionable_bool
| **datetime** | Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Espresso nel [formato](https://www.iso.org/iso-8601-date-and-time-format.html) ISO 8601. Le proprietà DateTime sono sempre archiviate in formato UTC. Gli offset del fuso orario, se formattati correttamente, verranno applicati e quindi il valore archiviato in UTC. Per ulteriori informazioni sulla proprietà timestamp dell'ambiente e sugli offset DateTime, vedere [questa](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sezione. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` | eventProcessedLocalTime_datetime
| **double** | Numero a 64 bit a precisione doppia  | `"value": 31.0482941` | value_double
| **long** | Intero con segno a 64 bit  | `"value" : 31` | value_long
| **string** | I valori di testo devono essere costituiti da UTF-8 valido. Le stringhe null e vuote vengono considerate identiche. |  `"site": "DIM_MLGGG"` | site_string
| **dinamico** | Tipo complesso (non primitivo) costituito da una matrice o da un contenitore di proprietà (dizionario). Attualmente solo le matrici JSON file di primitive o matrici di oggetti che non contengono l'ID TS o la proprietà timestamp verranno archiviate come dinamiche. Leggere questo [articolo](./concepts-json-flattening-escaping-rules.md) per comprendere in che modo gli oggetti verranno resi bidimensionali e gli array potrebbero essere registrati. Le proprietà del payload archiviate come questo tipo sono accessibili tramite il Azure Time Series Insights Gen2 Explorer e l' `GetEvents`   API di query. |  `"values": "[197, 194, 189, 188]"` | values_dynamic

## <a name="sending-mixed-data-types"></a>Invio di tipi di dati misti

L'ambiente di Azure Time Series Insights Gen2 è fortemente tipizzato. Se i dispositivi o i tag inviano dati di tipi diversi per una proprietà del dispositivo, i valori verranno archiviati in due colonne separate e la [funzione COALESCE ()](https://docs.microsoft.com/rest/api/time-series-insights/preview#other-functions) deve essere usata durante la definizione delle espressioni variabili del modello Time Series nelle chiamate API.

Esplora Azure Time Series Insights offre un modo per unire automaticamente le colonne separate della stessa proprietà del dispositivo. Nell'esempio seguente il sensore invia una `PresentValue` proprietà che può essere sia long che Double. Per eseguire una query su tutti i valori archiviati (indipendentemente dal tipo di dati) della `PresentValue` proprietà, scegliere `PresentValue (Double | Long)` e le colonne verranno fuse.

[![COALESCE automaticamente Explorer](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Oggetti e matrici

È possibile inviare tipi complessi come gli oggetti e le matrici come parte del payload dell'evento. Gli oggetti annidati verranno resi bidimensionali e le matrici verranno archiviate come `dynamic` o bidimensionali per produrre più eventi a seconda della configurazione dell'ambiente e della forma JSON. Per altre informazioni, vedere le [regole di escape e Flat JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [regole di escape e Flat JSON](./concepts-json-flattening-escaping-rules.md) per comprendere come verranno archiviati gli eventi.

* Informazioni sulle limitazioni della [velocità effettiva](./concepts-streaming-ingress-throughput-limits.md) dell'ambiente

* Informazioni sulle [origini evento](concepts-streaming-ingestion-event-sources.md) per inserire i dati di streaming.

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
ms.date: 08/31/2020
ms.openlocfilehash: 4e6586453469797458bc60fc7499a45a9aad9b9b
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226744"
---
# <a name="supported-data-types"></a>Tipi di dati supportati

Nella tabella seguente sono elencati i tipi di dati supportati da Azure Time Series Insights Gen2

| Tipo di dati | Descrizione | Esempio | [Sintassi delle espressioni Time Series](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) | Nome della colonna proprietà in parquet
|---|---|---|---|---|
| **bool** | Tipo di dati con uno dei due stati: `true` o `false`. | `"isQuestionable" : true` | `$event.isQuestionable.Bool` o `$event['isQuestionable'].Bool` | `isQuestionable_bool`
| **datetime** | Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Espresso nel [formato](https://www.iso.org/iso-8601-date-and-time-format.html) ISO 8601. Le proprietà DateTime sono sempre archiviate in formato UTC. Gli offset del fuso orario, se formattati correttamente, verranno applicati e quindi il valore archiviato in UTC. Per ulteriori informazioni sulla proprietà timestamp dell'ambiente e sugli offset DateTime, vedere [questa](concepts-streaming-ingestion-event-sources.md#event-source-timestamp) sezione. | `"eventProcessedLocalTime": "2020-03-20T09:03:32.8301668Z"` |  Se "eventProcessedLocalTime" è il timestamp dell'origine evento: `$event.$ts` . Se si tratta di un'altra proprietà JSON: `$event.eventProcessedLocalTime.DateTime` o `$event['eventProcessedLocalTime'].DateTime` | `eventProcessedLocalTime_datetime`
| **double** | Numero a 64 bit a precisione doppia  | `"value": 31.0482941` | `$event.value.Double` o `$event['value'].Double` |  `value_double`
| **long** | Intero con segno a 64 bit  | `"value" : 31` | `$event.value.Long` o `$event['value'].Long` |  `value_long`
| **string** | I valori di testo devono essere costituiti da UTF-8 valido. Le stringhe null e vuote vengono considerate identiche. |  `"site": "DIM_MLGGG"`| `$event.site.String` o `$event['site'].String`| `site_string`
| **dinamico** | Tipo complesso (non primitivo) costituito da una matrice o da un contenitore di proprietà (dizionario). Attualmente solo le matrici JSON file di primitive o matrici di oggetti che non contengono l'ID TS o la proprietà timestamp verranno archiviate come dinamiche. Leggere questo [articolo](./concepts-json-flattening-escaping-rules.md) per comprendere in che modo gli oggetti verranno resi bidimensionali e gli array potrebbero essere registrati. Le proprietà del payload archiviate come questo tipo sono accessibili solo selezionando `Explore Events` in TSI Explorer per visualizzare gli eventi non elaborati o tramite l' [`GetEvents`](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)   API di query per l'analisi lato client. |  `"values": "[197, 194, 189, 188]"` | Il riferimento ai tipi dinamici in un'espressione Time Series non è ancora supportato | `values_dynamic`

> [!NOTE]
> sono supportati i valori interi a 64 bit, ma il numero maggiore che il Azure Time Series Insights Explorer può esprimere in modo sicuro è 9.007.199.254.740.991 (2 ^ 53-1) a causa delle limitazioni di JavaScript. Se si utilizzano i numeri nel modello di dati precedente, è possibile ridurre le dimensioni creando una variabile di [modello Time Series](/concepts-variables#numeric-variables) e [convertendo](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) il valore.

> [!NOTE]
> Il tipo **stringa** non ammette i valori null:
>
> * Un' [espressione Time Series (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) espressa in una [query di serie temporali](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis) che confronta il valore di una stringa vuota (**''**) rispetto a **null** si comporta allo stesso modo: `$event.siteid.String = NULL` equivale a `$event.siteid.String = ''` .
> * L'API può restituire valori **null** anche se gli eventi originali contengono stringhe vuote.
> * Non considerare la dipendenza dai valori **null** nelle colonne di tipo **stringa** per eseguire confronti o valutazioni, trattare le stesse modalità delle stringhe vuote.

## <a name="sending-mixed-data-types"></a>Invio di tipi di dati misti

L'ambiente di Azure Time Series Insights Gen2 è fortemente tipizzato. Se i dispositivi o i tag inviano dati di tipi diversi per una proprietà del dispositivo, i valori verranno archiviati in due colonne separate e la [funzione COALESCE ()](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#other-functions) deve essere usata durante la definizione delle espressioni variabili del modello Time Series nelle chiamate API.

Esplora Azure Time Series Insights offre un modo per unire automaticamente le colonne separate della stessa proprietà del dispositivo. Nell'esempio seguente il sensore invia una `PresentValue` proprietà che può essere sia long che Double. Per eseguire una query su tutti i valori archiviati (indipendentemente dal tipo di dati) della `PresentValue` proprietà, scegliere `PresentValue (Double | Long)` e le colonne verranno fuse.

[![COALESCE automaticamente Explorer](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png)](media\concepts-supported-data-types/explorer-auto-coalesce-sample.png#lightbox)

## <a name="objects-and-arrays"></a>Oggetti e matrici

È possibile inviare tipi complessi come gli oggetti e le matrici come parte del payload dell'evento. Gli oggetti annidati verranno resi bidimensionali e le matrici verranno archiviate come `dynamic` o bidimensionali per produrre più eventi a seconda della configurazione dell'ambiente e della forma JSON. Per altre informazioni, vedere le [regole di escape e Flat JSON](./concepts-json-flattening-escaping-rules.md)

## <a name="next-steps"></a>Passaggi successivi

* Leggere le [regole di escape e Flat JSON](./concepts-json-flattening-escaping-rules.md) per comprendere come verranno archiviati gli eventi.

* Informazioni sulle limitazioni della [velocità effettiva](./concepts-streaming-ingress-throughput-limits.md) dell'ambiente

* Informazioni sulle [origini evento](concepts-streaming-ingestion-event-sources.md) per inserire i dati di streaming.

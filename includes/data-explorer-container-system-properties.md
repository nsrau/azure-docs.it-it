---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128961"
---
### <a name="event-system-properties-mapping"></a>Mapping delle proprietà del sistema di eventi

> [!Note]
> * Le proprietà di sistema sono supportate per gli eventi a record singolo.
> * Per `csv` il mapping, le proprietà vengono aggiunte all'inizio del record. Per `json` il mapping, le proprietà vengono aggiunte in base al nome visualizzato nell'elenco a discesa.

Se è stata selezionata l'opzione **Proprietà del sistema** di eventi nella sezione Origine **dati** della tabella, è necessario includere le proprietà seguenti nello schema e nel mapping della tabella.

**Esempio di schema di tabella**

Se i dati includono`Timespan` `Metric`tre `Value`colonne ( , e `x-opt-enqueued-time` `x-opt-offset`) e le proprietà incluse sono e creare o modificare lo schema della tabella utilizzando il comando seguente:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Esempio di mapping CSV**

Eseguire i comandi seguenti per aggiungere dati all'inizio del record. Nota sui valori ordinali.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Esempio di mapping JSON**

I dati vengono aggiunti usando i nomi delle proprietà di sistema così come vengono visualizzati nell'elenco Proprietà **del sistema Event** del pannello Connessione **dati.** Eseguire i comandi seguenti.

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128961"
---
### <a name="event-system-properties-mapping"></a>Mapping delle proprietà del sistema eventi

> [!Note]
> * Le proprietà di sistema sono supportate per gli eventi a record singolo.
> * Per `csv` mapping, le proprietà vengono aggiunte all'inizio del record. Per `json` mapping, le proprietà vengono aggiunte in base al nome visualizzato nell'elenco a discesa.

Se è stata selezionata l'opzione **Proprietà sistema eventi** nella sezione **origine dati** della tabella, è necessario includere le proprietà seguenti nello schema e nel mapping della tabella.

**Esempio di schema di tabella**

Se i dati includono tre colonne (`Timespan`, `Metric`e `Value`) e le proprietà incluse sono `x-opt-enqueued-time` e `x-opt-offset`, creare o modificare lo schema della tabella utilizzando questo comando:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Esempio di mapping CSV**

Eseguire i comandi seguenti per aggiungere dati all'inizio del record. Prendere nota dei valori ordinali.

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

I dati vengono aggiunti usando i nomi delle proprietà di sistema così come vengono visualizzati nell'elenco delle **proprietà del sistema di eventi** del pannello **connessione dati** . Eseguire i comandi seguenti.

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

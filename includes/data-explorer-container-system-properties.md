---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/08/2020
ms.author: orspodek
ms.openlocfilehash: f9788e4623ce60ad55d79558d1d77a17eb2a9f26
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779952"
---
### <a name="event-system-properties-mapping"></a>Mapping delle proprietà del sistema eventi

Se sono state selezionate le **proprietà del sistema eventi** nella sezione **origine dati** della tabella precedente, passare all' [interfaccia utente Web](https://dataexplorer.azure.com/) per eseguire il comando KQL pertinente per la creazione del mapping appropriato.

   **Per il mapping CSV:**

    ```kusto
    .create table MyTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "messageid", "DataType":"string", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "userid", "DataType":"string", "Properties":{"Ordinal":"1"}},'
    '   { "column" : "other", "DataType":"int", "Properties":{"Ordinal":"2"}}'
    ']'
    ```
 
   **Per il mapping JSON:**

    ```kusto
    .create table MyTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "messageid", "datatype" : "string", "Properties":{"Path":"$.message-id"}},'
    '    { "column" : "userid", "Properties":{"Path":"$.user-id"}},'
    '    { "column" : "other", "Properties":{"Path":"$.other"}}'
    ']'
    ```

   > [!TIP]
   > * È necessario includere tutte le proprietà selezionate nel mapping. 
   > * L'ordine delle proprietà è importante nel mapping CSV. Le proprietà di sistema devono essere elencate prima di tutte le altre proprietà e nello stesso ordine in cui vengono visualizzate nell'elenco a discesa delle **proprietà del sistema di eventi** .
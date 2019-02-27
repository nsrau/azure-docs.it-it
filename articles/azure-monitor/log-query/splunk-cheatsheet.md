---
title: Splunk in query di log di Monitoraggio di Azure | Microsoft Docs
description: Guida per gli utenti che hanno familiarità con Splunk nell'apprendimento delle query di log di Monitoraggio di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: dafafa8ff5d721034b3b10bdeb1a2fc09cd32835
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267581"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk in query di log di Monitoraggio di Azure

Questo articolo è concepito per aiutare gli utenti che hanno familiarità con Splunk ad apprendere il linguaggio di query Kusto per scrivere le query di log in Monitoraggio di Azure. I confronti diretti vengono eseguiti tra le due versioni per comprendere le differenze principali e le analogie in cui è possibile sfruttare le proprie conoscenze esistenti.

## <a name="structure-and-concepts"></a>Struttura e concetti

La tabella seguente confronta i concetti e le strutture di dati tra Splunk e i log di Monitoraggio di Azure.

 | Concetto  | Splunk | Monitoraggio di Azure |  Comment
 | --- | --- | --- | ---
 | Unità di distribuzione  | cluster |  cluster |  Monitoraggio di Azure consente query tra cluster arbitrari. Splunk non lo consente. |
 | Cache di dati |  bucket  |  Memorizzazione nella cache e criteri di conservazione |  Controlla il periodo e il livello di memorizzazione nella cache per i dati. Questa impostazione influisce direttamente sulle prestazioni delle query e sui costi della distribuzione. |
 | Partizione logica dei dati  |  index  |  database  |  Consente la separazione logica dei dati. Entrambe le implementazioni consentono unioni e join tra le partizioni. |
 | Metadati degli eventi strutturati | N/D | tabella |  Splunk non dispone del concetto espresso nel linguaggio di ricerca di metadati dell'evento. I log di Monitoraggio di Azure presentano il concetto di una tabella che contiene colonne. Ogni istanza dell'evento è mappata a una riga. |
 | Record dei dati | event | riga |  Solo modifica terminologica. |
 | Attributo di record di dati | campo |  colonna |  In Monitoraggio di Azure, questo è già definito come parte della struttura della tabella. In Splunk, ogni evento ha un proprio set di campi. |
 | Tipi | tipo di dati |  tipo di dati |  I tipi di dati di Monitoraggio di Azure sono più espliciti poiché vengono impostati nelle colonne. Entrambi sono in grado di lavorare in modo dinamico con i tipi di dati e con i set quasi equivalenti ai tipi di dati che includono il supporto JSON. |
 | Query e ricerca  | ricerca | query |  I concetti sono essenzialmente uguali tra Monitoraggio di Azure e Splunk. |
 | Tempo di inserimento evento | Ora di sistema | ingestion_time() |  In Splunk, ogni evento ottiene un timestamp di sistema del momento in cui l'evento è stato indicizzato. In Monitoraggio di Azure, è possibile definire un criterio denominato ingestion_time che espone una colonna di sistema a cui è possibile fare riferimento tramite la funzione ingestion_time(). |

## <a name="functions"></a>Funzioni

La tabella seguente specifica le funzioni in Monitoraggio di Azure equivalenti alle funzioni di Splunk.

|Splunk | Monitoraggio di Azure |Comment
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> Si noti anche che, sebbene `replace()` accetti tre parametri in entrambi i prodotti, i parametri sono diversi. |
| substr | substring() | (1)<br>Si noti inoltre che Splunk usa gli indici in base uno. Monitoraggio di Azure rileva indici in base zero. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | corrisponde a regex |   (2)  |
| regex | corrisponde a regex | In Splunk, `regex` è un operatore. In Monitoraggio di Azure, è un operatore relazionale. |
| searchmatch | == | In Splunk, `searchmatch` consente di cercare la stringa esatta.
| random | rand()<br>rand(n) | La funzione di Splunk restituisce un numero compreso tra zero e 2<sup>31</sup>-1. Monitoraggio di Azure restituisce un numero compreso tra 0,0 e 1,0, o se un parametro specificato, compreso tra 0 e n-1.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>In Monitoraggio di Azure, l'equivalente di Splunk di relative_time (datetimeVal, offsetVal) è datetimeVal + totimespan(offsetVal).<br>Ad esempio: <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> diventa <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) in Splunk, viene richiamata la funzione con l'operatore `eval`. In Monitoraggio di Azure, viene usato come parte di `extend` o `project`.<br>(2) in Splunk, viene richiamata la funzione con l'operatore `eval`. In Monitoraggio di Azure, può essere usato con l'operatore `where`.


## <a name="operators"></a>Operatori

Nelle sezioni seguenti vengono illustrati esempi dell'uso di diversi operatori tra Splunk e Monitoraggio di Azure.

> [!NOTE]
> Ai fini di questo esempio, la _regola_ del campo Splunk esegue il mapping a una tabella in Monitoraggio di Azure, e il timestamp predefinito di Splunk esegue il mapping alla colonna di Log Analytics _ingestion_time()_.

### <a name="search"></a>Ricerca
In Splunk, è possibile omettere la parola chiave `search` e specificare una stringa senza virgolette. In Monitoraggio di Azure è necessario avviare ogni query con `find`, una stringa senza virgolette è un nome di colonna e il valore di ricerca deve essere una stringa tra virgolette. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Monitoraggio di Azure | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filtro
Le query di Monitoraggio di Azure iniziano da un risultato tabulare in cui è impostato il filtro. In Splunk, il filtro è l'operazione predefinita per l'indice corrente. È anche possibile usare l'operatore `where` in Splunk, ma non è consigliato.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Monitoraggio di Azure | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Recupero di n eventi/righe per l'ispezione 
Le query di log di Monitoraggio di Azure supportano anche `take` come alias per `limit`. In Splunk, se i risultati vengono ordinati, `head` restituirà i primi n risultati. In Monitoraggio di Azure, il limite non viene ordinato, ma restituisce le prime n righe che vengono trovate.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Monitoraggio di Azure | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Ottenere i primi n eventi/righe ordinati in base a un campo/colonna
Per ottenere risultati nella parte inferiore, usare in Splunk `tail`. In Monitoraggio di Azure è possibile specificare la direzione di ordinamento con `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Monitoraggio di Azure | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Estendere il set di risultati impostato con i nuovi campi/colonne
Splunk ha anche una funzione `eval` che non deve essere confrontabile con l'operatore `eval`. Entrambi gli operatori `eval` in Splunk e `extend` in Monitoraggio di Azure supportano solo le funzioni a valori scalari e gli operatori aritmetici.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Monitoraggio di Azure | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Rinominare 
Monitoraggio di Azure usa lo stesso operatore per rinominare e creare un nuovo campo. Splunk ha due operatori separati, `eval` e `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Monitoraggio di Azure | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Risultati/proiezione del formato
Splunk sembra non disporre di un operatore simile a `project-away`. È possibile usare l'interfaccia utente per filtrare i campi.

| |  | |
|:---|:---|:---|
| Splunk | **tabella** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Monitoraggio di Azure | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Aggregazione
Vedere le [Aggregazioni nelle query di log di Monitoraggio di Azure](aggregations.md) per le diverse funzioni di aggregazione.

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Monitoraggio di Azure | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Join in Splunk presenta limitazioni significative. La sottoquery ha un limite dei 10000 risultati (impostati nel file di configurazione della distribuzione); non esiste un numero limitato di caratteristiche join.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Monitoraggio di Azure | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Ordina
In Splunk, per ordinare in modo crescente è necessario usare l'operatore `reverse`. Monitoraggio di Azure supporta anche la precisazione della posizione in cui inserire i valori Null, all'inizio o alla fine.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Monitoraggio di Azure | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Espansione del multivalore
Si tratta di un operatore simile in Splunk e Monitoraggio di Azure.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Monitoraggio di Azure | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facet dei risultati, campi interessanti
In Log Analytics nel portale di Azure, viene esposta solo la prima colonna. Tutte le colonne sono disponibili tramite l'API.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Monitoraggio di Azure | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Deduplicare
È possibile usare `summarize arg_min()` invece di invertire l'ordine in cui i record vengono scelti.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Monitoraggio di Azure | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Passaggi successivi

- Seguire una lezione sulla [scrittura di query di log in Monitoraggio di Azure](get-started-queries.md).
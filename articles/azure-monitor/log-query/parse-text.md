---
title: Analizzare i dati di testo in log di Monitoraggio di Azure | Microsoft Docs
description: Descrive diverse opzioni per l'analisi dei dati di log nei record di Monitoraggio di Azure quando i dati vengono inseriti e quando vengono recuperati in una query, confrontando i rispettivi vantaggi.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 5a3b6852563955bfac940073bdda7d0afa02e77f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900255"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analizzare i dati di testo in log di Monitoraggio di Azure
Alcuni dati di log raccolti da Monitoraggio di Azure includeranno più informazioni in una singola proprietà. L'analisi di questi dati in più proprietà ne semplifica l'uso nelle query. Un esempio comune è un [log personalizzato](../../log-analytics/log-analytics-data-sources-custom-logs.md) che raccoglie un'intera voce di registro con più valori in una singola proprietà. Creando proprietà separate per i diversi valori, è possibile eseguire ricerche e aggregare in ciascuna di esse.

Questo articolo descrive diverse opzioni per l'analisi dei dati di log in Monitoraggio di Azure quando i dati vengono inseriti e quando vengono recuperati in una query, confrontando i rispettivi vantaggi.


## <a name="parsing-methods"></a>Metodi di analisi
È possibile analizzare i dati in fase di inserimento quando i dati vengono raccolti o al momento della query durante l'analisi dei dati con una query. Ogni strategia offre vantaggi esclusivi, come descritto di seguito.

### <a name="parse-data-at-collection-time"></a>Analizzare i dati in fase di raccolta
Quando si analizzano i dati in fase di raccolta, configurare [campi personalizzati](../../log-analytics/log-analytics-custom-fields.md) che creano nuove proprietà nella tabella. Le query non devono includere una logica di analisi ma semplicemente usare queste proprietà come qualsiasi altro campo nella tabella.

I vantaggi di questo metodo sono i seguenti:

- È più semplice eseguire una query sui dati raccolti perché non è necessario includere i comandi di analisi nella query.
- Le prestazioni della query migliorano perché non è necessario eseguire l'analisi.
 
Gli svantaggi di questo metodo sono i seguenti:

- Deve essere definito in anticipo. Non può includere dati già raccolti.
- Se si modifica la logica di analisi, verrà applicata solo ai nuovi dati.
- Meno opzioni di analisi disponibili nelle query.
- Aumenta il tempo di latenza per la raccolta dei dati.
- Gli errori possono essere difficili da gestire.


### <a name="parse-data-at-query-time"></a>Analizzare i dati in fase di query
Quando si analizzano i dati in fase di query, si include la logica nella query per analizzare i dati in più campi. La tabella effettiva non viene modificata.

I vantaggi di questo metodo sono i seguenti:

- Si applica a tutti i dati, compresi i dati già raccolti.
- È possibile applicare modifiche della logica immediatamente a tutti i dati.
- Opzioni di analisi flessibili, tra cui la logica predefinita per strutture di dati specifiche.
 
Gli svantaggi di questo metodo sono i seguenti:

- Richiede query più complesse. Questo problema può essere contenuto usando [funzioni che simulano una tabella](#use-function-to-simulate-a-table).
- È necessario replicare la logica di analisi in più query. Può condividere la logica tra le funzioni.
- Può creare un overhead quando esegue una logica complessa in set di record di dimensioni molto grandi (miliardi di record).

## <a name="parse-data-as-its-collected"></a>Analizzare i dati durante la raccolta
Per informazioni sull'analisi dei dati durante la raccolta, vedere [Creare campi personalizzati in Monitoraggio di Azure](../platform/custom-fields.md). Crea proprietà personalizzate nella tabella che è possibile usare nelle query proprio come qualsiasi altra proprietà.

## <a name="parse-data-in-query-using-patterns"></a>Analizzare i dati nelle query usando criteri
Quando i dati da analizzare possono essere identificati in base a un criterio ripetuto nei record, è possibile usare diversi operatori nel [linguaggio di query Kusto](/azure/kusto/query/) per estrarre i dati specifici in una o più nuove proprietà.

### <a name="simple-text-patterns"></a>Semplici criteri di testo

Usare l'operatore [parse](/azure/kusto/query/parseoperator) nella query per creare una o più proprietà personalizzate che è possibile estrarre da un'espressione di stringa. Specificare il criterio che deve essere identificato e i nomi delle proprietà da creare. Ciò è particolarmente utile per i dati con stringhe chiave-valore con un modulo simile a _chiave = valore_.

Prendere in considerazione un log personalizzato con i dati nel formato seguente.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

La query seguente analizzerebbe questi dati in singole proprietà. La riga con _project_ viene aggiunta per restituire solo le proprietà calcolate e non _RawData_, cioè la singola proprietà che contiene l'intera voce del log personalizzato.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Di seguito è riportato un altro esempio che suddivide il nome utente di un UPN nella tabella _AzureActivity_.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Espressioni regolari
Se i dati possono essere identificati con un'espressione regolare, è possibile usare le [funzioni che usano espressioni regolari](/azure/kusto/query/re2) per estrarre i singoli valori. L'esempio seguente usa [extract](/azure/kusto/query/extractfunction) per estrarre il campo _UPN_ dai record di _AzureActivity_ e restituire utenti distinti.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Per abilitare l'analisi efficiente su vasta scala, Monitoraggio di Azure usa la versione re2 delle espressioni regolari, che è simile ma non identica ad alcune delle altre varianti di espressione regolare. Per informazioni, consultare la [sintassi dell'espressione re2](https://aka.ms/kql_re2syntax).


## <a name="parse-delimited-data-in-a-query"></a>Analizzare i dati delimitati in una query
I dati delimitati separano i campi con un carattere comune, ad esempio una virgola in un file CSV. Usare la funzione [split](/azure/kusto/query/splitfunction) per analizzare dati delimitati usando un delimitatore specificato. È possibile usarlo con l'operatore [extend](/azure/kusto/query/extendoperator) per restituire tutti i campi nei dati o per specificare i singoli campi da includere nell'output.

> [!NOTE]
> Dal momento che split restituisce un oggetto dinamico, potrebbe essere necessario eseguire esplicitamente il cast dei risultati nei tipi di dati, ad esempio una stringa da usare in operatori e filtri.

Prendere in considerazione un log personalizzato con i dati nel formato CSV seguente.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

La query seguente analizzerebbe questi dati riepilogandoli in base a due delle proprietà calcolate. La prima riga suddivide la proprietà _RawData_ in una matrice di stringa. Ognuna delle righe successive assegna un nome alle singole proprietà e le aggiunge all'output usando funzioni per convertirle nel tipo di dati appropriato.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analizzare le strutture predefinite in una query
Se i dati vengono formattati in una struttura nota, è possibile usare una delle funzioni nel [linguaggio di query Kusto](/azure/kusto/query/) per l'analisi di strutture predefinite:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [Query URL](/azure/kusto/query/parseurlqueryfunction)
- [Percorso file](/azure/kusto/query/parsepathfunction)
- [Agente utente](/azure/kusto/query/parse-useragentfunction)
- [Stringa di versione](/azure/kusto/query/parse-versionfunction)

La query di esempio seguente analizza il campo _Proprietà_ della tabella _AzureActivity_, che è strutturata in JSON. Salva i risultati in una proprietà dinamica denominata _parsedProp_, che include il singolo valore denominato nel file JSON. Questi valori vengono usati per filtrare e riepilogare i risultati della query.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Queste funzioni di analisi possono richiedere notevoli risorse del processore, quindi occorre usarle solo quando la query usa più proprietà dai dati formattati. In caso contrario, la semplice elaborazione della corrispondenza dei criteri sarà più rapida.

L'esempio seguente mostra il dettaglio del tipo Preauth TGT del controller di dominio. Il tipo è presente solo nel campo EventData, che è una stringa XML, ma non sono necessari altri dati di questo campo. In questo caso [parse](/azure/kusto/query/parseoperator) viene usato per selezionare la porzione di dati richiesta.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Usare la funzione per simulare una tabella
Potrebbero essere disponibili più query che eseguono la stessa analisi di una tabella specifica. In questo caso [creare una funzione](functions.md) che restituisca i dati analizzati invece di replicare la logica di analisi in ogni query. È quindi possibile usare l'alias di funzione al posto della tabella originale in altre query.

Si consideri l'esempio di log personalizzato delimitato da virgole riportato sopra. Per usare i dati analizzati in più query, creare una funzione usando la query seguente e salvarla con l'alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

È ora possibile usare l'alias _MyCustomCSVLog_ al posto del nome della tabella effettiva nelle query simili alla seguente.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [query di log](log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
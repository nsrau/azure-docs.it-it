---
title: Azure Data Explorer integration with Azure Data Factory
description: In questo argomento integrare Azure Data Explorer con Azure Data Factory per usare le attività di copia, ricerca e comando
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: bb08cf4db45a378b35a8245eadd56a2ab3e48bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293624"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrare Azure Data Explorer con Azure Data FactoryIntegrate Azure Data Explorer with Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) è un servizio di integrazione dati basato su cloud che consente di integrare archivi dati diversi ed eseguire attività sui dati. ADF consente di creare flussi di lavoro basati sui dati per l'orchestrazione e l'automazione degli spostamento dei dati e la trasformazione dei dati. Azure Data Explorer è uno degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Azure Data Factory.Azure Data Explorer is one of the supported data stores in Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Factory activities for Azure Data Explorer

Sono disponibili varie integrazioni con Azure Data Factory per gli utenti di Azure Data Explorer:Various integrations with Azure Data Factory users are available for Azure Data Explorer users:

### <a name="copy-activity"></a>Attività di copia
 
L'attività di copia di Azure Data Factory viene usata per trasferire dati tra archivi dati. Azure Data Explorer is supported as a source, where data is copied from Azure Data Explorer to any supported data store, and a sink, where data is copied from any supported data store to Azure Data Explorer. Per altre informazioni, vedere [Copiare dati in o da Azure Data Explorer con Azure Data Factory.For](/azure/data-factory/connector-azure-data-explorer)more information, see copy data to or from Azure Data Explorer using Azure Data Factory. e per una procedura dettagliata, vedere i dati di [caricamento da Azure Data Factory in Azure Data Explorer](data-factory-load-data.md).
Azure Data Explorer is supported by Azure IR (Integration Runtime), used when data is copied within Azure, and self-hosted IR, used when copying data from/to data stores located on-premises or in a network with access control, such as an Azure Virtual Network. Per ulteriori informazioni, vedere [quale iR utilizzare](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Quando si usa l'attività di copia e si crea un **servizio collegato** o un **set di**dati , selezionare l'archivio dati Azure Data **Explorer (Kusto)** e non l'archivio dati precedente **Kusto**.  

### <a name="lookup-activity"></a>Attività di ricerca
 
L'attività Lookup viene usata per l'esecuzione di query in Azure Data Explorer.The Lookup activity is used for executing queries on Azure Data Explorer. Il risultato della query verrà restituito come output dell'attività Lookup e potrà essere utilizzato nell'attività successiva nella pipeline, come descritto nella documentazione relativa alla [ricerca ADF.](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)  
Oltre al limite di dimensione della risposta di 5.000 righe e 2 MB, l'attività ha anche un limite di timeout di query di 1 ora.

### <a name="command-activity"></a>Attività di comando

L'attività Comando consente l'esecuzione dei comandi di [controllo](/azure/kusto/concepts/#control-commands)di Esplora dati di Azure. A differenza delle query, i comandi di controllo possono potenzialmente modificare i dati o i metadati. Alcuni comandi di controllo sono destinati all'inserimento di dati `.ingest` `.set-or-append`in Azure Data Explorer, usando comandi come o `.export`) o per copiare i dati da Azure Data Explorer in archivi dati esterni usando comandi come .
Per una procedura dettagliata dell'attività dei comandi, vedere Usare l'attività dei comandi di Azure Data Factory per eseguire i comandi di controllo di Azure Data Explorer.For a detailed walk-through of the command activity, see [use Azure Data Factory command activity to run Azure Data Explorer control commands](data-factory-command-activity.md).  L'utilizzo di un comando di controllo per copiare i dati può, a volte, essere un'opzione più veloce ed economica rispetto all'attività Copia. Per determinare quando utilizzare l'attività Comando e Copia, vedere [Selezionare tra](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)le attività Copia e Comando durante la copia dei dati.

### <a name="copy-in-bulk-from-a-database-template"></a>Copiare in blocco da un modello di databaseCopy in bulk from a database template

Copia [in blocco da un database ad Azure Data Explorer usando il modello](data-factory-template.md) di Azure Data Factory è una pipeline predefinita di Azure Data Factory.The Copy in bulk from a database to Azure Data Explorer by using the Azure Data Factory template is a predefined Azure Data Factory pipeline. Il modello viene utilizzato per creare molte pipeline per database o per tabella per una copia dei dati più rapida. 

### <a name="mapping-data-flows"></a>Flussi di dati di mapping 

I [flussi](/azure/data-factory/concepts-data-flow-overview) di dati di mapping di Azure Data Factory sono trasformazioni dei dati progettate visivamente che consentono ai tecnici di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. Per creare un flusso di dati e inserire dati in Esplora dati di Azure, usare il metodo seguente:To create a data flow and ingest data to Azure Data Explorer, use the following method:

1. Creare il [flusso di dati di mapping.](/azure/data-factory/data-flow-create)
1. [Esportare i dati in BLOB di Azure.](/azure/data-factory/data-flow-sink) 
1. Definire [l'attività](/azure/data-explorer/ingest-data-event-grid) di copia di Griglia di eventi o ADF per l'inserimento dei dati in Esplora dati di Azure.Define Event Grid or [ADF copy activity](/azure/data-explorer/data-factory-load-data) to ingest the data to Azure Data Explorer.

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Selezionare tra le attività comando Copia e Esplora dati di Azure quando si copiano i dati 

Questa sezione ti aiuterà a selezionare l'attività corretta per le tue esigenze di copia dei dati.

Quando si copiano dati da o in Azure Data Explorer, sono disponibili due opzioni in Azure Data Factory:When copying data from or to Azure Data Explorer, there are two available options in Azure Data Factory:
* Attività di copia.
* Azure Data Explorer Command activity, which executes one of the control commands that transfer data in Azure Data Explorer. 

### <a name="copy-data-from-azure-data-explorer"></a>Copiare dati da Azure Data ExplorerCopy data from Azure Data Explorer
  
È possibile copiare dati da Azure Data [`.export`](/azure/kusto/management/data-export/) Explorer usando l'attività di copia o il comando. Il `.export` comando esegue una query e quindi esporta i risultati della query. 

Vedere la tabella seguente per un `.export` confronto dell'attività di copia e del comando per la copia dei dati da Azure Data Explorer.See the following table for a comparison of the Copy activity and command for copying data from Azure Data Explorer.

| | Attività di copia | Comando .export |
|---|---|---|
| **Descrizione del flusso** | ADF esegue una query su Kusto, elabora il risultato e lo invia all'archivio dati di destinazione. <br>(**ADX > Archivio dati sink > ADF**) | ADF invia `.export` un comando di controllo ad Azure Data Explorer, che esegue il comando e invia i dati direttamente all'archivio dati di destinazione. <br>(**ADX > archivio dati sink**) |
| **Archivi dati di destinazione supportati** | Un'ampia gamma di [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, Azure Blob, SQL Database |
| **Prestazioni** | Centralizzata | <ul><li>Distribuito (predefinito), esportazione simultanea di dati da più nodi</li><li>Più veloce ed efficiente in base all'efficienza COGS.</li></ul> |
| **Limiti del server** | [I limiti di query](/azure/kusto/concepts/querylimits) possono essere estesi/disabilitati. Per impostazione predefinita, le query ADF contengono: <ul><li>Limite di dimensione di 500.000 record o 64 MB.</li><li>Limite di tempo di 10 minuti.</li><li>`noTruncation`impostato su false.</li></ul> | Per impostazione predefinita, estende o disabilita i limiti di query: <ul><li>I limiti di dimensione sono disabilitati.</li><li>Il timeout del server viene esteso a 1 ora.</li><li>`MaxMemoryConsumptionPerIterator`e `MaxMemoryConsumptionPerQueryPerNode` vengono estesi al massimo (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Se la destinazione di copia è uno `.export` degli archivi dati supportati dal comando e se nessuna `.export` delle funzionalità dell'attività di copia è fondamentale per le proprie esigenze, selezionare il comando.

### <a name="copying-data-to-azure-data-explorer"></a>Copia dei dati in Azure Data ExplorerCopying data to Azure Data Explorer

È possibile copiare i dati in Esplora dati di Azure usando l'attività `.set` `.replace)`di copia o i comandi di inserimento, ad esempio l'inserimento [dalla query](/azure/kusto/management/data-ingestion/ingest-from-query) `.set-or-append`( , `.set-or-replace`, , e l'inserimento [dall'archiviazione](/azure/kusto/management/data-ingestion/ingest-from-storage) ( ).`.ingest` 

Vedere la tabella seguente per un confronto dell'attività Copia e comandi di inserimento per la copia dei dati in Azure Data Explorer.See the following table for a comparison of the Copy activity, and ingestion commands for copying data to Azure Data Explorer.

| | Attività di copia | Inserimento da query<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Inserimento da risorsa di archiviazione <br> `.ingest` |
|---|---|---|---|
| **Descrizione del flusso** | ADF ottiene i dati dall'archivio dati di origine, li converte in un formato tabulare ed esegue le modifiche di mapping dello schema necessarie. ADF carica quindi i dati nei BLOB di Azure, li suddivide in blocchi, quindi scarica i BLOB per inserirli nella tabella ADX. <br> **(Archivio dati di origine > I BLOB DI ADF > Azure > ADX**) | Questi comandi possono eseguire `.show` una query o un comando e inserire i risultati della query in una tabella (**ADX > ADX**). | Questo comando inserisce i dati in una tabella "tirando" i dati da uno o più elementi di archiviazione cloud. |
| **Archivi dati di origine supportati** |  [varietà di opzioni](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2, Blob di Azure, SQL (usando il plug-in sql_request), Cosmos (tramite il plug-in cosmosdb_sql_request) e qualsiasi altro archivio dati che fornisce API HTTP o Python. | File system, Archiviazione BLOB di Azure, ADLS Gen 1, ADLS Gen 2 |
| **Prestazioni** | Le inserimenti vengono accodate e gestite, garantendo inserimenti di piccole dimensioni e garantendo un'elevata disponibilità fornendo bilanciamento del carico, tentativi e gestione degli errori. | <ul><li>Questi comandi non sono stati progettati per l'importazione di dati ad alto volume.</li><li>Funziona come previsto e più economico. Ma per gli scenari di produzione e quando le velocità di traffico e le dimensioni dei dati sono di grandi dimensioni, usare l'attività Copia.But for production scenarios and when traffic rates and data sizes are large, use the Copy activity.</li></ul> |
| **Limiti del server** | <ul><li>Nessun limite di dimensione.</li><li>Limite massimo di timeout: 1 ora per BLOB ingerita. |<ul><li>Esiste solo un limite di dimensione per la parte di `noTruncation=true`query, che può essere ignorato specificando .</li><li>Limite massimo di timeout: 1 ora.</li></ul> | <ul><li>Nessun limite di dimensione.</li><li>Limite massimo di timeout: 1 ora.</li></ul>|

> [!TIP]
> * Quando si copiano dati da ADF ad Azure Data Explorer, usare i `ingest from query` comandi.  
> * Per set di dati di grandi dimensioni (>1 GB), usare l'attività Copia.For large data sets (>1GB), use the Copy activity.  

## <a name="required-permissions"></a>Autorizzazioni necessarie

The following table lists the required permissions for various steps in the integration with Azure Data Factory.

| Passaggio | Operazione | Livello minimo di autorizzazioni | Note |
|---|---|---|---|
| **Creare un servizio collegatoCreate a Linked Service** | Navigazione nel database | *visualizzatore di database* <br>L'utente connesso che utilizza ADF deve essere autorizzato a leggere i metadati del database. | L'utente può fornire il nome del database manualmente. |
| | Test della connessione | *monitor di database* o *tabella in* <br>L'entità servizio deve essere `.show` autorizzata a eseguire comandi a livello di database o inserimento a livello di tabella. | <ul><li>TestConnection verifica la connessione al cluster e non al database. Può avere esito positivo anche se il database non esiste.</li><li>Le autorizzazioni di amministratore della tabella non sono sufficienti.</li></ul>|
| **Creazione di un set di dati** | Navigazione in tabella | *monitor di database* <br>L'utente connesso tramite ADF deve essere `.show` autorizzato a eseguire comandi a livello di database. | L'utente può fornire manualmente il nome della tabella.|
| **Creazione di un set di dati** o di un'attività di **copiaCreating** a Dataset or Copy Activity | Anteprima dei dati | *visualizzatore di database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | | 
|   | Importa schema | *visualizzatore di database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | Quando ADX è l'origine di una copia da tabella a tabulare, ADF importerà automaticamente lo schema, anche se l'utente non ha importato lo schema in modo esplicito. |
| **ADX come sink** | Creare un mapping di colonna per nomeCreate a by-name column mapping | *monitor di database* <br>L'entità servizio deve essere `.show` autorizzata per eseguire comandi a livello di database. | <ul><li>Tutte le operazioni obbligatorie funzioneranno con *table in.*</li><li> Alcune operazioni facoltative possono avere esito negativo.</li></ul> |
|   | <ul><li>Creare un mapping CSV nella tabella</li><li>Eliminare il mapping</li></ul>| *tabella ingestore* o *amministratore di database* <br>L'entità servizio deve essere autorizzata per apportare modifiche a una tabella. | |
|   | Inserire dati | *tabella ingestore* o *amministratore di database* <br>L'entità servizio deve essere autorizzata per apportare modifiche a una tabella. | | 
| **ADX come origine** | Eseguire la query | *visualizzatore di database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | |
| **Comando di Kusto** | | In base al livello di autorizzazioni di ogni comando. |

## <a name="performance"></a>Prestazioni 

Se Azure Data Explorer è l'origine e si usa l'attività Lookup, copy o command contenente una query in cui fare riferimento alle [procedure consigliate](/azure/kusto/query/best-practices) per le query per le informazioni sulle prestazioni e alla [documentazione ADF per l'attività](/azure/data-factory/copy-activity-performance)di copia.
  
Questa sezione riguarda l'uso dell'attività di copia in cui Azure Data Explorer è il sink. La velocità effettiva stimata per il sink di Azure Data Explorer è 11-13 MBps.The estimated throughput for Azure Data Explorer sink is 11-13 MBps. Nella tabella seguente vengono descritti in dettaglio i parametri che influenzano le prestazioni del sink di Azure Data Explorer.The following table details the parameters influencing the performance of the Azure Data Explorer sink.

| Parametro | Note |
|---|---|
| **Prossimità geografica dei componenti** | Posizionare tutti i componenti nella stessa regione:<ul><li>archivi dati di origine e sink.</li><li>Runtime di integrazione ADF.</li><li>Il cluster ADX.</li></ul>Assicurarsi che almeno il runtime di integrazione si trova nella stessa area del cluster ADX. |
| **Numero di DII** | 1 MACCHINA virtuale per ogni 4 DIE usate dall'ADF. <br>L'aumento delle divus sarà utile solo se l'origine è un archivio basato su file con più file. Ogni macchina virtuale elaborerà quindi un file diverso in parallelo. Pertanto, la copia di un singolo file di grandi dimensioni avrà una latenza più alta rispetto alla copia di più file più piccoli.|
|**Importo e SKU del cluster ADX** | Un numero elevato di nodi ADX aumenterà il tempo di elaborazione dell'ingestione.|
| **Parallelismo** | Per copiare una grande quantità di dati da un database, partizionare i dati e quindi usare un ciclo ForEach che copia ogni partizione in parallelo o usare la [copia bulk dal database al modello](data-factory-template.md)di Azure Data Explorer . Nota: Il**grado di parallelismo delle** **impostazioni** > nell'attività Copia non è rilevante per ADX. |
| **Complessità del trattamento dei dati** | La latenza varia in base al formato del file di origine, al mapping delle colonne e alla compressione.|
| **La macchina virtuale che esegue il runtime di integrazione** | <ul><li>Per la copia di Azure, le macchine virtuali ADF e gli SKU del computer non possono essere modificati.</li><li> Per la copia locale in Azure, determinare che la macchina virtuale che ospita il componente di distribuzione self-hosted è sufficientemente forte.</li></ul>|

## <a name="tips-and-common-pitfalls"></a>Consigli e insidie comuni

### <a name="monitor-activity-progress"></a>Monitorare l'avanzamento dell'attività

* Quando si monitora lo stato di avanzamento dell'attività, la proprietà *Data written* può essere molto più grande della proprietà *Data read* perché i dati *letti* vengono calcolati in base alle dimensioni del file binario, mentre i *dati scritti* vengono calcolati in base alle dimensioni in memoria, dopo la deserializzazione e la decompressione dei dati.

* Quando si monitora lo stato di avanzamento dell'attività, è possibile vedere che i dati vengono scritti nel sink di Azure Data Explorer.When monitoring the activity progress, you can see that data is written to the Azure Data Explorer sink. Quando si esegue una query sulla tabella di Azure Data Explorer, si nota che i dati non sono arrivati. Ciò è dovuto al fatto che esistono due fasi durante la copia in Azure Data Explorer.This is because there are two stages when copying to Azure Data Explorer. 
    * La prima fase legge i dati di origine, li suddivide in blocchi da 900 MB e carica ogni blocco in un BLOB di Azure.First stage reads the source data, splits it to 900-MB chunks, and uploads each chunk to an Azure Blob. La prima fase è vista dalla visualizzazione dello stato di avanzamento dell'attività ADF. 
    * The second stage begins once all the data is uploaded to Azure Blobs. I nodi del motore di Azure Data Explorer scaricano i BLOB e inseriscono i dati nella tabella sink. I dati vengono quindi visualizzati nella tabella di Azure Data Explorer.The data is then seen in your Azure Data Explorer table.

### <a name="failure-to-ingest-csv-files-due-to-improper-escaping"></a>Mancata inserimento di file CSV a causa di un'espulsioni impropria

Azure Data Explorer prevede che i file CSV siano allineati con [RFC 4180.](https://www.ietf.org/rfc/rfc4180.txt)
Si aspetta:
* I campi che contengono caratteri che richiedono l'escattività (ad esempio " e nuove righe) devono iniziare e terminare con un **carattere "** , senza spazi vuoti. Tutti i caratteri **"** *all'interno* del campo vengono sottoposti a escape utilizzando un carattere **""** doppio (**""**). Ad esempio, _"Hello, "World"""_ è un file CSV valido con un singolo record con una singola colonna o campo con il contenuto _Hello, "World"_.
* Tutti i record nel file devono avere lo stesso numero di colonne e campi.

Azure Data Factory consente il carattere barra rovesciata (escape). Se si genera un file CSV con un carattere barra rovesciata usando Azure Data Factory, l'inserimento del file in Azure Data Explorer avrà esito negativo.

#### <a name="example"></a>Esempio

I seguenti valori di testo: Hello, "World"<br/>
ABC DEF<br/>
EF "ABC-D"<br/>
"ABC DEF<br/>

Dovrebbe essere visualizzato in un file CSV corretto come segue: "Ciao, ""Mondo"""<br/>
"ABC DEF"<br/>
"""ABC DEF"<br/>
"""ABC"D"EF"<br/>

Utilizzando il carattere di escape predefinito (barra rovesciata), il file \"CSV\"seguente non funzionerà con Azure Data Explorer: "Hello, World"<br/>
"ABC DEF"<br/>
"\"ABC DEF"<br/>
"\"ABC-\"D EF"<br/>

### <a name="nested-json-objects"></a>Oggetti JSON annidatiNested JSON objects

Quando si copia un file JSON in Azure Data Explorer, tenere presente che:When copying a JSON file to Azure Data Explorer, note that:
* Le matrici non sono supportate.
* Se la struttura JSON contiene tipi di dati oggetto, Azure Data Factory appiattirà gli elementi figlio dell'oggetto e tenterà di eseguire il mapping di ogni elemento figlio a una colonna diversa nella tabella di Azure Data Explorer.If your JSON structure contains object data types, Azure Data Factory will flatten the object's child items, and try to map each child item to a different column in your Azure Data Explorer table. Se si vuole eseguire il mapping dell'intero elemento oggetto a una singola colonna in Azure Data Explorer:If you want the entire object item to be mapped to a single column in Azure Data Explorer:
    * Inserire l'intera riga JSON in una singola colonna dinamica in Azure Data Explorer.Ingest the entire JSON row into a single dynamic column in Azure Data Explorer.
    * Modificare manualmente la definizione della pipeline usando l'editor JSON di Azure Data Factory.Manually edit the pipeline definition by using Azure Data Factory's JSON editor. In **Mapping**
       * Rimuovere i mapping multipli creati per ogni elemento figlio e aggiungere un singolo mapping che esegue il mapping del tipo di oggetto alla colonna della tabella.
       * Dopo la parentesi quadra di chiusura, aggiungere una virgola seguita da:<br/>
       `"mapComplexValuesToString": true`.

### <a name="specify-additionalproperties-when-copying-to-azure-data-explorer"></a>Specificare AdditionalProperties durante la copia in Azure Data ExplorerSpecify AdditionalProperties when copying to Azure Data Explorer

> [!NOTE]
> Questa funzionalità è attualmente disponibile modificando manualmente il payload JSON. 

Aggiungere una singola riga nella sezione "sink" dell'attività di copia come segue:

```json
"sink": {
    "type": "AzureDataExplorerSink",
    "additionalProperties": "{\"tags\":\"[\\\"drop-by:account_FiscalYearID_2020\\\"]\"}"
},
```

La fuga del valore può essere difficile. Usare il frammento di codice seguente come riferimento:Use the following code snippet as a reference:

```csharp
static void Main(string[] args)
{
       Dictionary<string, string> additionalProperties = new Dictionary<string, string>();
       additionalProperties.Add("ignoreFirstRecord", "false");
       additionalProperties.Add("csvMappingReference", "Table1_mapping_1");
       IEnumerable<string> ingestIfNotExists = new List<string> { "Part0001" };
       additionalProperties.Add("ingestIfNotExists", JsonConvert.SerializeObject(ingestIfNotExists));
       IEnumerable<string> tags = new List<string> { "ingest-by:Part0001", "ingest-by:IngestedByTest" };
       additionalProperties.Add("tags", JsonConvert.SerializeObject(tags));
       var additionalPropertiesForPayload = JsonConvert.SerializeObject(additionalProperties);
       Console.WriteLine(additionalPropertiesForPayload);
       Console.ReadLine();
}
```

Il valore stampato:

```json
{"ignoreFirstRecord":"false","csvMappingReference":"Table1_mapping_1","ingestIfNotExists":"[\"Part0001\"]","tags":"[\"ingest-by:Part0001\",\"ingest-by:IngestedByTest\"]"}
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [copiare dati in Azure Data Explorer tramite Azure Data Factory.](data-factory-load-data.md)
* Informazioni sull'uso del modello di Azure Data Factory per la [copia bulk dal database ad Azure Data Explorer.](data-factory-template.md)
* Informazioni sull'uso dell'attività dei comandi di Azure Data Factory per eseguire i comandi di controllo di Azure Data Explorer.Learn about using [Azure Data Factory command activity to run Azure Data Explorer control commands](data-factory-command-activity.md).
* Informazioni sulle query di [Azure Data Explorer](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.




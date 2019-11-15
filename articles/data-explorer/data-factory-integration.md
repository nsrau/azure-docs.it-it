---
title: Integrazione di Azure Esplora dati con Azure Data Factory
description: In questo argomento si integrano Esplora dati di Azure con Azure Data Factory per usare le attività di copia, ricerca e comando
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093761"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Integrare Esplora dati di Azure con Azure Data Factory

[Azure Data Factory](/azure/data-factory/) (ADF) è un servizio di integrazione dei dati basato sul cloud che consente di integrare archivi dati diversi ed eseguire attività sui dati. ADF consente di creare flussi di lavoro basati sui dati per orchestrare e automatizzare lo spostamento e la trasformazione dei dati. Azure Esplora dati è uno degli [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) in Azure Data Factory. 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Attività Azure Data Factory per Azure Esplora dati

Sono disponibili diverse integrazioni con Azure Data Factory per gli utenti di Azure Esplora dati:

### <a name="copy-activity"></a>Attività di copia
 
Azure Data Factory attività di copia viene utilizzata per trasferire i dati tra archivi dati. Azure Esplora dati è supportato come origine, in cui i dati vengono copiati da Azure Esplora dati a qualsiasi archivio dati supportato e da un sink, in cui i dati vengono copiati da qualsiasi archivio dati supportato in Azure Esplora dati. Per altre informazioni, vedere [copiare dati da o verso Azure Esplora dati usando Azure Data Factory](/azure/data-factory/connector-azure-data-explorer). per una procedura dettagliata, vedere [caricare i dati da Azure Data Factory in Azure Esplora dati](data-factory-load-data.md).
Azure Esplora dati è supportato da Azure IR (Integration Runtime), usato quando i dati vengono copiati in Azure e con il runtime di integrazione self-hosted, usati per la copia di dati da/in archivi dati locali o in una rete con controllo di accesso, ad esempio una rete virtuale di Azure. Per ulteriori informazioni, vedere il runtime [di integrazione da usare](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use)
 
> [!TIP]
> Quando si usa l'attività di copia e si crea un **servizio collegato** o un **set**di dati, selezionare l'archivio dati **Azure Esplora dati (kusto)** e non l'archivio dati precedente **kusto**.  

### <a name="lookup-activity"></a>Attività Lookup
 
L'attività di ricerca viene usata per eseguire query in Azure Esplora dati. Il risultato della query verrà restituito come output dell'attività di ricerca e potrà essere usato nell'attività successiva della pipeline, come descritto nella [documentazione di ricerca di ADF](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity).  
Oltre al limite delle dimensioni della risposta di 5.000 righe e 2 MB, l'attività ha anche un limite di timeout per le query di 1 ora.

### <a name="command-activity"></a>Attività Command

L'attività Command consente l'esecuzione dei comandi di [controllo](/azure/kusto/concepts/#control-commands)Esplora dati di Azure. Diversamente dalle query, i comandi di controllo possono potenzialmente modificare dati o metadati. Alcuni comandi di controllo sono destinati a inserire dati in Esplora dati di Azure, usando comandi come `.ingest`o `.set-or-append`) o copiare dati da Esplora dati di Azure a archivi dati esterni usando comandi come `.export`.
Per una procedura dettagliata dell'attività Command, vedere [usare Azure Data Factory attività Command per eseguire i comandi di controllo Esplora dati di Azure](data-factory-command-activity.md).  L'uso di un comando di controllo per la copia dei dati può, a volte, essere un'opzione più veloce ed economica rispetto all'attività di copia. Per determinare quando usare l'attività del comando rispetto all'attività di copia, vedere [selezionare le attività di copia e comando durante la copia dei dati](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data).

### <a name="copy-in-bulk-from-a-database-template"></a>Copia in blocco da un modello di database

La [copia in blocco da un database in Azure Esplora dati usando il modello di Azure Data Factory](data-factory-template.md) è una pipeline di Azure Data Factory predefinita. Il modello viene usato per creare molte pipeline per database o per tabella per una maggiore velocità di copia dei dati. 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>Consente di selezionare tra le attività Copy e Azure Esplora dati Command durante la copia dei dati 

Questa sezione fornirà assistenza per la selezione dell'attività corretta per le esigenze di copia dei dati.

Quando si copiano dati da o in Azure Esplora dati, sono disponibili due opzioni disponibili in Azure Data Factory:
* Attività di copia.
* Attività del comando di Esplora dati di Azure, che esegue uno dei comandi di controllo che trasferiscono i dati in Azure Esplora dati. 

### <a name="copy-data-from-azure-data-explorer"></a>Copiare dati da Esplora dati di Azure
  
È possibile copiare dati da Esplora dati di Azure usando l'attività di copia o il comando [`.export`](/azure/kusto/management/data-export/) . Il `.export` comando esegue una query, quindi Esporta i risultati della query. 

Vedere la tabella seguente per un confronto tra l'attività di copia e il comando `.export` per la copia dei dati da Esplora dati di Azure.

| | Attività di copia | . Export (comando) |
|---|---|---|
| **Descrizione del flusso** | ADF esegue una query su kusto, elabora il risultato e lo invia all'archivio dati di destinazione. <br>(**ADX > ADF > Archivio dati sink**) | ADF Invia un comando di controllo `.export` ad Azure Esplora dati, che esegue il comando e invia i dati direttamente all'archivio dati di destinazione. <br>(**ADX > Archivio dati sink**) |
| **Archivi dati di destinazione supportati** | Un'ampia gamma di [archivi dati supportati](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2, BLOB di Azure, database SQL |
| **Prestazioni** | Centralizzata | <ul><li>Distribuito (impostazione predefinita), esportazione di dati da più nodi simultaneamente</li><li>Efficienza e velocità.</li></ul> |
| **Limiti del server** | I [limiti di query](/azure/kusto/concepts/querylimits) possono essere estesi o disabilitati. Per impostazione predefinita, le query ADF contengono: <ul><li>Limite di dimensioni di 500.000 record o 64 MB.</li><li>Limite di tempo di 10 minuti.</li><li>`noTruncation` impostato su false.</li></ul> | Per impostazione predefinita, estende o Disabilita i limiti della query: <ul><li>I limiti delle dimensioni sono disabilitati.</li><li>Il timeout del server è esteso a 1 ora.</li><li>`MaxMemoryConsumptionPerIterator` e `MaxMemoryConsumptionPerQueryPerNode` sono estesi fino al massimo (5 GB, TotalPhysicalMemory/2).</li></ul>

> [!TIP]
> Se la destinazione della copia è uno degli archivi dati supportati dal comando `.export` e se nessuna delle funzionalità dell'attività di copia è cruciale per le proprie esigenze, selezionare il comando `.export`.

### <a name="copying-data-to-azure-data-explorer"></a>Copia dei dati in Azure Esplora dati

È possibile copiare i dati in Azure Esplora dati usando l'attività di copia o i comandi di inserimento, ad esempio inserimento [da query](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`, `.set-or-replace`, `.set`, `.replace)`e inserimento [dalla risorsa di archiviazione](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`). 

Vedere la tabella seguente per un confronto tra l'attività di copia e i comandi di inserimento per la copia dei dati in Esplora dati di Azure.

| | Attività di copia | Inserimento da query<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | Inserimento da risorsa di archiviazione <br> `.ingest` |
|---|---|---|---|
| **Descrizione del flusso** | ADF recupera i dati dall'archivio dati di origine, li converte in un formato tabulare ed esegue le modifiche necessarie al mapping dello schema. ADF carica quindi i dati nei BLOB di Azure, li suddivide in blocchi, quindi Scarica i BLOB per inserirli nella tabella ADX. <br> (**Archivio dati di origine > ADF > BLOB di Azure > ADX**) | Questi comandi possono eseguire una query o un comando `.show` e inserire i risultati della query in una tabella (**ADX > ADX**). | Questo comando inserisce i dati in una tabella eseguendo il pull dei dati da uno o più elementi di archiviazione cloud. |
| **Archivi dati di origine supportati** |  [varie opzioni](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS gen 2, BLOB di Azure, SQL (usando il plug-in sql_request), Cosmos (usando il plug-in cosmosdb_sql_request) e qualsiasi altro archivio dati che fornisce API HTTP o Python. | Filesystem, archiviazione BLOB di Azure, ADLS gen 1, ADLS gen 2 |
| **Prestazioni** | Le inserimenti vengono accodate e gestite, garantendo inserimenti di dimensioni ridotte e garantendo una disponibilità elevata grazie al bilanciamento del carico, ai tentativi e alla gestione degli errori. | <ul><li>Questi comandi non sono stati progettati per l'importazione di volumi elevati di dati.</li><li>Funziona come previsto e più economico. Tuttavia, per gli scenari di produzione e quando le tariffe e le dimensioni dei dati sono grandi, usare l'attività di copia.</li></ul> |
| **Limiti del server** | <ul><li>Nessun limite di dimensione.</li><li>Limite massimo di timeout: 1 ora per ogni BLOB inserito. |<ul><li>Esiste solo un limite di dimensioni per la parte della query, che può essere ignorato specificando `noTruncation=true`.</li><li>Limite massimo di timeout: 1 ora.</li></ul> | <ul><li>Nessun limite di dimensione.</li><li>Limite massimo di timeout: 1 ora.</li></ul>|

> [!TIP]
> * Quando si copiano dati da ADF ad Azure Esplora dati usare i comandi di `ingest from query`.  
> * Per i set di dati di grandi dimensioni (> 1 GB), usare l'attività di copia.  

## <a name="required-permissions"></a>Autorizzazioni necessarie

Nella tabella seguente sono elencate le autorizzazioni necessarie per diversi passaggi dell'integrazione con Azure Data Factory.

| Passaggio | Operazione | Livello minimo di autorizzazioni | note |
|---|---|---|---|
| **Creare un servizio collegato** | Navigazione nel database | *visualizzatore database* <br>L'utente che ha eseguito l'accesso con ADF deve essere autorizzato a leggere i metadati del database. | L'utente può specificare il nome del database manualmente. |
| | Test della connessione | *monitoraggio database* o inserimento di *tabelle* <br>L'entità servizio deve essere autorizzata a eseguire comandi `.show` a livello di database o a livello di tabella. | <ul><li>TestConnection verifica la connessione al cluster e non al database. Questa operazione può essere eseguita anche se il database non esiste.</li><li>Le autorizzazioni di amministratore della tabella non sono sufficienti.</li></ul>|
| **Creazione di un set di dati** | Navigazione tra tabelle | *Monitoraggio database* <br>L'utente che ha eseguito l'accesso con ADF deve essere autorizzato a eseguire i comandi `.show` a livello di database. | L'utente può specificare il nome della tabella manualmente.|
| **Creazione di un set di dati o di un'attività di** **copia** | Anteprima dati | *visualizzatore database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | | 
|   | Importa schema | *visualizzatore database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | Quando ADX è l'origine di una copia tabulare in tabulare, ADF importerà automaticamente lo schema, anche se l'utente non ha importato lo schema in modo esplicito. |
| **ADX come sink** | Creazione di un mapping di colonna per nome | *Monitoraggio database* <br>L'entità servizio deve essere autorizzata a eseguire i comandi `.show` a livello di database. | <ul><li>Tutte le operazioni obbligatorie funzioneranno con l'inserimento di *tabelle*.</li><li> Alcune operazioni facoltative possono avere esito negativo.</li></ul> |
|   | <ul><li>Creare un mapping CSV nella tabella</li><li>Elimina il mapping</li></ul>| inserimento di *tabelle* o *amministratore di database* <br>L'entità servizio deve essere autorizzata a apportare modifiche a una tabella. | |
|   | Inserire dati | inserimento di *tabelle* o *amministratore di database* <br>L'entità servizio deve essere autorizzata a apportare modifiche a una tabella. | | 
| **ADX come origine** | Esegui query | *visualizzatore database* <br>L'entità servizio deve essere autorizzata a leggere i metadati del database. | |
| **Comando kusto** | | In base al livello di autorizzazione di ogni comando. |

## <a name="performance"></a>Prestazioni 

Se Esplora dati di Azure è l'origine e si usa l'attività di ricerca, copia o comando che contiene una query in cui, fare riferimento alle [procedure consigliate](/azure/kusto/query/best-practices) per le prestazioni e alla [documentazione di ADF per l'attività di copia](/azure/data-factory/copy-activity-performance).
  
Questa sezione illustra l'uso dell'attività di copia in cui Azure Esplora dati è il sink. La velocità effettiva stimata per il sink di Azure Esplora dati è 11-13 MBps. La tabella seguente illustra in dettaglio i parametri che influiscono sulle prestazioni del sink di Azure Esplora dati.

| . | note |
|---|---|
| **Prossimità geografica dei componenti** | Inserire tutti i componenti nella stessa area:<ul><li>archivi dati di origine e sink.</li><li>Runtime di integrazione di ADF.</li><li>Il cluster ADX.</li></ul>Assicurarsi che almeno il runtime di integrazione si trovi nella stessa area del cluster ADX. |
| **Numero di DIUs** | 1 VM per ogni 4 DIUs usato da ADF. <br>L'aumento del valore di DIUs sarà utile solo se l'origine è un archivio basato su file con più file. Ogni macchina virtuale elaborerà quindi un file diverso in parallelo. Quindi, la copia di un singolo file di grandi dimensioni avrà una latenza maggiore rispetto alla copia di più file più piccoli.|
|**Quantità e SKU del cluster ADX** | Il numero elevato di nodi ADX aumenterà il tempo di elaborazione dell'inserimento.|
| **Parallelismo** | Per copiare una quantità molto elevata di dati da un database, partizionare i dati e quindi usare un ciclo ForEach che copia ogni partizione in parallelo o usare la [copia bulk dal database al modello di Esplora dati di Azure](data-factory-template.md). Nota: **le impostazioni** > **grado di parallelismo** nell'attività di copia non è pertinente per ADX. |
| **Complessità dell'elaborazione dei dati** | La latenza varia in base al formato del file di origine, al mapping delle colonne e alla compressione.|
| **Macchina virtuale che esegue il runtime di integrazione** | <ul><li>Per la copia di Azure, le macchine virtuali ADF e gli SKU del computer non possono essere modificati.</li><li> Per la copia da locale ad Azure, determinare che la macchina virtuale che ospita il runtime di integrazione self-hosted è sufficientemente sicura.</li></ul>|

## <a name="monitor-activity-progress"></a>Monitorare lo stato dell'attività

* Quando si monitora lo stato di avanzamento dell'attività, la proprietà *scrittura dati* può essere molto più grande della proprietà *lettura dati* perché i *dati letti* vengono calcolati in base alle dimensioni del file binario, mentre i *dati scritti* vengono calcolati in base alle dimensioni in memoria, dopo la deserializzazione e la decompressione dei dati.

* Quando si monitora lo stato dell'attività, è possibile osservare che i dati vengono scritti nel sink di Esplora dati di Azure. Quando si esegue una query sulla tabella di Esplora dati di Azure, si noterà che i dati non sono stati ricevuti. Questo è dovuto al fatto che durante la copia in Azure Esplora dati sono presenti due fasi. 
    * La prima fase legge i dati di origine, li suddivide in blocchi da 900 MB e carica ogni blocco in un BLOB di Azure. La prima fase viene visualizzata dalla visualizzazione dello stato di avanzamento dell'attività di ADF. 
    * La seconda fase inizia dopo il caricamento di tutti i dati nei BLOB di Azure. I nodi del motore di Azure Esplora dati scaricano i BLOB e inseriscono i dati nella tabella di sink. I dati vengono quindi visualizzati nella tabella di Esplora dati di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [copiare dati in Esplora dati di Azure usando Azure Data Factory](data-factory-load-data.md).
* Informazioni sull'uso di [Azure Data Factory modello per la copia bulk dal database in Azure Esplora dati](data-factory-template.md).
* Informazioni sull'uso [di Azure Data Factory attività Command per eseguire i comandi di controllo Esplora dati di Azure](data-factory-command-activity.md).
* Informazioni sulle [query di Azure Esplora dati](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.




---
title: Risolvere i problemi quando si usa il trigger di Funzioni di Azure per Cosmos DBTroubleshoot issues when using Azure Functions trigger for Cosmos DB
description: Problemi comuni, soluzioni alternative e passaggi di diagnostica, quando si usa il trigger Funzioni di Azure per Cosmos DBCommon issues, workarounds, and diagnostic steps, when using the Azure Functions trigger for Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365509"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnosticare e risolvere i problemi quando si usa il trigger di Funzioni di Azure per Cosmos DBDiagnose and troubleshoot issues when using Azure Functions trigger for Cosmos DB

Questo articolo illustra problemi comuni, soluzioni alternative e passaggi diagnostici quando si usa il trigger Funzioni di [Azure per Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Dependencies

Il trigger di Funzioni di Azure e le associazioni per Cosmos DB dipendono dai pacchetti di estensione nel runtime di Base Funzioni di Azure.The Azure Functions trigger and bindings for Cosmos DB depend on the extension packages over the base Azure Functions runtime. Mantenere sempre aggiornati questi pacchetti, in quanto potrebbero includere correzioni e nuove funzionalità che potrebbero risolvere eventuali problemi che potrebbero verificarsi:

* Per funzioni di Azure V2, vedere [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Per funzioni di Azure V1, vedere [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Questo articolo farà sempre riferimento a Funzioni di Azure V2 ogni volta che viene menzionato il runtime, a meno che non venga specificato in modo esplicito.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Usare Azure Cosmos DB SDK in modo indipendenteConsume the Azure Cosmos DB SDK independently

La funzionalità principale del pacchetto di estensione consiste nel fornire il supporto per il trigger di funzioni di Azure e le associazioni per Cosmos DB. Include inoltre [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), utile se si vuole interagire con Azure Cosmos DB a livello di programmazione senza usare il trigger e le associazioni.

Se si vuole usare Azure Cosmos DB SDK, assicurarsi di non aggiungere al progetto un altro riferimento al pacchetto NuGet.If want to use the Azure Cosmos DB SDK, make sure that you don't add to your project another NuGet package reference. Al contrario, **lasciare che il riferimento SDK risolva la risoluzione tramite il pacchetto di estensione di Funzioni**di Azure. Usare Azure Cosmos DB SDK separatamente dal trigger e dalle associazioniConsume the Azure Cosmos DB SDK separately from the trigger and bindings

Inoltre, se si crea manualmente la propria istanza del [client Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), è necessario seguire il modello di avere una sola istanza del client utilizzando un approccio di modello [Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Questo processo eviterà i potenziali problemi socket nelle operazioni.

## <a name="common-scenarios-and-workarounds"></a>Scenari comuni e soluzioni alternative

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funzione di Azure ha esito negativo con raccolta dei messaggi di errore inesistenteAzure Function fails with error message collection doesn't exist

Funzione di Azure ha esito negativo con il messaggio di errore "la raccolta di origine 'nome-raccolta' (nel database 'nome-database') o la raccolta lease 'collection2-name' (nel database 'database2-name') non esiste. Entrambe le raccolte devono esistere prima dell'avvio del listener. Per creare automaticamente la raccolta lease, impostare 'CreateLeaseCollectionIfNotExists' su 'true'"

Ciò significa che uno o entrambi i contenitori di Azure Cosmos necessari per il funzionamento del trigger non esistono o non sono raggiungibili dalla funzione di Azure.This means that either one or both of the Azure Cosmos containers required for the trigger to work do not exist or not reachable to the Azure Function. **L'errore stesso indicherà quale database e contenitore di Azure Cosmos è il trigger che cerca** in base alla configurazione.

1. Verificare `ConnectionStringSetting` l'attributo e che faccia riferimento a **un'impostazione presente nell'app per**le funzioni di Azure . Il valore di questo attributo non deve essere la stringa di connessione stessa, ma il nome dell'impostazione di configurazione.
2. Verificare `databaseName` che `collectionName` l'account di Azure Cosmos esista nell'account Cosmos di Azure. Se si usa la sostituzione `%settingName%` automatica del valore (usando i modelli), assicurarsi che il nome dell'impostazione esista nell'app per le funzioni di Azure.If you are using automatic value replacement (using patterns), make sure the name of the setting exists in your Azure Function App.
3. Se non si specifica `LeaseCollectionName/leaseCollectionName`un , il valore predefinito è "leases". Verificare che tale contenitore esista. Facoltativamente, è `CreateLeaseCollectionIfNotExists` possibile impostare `true` l'attributo nel trigger su per crearlo automaticamente.
4. Verificare la [configurazione del firewall dell'account Azure Cosmos](how-to-configure-firewall.md) per verificare che non stia bloccando la funzione di Azure.Verify your Azure Cosmos account's Firewall configuration to see that it's not it's not blocking the Azure Function.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Funzione di Azure non viene avviata con "Raccolta di velocità effettiva condivisa deve avere una chiave di partizione"Azure Function fails to start with "Shared throughput collection should have a partition key"

Le versioni precedenti dell'estensione del database Cosmos di Azure non supportavano l'utilizzo di un contenitore lease creato all'interno di un database di [velocità effettiva condiviso.](./set-throughput.md#set-throughput-on-a-database) Per risolvere questo problema, aggiornare l'estensione [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) per ottenere la versione più recente.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Funzione di Azure non viene avviato con "PartitionKey deve essere fornito per questa operazione".

Questo errore indica che si sta attualmente utilizzando una raccolta di lease partizionata con [una dipendenza](#dependencies)di estensione precedente. Eseguire l'aggiornamento all'ultima versione disponibile. Se è in esecuzione in Funzioni di Azure V1, sarà necessario eseguire l'aggiornamento a Funzioni di Azure V2.If you are currently running on Azure Functions V1, you will need to upgrade to Azure Functions V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funzione di Azure non viene avviata con "La raccolta di lease, se partizionata, deve avere la chiave di partizione uguale a id".

Questo errore indica che il contenitore dei lease corrente è `/id`partizionato, ma il percorso della chiave di partizione non è . Per risolvere questo problema, è necessario ricreare `/id` il contenitore lease con come chiave di partizione.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Viene visualizzato un "Valore non può essere null. Nome parametro: o" nei log di Funzioni di Azure quando si tenta di eseguire il triggerParameter name: o" in your Azure Functions logs when you try to Run the Trigger

Questo problema viene visualizzato se si utilizza il portale di Azure e si tenta di selezionare il pulsante **Esegui** sullo schermo durante l'ispezione di una funzione di Azure che usa il trigger. Il trigger non richiede la selezione dell'avvio da esecuzione, verrà avviato automaticamente quando viene distribuita la funzione di Azure.The trigger does not require for you to select Run to start, it will automatically start when the Azure Function is deployed. Se si vuole controllare il flusso di log della funzione di Azure nel portale di Azure, passare al contenitore monitorato e inserire alcuni nuovi elementi, verrà visualizzato automaticamente il trigger in esecuzione.

### <a name="my-changes-take-too-long-to-be-received"></a>Le modifiche richiedono troppo tempo per essere ricevute

Questo scenario può avere più cause e tutte devono essere controllate:This scenario can have multiple causes and all of them should be checked:

1. La funzione di Azure è distribuita nella stessa area dell'account Azure Cosmos? Per una latenza di rete ottimale, sia la funzione di Azure che l'account Azure Cosmos devono trovarsi nella stessa area di Azure.
2. Le modifiche apportate nel contenitore Azure Cosmos sono continue o sporadiche?
Se sono sporadiche, potrebbe esserci un ritardo tra l'archiviazione delle modifiche e la loro scelta da parte della funzione di Azure. Questo avviene perché internamente, quando controlla la presenza di modifiche nel contenitore Azure Cosmos e non ne trova nessuna in sospeso, il trigger rimane in sospensione per un periodo di tempo configurabile (per impostazione predefinita 5 secondi) prima di controllare la presenza di nuove modifiche (per evitare un consumo di UR elevato). È possibile configurare questo tempo di sospensione tramite l'impostazione `FeedPollDelay/feedPollDelay` nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del trigger (si prevede un valore in millisecondi).
3. Il contenitore Cosmos di Azure potrebbe essere [limitato dalla tariffa.](./request-units.md)
4. È possibile `PreferredLocations` usare l'attributo nel trigger per specificare un elenco separato da virgole di aree di Azure per definire un ordine di connessione preferito personalizzato.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Alcune modifiche vengono ripetute nel trigger

Il concetto di "modifica" è un'operazione su un documento. Gli scenari più comuni in cui vengono ricevuti gli eventi per lo stesso documento sono:The most common scenarios where events for the same document is received are:
* L'account utilizza la coerenza finale. Durante l'utilizzo del feed di modifiche in un livello di coerenza Eventuale, potrebbero verificarsi eventi duplicati tra le successive operazioni di lettura del feed di modifiche (l'ultimo evento di un'operazione di lettura viene visualizzato come la prima della successiva).
* È in corso l'aggiornamento del documento. Il feed di modifiche può contenere più operazioni per gli stessi documenti, se il documento riceve gli aggiornamenti, può prelevare più eventi (uno per ogni aggiornamento). Un modo semplice per distinguere tra le diverse `_lsn` operazioni per lo stesso documento consiste nel tenere traccia della [proprietà per ogni modifica.](change-feed.md#change-feed-and-_etag-_lsn-or-_ts) Se non corrispondono, si tratta di modifiche diverse rispetto allo stesso documento.
* Se si identificano i `id`documenti solo in base a `id` , tenere presente che l'identificatore `id` univoco di un documento è la e la relativa chiave di partizione (possono essere presenti due documenti con la stessa chiave di partizione ma diversa).

### <a name="some-changes-are-missing-in-my-trigger"></a>Alcune modifiche non sono presenti nel trigger

Se si scopre che alcune delle modifiche che si sono verificate nel contenitore Cosmos di Azure non vengono rilevate dalla funzione di Azure, è necessario eseguire un passaggio di analisi iniziale.

Quando la funzione di Azure riceve le modifiche, spesso le elabora e facoltativamente può inviare il risultato a un'altra destinazione. Quando si analizzano le modifiche mancanti, assicurarsi di **misurare le modifiche ricevute nel punto** di inserimento (all'avvio della funzione di Azure), non nella destinazione.

Se nella destinazione mancano alcune modifiche, ciò potrebbe significare che si è verificato un errore durante l'esecuzione della funzione di Azure dopo che le modifiche sono state ricevute.

In questo scenario, la soluzione migliore `try/catch` consiste nell'aggiungere blocchi nel codice e all'interno dei cicli che potrebbero elaborare le modifiche, per rilevare eventuali errori per un particolare sottoinsieme di elementi e gestirli di conseguenza (inviarli a un altro archivio per ulteriori analisi o tentativi). 

> [!NOTE]
> Per impostazione predefinita, il trigger di Funzioni di Azure per Cosmos DB non cerca di ripetere un batch di modifiche se si è verificata un'eccezione non gestita durante l'esecuzione del codice. Ciò significa che il motivo per cui le modifiche non sono arrivate a destinazione è perché non è possibile elaborarle.

Se alcune modifiche non sono state ricevute affatto dal trigger, lo scenario più comune è che è in **esecuzione un'altra funzione**di Azure . Potrebbe trattarsi di un'altra funzione di Azure distribuita in Azure o di una funzione di Azure in esecuzione in locale nel computer di uno sviluppatore con **esattamente la stessa configurazione** (stessi contenitori monitorati e di lease) e questa funzione di Azure sta rubando un sottoinsieme delle modifiche che ci si aspetta che la funzione di Azure venga elaborata.

Inoltre, lo scenario può essere convalidato, se si conosce il numero di istanze dell'app per le funzioni di Azure in esecuzione. Se si esamina il contenitore lease e si conta il `Owner` numero di elementi lease al suo interno, i valori distinti della proprietà in essi contenuti devono essere uguali al numero di istanze dell'app per le funzioni. Se sono presenti più proprietari rispetto alle istanze note dell'app per le funzioni di Azure, significa che questi proprietari aggiuntivi sono quelli che "rubano" le modifiche.

Un modo semplice per risolvere questa situazione, consiste nell'applicare un `LeaseCollectionPrefix/leaseCollectionPrefix` alla funzione con un valore nuovo / diverso o, in alternativa, testare con un nuovo contenitore di lease.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>È necessario riavviare e rielaborare tutti gli elementi nel contenitore dall'inizio 
Per rielaborare tutti gli elementi in un contenitore dall'inizio:
1. Arrestare la funzione di Azure se è attualmente in esecuzione. 
1. Eliminare i documenti nella raccolta di lease (o eliminare e ricreare la raccolta lease in modo che sia vuota)
1. Impostare l'attributo [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger nella funzione su true. 
1. Riavviare la funzione di Azure.Restart the Azure function. Ora leggerà ed elaborerà tutte le modifiche dall'inizio. 

L'impostazione [di StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) su true indicherà alla funzione di Azure di avviare la lettura delle modifiche dall'inizio della cronologia della raccolta anziché l'ora corrente. Questo funziona solo quando non sono presenti lease già creati (vale a dire, documenti nella raccolta di lease). L'impostazione di questa proprietà su true quando sono presenti lease già creati non ha alcun effetto; In questo scenario, quando una funzione viene arrestata e riavviata, inizierà la lettura dall'ultimo checkpoint, come definito nella raccolta di lease. Per rielaborare dall'inizio, seguire i passaggi precedenti 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>L'associazione può essere eseguita solo con IReadOnlyList\<Document> o JArray

Questo errore si verifica se il progetto Funzioni di Azure (o qualsiasi progetto a cui si fa riferimento) contiene un riferimento NuGet manuale all'SDK del database di Azure Cosmos con una versione diversa da quella fornita [dall'estensione DB Cosmos](./troubleshoot-changefeed-functions.md#dependencies)di Azure.

Per risolvere questo problema, rimuovere il riferimento Manuale NuGet che è stato aggiunto e lasciare che il riferimento SDK cosmos DB di Azure risolvere tramite il pacchetto di estensione Cosmos DB di Azure.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Modifica dell'intervallo di polling della funzione di Azure per le modifiche di rilevamento

Come spiegato in precedenza per [le modifiche personali richiedono troppo tempo per essere ricevute,](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)la funzione di Azure rimarrà in attesa di un periodo di tempo configurabile (5 secondi, per impostazione predefinita) prima di verificare la presenza di nuove modifiche (per evitare un elevato consumo di RU). È possibile configurare questo tempo di sospensione tramite l'impostazione `FeedPollDelay/feedPollDelay` nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) del trigger (si prevede un valore in millisecondi).

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio per le funzioni di AzureEnable monitoring for your Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK Troubleshooting](./troubleshoot-dot-net-sdk.md)

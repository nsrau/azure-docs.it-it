---
title: Diagnosticare e risolvere i problemi quando si usano Trigger di Azure Cosmos DB in funzioni di Azure
description: Problemi comuni e soluzioni alternative diagnostica passaggi, quando si usa il Trigger di Azure Cosmos DB con funzioni di Azure
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09ea70ac302806b4cb0e97fde92dda4208e3d659
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734508"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnosticare e risolvere i problemi quando si usano Trigger di Azure Cosmos DB in funzioni di Azure

Questo articolo descrive i problemi comuni, le soluzioni alternative e i passaggi di diagnostica, quando si usa la [Trigger di Azure Cosmos DB](change-feed-functions.md) con funzioni di Azure.

## <a name="dependencies"></a>Dependencies

I Trigger di Azure Cosmos DB e le associazioni dipendono i pacchetti di estensione tramite il runtime di funzioni di Azure di base. Mantenere sempre questi pacchetti aggiornati, come possono includere correzioni e nuove funzionalità che potrebbero risolvere eventuali problemi potenziali che possono verificarsi:

* Per le funzioni di Azure V2, vedere [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Per la versione 1 di funzioni di Azure, vedere [webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Questo articolo farà sempre riferimento a funzioni di Azure V2 ogni volta che viene indicato il runtime, a meno che non specificato in modo esplicito.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Usare Azure Cosmos DB SDK in modo indipendente

La funzionalità principale del pacchetto di estensione consiste nel fornire supporto per i trigger di Azure Cosmos DB e le associazioni. Include anche il [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), ciò è utile se si vuole interagire con Azure Cosmos DB a livello di codice senza usare i trigger e associazioni.

Se desidera utilizzare il SDK di Azure Cosmos DB, assicurarsi che non si aggiunge al progetto un altro riferimento al pacchetto NuGet. Al contrario, **consentire il riferimento al SDK di risolvere tramite il pacchetto di estensione di funzioni di Azure**. Usare Azure Cosmos DB SDK separatamente dal trigger e associazioni

Inoltre, se si sta creando manualmente una propria istanza del [client SDK di Azure Cosmos DB](./sql-api-sdk-dotnet-core.md), è consigliabile seguire il modello di avere solo un'istanza del client [usando un approccio di modello Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c) . Questo processo verrà evitare i potenziali problemi di socket nelle operazioni.

## <a name="common-scenarios-and-workarounds"></a>Scenari comuni e soluzioni alternative

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure funzione ha esito negativo con l'insieme di messaggi di errore non esiste

Funzioni di Azure ha esito negativo con messaggio di errore "una raccolta di origine 'raccolta-name' (nel database 'database-name') o la raccolta di lease 'collection2-name' (nel database 'database2-name') non esiste. Entrambe le raccolte devono esistere prima che inizi il listener. Per creare automaticamente la raccolta di lease, impostare 'CreateLeaseCollectionIfNotExists' su 'true' "

Ciò significa che uno o entrambi i contenitori di Azure Cosmos necessari per il trigger funzioni non esistono o non sono raggiungibili alla funzione di Azure. **L'errore stesso indicherà quale database Cosmos Azure e contenitori è il trigger per** in base alla configurazione.

1. Verificare i `ConnectionStringSetting` attributo e la sua **fa riferimento a un'impostazione esistente in App per le funzioni Azure**. Il valore su questo attributo non deve essere la stringa di connessione, ma il nome dell'impostazione di configurazione.
2. Verificare che il `databaseName` e `collectionName` già nell'account di Azure Cosmos. Se si usa la sostituzione valore automatico (utilizzando `%settingName%` pattern), verificare che il nome dell'impostazione presente nell'App per le funzioni di Azure.
3. Se non si specifica un `LeaseCollectionName/leaseCollectionName`, il valore predefinito è "lease". Verificare che tale contenitore non esiste. Facoltativamente è possibile impostare il `CreateLeaseCollectionIfNotExists` attributo nel Trigger per `true` crearla automaticamente.
4. Verificare i [configurazione del Firewall dell'account Azure Cosmos](how-to-configure-firewall.md) per verificare che sia non non blocchi la funzione di Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Funzioni di Azure non riesce a iniziare con "raccolta di velocità effettiva condiviso deve avere una chiave di partizione"

Le versioni precedenti dell'estensione di Azure Cosmos DB non supportava usando un contenitore di lease che è stato creato all'interno di un [database di velocità effettiva condiviso](./set-throughput.md#set-throughput-on-a-database). Per risolvere questo problema, aggiornare il [cosmosdb](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) estensione per ottenere la versione più recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funzioni di Azure non riesce a iniziare con "la raccolta di lease, se è partizionata, deve avere la chiave di partizione uguale all'id."

Questo errore indica che il contenitore di lease corrente è partizionato, ma non è il percorso della chiave di partizione `/id`. Per risolvere questo problema, è necessario ricreare il contenitore di lease con `/id` come chiave di partizione.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Viene visualizzato un "valore non può essere null. Nome del parametro: o "nei log di funzioni di Azure quando si prova a eseguire il Trigger

Il problema si verifica se si usa il portale di Azure e si tenta di selezionare il **eseguire** pulsante sullo schermo quando si esamina una funzione di Azure che usa il trigger. Il trigger non richiede la selezione eseguire per avviare, verrà avviato automaticamente quando si distribuisce la funzione di Azure. Se si desidera controllare il flusso di registrazione della funzione di Azure nel portale di Azure, passare al contenitore monitorato basta inserire alcuni nuovi elementi, si verrà visualizzato automaticamente il Trigger in esecuzione.

### <a name="my-changes-take-too-long-be-received"></a>Esecuzione delle modifiche troppo lungo da ricevere

Questo scenario può avere più cause e tutti gli elementi devono essere controllati:

1. La funzione di Azure viene distribuita nella stessa area dell'account Azure Cosmos? Ottimali latenza di rete, la funzione di Azure sia all'account Azure Cosmos deve essere installati nella stessa area di Azure.
2. Le modifiche eseguite nel contenitore di Azure Cosmos continuo o sporadico?
Se quest'ultimo, potrebbe esserci un ritardo tra le modifiche vengono archiviate e la funzione di Azure esegue la scelta di essi. Questo avviene perché, internamente, quando il trigger controlla le modifiche nel contenitore di Azure Cosmos e trova nessuno in sospeso da leggere, rimarrà in sospensione per una quantità configurabile di tempo (5 secondi, per impostazione predefinita) la presenza di nuove modifiche (evitare l'utilizzo elevato di UR). È possibile configurare questo periodo di inattività tramite il `FeedPollDelay/feedPollDelay` impostazione [configuration](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) di trigger (il valore deve essere espresso in millisecondi).
3. Potrebbe essere il contenitore di Azure Cosmos [soggette a limitazioni](./request-units.md).
4. È possibile usare il `PreferredLocations` attributo nel trigger per specificare un elenco delimitato da virgole di aree di Azure per definire un ordine di connessione preferito personalizzato.

### <a name="some-changes-are-missing-in-my-trigger"></a>Alcune modifiche non sono presenti nella mio Trigger

Se si ritiene che alcune delle modifiche che si sono verificati nel contenitore di Azure Cosmos non sono viene prelevati dalla funzione di Azure, è un passaggio di analisi iniziale che deve avvenire.

Quando la funzione di Azure riceve le modifiche, spesso li elabora e può facoltativamente, inviare il risultato a un'altra destinazione. Quando si stanno analizzando le modifiche mancante, verificare che si **misure quali modifiche vengono ricevuti in corrispondenza del punto di inserimento** (quando la funzione di Azure Avvia), non nella destinazione.

Se non sono presenti alcune modifiche nella destinazione, ciò vuol dire che è un errore che si verifica durante l'esecuzione di funzioni di Azure dopo le modifiche sono state ricevute.

In questo scenario, la linea di condotta migliore consiste nell'aggiungere `try/catch blocks` nel codice e all'interno di cicli che potrebbero essere elaborando le modifiche, per rilevare eventuali errori per un determinato subset di elementi e gestirli in modo appropriato (inviarli a un'altra risorsa di archiviazione per altri analisi o un nuovo tentativo). 

> [!NOTE]
> Il Trigger di Azure Cosmos DB, per impostazione predefinita, non sarà ripetere un batch di modifiche se si verifica un'eccezione non gestita durante l'esecuzione del codice. Ciò significa che il motivo che le modifiche non sono arrivato nella destinazione è perché che hanno esito negativo per l'elaborazione.

Se si ritiene che alcune modifiche non sono state ricevute affatto da trigger, lo scenario più comune è che è presente **in esecuzione un'altra funzione di Azure**. Può trattarsi di un'altra funzione di Azure distribuite in Azure o dispone di una funzione di Azure in esecuzione localmente sul computer dello sviluppatore **esattamente la stessa configurazione** (stesso monitorati e i contenitori di lease), e questa funzione di Azure fa per rubare un subset delle modifiche si aspetta la funzione di Azure per l'elaborazione.

Inoltre, lo scenario può essere convalidato, se si conosce il numero di istanze di App per le funzioni sono in esecuzione. Se è possibile controllare il contenitore di lease e contare il numero di elementi di lease all'interno, i valori distinti del `Owner` proprietà in essi contenuti deve essere uguale al numero di istanze di App per le funzioni. Se sono presenti più proprietari rispetto alle istanze di App di funzioni di Azure note, significa che questi proprietari extra sono quello "furto" le modifiche.

Un modo semplice per risolvere questa situazione, è per applicare un `LeaseCollectionPrefix/leaseCollectionPrefix` alla funzione con un valore nuovo o diverso o, in alternativa, eseguire il test con un nuovo contenitore di lease.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Associazione può essere eseguita solo con IReadOnlyList<Document> o JArray

Questo errore si verifica se il progetto di funzioni di Azure (o qualsiasi progetto di riferimento) contiene un riferimento di NuGet manuale ad Azure Cosmos DB SDK con una versione diversa rispetto a quello fornito dal [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies).

Per risolvere questa situazione, rimuovere il riferimento NuGet manuale è stato aggiunto e consentire il riferimento di Azure Cosmos DB SDK risolto tramite il pacchetto di estensione Cosmos DB di funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio per le funzioni di Azure](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK di risoluzione dei problemi](./troubleshoot-dot-net-sdk.md)
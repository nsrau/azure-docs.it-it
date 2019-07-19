---
title: Diagnosticare e risolvere i problemi quando si usa Azure Cosmos DB trigger in funzioni di Azure
description: Problemi comuni, soluzioni alternative e procedure di diagnostica quando si usa il trigger Azure Cosmos DB con funzioni di Azure
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 9c728a735e56e461e49dd3f594186c9c0192a3f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250021"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnosticare e risolvere i problemi quando si usa Azure Cosmos DB trigger in funzioni di Azure

Questo articolo descrive i problemi comuni, le soluzioni alternative e i passaggi di diagnostica, quando si usa il [Trigger Azure Cosmos DB](change-feed-functions.md) con funzioni di Azure.

## <a name="dependencies"></a>Dipendenze

Il trigger e le associazioni di Azure Cosmos DB dipendono dai pacchetti di estensione nel runtime di funzioni di Azure di base. Mantieni sempre aggiornati i pacchetti, in quanto potrebbero includere correzioni e nuove funzionalità che potrebbero risolvere eventuali problemi potenziali che possono verificarsi:

* Per funzioni di Azure V2, vedere [Microsoft. Azure. webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Per funzioni di Azure V1, vedere [Microsoft. Azure. webjobs. Extensions. DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Questo articolo si riferisce sempre a funzioni di Azure V2 ogni volta che viene indicato il runtime, a meno che non venga specificato in modo esplicito.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Utilizzare il Azure Cosmos DB SDK in modo indipendente

La funzionalità principale del pacchetto di estensione consiste nel fornire supporto per il trigger e le associazioni Azure Cosmos DB. Include anche [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), utile se si desidera interagire con Azure Cosmos DB a livello di codice senza utilizzare il trigger e le associazioni.

Se si vuole usare Azure Cosmos DB SDK, assicurarsi di non aggiungere al progetto un altro riferimento al pacchetto NuGet. Al contrario, **lasciare che il riferimento all'SDK venga risolto tramite il pacchetto di estensione delle funzioni di Azure**. Utilizzare il Azure Cosmos DB SDK separatamente dal trigger e dalle associazioni

Inoltre, se si crea manualmente una propria istanza del [client Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), è necessario seguire il modello per avere una sola istanza del client [usando un approccio basato su modello singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Questo processo eviterà i potenziali problemi di socket nelle operazioni.

## <a name="common-scenarios-and-workarounds"></a>Scenari comuni e soluzioni alternative

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>La funzione di Azure non riesce e la raccolta dei messaggi di errore non esiste

La funzione di Azure ha esito negativo con il messaggio di errore "la raccolta di origine ' Collection-Name ' (nel database ' database-name ') o la raccolta di lease ' Collection2-Name ' (nel database ' Database2-Name ') non esiste. Entrambe le raccolte devono esistere prima che il listener venga avviato. Per creare automaticamente la raccolta di lease, impostare "CreateLeaseCollectionIfNotExists" su "true" "

Ciò significa che uno o entrambi i contenitori di Azure Cosmos necessari per il funzionamento del trigger non esistono o non sono raggiungibili dalla funzione di Azure. **L'errore indica quali database e contenitori di Azure Cosmos è il trigger che cerca** in base alla configurazione.

1. Verificare l' `ConnectionStringSetting` attributo e che **faccia riferimento a un'impostazione esistente nel app per le funzioni di Azure**. Il valore di questo attributo non deve essere la stringa di connessione, ma il nome dell'impostazione di configurazione.
2. Verificare che `databaseName` e `collectionName` esistano nell'account Azure Cosmos. Se si usa la sostituzione automatica dei valori ( `%settingName%` usando i modelli), assicurarsi che il nome dell'impostazione esista nel app per le funzioni di Azure.
3. Se non si specifica un `LeaseCollectionName/leaseCollectionName`oggetto, il valore predefinito è "leases". Verificare che tale contenitore esista. Facoltativamente, è possibile impostare `CreateLeaseCollectionIfNotExists` l'attributo nel `true` trigger su per crearlo automaticamente.
4. Verificare la [configurazione del firewall dell'account Azure Cosmos](how-to-configure-firewall.md) per verificare che non stia bloccando la funzione di Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Non è possibile avviare la funzione di Azure con la "raccolta della velocità effettiva condivisa deve avere una chiave di partizione"

Le versioni precedenti dell'estensione Azure Cosmos DB non supportano l'utilizzo di un contenitore lease creato in un [database di velocità effettiva condivisa](./set-throughput.md#set-throughput-on-a-database). Per risolvere questo problema, aggiornare l'estensione [Microsoft. Azure. Webjobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) per ottenere la versione più recente.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>La funzione di Azure non inizia con "la raccolta di lease, se partizionata, deve avere una chiave di partizione uguale a ID".

Questo errore indica che il contenitore dei lease correnti è partizionato, ma il percorso della chiave di partizione `/id`non lo è. Per risolvere questo problema, è necessario ricreare il contenitore leases con `/id` come chiave di partizione.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Viene visualizzato un valore che non può essere null. Nome parametro: o "nei log di funzioni di Azure quando si tenta di eseguire il trigger

Questo problema viene visualizzato se si usa il portale di Azure e si tenta di selezionare il pulsante **Esegui** sullo schermo quando si esamina una funzione di Azure che usa il trigger. Per il trigger non è necessario selezionare Esegui per avviare, che verrà avviato automaticamente quando viene distribuita la funzione di Azure. Se si vuole controllare il flusso di log della funzione di Azure nella portale di Azure, è sufficiente passare al contenitore monitorato e inserire alcuni nuovi elementi. verrà visualizzato automaticamente il trigger in esecuzione.

### <a name="my-changes-take-too-long-be-received"></a>La ricezione delle modifiche richiede troppo tempo

Questo scenario può avere più cause e tutti devono essere controllati:

1. La funzione di Azure è stata distribuita nella stessa area dell'account Azure Cosmos? Per una latenza di rete ottimale, la funzione di Azure e l'account Azure Cosmos devono trovarsi nella stessa area di Azure.
2. Le modifiche apportate nel contenitore di Azure Cosmos sono continue o sporadiche?
Se è il secondo, è possibile che si verifichi un ritardo tra le modifiche archiviate e che la funzione di Azure le preleva. Questo avviene perché internamente, quando il trigger controlla la presenza di modifiche nel contenitore Azure Cosmos e ne trova nessuna in sospeso, rimarrà in sospensione per un periodo di tempo configurabile (per impostazione predefinita, 5 secondi) prima di controllare le nuove modifiche (per evitare un consumo di ur elevato). È possibile configurare questo tempo di sospensione tramite `FeedPollDelay/feedPollDelay` l'impostazione nella [configurazione](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) del trigger (il valore dovrebbe essere in millisecondi).
3. Il contenitore Azure Cosmos potrebbe avere una [frequenza limitata](./request-units.md).
4. È possibile usare l' `PreferredLocations` attributo nel trigger per specificare un elenco delimitato da virgole di aree di Azure per definire un ordine di connessione preferito personalizzato.

### <a name="some-changes-are-missing-in-my-trigger"></a>Alcune modifiche non sono presenti nel trigger

Se alcune delle modifiche apportate al contenitore Azure Cosmos non vengono prelevate dalla funzione di Azure, è necessario eseguire un passaggio di analisi iniziale.

Quando la funzione di Azure riceve le modifiche, le elabora spesso e può facoltativamente inviare il risultato a un'altra destinazione. Quando si esaminano le modifiche mancanti, assicurarsi di **misurare le modifiche ricevute nel punto di** inserimento (all'avvio della funzione di Azure) e non nella destinazione.

Se nella destinazione mancano alcune modifiche, ciò potrebbe indicare che si è verificato un errore durante l'esecuzione della funzione di Azure dopo la ricezione delle modifiche.

In questo scenario, il modo migliore consiste nell'aggiungere `try/catch blocks` il codice e all'interno dei cicli che potrebbero elaborare le modifiche, per rilevare qualsiasi errore per un particolare subset di elementi e gestirli di conseguenza (inviarli a un'altra risorsa di archiviazione per ulteriori analisi o ripetizione dei tentativi). 

> [!NOTE]
> Il trigger Azure Cosmos DB, per impostazione predefinita, non tenterà di ritentare un batch di modifiche se si è verificata un'eccezione non gestita durante l'esecuzione del codice. Ciò significa che il motivo per cui le modifiche non arrivano alla destinazione è dovuto al fatto che l'elaborazione non è riuscita.

Se si rileva che alcune modifiche non sono state ricevute dal trigger, lo scenario più comune è che è **in esecuzione un'altra funzione di Azure**. Potrebbe trattarsi di un'altra funzione di Azure distribuita in Azure o di una funzione di Azure in esecuzione in locale nel computer di uno sviluppatore che ha **esattamente la stessa configurazione** (gli stessi contenitori monitorati e di lease) e che questa funzione di Azure sta rubando un subset delle modifiche si aspetterebbe che la funzione di Azure venga elaborata.

Inoltre, è possibile convalidare lo scenario, se si conosce il numero di istanze di app per le funzioni di Azure in esecuzione. Se si esamina il contenitore dei lease e si conta il numero di elementi di lease in, i valori distinti `Owner` della proprietà in essi contenuti devono essere uguali al numero di istanze del app per le funzioni. Se sono presenti più proprietari delle istanze note di Azure app per le funzioni, significa che questi proprietari aggiuntivi sono quelli che "rubano" le modifiche.

Un modo semplice per aggirare questa situazione consiste nell'applicare un `LeaseCollectionPrefix/leaseCollectionPrefix` alla funzione con un valore nuovo o diverso oppure, in alternativa, eseguire il test con un nuovo contenitore lease.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>L'associazione può essere eseguita solo con\<il documento IReadOnlyList > o JArray

Questo errore si verifica se il progetto di funzioni di Azure o qualsiasi progetto a cui si fa riferimento contiene un riferimento a NuGet manuale a Azure Cosmos DB SDK con una versione diversa da quella fornita dall' [estensione Cosmos DB di funzioni di Azure](./troubleshoot-changefeed-functions.md#dependencies).

Per aggirare questa situazione, rimuovere il riferimento NuGet manuale che è stato aggiunto e consentire al Azure Cosmos DB riferimento SDK di risolversi tramite il pacchetto di estensioni Cosmos DB di funzioni di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Abilitare il monitoraggio per le funzioni di Azure](../azure-functions/functions-monitoring.md)
* [Risoluzione dei problemi di Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)

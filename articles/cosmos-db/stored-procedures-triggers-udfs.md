---
title: Utilizzo delle stored procedure, dei trigger e delle funzioni definite dall'utente in Azure Cosmos DB
description: Questo articolo illustra concetti quali stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 700cd6c0c75b25d56e812a394d6bdd193e4fb57c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614065"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Stored procedure, trigger e funzioni definite dall'utente

Azure Cosmos DB offre l'esecuzione transazionale di JavaScript integrata nel linguaggio. Quando si usa l'API SQL in Azure Cosmos DB, è possibile scrivere **stored procedure**, **trigger** e **funzioni definite dall'utente (UDF)** nel linguaggio JavaScript. È possibile scrivere la logica nel linguaggio JavaScript eseguito all'interno del motore di database. È possibile creare ed eseguire trigger, stored procedure e UDF usando il [portale di Azure](https://portal.azure.com/), l'[API di query integrata nel linguaggio JavaScript in Azure Cosmos DB](javascript-query-api.md) o gli [SDK client dell'API SQL di Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Vantaggi dell'uso della programmazione lato server

La scrittura di stored procedure, trigger e funzioni definite dall'utente (UDF) in JavaScript consente di creare applicazioni avanzate con i vantaggi seguenti:

* **Logica procedurale:** come linguaggio di programmazione di alto livello, JavaScript offre un'interfaccia completa e familiare per esprimere la logica di business. È possibile eseguire una sequenza di operazioni complesse sui dati.

* **Transazioni atomiche:** Azure Cosmos DB garantisce che le operazioni di database all'interno di una singola stored procedure o un trigger siano atomiche. Questa funzionalità atomica consente a un'applicazione di combinare le operazioni correlate in un unico batch, in modo che o nessuna o tutte abbiano esito positivo.

* **Prestazioni:** i dati JSON sono intrinsecamente mappati al sistema di tipi di linguaggio JavaScript. Questo mapping consente un numero di ottimizzazioni, ad esempio la materializzazione differita dei documenti JSON nel pool di buffer e la relativa disponibilità su richiesta per il codice di esecuzione. Vi sono altri vantaggi relativi alle prestazioni associati all'integrazione della logica di business nel database, tra cui:

   * *Invio in batch:* è possibile raggruppare operazioni come gli inserimenti e inviarle in blocco. Ciò comporta una drastica riduzione dei costi legati alla latenza del traffico di rete e dei costi generali di archiviazione per la creazione di transazioni separate.

   * *Precompilazione:* le stored procedure, i trigger e le UDF vengono precompilati implicitamente nel formato di codice byte per evitare costi di compilazione a ogni chiamata dello script. La precompilazione garantisce velocità elevata e footprint ridotto delle chiamate delle stored procedure.

   * *Sequenziazione:* a volte le operazioni necessitano di un meccanismo di attivazione che possa eseguire uno o più aggiornamenti per i dati. Oltre all'atomicità, esistono anche vantaggi per le prestazioni durante l'esecuzione sul lato server.

* **Incapsulamento:** è possibile usare le stored procedure per raggruppare la logica in un solo posto. L'incapsulamento aggiunge un livello di astrazione al di sopra dei dati, consentendo l'evoluzione delle applicazioni indipendentemente dai dati. Questo livello di astrazione è utile quando i dati sono senza schema e non è necessario gestire l'aggiunta di altra logica direttamente nell'applicazione. Questa astrazione consente di proteggere i dati semplificando l'accesso dagli script.

> [!TIP]
> Le stored procedure sono ideali per operazioni di scrittura e richiedono una transazione in un valore di chiave di partizione. Quando si decide se utilizzare le stored procedure, è possibile ottimizzare l'incapsulamento della quantità massima di Scritture. In generale, le stored procedure non rappresentano il modo più efficiente per eseguire un numero elevato di operazioni di lettura o di query, pertanto l'utilizzo di stored procedure per eseguire il batch di un numero elevato di letture per tornare al client non produrrà il vantaggio desiderato. Per ottenere prestazioni ottimali, è consigliabile eseguire le operazioni di lettura sul lato client, usando Cosmos SDK. 

## <a name="transactions"></a>Transazioni

Una transazione in un tipico database può essere definita come una sequenza di operazioni eseguite come singola unità di lavoro logica. Ogni transazione offre **garanzie di proprietà ACID**. ACID è un acronimo noto di: **A**tomicity, **C**onsistency, **I**solation e **D**urability (atomicità, coerenza, isolamento e durabilità). 

* L'atomicità garantisce che tutte le operazioni eseguite nell'ambito di una transazione siano trattate come unità singola e che venga eseguito il commit di tutte le operazioni o di nessuna di esse. 

* La coerenza garantisce che i dati siano sempre in uno stato valido in tutte le transazioni. 

* L'isolamento garantisce che non vi siano transazioni conflittuali. La maggior parte dei sistemi commerciali offre più livelli di isolamento utilizzabili in base alle esigenze dell'applicazione. 

* La durabilità assicura che qualsiasi modifica di cui sia stato eseguito il commit in un database sia sempre presente.

In Azure Cosmos DB il runtime di JavaScript è ospitato all'interno del motore di database. Di conseguenza, le richieste effettuate nelle stored procedure e nei trigger vengono eseguite nello stesso ambito di una sessione di database. Questa funzionalità consente ad Azure Cosmos DB di garantire proprietà ACID per tutte le operazioni che fanno parte di una stored procedure o di un singolo trigger. Per gli esempi, vedere l'articolo relativo a [come implementare le transazioni](how-to-write-stored-procedures-triggers-udfs.md#transactions).

### <a name="scope-of-a-transaction"></a>Ambito di una transazione

Se è associata a un contenitore Azure Cosmos, la stored procedure viene eseguita nell'ambito della transazione di una chiave di partizione logica. Ogni esecuzione di stored procedure deve includere un valore di chiave di partizione logica corrispondente all'ambito della transazione. Per altre informazioni, vedere l'articolo [Partizionamento di Azure Cosmos DB](partition-data.md).

### <a name="commit-and-rollback"></a>Commit e rollback

Le transazioni sono integrate in modo nativo nel modello di programmazione JavaScript di Azure Cosmos DB. All'interno di una funzione JavaScript, viene eseguito il wrapping di tutte le operazioni in un'unica transazione. Se la logica JavaScript in una stored procedure viene completata senza eccezioni, nel database viene eseguito il commit di tutte le operazioni all'interno della transazione. Istruzioni come `BEGIN TRANSACTION` e `COMMIT TRANSACTION` (familiari nei database relazionali) sono implicite in Azure Cosmos DB. Se si verifica un'eccezione dallo script, il runtime JavaScript di Cosmos DB esegue il rollback dell'intera transazione. Di per sé, generare un'eccezione equivale effettivamente a una `ROLLBACK TRANSACTION` in Azure Cosmos DB.

### <a name="data-consistency"></a>Coerenza dei dati

Le stored procedure e i trigger vengono sempre eseguiti nella replica primaria del contenitore di Azure Cosmos DB. Questa funzionalità garantisce la [coerenza assoluta](consistency-levels-tradeoffs.md) delle letture dalle stored procedure. Le query che usano funzioni definite dall'utente possono essere eseguite nella replica primaria o in qualsiasi replica secondaria. Le stored procedure e i trigger sono progettati per supportare scritture transazionali, mentre la logica di sola lettura viene implementata meglio come logica sul lato applicazione e le query che usano gli [SDK delle API SQL di Azure Cosmos DB](sql-api-dotnet-samples.md) consentiranno di saturare la velocità effettiva del database. 

## <a name="bounded-execution"></a>Esecuzione vincolata

Tutte le operazioni di Azure Cosmos DB devono essere completate entro la scadenza specificata. Questo vincolo si applica anche alle funzioni JavaScript (stored procedure, trigger e funzioni definite dall'utente). Se un'operazione non viene completata entro questo limite di tempo, viene eseguito il rollback della transazione.

È possibile verificare che le funzioni JavaScript terminino entro il tempo limite oppure implementare un modello basato sulla continuazione in modo da riprendere l'esecuzione o eseguirla in batch. Per semplificare lo sviluppo delle stored procedure e dei trigger per la gestione dei limiti di tempo, tutte le funzioni nel contenitore di Azure Cosmos (ad esempio per creare, leggere, aggiornare ed eliminare gli elementi) restituiscono un valore booleano che indica se l'operazione verrà completata. Se questo valore è false, è un'indicazione che la procedura deve eseguire il wrapping dell'esecuzione perché lo script usa più tempo o velocità effettiva con provisioning rispetto al valore configurato. Il completamento delle operazioni inserite in coda precedentemente alla prima operazione di archiviazione non accettata è garantito se la stored procedure viene completata in tempo e non vengono inserite in coda altre richieste. Di conseguenza, le operazioni devono essere accodate una alla volta usando convenzioni di callback di JavaScript per gestire il flusso di controllo dello script. Poiché gli script vengono eseguiti in un ambiente lato server, sono strettamente regolati. Gli script che violano ripetutamente i limiti di esecuzione possono essere contrassegnati come inattivi e non possono essere eseguiti. Devono essere ricreati per rispettare i limiti di esecuzione.

Le funzioni JavaScript sono anche soggette alla [capacità di velocità effettiva con provisioning](request-units.md). È possibile che le funzioni JavaScript finiscano per usare un numero elevato di unità richiesta entro un breve periodo di tempo e siano soggette a velocità limitata se viene raggiunto il limite di capacità di velocità effettiva con provisioning. È importante notare che gli script usano velocità effettiva aggiuntiva oltre a quella usata per l'esecuzione delle operazioni di database, anche se queste operazioni di database sono leggermente meno costose rispetto all'esecuzione delle stesse operazioni a partire dal client.

## <a name="triggers"></a>Trigger

Azure Cosmos DB supporta due tipi di trigger:

### <a name="pre-triggers"></a>Pre-trigger

Azure Cosmos DB fornisce trigger che possono essere richiamati eseguendo un'operazione su un elemento di Azure Cosmos. È ad esempio possibile specificare un pre-trigger durante la creazione di un elemento. In questo caso, il pre-trigger verrà eseguito prima della creazione dell'elemento. I pre-trigger non possono avere parametri di input. Se necessario, l'oggetto richiesta è utilizzabile per aggiornare il corpo del documento della richiesta originale. Quando i trigger vengono registrati, gli utenti possono specificare le operazioni con le quali è possibile eseguirli. Se un trigger è stato creato con `TriggerOperation.Create`, non sarà consentito usarlo in un'operazione di sostituzione. Per gli esempi, vedere [Come scrivere i trigger](how-to-write-stored-procedures-triggers-udfs.md#triggers).

### <a name="post-triggers"></a>Post-trigger

Analogamente ai pre-trigger, i post-trigger sono associati anche a un'operazione su un elemento di Azure Cosmos e non richiedono alcun parametro di input. Vengono eseguiti *dopo* il completamento dell'operazione e hanno accesso al messaggio di risposta inviato al client. Per gli esempi, vedere [Come scrivere i trigger](how-to-write-stored-procedures-triggers-udfs.md#triggers).

> [!NOTE]
> I trigger registrati non vengono eseguiti automaticamente quando si verificano le operazioni corrispondenti (creazione/eliminazione/sostituzione/aggiornamento). Devono essere chiamati in modo esplicito durante l'esecuzione di queste operazioni. Per altre informazioni, vedere [How to Run Triggers](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) article.

## <a id="udfs"></a>Funzioni definite dall'utente

Le funzioni definite dall'utente, o UDF, consentono di estendere la sintassi del linguaggio di query API SQL e implementare facilmente la logica di business. Possono essere chiamate solo all'interno di query. Non hanno accesso all'oggetto contesto e vanno usate come JavaScript di solo calcolo. È quindi possibile eseguire le funzioni definite dall'utente su repliche secondarie. Per gli esempi, vedere [Come scrivere funzioni definite dall'utente](how-to-write-stored-procedures-triggers-udfs.md#udfs).

## <a id="jsqueryapi"></a>API di query integrate nel linguaggio JavaScript

Oltre a eseguire una query utilizzando la sintassi delle query API SQL, l'[SDK sul lato server](https://azure.github.io/azure-cosmosdb-js-server) consente di eseguire query tramite un'interfaccia JavaScript senza alcuna conoscenza di SQL. L'API di query JavaScript consente di creare query a livello di codice passando funzioni predicato nella sequenza delle chiamate di funzione. Le query vengono analizzate dal runtime JavaScript ed eseguite in modo efficiente usando Azure Cosmos DB. Per altre informazioni sul supporto delle API di query JavaScript, vedere l'articolo [Utilizzo dell'API di query integrata nel linguaggio JavaScript](javascript-query-api.md). Per gli esempi, vedere l'articolo [Come scrivere stored procedure e trigger in Azure Cosmos DB usando l'API di query di JavaScript](how-to-write-javascript-query-api.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere informazioni su come usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB facendo riferimento ai seguenti articoli:

* [Come scrivere stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)

* [Come usare stored procedure, trigger e funzioni definite dall'utente in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Utilizzo dell'API di query integrata nel linguaggio JavaScript](javascript-query-api.md)

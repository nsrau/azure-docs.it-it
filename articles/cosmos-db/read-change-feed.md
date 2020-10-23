---
title: Lettura del feed di modifiche in Azure Cosmos DB
description: Questo articolo descrive le diverse opzioni disponibili per eseguire operazioni di lettura e accesso sul feed di modifiche in Azure Cosmos DB.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/09/2020
ms.reviewer: sngun
ms.openlocfilehash: 399f81a5246633912d1e17a13492e404119e362f
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282059"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Lettura del feed di modifiche in Azure Cosmos DB

È possibile usare il feed di modifiche di Azure Cosmos DB con un modello push o un modello pull. Con un modello push, un server (il processore del feed delle modifiche) effettua il push del lavoro a un client con la logica di business per l'elaborazione di questa operazione. Tuttavia, la complessità del controllo del lavoro e dell'archiviazione dello stato per l'ultimo lavoro elaborato viene gestita nel server.

Con un modello pull, il client deve eseguire il pull del lavoro dal server. Il client, in questo caso, non ha solo la logica di business per l'elaborazione del lavoro, ma anche l'archiviazione dello stato per l'ultima operazione elaborata, la gestione del bilanciamento del carico tra più client che elaborano il lavoro in parallelo e la gestione degli errori.

Quando si legge il feed di modifiche di Azure Cosmos DB, è consigliabile in genere usare un modello push perché non è necessario preoccuparsi degli aspetti seguenti:

- Polling del feed di modifiche per modifiche future.
- Archiviazione dello stato dell'ultima modifica elaborata. Quando si legge il feed di modifiche, questo viene automaticamente archiviato in un [contenitore del lease](change-feed-processor.md#components-of-the-change-feed-processor).
- Bilanciamento del carico tra più client che archiviano le modifiche, come nel caso in cui, ad esempio, un client non sia più in grado di gestire alcuna modifica mentre un altro abbia ancora capacità disponibile.
- [Gestione degli errori](change-feed-processor.md#error-handling), come quando, ad esempio, si ritentano automaticamente modifiche non riuscite che non sono state elaborate correttamente dopo un'eccezione non gestita nel codice o in un problema di rete temporaneo.

Nella maggior parte degli scenari in cui si usa il feed di modifiche di Azure Cosmos DB verrà adottata una delle opzioni del modello push. Esistono tuttavia alcune situazioni in cui potrebbe essere utile il controllo aggiuntivo di basso livello del modello pull, incluse le seguenti:

- Lettura delle modifiche di una chiave di partizione specifica
- Controllo della velocità di ricezione delle modifiche per l'elaborazione da parte del client
- Esecuzione di una sola lettura dei dati esistenti nel feed di modifiche (ad esempio per eseguire una migrazione dei dati)

## <a name="reading-change-feed-with-a-push-model"></a>Lettura del feed di modifiche con un modello push

L'adozione di un modello push costituisce il modo più semplice per leggere dal feed di modifiche. Esistono due modi per leggere dal feed di modifiche con un modello push: [Trigger di Azure Cosmos DB in Funzioni di Azure](change-feed-functions.md) e la [libreria del processore dei feed delle modifiche](change-feed-processor.md). Funzioni di Azure usa il processore dei feed di modifiche in backgroud: costituiscono quindi due modi molto simili di leggere il feed di modifiche. È possibile considerare Funzioni di Azure come una semplice piattaforma di hosting per il processore dei feed di modifiche, non un modo completamente diverso di leggere il feed di modifiche.

### <a name="azure-functions"></a>Funzioni di Azure

Funzioni di Azure è l'opzione più semplice se si sta iniziando a usare il feed di modifiche. Grazie alla sua semplicità d'uso, è comunque l'opzione consigliata nella maggior parte dei casi in cui è necessario elaborare il feed di modifiche. Quando si crea un trigger di Funzioni di Azure per Azure Cosmos DB, si seleziona il contenitore da connettere per attivare Funzioni di Azure quando viene apportata una modifica al contenitore. Poiché Funzioni di Azure usa il processore dei feed di modifiche in background, viene automaticamente parallelizzata l'elaborazione delle modifiche tra le [partizioni](partitioning-overview.md) del contenitore.

Sviluppare un'applicazione con Funzioni di Azure è più semplice e talvolta più veloce della distribuzione in autonomia di un processore di feed di modifiche. I trigger possono essere creati usando il portale di Funzioni di Azure o a livello di codice usando gli SDK. Visual Studio e VS Code forniscono il supporto per scrivere le funzioni di Azure ed è anche possibile usare l'interfaccia della riga di comando di Funzioni di Azure per lo sviluppo multipiattaforma. È possibile scrivere il codice ed eseguire il debug dal desktop e quindi distribuire la funzione con un solo clic. Per altre informazioni, vedere gli articoli [Elaborazione di database serverless con Funzioni di Azure](serverless-computing-database.md) e [Feed di modifiche con Funzioni di Azure](change-feed-functions.md).

### <a name="change-feed-processor-library"></a>Libreria del processore dei feed di modifiche

Il processore dei feed di modifiche offre un controllo maggiore sul feed di modifiche e nasconde la maggior parte delle complessità. La libreria del processore dei feed di modifiche segue il criterio osservatore in cui la funzione di elaborazione viene chiamata dalla libreria. Inoltre, verifica automaticamente la presenza di modifiche e, se viene rilevata una modifica, ne "effettua il push" al client. Se il feed di modifiche è caratterizzato da una velocità effettiva elevata, è possibile creare più istanze client per leggere il feed di modifiche. La libreria del processore dei feed di modifiche divide automaticamente il carico tra client diversi. Non sarà quindi necessario implementare alcuna logica per bilanciare il carico tra più client o per mantenere lo stato del lease.

La libreria del processore dei feed delle modifiche garantisce che tutte le modifiche venga distribuite in modo "at-least-once". In altre parole, se si usa la libreria del processore dei feed di modifiche, la funzione di elaborazione verrà chiamata correttamente per ogni elemento contenuto nel feed di modifiche. Se si verifica un'eccezione non gestita nella logica di business della funzione di elaborazione, le modifiche non riuscite verranno ritentate fino a quando non verranno elaborate correttamente. Per evitare che il processore dei feed di modifiche venga bloccato continuamente durante i nuovi tentativi di apportare le modifiche, è necessario aggiungere nella funzione di elaborazione la logica necessaria per scrivere i documenti, in caso di eccezione, in una coda di messaggi non recapitabili. Altre informazioni sulla [gestione degli errori](change-feed-processor.md#error-handling).

In Funzioni di Azure, si consiglia di adottare lo stesso approccio per la gestione degli errori. È comunque necessario aggiungere nel codice del delegato la logica necessaria per scrivere i documenti, in caso di eccezione, in una coda di messaggi non recapitabili. Se, tuttavia, si verifica un'eccezione che non può essere gestita in Funzione di Azure, la modifica che ha generato l'eccezione non verrà ritentata automaticamente. Se invece si verifica un'eccezione che non viene gestita nella logica di business, Funzione di Azure passerà all'elaborazione della modifica successiva e non ritenterà la stessa modifica.

Analogamente a Funzioni di Azure, anche con la libreria del processore dei feed delle modifiche è facile eseguire attività di sviluppo. In questo caso, tuttavia, l'utente ha il compito di distribuire uno o più host per il processore dei feed di modifiche. Un host è un'istanza dell'applicazione che usa il processore del feed di modifiche per restare in ascolto delle modifiche. Mentre Funzioni di Azure offre funzionalità di scalabilità automatica, la scalabilità degli host è di responsabilità dell'utente. Per altre informazioni, vedere [Uso del processore dei feed di modifiche](change-feed-processor.md#dynamic-scaling). La libreria del processore dei feed di modifiche è inclusa in [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3).

## <a name="reading-change-feed-with-a-pull-model"></a>Lettura del feed di modifiche con un modello pull

Il [modello di pull dei feed di modifiche](change-feed-pull-model.md) consente di usare il feed di modifiche in modo personalizzato. Le modifiche devono essere richieste dal client e non viene eseguito il polling automatico delle modifiche. Se si vuole che un "segnalibro" indichi in modo permanente l'ultima modifica elaborata (simile alla funzione del contenitore del lease nel modello push), è necessario [salvare un token di continuazione](change-feed-pull-model.md#saving-continuation-tokens).

Con il modello pull dei feed di modifiche, si ottiene quindi un maggiore controllo di basso livello del feed di modifiche. Quando si legge il feed di modifiche con il modello pull, sono disponibili tre opzioni:

- Leggere le modifiche relative a un intero contenitore
- Leggere le modifiche relative a uno specifico [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)
- Leggere le modifiche per un determinato valore della chiave di partizione

È possibile parallelizzare l'elaborazione delle modifiche tra più client, esattamente come con il processore dei feed di modifiche. Il modello pull, tuttavia, non gestisce automaticamente il bilanciamento del carico tra i client. Se si usa il modello pull per parallelizzare l'elaborazione del feed di modifiche, si otterrà per primo un elenco di FeedRange. Un elemento FeedRange è costituito da un intervallo di valori di chiave di partizione. Dopo che un processo dell'agente di orchestrazione avrà ottenuto i FeedRange e li avrà distribuiti tra i computer, sarà possibile usare i FeedRange per configurare i computer in modo che leggano il feed di modifiche in parallelo.

Con il modello pull non viene garantita la distribuzione "at-least-once". Il modello pull offre tuttavia un controllo di basso livello per decidere più consapevolmente come si vogliono gestire gli errori.

> [!NOTE]
> Il modello di pull del feed di modifiche è attualmente disponibile solo in [anteprima in Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.13.0-preview). L'anteprima non è ancora disponibile per altre versioni dell'SDK.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed di modifiche nelle API per Cassandra e MongoDB

La funzionalità del feed di modifiche è disponibile come flusso di modifiche nell'API MongoDB e come query con predicato nell'API Cassandra. Per altre informazioni sui dettagli di implementazione per l'API MongoDB, vedere [Flussi di modifiche nell'API Azure Cosmos DB per MongoDB](mongodb-change-streams.md).

La versione nativa di Apache Cassandra include Change Data Capture (CDC), un meccanismo per contrassegnare tabelle specifiche per l'archiviazione e rifiutare operazioni di scrittura in tali tabelle quando viene raggiunta una dimensione su disco configurabile per il log CDC. La funzionalità del feed di modifiche nell'API Azure Cosmos DB per Cassandra consente di eseguire query relative alle modifiche con predicato tramite CQL. Per altre informazioni sui dettagli di implementazione, vedere [Feed di modifiche nell'API Azure Cosmos DB per Cassandra](cassandra-change-feed.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere altre informazioni sul feed di modifiche negli articoli seguenti:

* [Panoramica del feed di modifiche](change-feed.md)
* [Feed di modifiche con Funzioni di Azure](change-feed-functions.md)
* [Uso della libreria del processore dei feed di modifiche](change-feed-processor.md)

---
title: Risolvere i problemi di Azure Cosmos DB HTTP 408 o di timeout della richiesta con .NET SDK
description: Informazioni su come diagnosticare e correggere le eccezioni di timeout delle richieste .NET SDK.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 211121e21502e9cd4929169053a8ad58a9d7b21b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476927"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout-exceptions"></a>Diagnosticare e risolvere i problemi relativi alle eccezioni di timeout della richiesta Azure Cosmos DB .NET SDK
Si verifica l'errore HTTP 408 se l'SDK non è riuscito a completare la richiesta prima che si verifichi il limite di timeout.

## <a name="customize-the-timeout-on-the-azure-cosmos-db-net-sdk"></a>Personalizzare il timeout in Azure Cosmos DB .NET SDK

L'SDK offre due alternative distinte per il controllo dei timeout, ognuno con un ambito diverso.

### <a name="requesttimeout"></a>RequestTimeout

La `CosmosClientOptions.RequestTimeout` configurazione (o `ConnectionPolicy.RequestTimeout` per SDK v2) consente di impostare un timeout che influisca su ogni singola richiesta di rete. Un'operazione avviata da un utente può estendersi a più richieste di rete, ad esempio potrebbe essere presente una limitazione. Questa configurazione si applica per ogni richiesta di rete al nuovo tentativo. Questo timeout non è un timeout della richiesta di operazione end-to-end.

### <a name="cancellationtoken"></a>CancellationToken

Tutte le operazioni asincrone nell'SDK hanno un parametro CancellationToken facoltativo. Questo parametro [CancellationToken](/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) viene usato durante l'intera operazione, in tutte le richieste di rete. Tra le richieste di rete, il token di annullamento può essere controllato e un'operazione annullata se il token correlato è scaduto. Il token di annullamento deve essere usato per definire un timeout previsto approssimativo nell'ambito dell'operazione.

> [!NOTE]
> Il `CancellationToken` parametro è un meccanismo in cui la libreria verificherà l'annullamento quando non [provocherà uno stato non valido](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). È possibile che l'operazione non venga annullata esattamente quando l'ora definita nell'annullamento è attiva. Al contrario, quando il tempo è attivo, viene annullato quando è sicuro.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per le eccezioni di timeout della richiesta.

### <a name="high-cpu-utilization"></a>Utilizzo CPU elevato
Un utilizzo elevato della CPU è il caso più comune. Per una latenza ottimale, l'utilizzo della CPU deve essere approssimativamente del 40%. Usare 10 secondi come intervallo per monitorare l'utilizzo della CPU massimo (non medio). I picchi di CPU sono più comuni con le query tra partizioni in cui è possibile eseguire più connessioni per una singola query.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata o orizzontale.

### <a name="socket-or-port-availability-might-be-low"></a>La disponibilità del socket o della porta potrebbe essere bassa
Quando è in esecuzione in Azure, i client che usano .NET SDK possono raggiungere l'esaurimento delle porte di Azure SNAT (PAT).

#### <a name="solution-1"></a>Soluzione n. 1:
Se si eseguono macchine virtuali di Azure, seguire la [Guida all'esaurimento delle porte di SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Soluzione n. 2:
Se si sta eseguendo il servizio app Azure, seguire la [Guida alla risoluzione dei problemi di connessione](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e [usare la diagnostica del servizio app](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Soluzione 3:
Se si esegue funzioni di Azure, verificare di seguire le indicazioni di [funzioni di Azure](../azure-functions/manage-connections.md#static-clients) per la gestione dei client Singleton o statici per tutti i servizi interessati (incluso Azure Cosmos DB). Controllare i [limiti del servizio](../azure-functions/functions-scale.md#service-limits) in base al tipo e alle dimensioni del app per le funzioni host.

#### <a name="solution-4"></a>Soluzione 4:
Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `ConnectionPolicy` dell'SDK. In caso contrario, verranno affrontati i problemi di connessione.

### <a name="create-multiple-client-instances"></a>Creare più istanze client
La creazione di più istanze client può causare problemi di connessione e timeout.

#### <a name="solution"></a>Soluzione:
Seguire i [suggerimenti](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)per le prestazioni e usare una singola istanza di CosmosClient in un intero processo.

### <a name="hot-partition-key"></a>Chiave di partizione a caldo
Azure Cosmos DB distribuisce in modo uniforme la velocità effettiva di provisioning complessiva tra le partizioni fisiche. Quando è presente una partizione a caldo, una o più chiavi di partizione logiche in una partizione fisica utilizzano tutte le unità richiesta della partizione fisica al secondo (UR/sec). Allo stesso tempo, le UR/sec in altre partizioni fisiche sono inutilizzate. Come sintomo, il numero totale di Ur/s utilizzato sarà inferiore alle UR/sec di cui è stato effettuato il provisioning nel database o nel contenitore, ma verrà comunque visualizzata la limitazione (429s) sulle richieste rispetto alla chiave di partizione logica attiva. Usare la [metrica consumo unità richiesta normalizzata](monitor-normalized-request-units.md) per verificare se il carico di lavoro sta riscontrando una partizione a caldo. 

#### <a name="solution"></a>Soluzione:
Scegliere una chiave di partizione appropriata che distribuisca equamente il volume di richiesta e l'archiviazione. Informazioni su come [modificare la chiave di partizione](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="high-degree-of-concurrency"></a>Livello elevato di concorrenza
L'applicazione sta eseguendo un elevato livello di concorrenza, che può causare conflitti sul canale.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata o orizzontale.

### <a name="large-requests-or-responses"></a>Richieste o risposte di grandi dimensioni
Le richieste o le risposte di grandi dimensioni possono compromettere il blocco del canale ed esacerbare i conflitti, anche con un livello di concorrenza relativamente basso.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata o orizzontale.

### <a name="failure-rate-is-within-the-azure-cosmos-db-sla"></a>La percentuale di errori rientra nell'ambito del contratto di Azure Cosmos DB
Quando necessario, l'applicazione deve essere in grado di gestire gli errori temporanei e di riprovare. Eventuali eccezioni 408 non vengono ritentate perché nei percorsi di creazione non è possibile stabilire se il servizio ha creato o meno l'elemento. Se si invia di nuovo lo stesso elemento per create, verrà generata un'eccezione di conflitto. La logica di business delle applicazioni utente potrebbe avere logica personalizzata per gestire i conflitti, che potrebbero interrompersi dall'ambiguità di un elemento esistente rispetto al conflitto da un tentativo di creazione.

### <a name="failure-rate-violates-the-azure-cosmos-db-sla"></a>La percentuale di errori viola il contratto di Azure Cosmos DB
Contattare il [supporto tecnico di Azure](https://aka.ms/azure-support).

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
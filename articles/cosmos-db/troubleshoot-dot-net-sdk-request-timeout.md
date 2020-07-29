---
title: Risolvere i problemi di Azure Cosmos DB HTTP 408 o di timeout della richiesta con .NET SDK
description: Come diagnosticare e correggere l'eccezione di timeout della richiesta .NET SDK
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 29b0c6237ae04ea5da9ec496498fc7c20890b173
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294397"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB timeout della richiesta .NET SDK
L'errore HTTP 408 si verifica se l'SDK non è stato in grado di completare la richiesta prima che venga generato il limite di timeout.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per le eccezioni di timeout della richiesta.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. utilizzo elevato della CPU (caso più comune)
Per una latenza ottimale, è consigliabile che l'utilizzo della CPU sia approssimativamente del 40%. È consigliabile usare 10 secondi come intervallo per il monitoraggio dell'utilizzo della CPU max (non medio). I picchi di CPU sono più comuni con le query tra partizioni in cui è possibile eseguire più connessioni per una singola query.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata/orizzontale.

### <a name="2-socket--port-availability-might-be-low"></a>2. la disponibilità del socket/porta potrebbe essere bassa
Quando è in esecuzione in Azure, i client che usano .NET SDK possono raggiungere l'esaurimento delle porte di Azure SNAT (PAT).

#### <a name="solution-1"></a>Soluzione n. 1:
Seguire la [Guida all'esaurimento delle porte di SNAT](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Soluzione n. 2:
Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `ConnectionPolicy` dell'SDK.
In caso contrario, verranno riscontrati problemi di connessione.

### <a name="3-creating-multiple-client-instances"></a>3. creazione di più istanze client
La creazione di più istanze client può causare problemi di connessione e timeout.

#### <a name="solution"></a>Soluzione:
Seguire i [suggerimenti](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)per le prestazioni e usare una singola istanza di CosmosClient in un intero processo.

### <a name="4-hot-partition-key"></a>4. chiave di partizione a caldo
Azure Cosmos DB distribuisce in modo uniforme la velocità effettiva di provisioning complessiva tra le partizioni fisiche. Quando è presente una partizione a caldo, una o più chiavi di partizione logiche in una partizione fisica utilizzano tutte le UR/s della partizione fisica, mentre le UR/sec in altre partizioni fisiche non vengono utilizzate. Come sintomo, il numero totale di Ur/s utilizzato sarà inferiore alle UR/sec di cui è stato effettuato il provisioning nel database o nel contenitore, ma verrà comunque visualizzata la limitazione (429s) sulle richieste rispetto alla chiave di partizione logica attiva. Usare la [metrica consumo unità richiesta normalizzata](monitor-normalized-request-units.md) per verificare se il carico di lavoro sta riscontrando una partizione a caldo. 

#### <a name="solution"></a>Soluzione:
Scegliere una chiave di partizione appropriata che distribuisca equamente il volume di richiesta e l'archiviazione. Informazioni su come [modificare la chiave di partizione](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. elevato livello di concorrenza
L'applicazione sta eseguendo un elevato livello di concorrenza, che può causare conflitti sul canale

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata/orizzontale.

### <a name="6-large-requests-andor-responses"></a>6. richieste e/o risposte di grandi dimensioni
Le richieste o le risposte di grandi dimensioni possono compromettere il blocco del canale ed esacerbare i conflitti, anche con un livello di concorrenza relativamente basso.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata/orizzontale.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. la percentuale di errori è entro il contratto di Cosmos DB
Quando necessario, l'applicazione deve essere in grado di gestire gli errori temporanei e di riprovare. 408 le eccezioni non vengono ritentate perché nei percorsi di creazione non è possibile stabilire se il servizio ha creato l'elemento o in caso contrario. Se si invia di nuovo lo stesso elemento per create, verrà generata un'eccezione di conflitto. La logica di business delle applicazioni utente potrebbe avere logica personalizzata per gestire i conflitti, che potrebbero interrompersi dall'ambiguità di un elemento esistente rispetto a un conflitto da un tentativo di creazione.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. la percentuale di errori sta violando il contratto di Cosmos DB
Contattare il supporto tecnico di Azure.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi relativi all'uso di Azure Cosmos DB .NET SDK
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md)
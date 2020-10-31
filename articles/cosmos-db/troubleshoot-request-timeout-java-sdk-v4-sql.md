---
title: Risolvere i problemi di Azure Cosmos DB HTTP 408 o di timeout della richiesta con Java v4 SDK
description: Informazioni su come diagnosticare e correggere le eccezioni di timeout delle richieste Java SDK con Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 45452109582be40f007ae57a00c2a151f216bdb8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103011"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-request-timeout-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB le eccezioni di timeout richieste Java v4 SDK
Si verifica l'errore HTTP 408 se l'SDK non è riuscito a completare la richiesta prima che si verifichi il limite di timeout.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause e le soluzioni note per le eccezioni di timeout della richiesta.

### <a name="high-cpu-utilization"></a>Utilizzo CPU elevato
Un utilizzo elevato della CPU è il caso più comune. Per una latenza ottimale, l'utilizzo della CPU deve essere approssimativamente del 40%. Usare 10 secondi come intervallo per monitorare l'utilizzo della CPU massimo (non medio). I picchi di CPU sono più comuni con le query tra partizioni in cui è possibile eseguire più connessioni per una singola query.

#### <a name="solution"></a>Soluzione:
L'applicazione client che usa l'SDK deve essere aumentata o orizzontale.

### <a name="connection-throttling"></a>Limitazione della connessione
La limitazione delle connessioni può verificarsi a causa di un limite di connessioni nel computer host o di un esaurimento delle porte SNAT (PAT) di Azure.

### <a name="connection-limit-on-a-host-machine"></a>Limite di connessioni nel computer host
Alcuni sistemi Linux, come Red Hat, prevedono un limite massimo per il numero totale di file aperti. I socket in Linux vengono implementati come file, per cui questo numero limita anche il numero totale di connessioni. Eseguire il comando seguente.

```bash
ulimit -a
```

#### <a name="solution"></a>Soluzione:
Il numero massimo di file aperti consentiti, identificati come "nofile", deve essere almeno 10.000. Per altre informazioni, vedere i [suggerimenti per le prestazioni](performance-tips-java-sdk-v4-sql.md) relativi ad Azure Cosmos DB Java SDK v4.

### <a name="socket-or-port-availability-might-be-low"></a>La disponibilità del socket o della porta potrebbe essere bassa
Quando è in esecuzione in Azure, i client che usano Java SDK possono raggiungere l'esaurimento delle porte di Azure SNAT (PAT).

#### <a name="solution-1"></a>Soluzione n. 1:
Se si eseguono macchine virtuali di Azure, seguire la [Guida all'esaurimento delle porte di SNAT](troubleshoot-java-sdk-v4-sql.md#snat).

#### <a name="solution-2"></a>Soluzione n. 2:
Se si sta eseguendo il servizio app Azure, seguire la [Guida alla risoluzione dei problemi di connessione](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) e [usare la diagnostica del servizio app](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Soluzione 3:
Se si esegue funzioni di Azure, verificare di seguire le indicazioni di [funzioni di Azure](../azure-functions/manage-connections.md#static-clients) per la gestione dei client Singleton o statici per tutti i servizi interessati (incluso Azure Cosmos DB). Controllare i [limiti del servizio](../azure-functions/functions-scale.md#service-limits) in base al tipo e alle dimensioni del app per le funzioni host.

#### <a name="solution-4"></a>Soluzione 4:
Se si usa un proxy HTTP, assicurarsi che possa supportare il numero di connessioni configurate in `GatewayConnectionConfig` dell'SDK. In caso contrario, verranno affrontati i problemi di connessione.

### <a name="create-multiple-client-instances"></a>Creare più istanze client
La creazione di più istanze client può causare problemi di connessione e timeout.

#### <a name="solution-1"></a>Soluzione n. 1:
Seguire i [suggerimenti](performance-tips-java-sdk-v4-sql.md#sdk-usage)per le prestazioni e usare una singola istanza di CosmosClient in un'intera applicazione.

#### <a name="solution-2"></a>Soluzione n. 2:
Se non è possibile usare singleton CosmosClient in un'applicazione, è consigliabile usare la condivisione della connessione tra più client Cosmos tramite questa API `connectionSharingAcrossClientsEnabled(true)` in CosmosClient. Quando si hanno più istanze di Cosmos client nella stessa JVM che interagisce con più account Cosmos, l'abilitazione consente la condivisione della connessione in modalità diretta, se possibile tra le istanze di Cosmos client. Si noti che quando si imposta questa opzione, la configurazione della connessione (ad esempio, la configurazione del timeout del socket, la configurazione del timeout di inattività) del primo client di cui è stata creata un'istanza verrà utilizzata per tutte le altre istanze client.

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
* [Diagnosticare e risolvere](troubleshoot-java-sdk-v4-sql.md) i problemi quando si usa il Azure Cosmos DB Java v4 SDK.
* Informazioni sulle linee guida sulle prestazioni per [Java V4](performance-tips-java-sdk-v4-sql.md).

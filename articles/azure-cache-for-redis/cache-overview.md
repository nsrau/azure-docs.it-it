---
title: Informazioni su Cache Redis di Azure
description: Informazioni su come usare Cache di Azure per Redis per abilitare le funzionalità di cache-aside, memorizzazione del contenuto nella cache, memorizzazione nella cache delle sessioni utente, accodamento di messaggi e processi e transazioni distribuite.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 8ec2a302226e3dc44701209a8cbb47b7814a5a2c
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874850"
---
# <a name="azure-cache-for-redis"></a>Cache Redis di Azure
Cache di Azure per Redis offre un archivio dati in memoria basato sul software open source [Redis](https://redis.io/). Redis migliora in modo significativo le prestazioni e la scalabilità di un'applicazione che usano gli archivi dati back-end. È in grado di elaborare volumi elevati di richieste delle applicazioni mantenendo i dati usati di frequente nella memoria del server che garantisce rapidità di lettura e scrittura. Redis offre una soluzione di archiviazione dati a bassa latenza e velocità effettiva elevata strategica per le applicazioni moderne.

Cache di Azure per Redis offre Redis come servizio gestito. Fornisce istanze del server Redis sicure e dedicate e compatibilità completa con l'API Redis. Il servizio è gestito da Microsoft, ospitato in Azure e accessibile a qualsiasi applicazione all'interno o all'esterno di Azure.

La cache di Azure per Redis può essere usata come cache del contenuto o dei dati distribuiti, archivio di sessioni, broker di messaggi e altro ancora. Può essere distribuita come in modalità autonoma o affiancata ad altri servizi di database di Azure, ad esempio SQL di Azure o Cosmos DB.

## <a name="key-scenarios"></a>Scenari principali
Cache di Azure per Redis migliora le prestazioni dell'applicazione grazie al supporto modelli di architettura delle applicazioni comuni. Alcuni dei più comuni sono:

| Modello      | Descrizione                                        |
| ------------ | -------------------------------------------------- |
| [Cache dei dati](cache-web-app-cache-aside-leaderboard.md) | Spesso i database sono troppo grandi per essere caricati direttamente in una cache. In genere si usa il modello [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) per caricare i dati nella cache solo in base alle esigenze. Quando il sistema apporta modifiche ai dati, può anche aggiornare la cache, che viene quindi distribuita ad altri client. Inoltre, il sistema può impostare una scadenza per i dati oppure usare criteri di rimozione per attivare gli aggiornamenti dei dati nella cache.|
| [Cache del contenuto](cache-aspnet-output-cache-provider.md) | Molte pagine Web vengono generate da modelli che usano contenuto statico, ad esempio intestazioni, piè di pagina e banner. Questi elementi statici non devono essere modificati di frequente. L'uso di una cache in memoria offre un accesso al contenuto statico più rapido rispetto agli archivi dati back-end. Questo modello riduce il tempo di elaborazione e il carico del server, consentendo ai server Web di essere più reattivi. Permette inoltre di ridurre il numero di server necessari per gestire i carichi. Cache di Azure per Redis fornisce il provider di cache di output per Redis per supportare questo modello con ASP.NET.|
| [Archivio di sessioni](cache-aspnet-session-state-provider.md) | Questo modello viene comunemente usato con i carrelli della spesa e altri dati della cronologia utente che un'applicazione Web potrebbe voler associare ai cookie dell'utente. L'archiviazione di troppi dati in un cookie può avere un impatto negativo sulle prestazioni poiché le dimensioni dei cookie aumentano, vengono superate e convalidate con ogni richiesta. Una soluzione tipica consiste nell'usare il cookie come chiave per eseguire query sui dati in un database. L'uso di una cache interna alla memoria, come Cache Redis di Azure, per associare informazioni a un utente risulta molto più rapido rispetto all'interazione con un database relazionale completo. |
| Accodamento di messaggi e processi | Spesso le applicazioni aggiungono le attività a una coda quando l'esecuzione delle operazioni associate alla richiesta richiede tempo. Le operazioni a esecuzione prolungata vengono accodate per essere elaborate in sequenza, spesso da un altro server.  Questo metodo di rinviare il lavoro viene detto accodamento delle attività. Cache di Azure per Redis fornisce una coda distribuita per abilitare questo modello nell'applicazione.|
| Transazioni distribuite | Talvolta le applicazioni richiedono una serie di comandi su un archivio dati back-end per l'esecuzione come singola operazione atomica. Tutti i comandi devono avere esito positivo oppure è necessario eseguire il rollback allo stato iniziale. Cache di Azure per Redis supporta l'esecuzione di un batch di comandi come singola [transazione](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Versioni di Redis

Cache di Azure per Redis supporta Redis versione 4.x e, come anteprima, 6.0. È stato deciso di ignorare Redis 5.0 per offrire l'ultima versione. In precedenza, cache di Azure per Redis manteneva solo una singola versione di Redis. In futuro fornirà un aggiornamento più recente della versione principale e almeno una versione stabile precedente. È possibile [scegliere quale versione](cache-how-to-version.md) è più adatta per l'applicazione.

> [!NOTE]
> Redis 6.0 è attualmente in fase di anteprima. [Contattare](mailto:azurecache@microsoft.com) Microsoft se si è interessati. Questa anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Livelli di servizio
Cache Redis di Azure è disponibile nei livelli seguenti:

| Livello | Descrizione |
|---|---|
| Basic | Una cache con un nodo singolo. Questo livello supporta più dimensioni di memoria (da 250 MB a 53 GB) ed è ideale per carichi di lavoro di sviluppo/test e non critici. Il livello Basic non dispone di alcun contratto di servizio. |
| Standard | Una cache replicata in una configurazione a due nodi, primario/replica, gestita da Azure, con un [contratto di servizio](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) che garantisce disponibilità elevata. |
| Premium | Premium è il piano di livello aziendale. Le cache di livello Premium supportano più funzionalità e hanno una velocità effettiva superiore con latenze inferiori. Le cache di livello Premium sono distribuite su un hardware più potente e che offre prestazioni migliori rispetto al livello Standard o Basic. Questo vantaggio significa che la velocità effettiva per una cache delle stesse dimensioni sarà superiore nel livello Premium rispetto al livello Standard. |

### <a name="feature-comparison"></a>Confronto tra le funzionalità
Nella pagina [Prezzi di Cache di Azure per Redis](https://azure.microsoft.com/pricing/details/cache/) è presente un confronto dettagliato dei livelli disponibili. La tabella seguente descrive alcune delle funzionalità supportate in base al livello:

| Descrizione della funzionalità | Premium | Standard | Basic |
| ------------------- | :-----: | :------: | :---: |
| [Contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistenza dei dati Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Sicurezza tramite le regole del firewall](cache-configure.md#firewall) |✔|✔|✔|
| Crittografia in transito |✔|✔|✔|
| [Isolamento e protezione avanzata con rete virtuale](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importazione/Esportazione](cache-how-to-import-export-data.md) |✔|-|-|
| [Aggiornamenti pianificati](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Replica geografica](cache-how-to-geo-replication.md) |✔|-|-|
| [Reboot](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>Scelta del livello appropriato
Per la scelta di un livello per la cache di Azure per Redis, tenere presente quanto segue.

* **Memoria**: i livelli Basic e Standard offrono 250 MB – 53 GB. Il livello Premium offre fino a 1,2 TB (in cluster) o 120 GB (non in cluster). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Prestazioni di rete**: in presenza di un carico di lavoro che richiede una velocità effettiva elevata, il livello Premium offre maggiore larghezza di banda rispetto a Standard o Basic. Anche all'interno di ogni livello, a dimensioni più grandi della cache corrisponde maggiore larghezza di banda, a causa della macchina virtuale sottostante che ospita la cache. Per altre informazioni, vedere [Prestazioni di Cache di Azure per Redis](cache-planning-faq.md#azure-cache-for-redis-performance).
* **Velocità effettiva**: il livello Premium offre la massima velocità effettiva disponibile. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si potrebbero ricevere timeout sul lato client. Per altre informazioni, vedere la tabella seguente.
* **Disponibilità elevata**: la cache di Azure per Redis offre più opzioni a [disponibilità elevata](cache-high-availability.md). Garantisce la disponibilità di una cache di livello Standard o Premium in base al [contratto di servizio](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Il contratto di servizio copre solo la connettività agli endpoint cache. Il contratto di servizio non offre copertura per la protezione dalla perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati.
* **Persistenza dei dati Redis**: Il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. I problemi dell'infrastruttura sottostante possono comportare una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache di Azure per Redis offre le opzioni RDB e AOF (anteprima) per la persistenza di Redis. Per altre informazioni, vedere [Come configurare la persistenza dei dati per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).
* **Cluster Redis**: per creare cache di dimensioni superiori a 120 GB o partizionare i dati tra più nodi Redis, è possibile usare le funzionalità di clustering Redis, disponibili nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica per la disponibilità elevata. Per altre informazioni, vedere [Come configurare il clustering per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
* **Sicurezza e isolamento rete ottimizzati**: La distribuzione di Rete virtuale di Azure offre sicurezza e isolamento migliorate per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).
* **Configurare Redis**: nei livelli Standard e Premium, è possibile configurare Redis per le notifiche di Keyspace.
* **Numero massimo di connessioni client**: il livello Premium offre il numero massimo di client che possono connettersi a Redis, con un numero maggiore di connessioni per cache di dimensioni più grandi. Il clustering non aumenta il numero di connessioni disponibili per una cache in cluster. Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Core dedicato per il server Redis**: nel livello Premium per tutte le dimensioni della cache è disponibile un core dedicato per Redis. Nei livelli Basic/Standard, per le dimensioni C1 e superiori è disponibile un core dedicato per il server Redis.
* **Elaborazione a thread singolo**: Redis, per impostazione predefinita, usa un solo thread per l'elaborazione dei comandi. Cache di Azure per Redis usa anche core aggiuntivi per l'elaborazione di I/O. Un maggior numero di core migliora le prestazioni della velocità effettiva anche se potrebbe non garantire la scalabilità lineare. Inoltre, le dimensioni di macchina virtuale più elevate prevedono in genere limiti di larghezza di banda superiori. Questo consente di evitare la saturazione della rete, che causa i timeout dell'applicazione.
* **Miglioramenti delle prestazioni**: le cache nel livello Premium sono distribuite su hardware con processori più veloci e che offrono migliori prestazioni rispetto al livello Basic o Standard. Le cache di livello Premium offrono una velocità effettiva più elevata e minori latenze. Per altre informazioni, vedere [Prestazioni di Cache di Azure per Redis](cache-planning-faq.md#azure-cache-for-redis-performance)

È possibile aumentare le prestazioni della cache passando a un livello superiore dopo averla creata. Il passaggio a un piano inferiore non è supportato. Per istruzioni dettagliate sul ridimensionamento, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md) e [Come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passaggi successivi
* [Guida introduttiva all'app Web ASP.NET](cache-web-app-howto.md): creare una semplice app Web ASP.NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET](cache-dotnet-how-to-use-azure-redis-cache.md): creare un'app .NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET Core](cache-dotnet-core-quickstart.md): creare un'app .NET Core che usa Cache Redis di Azure.
* [Guida introduttiva a Node.js](cache-nodejs-get-started.md): creare una semplice app Node.js che usa Cache Redis di Azure.
* [Guida introduttiva a Java](cache-java-get-started.md): creare una semplice app Java che usa Cache Redis di Azure.
* [Guida introduttiva a Python](cache-python-get-started.md): creare un'app Python che usa Cache Redis di Azure.

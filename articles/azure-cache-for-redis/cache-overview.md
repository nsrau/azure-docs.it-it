---
title: Informazioni su Cache Redis di Azure
description: Informazioni su come usare Cache di Azure per Redis per abilitare le funzionalità di cache-aside, memorizzazione del contenuto nella cache, memorizzazione nella cache delle sessioni utente, accodamento di messaggi e processi e transazioni distribuite.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: 38936000e426d560237295105b5456429d9ae16d
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126358"
---
# <a name="azure-cache-for-redis-description"></a>Descrizione di Cache Redis di Azure

Cache di Azure per Redis offre un archivio dati in memoria basato sul software open source [Redis](https://redis.io/). Quando viene usato come cache, Redis migliora le prestazioni e la scalabilità dei sistemi che si basano su archivi dati back-end. Le prestazioni vengono migliorate copiando i dati usati di frequente all'interno di una risorsa di archiviazione rapida vicina all'applicazione. Con Cache di Azure per Redis, questa risorsa di archiviazione rapida è situata all'interno della memoria invece di essere caricata dal disco da un database.

È possibile usare Cache di Azure per Redis anche come archivio della struttura dei dati in memoria, database non relazionale distribuito e broker messaggi. Le prestazioni dell'applicazione vengono aumentate grazie alle prestazioni a bassa latenza ed elevata velocità effettiva del motore Redis.

Cache di Azure per Redis consente di accedere a una cache Redis sicura e dedicata. Cache Redis di Azure è gestita da Microsoft, ospitata in Azure e accessibile dall'interno o dall'esterno di Azure.

## <a name="using-azure-cache-for-redis"></a>Uso di Cache Redis di Azure

Cache di Azure per Redis migliora le prestazioni dell'applicazione grazie al supporto modelli di architettura delle applicazioni comuni. Alcuni dei più comuni sono:

| Modello      | Descrizione                                        |
| ------------ | -------------------------------------------------- |
| [Cache-aside](cache-web-app-cache-aside-leaderboard.md) | Spesso i database sono troppo grandi per essere caricati direttamente in una cache. In genere si usa il modello [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) per caricare i dati nella cache solo in base alle esigenze. Quando il sistema apporta modifiche ai dati, può anche aggiornare la cache, che viene quindi distribuita ad altri client. Inoltre, il sistema può impostare una scadenza per i dati oppure usare criteri di rimozione per attivare gli aggiornamenti dei dati nella cache.|
| [Memorizzazione di contenuti nella cache](cache-aspnet-output-cache-provider.md) | Molte pagine Web vengono generate da modelli che usano contenuto statico, ad esempio intestazioni, piè di pagina e banner. Questi elementi statici non devono essere modificati di frequente. L'uso di una cache in memoria offre un accesso al contenuto statico più rapido rispetto agli archivi dati back-end. Questo modello riduce il tempo di elaborazione e il carico del server, consentendo ai server Web di essere più reattivi. Permette inoltre di ridurre il numero di server necessari per gestire i carichi. Cache di Azure per Redis fornisce il provider di cache di output per Redis per supportare questo modello con ASP.NET.|
| [Usare la memorizzazione nella cache di una sessione](cache-aspnet-session-state-provider.md) | Questo modello viene comunemente usato con i carrelli della spesa e altri dati della cronologia utente che un'applicazione Web potrebbe voler associare ai cookie dell'utente. L'archiviazione di troppi dati in un cookie può avere un impatto negativo sulle prestazioni poiché le dimensioni dei cookie aumentano, vengono superate e convalidate con ogni richiesta. Una soluzione tipica consiste nell'usare il cookie come chiave per eseguire query sui dati in un database. L'uso di una cache interna alla memoria, come Cache Redis di Azure, per associare informazioni a un utente risulta molto più rapido rispetto all'interazione con un database relazionale completo. |
| Accodamento di messaggi e processi | Spesso le applicazioni aggiungono le attività a una coda quando l'esecuzione delle operazioni associate alla richiesta richiede tempo. Le operazioni a esecuzione prolungata vengono accodate per essere elaborate in sequenza, spesso da un altro server.  Questo metodo di rinviare il lavoro viene detto accodamento delle attività. Cache di Azure per Redis fornisce una coda distribuita per abilitare questo modello nell'applicazione.|
| Transazioni distribuite | Talvolta le applicazioni richiedono una serie di comandi su un archivio dati back-end per l'esecuzione come singola operazione atomica. Tutti i comandi devono avere esito positivo oppure è necessario eseguire il rollback allo stato iniziale. Cache di Azure per Redis supporta l'esecuzione di un batch di comandi come singola [transazione](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Offerte disponibili per Cache Redis di Azure

Cache Redis di Azure è disponibile nei livelli seguenti:

| Livello | Descrizione |
|---|---|
Basic | Una cache con un nodo singolo. Questo livello supporta più dimensioni di memoria (da 250 MB a 53 GB) ed è ideale per carichi di lavoro di sviluppo/test e non critici. Il livello Basic non dispone di alcun contratto di servizio |
| Standard | Una cache replicata in una configurazione a due nodi, primario/secondario, gestita da Azure, con un contratto di servizio che garantisce disponibilità elevata (99,9%) |
| Premium | Premium è il piano di livello aziendale. Le cache di livello Premium supportano più funzionalità e hanno una velocità effettiva superiore con latenze inferiori. Le cache di livello Premium sono distribuite su un hardware più potente e che offre prestazioni migliori rispetto al livello Standard o Basic. Questo vantaggio significa che la velocità effettiva per una cache delle stesse dimensioni sarà superiore nel livello Premium rispetto al livello Standard. |

> [!TIP]
> Per altre informazioni su dimensioni, velocità effettiva e larghezza di banda con le cache Premium, vedere [Domande frequenti su Cache Redis di Azure](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

È possibile aumentare le prestazioni della cache passando a un livello superiore dopo averla creata. Il passaggio a un piano inferiore non è supportato. Per istruzioni dettagliate sul ridimensionamento, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md) e [Come automatizzare un'operazione di ridimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Confronto tra le funzionalità

Nella pagina [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/) è presente un confronto dettagliato dei livelli disponibili. La tabella seguente descrive alcune delle funzionalità supportate in base al livello:

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

## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva all'app Web ASP.NET](cache-web-app-howto.md): creare una semplice app Web ASP.NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET](cache-dotnet-how-to-use-azure-redis-cache.md): creare un'app .NET che usa Cache Redis di Azure.
* [Guida introduttiva a .NET Core](cache-dotnet-core-quickstart.md): creare un'app .NET Core che usa Cache Redis di Azure.
* [Guida introduttiva a Node.js](cache-nodejs-get-started.md): creare una semplice app Node.js che usa Cache Redis di Azure.
* [Guida introduttiva a Java](cache-java-get-started.md): creare una semplice app Java che usa Cache Redis di Azure.
* [Guida introduttiva a Python](cache-python-get-started.md): creare un'app Python che usa Cache Redis di Azure.

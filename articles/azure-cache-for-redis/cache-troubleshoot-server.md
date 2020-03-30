---
title: Risolvere i problemi del lato server di cache di Azure per Redis
description: Informazioni su come risolvere i problemi comuni sul lato server con la cache di Azure per Redis, ad esempio pressione della memoria, CPU elevata, comandi a esecuzione prolungata o limitazioni della larghezza di banda.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277934"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Risolvere i problemi del lato server di cache di Azure per Redis

In questa sezione vengono illustrati i problemi di risoluzione dei problemi che si verificano a causa di una condizione in una cache di Azure per Redis o nelle macchine virtuali che la ospitano.

- [Utilizzo elevato di memoria nel server Redis](#memory-pressure-on-redis-server)
- [Utilizzo elevato della CPU o carico del server](#high-cpu-usage-or-server-load)
- [Comandi con esecuzione prolungata](#long-running-commands)
- [Limitazione della larghezza di banda lato server](#server-side-bandwidth-limitation)

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Utilizzo elevato di memoria nel server Redis

L'utilizzo elevato di memoria sul lato server causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle richieste. Quando si verifica una pressione della memoria, il sistema può paginare i dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema. Le possibili cause di questo utilizzo elevato di memoria sono diverse:

- La cache viene riempita con i dati vicini alla sua capacità massima.
- Redis sta assistendo a un'elevata frammentazione della memoria. Questa frammentazione è spesso causata dall'archiviazione di oggetti di grandi dimensioni poiché Redis è ottimizzato per oggetti di piccole dimensioni.

Redis espone due statistiche tramite il comando [INFO](https://redis.io/commands/info) che consentono di identificare questo problema: "used_memory" e "used_memory_rss". È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) tramite il portale.

Esistono diverse possibili modifiche che è possibile apportare per mantenere l'utilizzo della memoria sano:There are several possible changes you can make to help keep memory usage healthy:

- [Configurare un criterio per la memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e impostare scadenze per le chiavi. Questo criterio potrebbe non essere sufficiente se si dispone di frammentazione.
- [Configurare un valore maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) abbastanza grande da compensare la frammentazione della memoria.
- Suddividere gli oggetti di grandi dimensioni memorizzati nella cache in oggetti correlati più piccoli.
- [Crea avvisi](cache-how-to-monitor.md#alerts) su metriche come la memoria usata per ricevere una notifica anticipata sui potenziali impatti.
- [Scalabilità fino](cache-how-to-scale.md) a una dimensione della cache maggiore con una maggiore capacità di memoria.

## <a name="high-cpu-usage-or-server-load"></a>Utilizzo elevato della CPU o carico del server

Un elevato carico del server o un utilizzo della CPU significa che il server non è in grado di elaborare le richieste in modo tempestivo. Il server potrebbe essere lento a rispondere e non essere in grado di tenere il passo con le richieste di frequenza.

[Monitorare metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) come il carico della CPU o del server. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout.

Esistono diverse modifiche che è possibile apportare per ridurre il carico elevato del server:There are several changes you can make to mitigate high server load:

- Esaminare la causa dei picchi della CPU, ad esempio i [comandi a esecuzione prolungata](#long-running-commands) indicati di seguito o l'errore di pagina a causa dell'elevata pressione della memoria.
- [Crea avvisi](cache-how-to-monitor.md#alerts) su metriche come il carico della CPU o del server per ricevere una notifica anticipata sui potenziali impatti.
- [Scalabilità fino](cache-how-to-scale.md) a una dimensione della cache maggiore con una maggiore capacità della CPU.

## <a name="long-running-commands"></a>Comandi con esecuzione prolungata

Alcuni comandi Redis sono più costosi da eseguire rispetto ad altri. La [documentazione dei comandi Redis](https://redis.io/commands) mostra la complessità temporale di ogni comando. Poiché l'elaborazione dei comandi Redis è a thread singolo, un comando che richiede tempo per l'esecuzione bloccherà tutti gli altri che verranno successivi. È consigliabile esaminare i comandi che si stanno emettendo al server Redis per comprenderne l'impatto sulle prestazioni. Ad esempio, il comando [KEYS](https://redis.io/commands/keys) viene spesso utilizzato senza sapere che si tratta di un'operazione O(N). È possibile evitare KEYS utilizzando [SCAN](https://redis.io/commands/scan) per ridurre i picchi di CPU.

Utilizzando il comando [SLOWLOG,](https://redis.io/commands/slowlog) è possibile misurare costosi comandi eseguiti sul server.

## <a name="server-side-bandwidth-limitation"></a>Limitazione della larghezza di banda lato server

Dimensioni della cache diverse hanno capacità di larghezza di banda di rete diverse. Se il server supera la larghezza di banda disponibile, i dati non verranno inviati al client con la velocità massima. Le richieste dei client potrebbero sarsi perché il server non è in grado di eseguire il push dei dati al client abbastanza velocemente.

Le metriche "Cache Read" e "Cache Write" possono essere utilizzate per visualizzare la quantità di larghezza di banda lato server utilizzata. È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) nel portale.

Per ridurre le situazioni in cui l'utilizzo della larghezza di banda di rete è vicino alla capacità massima:To mitigate situations where network bandwidth usage is close to maximum capacity:

- Modificare il comportamento delle chiamate client per ridurre la richiesta di rete.
- [Crea avvisi](cache-how-to-monitor.md#alerts) su metriche come la lettura della cache o la scrittura nella cache per ricevere una notifica anticipata sui potenziali impatti.
- [Scalabilità fino](cache-how-to-scale.md) a una dimensione della cache maggiore con una maggiore capacità di larghezza di banda della rete.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi relativi al lato client di cache di Azure per Redis](cache-troubleshoot-client.md)
- [Quali offerte e dimensioni di Cache Redis di Azure è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)

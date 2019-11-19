---
title: Risolvere i problemi del lato server di cache di Azure per il server Redis
description: Informazioni su come risolvere i problemi comuni del lato server con cache di Azure per Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: yegu
ms.openlocfilehash: 22cb4beb3411c617882972e1b91c5f538019fae4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122564"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Risolvere i problemi del lato server di cache di Azure per il server Redis

Questa sezione illustra la risoluzione dei problemi che si verificano a causa di una condizione in una cache di Azure per Redis o delle macchine virtuali che lo ospitano.

- [Pressione della memoria nel server Redis](#memory-pressure-on-redis-server)
- [Utilizzo CPU elevato o carico server](#high-cpu-usage-or-server-load)
- [Comandi con esecuzione prolungata](#long-running-commands)
- [Limitazione della larghezza di banda lato server](#server-side-bandwidth-limitation)

> [!NOTE]
> Diversi passaggi per la risoluzione dei problemi illustrati in questa guida includono istruzioni per eseguire comandi di Redis e monitorare svariate metriche delle prestazioni. Per altre informazioni e istruzioni, vedere gli articoli della sezione [Informazioni aggiuntive](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Pressione della memoria nel server Redis

L'utilizzo elevato di memoria sul lato server causa tutti i tipi di problemi di prestazioni che possono ritardare l'elaborazione delle richieste. Quando si verifica un utilizzo elevato di memoria, il sistema può eseguire il paging dei dati su disco. Questo _errore di pagina_ causa un rallentamento significativo del sistema. Le possibili cause di questo utilizzo elevato di memoria sono diverse:

- La cache viene riempita con i dati vicino alla capacità massima.
- Redis sta osservando una frammentazione elevata della memoria. Questa frammentazione è spesso causata dall'archiviazione di oggetti di grandi dimensioni, dal momento che Redis è ottimizzato per oggetti di piccole dimensioni.

Redis espone due statistiche tramite il comando [info](https://redis.io/commands/info) che consentono di identificare il problema: "used_memory" e "used_memory_rss". È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando il portale.

Esistono diverse possibili modifiche che è possibile apportare per garantire l'integrità dell'utilizzo della memoria:

- [Configurare un criterio per la memoria](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e impostare scadenze per le chiavi. Questo criterio potrebbe non essere sufficiente in caso di frammentazione.
- [Configurare un valore maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) abbastanza grande da compensare la frammentazione della memoria.
- Suddividere gli oggetti di grandi dimensioni memorizzati nella cache in oggetti correlati più piccoli.
- [Creare avvisi](cache-how-to-monitor.md#alerts) sulle metriche, ad esempio la memoria usata, per ricevere notifiche tempestive sugli effetti potenziali.
- [Ridimensionare](cache-how-to-scale.md) le dimensioni della cache con una maggiore capacità di memoria.

## <a name="high-cpu-usage-or-server-load"></a>Utilizzo CPU elevato o carico server

Un utilizzo elevato del server o della CPU significa che il server non è in grado di elaborare le richieste in modo tempestivo. Il server può essere lento a rispondere e non è in grado di soddisfare le tariffe richieste.

[Monitorare le metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , ad esempio il carico della CPU o del server. Cercare nell'utilizzo della CPU i picchi corrispondenti ai timeout.

Sono disponibili diverse modifiche che è possibile apportare per mitigare il carico elevato del server:

- Esaminare gli elementi che causano picchi di CPU, ad esempio i [comandi a esecuzione prolungata](#long-running-commands) indicati di seguito o errori di pagina a causa di un elevato utilizzo della memoria.
- [Creare avvisi](cache-how-to-monitor.md#alerts) sulle metriche, ad esempio il carico della CPU o del server, per ricevere notifiche tempestive sugli effetti potenziali.
- [Scalabilità](cache-how-to-scale.md) a dimensioni della cache maggiori con maggiore capacità di CPU.

## <a name="long-running-commands"></a>Comandi con esecuzione prolungata

Alcuni comandi di redis sono più costosi da eseguire rispetto ad altri. La [documentazione dei comandi di redis](https://redis.io/commands) Mostra la complessità temporale di ogni comando. Dato che l'elaborazione del comando Redis è a thread singolo, un comando che richiede tempo per l'esecuzione bloccherà tutti gli altri che lo derivano. Esaminare i comandi che si stanno inviando al server Redis per comprenderne gli effetti sulle prestazioni. Ad esempio, il comando [Keys](https://redis.io/commands/keys) viene spesso usato senza sapere che si tratta di un'operazione O (N). È possibile evitare le chiavi usando [Scan](https://redis.io/commands/scan) per ridurre i picchi della CPU.

Utilizzando il comando [SLOWLOG](https://redis.io/commands/slowlog) , è possibile misurare i comandi costosi eseguiti sul server.

## <a name="server-side-bandwidth-limitation"></a>Limitazione della larghezza di banda lato server

Diverse dimensioni della cache hanno capacità diverse per la larghezza di banda di rete. Se il server supera la larghezza di banda disponibile, i dati non verranno inviati al client con la stessa rapidità. È possibile che si verifichi il timeout delle richieste dei client perché il server non è in grado di eseguire il push dei dati al client

Per visualizzare la quantità di larghezza di banda sul lato server in uso, è possibile usare le metriche "lettura cache" e "scrittura cache". È possibile [visualizzare queste metriche](cache-how-to-monitor.md#view-metrics-with-azure-monitor) nel portale.

Per attenuare le situazioni in cui l'utilizzo della larghezza di banda di rete è prossimo alla capacità massima:

- Modificare il comportamento della chiamata client per ridurre la richiesta di rete.
- [Creare avvisi](cache-how-to-monitor.md#alerts) sulle metriche come la lettura della cache o la scrittura nella cache per ricevere notifiche tempestive sugli effetti potenziali.
- [Scalabilità](cache-how-to-scale.md) a dimensioni della cache maggiori con maggiore capacità della larghezza di banda di rete.

## <a name="additional-information"></a>Informazioni aggiuntive

- [Risolvere i problemi relativi al lato client di cache di Azure per Redis](cache-troubleshoot-client.md)
- [Quali offerte e dimensioni della Cache Redis è consigliabile usare?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [In che modo è possibile valutare e testare le prestazioni della cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md)
- [Come si eseguono i comandi Redis?](cache-faq.md#how-can-i-run-redis-commands)

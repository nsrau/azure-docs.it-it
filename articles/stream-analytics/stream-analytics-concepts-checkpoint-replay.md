---
title: Concetti di checkpoint e riproduzione per il ripristino dei processi in Analisi di flusso di Azure
description: Questo articolo illustra i concetti di checkpoint e riproduzione per il ripristino dei processi in Analisi di flusso di Azure.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 9dcfbd4b5fcc8462c88b16f585424166ecd3d499
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088256"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Concetti di checkpoint e riproduzione nei processi di Analisi di flusso di Azure
Questo articolo illustra i concetti di checkpoint interno e riproduzione in Analisi di flusso di Azure e il relativo impatto sul ripristino dei processi. Ogni volta che viene eseguito un processo di Analisi di flusso, le informazioni sullo stato vengono mantenute all'interno del servizio e salvate periodicamente in un checkpoint. In alcuni scenari le informazioni dei checkpoint vengono usate per il ripristino dei processi in caso di errore o di aggiornamento. In altre circostanze il checkpoint non può essere usato per il ripristino ed è necessario procedere alla riproduzione.

## <a name="stateful-query-logicin-temporal-elements"></a>Logica di query con stato negli elementi temporali
Una delle esclusive funzionalità dei processi di Analisi di flusso di Azure è l'esecuzione dell'elaborazione con stato, ad esempio per funzioni di aggregazione finestra, join temporali e funzioni di analisi temporali. Ognuno di questi operatori mantiene le informazioni sullo stato durante l'esecuzione del processo. La dimensione massima della finestra temporale per questi elementi di query è sette giorni. 

Il concetto di finestra temporale è presente in diversi elementi di query di Analisi di flusso:
1. Funzioni di aggregazione finestra (GROUP BY di finestre temporali scorrevoli, di salto e a cascata)

2. Join temporali (JOIN con DATEDIFF)

3. Funzioni di analisi temporale (ISFIRST, LAST e LAG con LIMIT DURATION)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Ripristino di un processo in seguito a errore del nodo e aggiornamento del sistema operativo
Ogni volta che viene eseguito un processo di Analisi di flusso, all'interno del servizio il processo viene scalato orizzontalmente per l'elaborazione su più nodi di lavoro. Per ogni nodo di lavoro viene generato un checkpoint dello stato ogni pochi minuti, in modo da facilitare il ripristino del sistema in caso di errore.

Talvolta può verificarsi un errore su un determinato nodo di lavoro oppure un aggiornamento del sistema operativo per il nodo. In tal caso, Analisi di flusso acquisisce un nuovo nodo integro e ripristina automaticamente lo stato del nodo di lavoro precedente in base al checkpoint disponibile più recente. Per riprendere il lavoro, è necessario eseguire una breve riproduzione che consenta di ripristinare lo stato a partire dal momento in cui è stato acquisito il checkpoint. L'intervallo di ripristino è in genere di solo pochi minuti. Quando è selezionata una quantità sufficiente di unità di streaming per il processo, la riproduzione viene in genere completata in modo rapido. 

In una query perfettamente parallela, il tempo necessario per il ripristino in caso di errore di un nodo di lavoro è proporzionale a:

[frequenza di input degli eventi] x [durata dell'intervallo] / [numero di partizioni di elaborazione]

Se si nota un ritardo significativo nell'elaborazione a causa di un errore del nodo e dell'aggiornamento del sistema operativo, valutare l'opportunità rendere la query perfettamente parallela e di ridimensionare il processo per allocare altre unità di streaming. Per altre informazioni, vedere [Ridimensionare un processo di Analisi di flusso di Azure per aumentare la velocità effettiva](stream-analytics-scale-jobs.md).

La versione corrente di Analisi di flusso non visualizza un report quando viene eseguito questo tipo di ripristino.

## <a name="job-recovery-from-a-service-upgrade"></a>Ripristino di un processo in seguito a un aggiornamento del servizio 
Occasionalmente Microsoft aggiorna i file binari che eseguono i processi di Analisi di flusso nel servizio di Azure. In questi casi, i processi in esecuzione per gli utenti vengono aggiornati alla versione più recente e riavviati automaticamente. 

Nella versione corrente il formato dei checkpoint di ripristino non viene mantenuto da un aggiornamento all'altro. Di conseguenza, lo stato della query di streaming deve essere ripristinato interamente usando la tecnica di riproduzione. Per consentire i processi di Analisi di flusso di riprodurre esattamente lo stesso input precedente, è importante impostare i criteri di conservazione dei dati di origine almeno sulla dimensione della finestra temporale nella query. In caso contrario, è possibile che durante l'aggiornamento del servizio vengano restituiti risultati parziali o non corretti perché i dati di origine potrebbero non essere stati conservati abbastanza a lungo da includere la dimensione dell'intera finestra temporale.

La quantità di riproduzione necessaria è in genere proporzionale alla dimensione della finestra moltiplicata per la frequenza media degli eventi. Ad esempio, per un processo con una frequenza di input di 1000 eventi al secondo, una dimensione della finestra superiore a un'ora richiede un'ampia dimensione di riproduzione. Potrebbe essere necessaria un'ora di rielaborazione dei dati per inizializzare lo stato in modo da produrre risultati completi e corretti, che potrebbe causare un ritardo dell'output (nessun output) per un periodo di tempo prolungato. Per le query senza finestre o di altri operatori temporali, come `JOIN` o `LAG`, non è prevista nessuna riproduzione.

## <a name="estimate-replay-catch-up-time"></a>Stimare il tempo di ripristino
Per stimare la durata del ritardo a causa di un aggiornamento del servizio, è possibile adottare questa tecnica:

1. Caricare l'hub eventi di input con dati sufficienti per coprire la dimensione massima della finestra nella query, in base alla frequenza degli eventi prevista. Il timestamp degli eventi deve corrispondere approssimativamente all'ora reale durante tale periodo, come per un feed di input in tempo reale. Se ad esempio per la query è definita una finestra di tre giorni, inviare eventi all'hub per tre giorni e continuare a inviare eventi. 

2. Avviare il processo usando **Now** come ora di inizio. 

3. Misurare il tempo tra l'ora di inizio e il momento in cui viene generato il primo output. Questo tempo corrisponde approssimativamente al ritardo che subirebbe il processo durante un aggiornamento del servizio.

4. Se il ritardo è troppo lungo, provare a suddividere il processo e ad aumentare il numero di unità di streaming, in modo da distribuire il carico a più nodi. In alternativa, valutare l'opportunità di ridurre la dimensione della finestra temporale nella query ed eseguire un'altra aggregazione o un altro processo di elaborazione con stato sull'output generato dal processo di Analisi di flusso nel sink downstream, ad esempio usando il database SQL di Azure.

In caso di problemi di stabilità generale del servizio durante l'aggiornamento di processi di importanza cruciale, valutare l'opportunità di eseguire processi duplicati in aree di Azure abbinate. Per altre informazioni, vedere [Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Ripristino di un processo in seguito all'arresto e al riavvio da parte di un utente
Per modificare la sintassi di query in un processo di streaming o per modificare gli input e gli output, è necessario arrestare il processo per apportare le modifiche e aggiornare la progettazione. In scenari di questo tipo, quando un utente arresta e riavvia il processo di streaming, lo scenario di ripristino è simile a quello relativo all'aggiornamento del servizio. 

Quando un processo viene riavviato da un utente, i dati dei checkpoint non possono essere utilizzati. Per stimare il ritardo dell'output durante il riavvio, seguire la stessa procedura descritta nella sezione precedente e adottare un approccio di mitigazione analogo se il ritardo è troppo lungo.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla scalabilità e sull'affidabilità, vedere questi articoli:
- [Esercitazione: Impostare gli avvisi per i processi di Analisi di flusso di Azure](stream-analytics-set-up-alerts.md)
- [Ridimensionare un processo di Analisi di flusso di Azure per aumentare la velocità effettiva](stream-analytics-scale-jobs.md)
- [Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio](stream-analytics-job-reliability.md)

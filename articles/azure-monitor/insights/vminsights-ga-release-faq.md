---
title: Domande frequenti su Monitoraggio di Azure per le macchine virtuali (GA) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali è una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo risponde alle domande più comuni sulla versione GA.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 523fb2d3a3b148afc9219e666c2fbe7fa40d58ad
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553792"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Monitoraggio di Azure per le macchine virtuali domande frequenti disponibili a livello generale (GA)

Recentemente abbiamo annunciato nel Blog di [Azure Update](https://azure.microsoft.com/blog/) alcune modifiche pianificate che verranno eseguite nel ottobre e nel 2019 novembre. Altre informazioni su queste modifiche sono descritte in questa sezione Domande frequenti sulla disponibilità generale.

## <a name="updates-for-azure-monitor-for-vms"></a>Aggiornamenti per Monitoraggio di Azure per le macchine virtuali

Si sta rilasciando una nuova versione di Monitoraggio di Azure per le macchine virtuali a novembre. I clienti che abilitano i monitoraggi di Azure per le macchine virtuali dopo questa versione ricevono automaticamente la nuova versione, ma viene richiesto di aggiornare i clienti esistenti che usano già Monitoraggio di Azure per le macchine virtuali.  Queste domande frequenti e la documentazione offrono indicazioni per eseguire un aggiornamento in blocco se si dispone di distribuzioni di grandi dimensioni in più aree di lavoro.

Con questo aggiornamento, i set di dati delle prestazioni Monitoraggio di Azure per le macchine virtuali vengono ora archiviati nella stessa tabella `InsightsMetrics` di [monitoraggio di Azure per i contenitori](container-insights-overview.md)e semplificano la query dei due set di dati. Inoltre, è possibile archiviare set di dati più diversi che non è stato possibile archiviare nella tabella usata in precedenza.  Le visualizzazioni delle prestazioni verranno aggiornate anche per l'uso della nuova tabella.

Viene spostato in nuovi tipi di dati per i set di dati di connessione. I dati attualmente archiviati in `ServiceMapComputer_CL` e `ServiceMapProcess_CL`, che utilizzano tabelle di log personalizzate, verranno spostati in tipi di dati dedicati denominati `VMComputer` e `VMProcess`.  Passando ai tipi di dati dedicati, è possibile assegnare queste priorità per l'inserimento dei dati e lo schema della tabella verrà standardizzato in tutti i clienti.

Ci rendiamo conto che la richiesta di aggiornamento da parte dei clienti esistenti causa un'alterazione del flusso di lavoro. Ecco perché abbiamo scelto di eseguire questa operazione ora in anteprima pubblica anziché in un secondo momento.

## <a name="what-will-change"></a>Quali modifiche verranno apportate?

Attualmente, quando si completa il processo di caricamento per Monitoraggio di Azure per le macchine virtuali, si Abilita la soluzione Mapping dei servizi nell'area di lavoro selezionata per archiviare i dati di monitoraggio e quindi si configurano i contatori delle prestazioni per i dati raccolti dalle macchine virtuali. Nelle prossime settimane verrà rilasciata una nuova soluzione, denominata **VMInsights**, che includerà funzionalità aggiuntive per la raccolta dei dati insieme a una nuova posizione per l'archiviazione dei dati in log Analytics.

Il processo corrente di utilizzo dei contatori delle prestazioni nell'area di lavoro invia i dati alla tabella delle prestazioni in Log Analytics.  Questa nuova soluzione invierà i dati a una tabella denominata `InsightsMetrics` usata anche da monitoraggio di Azure per i contenitori. Questo schema di tabella consente di archiviare metriche e set di dati del servizio aggiuntivi che non sono compatibili con il formato di tabella delle prestazioni.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Cosa devo fare per i contatori delle prestazioni nell'area di lavoro se si installa la soluzione VMInsights?

Il metodo corrente di abilitazione di Monitoraggio di Azure per le macchine virtuali usa i contatori delle prestazioni nell'area di lavoro. Il nuovo metodo archivia questi dati in una nuova tabella denominata `InsightsMetrics`.

Una volta aggiornata l'interfaccia utente per l'uso dei dati in InsightsMetrics, la documentazione verrà aggiornata e l'annuncio verrà comunicato tramite più canali, inclusa la visualizzazione di un banner nella portale di Azure. A questo punto, è possibile scegliere di disabilitare questi [contatori delle prestazioni](vminsights-enable-overview.md#performance-counters-enabled) nell'area di lavoro se non si vuole più usarli. 

[!NOTE]
>Se sono presenti regole di avviso che fanno riferimento a questi contatori nella tabella delle prestazioni, è necessario aggiornarli in modo che facciano riferimento ai nuovi dati nella tabella `InsightsMetrics`.  Vedere la documentazione per le query di log di esempio che è possibile usare per fare riferimento a questa tabella.

Se si decide di mantenere i contatori delle prestazioni abilitati, verranno addebitati i dati inseriti e conservati nella tabella delle prestazioni in base a [Log Analytics pricing [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>In che modo questa modifica influirà sulle regole di avviso?

Se sono stati creati [avvisi del log](../platform/alerts-unified-log.md) che eseguono una query sulla tabella `Perf` destinata ai contatori delle prestazioni abilitati nell'area di lavoro, è necessario aggiornare queste regole per fare riferimento alla tabella `InsightsMetrics`. Queste linee guida si applicano anche a tutte le regole di ricerca nei log che usano `ServiceMapComputer_CL` e `ServiceMapProcess_CL`, perché tali set di dati vengono spostati nelle tabelle `VMComputer` e `VMProcess`.

Le domande frequenti e la documentazione per includere le regole di avviso di ricerca log di esempio per i set di dati raccolti vengono aggiornate.

## <a name="will-there-be-any-changes-to-billing"></a>Verranno apportate modifiche alla fatturazione?

La fatturazione è ancora basata sui dati inseriti e conservati nell'area di lavoro Log Analytics.

I dati sulle prestazioni a livello di computer raccolti sono gli stessi, con dimensioni simili ai dati archiviati nella tabella delle prestazioni e che costeranno approssimativamente la stessa quantità.

## <a name="what-if-i-only-want-to-use-service-map"></a>Cosa accade se si vuole usare solo Mapping dei servizi?

Questo è un problema.  Quando si visualizzano Monitoraggio di Azure per le macchine virtuali sull'aggiornamento imminente, vengono visualizzate richieste nella portale di Azure. Una volta rilasciata, viene visualizzato un messaggio che richiede di eseguire l'aggiornamento alla nuova versione. Se si preferisce usare solo la funzionalità [Maps](vminsights-maps.md) , è possibile scegliere di non eseguire l'aggiornamento e continuare a usare la funzionalità Maps monitoraggio di Azure per le macchine virtuali e la soluzione mapping dei servizi a cui si accede dall'area di lavoro o dal riquadro del dashboard.

Se si sceglie di abilitare manualmente i contatori delle prestazioni nell'area di lavoro, potrebbe essere possibile visualizzare i dati in alcuni dei grafici delle prestazioni visualizzati da monitoraggio di Azure. Una volta rilasciata la nuova soluzione, i grafici delle prestazioni vengono aggiornati per eseguire query sui dati archiviati nella tabella `InsightsMetrics`. Se si desidera visualizzare i dati di tale tabella in questi grafici, sarà necessario eseguire l'aggiornamento alla nuova versione di Monitoraggio di Azure per le macchine virtuali.

Le modifiche per spostare i dati da `ServiceMapComputer_CL` e `ServiceMapProcess_CL` avranno effetto sia Mapping dei servizi che Monitoraggio di Azure per le macchine virtuali, quindi è comunque necessario pianificare l'aggiornamento.

Se si sceglie di non eseguire l'aggiornamento alla soluzione **VMInsights** , si continuerà a fornire le versioni legacy delle cartelle di lavoro delle prestazioni che fanno riferimento ai dati nella tabella `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>I set di dati Mapping dei servizi verranno archiviati anche in InsightsMetrics?

I set di dati non verranno duplicati se si utilizzano entrambe le soluzioni. Entrambe le offerte condividono i set di dati che verranno archiviati nelle tabelle `VMComputer` (in precedenza ServiceMapComputer_CL), `VMProcess` (in precedenza ServiceMapProcess_CL), `VMConnection` e `VMBoundPort` per archiviare i set di dati della mappa raccolti.  

La tabella `InsightsMetrics` verrà usata per archiviare i set di dati di macchine virtuali, processi e servizi raccolti e verrà popolato solo se si usa Monitoraggio di Azure per le macchine virtuali.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Viene addebitato un costo doppio se sono disponibili le soluzioni Mapping dei servizi e VMInsights nell'area di lavoro?

No, le due soluzioni condividono i set di dati della mappa archiviati in `VMComputer` (in precedenza ServiceMapComputer_CL), `VMProcess` (in precedenza ServiceMapProcess_CL), `VMConnection` e `VMBoundPort`.  I dati non verranno addebitati a doppio se si dispone di entrambe le soluzioni nell'area di lavoro.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Se si rimuove la soluzione Mapping dei servizi o VMInsights, i dati vengono rimossi in Log Analytics?

No, le due soluzioni condividono i set di dati della mappa archiviati in `VMComputer` (in precedenza ServiceMapComputer_CL), `VMProcess` (in precedenza ServiceMapProcess_CL), `VMConnection` e `VMBoundPort`.  Se si rimuove una delle soluzioni, questi set di dati si notano che è ancora disponibile una soluzione che usa i dati e rimane in Log Analytics.  È necessario rimuovere entrambe le soluzioni dall'area di lavoro per rimuovere i dati dall'area di lavoro Log Analytics.

## <a name="when-will-this-update-be-released"></a>Quando verrà rilasciato questo aggiornamento?

Si prevede di rilasciare l'aggiornamento per Monitoraggio di Azure per le macchine virtuali a metà novembre. Man mano che ci avviciniamo alla data di rilascio, gli aggiornamenti verranno pubblicati qui e verranno presentate le notifiche nella portale di Azure quando si passa a monitoraggio di Azure.

## <a name="health-feature-to-enter-limited-public-preview"></a>Funzionalità di integrità per l'immissione dell'anteprima pubblica limitata

Abbiamo ricevuto numerosi commenti e suggerimenti dai clienti sul set di funzionalità per l'integrità delle macchine virtuali.  Questa funzionalità è molto interessante e può essere utile per supportare i flussi di lavoro di monitoraggio. Si prevede di apportare una serie di modifiche per aggiungere funzionalità e risolvere i commenti ricevuti. Per ridurre al minimo l'effetto di queste modifiche ai nuovi clienti, la funzionalità viene trasferita in un'anteprima pubblica limitata.

Questa transizione inizierà all'inizio di ottobre e verrà completata entro la fine del mese.

Di seguito sono riportate alcune delle aree in cui ci si concentrerà:

- Maggiore controllo sul modello di integrità e sulle soglie
- Creazione di modelli di integrità a livello di scalabilità applicabili a un ambito di macchine virtuali
- Uso nativo della piattaforma avvisi per la gestione delle regole di avviso basate sull'integrità
- Possibilità di visualizzare l'integrità in un ambito più ampio, ad esempio una o più sottoscrizioni
- Latenza ridotta nelle transizioni di integrità e notifiche di avviso

## <a name="how-do-existing-customers-access-the-health-feature"></a>In che modo i clienti esistenti accedono alla funzionalità di integrità?

I clienti esistenti che usano la funzionalità di integrità continueranno ad avere accesso a tale funzionalità, ma non verranno offerti ai nuovi clienti.  

Per accedere alla funzionalità, è possibile aggiungere il flag di funzionalità seguente `feature.vmhealth=true` all'URL del portale [https://portal.azure.com](https://portal.azure.com). Esempio `https://portal.azure.com/?feature.vmhealth=true`.

È anche possibile usare questo breve URL, che imposta automaticamente il flag di funzionalità: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Come cliente esistente, è possibile continuare a usare la funzionalità di integrità nelle VM connesse a una configurazione dell'area di lavoro esistente con la funzionalità di integrità.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Si usa ora l'integrità VM con un ambiente e si vuole distribuirla per un nuovo ambiente

Se si è un cliente esistente che utilizza la funzionalità di integrità e si desidera utilizzarlo per un nuovo Rollup, contattare Microsoft all'indirizzo vminsights@microsoft.com per richiedere istruzioni.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui requisiti e i metodi per il monitoraggio delle macchine virtuali, vedere [Distribuire Monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md).

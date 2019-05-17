---
title: Domande frequenti su Monitoraggio di Azure per le macchine virtuali (anteprima) | Microsoft Docs
description: Monitoraggio di Azure per le macchine virtuali è una soluzione di Azure che combina il monitoraggio dell'integrità e delle prestazioni del sistema operativo delle macchine virtuali di Azure, nonché l'individuazione automatica dei componenti e delle dipendenze delle applicazioni con altre risorse e mappa la comunicazione tra questi elementi. Questo articolo fornisce le risposte alle domande frequenti.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 420ba9d74532095c2d028fef8f549d532e5dfa05
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522216"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure per le macchine virtuali (anteprima)
Le Domande frequenti Microsoft sono un elenco di domande frequenti su Monitoraggio di Azure per le macchine virtuali. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

## <a name="can-i-onboard-to-an-existing-workspace"></a>È possibile eseguire l'onboarding in un'area di lavoro esistente?
Se le macchine virtuali sono già connesse a un'area di lavoro Log Analytics, è possibile continuare a usare tale area di lavoro quando si esegue l'onboarding per Monitoraggio di Azure per le macchine virtuali, purché si trovi in una delle aree supportate elencate [qui](vminsights-enable-overview.md#prerequisites).

Quando si esegue l'onboarding, si configurano i contatori delle prestazioni per l'area di lavoro in modo che tutte le macchine virtuali che inviano dati all'area di lavoro inizino a raccogliere queste informazioni per la visualizzazione e l'analisi in Monitoraggio di Azure per le macchine virtuali.  Di conseguenza, verranno visualizzati i dati sulle prestazioni di tutte le macchine virtuali connesse all'area di lavoro selezionata.  Le funzionalità di integrità e mappa sono abilitate solo per le macchine virtuali specificate per l'onboarding.

Per altre informazioni su quali le prestazioni sono abilitati i contatori, fare riferimento a nostro [abilitare Panoramica](vminsights-enable-overview.md#performance-counters-enabled) articolo.

## <a name="can-i-onboard-to-a-new-workspace"></a>È possibile eseguire l'onboarding in una nuova area di lavoro? 
Se le macchine virtuali non attualmente connesse a un'area di lavoro Log Analytics esistente, è necessario crearne una nuova per archiviare i dati. La creazione di una nuova area di lavoro predefinita avviene automaticamente nel caso in cui si configuri una singola macchina virtuale di Azure per Monitoraggio di Azure per le macchine virtuali tramite il portale di Azure.

Se si sceglie di usare il metodo basato su script, questi passaggi vengono descritti nel [abilitare monitoraggio di Azure per le macchine virtuali (anteprima) tramite Azure PowerShell o Resource Manager il modello](vminsights-enable-at-scale-powershell.md) articolo. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Cosa occorre fare se la macchina virtuale invia già informazioni a un'area di lavoro esistente?
Se già si raccolgono dati dalle macchine virtuali, è possibile che sia già stato configurato l'invio di dati a un'area di lavoro Log Analytics esistente.  Se quest'area di lavoro è in una delle aree supportate, è possibile abilitare Monitoraggio di Azure per le macchine virtuali per tale area di lavoro preesistente.  Se l'area di lavoro già in uso non si trova in una delle aree supportate, non è attualmente possibile eseguire l'onboarding per Monitoraggio di Azure per le macchine virtuali.  Microsoft sta lavorando attivamente per supportare altre aree.

>[!NOTE]
>Microsoft configura i contatori delle prestazioni per l'area di lavoro con effetto su tutte le macchine virtuali che inviano informazioni all'area di lavoro, anche se non si è scelto di eseguirne l'onboarding per Monitoraggio di Azure per le macchine virtuali. Per altre informazioni sulla configurazione dei contatori delle prestazioni per l'area di lavoro, vedere la [documentazione](../../azure-monitor/platform/data-sources-performance-counters.md). Per informazioni sui contatori configurato per monitoraggio di Azure per le macchine virtuali, consultare il [abilitare il monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md#performance-counters-enabled) articolo.  

## <a name="why-did-my-vm-fail-to-onboard"></a>Perché la macchina virtuale non è riuscita a eseguire l'onboarding?
Quando si esegue l'onboarding di una macchina virtuale di Azure dal portale di Azure, si verificano i passaggi seguenti:

* Viene creata un'area di lavoro Log Analytics predefinita, se tale opzione è stata selezionata.
* I contatori delle prestazioni vengono configurati per l'area di lavoro selezionata. Se questo passaggio non riesce, si noterà che alcuni grafici delle prestazioni e alcune tabelle non contengono i dati relativi alla macchina virtuale di cui è stato eseguito l'onboarding. Per risolvere il problema, è possibile eseguire lo script di PowerShell documentato [qui](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* L'agente di Log Analytics viene installato nelle macchine virtuali di Azure mediante un'estensione VM, se necessario.  
* Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali viene installato nelle macchine virtuali di Azure mediante un'estensione, se necessario.  
* I componenti di Monitoraggio di Azure che supportano la funzionalità Integrità vengono configurati, se necessario, e la macchina virtuale viene configurata per l'invio dei dati sull'integrità.

Durante il processo di onboarding, viene controllato lo stato di ciascun passaggio, visualizzando una notifica nel portale. La configurazione dell'area di lavoro e l'installazione dell'agente richiedono in genere 5-10 minuti. La visualizzazione dei dati di monitoraggio e sull'integrità nel portale richiedere altri 5-10 minuti.  

Se l'onboarding è stato avviato e vengono visualizzati dei messaggi che indicano che occorre eseguire l'onboarding della macchina virtuale, attendere 30 minuti affinché la macchina virtuale completi il processo. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Anche se è stato abilitato solo Monitoraggio di Azure per le macchine virtuali, perché vengono visualizzate tutte le macchine virtuali monitorate dalla funzionalità di integrità?
La funzionalità di integrità è abilitata per tutte le macchine virtuali connesse all'area di lavoro Log Analytics, anche quando l'operazione viene avviata per una singola macchina virtuale.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>È possibile modificare il tempo pianificato per la valutazione di una condizione da parte dei criteri di integrità?
No, in questa versione non è possibile modificare il periodo di tempo e la frequenza dei criteri di integrità. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>È possibile disabilitare i criteri di integrità per una condizione che non è necessario monitorare?
In questa versione i criteri di integrità non possono essere disabilitati.

## <a name="are-the-health-alert-severities-configurable"></a>È possibile configurare i livelli di gravità degli avvisi di integrità?  
La gravità degli avvisi di integrità non può essere modificata. Gli avvisi possono solo essere abilitati o disabilitati. Inoltre, alcuni livelli di gravità degli avvisi vengono aggiornati in base allo stato dei criteri di integrità. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Se si riconfigurano le impostazioni di un determinato criterio di integrità, è possibile limitarne l'ambito a un'istanza specifica?  
Se si modifica una qualsiasi impostazione di un'istanza di criterio di integrità, nella macchina virtuale di Azure vengono modificate tutte le istanze di criterio di integrità dello stesso tipo. Se ad esempio viene modificata la soglia dell'istanza del criterio di integrità relativo allo spazio libero su disco che corrisponde al disco logico C:, questa soglia viene applicata a tutti gli altri dischi logici individuati e monitorati per la stessa macchina virtuale.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>La funzionalità di integrità esegue il monitoraggio di core e processori logici?
No, i criteri di integrità a livello di singolo processore e di processore logico non sono inclusi per Windows. Per impostazione predefinita, viene monitorato solo l'utilizzo della CPU totale allo scopo di valutare in modo efficace l'utilizzo elevato della CPU in base al numero totale di CPU logiche disponibili per la macchina virtuale di Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Tutte le soglie dei criteri di integrità sono configurabili?  
Le soglie per i criteri di integrità per una macchina virtuale Windows non sono modificabili perché i relativi stati di integrità sono impostati su *in esecuzione* o *disponibile*. Quando viene eseguita una query sullo stato di integrità dall'[API di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), per il valore *comparisonOperator* (operatore di confronto) viene visualizzato **LessThan** (minore di) o **GreaterThan** (maggiore di) con un valore *threshold* (soglia) pari a **4** per il servizio o l'entità se:
   - Integrità del servizio client DNS - Il servizio non è in esecuzione. 
   - Integrità del servizio client DHCP - Il servizio non in esecuzione. 
   - Integrità del servizio RPC - Il servizio non è in esecuzione. 
   - Integrità del servizio Windows Firewall - Il servizio non è in esecuzione.
   - Integrità del servizio log di eventi di Windows - Il servizio non è in esecuzione. 
   - Integrità del servizio server - Il servizio non è in esecuzione. 
   - Integrità del servizio Gestione remota Windows - Il servizio non è in esecuzione. 
   - Errore o danneggiamento del file system - Il disco logico non è disponibile.

Le soglie per i criteri di integrità di Linux seguenti non possono essere modificate, perché il relativo stato di integrità è già impostato su *true*. Quando viene eseguita una query sullo stato di integrità dall'API di monitoraggio del carico di lavoro per l'entità in base al contesto, per lo stato di integrità viene visualizzato il valore di *comparisonOperator* (operatore di confronto) **LessThan** (minore di) con un valore *threshold* (soglia) pari a **1**:
   - Stato disco logico - Il disco logico non è online/disponibile
   - Stato disco - Il disco non è online/disponibile
   - Stato scheda di rete - Scheda di rete disabilitata

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Come si modificano gli avvisi che sono inclusi con la funzionalità di integrità?
Le regole di avviso definite per ogni criterio di integrità non sono esposte nel portale di Azure. È possibile abilitare o disabilitare una regola di avviso per l'integrità solo nell'[API di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Inoltre, nel portale di Azure non è possibile assegnare un [gruppo di azioni di Monitoraggio di Azure](../../azure-monitor/platform/action-groups.md) per gli avvisi di integrità. È possibile usare solo l'API di impostazione delle notifiche per configurare un gruppo di azioni in modo che venga attivato ogni volta che viene generato un avviso di integrità. Attualmente è possibile assegnare gruppi di azioni a una macchina virtuale, in modo che tutti gli *avvisi di integrità* generati per la macchina virtuale attivino gli stessi gruppi di azioni. A differenza degli avvisi tradizionali di Azure, non è possibile usare un gruppo di azioni distinto per ogni regola di avviso di integrità. Inoltre, quando vengono generati avvisi di integrità, sono supportati solo i gruppi di azioni configurati per inviare notifiche tramite posta elettronica o SMS. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>I grafici delle prestazioni della macchina virtuale non contengono alcuni dati o non ne contengono affatto
Se non compaiono i dati sulle prestazioni nella tabella del disco o in alcuni grafici delle prestazioni, è possibile che i contatori delle prestazioni non siano stati configurati nell'area di lavoro. Per risolvere il problema, eseguire lo [script di PowerShell](vminsights-enable-at-scale-powershell.md#enable-with-powershell) seguente.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>In cosa differisce la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali da Mapping dei servizi?
La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali si basa su Mapping dei servizi, ma presenta le differenze seguenti:

* È possibile accedere alla vista della mappa dal pannello VM e da Monitoraggio di Azure per le macchine virtuali in Monitoraggio di Azure.
* Le connessioni nella mappa ora sono selezionabili e mostrano i dati delle metriche di connessione nel pannello laterale relativo alla connessione selezionata.
* È disponibile una nuova API che consente di creare le mappe in modo da supportare meglio quelle più complesse.
* Le macchine virtuali monitorate sono ora incluse nel nodo del gruppo client e il grafico ad anello mostra la proporzione tra le macchine virtuali monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Le macchine virtuali monitorate sono ora incluse nei nodi del gruppo di porte server e il grafico ad anello mostra la proporzione tra le macchine monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Lo stile della mappa è stato aggiornato per maggiore coerenza con la mappa delle app di Application Insights.
* I pannelli lato sono stati aggiornati e non è il set completo di integrazione che erano supportate in mapping dei servizi - gestione degli aggiornamenti, rilevamento delle modifiche, sicurezza e Service Desk. 
* L'opzione per la scelta dei gruppi e delle macchine di cui eseguire il mapping è stata aggiornata e ora supporta sottoscrizioni, gruppi di risorse, set di scalabilità delle macchine virtuali di Azure e servizi cloud.
* Non è possibile creare nuovi gruppi di macchine di Mapping dei servizi nella funzionalità Monitoraggio di Azure per le macchine virtuali.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Perché nei grafici delle prestazioni appaiono delle linee tratteggiate?
I motivi possono essere vari.  Nei casi in cui sia presente un vuoto nella raccolta di dati, le linee si presentano tratteggiate.  Se è stata modificata la frequenza di campionamento dei dati per i contatori delle prestazioni abilitati (l'impostazione predefinita prevede la raccolta di dati ogni 60 secondi), è possibile che appaiano delle linee punteggiate nel grafico quando si sceglie un intervallo di tempo ristretto per il grafico e la frequenza di campionamento è inferiore alle dimensioni del bucket usate nel grafico (ad esempio, la frequenza di campionamento è ogni 10 minuti e ogni bucket del grafico è di 5 minuti).  Se si sceglie di visualizzare un intervallo di tempo più ampio, le linee del grafico appariranno continue e non tratteggiate.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>I gruppi sono supportati con Monitoraggio di Azure per le macchine virtuali?
Sì, dopo aver installato Dependency Agent, vengono raccolte informazioni dalle macchine virtuali per visualizzare i gruppi in base alla sottoscrizione, al gruppo di risorse, al set di scalabilità delle macchine virtuali e ai servizi cloud.  Se si usa Mapping dei servizi e sono stati creati gruppi di macchine, vengono visualizzati anch'essi.  Se per l'area di lavoro che si sta esaminando sono stati creati gruppi di computer, verranno visualizzati nel filtro dei gruppi. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Come visualizzare i dettagli alla base della linea del 95° percentile nei grafici delle prestazioni in forma aggregata?
Per impostazione predefinita, l'elenco è ordinato in modo da mostrare le macchine virtuali con il valore più elevato per il 95° percentile in relazione alla metrica selezionata, ad eccezione del grafico della memoria disponibile, che mostra le macchine con il valore più basso per il 5° percentile.  Se si fa clic sul grafico, appare la visualizzazione **elenco delle prime N** con la metrica appropriata selezionata.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>In che modo la funzionalità di mappa gestisce gli indirizzi IP duplicati su reti virtuali e subnet diverse?
Se si duplicano gli intervalli IP con le VM o i set di scalabilità di macchine virtuali di Azure su subnet e reti virtuali, la mappa di Monitoraggio di Azure per le macchine virtuali potrebbe mostrare informazioni non corrette. Si tratta di un problema noto ed è in corso l'analisi delle opzioni per migliorare questa esperienza.

## <a name="does-map-feature-support-ipv6"></a>La funzionalità di mappa supporta il protocollo IPv6?
La funzionalità di mappa supporta attualmente solo il protocollo IPv4 ed è in corso l'analisi del supporto per IPv6. È supportato anche il protocollo IPv4 con a tunneling all'interno di IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Quando si carica una mappa per un gruppo di risorse o un altro gruppo di grandi dimensioni, la mappa è di difficile visualizzazione
Anche se sono stati apportati miglioramenti alla mappa per gestire le configurazioni complesse e di grandi dimensioni, ci rendiamo conto che una mappa può contenere molti nodi, connessioni e nodi che funzionano come cluster.  Microsoft si impegna a migliorare costantemente il supporto per aumentare la scalabilità.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Perché il grafico di rete nella scheda Prestazioni ha un aspetto diverso rispetto al grafico di rete nella pagina di panoramica della VM di Azure?

La pagina di panoramica di una VM di Azure mostra i grafici basati sulla misurazione dell'attività dell'host nella VM guest.  Per il grafico di rete nella panoramica della VM di Azure, viene visualizzato solo il traffico che verrà fatturato  e non è incluso il traffico tra reti virtuali.  I dati e i grafici visualizzati per Monitoraggio di Azure per le macchine virtuali si basano sui dati ottenuti dalla VM guest e il grafico di rete mostra tutto il traffico TCP/IP in ingresso e in uscita verso tale VM, incluso quello tra reti virtuali.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Come viene misurato il tempo di risposta per i dati archiviati in VMConnection e visualizzato nel Pannello di connessione e le cartelle di lavoro?

Tempo di risposta è un'approssimazione. Poiché non abbiamo instrumentare il codice dell'applicazione, non realmente conosciamo quando inizia una richiesta e all'arrivo della risposta. In alternativa è osservare dati inviati su una connessione e quindi i dati restituiti in tale connessione. Agente tiene traccia di queste Invia e riceve e tenta di associarle: una sequenza delle trasmissioni, seguita da una sequenza di riceve viene interpretato come una coppia richiesta-risposta. L'intervallo tra queste operazioni è il tempo di risposta. Includerà la latenza di rete e il tempo di elaborazione server.

Questa approssimazione funziona bene per i protocolli basati su richiesta/risposta: una singola richiesta viene trasmessa nella connessione e un'unica risposta arriva. Questo è il caso per HTTP (S) (senza l'utilizzo di pipeline), ma non soddisfatta per altri protocolli.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Sono previste limitazioni se è in vigore il piano tariffario Gratuito di Log Analytics?
Se Monitoraggio di Azure è stato configurato con un'area di lavoro Log Analytics che usa il piano tariffario *Gratuito*, la funzionalità della mappa Monitoraggio di Azure per le macchine virtuali supporterà solo cinque computer connessi all'area di lavoro. Se si hanno cinque macchine virtuali connesse a un'area di lavoro gratuita, si disconnette una delle macchine virtuali e in un secondo momento si connette una nuova macchina virtuale, la nuova macchina virtuale non viene monitorata e riflessa nella pagina della mappa.  

In questo caso, verrà visualizzata l'opzione **Prova adesso** quando si apre la macchina virtuale e si seleziona **Insights (preview)** (Insights - Anteprima) nel riquadro a sinistra, anche dopo che è già stato installato nella macchina virtuale.  Non vengono tuttavia visualizzate le normali opzioni come avverrebbe normalmente se non fosse stato eseguito l'onboarding di questa macchina virtuale in Monitoraggio di Azure per le macchine virtuali. 

## <a name="next-steps"></a>Passaggi successivi
Revisione [abilitare il monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md) sui requisiti e i metodi per abilitare il monitoraggio delle macchine virtuali.

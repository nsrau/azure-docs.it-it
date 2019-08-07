---
title: Domande frequenti - Soluzione Monitoraggio prestazioni rete in Azure | Microsoft Docs
description: Questo articolo acquisisce le domande frequenti su Monitoraggio prestazioni rete in Azure. Monitoraggio prestazioni rete (NPM) consente di monitorare le prestazioni delle reti quasi in tempo reale e rilevare e individuare i colli di bottiglia delle prestazioni di rete.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinigam
ms.openlocfilehash: b3274c214aa60c930e62e651af960d5f01cbdd20
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782119"
---
# <a name="network-performance-monitor-solution-faq"></a>Domande frequenti sulla soluzione Monitoraggio prestazioni rete in Azure

![Simbolo di Monitoraggio prestazioni rete](media/network-performance-monitor-faq/npm-symbol.png)

Questo articolo presenta le domande frequenti sulla soluzione Monitoraggio prestazioni rete in Azure.

[Monitoraggio prestazioni rete](/azure/networking/network-monitoring-overview) è una soluzione di [monitoraggio delle reti ibrida](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) basata sul cloud che consente di monitorare le prestazioni di rete tra vari punti dell'infrastruttura di rete. Con questa soluzione è anche possibile monitorare la connettività di rete agli [endpoint di servizio e applicazione](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md), nonché [monitorare le prestazioni di Azure ExpressRoute](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

Monitoraggio prestazioni rete rileva i problemi di rete come i buchi neri del traffico, gli errori di routing e i problemi che i metodi di monitoraggio delle reti tradizionali non sono in grado di rilevare. La soluzione genera avvisi e invia notifiche quando viene superata una soglia per un collegamento di rete. Garantisce anche una tempestiva individuazione dei problemi legati alle prestazioni di rete e localizza l'origine del problema in un dispositivo o segmento di rete specifico. 

Ulteriori informazioni sulle diverse funzionalità supportate da [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/networking/network-monitoring-overview) sono disponibili online.

## <a name="set-up-and-configure-agents"></a>Installare e configurare agenti

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quali sono i requisiti di piattaforma per i nodi da usare per il monitoraggio tramite Monitoraggio prestazioni rete?
Di seguito sono riportati i requisiti di piattaforma per le varie funzionalità di Monitoraggio prestazioni rete:

- Le funzionalità di monitoraggio delle prestazioni e della connettività del servizio di NPM supportano i sistemi operativi Windows Server e Windows/client. Le versioni del sistema operativo Windows Server supportate sono 2008 SP1 o versioni successive. I desktop Windows/versioni client supportate sono Windows 10, Windows 8.1, Windows 8 e Windows 7. 
- La funzionalità Monitoraggio di ExpressRoute di Monitoraggio prestazioni rete supporta solo il sistema operativo Windows Server (2008 SP1 o versione successiva).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>È possibile usare computer Linux come nodi di monitoraggio in Monitoraggio prestazioni rete?
La funzionalità di monitoraggio delle reti tramite nodi basati su Linux è attualmente in anteprima. Per altre informazioni, contattare il proprio account manager. Gli agenti Linux offrono il monitoraggio solo per la funzionalità Performance Monitor di Monitoraggio prestazioni rete e non sono disponibili per le funzionalità Monitoraggio connettività servizio e Monitoraggio di ExpressRoute.

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Quali sono i requisiti di dimensione per i nodi da usare per il monitoraggio tramite Monitoraggio prestazioni rete?
Per eseguire la soluzione Monitoraggio prestazioni rete su VM nodo per monitorare le reti, i nodi devono avere almeno 500 MB di memoria e un core. Non è necessario usare nodi separati per l'esecuzione di NPM. La soluzione può essere eseguita su nodi sui quali sono in esecuzione altri carichi di lavoro. La soluzione è in grado di arrestare il processo di monitoraggio se utilizza più del 5% di CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Per usare Monitoraggio prestazioni rete è necessario connettersi ai nodi come agente diretto o tramite System Center Operations Manager?
Le funzionalità di monitoraggio delle prestazioni e della connettività del servizio supportano sia i nodi [connessi come agenti diretti](../../azure-monitor/platform/agent-windows.md) che quelli [connessi tramite Operations Manager](../../azure-monitor/platform/om-agents.md).

Per la funzionalità Monitoraggio di ExpressRoute, i nodi di Azure devono essere connessi solo come agenti diretti. Quelli connessi tramite Operations Manager non sono supportati. Per i nodi locali, i nodi connessi come agenti diretti e tramite Operations Manager sono supportati per il monitoraggio di un circuito ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Quale protocollo, tra TCP e ICMP, deve essere selezionato per il monitoraggio?
Se si sta monitorando la rete con i nodi basati su Windows Server, è consigliabile usare TCP come protocollo di monitoraggio, perché fornisce un'accuratezza migliore. 

Il protocollo ICMP è consigliato per i nodi basati su sistema operativo desktop/client Windows. Questa piattaforma does consente l'invio dei dati TCP su socket non elaborati, che NPM USA per individuare la topologia di rete.

È possibile ottenere [qui](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol) altre informazioni sui vantaggi dei vari protocolli.

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Come è possibile configurare un nodo per supportare il monitoraggio tramite il protocollo TCP?
Per configurare un nodo per supportare il monitoraggio tramite il protocollo TCP: 
* Assicurarsi che la piattaforma del nodo sia Windows Server (2008 SP1 o versione successiva).
* Eseguire lo script [EnableRules.ps1](https://aka.ms/npmpowershellscript) di PowerShell sul nodo. Per altre informazioni, vedere queste [istruzioni](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring).


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Come è possibile modificare la porta TCP usata da Monitoraggio prestazioni rete per il monitoraggio?
Per modificare la porta TCP usata da Monitoraggio prestazioni rete per il monitoraggio, è possibile eseguire lo script [EnableRules.ps1](https://aka.ms/npmpowershellscript). È necessario immettere il numero di porta che si intende usare come parametro. Ad esempio, per abilitare TCP sulla porta 8060, eseguire `EnableRules.ps1 8060`. Assicurarsi di usare la stessa porta TCP su tutti i nodi usati per il monitoraggio.

Lo script configura Windows Firewall solo in locale. Se sono definite regole del firewall di rete o del gruppo di sicurezza di rete, assicurarsi che sia consentito il traffico destinato alla porta TCP usata da Monitoraggio prestazioni rete.

### <a name="how-many-agents-should-i-use"></a>Quanti agenti è consigliabile usare?
È consigliabile usare almeno un agente per ogni subnet che si vuole monitorare.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Qual è il numero massimo di agenti che è possibile usare o viene visualizzato l'errore ".... è stato raggiunto il limite di configurazione?
NPM limita il numero di indirizzi IP a 5000 IP per ogni area di lavoro. Se un nodo dispone sia di indirizzi IPv4 che IPv6, questo vale come 2 indirizzi IP per tale nodo. Di conseguenza, il limite di 5000 IP stabilisce il limite massimo del numero di agenti. È possibile eliminare gli agenti inattivi dalla scheda Nodi in NPM >> Configura. NPM gestisce anche la cronologia di tutti gli indirizzi IP che sono stati assegnati alla macchina virtuale che ospita l'agente e ognuno viene conteggiato come IP separato, contribuendo a tale limite superiore di 5000 IP. Per liberare gli IP per l'area di lavoro, è possibile usare la pagina nodi per eliminare gli IP non in uso.

## <a name="monitoring"></a>Monitoraggio

### <a name="how-are-loss-and-latency-calculated"></a>Come vengono calcolate la perdita e la latenza?
Gli agenti di origine inviano richieste SYN TCP (se è selezionato TCP come protocollo per il monitoraggio) o ECHO ICMP (se invece è selezionato ICMP) all'indirizzo IP di destinazione a intervalli regolari per verificare che tutti i percorsi per la combinazione di IP di origine e destinazione siano coperti. Per calcolare la perdita e la latenza di ogni percorso viene misurata la percentuale di pacchetti ricevuti e di tempo di round trip dei pacchetti. I dati vengono aggregati per l'intervallo di polling e per tutti i percorsi in modo da ottenere i valori complessivi di perdita e latenza per la combinazione di IP per lo specifico intervallo di polling.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Con quale frequenza l'agente di origine invia pacchetti alla destinazione per il monitoraggio?
Per le funzionalità Performance Monitor e Monitoraggio di ExpressRoute, l'origine invia pacchetti ogni 5 secondi e registra le misure di rete. Questi dati vengono aggregati in un intervallo di polling di 3 minuti per calcolare i valori medi e di picco per la perdita e la latenza. Per la funzionalità Monitoraggio connettività servizio, la frequenza di invio dei pacchetti per la misura di rete è determinata dalla frequenza immessa dall'utente per il test specifico durante la configurazione del test.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Quanti pacchetti vengono inviati per il monitoraggio?
Il numero di pacchetti inviati dall'agente di origine alla destinazione in un polling è adattivo e viene stabilito da un algoritmo proprietario Microsoft, che può essere diverso per le varie topologie di rete. Quanto maggiore è il numero di percorsi di rete per la combinazione di IP di origine e destinazione, tanto più elevato sarà il numero di pacchetti inviati. Il sistema garantisce che tutti i percorsi per la combinazione di IP di origine e destinazione siano coperti.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>In che modo Monitoraggio prestazioni rete individua la topologia di rete tra origine e destinazione?
Monitoraggio prestazioni rete usa un algoritmo proprietario basato Traceroute per individuare tutti i percorsi e gli hop tra origine e destinazione.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Monitoraggio prestazioni rete fornisce informazioni a livello di routing e commutazione? 
Anche se Monitoraggio prestazioni rete rileva tutte le route possibili tra l'agente di origine e la destinazione, non fornisce informazioni riguardo alla route che seguono i pacchetti inviati da carichi di lavoro specifici. La soluzione consente di identificare i percorsi e gli hop di rete sottostanti, che aggiungono maggiore latenza rispetto a quanto previsto.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Per quale motivo alcuni percorsi non risultano integri?
Tra gli IP di origine e destinazione possono esistere diversi percorsi e ogni percorso può avere un valore diverso di perdita e latenza. Monitoraggio prestazioni rete contrassegna tali percorsi come non integri (evidenziandoli con il colore rosso) quando per tali percorsi i valori di perdita e/o latenza superano la rispettiva soglia impostata nella configurazione di monitoraggio.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Che cosa indica un hop di colore rosso nella mappa della topologia di rete?
Se un hop è rosso, significa che fa parte di almeno un percorso non integro. Monitoraggio prestazioni rete contrassegna solo i percorsi come non integri, non separa i percorsi in base allo stato di integrità. Per identificare gli hop che presentano problemi, è possibile visualizzare la latenza per ogni singolo hop e separare quelli che aggiungono più latenza del previsto.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Come funziona l'individuazione degli errori in Performance Monitor?
Monitoraggio prestazioni rete adotta un meccanismo probabilistico per assegnare le probabilità di errore a ogni percorso e segmento di rete e agli hop di rete in base al numero di percorsi non integri di cui fanno parte. Quando i segmenti e gli hop di rete diventano parte di un numero maggiore di percorsi non integri, la probabilità di errore associata aumenta. Questo algoritmo funziona in modo ottimale quando sono presenti molti nodi con agente di Monitoraggio prestazioni rete connessi tra loro poiché in questo modo aumenta il numero di punti dati per calcolare le probabilità di errore.

### <a name="how-can-i-create-alerts-in-npm"></a>Come è possibile creare avvisi in Monitoraggio prestazioni rete?
Per istruzioni dettagliate, vedere la [sezione relativa agli avvisi nella documentazione](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts).

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Monitoraggio prestazioni rete può eseguire il monitoraggio di router e server come singoli dispositivi?
Monitoraggio prestazioni rete identifica solo l'indirizzo IP e il nome host degli hop di rete sottostanti (commutatori, router, server e così via) tra gli IP di origine e di destinazione. Rileva inoltre la latenza tra questi hop identificati. Non esegue il monitoraggio dei singoli hop sottostanti.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Monitoraggio prestazioni rete può essere usato per monitorare la connettività di rete tra Azure e AWS?
Sì. Per informazioni, vedere l'articolo [Monitor Azure, AWS, and on-premises networks using NPM](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) (Monitorare Azure, AWS e le reti locali usando Monitoraggio prestazioni rete).

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Il valore di utilizzo della larghezza di banda di ExpressRoute riguarda la larghezza di banda in ingresso o quella in uscita?
Il valore di utilizzo della larghezza di banda corrisponde al totale della larghezza di banda in ingresso e in uscita. Il valore è espresso in bit al secondo.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>È possibile ottenere informazioni sulla larghezza di banda in ingresso e in uscita per ExpressRoute?
È possibile acquisire i valori in ingresso e in uscita sia per la larghezza di banda primaria sia per quella secondaria.

Per informazioni a livello di peering, usare la query seguente in Ricerca log

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Per informazioni a livello di circuito, usare la query seguente 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Quali aree sono supportate per la funzionalità Performance Monitor di Monitoraggio prestazioni rete?
Monitoraggio prestazioni rete può monitorare la connettività tra reti in qualsiasi parte del mondo, da un'area di lavoro ospitata in una delle [aree supportate](../../azure-monitor/insights/network-performance-monitor.md#supported-regions).

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Quali aree sono supportate per la funzionalità Monitoraggio connettività servizio di Monitoraggio prestazioni rete?
Monitoraggio prestazioni rete può monitorare la connettività ai servizi in qualsiasi parte del mondo, da un'area di lavoro ospitata in una delle [aree supportate](../../azure-monitor/insights/network-performance-monitor.md#supported-regions).

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Quali aree sono supportate per la funzionalità Monitoraggio di ExpressRoute di Monitoraggio prestazioni rete?
Monitoraggio prestazioni rete può monitorare i circuiti ExpressRoute presenti in qualsiasi area di Azure. Per eseguire l'onboarding a Monitoraggio prestazioni rete, è necessaria un'area di lavoro Log Analytics ospitata in una delle [aree supportate](/azure/expressroute/how-to-npm).

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Per quale motivo alcuni hop sono contrassegnati come non identificati nella visualizzazione della topologia di rete?
Monitoraggio prestazioni rete usa una versione modificata di traceroute per individuare la topologia dall'agente di origine alla destinazione. Un hop non identificato indica che l'hop di rete non ha risposto alla richiesta traceroute dell'agente di origine. Se tre hop di rete consecutivi non rispondono al traceroute dell'agente, la soluzione contrassegna gli hop che non rispondono come non identificati e non tenta di individuare più hop.

Un hop può non rispondere a una richiesta traceroute in almeno uno degli scenari seguenti:

* I router sono stati configurati in modo da non rivelare la propria identità.
* I dispositivi di rete non consentano il traffico ICMP_TTL_EXCEEDED.
* Un firewall sta bloccando la risposta ICMP_TTL_EXCEEDED dal dispositivo di rete.

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy-"></a>Ricevo avvisi per i test non integri, ma i valori elevati non sono visualizzati nel grafico della perdita e della latenza di NPM. Ricerca per categorie verificare che cos'è non integro?
NPM genera un avviso se la latenza end-to-end tra l'origine e la destinazione attraversa la soglia per qualsiasi percorso tra di essi. Alcune reti hanno più percorsi che connettono la stessa origine e la stessa destinazione. NPM genera un avviso se il percorso non è integro. La perdita e la latenza visualizzate nei grafici sono il valore medio per tutti i percorsi, quindi è possibile che non venga visualizzato il valore esatto di un singolo percorso. Per capire dove è stata superata la soglia, cercare la colonna "sottotipo" nell'avviso. Se il problema è causato da un percorso, il valore del sottotipo sarà NetworkPath (per i test di performance monitor), EndpointPath (per i test di monitoraggio della connettività del servizio) e ExpressRoutePath (per i test di monitoraggio di Expressroute). 

La query di esempio da trovare è il percorso non è integro:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Perché il test mostra non integro, ma la topologia non 
NPM monitora la perdita, la latenza e la topologia end-to-end a intervalli diversi. La perdita e la latenza vengono misurate una volta ogni 5 secondi e vengono aggregate ogni tre minuti (per performance monitor e Express Route monitor), mentre la topologia viene calcolata usando traceroute una volta ogni 10 minuti. Ad esempio, tra 3:44 e 4:04, la topologia può essere aggiornata tre volte (3:44, 3:54, 4:04), ma la perdita e la latenza vengono aggiornate circa sette volte (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Viene eseguito il rendering della topologia generata alle 3:54 per la perdita e la latenza calcolata a 3:56, 3:59 e 4:02. Si supponga di ricevere un avviso per segnalare che il circuito ER non era integro a 3:59. Accedere a NPM e provare a impostare il tempo della topologia su 3:59. NPM eseguirà il rendering della topologia generata alle 3:54. Per comprendere l'ultima topologia nota della rete, confrontare i campi TimeProcessed (tempo in cui è stata calcolata la perdita e la latenza) e TracerouteCompletedTime (ora in cui è stata calcolata la topologia). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Qual è la differenza tra i campi E2EMedianLatency e AvgHopLatencyList nella tabella NetworkMonitoring
E2EMedianLatency è la latenza aggiornata ogni tre minuti dopo aver aggregato i risultati dei test di ping TCP, mentre AvgHopLatencyList viene aggiornato ogni 10 minuti in base al traceroute. Per comprendere l'ora esatta in cui è stato calcolato E2EMedianLatency, utilizzare il campo TimeProcessed. Per comprendere l'ora esatta in cui traceroute ha completato e aggiornato AvgHopLatencyList, usare il campo TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Perché i numeri di latenza hop per hop differiscono da HopLatencyValues 
HopLatencyValues sono da origine a endpoint.
Di seguito è riportato un esempio: Hop-A, B, C. AvgHopLatency-10, 15, 20. Ciò significa che l'origine è una latenza = 10, la latenza da origine a B = 15 e la latenza di origine a C è 20. L'interfaccia utente calcolerà la latenza hop A-B come 5 nella topologia

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Per quale motivo la soluzione mostra una perdita del 100%, ma è presente connettività tra l'origine e la destinazione?
Questa situazione può verificarsi se il firewall dell'host o il firewall intermedio (firewall di rete o gruppo di sicurezza di rete di Azure) blocca le comunicazioni tra l'agente di origine e la destinazione attraverso la porta usata da Monitoraggio prestazioni rete per il monitoraggio. Per impostazione predefinita, viene usata la porta 8084, a meno che il cliente non abbia modificato l'impostazione.

* Per verificare che il firewall dell'host non blocchi le comunicazioni sulla porta richiesta, visualizzare lo stato di integrità dei nodi di origine e di destinazione dalla visualizzazione seguente: Monitoraggio prestazioni rete -> Configurazione -> Nodi. 
  Se i nodi non sono integri, visualizzare le istruzioni e adottare le misure correttive. Se i nodi sono integri, proseguire con il passaggio b desiderato.
* Per verificare che un firewall di rete intermedio o un gruppo di sicurezza di rete di Azure non blocchi le comunicazioni sulla porta richiesta, eseguire l'utilità PsPing di terze parti usando le istruzioni seguenti:
  * L'utilità PsPing è disponibile [qui](https://technet.microsoft.com/sysinternals/psping.aspx) per il download. 
  * Eseguire il comando seguente dal nodo di origine.
    * psping -n 15 \<IndirizzoIP nodo destinazione\>:portNumber Per impostazione predefinita, Monitoraggio prestazioni rete usa la porta 8084. Se si è esplicitamente cambiato il numero di porta tramite lo script EnableRules.ps1, immettere il numero di porta impostato. Questo è un ping dal computer di Azure al sistema locale.
* Controllare se i ping hanno esito positivo. Se l'esito è negativo, significa che il traffico su questa porta viene bloccato da un firewall di rete intermedio o da un gruppo di sicurezza di rete di Azure.
* A questo punto, eseguire il comando dal nodo di destinazione all'indirizzo IP del nodo di origine.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Si verifica una perdita dal nodo A al nodo B, ma non viceversa. Perché?
I percorsi di rete tra il nodo A e il B possono essere diversi da quelli tra il nodo B e l'A ed è quindi possibile osservare valori diversi per la perdita e la latenza.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Per quale motivo non tutti i circuiti e le connessioni di peering di ExpressRoute vengono individuati?
NPM individua ora i circuiti ExpressRoute e le connessioni di peering in tutte le sottoscrizioni a cui l'utente ha accesso. Scegliere tutte le sottoscrizioni in cui sono collegate risorse di ExpressRoute e abilitare il monitoraggio per ogni risorsa individuata. NPM cerca gli oggetti di connessione durante l'individuazione di un peering privato, verificare quindi se il peering è associato a una rete virtuale.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>La funzionalità Monitoraggio di ExpressRoute visualizza un messaggio di diagnostica per segnalare che il traffico non passa attraverso alcun circuito. Che cosa significa?

Può verificarsi uno scenario in cui è presente una connessione integra tra il nodo locale e quello di Azure, ma il traffico non passa attraverso il circuito ExpressRoute configurato per il monitoraggio tramite Monitoraggio prestazioni rete. 

Ciò avviene se:

* Il circuito di ExpressRoute è inattivo.
* I filtri di route sono configurati in modo da dare priorità ad altre route, ad esempio una connessione VPN o un altro circuito ExpressRoute, attraverso il circuito ExpressRoute previsto. 
* Non è presente connettività attraverso il circuito ExpressRoute previsto tra il nodo locale e quello di Azure selezionati per il monitoraggio del circuito ExpressRoute nella configurazione di monitoraggio. Assicurarsi di aver scelto i nodi corretti tra cui è presente connettività attraverso il circuito ExpressRoute che si intende monitorare.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Per quale motivo, quando si configura il monitoraggio del circuito ExpressRoute, i nodi di Azure non vengano rilevati?
Questo problema può verificarsi se i nodi di Azure sono connessi tramite Operations Manager. La funzionalità Monitoraggio di ExpressRoute supporta solo i nodi di Azure connessi come agenti diretti.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Perché non è possibile individuare i circuiti ExpressRoute nel portale di OMS?
Anche se Monitoraggio prestazioni rete può essere usato sia dal portale di Azure che dal portale di OMS, l'individuazione dei circuiti della funzionalità Monitoraggio di ExpressRoute può essere eseguita solo tramite il portale di Azure. Dopo che i circuiti sono stati individuati tramite il portale di Azure, è possibile usare la funzionalità in uno dei due portali. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>Per quale motivo, nella funzionalità Monitoraggio connettività servizio, il tempo di risposta del servizio, la perdita di rete e la latenza risultano non disponibili?
Questa situazione può verificarsi se almeno una di queste condizioni è vera:

* Il servizio è inattivo.
* Il nodo usato per controllare la connettività di rete al servizio è inattivo.
* La destinazione immessa nella configurazione di test non è corretta.
* Il nodo non ha connettività di rete.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>Per quale motivo, nella funzionalità Monitoraggio connettività servizio, viene visualizzato un tempo di risposta del servizio valido, ma la perdita di rete e la latenza risultano non disponibili?
 Questa situazione può verificarsi se almeno una di queste condizioni è vera:

* Se il nodo usato per controllare la connettività di rete al servizio è un computer client Windows, il servizio di destinazione sta bloccando le richieste ICMP o un firewall di rete sta bloccando le richieste ICMP provenienti dal nodo.
* La casella di controllo Esegui misure di rete è stata deselezionata nella configurazione di test.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>Per quale motivo, nella funzionalità Monitoraggio connettività servizio, il tempo di risposta del servizio non è disponibile, ma i valori di perdita di rete e latenza sono validi?
Questa situazione può verificarsi se il servizio di destinazione non è un'applicazione Web, ma il test è configurato come test Web. Modificare la configurazione di test e scegliere il tipo di test Rete invece di Web.

## <a name="miscellaneous"></a>Miscellaneous

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Si può riscontrare un impatto sulle prestazioni del nodo usato per il monitoraggio?
Monitoraggio prestazioni rete è configurato in modo da arrestare il processo di monitoraggio se utilizza più del 5% delle risorse della CPU dell'host. Ciò consente di assicurarsi di poter usare i nodi per i normali carichi di lavoro senza alcun impatto sulle prestazioni.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Monitoraggio prestazioni rete modifica le regole del firewall per il monitoraggio?
Monitoraggio prestazioni rete crea soltanto una regola di Windows Firewall locale nei nodi su cui viene eseguito lo script EnableRules.ps1 di PowerShell per consentire agli agenti di stabilire tra loro connessioni TCP sulla porta specificata. La soluzione non modifica alcuna regola del firewall di rete o del gruppo di sicurezza di rete.

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Come si può verificare l'integrità dei nodi usati per il monitoraggio?
È possibile esaminare lo stato di integrità dei nodi usati per il monitoraggio tramite la visualizzazione seguente: Monitoraggio prestazioni rete -> Configurazione -> Nodi. Se un nodo non è integro, è possibile visualizzare i dettagli dell'errore ed eseguire l'azione suggerita.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Monitoraggio prestazioni rete segnala i valori di latenza in microsecondi?
Monitoraggio prestazioni rete arrotonda in millisecondi i valori di latenza visualizzati nell'interfaccia utente. Gli stessi dati vengono archiviati con un livello di granularità maggiore (talvolta fino a quattro cifre decimali).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su Monitoraggio prestazioni rete sono disponibili in [Soluzione Monitoraggio prestazioni rete in Azure](../../azure-monitor/insights/network-performance-monitor.md).

<properties
   pageTitle="Elenco di controllo della disponibilità elevata | Microsoft Azure"
   description="Rapido elenco di controllo di impostazioni e azioni che è possibile usare per migliorare la disponibilità delle applicazioni con Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#Elenco di controllo per la disponibilità elevata
Uno dei principali vantaggi dell'uso di Azure è la possibilità di aumentare la disponibilità e la scalabilità delle applicazioni con l'aiuto del cloud. Per trarre il massimo da queste opzioni, l'elenco di controllo riportato di seguito offre alcune nozioni di base dell'infrastruttura per garantire che le applicazioni siano resilienti.

>[AZURE.NOTE] La maggior parte dei suggerimenti seguenti possono essere implementati in qualsiasi momento nell'applicazione e sono quindi ideali per "correzioni rapide". La soluzione migliore a lungo termine spesso comporta la progettazione di un'applicazione compilata per il cloud. Per un elenco di controllo di queste aree più orientate alla progettazione, vedere [Elenco di controllo della disponibilità](../best-practices-availability-checklist.md).

###Si usa Gestione traffico per le risorse?
Gestione traffico consente di instradare il traffico Internet tra aree di Azure o tra Azure e percorsi locali. Questa operazione può essere opportuna per diversi motivi, tra cui aspetti di latenza e disponibilità. Per altre informazioni su come usare Gestione traffico per aumentare la resilienza e distribuire il traffico su più aree, vedere [Gestione delle VM in più data center in Azure per assicurare disponibilità elevata](../guidance/guidance-compute-multiple-datacenters.md).

__Cosa accade se non si usa Gestione traffico?__ Se non si usa Gestione traffico davanti all'applicazione, le risorse saranno limitate a una sola area. Ciò limita la scalabilità, aumenta la latenza per gli utenti che non si trovano in prossimità dell'area scelta e riduce la protezione in caso di interruzione del servizio a livello di area.

###È stato evitato l'uso di una singola macchina virtuale per un ruolo?
Una buona progettazione evita i singoli punti di errore. Questo aspetto è importante nella progettazione di tutti i servizi, locali e cloud, ma è particolarmente utile nel cloud perché consente di incrementare la scalabilità e la resilienza aumentando le istanze, ovvero aggiungendo macchine virtuali, anziché aumentare le prestazioni, ovvero usando una macchina virtuale più potente. Per altre informazioni sulla progettazione di applicazioni scalabili, vedere [Disponibilità elevata per le applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).

__Cosa accade se è presente una singola macchina virtuale per un ruolo?__ Un singolo computer è un singolo punto di errore e non è disponibile per il [Contratto di servizio per le macchine virtuali Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/). Nei casi migliori, l'applicazione viene eseguita correttamente, ma non si tratta di una progettazione resiliente, non è coperta dal Contratto di servizio di Azure Virtual Machine e i singoli punti di errore aumentano il rischio di tempi di inattività in caso di errore.

###Viene usato un servizio di bilanciamento del carico davanti alle macchine virtuali con connessione Internet dell'applicazione?
I servizi di bilanciamento del carico consentono di distribuire il traffico in ingresso per l'applicazione su un numero arbitrario di macchine. È possibile aggiungere e rimuovere macchine dal servizio di bilanciamento del carico in qualsiasi momento; ciò funziona bene con le macchine virtuali, e con la scalabilità automatica con i set di scalabilità di macchine virtuali, per gestire facilmente un aumento del traffico o errori delle macchine virtuali. Per altre informazioni sui servizi di bilanciamento del carico,vedere [Panoramica di Azure Load Balancer](../load-balancer/load-balancer-overview.md) ed [Esecuzione di più VM in Azure per la scalabilità e la disponibilità](../guidance/guidance-compute-multi-vm.md).

__Cosa accade se non si usa un servizio di bilanciamento del carico davanti alle macchine virtuali con connessione Internet?__ Senza un servizio di bilanciamento del carico non sarà possibile aumentare il numero di istanze, ovvero aggiungere macchine virtuali, e sarà possibile solo aumentare le prestazioni, ovvero incrementare le dimensioni della macchina virtuale con connessione Internet. Quella macchina virtuale costituirà anche un singolo punto di errore. Sarà anche necessario scrivere codice DNS per rilevare l'eventuale perdita di una macchina con connessione Internet e mappare di nuovo la voce DNS sulla nuova macchina sostitutiva avviata.

###Vengono usati set di disponibilità per i server applicazioni e Web senza stato?
L'inserimento delle macchine nello stesso livello ain un set di disponibilità rende le VM idonee per il contratto di servizio per macchine virtuali di Azure. L'inserimento in un set di disponibilità garantisce anche che le macchine vengano inserite in domini di aggiornamento diversi, ovvero diverse macchine host con applicazione di patch in momenti diversi, e in domini di errore, ovvero macchine host che condividono alimentazione elettrica e commutatore di rete. Se non si trovano in un set di disponibilità, le macchine virtuali possono trovarsi sullo stesso computer host e potrebbe quindi esistere un singolo punto di errore non visibile all'utente. Per altre informazioni sull'aumento della disponibilità delle macchine virtuali con i set di disponibilità, vedere [Gestire la disponibilità delle macchine virtuali](../virtual-machines/virtual-machines-windows-manage-availability.md).

__Cosa accade se non si usa un set di disponibilità per i server applicazioni e Web senza stato?__ Non si usa un set di disponibilità non sarà possibile sfruttare il contratto di servizio per macchine virtuali di Azure. Le macchine in quel livello dell'applicazione possono anche passare tutte alla modalità offline in caso di aggiornamento del computer host, ovvero del computer che ospita le macchine virtuali in uso, o se si verifica un comune errore hardware.

###Vengono usati set di scalabilità di macchine virtuali per i server applicazioni e Web senza stato?
Una buona progettazione scalabile e resiliente usa i set di scalabilità di macchine virtuali per garantire che sia possibile aumentare/ridurre il numero di macchine in un livello dell'applicazione, ad esempio il livello web. Il set di scalabilità di macchine virtuali consente di definire la scalabilità dei livelli dell'applicazione, ovvero l'aggiunta o la rimozione di server a seconda dei criteri scelti. Per altre informazioni su come usare i set di scalabilità di macchine virtuali di Azure per aumentare la resilienza ai picchi di traffico, vedere [Panoramica dei set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

__Cosa accade se non si usa un set di scalabilità di macchine virtuali per i server applicazioni e Web senza stato?__ Senza un set di scalabilità di macchine virtuali si limita la scalabilità e l'ottimizzazione dell'uso delle risorse. Una progettazione senza set di scalabilità di macchine virtuali ha un limite di scalabilità superiore che dovrà essere gestito con codice aggiuntivo o manualmente. L'assenza di un set di scalabilità di macchine virtuali implica anche che l'applicazione non può aggiungere e rimuovere facilmente macchine, indipendentemente dalla scalabilità, per gestire picchi di traffico elevati, ad esempio durante una promozione o nel caso in cui il sito, l'app o il prodotto diventi virale.

###Si usano Archiviazione Premium e account di archiviazione separati per ogni macchina virtuale?
È preferibile usare Archiviazione Premium per le macchine virtuali di produzione. È anche opportuno usare un account di archiviazione separato per ogni macchina virtuale. Ciò vale per le distribuzioni di piccole dimensioni. Per distribuzioni estese è possibile riusare gli account di archiviazione per più macchine, ma è necessario provvedere al bilanciamento tra domini di aggiornamento e livelli dell'applicazione. Per altre informazioni sulle prestazioni di Archiviazione di Azure e la scalabilità, vedere [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage/storage-performance-checklist.md).

__Cosa accade se non si usano account di archiviazione separati per ogni macchina virtuale?__ Come molte altre risorse, un account di archiviazione è un singolo punto di errore. Anche se esistono numerose funzionalità di protezione e resilienza in Archiviazione di Azure, un singolo punto di errore non è mai indice di una buona progettazione. Se ad esempio i diritti di accesso sono danneggiati per quell'account, viene raggiunto un limite di archiviazione oppure un limite di [operazioni di I/O](../azure-subscription-service-limits.md#virtual-machine-disk-limits) al secondo, tutte le macchine virtuali con tale account di archiviazione saranno interessate da questi eventi. Se si verifica un'interruzione del servizio che interessa uno stamp di archiviazione che include quel particolare account di archiviazione, il problema potrebbe ripercuotersi su più macchine virtuali.

###Si usa un servizio di bilanciamento del carico o una coda tra ogni livello dell'applicazione?
L'uso di servizi di bilanciamento del carico o code tra ogni livello dell'applicazione consente la scalabilità di ogni livello dell'applicazione in modo semplice e indipendente. È necessario scegliere tra queste tecnologie in base alle esigenze di latenza, complessità e distribuzione, ovvero all'estensione della distribuzione dell'app. Le code tendono in generale ad avere latenza e complessità maggiori, ma offrono più resilienza e consentono una distribuzione più estesa dell'applicazione, ad esempio su più aree. Per altre informazioni su come usare i servizi di bilanciamento del carico interni o le code, vedere [Panoramica del bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md) e [Analogie e differenze tra le code di Azure e le code del bus di servizio](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

__Cosa accade se non si usa un servizio di bilanciamento del carico o una coda tra ogni livello dell'applicazione?__ Senza un servizio di bilanciamento del carico o una coda tra ogni livello dell'applicazione è difficile aumentare le prestazioni o le istanze dell'applicazione e distribuirne il carico tra più macchine. La conseguenza può essere un provisioning eccessivo o insufficiente delle risorse, con il rischio di tempi di inattività o esperienze utente di scarsa qualità, in caso di variazioni impreviste del traffico o errori di sistema.
 
###I database SQL usano la replica geografica attiva? 
La replica geografica attiva consente di configurare fino a 4 database secondari accessibili in lettura nella stessa area o in aree diverse. I database secondari sono disponibili in caso di interruzione del servizio o di impossibilità di connettersi al database primario. Per altre informazioni sulla replica geografica attiva dei database SQL, vedere [Panoramica: Replica geografica attiva per il database SQL](../sql-database/sql-database-geo-replication-overview.md).
 
 __Cosa accade se non si usa la replica geografica attiva con i database SQL?__ Senza replica geografica attiva, se il database primario va offline per manutenzione pianificata, interruzioni del servizio, un errore hardware e così via, il database dell'applicazione sarà offline fino a quando non sarà possibile riportarlo online in uno stato integro.
 
###Viene usata una cache (Cache Redis di Azure) davanti ai database?
Se l'applicazione presenta un carico di database elevato in cui la maggior parte delle chiamate di database sono operazioni di lettura, è possibile aumentare la velocità dell'applicazione e ridurre il carico sul database implementando un livello di caching davanti al database per l'offload di queste operazioni di lettura. È possibile aumentare la velocità dell'applicazione e ridurre il carico del database, aumentando così la scalabilità gestibile, inserendo un livello di caching davanti al database. Per altre informazioni sulla cache Redis di Azure, vedere [Informazioni aggiuntive sulla memorizzazione nella cache](../best-practices-caching.md).
 
 __Cosa accade se non si usa una cache davanti al database?__ Se il computer di database è sufficientemente potente per gestire il carico del traffico, l'applicazione risponderà normalmente, anche se questo può significare che a un carico inferiore si sosterranno spese per un computer di database più costoso del necessario. Se il computer di database non è abbastanza potente per gestire il carico, si avranno esperienze utente di scarsa qualità con l'applicazione, in termini di latenza, timeout ed eventualmente tempi di inattività.
 
###È stato contattato il supporto di Microsoft Azure se si prevede un evento di scalabilità elevata?
Il supporto di Azure può aiutare ad aumentare i limiti del servizio per gestire eventi di traffico elevato pianificati, ad esempio lancio di nuovi prodotti o festività speciali. Il supporto di Azure può anche aiutare a entrare in contatto con esperti che consentono di esaminare la progettazione in uso con il team dell'account e trovare la soluzione migliore per soddisfare le esigenze di eventi di scalabilità elevata. Per altre informazioni su come contattare il supporto di Azure, vedere [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).

__Cosa accade se non si contatta il supporto di Azure per un evento di scalabilità elevata?__ Se non si comunica o non si pianifica un evento di traffico elevato, si rischia di raggiungere determinati [limiti relativi ai servizi Azure](../azure-subscription-service-limits.md) e quindi di creare un'esperienza utente di scarsa qualità o nel peggiore dei casi tempi di inattività durante l'evento. L'esame dell'architettura e la comunicazione prima dei picchi consentono di attenuare tali rischi.

###Si usa una rete per la distribuzione di contenuti (rete CDN di Azure) davanti ai BLOB di archiviazione con connessione Internet e agli asset statici?
Una rete CDN consente di alleggerire il carico dei server eseguendo il caching del contenuto nelle località POP/perimetrali della rete CDN che si trovano in tutto il mondo. È possibile eseguire questa operazione per ridurre la latenza, aumentare la scalabilità, ridurre il carico dei server e nell'ambito di una strategia per la protezione da attacchi Denial of Service. Per altre informazioni su come usare la rete CDN di Azure per aumentare la resilienza e ridurre la latenza per i clienti, vedere [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../cdn/cdn-overview.md).

__Cosa accade se non si usa una rete CDN?__ Se non si usa una rete CDN, tutto il traffico dei clienti giunge direttamente alle risorse. Ciò implica carichi più elevati sui server, con la conseguente riduzione della loro scalabilità. I clienti possono anche sperimentare latenze più elevate perché le reti CDN offrono in tutto il mondo località probabilmente più vicine ai clienti.

##Passaggi successivi:
Per altre informazioni sulla progettazione delle applicazioni per la disponibilità elevata, vedere [Disponibilità elevata per le applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0824_2016-->
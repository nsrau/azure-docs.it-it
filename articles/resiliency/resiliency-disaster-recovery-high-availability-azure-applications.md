<properties
   pageTitle="Ripristino di emergenza e disponibilità elevata per le applicazioni Azure | Microsoft Azure"
   description="Panoramiche tecniche e informazioni approfondite sulla progettazione e la creazione di applicazioni per la disponibilità e il ripristino di emergenza basate su Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/01/2016"
   ms.author="aglick"/>

#Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure

##Introduzione

Questo articolo è incentrato sulla disponibilità elevata delle applicazioni eseguite in Microsoft Azure. Una strategia generale per la disponibilità elevata deve includere anche l'aspetto del ripristino di emergenza. Nei piani predisposti per gli errori e le emergenze nel cloud è necessario rilevare rapidamente gli errori. e implementare quindi una strategia che adeguata per il massimo tempo di inattività delle applicazioni accettabile. È inoltre necessario tenere conto della misura in cui è ammissibile la perdita di dati dell'applicazione, senza effetti negativi sulle attività aziendali in seguito al ripristino.

La maggior parte delle aziende afferma di essere preparata ad affrontare problemi temporanei e su larga scala. Prima di rispondere, occorrerebbe tuttavia chiedersi se la propria società sperimenta questi errori, se testa il ripristino dei database per accertarsi di avere a disposizione i processi corretti. La risposta è probabilmente negativa. Ciò accade perché, ai fini di un corretto ripristino di emergenza, è necessario pianificare e progettare con estrema cura l'implementazione dei processi adeguati. Analogamente a quanto avviene per molti altri requisiti non funzionali, ad esempio la sicurezza, raramente si dedicano al ripristino di emergenza il tempo e le attività di analisi preliminari effettivamente necessari. La maggior parte delle aziende, inoltre, non ha a disposizione budget sufficienti per dotarsi di aree distribuite geograficamente con capacità ridondante. Di conseguenza, anche le applicazioni cruciali vengono spesso escluse da un'accurata pianificazione del ripristino di emergenza.

Le piattaforme cloud, come Azure, forniscono aree geograficamente distribuite in diverse parti del mondo. Queste piattaforme offrono inoltre funzionalità che supportano la disponibilità e diversi scenari di ripristino di emergenza. È quindi possibile tenere nell'opportuna considerazione tutte le applicazioni cloud cruciali per realizzare un sistema a prova di emergenza. Azure offre resilienza e funzionalità di ripristino di emergenza integrate in molti dei relativi servizi. Le funzionalità della piattaforma devono essere accuratamente esaminate e corredate di strategie applicative.

Questo articolo descrive le fasi di progettazione necessarie per una distribuzione di Azure a prova di emergenza, per poter implementare un più ampio processo per la continuità aziendale. I piani per la continuità aziendale sono roadmap concepite per assicurare la continuità delle attività in condizioni avverse. Può trattarsi di errori della tecnologia, ad esempio l'interruzione di un servizio, oppure da calamità naturali come tempeste o interruzioni di energia. La resilienza delle applicazioni in caso di emergenze rappresenta solo una parte del più ampio processo di ripristino di emergenza descritto in questo documento di NIST, che fornisce una [guida ai piani di emergenza per i sistemi IT](https://www.fismacenter.com/sp800-34.pdf).

Le sezioni seguenti definiscono diversi livelli di problemi, le tecniche con cui affrontarli e le architetture che supportano tali tecniche. Queste informazioni consentono di approfondire gli aspetti dei processi e delle procedure di ripristino di emergenza, per accertarsi che la propria strategia risulti corretta ed efficiente.

##Caratteristiche delle applicazioni cloud resilienti

Un'applicazione ben strutturata è in grado di resistere a errori funzionali a livello tattico e può tollerare anche errori di sistema strategici a livello di area. Le sezioni seguenti definiscono la terminologia a cui si fa riferimento nel documento per descrivere diversi aspetti dei servizi cloud resilienti.

###Disponibilità elevata

Un'applicazione cloud a disponibilità elevata implementa strategie che attenuano l'effetto prodotto dalla mancata disponibilità delle dipendenze, quali i servizi gestiti offerti dalla piattaforma cloud. Nonostante i possibili errori delle funzionalità della piattaforma cloud, questo approccio consente all'applicazione di continuare a fornire le caratteristiche di sistema previste a livello funzionale e non funzionale. Questa procedura è illustrata in dettaglio nella serie di video di Channel 9 su [FailSafe: Informazioni aggiuntive per architetture cloud resilienti](https://channel9.msdn.com/Series/FailSafe).

Quando si implementa l'applicazione è necessario considerare la possibilità che si verifichi un'interruzione di funzionalità. È necessario tenere conto anche dell'impatto che un problema di questo tipo può avere sull'applicazione dal punto di vista delle attività aziendali, prima di approfondire le strategie di implementazione. Se non si valutano con attenzione l'impatto aziendale e la probabilità che si verifichino condizioni di rischio, l'implementazione può rivelarsi costosa e potenzialmente inutile.

Per comprendere questo aspetto correlato alla disponibilità elevata, è possibile ricorrere a un'analogia automobilistica. Anche i componenti della migliore qualità e una meccanica eccellente non impediscono guasti occasionali. Ad esempio, nel caso di uno pneumatico a terra, l'auto continua a funzionare, ma le prestazioni risultano ridotte. Se si è tenuto conto in anticipo di questa eventualità, è possibile usare un ruotino di scorta finché non si raggiunge un'officina. Anche se questa soluzione non consenta di tenere velocità elevate, è comunque possibile continuare a guidare il veicolo finché non si provvede a sostituire lo pneumatico. Analogamente, se si pianifica una potenziale perdita di funzionalità in un servizio cloud, è possibile evitare che un problema relativamente minore provochi la mancata disponibilità dell'intera applicazione. Questo vale anche se il servizio cloud deve essere eseguito in modalità con funzionalità ridotte.

La disponibilità elevata per i servizi cloud include alcune funzionalità chiave, ovvero disponibilità, scalabilità e tolleranza di errore. Anche se queste funzionalità sono correlate tra loro, è importante comprendere gli aspetti di ognuna e il modo in cui contribuiscono alla disponibilità generale della soluzione.

###Availability

Un'applicazione disponibile tiene conto della disponibilità dell'infrastruttura sottostante e dei servizi dipendenti. Le applicazioni disponibili rimuovono i singoli punti di errore grazie alla ridondanza e a una progettazione resiliente. Quando si amplia l'ambito per includere la disponibilità in Azure, è importante comprendere il concetto di disponibilità effettiva della piattaforma. La disponibilità effettiva si basa sui contratti di servizio di ogni servizio dipendente e sul relativo effetto cumulativo rispetto alla disponibilità complessiva del sistema.

La disponibilità del sistema si misura in base alla percentuale di un intervallo di tempo in cui il sistema potrà funzionare. Ad esempio, il contratto di servizio sulla disponibilità di almeno due istanze di un ruolo Web o di lavoro in Azure è pari al 99,95% (sul 100%). Questo valore non misura i livelli di prestazioni o funzionalità dei servizi eseguiti in quei ruoli. Tuttavia, la disponibilità effettiva di un servizio cloud dipende anche dai vari contratti di servizio degli altri servizi dipendenti. Maggiore è il numero di componenti mobili nel sistema, maggiore è l'attenzione da dedicare alla verifica che l'applicazione sia in grado di soddisfare in modo resiliente i requisiti di disponibilità degli utenti finali.

Verranno ora esaminati i contratti di servizio seguenti che usano i servizi di Azure: Calcolo, Database SQL di Azure e Archiviazione di Azure.

|Servizio di Azure|Contratto di servizio |Potenziale inattività (in minuti)/mese (30 giorni)|
|:------------|:-----|:----------------------------------------:|
|Calcolo |99,95%|21,6 minuti |
|Database SQL |99,99%|4,3 minuti |
|Archiviazione |99,90%|43,2 minuti |

È necessario che la pianificazione tenga conto della possibilità che i servizi diventino non disponibili in momenti diversi. In questo esempio semplificato, il numero totale di minuti al mese in cui l'applicazione potrebbe essere inattiva è pari a 108. Un mese di 30 giorni è costituito da un totale di 43.200 minuti. 108 minuti rappresentano una percentuale pari allo 0,25% del numero totale di minuti in un mese di 30 giorni (43.200 minuti). Questo valore implica una disponibilità effettiva del 99,75% per il servizio cloud.

L'uso delle tecniche per la disponibilità descritte in questo documento può tuttavia migliorare questa soglia. Se ad esempio si progetta l'applicazione in modo che rimanga in esecuzione in caso di non disponibilità del database SQL, è possibile rimuovere questa voce dall'equazione. Ciò può implicare un'esecuzione dell'applicazione in modalità con funzionalità ridotte, quindi è necessario tenere conto anche dei requisiti aziendali. Per un elenco completo dei contratti di servizio di Azure, vedere [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

###Scalabilità

La scalabilità ha un impatto diretto sulla disponibilità, perché un'applicazione che smette di funzionare a causa dell'aumento del carico non risulta più disponibile. Le applicazioni scalabili sono in grado di soddisfare un aumento della domanda con risultati coerenti in tempi accettabili. Quando un sistema è scalabile, viene modificato aumentando il numero di istanze o aumentando le prestazioni per gestire gli incrementi di carico mantenendo prestazioni coerenti. In termini semplici, l'aumento delle istanze consiste nell'aggiunta di un maggior numero di computer delle stesse dimensioni in termini di processore, memoria e larghezza di banda, mentre l'aumento delle prestazioni consiste nell'aumento delle dimensioni dei computer esistenti. Per Azure sono disponibili opzioni di aumento delle prestazioni per la selezione di computer di diverse dimensioni per il calcolo. La modifica delle dimensioni di un computer ne implica tuttavia la ridistribuzione. Le soluzioni più flessibili sono quindi quelle progettate per aumentare il numero di istanze. Ciò è particolarmente vero per il calcolo, perché è possibile aumentare facilmente il numero di istanze in esecuzione di qualsiasi ruolo Web o di lavoro. Tali istanze aggiuntive gestiscono l'aumento del traffico tramite il portale Web di Azure, gli script di PowerShell o il codice Questa decisione deve basarsi sugli aumenti rilevati nelle specifiche metriche sottoposte a monitoraggio. In questo scenario le metriche o le prestazioni correlate agli utenti non subiscono un calo apprezzabile sotto carico. In genere, i ruoli Web e di lavoro archiviano gli stati esternamente. Questo consente di bilanciare il carico in modo flessibile e di gestire automaticamente eventuali modifiche al numero totale di istanze. L'aumento del numero di istanze è utile anche con i servizi, come Archiviazione di Azure, per i quali non sono disponibili opzioni multilivello per l'aumento delle prestazioni.

Le distribuzioni cloud dovrebbero essere considerate come raccolte di unità di scala. Ciò conferisce elasticità all'applicazione per soddisfare i livelli di velocità effettiva richiesti dagli utenti finali. Le unità di scala sono più semplici da visualizzare a livello di server applicazioni o Web, perché Azure offre già nodi di calcolo senza stato tramite i ruoli Web e di lavoro. L'aggiunta di più unità di scala di calcolo alla distribuzione non determina effetti collaterali in termini di gestione degli stati, perché le unità di scala di calcolo sono senza stato. Le unità di scala di archiviazione sono responsabili della gestione di una partizione di dati strutturati o non strutturati. Esempi di unità di scala di archiviazione includono la partizione di tabelle, il contenitore BLOB e il database SQL di Azure. Anche l'uso di più account di archiviazione di Azure influisce direttamente sulla scalabilità dell'applicazione. Un servizio cloud altamente scalabile deve essere progettato in modo da integrare più unità di scala di archiviazione. Se ad esempio un'applicazione usa dati relazionali, i dati devono essere partizionati tra diversi database SQL. In questo modo l'archiviazione potrà adattarsi al modello elastico delle unità di scala di calcolo. Analogamente, Archiviazione di Azure consente schemi di partizionamento dei dati che richiedono una progettazione mirata per soddisfare le esigenze di velocità effettiva del livello di calcolo. Per un elenco di procedure consigliate relative alla progettazione di servizi cloud scalabili, vedere il blog sulle [procedure consigliate per la progettazione di servizi su larga scala nei servizi cloud di Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

###Tolleranza di errore

Le applicazioni devono presupporre che ogni funzionalità dipendente del cloud possa prima o poi risultare non disponibile. Le applicazioni a tolleranza di errore rilevano gli elementi con errori e agiscono in modo da continuare a funzionare e restituire risultati coerenti entro un intervallo di tempo specifico. Per le condizioni di errore temporanee, i sistemi a tolleranza di errore si avvalgono di criteri di ripetizione dei tentativi. Per gli errori più gravi, l'applicazione può rilevare i problemi ed eseguire il failover su hardware alternativo o ricorrere a piani di emergenza finché l'errore non viene risolto. Un'applicazione affidabile può gestire correttamente gli errori di uno o più componenti senza smettere di funzionare nel modo corretto. Le applicazioni a tolleranza di errore possono adottare una o più strategie di progettazione, ad esempio la ridondanza, la replica o la riduzione delle funzionalità.

##Ripristino di emergenza

Le distribuzioni cloud possono smettere di funzionare a causa di interruzioni sistemiche dei servizi dipendenti o dell'infrastruttura sottostante. In tali circostanze, il piano per la continuità aziendale prevede l'attivazione del processo di ripristino di emergenza. Questo processo in genere include sia procedure automatizzate sia interventi del personale operativo per riattivare l'applicazione in un'area disponibile. Ciò implica il trasferimento di utenti, dati e servizi dell'applicazione alla nuova area. Prevede inoltre l'uso di supporti di backup o di una replica continua.

Tornare al paragone precedente tra la disponibilità elevata e la capacità di risolvere il problema di uno pneumatico forato grazie a un ruotino di scorta. Il ripristino di emergenza è invece paragonabile alle misure che si adottano in seguito a un tamponamento a causa del quale il veicolo risulta inutilizzabile. In questo caso, la soluzione migliore consiste nel trovare un modo efficiente per cambiare mezzo, ad esempio rivolgendosi a un servizio di trasporto o a un amico. In uno scenario di questo tipo, è probabile che occorra più tempo per rimettersi in viaggio e che risulti più complesso ripristinare e riparare il veicolo originale. Analogamente, il ripristino di emergenza in un'altra area rappresenta un'attività complessa che implica in genere un certo tempo di inattività e potenziali perdite di dati. Per comprendere e valutare meglio le strategie di ripristino di emergenza, è importante definire due termini, ovvero l'obiettivo del tempo di ripristino (RTO) e l'obiettivo del punto di ripristino (RPO).

###Obiettivo tempo di ripristino (RTO)

L'obiettivo tempo di ripristino (RTO) corrisponde alla quantità di tempo massima allocata per il ripristino delle funzionalità dell'applicazione. Questo valore si basa sui requisiti aziendali ed è correlato all'importanza dell'applicazione. Le applicazioni aziendali critiche richiedono un RTO basso.

###Obiettivo punto di ripristino (RPO)

L'obiettivo punto di ripristino (RPO) è l'intervallo di tempo accettabile per i dati persi a causa del processo di ripristino. Se ad esempio l'RPO è pari a un'ora, è necessario eseguire il backup o la replica completa di tutti i dati almeno ogni ora. Dopo la riattivazione dell'applicazione in un'area alternativa, nel backup può mancare al massimo un'ora di dati. Come per l'RTO, le applicazioni critiche richiedono un RPO ridotto.

##Elenco di controllo

Ecco i punti chiave che sono stati trattati in questo articolo e negli articoli correlati sulla [disponibilità elevata](./resiliency-high-availability-azure-applications.md) e il [ripristino di emergenza](./resiliency-disaster-recovery-azure-applications.md) per le applicazioni Azure. Questo riepilogo sarà un elenco di controllo di cui tenere conto ai fini della pianificazione della disponibilità e del ripristino di emergenza. Si tratta di procedure consigliate utili per i clienti che vogliono implementare una soluzione efficiente.

1. Eseguire una valutazione dei rischi per ogni applicazione, perché ognuna di esse può presentare requisiti diversi. Alcune applicazioni sono più importanti di altre, il che giustifica i costi aggiuntivi necessari per progettare il ripristino di emergenza.
1. Usare queste informazioni per definire l'RTO e l'RPO di ogni applicazione.
1. Progettare in funzione del rischio di errori, a partire dall'architettura dell'applicazione.
1. Implementare le procedure consigliate per la disponibilità elevata senza rinunciare all'equilibro tra costi, complessità e rischio.
1. Implementare i piani e i processi di ripristino di emergenza.
  * Valutare gli errori, da quelli a livello di modulo fino a quelli che compromettono completamente il cloud.
  * Stabilire strategie di backup per tutti i dati di riferimento e transazionali.
  * Scegliere un'architettura di ripristino di emergenza multisito.
1. Definire un proprietario specifico per i processi, l'automazione e i test del ripristino di emergenza. Il proprietario deve gestire l'intero processo.
1. Documentare i processi in modo che siano facilmente ripetibili. Anche se il proprietario è uno solo, più persone devono essere in grado di comprendere e seguire i processi in caso di emergenza.
1. Formare il personale in merito all'implementazione del processo.
1. Usare le simulazioni di ripristino a fini della formazione e della convalida del processo.

##Riepilogo

Quando si verificano errori a livello di hardware o applicazioni in Azure, è necessario adottare tecniche e strategie di gestione diverse da quelle usate in caso di errori nei sistemi locali. Ciò dipende soprattutto dal fatto che le soluzioni cloud presentano in genere più dipendenze dall'infrastruttura distribuita in un'area di Azure e vengono gestite come servizi distinti. Gli errori parziali devono essere gestiti mediante tecniche per la disponibilità elevata. Per gestire errori più gravi, dovuti probabilmente a un evento di emergenza, usare strategie di ripristino di emergenza.

Azure rileva e gestisce molti errori, ma esistono molti tipi di errori che richiedono strategie specifiche dell'applicazione. È necessario prepararsi attivamente per gestire gli errori di applicazioni, servizi e dati.

Quando si crea un piano di disponibilità e ripristino di emergenza dell'applicazione, tenere conto delle conseguenze aziendali dell'errore dell'applicazione. La definizione di processi, criteri e procedure per il ripristino di sistemi critici in seguito a eventi catastrofici richiede tempo, pianificazione e impegno. L'operazione, inoltre, non si esaurisce con la definizione dei piani. È necessario analizzare, testare e migliorare continuamente i piani in base al portafoglio di applicazioni, alle esigenze aziendali e alle tecnologie disponibili. Azure offre nuove funzionalità e pone nuove sfide per la creazione di applicazioni affidabili in grado di far fronte agli errori.

##Risorse aggiuntive

[Disponibilità elevata per le applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Ripristino di emergenza per le applicazioni basate su Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Indicazioni tecniche sulla resilienza di Azure](resiliency-technical-guidance.md)

[Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md)

[Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[FailSafe: Informazioni aggiuntive per architetture cloud resilienti](https://channel9.msdn.com/Series/FailSafe)

[Procedure consigliate per la progettazione di servizi su larga scala nei servizi cloud di Azure.](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

##Passaggi successivi

Questo articolo fa parte di una serie dedicata a ripristino di emergenza e disponibilità elevata per le applicazioni Azure. L'articolo successivo della serie riguarda [Disponibilità elevata per le applicazioni basate su Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0803_2016-->
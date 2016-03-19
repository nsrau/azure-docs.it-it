<properties
	pageTitle="Progettazione dell'infrastruttura di rete per il ripristino di emergenza | Microsoft Azure"
	description="In questo articolo vengono illustrate alcune considerazioni sulla progettazione di rete per Azure Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/22/2016"
	ms.author="pratshar"/>

#  Progettazione dell'infrastruttura di rete per il ripristino di emergenza

Questo articolo si rivolge ai professionisti IT che sono responsabili dell'architettura, dell'implementazione e del supporto dell'infrastruttura di continuità aziendale e ripristino di emergenza (BCDR, Business Continuity and Disaster Recovery) e che vogliono sfruttare Microsoft Azure Site Recovery (ASR) per supportare e migliorare i servizi BCDR. Questo documento illustra le considerazioni pratiche per la distribuzione di server System Center Virtual Machine Manager, i pro e i contro delle subnet estese rispetto al failover su subnet e come strutturare il ripristino di emergenza per siti virtuali in Microsoft Azure.

## Panoramica

[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) è un servizio di Microsoft Azure che orchestra la protezione e il ripristino delle applicazioni virtualizzate per finalità di continuità aziendale e ripristino di emergenza. Questo documento è una guida al processo di progettazione delle reti, che si concentra sull'architettura di intervalli IP e di subnet nel sito di ripristino di emergenza, quando si replicano macchine virtuali (VM) usando Site Recovery.

Questo articolo illustra inoltre come Site Recovery consenta di architettare e implementare un data center virtuale multisito per supportare i servizi BCDR in fase di test o in caso di emergenza.

In un mondo in cui tutti si aspettano una connettività 24/7, è più importante che mai mantenere operative l'infrastruttura e le applicazioni. Lo scopo della continuità aziendale e del ripristino di emergenza è di ripristinare i componenti in cui si sono verificati problemi in modo che l'organizzazione possa rapidamente riprendere le normali operazioni. Sviluppare strategie di ripristino di emergenza per gestire improbabili eventi disastrosi è molto difficile, a causa dell'intrinseca difficoltà di prevedere il futuro, soprattutto in relazione a eventi improbabili, e del costo elevato richiesto dalle misure di protezione adeguate contro catastrofi di vasta portata.

Nell'ambito di un piano di ripristino di emergenza è necessario definire l'obiettivo del tempo di ripristino (RTO, Recovery Time Objective) e l'obiettivo del punto di ripristino (RPO, Recovery Point Objective), indispensabili per la pianificazione BCDR. Quando si verifica un'emergenza nel data center del cliente, usando Azure Site Recovery, il cliente può portare rapidamente (RTO basso) online le macchine virtuali replicate presenti nel data center secondario o in Microsoft Azure con una perdita di dati minima (RPO basso).

Il failover è possibile grazie ad ASR che all'inizio copia le macchine virtuali designate dal data center primario al data center secondario o in Azure (a seconda dello scenario) e quindi aggiorna periodicamente le repliche. Durante la pianificazione dell'infrastruttura, la progettazione della rete deve essere considerata un potenziale collo di bottiglia che può impedire di raggiungere gli obiettivi RTO e RPO aziendali.

Quando gli amministratori pianificano di distribuire una soluzione di ripristino di emergenza, una delle prime cose a cui pensano è come poter raggiungere la macchina virtuale al termine del failover. Usando il mapping delle reti in ASR, l'amministratore può scegliere a quale rete verrà collegata la macchina virtuale dopo il failover. Per altre informazioni sul mapping delle reti, vedere [Preparare il mapping di rete](site-recovery-network-mapping.md).

Durante la progettazione della rete per il sito di ripristino, l'amministratore ha due scelte:

- Usare un intervallo di indirizzi IP diverso per la rete nel sito di ripristino. In questo scenario la macchina virtuale dopo il failover otterrà un nuovo indirizzo IP e l'amministratore dovrà eseguire un aggiornamento DNS. Altre informazioni su come eseguire l'aggiornamento DNS sono disponibili qui.
- Usare lo stesso intervallo di indirizzi IP per la rete nel sito di ripristino. In alcuni scenari gli amministratori preferiscono mantenere gli indirizzi IP che hanno nel sito primario dopo il failover. In un normale scenario un amministratore dovrà aggiornare le route per indicare la nuova posizione degli indirizzi IP, ma nello scenario in cui una VLAN estesa viene distribuita tra il sito primario e quello di ripristino mantenere gli indirizzi IP per le macchine virtuali diventa un'opzione interessante. Mantenere gli stessi indirizzi IP semplifica il processo di ripristino eliminando i passaggi post-failover correlati alla rete.

Questo documento illustra due importanti considerazioni tecniche per la progettazione dello spazio indirizzi della rete per il ripristino di emergenza:

- Progettazione della topologia di distribuzione del componente di gestione dell'infrastruttura (System Center Virtual Machine Manager) per proteggere i carichi di lavoro con Azure Site Recovery (ASR).
- Gestione indirizzi IP in un ambiente di ripristino di emergenza.


## Progettazione della distribuzione di System Center VMM per Azure Site Recovery

I requisiti aziendali determinano le decisioni relative alla topologia. In base a tali requisiti un'organizzazione può scegliere di avere più data center gestiti da un solo responsabile della gestione o più responsabili della gestione che gestiscono data center diversi. Questa sezione illustra come un'organizzazione progetta una topologia di distribuzione di System Center Virtual Machine Manager (SCVMM) per proteggere i carichi di lavoro con ASR.

Azure Site Recovery favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) gestendo la replica, il failover e il ripristino delle macchine virtuali in diversi scenari di distribuzione. ASR, basato sulla semplicità della progettazione, è modellato in modo che gli amministratori possano compilare una soluzione di ripristino di emergenza sovrapponendola alle topologie di data center esistenti. ASR consente alle organizzazioni di scegliere una topologia che soddisfi nel modo migliore le esigenze aziendali.

Per un elenco completo degli scenari di distribuzione, vedere [Che cos'è Site Recovery?](site-recovery-overview.md)

I clienti possono proteggere i carichi di lavoro in un server VMM nel sito primario con un server VMM che gestisce il sito secondario. In caso di emergenza, tutti i carichi di lavoro effettueranno facilmente il failover dal sito primario al sito secondario che viene gestito dal server VMM secondario. Alcune organizzazioni, tuttavia, possono avere la necessità di usare un solo server VMM per gestire tutti i data center ed evitare così un sovraccarico di gestione. Per un cliente con questa topologia è indispensabile poter ripristinare il server VMM prima che i carichi di lavoro possano essere ripristinati. Un cliente deve quindi progettare attentamente le topologie VMM per rendere i carichi di lavoro "a prova di emergenza".

Se nell'infrastruttura è disponibile un solo server VMM, è possibile distribuire Azure Site Recovery per replicare le macchine virtuali in cloud VMM in Azure oppure è possibile replicarle tra cloud in un singolo server VMM. Per il ripristino è necessario eseguire manualmente il failover del server VMM dall'esterno della console di Azure Site Recovery usando la replica Hyper-V nella console di gestione di Hyper-V. È consigliabile distribuire il server VMM in una delle topologie seguenti per poter ripristinare i carichi di lavoro con i migliori RTO (Recovery Time Objective) e RPO (Recovery Point Objective) possibili.

### Distribuzione autonoma

In questa topologia si distribuisce un server VMM in una macchina virtuale in un sito primario, replicando tale macchina virtuale in un sito secondario con Azure Site Recovery e la replica Hyper-V. In alcuni casi, installando il server VMM e l'istanza di SQL Server di supporto nella stessa macchina virtuale, è possibile ridurre i tempi di inattività, perché è necessario creare un'istanza di una sola macchina virtuale. Quando il servizio VMM utilizza un SQL Server remoto, occorre ripristinare l'istanza di SQL Server prima del ripristino del server VMM.

I passaggi per distribuire una singola istanza di VMM in una macchina virtuale con la replica Hyper-V abilitata sono:

1. Configurare il server VMM in una macchina virtuale in cui è installato SQL Server.
2. Aggiungere al server VMM gli host da gestire nei cloud.
3. Accedere al portale di Azure e configurare la protezione dei cloud.
4. Abilitare la replica per tutte le macchine virtuali che devono essere protette dal server VMM.
5. Passare alla console di gestione di Hyper-V, selezionare la Replica Hyper-V e quindi abilitare la replica nella macchina virtuale VMM. Verificare che la macchina virtuale VMM non venga aggiunta ai cloud protetti dal servizio Azure Site Recovery, affinché le impostazioni di replica Hyper-V non vengano sovrascritte da ASR.

In caso di emergenza, i carichi di lavoro possono essere ripristinati tramite la procedura seguente:

1. Effettuare il failover della macchina virtuale VMM di replica nel sito di ripristino, mediante la console di gestione di Hyper-V.
2. Al termine del ripristino della macchina virtuale VMM, l'utente può accedere a Gestione ripristino di Hyper-V dal sito secondario.
3. Al termine del failover non pianificato, l'utente può accedere a tutte le risorse nel sito primario.


Con questa topologia l'utente dovrà effettuare il failover manuale della VM VMM nel sito secondario prima di poter effettuare il failover dei carichi di lavoro.

![autonoma](./media/site-recovery-network-design/standalone.png)

### Distribuzione cluster


La distribuzione di un server VMM a disponibilità elevata consente di rendere il servizio VMM stesso compatibile con il cluster. Questa operazione è utile se carichi di lavoro critici sono gestiti da VMM, perché garantisce la disponibilità del carico di lavoro e protegge da un errore del server VMM. È estremamente importante che il server VMM sia sempre disponibile. Rendere il servizio compatibile con il cluster consente di proteggere il server VMM dal failover hardware dell'host in cui è in esecuzione il server VMM, oltre che proteggere dai problemi che possono verificarsi nella VM in cui è in esecuzione VMM. Per distribuire un server VMM a disponibilità elevata, è necessario che il server VMM sia presente in un cluster di failover di Windows Server. Per altre informazioni su come distribuire un server VMM a disponibilità elevata, vedere il post di blog su System Center disponibile qui.

Quando si proteggono i carichi di lavoro con ASR, il server VMM deve essere distribuito in un cluster esteso in siti geograficamente separati, come illustrato nella figura 2. Il database di SQL Server usato da VMM deve essere protetto con gruppi di disponibilità AlwaysOn di SQL Server con una replica nel sito secondario. In caso di emergenze, il server VMM e il database SQL Server corrispondente effettueranno automaticamente il failover nel sito di ripristino e sarà quindi possibile effettuare il failover di tutti i carichi di lavoro con ASR.

![Cluster VMM esteso](./media/site-recovery-network-design/network-design1.png)

Figura 2

## Progettazione dello spazio di indirizzi della rete in un ambiente di ripristino di emergenza

Quando gli amministratori pianificano di distribuire una soluzione di ripristino di emergenza, una delle prime cose a cui pensano è come poter raggiungere le applicazioni al termine del failover. Le applicazioni moderne necessitano quasi sempre di un collegamento in rete, quindi lo spostamento fisico di un servizio da un sito a un altro è difficoltoso. Nelle soluzioni di ripristino di emergenza questo problema viene gestito principalmente in due modi. Il primo approccio consiste nel mantenere fissi gli indirizzi IP. Nonostante lo spostamento dei servizi e la presenza dei server di hosting in posizioni fisiche diverse, le applicazioni mantengono la configurazione degli indirizzi IP nella nuova posizione. Il secondo approccio richiede la modifica completa dell'indirizzo IP durante la transizione al sito ripristinato. Ogni approccio presenta diverse varianti di implementazione che vengono riepilogate sotto.

Durante la progettazione della rete per il sito di ripristino, l'amministratore ha due scelte:

### Opzione 1: Mantenere gli indirizzi IP 

Dal punto di vista del processo di ripristino di emergenza, l'uso di indirizzi IP fissi si presenta come il metodo più semplice da implementare. In realtà è l'approccio meno comune a causa delle diverse potenziali difficoltà che comporta. Azure Site Recovery consente di conservare gli indirizzi IP in tutte le situazioni. Prima di decidere di mantenere l'IP, è opportuno considerare i vincoli che vengono imposti alle funzionalità di failover. Verranno analizzati i fattori che possono indurre a decidere di mantenere gli indirizzi IP oppure no. In questa categoria sono presenti due sottotipi principali: la subnet estesa e il failover sulla subnet. Verranno illustrati scenari distinti con un failover sulla subnet o una subnet estesa in due posizioni.

#### Subnet estesa

La subnet è resa disponibile simultaneamente sia nella località primaria che in quella di ripristino di emergenza. In altre parole, è possibile spostare un server e la relativa configurazione IP (livello 3) nel secondo sito e la rete instraderà automaticamente il traffico nella nuova posizione. Dal punto di vista del server si tratta di un'operazione semplice, tuttavia presenta una serie di difficoltà:

- Considerando il livello 2 (livello di collegamento dati), tale approccio richiederà apparecchiature di rete in grado di gestire una cosiddetta VLAN estesa, ma non dovrebbe essere un problema perché oggi questo tipo di apparecchiature è ampiamente disponibile. Il secondo e più difficile problema è il fatto che, estendendo la VLAN, il dominio di errore potenziale viene esteso a entrambi i siti, creando in sostanza un singolo punto di errore. Anche se è un'eventualità improbabile, è accaduto che una broadcast storm fosse avviata, ma non potesse essere isolata. Sono state riscontrate opinioni diverse in merito a quest'ultimo problema e se da un lato sono state effettuate con successo molte implementazioni, dall'altro c'è stato anche un netto rifiuto di implementare questa tecnologia.
- Non è possibile adottare la subnet estesa se si usa Microsoft Azure come sito di ripristino di emergenza.


#### Failover sulla subnet

È possibile implementare il failover sulla subnet per ottenere i vantaggi della soluzione di subnet estesa illustrati sopra senza estendere la subnet su più siti. Qui qualsiasi subnet specificata sarà presente nel sito 1 o nel sito 2, ma mai in entrambi i siti contemporaneamente. Per mantenere lo spazio degli indirizzi IP in caso di failover, è possibile programmare l'infrastruttura del router per lo spostamento delle subnet da un sito a un altro. In uno scenario di failover le subnet verranno spostate con le macchine virtuali protette associate. Lo svantaggio principale di questo approccio è che, in caso di errore, è necessario spostare l'intera subnet. Ciò può funzionare, ma può influire sulle considerazioni relative alla granularità di failover.

Verrà ora esaminato come un'azienda fittizia, denominata Contoso, possa replicare le proprie macchine virtuali in un percorso di ripristino, durante il failover dell'intera subnet. Verrà prima di tutto esaminato come Contoso possa gestire le subnet durante la replica delle macchine virtuali tra due posizioni locali e quindi verrà illustrato il funzionamento del failover sulla subnet quando si usa Azure come sito di ripristino di emergenza.

##### Failover sulla subnet - Ripristino di emergenza a livello aziendale

Verrà ora esaminato uno scenario in cui si vuole mantenere l'IP di ogni VM ed effettuare il failover della subnet completa. Il sito primario dispone di applicazioni in esecuzione nella subnet 192.168.1.0/24. Quando il failover viene effettuato, tutte le macchine virtuali che fanno parte di questa subnet verranno sottoposte a failover nel sito di ripristino e manterranno gli indirizzi IP. Le route dovranno essere modificate in modo appropriato per rispecchiare il fatto che tutte le macchine virtuali appartenenti alla subnet 192.168.1.0/24 sono state spostate nel sito di ripristino.

Nell'illustrazione seguente le route tra il sito primario e il sito di ripristino, il terzo sito e il sito primario e il terzo sito e il sito di ripristino dovranno essere modificate in modo appropriato. Per la versione iniziale di questo documento si presuppone quanto segue:

- Ogni data center viene gestito da una istanza di System Center VMM dedicata. Non sarà presente alcuna replica dei database di System Center VMM tra i data center.
- Ogni data center userà indirizzi IP statici per le macchine virtuali.
- La connettività tra i data center avviene tramite un circuito dedicato e non tramite la connettività VPN su Internet.

	![Mantenere l'indirizzo IP](./media/site-recovery-network-design/network-design3.png)

	Figura 3

	![Mantenere l'indirizzo IP](./media/site-recovery-network-design/network-design2.png)
	
	Figura 4

Quando si abilita la protezione per una macchina virtuale specifica, ASR allocherà le risorse di rete in base al flusso di lavoro seguente:

- ASR alloca un indirizzo IP per ogni interfaccia di rete nella macchina virtuale dal pool di indirizzi IP statici definiti nella rete pertinente per ogni istanza di System Center VMM.
- Se l'amministratore definisce nel sito di ripristino per la rete lo stesso pool di indirizzi IP della rete nel sito primario, durante l'allocazione dell'indirizzo IP nella macchina virtuale di replica ASR allocherà lo stesso indirizzo IP della macchina virtuale primaria. L'IP è riservato in VMM, ma non è impostato come IP di failover. L'IP di failover viene impostato subito prima del failover.

	![Mantenere l'indirizzo IP](./media/site-recovery-network-design/network-design4.png)
	
	Figura 5

La figura 5 indica le impostazioni TCP/IP del failover per la macchina virtuale di replica (nella console di Hyper-V). Queste impostazioni verranno popolate subito prima dell'avvio della macchina virtuale dopo un failover.

Se non è disponibile lo stesso IP, ASR allocherà un altro indirizzo IP disponibile dal pool di indirizzi IP definiti.

Dopo avere abilitato la protezione nella VM, è possibile usare lo script di esempio seguente per verificare l'IP allocato nella macchina virtuale. Lo stesso IP dovrebbe essere impostato come IP di failover e assegnato alla VM al momento del failover:

![Mantenere l'indirizzo IP](./media/site-recovery-network-design/script.png)

>[AZURE.NOTE] Nello scenario in cui le macchine virtuali usano DHCP, la gestione degli indirizzi IP è completamente al di fuori del controllo di ASR. Un amministratore deve assicurarsi che il server DHCP che gestisce gli indirizzi IP nel sito di ripristino possa gestirli dallo stesso intervallo del sito primario.

##### Failover sulla subnet locale - Ripristino di emergenza in Azure

Azure Site Recovery (ASR) consente di usare Microsoft Azure come sito di ripristino di emergenza per le macchine virtuali. In questo caso, sarà necessario gestire più vincoli.

Verrà ora esaminato uno scenario in cui una società fittizia, denominata Woodgrove Bank, ha un'infrastruttura locale che ospita le applicazioni line-of-business, mentre le applicazioni per dispositivi mobili sono ospitate in Azure. La connettività tra le VM di Woodgrove Bank in Azure e i server locali è fornita da una rete privata virtuale (VPN) da sito a sito (S2S). La VPN S2S consente di considerare la rete virtuale di Woodgrove Bank in Azure come un'estensione della rete locale di Woodgrove Bank. Questa comunicazione è abilitata dalla VPN S2S tra il perimetro di Woodgrove Bank e la rete virtuale di Azure. Ora Woodgrove vuole usare ASR per replicare i carichi di lavoro in esecuzione nel data center in Azure. Questa opzione soddisfa le esigenze di Woodgrove, che vuole un'opzione di ripristino di emergenza economica e può archiviare i dati in ambienti di cloud pubblico. Woodgrove deve tenere conto delle applicazioni e delle configurazioni che dipendono da indirizzi IP hardcoded, quindi ha la necessità di mantenere gli indirizzi IP delle rispettive applicazioni dopo il failover in Azure.

L'infrastruttura locale della Woodgrove è gestita da un server VMM 2012 R2. Una rete logica basata su VLAN, denominata rete delle applicazioni, è stata creata nel server System Center VMM. Una rete VM, denominata rete VM delle applicazioni, viene creata usando la rete logica. Tutte le macchine virtuali nell'applicazione usano indirizzi IP statici, quindi è stato definito anche un pool di IP statici per la rete logica.

**Rete logica**

![Rete logica](./media/site-recovery-network-design/network-design5.png)

Figura 6

**Rete VM**

![Rete VM](./media/site-recovery-network-design/network-design6.png)

Figura 7

Woodgrove ha deciso di assegnare gli indirizzi IP dall'intervallo di indirizzi IP (172.16.1.0/24, 172.16.2.0/24) alle risorse in esecuzione in Azure.

Per consentire a Woodgrove di replicare le macchine virtuali in Azure mantenendo al contempo gli indirizzi IP, è necessario creare una rete virtuale di Azure, che dovrà essere un'estensione della rete locale, in modo che le applicazioni possano effettuare facilmente il failover dal sito locale ad Azure. Azure consente di aggiungere alle reti virtuali create in Azure la connettività VPN da sito a sito oltre che da punto a sito. Quando si configura la connessione da sito a sito, la rete di Azure consente di instradare il traffico verso il percorso locale (Azure lo definisce rete locale) solo se l'intervallo di indirizzi IP è diverso da quello degli indirizzi IP locali, perché Azure non supporta l'estensione delle subnet. Ciò significa che se si dispone di una subnet 192.168.1.0/24 locale, è impossibile aggiungere una rete locale 192.168.1.0/24 nella rete di Azure. Ciò è previsto poiché Azure non rileva l'assenza di macchine virtuali attive nella subnet, né riconosce che la subnet viene creata solo per scopi di ripristino di emergenza. Per poter instradare correttamente il traffico di rete all'esterno di una rete di Azure, le subnet nella rete e quelle nella rete locale non devono essere in conflitto.

![Failover sulla subnet](./media/site-recovery-network-design/network-design7.png)

Figura 8

Per consentire a Woodgrove di soddisfare i requisiti aziendali, è necessario implementare i flussi di lavoro seguenti:

- Creare una rete aggiuntiva, definita rete di ripristino, in cui verranno create le macchine virtuali di cui verrà effettuato il failover.
- Per assicurarsi che l'IP per una VM venga mantenuto dopo un failover, passare alla scheda Configura nelle proprietà della VM, specificare lo stesso IP locale della VM e quindi fare clic su Salva. Quando viene effettuato il failover della VM, Azure Site Recovery assegnerà l'IP specificato alla macchina virtuale. 

	![Proprietà di rete](./media/site-recovery-network-design/network-design8.png)

	Figura 9

Una volta attivato il failover e create le macchine virtuali nella rete di ripristino con l'IP desiderato, la connettività a questa rete può essere stabilita usando una connessione da rete virtuale a rete virtuale. Se necessario, è possibile creare script per questa azione. Come illustrato nella sezione precedente relativa al failover sulla subnet, anche nel caso di failover in Azure sarà necessario modificare in modo appropriato le route per rispecchiare il fatto che la subnet 192.168.1.0/24 è stata spostata in Azure.

![Proprietà di rete](./media/site-recovery-network-design/network-design9.png)

Figura 10

### Opzione 2: Modifica degli indirizzi IP

Questo approccio, in base a quanto riscontrato dagli autori, sembra quello prevalente. Consiste nel modificare l'indirizzo IP di ogni VM coinvolta nel failover. Un inconveniente di questo approccio è che la rete in entrata deve "sapere" che l'applicazione che si trovava all'IPx ora si trova all'IPy. Anche se IPx e IPy sono nomi logici, le voci DNS in genere devono essere modificate o scaricate in tutta la rete e le voci memorizzate nella cache nelle tabelle di rete devono essere aggiornate o scaricate, quindi potrebbe verificarsi un tempo di inattività a seconda di come è stata configurata l'infrastruttura DNS. È possibile ridurre questi problemi usando valori TTL bassi nel caso di applicazioni Intranet e usando [Gestione traffico di Azure con ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) per le applicazioni basate su Internet.

#### Modifica degli Indirizzi IP - Ripristino di emergenza a livello aziendale

Verrà ora esaminato lo scenario in cui si pianifica di usare IP diversi nel sito primario e in quello di ripristino. Nell'esempio seguente è compreso un terzo sito da cui è possibile accedere alle applicazioni ospitate nel sito primario o in quello di ripristino.

![IP diverso](./media/site-recovery-network-design/network-design10.png)

Figura 11

Nella figura 11 sono presenti alcune applicazioni ospitate nella subnet 192.168.1.0/24 nel sito primario e configurate per passare al sito di ripristino nella subnet 172.16.1.0/24 dopo un failover. La configurazione dei percorsi di rete o delle connessioni VPN è avvenuta in modo corretto, pertanto tutti e tre i siti dispongono dell'accesso reciproco.
 
Come illustrato nella figura 12, dopo il failover di una o più applicazioni, queste verranno ripristinate nella subnet di ripristino. In questo caso non esiste il vincolo di effettuare il failover dell'intera subnet contemporaneamente. Non sono necessarie modifiche per riconfigurare la VPN o le route di rete. Un failover e alcuni aggiornamenti DNS verificheranno che le applicazioni rimangano accessibili. Se il DNS è configurato per consentire aggiornamenti dinamici, le macchine virtuali potrebbero registrarsi con il nuovo IP, una volta avviate dopo un failover.

![IP diverso](./media/site-recovery-network-design/network-design11.png)

Figura 12

Al termine del failover, la macchina virtuale di replica potrebbe avere un indirizzo IP diverso da quello della macchina virtuale primaria. Le macchine virtuali aggiorneranno il server DNS in uso dopo l'avvio. Di solito, occorre modificare o cancellare le voci DNS in tutta la rete, così come occorre aggiornare o cancellare le voci memorizzate nella cache nelle tabelle di rete, pertanto non è insolito riscontrare tempi di inattività durante tali modifiche. È possibile ridurre tale problema nei modi seguenti:

- Utilizzando valori TTL bassi per le applicazioni Intranet.
- Usando Gestione traffico di Azure con ASR per le applicazioni basate su Internet.
- Utilizzando il seguente script all'interno del piano di ripristino per aggiornare il server DNS, al fine di garantire un aggiornamento tempestivo (lo script non è obbligatorio se è stata configurata la registrazione di DNS dinamici)

![IP diverso](./media/site-recovery-network-design/script2.png)

#### Modifica degli indirizzi IP - Ripristino di emergenza in Azure

Il post di blog dedicato alla [configurazione dell'infrastruttura di rete per Microsoft Azure come sito di ripristino di emergenza](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) spiega come configurare l'infrastruttura di rete di Azure necessaria quando non è obbligatorio mantenere gli indirizzi IP. All'inizio viene descritta l'applicazione, quindi viene illustrato come configurare la rete in locale e in Azure e infine viene illustrato come effettuare un failover di test e un failover pianificato.




## Passaggi successivi

[Informazioni](site-recovery-network-mapping.md) sulla modalità di mapping delle reti di origine e destinazione di Site Recovery.

<!---HONumber=AcomDC_0224_2016-->
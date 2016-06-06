<properties
   pageTitle="Procedure consigliate per la sicurezza della rete di Azure | Microsoft Azure"
   description="Questo articolo fornisce una serie di procedure consigliate per la sicurezza di rete usando le funzionalità integrate di Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="swadhwa"
   editor="TomShinder"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="TomSh"/>

# Procedure consigliate per la sicurezza della rete di Azure

Microsoft Azure consente di connettere macchine e dispositivi virtuali ad altri dispositivi di rete inserendoli in reti virtuali di Azure. Una rete virtuale di Azure è un costrutto di rete virtuale che consente di connettere le schede di rete virtuale a una rete virtuale per consentire le comunicazioni basate su TCP/IP tra i dispositivi abilitati per la rete. Le macchine virtuali di Azure connesse a una rete virtuale di Azure possono connettersi ai dispositivi nella stessa rete virtuale di Azure, in diverse reti virtuali di Azure, su Internet o persino in reti locali.

In questo articolo verrà illustrato un insieme di procedure consigliate per la sicurezza della rete di Azure, derivate dalla nostra esperienza con la rete di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata verrà illustrato:

- Qual è la procedura consigliata
- Il motivo per cui si vuole abilitare tale procedura consigliata
- Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
- Alternative possibili alla procedura consigliata
- Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza della rete di Azure si basa su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Le procedure consigliate per la sicurezza della rete di Azure discusse in questo articolo includono:

- Segmentare logicamente le subnet
- Controllare il comportamento di routing
- Abilitare il tunneling forzato
- Usare i dispositivi di rete virtuale
- Distribuire reti perimetrali per la suddivisione in zone di sicurezza
- Evitare l'esposizione a Internet con collegamenti WAN dedicati
- Ottimizzare il tempo di attività e le prestazioni
- Usare il bilanciamento del carico globale
- Disabilitare l'accesso RDP alle macchine virtuali di Azure
- Abilitare il Centro sicurezza di Azure
- Estendere il data center in Azure


## Segmentare logicamente le subnet

Le [reti virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-network/) sono simili a una rete LAN nella rete locale. L'idea alla base di una rete virtuale di Azure è creare una singola rete basata sullo spazio indirizzi IP privato in cui è possibile inserire tutte le [macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/). Gli spazi di indirizzi IP privati disponibili sono negli intervalli di Classe A (10.0.0.0/8), Classe B (172.16.0.0/12) e Classe C (192.168.0.0/16).

Analogamente alle operazioni svolte in locale, sarà consigliabile segmentare il più ampio spazio indirizzi in subnet. Per creare le subnet è possibile usare principi di suddivisione in subnet basati su [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Il routing tra le subnet verrà eseguito automaticamente e non sarà necessario configurare manualmente le tabelle di routing. Tuttavia, per impostazione predefinita non sono presenti controlli di accesso di rete tra le subnet create nella rete virtuale di Azure. Per creare controlli di accesso di rete tra subnet, è necessario inserire un elemento tra le subnet.

Uno degli elementi che è possibile usare per eseguire questa operazione è un [Gruppo di sicurezza di rete](./virtual-network/virtual-networks-nsg.md) (NSG). Gli NSG sono semplici dispositivi di ispezione dei pacchetti con stato che usano l'approccio a 5 tuple (indirizzo IP di origine, porta di origine, IP di destinazione, porta di destinazione e protocollo di livello 4) per creare regole allow/deny per il traffico di rete. È possibile consentire o negare il traffico da e verso un singolo indirizzo IP, a e da più indirizzi IP o persino a e da intere subnet.

L'uso di NSG per il controllo di accesso di rete tra subnet consente di inserire le risorse che appartengono alla stessa area di protezione o allo stesso ruolo nelle proprie subnet. Ad esempio, si pensi a una semplice applicazione a 3 livelli con un livello Web, un livello di logica di applicazione e un livello di database. Si inseriscono le macchine virtuali che appartengono a ciascuno di questi livelli nella propria subnet, quindi si usano gli NSG per controllare il traffico tra le subnet:

- Le macchine virtuali a livello Web possono avviare connessioni solo alle macchine a livello di logica dell'applicazione e accettare solo connessioni da Internet
- Le macchine virtuali a livello di logica dell'applicazione possono avviare connessioni solo con un livello database e accettare solo connessioni dal livello Web
- Le macchine virtuali a livello database non possono avviare alcuna connessione all'esterno della propria subnet e possono accettare solo connessioni dal livello di logica dell'applicazione

Per altre informazioni sui Gruppi di sicurezza di rete e su come è possibile usarli per segmentare logicamente le reti virtuali di Azure, leggere l'articolo [Che cos'è un gruppo di sicurezza di rete](./virtual-network/virtual-networks-nsg.md) (NSG).

## Controllare il comportamento di routing

Quando si inserisce una macchina virtuale in una rete virtuale di Azure, si noterà che la macchina virtuale può connettersi a qualsiasi altra macchina virtuale nella stessa rete virtuale Azure, anche se le altre macchine virtuali si trovano in subnet diverse. Il motivo per cui questo è possibile è che esiste un insieme di route di sistema, abilitate per impostazione predefinita, che consente questo tipo di comunicazione. Queste route predefinite consentono alle macchine virtuali nella stessa rete virtuale di Azure di avviare le connessioni tra loro e con Internet (per le comunicazioni in uscita solo con Internet).

Nonostante le route di sistema predefinite siano utili per molti scenari di distribuzione, in alcuni casi si vuole personalizzare la configurazione del routing per le distribuzioni. Queste personalizzazioni permettono di configurare l'indirizzo hop successivo in modo da raggiungere destinazioni specifiche.

È consigliabile configurare route definite dall'utente quando si distribuisce un dispositivo di sicurezza di rete virtuale, che verrà illustrato in una procedura consigliata successiva.

> [AZURE.NOTE] Le route definite dall'utente non sono necessarie e le route di sistema predefinite funzioneranno nella maggior parte dei casi.

Altre informazioni sulle route definite dall'utente e su come configurarle sono disponibili nell'articolo [Cosa sono le route definite dall'utente e l'inoltro IP](./virtual-network/virtual-networks-udr-overview.md).

## Abilitare il tunneling forzato

Per comprendere meglio il tunneling forzato, è utile comprendere cosa si intende per "split tunneling". L'esempio più comune di split tunneling è visibile nelle connessioni VPN. Si supponga di stabilire una connessione VPN da una camera d'albergo alla propria rete aziendale. Questa connessione consente di connettersi alle risorse nella rete aziendale e tutte le comunicazioni alle risorse nella rete aziendale passano attraverso il tunnel VPN.

Cosa accade quando ci si vuole connettere alle risorse in Internet? Quando è abilitato lo split tunneling, tali connessioni passano direttamente a Internet e non attraverso il tunnel VPN. Alcuni esperti di sicurezza lo considerano un potenziale rischio e quindi consigliano di disabilitare lo split tunneling e di far passare tutte le connessioni, che siano destinate a Internet o alle risorse aziendali, attraverso il tunnel VPN. Il vantaggio di questa operazione è che le connessioni a Internet vengono quindi forzate attraverso i dispositivi di sicurezza di rete aziendale, il che non accadrebbe se il client VPN fosse connesso a Internet di fuori del tunnel VPN.

Torniamo ora alle macchine virtuali in una rete virtuale di Azure. Le route predefinite per una rete virtuale di Azure consentono alle macchine virtuali di inviare traffico a Internet. Anche questo può rappresentare un rischio per la sicurezza, perché le connessioni in uscita potrebbero aumentare la superficie di attacco di una macchina virtuale ed essere sfruttate da utenti malintenzionati. Per questo motivo, è consigliabile abilitare il tunneling forzato su macchine virtuali quando è disponibile una connettività cross-premise tra la rete virtuale di Azure e la rete locale. Si discuterà della connettività cross-premise più avanti in questo documento sulle procedure consigliate per le reti di Azure.

Se non si ha una connessione cross-premise, assicurarsi che sia possibile sfruttare i gruppi di sicurezza di rete (descritti in precedenza) o i dispositivi di sicurezza di rete virtuali di Azure (illustrati di seguito) per impedire le connessioni in uscita a Internet dalle macchine virtuali di Azure.

Per altre informazioni sul tunneling forzato e su come abilitarlo, leggere l'articolo [Configurare il tunneling forzato con PowerShell e Azure Resource Manager](./vpn-gateway/vpn-gateway-forced-tunneling-rm.md).

## Usare i dispositivi di rete virtuale

Nonostante i gruppi di sicurezza di rete e il routing definito dall'utente possano fornire un certo grado di sicurezza di rete ai livelli di rete e di trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), vi saranno situazioni in cui si vorrà oppure sarà necessario abilitare la protezione di alto livello dello stack. In tali situazioni, è consigliabile distribuire i dispositivi di sicurezza di rete virtuale forniti dai partner di Azure.

I dispositivi di sicurezza di rete di Azure possono offrire livelli di sicurezza notevolmente migliorati rispetto a quanto offerto dai controlli a livello di rete. Tra le funzionalità di sicurezza di rete fornite da dispositivi di sicurezza di rete virtuale sono incluse le seguenti:

- Funzionalità di firewall
- Rilevamento intrusione/Prevenzione intrusioni
- Gestione vulnerabilità
- Controllo applicazione
- Rilevamento anomalie basato su rete
- Filtro Web
- Antivirus
- Protezione botnet

Se è necessario un livello di sicurezza di rete più elevato, ottenibile con i controlli di accesso a livello di rete, è consigliabile indagare e distribuire i dispositivi di sicurezza di rete virtuale di Azure.

Per informazioni su quali dispositivi di sicurezza della rete virtuale di Azure sono disponibili e sulle relative capacità, visitare [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "sicurezza di rete".

##Distribuire reti perimetrali per la suddivisione in zone di sicurezza
Una "rete perimetrale" è un segmento di rete fisica o logica che è progettato per fornire un ulteriore livello di sicurezza tra le risorse e Internet. Lo scopo della rete perimetrale è inserire i dispositivi di controllo di accesso di rete specializzati al margine della rete perimetrale in modo che sia consentito solo il traffico desiderato oltre il dispositivo di sicurezza di rete e nella rete virtuale di Azure.

Le reti perimetrali sono utili perché permettono di concentrare le operazioni di gestione, monitoraggio, registrazione e creazione di report del controllo di accesso alla rete sui dispositivi al margine della rete virtuale di Azure. In questo caso, in genere si abilitano la prevenzione DDoS, i sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IPS), le regole e i criteri dei firewall, il filtro Web, il software antimalware per la rete e molto altro. I dispositivi di sicurezza di rete sono posizionati tra Internet e la rete virtuale di Azure e hanno un'interfaccia su entrambe le reti.

Nonostante questa sia la progettazione di base di una rete perimetrale, esistono molte diverse progettazioni di rete perimetrale, come ad esempio back to back, tri-homed, multihomed e altre.

Per tutte le distribuzioni di sicurezza elevata è consigliabile prendere in considerazione la distribuzione di una rete perimetrale per migliorare il livello di sicurezza di rete per le risorse di Azure.

Per altre informazioni sulle reti perimetrali e sulla relativa distribuzione in Azure, leggere l'articolo [Servizi cloud Microsoft e sicurezza di rete](best-practices-network-security.md).

## Evitare l'esposizione a Internet con collegamenti WAN dedicati
Molte organizzazioni hanno scelto la strada dell'IT ibrido. Nell'ambiente IT ibrido, alcune delle informazioni sulla società si trovano in Azure, mentre altre rimangono in locale. In molti casi alcuni componenti di un servizio verranno eseguiti in Azure mentre altri componenti resteranno in locale.

In uno scenario IT ibrido generalmente è presente un certo tipo di connettività cross-premise, che consente alla società di connettere le proprie reti locali alle reti virtuali di Azure. Sono disponibili due soluzioni di connettività cross-premise:

- Da sito a VPN
- ExpressRoute

[VPN da sito a sito](./vpn-gateway/vpn-gateway-site-to-site-create.md) rappresenta una connessione privata virtuale tra la rete locale e una rete virtuale di Azure. Questa connessione viene eseguita via Internet e consente di inviare le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La crittografia del tunnel viene eseguita usando la [modalità tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Nonostante la tecnologia VPN da sito a sito sia attendibile, affidabile e consolidata, il traffico all'interno del tunnel attraversa comunque Internet. Per di più, la larghezza di banda è relativamente vincolata a un massimo di circa 200 Mbps.

Se si richiede un livello di sicurezza o prestazioni eccezionale per le connessioni cross-premise, è consigliabile usare Azure ExpressRoute per la connettività cross-premise. ExpressRoute è un collegamento WAN dedicato tra il percorso locale o un provider di hosting di Exchange. Dal momento che si tratta di una connessione di telecomunicazioni, i dati non vengono trasmessi via Internet e quindi non sono esposti a potenziali rischi inerenti alle comunicazioni Internet.

Per altre informazioni sul funzionamento di Azure ExpressRoute e sulla relativa distribuzione, leggere l'articolo [Panoramica tecnica relativa a ExpressRoute](./expressroute/expressroute-introduction.md).

## Ottimizzare il tempo di attività e le prestazioni
La triade di riservatezza, integrità e disponibilità rappresenta il modello di sicurezza più influente del momento, realizzato nel principio CIA (Confidentiality, Integrity & Availability). La riservatezza riguarda la crittografia e la privacy, l'integrità consiste nell'assicurarsi che i dati non vengano modificati da personale non autorizzato e la disponibilità consiste nell'assicurarsi che gli utenti autorizzati riescano ad accedere alle informazioni per cui sono autorizzati. La carenza in una di queste aree rappresenta una potenziale violazione della sicurezza.

La disponibilità può essere considerata come una questione di tempi di attività e di prestazioni. Se un servizio non è attivo, non è possibile accedere alle informazioni. Se le prestazioni sono talmente insufficienti da rendere i dati inutilizzabili, sarà quindi possibile considerare che i dati non siano accessibili. Di conseguenza, dal punto di vista della sicurezza, è necessario fare del proprio meglio per assicurarsi che i servizi offrano sempre prestazioni e tempi di attività ottimali. Uno dei metodi più diffusi ed efficaci per migliorare le prestazioni e la disponibilità consiste nel ricorrere al bilanciamento del carico. Il bilanciamento del carico è un metodo di distribuzione del traffico di rete tra server che fanno parte di un servizio. Ad esempio, se dei server Web front-end fanno parte del servizio, è possibile usare il bilanciamento del carico per distribuire il traffico tra più server Web front-end.

Questa distribuzione del traffico aumenta la disponibilità perché se uno dei server Web non è più disponibile, il servizio di bilanciamento del carico interrompe l'invio di traffico a tale server, reindirizzandolo verso i server che sono ancora online. Il bilanciamento del carico favorisce anche le prestazioni, perché il sovraccarico di processore, rete e memoria per l'elaborazione delle richieste viene distribuito tra tutti server con carico bilanciato.

È consigliabile impiegare il bilanciamento del carico ogni volta possibile e quando adeguato ai servizi. L'adeguatezza verrà presa in considerazione nelle sezioni seguenti. A livello di rete virtuale di Azure, Azure offre che tre opzioni di bilanciamento del carico principali:

- Bilanciamento del carico basato su HTTP
- Bilanciamento del carico esterno
- Bilanciamento del carico interno

## Bilanciamento del carico basato su HTTP
Il bilanciamento del carico basato su HTTP consente di decidere a quale server inviare le connessioni che usano le caratteristiche del protocollo HTTP. Azure offre un servizio di bilanciamento del carico HTTP il cui nome è Gateway applicazione.

È consigliabile usare Gateway applicazione di Azure nei casi seguenti:

- Applicazioni che necessitano delle richieste provenienti dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end, ad esempio applicazioni carrello e server di posta Web.
- Applicazioni che hanno la funzione di liberare le server farm Web dal sovraccarico della terminazione SSL sfruttando la funzionalità [Offload SSL](https://f5.com/glossary/ssl-offloading) del Gateway applicazione.
- Applicazioni, ad esempio la rete per la distribuzione di contenuti, che necessitano che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.

Per altre informazioni sul funzionamento di Gateway applicazione di Azure e su come è possibile usarlo nelle distribuzioni, leggere l'articolo [Panoramica del gateway applicazione](./application-gateway/application-gateway-introduction.md).

## Bilanciamento del carico esterno
Il bilanciamento del carico esterno si verifica quando le connessioni in ingresso da Internet hanno un carico bilanciato tra i server che si trovano in una rete virtuale di Azure. Il servizio di bilanciamento del carico esterno di Azure può fornire questa funzionalità ed è consigliabile usarla quando non sono necessarie sessioni permanenti o l'offload SSL.

A differenza del servizio di bilanciamento del carico basato su HTTP, il bilanciamento del carico esterno usa informazioni livello di rete e trasporto del modello di rete OSI per decidere su quali server bilanciare il carico della connessione.

È consigliabile usare il bilanciamento del carico esterno ogni volta che le [applicazioni senza stato](http://whatis.techtarget.com/definition/stateless-app) accettano le richieste in ingresso da Internet.

Per altre informazioni sul funzionamento del servizio di bilanciamento del carico esterno di Azure e su come è possibile distribuirlo, leggere l'articolo [Introduzione su come creare un servizio di bilanciamento del carico per Internet in Azure Resource Manager](./load-balancer/load-balancer-get-started-internet-arm-ps.md).

## Bilanciamento del carico interno
Il bilanciamento del carico interno è simile al bilanciamento del carico esterno e usa lo stesso meccanismo per bilanciare il carico delle connessioni ai server su cui si basano. L'unica differenza è che il servizio di bilanciamento del carico in questo caso accetta connessioni da macchine virtuali che non si trovano su Internet. Nella maggior parte dei casi, le connessioni che sono accettate per il bilanciamento del carico possono essere avviate da dispositivi nella rete virtuale di Azure.

È consigliabile usare il bilanciamento del carico interno per gli scenari che trarranno vantaggio da questa funzionalità, ad esempio quando è necessario bilanciare il carico delle connessioni a SQL Server o ai server Web interni.

Per altre informazioni sul servizio di funzionamento del bilanciamento del carico interno di Azure e su come è possibile distribuirlo, leggere l'articolo [Introduzione su come creare un servizio di bilanciamento del carico per Internet in Gestione risorse con PowerShell](./load-balancer/load-balancer-get-started-internet-arm-ps.md#update-an-existing-load-balancer).

## Usare il bilanciamento del carico globale
Il cloud computing pubblico rende possibile distribuire applicazioni distribuite a livello globale con componenti situati nei data center di tutto il mondo. Ciò è possibile in Microsoft Azure a causa della presenza del data center globale di Azure. A differenza delle tecnologie di bilanciamento del carico menzionate in precedenza, il bilanciamento del carico globale consente di rendere i servizi disponibili anche quando gli interi data center potrebbero non essere disponibili.

È possibile ottenere questo tipo di bilanciamento del carico globale in Azure sfruttando [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/). Gestione traffico rende possibile bilanciare il carico delle connessioni ai servizi in base alla posizione dell'utente.

Ad esempio, se l'utente effettua una richiesta al servizio dall'Unione Europea, la connessione viene indirizzata ai servizi che si trovano in un data center dell'Unione europea. Questa parte bilanciamento del carico globale di Gestione traffico consente di migliorare le prestazioni perché la connessione al data center più vicino è più veloce rispetto alla connessione ai data center più lontani.

Relativamente alla disponibilità, il bilanciamento del carico globale garantisce che il servizio sia disponibile anche se un intero data center dovesse diventare disponibili.

Ad esempio, se un data center di Azure dovesse non essere più disponibile a causa di motivi ambientali o di interruzioni del servizio (ad esempio guasti della rete regionale), le connessioni al servizio verranno reindirizzate al più vicino data center online. Il bilanciamento del carico globale viene effettuato grazie all'uso dei criteri DNS che è possibile creare in Gestione traffico.

È consigliabile usare Gestione traffico per qualsiasi soluzione cloud sviluppata il cui ambito sia ampiamente distribuito tra più aree e che richieda il massimo livello di tempo di attività possibile.

Per altre informazioni su Gestione traffico di Azure e su come eseguirne la distribuzione, leggere l'articolo [Gestione traffico di Azure](./traffic-manager/traffic-manager-overview.md).

## Disabilitare l'accesso RDP/SSH alle macchine virtuali di Azure
È possibile raggiungere le macchine virtuali di Azure usando i protocolli [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Questi protocolli consentono di gestire le macchine virtuali da postazioni remote e sono standard nel computing dei data center.

Il potenziale problema di sicurezza comportato dall'uso di questi protocolli via Internet è che gli utenti malintenzionati possono usare varie tecniche di [attacco di forza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) per ottenere l'accesso alle macchine virtuali di Azure. Una volta che gli utenti malintenzionati avranno ottenuto l'accesso, potranno usare la macchina virtuale come punto di avvio per compromettere gli altri computer nella rete virtuale di Azure o persino per attaccare i dispositivi di rete all'esterno di Azure.

Per questo motivo, è consigliabile disabilitare l'accesso diretto RDP e SSH alle macchine virtuali di Azure da Internet. Dopo aver disabilitato l'accesso diretto RDP e SSH da Internet, sono disponibili altre opzioni per accedere a queste macchine virtuali per la gestione remota:

- VPN da punto a sito
- Da sito a VPN
- ExpressRoute

[VPN da punto a sito](./vpn-gateway/vpn-gateway-point-to-site-create.md) è un altro termine per indicare una connessione client/server VPN con accesso remoto. Una VPN da punto a sito consente a un singolo utente di connettersi a una rete virtuale di Azure via Internet. Dopo aver stabilito la connessione da punto a sito, l'utente riuscirà a usare RDP o SSH per connettersi a tutte le macchine virtuali presenti nella rete virtuale Azure e a cui l'utente si è connesso con VPN da punto a sito. Ciò presuppone che l'utente sia autorizzato a raggiungere tali macchine virtuali.

La VPN da punto a sito è più sicura delle connessioni dirette RDP o SSH perché l'utente deve autenticarsi due volte prima di potersi connettere a una macchina virtuale. In primo luogo, l'utente deve eseguire l'autenticazione (ed essere autorizzato) per stabilire la connessione VPN da punto a sito; in secondo luogo, l'utente deve eseguire l'autenticazione (ed essere autorizzato) per stabilire la sessione RDP o SSH.

Una [VPN da sito a sito](./vpn-gateway/vpn-gateway-site-to-site-create.md) connette un'intera rete a un'altra rete via Internet. È possibile usare una VPN da sito a sito per connettere la rete locale a una rete virtuale di Azure. Se si distribuisce una VPN da sito a sito, gli utenti nella rete locale riusciranno a connettersi alle macchine virtuali nella rete virtuale di Azure usando il protocollo RDP o SSH con una connessione VPN da sito a sito e non sarà necessario consentire l'accesso diretto RDP o SSH via Internet.

È anche possibile usare un collegamento WAN dedicato per fornire funzionalità simili alla VPN da sito a sito. Le differenze principali sono le seguenti: 1. Il collegamento WAN dedicato non deve attraversare Internet 2. I collegamenti WAN dedicati sono in genere più stabili e ad alte prestazioni. Azure offre una soluzione di collegamento WAN dedicata sotto forma di [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## Abilitare il Centro sicurezza di Azure
Il Centro sicurezza di Azure aiuta a impedire, rilevare e rispondere alle minacce offrendo visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza di Azure aiuta a ottimizzare e monitorare la sicurezza di rete offrendo:

- Suggerimenti per la sicurezza di rete
- Monitoraggio dello stato della configurazione della sicurezza di rete
- Avvisi relativi alle minacce basate sulla rete a livello di endpoint e di rete

È consigliabile abilitare il Centro sicurezza di Azure per tutte le distribuzioni di Azure.

Per altre informazioni sul Centro sicurezza di Azure e su come abilitarlo per le distribuzioni, leggere l'articolo [Introduzione al Centro sicurezza di Azure](./security-center/security-center-intro.md).

## Estendere il data center in Azure in modo sicuro
Molte organizzazioni IT di classe enterprise mirano a espandersi nel cloud piuttosto che aumentare le dimensioni dei data center locali. Questa espansione rappresenta un'estensione dell'infrastruttura IT esistente nel cloud pubblico. Grazie all'uso di più opzioni di connettività cross-premise è possibile gestire le reti virtuali di Azure semplicemente come un'altra subnet nella propria infrastruttura di rete locale.

È tuttavia necessario affrontare prima una serie di problemi di pianificazione e progettazione. Ciò è particolarmente importante nell'area della sicurezza di rete. Uno dei modi migliori per comprendere come avvicinarsi a una simile progettazione è guardare un esempio.

Microsoft ha creato il [Diagramma dell'architettura di riferimento delle estensioni del data center](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84#content) e materiale collaterale per comprendere quale aspetto avrebbe un'estensione del data center. In tal modo, si avrà un esempio di implementazione di riferimento da consultare per pianificare e progettare un'estensione del data center aziendale sicura nel cloud. È consigliabile rivedere questo documento per farsi un'idea dei componenti principali di una soluzione sicura.

Per altre informazioni su come estendere in modo sicuro il data center in Azure, guardare il video su come [estendere il data center in Microsoft Azure](https://www.youtube.com/watch?v=Th1oQQCb2KA).

<!---HONumber=AcomDC_0525_2016-->
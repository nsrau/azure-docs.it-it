<properties
   pageTitle="Panoramica della sicurezza di rete di Azure | Microsoft Azure"
   description="Questo articolo rende più facilmente comprensibile ciò che Microsoft Azure può offrire nell'area della sicurezza di rete. Vengono fornite spiegazioni di base per i principali concetti e requisiti della sicurezza di rete, nonché informazioni sulle funzionalità offerte da Azure in ognuna di queste aree."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="terrylan"/>

# Panoramica della sicurezza di rete di Azure

Microsoft Azure include una solida infrastruttura di rete per supportare i requisiti di connettività di applicazioni e servizi. La connettività di rete è possibile tra le risorse disponibili in Azure, le risorse locali e quelle ospitate in Azure, nonché da e verso Internet e Azure.

L'obiettivo di questo articolo è facilitare la comprensione di ciò che Microsoft Azure può offrire nell'area della sicurezza di rete. Vengono fornite spiegazioni di base per i principali concetti e requisiti della sicurezza di rete, oltre a informazioni sulle funzionalità offerte da Azure in ognuna di queste aree. Sono disponibili numerosi collegamenti ad altro contenuto che consente di approfondire la conoscenza delle aree di interesse.

Questa panoramica della sicurezza di rete sarà incentrata sugli argomenti seguenti:
- Rete di Azure
- Controllo di accesso alla rete
- Accesso remoto sicuro e connettività cross-premise
- Disponibilità
- Registrazione
- Risoluzione dei nomi
- Architettura della rete perimetrale
- Centro sicurezza di Azure

## Rete di Azure

La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure. Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni rete virtuale di Azure logica è isolata da tutte le altre reti virtuali di Azure. Ciò garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

Per altre informazioni sulle reti virtuali di Azure, vedere [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).

## Controllo di accesso alla rete
Il controllo di accesso alla rete comporta la limitazione della connettività da e verso subnet o dispositivi specifici all'interno di una rete virtuale di Azure. L'obiettivo del controllo di accesso alla rete consiste nel garantire che le macchine virtuali e i servizi siano accessibili solo agli utenti e ai dispositivi a cui si vuole concedere l'accesso. I controlli di accesso si basano su decisioni secondo cui le connessioni da e verso una macchina virtuale o un servizio sono consentite o negate.

Azure supporta diversi tipi di controllo di accesso alla rete. Sono inclusi:
- Controllo a livello rete
- Controllo di route e tunneling forzato
- Appliance di sicurezza di rete virtuale

### Controllo a livello rete
Qualsiasi distribuzione sicura richiede alcune misure di controllo di accesso alla rete. L'obiettivo del controllo di accesso alla rete è garantire che le macchine virtuali e i servizi di rete eseguiti in tali macchine virtuali possano comunicare solo con altri dispositivi in rete con i quali è necessario che comunichino, mentre tutti gli altri tentativi di connessione vengono bloccati.

Se è necessario un controllo di accesso di base a livello di rete, basato sull'indirizzo IP e sul protocollo TCP o UDP, è possibile usare i gruppi di sicurezza di rete. Un gruppo di sicurezza di rete (NSG) è un firewall di filtro dei pacchetti con stato di base e consente di controllare l'accesso sulla base di [5 tuple](https://www.techopedia.com/definition/28190/5-tuple). Gli NSG non forniscono ispezione a livello dell'applicazione o controlli di accesso autenticato.

Per altre informazioni sui **gruppi di sicurezza di rete**, vedere l'articolo [Che cos'è un gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

### Controllo di route e tunneling forzato
La possibilità di controllare il comportamento di routing nella rete virtuale di Azure è una funzionalità critica per il controllo di accesso e la sicurezza di rete. Se il routing non è configurato correttamente, è possibile che le applicazioni e i servizi ospitati nella macchina virtuale si connettano a dispositivi a cui non dovrebbero connettersi, inclusi i dispositivi di proprietà e gestita da potenziali utenti malintenzionati.

La rete di Azure supporta la personalizzazione del comportamento di routing per il traffico di rete nelle reti virtuali di Azure. Ciò consente di modificare le voci predefinite della tabella di routing nella rete virtuale di Azure. Il controllo del comportamento di routing consente di assicurarsi che l'ingresso o l'uscita di tutto il traffico da un determinato dispositivo o gruppo di dispositivi nella rete virtuale di Azure avvenga attraverso un percorso specifico.

Ad esempio, nella rete virtuale di Azure potrebbe essere presente un'appliance di sicurezza di rete virtuale. Si vuole essere certi che tutto il traffico da e verso la rete virtuale di Azure passi attraverso l'appliance di sicurezza virtuale. È possibile farlo configurando le [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) in Azure.

[Il tunneling forzato](https://www.petri.com/azure-forced-tunneling) è un meccanismo utilizzabile per assicurarsi che ai servizi non sia consentito stabilire una connessione a dispositivi in Internet. Questo approccio è diverso dall'accettare connessioni in ingresso e rispondere alle richieste. I server Web front-end devono rispondere alle richieste dagli host in Internet e di conseguenza il traffico originato da Internet è consentito in ingresso per questi server Web che sono autorizzati a rispondere.

Non si vuole invece consentire a un server Web front-end di avviare una richiesta in uscita. Tali richieste possono rappresentare un rischio per la sicurezza, perché queste connessioni potrebbero essere usate per il download di malware. Anche se si vuole che i server front-end possano avviare richieste in uscita verso Internet, è consigliabile imporre il passaggio tramite i proxy Web locali per poter sfruttare le funzionalità di filtro e registrazione degli URL.

Per evitare tutto questo, è preferibile usare invece il tunneling forzato. Quando si abilita il tunneling forzato, a tutte le connessioni verso Internet viene imposto il passaggio attraverso il gateway locale. È possibile configurare il tunneling forzato sfruttando le route definite dall'utente.

Per altre informazioni sulle **route definite dall'utente**, vedere l'articolo [Cosa sono le route definite dall'utente e l'inoltro IP](../virtual-network/virtual-networks-udr-overview.md).

### Appliance di sicurezza di rete virtuale
Mentre i gruppi di sicurezza di rete, le route definite dall'utente e il tunneling forzato offrono un livello di sicurezza ai livelli rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), a volte può essere consigliabile abilitare la sicurezza a livelli superiori rispetto alla rete.

Ad esempio, i requisiti di sicurezza possono includere:
- Autenticazione e autorizzazione prima di consentire l'accesso all'applicazione
- Rilevamento delle intrusioni e relativa risposta
- Ispezione a livello dell'applicazione per i protocolli di alto livello
- Filtro degli URL
- Antimalware e antivirus a livello di rete
- Protezione anti-robot
- Controllo di accesso all'applicazione
- Protezione da attacchi DDoS aggiuntiva, oltre alla protezione DDoS fornita l'infrastruttura di Azure

È possibile accedere a queste funzionalità di sicurezza di rete avanzate usando una soluzione dei partner di Azure. Le soluzioni di sicurezza di rete più recenti, offerte dai partner di Azure, sono disponibili in [Azure Marketplace](https://azure.microsoft.com/marketplace/) cercando "sicurezza" e "sicurezza di rete".

## Accesso remoto sicuro e connettività cross-premise
Le operazioni di installazione, configurazione e gestione delle risorse di Azure devono essere eseguite in modalità remota. È anche consigliabile distribuire soluzioni [IT ibride](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) che includono componenti sia locali che nel cloud pubblico di Azure. Questi scenari richiedono l'accesso remoto sicuro.

Rete di Azure supporta gli scenari di accesso remoto sicuro seguenti:
- Connettere singole workstation a una rete virtuale di Azure
- Connettere la rete locale a una rete virtuale di Azure tramite una VPN
- Connettere la rete locale a una rete virtuale di Azure tramite un collegamento WAN dedicato
- Connettere tra di esse le reti virtuali di Azure

### Connettere singole workstation a una rete virtuale di Azure
A volte è opportuno consentire a singoli sviluppatori o al personale operativo di gestire le macchine virtuali e i servizi in Azure. Ad esempio, è necessario accedere a una macchina virtuale in una rete virtuale di Azure e i criteri di sicurezza non consentono l'accesso remoto RDP o SSH a singole macchine virtuali. In questo caso, è possibile usare una connessione VPN da punto a sito.

La connessione VPN da punto a sito usa il protocollo [SSTP su VPN ](https://technet.microsoft.com/library/cc731352.aspx) che consente di configurare una connessione privata e sicura tra l'utente e la rete virtuale di Azure. Una volta stabilita la connessione VPN, l'utente potrà usare RDP o SSH sul collegamento VPN a una macchina virtuale qualsiasi nella rete virtuale di Azure, presupponendo che l'utente possa autenticarsi e sia autorizzato.

Per altre informazioni sulla connettività **VPN da punto a sito**, vedere l'articolo [Configurare una connessione da punto a sito a una rete virtuale con PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

### Connettere la rete locale a una rete virtuale di Azure tramite una VPN
È possibile connettere l'intera rete aziendale, o parti di essa, a una rete virtuale di Azure. Questo approccio è comune negli scenari IT ibridi in cui le aziende [estendono il data center locale in Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). In molti casi le aziende ospitano parti di un servizio in Azure e parti in locale, ad esempio quando una soluzione include server Web front-end in Azure e database back-end in locale. Questo tipo di connessioni "cross-premise" rende anche più sicura la gestione delle risorse residenti in Azure e abilita scenari come l'estensione di controller di dominio Active Directory in Azure.

A questo scopo è possibile usare una [VPN da sito a sito](https://www.techopedia.com/definition/30747/site-to-site-vpn). La differenza tra una VPN da sito a sito e una VPN da punto sito è data dal fatto che una VPN da punto a sito connette un singolo dispositivo a una rete virtuale di Azure, mentre una VPN da sito a sito connette un'intera rete, ad esempio la rete locale, a una rete virtuale di Azure. Le VPN da sito a sito a una rete virtuale di Azure usano il protocollo VPN in modalità tunnel IPsec altamente sicuro.

Per altre informazioni sulle **VPN da sito a sito in Azure**, vedere l'articolo [Creare una rete virtuale con una connessione VPN da sito a sito tramite il portale di Azure e Azure Resource Manager](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Per altre informazioni su come pianificare la **connettività cross-premise basata su VPN**, vedere [Pianificazione e progettazione per il gateway VPN](../vpn-gateway/vpn-gateway-plan-design.md).

### Connettere la rete locale a una rete virtuale di Azure tramite un collegamento WAN dedicato
Le connessioni VPN da punto a sito e da sito a sito consentono di abilitare la connettività cross-premise in modo efficace. Alcune organizzazioni ritengono tuttavia che queste connessioni presentino gli svantaggi seguenti:
- Le connessioni VPN spostano dati attraverso Internet, quindi sono esposte ai potenziali problemi di sicurezza che caratterizzano lo spostamento di dati su una rete pubblica. Per le connessioni Internet non è inoltre possibile garantire l'affidabilità e la disponibilità.
- Le connessioni VPN alle reti virtuali di Azure possono essere considerate vincolate dalla larghezza di banda per alcune applicazioni e scopi, perché raggiungono una velocità massima di circa 200 Mbps.

Le organizzazioni che necessitano del massimo livello di sicurezza e disponibilità per le connessioni cross-premise usano in genere collegamenti WAN dedicati per connettersi a siti remoti. Azure consente di usare un collegamento WAN dedicato, che può essere usato per connettere la rete locale a una rete virtuale di Azure e viene abilitato tramite Azure ExpressRoute.

Per altre informazioni sui **collegamenti WAN dedicati** tra il percorso locale e le reti virtuali di Azure, vedere l'articolo [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

### Connettere tra di esse le reti virtuali di Azure
Per le distribuzioni si possono usare molte reti virtuali di Azure. Esistono molti motivi per cui è possibile farlo. Uno di questi può essere la semplificazione della gestione, altri possono riguardare la sicurezza. Indipendentemente dalla motivazione o dalla logica secondo cui le risorse vengono inserite in reti virtuali di Azure diverse, a volte può essere opportuno stabilire una connessione tra le risorse disponibili in ognuna delle reti.

Una soluzione può consistere nel connettere i servizi in una rete virtuale di Azure a quelli in un'altra rete virtuale di Azure con un "loopback" tramite Internet. La connessione viene avviata in una rete virtuale di Azure, passa attraverso Internet e quindi ritorna alla rete virtuale di Azure di destinazione. Questa opzione espone la connessione ai problemi di sicurezza che caratterizzano tutte le comunicazioni basate su Internet.

Un'opzione migliore può essere la creazione di una VPN da sito a sito da rete virtuale di Azure a rete virtuale di Azure. Questa VPN da sito a sito da rete virtuale di Azure a rete virtuale di Azure usa lo stesso protocollo in [modalità tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx) della connessione VPN da sito a sito cross-premise citata sopra.

Il vantaggio che deriva dall'uso di una VPN da sito a sito da rete virtuale di Azure a rete virtuale di Azure consiste nel fatto che la connessione VPN viene stabilita sull'infrastruttura di rete di Azure, ovvero non si connette tramite Internet. Questo approccio fornisce un livello di sicurezza aggiuntivo rispetto alle VPN da sito a sito che si connettono tramite Internet.

Per altre informazioni sulle VPN da sito a sito da rete virtuale di Azure a rete virtuale di Azure, vedere l'articolo [Configurare una connessione da VNet a VNet tramite Azure Resource Manager e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

## Disponibilità
La disponibilità è un componente fondamentale di qualsiasi programma di sicurezza. Se gli utenti e i sistemi non possono accedere alle risorse necessarie attraverso la rete, il servizio può essere considerato compromesso. Azure offre tecnologie di rete che supportano i meccanismi a disponibilità elevata seguenti:
- Bilanciamento del carico basato su HTTP
- Bilanciamento del carico a livello di rete
- Bilanciamento del carico globale

Il meccanismo di bilanciamento del carico è progettato per distribuire equamente le connessioni tra più dispositivi. Ecco gli obiettivi del processo di bilanciamento del carico:
- Aumentare la disponibilità: quando si bilancia il carico delle connessioni tra più dispositivi, uno o più dispositivi possono diventare non disponibili e i servizi in esecuzione nei dispositivi rimasti online possono continuare a gestire il contenuto dal servizio.
- Migliorare le prestazioni: quando si bilancia il carico tra più dispositivi, un singolo dispositivo non deve assorbire l'intero carico di elaborazione. Al contrario, le richieste di elaborazione e memoria per gestire il contenuto vengono distribuite tra più dispositivi.

### Bilanciamento del carico basato su HTTP
Le organizzazioni che eseguono servizi basati sul Web spesso preferiscono implementare un servizio di bilanciamento del carico basato su HTTP a monte di tali servizi Web, per garantire livelli di prestazioni e disponibilità elevata adeguati. A differenza dei servizi di bilanciamento del carico tradizionali basati sulla rete, le decisioni relative al bilanciamento del carico prese dai servizi di bilanciamento del carico basati su HTTP fanno affidamento sulle caratteristiche del protocollo HTTP, non sui protocolli a livello trasporto e rete.

Per fornire il bilanciamento del carico basato su HTTP per i servizi basati sul Web, Azure fornisce il gateway applicazione di Azure. Il gateway applicazione di Azure supporta:
- Bilanciamento del carico basato su HTTP: le decisioni di bilanciamento del carico vengono prese in base a una caratteristica speciale del protocollo HTTP.
- Affinità di sessione basata su cookie: questa funzionalità garantisce che le connessioni stabilite a uno dei server controllati dal servizio di bilanciamento del carico restino inalterate tra il client e il server. In questo modo si assicura la stabilità delle transazioni.
- Offload SSL: quando viene stabilita una connessione client con il servizio di bilanciamento del carico, la sessione tra il client e il servizio di bilanciamento del carico viene crittografata con il protocollo HTTPS (SSL/). Per migliorare le prestazioni, è tuttavia possibile fare in modo che la connessione tra il servizio di bilanciamento del carico e il server Web controllato dal servizio di bilanciamento del carico usi il protocollo HTTP (senza crittografia). Questa modalità è definita "offload SSL", perché i server Web controllati dal servizio di bilanciamento del carico non sono soggetti al sovraccarico del processore interessato dalla crittografia e quindi saranno in grado di soddisfare le richieste più rapidamente.
- Routing del contenuto basato su URL: questa funzionalità consente al servizio di bilanciamento del carico di scegliere dove inoltrare le connessioni in base all'URL di destinazione, offrendo quindi maggiore flessibilità rispetto alle soluzioni che prendono decisioni sul bilanciamento del carico in base agli indirizzi IP.

Per altre informazioni sul gateway applicazione di Azure, vedere l'articolo [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md).

### Bilanciamento del carico a livello di rete
A differenza del bilanciamento del carico basato su HTTP, il bilanciamento del carico a livello di rete prende decisioni in base all'indirizzo IP e ai numeri di porta (TCP o UDP). È possibile sfruttare i vantaggi del bilanciamento del carico a livello di rete in Azure tramite Azure Load Balancer. Alcune delle caratteristiche principali di Azure Load Balancer includono:
- Bilanciamento del carico a livello di rete in base all'indirizzo IP e ai numeri di porta.
- Supporto per qualsiasi protocollo a livello dell'applicazione.
- Bilanciamento del carico tra macchine virtuali di Azure e istanze del ruolo di Servizi cloud.
- Può essere usato per applicazioni e macchine virtuali con connessione Internet (bilanciamento del carico esterno) e senza connessione Internet (bilanciamento del carico interno).
- Monitoraggio di endpoint, che viene usato per determinare se i servizi controllati dal servizio di bilanciamento del carico sono diventati non disponibili.

Per altre informazioni sul bilanciamento del carico esterno di Azure Load Balancer, vedere l'articolo [Bilanciamento del carico Internet tra più macchine virtuali o servizi](../load-balancer/load-balancer-internet-overview.md).

Per altre informazioni sul bilanciamento del carico interno di Azure Load Balancer, vedere l'articolo [Panoramica del bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md).

### Bilanciamento del carico globale
Alcune organizzazioni vogliono poter contare sul massimo livello di disponibilità possibile. Un modo per raggiungere questo obiettivo consiste nell'ospitare applicazioni in data center distribuiti a livello globale. Quando un'applicazione è ospitata in data center dislocati in tutto il mondo, è possibile che un'intera area geopolitica diventi non disponibile, ma che l'applicazione continui a essere operativa.

Oltre ai vantaggi a livello di disponibilità che si ottengono ospitando le applicazioni in data center distribuiti a livello globale, è anche possibile ottenere vantaggi in termini di prestazioni. Questi ultimi si possono ottenere usando un meccanismo che indirizza le richieste per il servizio al data center che si trova più vicino al dispositivo che invia la richiesta.

Il bilanciamento del carico a livello globale può fornire entrambi questi vantaggi. In Azure è possibile ottenere i vantaggi del bilanciamento del carico globale tramite Gestione traffico di Azure.

Per altre informazioni sul bilanciamento del traffico globale con Gestione traffico di Azure, vedere l'articolo [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md).

## Registrazione
La registrazione a livello di rete è una funzione chiave per qualsiasi scenario di sicurezza di rete. In Azure è possibile registrare le informazioni ottenute per i gruppi di sicurezza di rete per avere informazioni di registrazione a livello di rete. Con la registrazione dei gruppi di sicurezza di rete si ottengono informazioni da:
- Log di controllo: vengono usati per visualizzare tutte le operazioni inviate alle sottoscrizioni di Azure. Questi log sono abilitati per impostazione predefinita e possono essere usati nel portale di Azure.
- Log eventi: forniscono informazioni sulle regole applicate ai gruppi di sicurezza di rete.
- Log contatori: consentono di sapere quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico.

Per visualizzare e analizzare questi log, è anche possibile usare [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), un potente strumento di visualizzazione dei dati.

Per altre informazioni sulla registrazione dei gruppi di sicurezza di rete, vedere l'articolo [Log Analytics per i gruppi di sicurezza di rete](../virtual-network/virtual-network-nsg-manage-log.md).

## Risoluzione dei nomi
La risoluzione dei nomi è una funzione critica per tutti i servizi ospitati in Azure. Dal punto di vista della sicurezza la compromissione della funzione di risoluzione dei nomi può consentire a un utente malintenzionato di reindirizzare le richieste dai siti dell'utente al suo sito. La sicurezza della risoluzione dei nomi è un requisito per tutti i servizi cloud ospitati.

Esistono due tipi di risoluzione dei nomi che occorre considerare:
- Risoluzione dei nomi interna: viene usata dai servizi nelle reti virtuali di Azure, nelle reti locali o in entrambe. I nomi usati per la risoluzione dei nomi interna non sono accessibili tramite Internet. Per una sicurezza ottimale, è importante che lo schema di risoluzione dei nomi interna non sia accessibile a utenti esterni.
- Risoluzione dei nomi esterna: viene usata da utenti e dispositivi esterni alla rete locale e alle reti virtuali di Azure. Questi sono i nomi visibili su Internet e vengono usati per connettersi direttamente ai servizi basati sul cloud.

Per la risoluzione dei nomi interna sono disponibili due opzioni:
- Server DNS della rete virtuale di Azure: quando si crea una nuova rete virtuale di Azure, viene creato automaticamente un server DNS. Questo server DNS può risolvere i nomi dei computer che si trovano in tale rete virtuale di Azure. Il server DNS non è configurabile e viene gestito dal servizio di gestione dell'infrastruttura di Azure, rendendo così sicura la soluzione di risoluzione dei nomi.
- Server DNS personalizzato: è possibile scegliere di inserire un server DNS a propria scelta nella rete virtuale di Azure. Può essere un server DNS integrato con Active Directory oppure una soluzione server DNS fornita da un partner di Azure, che è possibile ottenere da Azure Marketplace.

Per altre informazioni sul **server DNS della rete virtuale Azure**, vedere l'articolo [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md).

Per altre informazioni sull'uso di **server DNS personalizzati** in una rete virtuale di Azure, vedere l'articolo [Gestire server DNS usati da una rete virtuale](../virtual-network/virtual-networks-manage-dns-in-vnet.md).

Per la risoluzione DNS esterna sono disponibili due opzioni:
- Ospitare il server DNS personalizzato esterno in locale.
- Ospitare il server DNS personalizzato esterno presso un provider di servizi.

Molte grandi organizzazioni ospitano i propri server DNS in locale. Lo fanno avendo l'esperienza di rete e la presenza globale necessarie per scegliere questo approccio.

Nella maggior parte dei casi è consigliabile ospitare i servizi di risoluzione dei nomi DNS presso un provider di servizi. I provider di servizi hanno l'esperienza di rete e la presenza globale per garantire una disponibilità molto elevata per i servizi di risoluzione dei nomi. La disponibilità è essenziale per i servizi DNS, perché se la risoluzione dei nomi non riesce, nessuno sarà in grado di raggiungere i servizi con connessione Internet.

Azure offre una soluzione DNS esterna a disponibilità elevata e ad alte prestazioni sotto forma di DNS Azure. Questa soluzione di risoluzione dei nomi esterna sfrutta l'infrastruttura DNS di Azure in tutto il mondo. Consente di ospitare i domini in Azure con le credenziali, le API, gli strumenti e la fatturazione usati per gli altri servizi di Azure. Come parte di Azure, eredita anche i controlli di sicurezza avanzati integrati nella piattaforma.

Per altre informazioni sul servizio DNS di Azure, vedere [Panoramica di DNS di Azure](../dns/dns-overview.md).

## Architettura della rete perimetrale
Molte organizzazioni aziendali usano le reti perimetrali per segmentare le proprie reti e creare una zona buffer tra Internet e i servizi locali. La parte rete perimetrale della rete è considerata una zona a bassa sicurezza e nessun asset critico viene inserito in quel segmento di rete. In genere, sono presenti dispositivi di sicurezza di rete con un'interfaccia di rete sul segmento di rete perimetrale e un'altra interfaccia di rete connessa a una rete con le macchine virtuali e i servizi che accettano connessioni in ingresso da Internet.

Esistono numerose varianti di progettazione per una rete perimetrale e la decisione di distribuire una rete perimetrale e quindi quale tipo usare è basata su requisiti di sicurezza di rete specifici.

Per altre informazioni sulle progettazioni di reti perimetrali in Azure, vedere l'articolo [Servizi cloud Microsoft e sicurezza di rete](../best-practices-network-security.md).

## Centro sicurezza di Azure
Il Centro sicurezza consente di prevenire, rilevare e rispondere alle minacce e fornisce livelli avanzati di visibilità e controllo della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il Centro sicurezza di Azure aiuta a ottimizzare e monitorare la sicurezza di rete offrendo:
- Suggerimenti per la sicurezza di rete
- Monitoraggio dello stato della configurazione della sicurezza di rete
- Avvisi relativi alle minacce basate sulla rete a livello di endpoint e di rete

Per altre informazioni sul Centro sicurezza di Azure, vedere l'articolo [Introduzione al Centro sicurezza di Azure](../security-center/security-center-intro.md).

<!---HONumber=AcomDC_0518_2016-->
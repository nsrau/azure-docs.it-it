<properties
   pageTitle="Servizi cloud Microsoft e sicurezza della rete | Microsoft Azure"
   description="Informazioni su alcune funzionalità chiave disponibili in Azure per creare ambienti di rete sicuri."
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# Servizi cloud Microsoft e sicurezza della rete

I Servizi cloud Microsoft offrono servizi e infrastruttura su scala elevata, capacità di livello aziendale e molte opzioni per la connettività ibrida. I clienti possono scegliere di accedere a questi servizi tramite Internet o con ExpressRoute, che offre connettività di rete privata. La piattaforma Microsoft Azure consente ai clienti di estendere con facilità la propria infrastruttura nel cloud e di sviluppare architetture a più livelli. Per integrare i servizi Microsoft, le terze parti possono abilitare capacità avanzate offrendo servizi di sicurezza e dispositivi virtuali. Questo white paper fornisce una panoramica dei problemi relativi a sicurezza e architettura che i clienti dovrebbero tenere in considerazione quando usano i Servizi cloud Microsoft con accesso tramite ExpressRoute, oltre a creare servizi sicuri nella rete virtuale di Microsoft Azure.

## Avvio veloce
Il grafico logico seguente può indirizzare a un esempio specifico delle molte tecniche di sicurezza disponibili con la piattaforma Microsoft Azure. Usare questo grafico per trovare rapidamente ciò che serve oppure leggere il documento dall'inizio per ottenere una spiegazione di molte delle opzioni disponibili. ![Diagramma di flusso sulle opzioni di sicurezza][0]

[Esempio 1: Creare una rete perimetrale per proteggere le applicazioni con gruppi di sicurezza di rete](#example-1-build-a-simple-dmz-with-nsgs)</br> [Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br> [Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br> [Esempio 4: Aggiunta di una connessione ibrida con VPN per dispositivi virtuali da sito a sito](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br> [Esempio 5: Aggiunta di una connessione ibrida con una VPN di gateway di Azure da sito a sito](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br> [Esempio 6: Aggiunta di una connessione ibrida con ExpressRoute](#example-6-adding-a-hybrid-connection-with-expressroute)</br> Esempi relativi all'aggiunta di connessioni da rete virtuale a rete virtuale, disponibilità elevata e concatenamento dei servizi verranno aggiunti a questo documento nei prossimi mesi.

## Conformità Microsoft e protezione dell'infrastruttura
Microsoft ha assunto una posizione di leadership nel supporto delle iniziative di conformità necessarie per i clienti aziendali. Queste sono alcune certificazioni di conformità ottenute da Azure: ![Notifiche sulla conformità di Azure][1]

Per altre informazioni, vedere [http://azure.microsoft.com/support/trust-center/compliance/](https://azure.microsoft.com/support/trust-center/compliance/)

Microsoft offre un approccio completo per la protezione dell'infrastruttura cloud necessaria per l'esecuzione di servizi globali su scala elevatissima. L'infrastruttura cloud Microsoft include personale addetto a hardware, software, reti, amministrazioni e operatività, oltre ai data center fisici.

![Funzionalità di sicurezza di Azure][2]

L'approccio indicato offre una base sicura che consente ai clienti di distribuire i propri servizi nel cloud Microsoft. Il passaggio successivo consiste nella progettazione e nella creazione di un'architettura di sicurezza da parte del cliente per la protezione dei servizi.

## Architetture di sicurezza tradizionali e reti perimetrali
Nonostante gli importanti investimenti Microsoft nella protezione dell'infrastruttura cloud, anche i clienti devono proteggere i propri servizi cloud e i gruppi di risorse. Un approccio a più livelli per la sicurezza offre la difesa migliore. Una rete perimetrale per la sicurezza di rete protegge le risorse di rete interne da una rete non attendibile. La rete perimetrale è un concetto noto nell'architettura di sicurezza di rete per IT aziendale e fa riferimento ai margini o alle parti della rete che si trovano tra Internet e l'infrastruttura IT aziendale protetta.

Nelle reti aziendali tipiche l'infrastruttura di base viene protetta in modo rilevante nelle aree perimetrali, con più livelli di dispositivi di sicurezza. Il limite di ogni livello è costituito da dispositivi e punti di imposizione dei criteri. I dispositivi possono includere firewall, prevenzione di attacchi DDoS (Distributed Denial of Service), IDS/IPS (rilevamento di intrusioni o sistemi di protezione), dispositivi VPN (Virtual Private Network) e così via. L'imposizione dei criteri può essere effettuata tramite criteri del firewall, ACL o routing specifico. La prima linea di difesa della rete, che accetta direttamente traffico in arrivo da Internet, è costituita da una combinazione di questi meccanismi per bloccare gli attacchi e il traffico dannoso, lasciando al tempo stesso passare nella rete le richieste legittime. Questo traffico verrà indirizzato direttamente a risorse nella rete perimetrale. Tale risorsa potrà quindi "comunicare" con risorse più interne della rete, attraversando il limite successivo per la convalida prima dell'indirizzamento a una posizione più interna della rete. Il livello più esterno è definito rete perimetrale perché questa parte della rete è esposta a Internet, in genere con qualche tipo di protezione su entrambi i lati della rete perimetrale. La figura seguente illustra un esempio di una singola subnet perimetrale in una rete aziendale con due limiti di sicurezza, ovvero rete perimetrale - Internet e rete perimetrale - VLAN back-end.

![Rete perimetrale in una rete aziendale][3]

Per implementare una rete perimetrale vengono usate molte architetture, da un semplice servizio di bilanciamento del carico davanti a una Web farm a più subnet perimetrali con diversi meccanismi in ogni limite per bloccare il traffico e proteggere i livelli più interni della rete aziendale. La modalità di costruzione della rete perimetrale dipende dalle esigenze specifiche dell'organizzazione e dalla tolleranza del rischio associata.

Quando i clienti spostano i propri carichi di lavoro nei cloud pubblici, è essenziale supportare capacità analoghe per l'architettura della rete perimetrale in Azure per soddisfare i requisiti di conformità e sicurezza. Questo documento fornisce indicazioni sul modo in cui i clienti possono creare un ambiente di rete sicuro in Azure, con un'attenzione specifica sulla rete perimetrale ma con informazioni complete su molti aspetti della sicurezza di rete, a partire, tra le altre, dalle domande seguenti:

1.	In che modo è possibile creare una rete perimetrale in Azure?
2.	Quali sono alcune funzionalità di Azure disponibili per la creazione della rete perimetrale?
3.	In che modo è possibile proteggere i carichi di lavoro back-end?
4.	In che modo vengono controllate le comunicazioni Internet per i carichi di lavoro in Azure?
5.	In che modo è possibile proteggere le reti locali da distribuzioni in Azure?
6.	Quando è consigliabile usare le funzionalità di sicurezza native di Azure invece di applicazioni o servizi di terze parti?

Il diagramma seguente mostra diversi livelli di sicurezza forniti da Azure ai clienti, tramite funzionalità native della piattaforma Azure stessa e tramite funzionalità definite dal cliente:

![Architettura di sicurezza di Azure][4]

La protezione da DDoS di Azure in ingresso da Internet protegge da attacchi su larga scala contro Azure. Se si supera questa protezione, si raggiungeranno endpoint pubblici definiti dall'utente, che vengono usati per determinare il traffico che può passare alla rete virtuale attraverso il servizio cloud. L'isolamento nativo della rete virtuale di Azure assicura l'isolamento completo da tutte le altre reti e lo scorrimento del traffico solo tramite percorsi e metodi configurati dall'utente. Questi percorsi e metodi costituiscono il livello successivo, in cui è possibile usare gruppi di sicurezza di rete, routing definito dall'utente e dispositivi virtuali di rete per creare limiti di sicurezza, incluse le reti perimetrali, per proteggere le distribuzioni delle applicazioni nella rete protetta.

La sezione successiva fornisce una panoramica delle reti virtuali di Azure. Le reti virtuali di Azure vengono create dai clienti e a tali reti vengono connessi i carichi di lavoro distribuiti dei clienti. Le reti virtuali sono la base di tutte le funzionalità di sicurezza di rete necessarie per stabilire una rete perimetrale per proteggere le distribuzioni dei clienti in Azure.

## Panoramica delle reti virtuali di Azure
Prima che il traffico Internet possa raggiungere le reti virtuali di Azure, sono disponibili due livelli di sicurezza inerenti alla piattaforma Azure:

1.	**Protezione DDoS**: il livello Distributed Denial of Service Protection (DDoS) è un livello della rete fisica di Azure che protegge la piattaforma Azure stessa da attacchi su larga scala basati su Internet, in cui gli utenti malintenzionati usano più nodi "bot", nel tentativo di sovraccaricare un servizio Internet. Azure ha una robusta rete mesh di protezione DDoS su tutta la connettività Internet in ingresso. Questo livello di protezione DDoS non ha attributi configurabili dall'utente e non è accessibile al cliente. Ciò protegge la piattaforma Azure da attacchi su larga scala, ma non proteggerà direttamente le singole applicazioni dei clienti. Il cliente può configurare livelli aggiuntivi di resilienza contro un attacco localizzato. Ad esempio, se il cliente A ha subito un attacco DDoS su larga scala su un endpoint pubblico, Azure bloccherà le connessioni a tale servizio. Per ripristinare il servizio, il cliente A può eseguire il failover su un'altra rete virtuale o un antro endpoint di servizio non coinvolto nell'attacco. Occorre notare che, mentre è possibile che il cliente A sia interessato dall'attacco su tale endpoint, nessun altro servizio all'esterno dell'endpoint sarà interessato dall'attacco. Altri clienti e servizi, inoltre, non subiranno alcun effetto di tale attacco.
2.	**Endpoint di servizio**: gli endpoint consentono ai Servizi cloud o ai gruppi di risorse di avere indirizzi IP pubblici (su Internet) e porte esposte. L'endpoint applicherà il processo NAT (Network Address Translation) al traffico fino all'indirizzo e alla porta interni nella rete virtuale di Azure. Questo è il percorso principale seguito dal traffico esterno per passare alla rete virtuale di Azure. Gli endpoint di servizio possono essere configurati dall'utente per determinare il traffico autorizzato a passare e come/dove viene convertito nella rete virtuale. 

Quando il traffico raggiunge la rete virtuale, sono disponibili molte funzionalità, perché le reti virtuali di Azure costituiscono la base a cui i clienti collegano i rispettivi carichi di lavoro e a cui viene applicata la sicurezza a livello di rete di base. Si tratta di una rete privata (un overlay di rete virtuale) in Azure per i clienti, che offre le funzionalità e le caratteristiche seguenti:
 
1.	**Isolamento del traffico**: una rete virtuale è il limite di isolamento del traffico nella piattaforma Azure. Le VM in una rete virtuale non possono comunicare direttamente con le VM in una rete virtuale diversa, anche se entrambe le reti virtuali vengono create dallo stesso cliente. Questa è una proprietà essenziale, che assicura che le macchine virtuali e le comunicazioni dei clienti rimangano private entro una rete virtuale. 
2.	**Topologia a più livelli**: le reti virtuali consentono ai clienti di definire una topologia a più livelli, allocando subnet e designando spazi di indirizzi separati per diversi elementi o "livelli" dei rispettivi carichi di lavoro. Questi raggruppamenti logici e queste topologie consentono ai clienti di definire diversi criteri di accesso in base ai tipi di carico di lavoro e anche di controllare i flussi del traffico tra i livelli. 
3.	**Connettività tra più ambienti**: i clienti possono stabilire la connettività tra più ambienti tra una rete virtuale e più sedi locali o altre reti virtuali in Azure tramite i gateway VPN di Azure o i dispositivi virtuali di rete di terze parti. Azure supporta VPN da sito a sito (S2S, site-to-site) mediante protocolli IPsec/IKE standard e la connettività privata di ExpressRoute. 
4.	**Gruppo di sicurezza di rete**: consente ai clienti di creare regole (ACL) al livello di granularità desiderato, ovvero interfacce di rete, singole VM o subnet virtuali. I clienti possono controllare l'accesso autorizzando o negando la comunicazione tra i carichi di lavoro in una rete virtuale, da sistemi nelle reti dei clienti tramite la connettività tra più ambienti oppure la comunicazione Internet diretta. 
5.	Le **route definite dall'utente** e l'**inoltro IP** consentono ai clienti di definire i percorsi di comunicazione tra diversi livelli in una rete virtuale. I clienti possono distribuire un firewall, IDS/IPS e altri dispositivi e indirizzare il traffico di rete attraverso questi dispositivi di sicurezza per l'applicazione dei criteri relativi ai limiti di sicurezza, per il controllo e l'ispezione.
6.	**Dispositivi virtuali di rete** in Azure Marketplace: i dispositivi di sicurezza, ad esempio firewall, servizi di bilanciamento del carico e IDS/IPS (servizi di rilevamento/prevenzione delle intrusioni) sono disponibili in Azure Marketplace e nella raccolta di immagini delle VM. I clienti possono distribuire questi dispositivi nelle proprie reti virtuali e, in particolare, nei propri limiti di sicurezza (incluse le subnet perimetrali) per completare un ambiente di rete sicuro a più livelli.

Con queste funzionalità e capacità è ad esempio possibile creare la seguente architettura di rete perimetrale in Azure:

![Rete perimetrale in una rete virtuale di Azure][5]

## Caratteristiche e requisiti della rete perimetrale
Questa sezione illustra le caratteristiche e i requisiti di una rete perimetrale in Azure. Come illustrato in precedenza, una rete perimetrale è progettata per essere il front-end della rete, che si interfaccia direttamente con le comunicazioni da Internet. I pacchetti in ingresso devono attraversare i dispositivi di sicurezza, ovvero firewall, IDS, IPS e così via nella rete perimetrale prima di raggiungere i server back-end. Anche i pacchetti diretti a Internet dai carichi di lavoro possono attraversare i dispositivi di sicurezza nella rete perimetrale per finalità di applicazione dei criteri, ispezione e controllo, prima di lasciare la rete. La rete perimetrale può essere usata anche per ospitare gateway VPN tra più ambienti tra reti virtuali del cliente e reti locali.

### Caratteristiche della rete perimetrale
Facendo riferimento alla figura precedente, relativa a una rete perimetrale in una rete virtuale di Azure, una rete perimetrale efficace deve avere le seguenti caratteristiche:

- Rete perimetrale rivolta a Internet (front-end):
    - La subnet perimetrale stessa è rivolta a Internet e comunica direttamente con Internet.
    - Gli indirizzi IP pubblici, gli indirizzi VIP e/o gli endpoint di servizio passano il traffico Internet alla rete e ai dispositivi front-end.
    - Il traffico in ingresso da Internet attraversa i dispositivi di sicurezza prima delle altre risorse sulla rete front-end.
    - Se la sicurezza in uscita è abilitata, il traffico attraversa i dispositivi di sicurezza, come passaggio finale, prima di passare a Internet.
- "Rete protetta" (back-end): verso l'infrastruttura di base.
    - Nessun percorso diretto da Internet all'infrastruttura di base.
    - I canali verso l'infrastruttura di base DEVONO attraversare i dispositivi di sicurezza, ad esempio i gruppi di sicurezza di rete, i firewall o i dispositivi VPN nella rete perimetrale.
    - Altri dispositivi nella rete perimetrale NON DEVONO eseguire il bridging tra Internet e l'infrastruttura di base.
    - I dispositivi di sicurezza nella rete rivolta a Internet e nella rete protetta rivolta ai limiti della rete perimetrale (ad esempio le due icone del firewall presenti nella figura precedente) possono in effetti essere un singolo dispositivo virtuale con regole o interfacce differenziate per il limite Internet e il limite back-end (ad esempio, un dispositivo, separato logicamente, che gestisce il carico per entrambi i limiti della rete perimetrale).
- Altri vincoli e procedure comuni
    - I carichi di lavoro della rete perimetrale NON DEVONO archiviare informazioni essenziali per l'azienda.
    - L'accesso e gli aggiornamenti alle configurazioni e alle distribuzioni della rete perimetrale sono limitati solo agli amministratori autorizzati.

### Requisiti della rete perimetrale
Per abilitare queste caratteristiche, l'elenco seguente fornisce indicazioni sui requisiti della rete virtuale necessari per implementare una rete perimetrale efficace:

- Architettura della subnet: specificare la rete virtuale in modo che un'intera subnet sia dedicata come rete perimetrale, separata da altre subnet nella stessa rete virtuale. In questo modo si assicurerà che il traffico tra il livello della rete perimetrale e altri livelli di subnet interne o private attraversi un firewall o un dispositivo virtuale IDS/IPS nei limiti della subnet con route definite dall'utente.
- Gruppo di sicurezza di rete per la rete perimetrale: la subnet perimetrale stessa deve essere aperta per consentire le comunicazioni tra la rete perimetrale e Internet, ma ciò non significa che i clienti devono ignorare i gruppi di sicurezza di rete. Seguire le procedure di sicurezza comuni per ridurre al minimo la superficie di rete esposta a Internet, bloccando gli intervalli di indirizzi remoti che sono autorizzati ad accedere alle distribuzioni e/o i protocolli applicativi specifici e le porte aperte. Si noti, tuttavia, che ciò potrebbe non essere sempre possibile. Ad esempio, se i clienti hanno un sito Web esterno in Azure, la rete perimetrale dovrà consentire le richieste Web in ingresso da qualsiasi indirizzo IP pubblico, ma dovrà aprire solo le porte di applicazione Web TCP:80 e TCP:443.
- Tabella di routing per la rete perimetrale: la subnet perimetrale stessa dovrebbe essere in grado di comunicare direttamente con Internet, ma non deve consentire le comunicazioni dirette verso e da il back-end o le reti locali senza attraversare un firewall o un dispositivo di sicurezza.
- Configurazione dei dispositivi di sicurezza della rete perimetrale: per indirizzare e ispezionare i pacchetti tra la rete perimetrale e il resto delle reti protette, i dispositivi di sicurezza, ad esempio firewall e dispositivi IDS e IPS nella rete perimetrale, possono essere multihomed, con schede di interfaccia di rete separate per la rete perimetrale e le subnet back-end. Le schede di interfaccia di rete della rete perimetrale comunicheranno direttamente verso e da Internet, con i gruppi di sicurezza di rete e la tabella di routing corrispondenti della rete perimetrale. Le schede di interfaccia di rete che si connettono alle subnet back-end avranno gruppi di sicurezza di rete e tabelle di routing con più restrizioni delle subnet back-end corrispondenti.
- Funzionalità del dispositivo di sicurezza della rete perimetrale: i dispositivi di sicurezza distribuiti nella rete perimetrale offrono in genere le funzionalità seguenti.
    - Firewall: applicazione delle regole del firewall o dei criteri di controllo di accesso per le richieste in ingresso.
    - Rilevamento e prevenzione delle minacce: rilevare e ridurre gli attacchi dannosi da Internet.
    - Controllo e registrazione: gestire log dettagliati per il controllo e l'analisi.
    - Proxy inverso: reindirizzare le richieste in ingresso ai server back-end corrispondenti tramite il mapping e la conversione degli indirizzi di destinazione nei dispositivi front-end della rete perimetrale, in genere firewall, agli indirizzi server back-end effettivi.
    - Proxy di inoltro: fornire processi NAT ed eseguire anche il controllo per le comunicazioni avviate dall'interno della rete virtuale verso Internet.
    - Router: agire come router per inoltrare il traffico in ingresso e tra le subnet all'interno della rete virtuale.
    - Dispositivo VPN: fungere da gateway VPN tra più ambienti per la connettività VPN tra più ambienti tra le reti locali dei clienti e le reti virtuali di Azure.
    - Server VPN: fungere da server VPN per accettare la connessione di client VPN alle reti virtuali di Azure.

>[AZURE.TIP] Gestire gli individui autorizzati ad accedere ai dispositivi di sicurezza della rete perimetrale in modo completamente diverso rispetto agli individui autorizzati come amministratori per lo sviluppo applicazioni/la distribuzione/le operazioni. Se si mantengono separati questi gruppi, sarà possibile separare nettamente i compiti e impedire a una singola persona di ignorare i controlli di sicurezza delle applicazioni e della rete.

### Domande da porre durante la creazione dei limiti della rete
Quando si accenna alle "reti di Azure" in questa sezione, se non indicato in modo specifico, tutte le reti (reti, reti virtuali o subnet) fanno riferimento a reti virtuali private di Azure create da un amministratore della sottoscrizione e non vengono illustrate le reti fisiche sottostanti in Azure.

Le reti virtuali di Azure, inoltre, vengono usate spesso per estendere reti locali tradizionali. È possibile incorporare soluzioni di rete ibride da sito a sito o ExpressRoute con le architetture della rete perimetrale. Questa è una considerazione importante quando si creano i limiti della sicurezza di rete e viene illustrata nelle domande seguenti.

Quando si crea una rete con una rete perimetrale e più limiti di sicurezza, è necessario rispondere a tre domande essenziali.

#### 1) Quanti limiti servono?
Il primo punto di decisione consiste nel decidere il numero di limiti di sicurezza necessari in un determinato scenario:

- Un singolo limite: un limite nella rete perimetrale front-end tra la rete virtuale e Internet.
- Due limiti: uno sul lato Internet della rete perimetrale, un altro tra la subnet perimetrale e le subnet back-end nelle reti virtuali di Azure.
- Tre limiti: sul lato Internet della rete perimetrale, tra la rete perimetrale e le subnet back-end e infine tra le subnet back-end e la rete locale.
- Numero indefinito di limiti: in base ai requisiti di sicurezza, non è previsto alcun limite al numero di limiti di sicurezza applicabili in una determinata rete.

Il numero e il tipo di limiti necessari dipende dalla tolleranza del rischio dell'azienda e dallo scenario specifico da implementare. Si tratta spesso di una decisione comune presa da più gruppi di un'organizzazione, spesso in collaborazione con un team responsabile dei rischi e della conformità, un team responsabile della rete/piattaforma e un team responsabile dello sviluppo di applicazioni. Le persone esperte in ambito di sicurezza, dei dati coinvolti e delle tecnologie da usare dovrebbero poter partecipare a questa decisione, per assicurare la strategia di sicurezza appropriata per ogni implementazione.

>[AZURE.TIP] Usare il numero minore possibile di limiti in grado di soddisfare i requisiti di sicurezza per una determinata situazione. Un numero maggiore di limiti può rendere più difficili le operazioni e la risoluzione dei problemi, oltre a incrementare l'overhead di gestione correlato alla gestione di più criteri di limite nel tempo. Un numero insufficiente di limiti, tuttavia, comporta un aumento del rischio. È essenziale trovare il giusto equilibrio.

![Rete ibrida con tre limiti di sicurezza][6]

La figura precedente mostra una visualizzazione generale di una rete con tre livelli di sicurezza, con un livello tra la rete perimetrale e Internet, uno tra il front- end di Azure e le subnet private back-end e infine uno tra la subnet back-end di Azure e la rete aziendale locale.

#### 2) Dove sono situati i limiti?
Dopo avere stabilito il numero di limiti, occorre determinare dove implementarli. Sono in genere disponibili tre opzioni, ovvero 1) Uso di un servizio intermediario basato su Internet, ad esempio un Web Application Firewall (WAF) basato sul cloud, non illustrato in questo documento, 2) Uso di funzionalità native e/o dispositivi virtuali di rete in Azure, 3) Uso di dispositivi fisici nella rete locale.

In reti puramente Azure le opzioni disponibili sono le funzionalità di Azure native, ad esempio i Servizi di bilanciamento del carico di Azure, oppure i dispositivi virtuali di rete presenti nel ricco ecosistema di partner di Azure, ad esempio i firewall Check Point.

Se è necessario un limite tra Azure e una rete locale, i dispositivi di sicurezza possono trovarsi su un qualsiasi lato della connessione o su entrambi i lati. È quindi necessario stabilire in che posizione inserire i dispositivi di sicurezza.

Nella figura precedente i limiti da Internet alla rete perimetrale e dal front-end al back-end sono inclusi interamente in Azure e devono essere funzionalità di Azure native o dispositivi virtuali di rete. I dispositivi di sicurezza sul limite tra Azure (subnet back-end) e la rete aziendale possono essere sul lato Azure o sul lato locale oppure è possibile usare una combinazione di dispositivi su entrambi i lati. Occorre valutare con attenzione i vantaggi e gli svantaggi significativi di entrambe le opzioni.

Ad esempio, si consideri l'uso dei dispositivi di sicurezza esistenti sul lato della rete locale. Vantaggio: non sono necessari nuovi dispositivi, occorre solo una nuova configurazione. Svantaggio: tutto il traffico deve tornare da Azure alla rete locale per potere essere visualizzato dal dispositivo di sicurezza. Il traffico da Azure ad Azure potrebbe quindi subire una latenza significativa e influire sulle prestazioni delle applicazioni e sull'esperienza utente, se ne è stato imposto il ritorno alla rete locale per l'applicazione dei criteri di sicurezza.

#### 3) In che modo vengono implementati i limiti?
È probabile che per ogni limite di sicurezza siano previsti requisiti di capacità diversi, ad esempio regole per IDS e firewall sul lato Internet della rete perimetrale, ma solo ACL tra la rete perimetrale e la subnet back-end. La decisione relativa ai dispositivi da usare dipende dallo scenario e dai requisiti di sicurezza. Gli esempi 1, 2 e 3 seguenti di rete perimetrale illustrano alcune opzioni disponibili. La verifica delle funzionalità di rete native di Azure e dei dispositivi disponibili in Azure dall'ecosistema partner esporrà le numerosissime opzioni disponibili per risolvere quasi tutti gli scenari.

Un altro punto di decisione essenziale per l'implementazione consiste nella modalità di connessione della rete locale con Azure. Tramite il gateway virtuale di Azure o un dispositivo virtuale di rete. Queste opzioni sono descritte e illustrate con maggiori dettagli negli esempi 4, 5 e 6 più avanti.

Potrebbe essere anche necessario il traffico da rete virtuale a rete virtuale in Azure. Questi scenari sono illustrati con maggiori dettagli negli esempi 7 e 8 più avanti.

Quando sono disponibili risposte per le domande precedenti, la sezione [Avvio veloce](#fast-start) precedente può semplificare l'identificazione degli esempi più appropriati per un determinato scenario.

## Creazione di limiti di sicurezza con le reti virtuali di Azure
### Esempio 1: Creare una semplice rete perimetrale con gruppi di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example1]

![Rete perimetrale in ingresso con gruppo di sicurezza di rete][7]

#### Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

- Due servizi cloud, "FrontEnd001" e "BackEnd001"
- Una rete virtuale, "CorpNetwork", con due subnet, "FrontEnd" e "BackEnd"
- Un gruppo di sicurezza di rete applicato a entrambe le subnet
- Un server Windows che rappresenta un server Web applicazioni ("IIS01")
- Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
- Un server Windows che rappresenta un server DNS ("DNS01").

Per creare questo esempio, che fornisce script e un modello ARM, sono necessari altri dettagli, disponibili nella pagina di [istruzioni di creazione dettagliate][Example1].

#### Descrizione del gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

>[AZURE.TIP] In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega" più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una regola specifica, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita, dal punto di vista della subnet.

A livello dichiarativo, per il traffico in ingresso vengono create le righe seguenti:

1.	Il traffico DNS interno (porta 53) è consentito.
2.	Il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale è consentito.
3.	Il traffico HTTP (porta 80) da Internet al server Web (IIS01) è consentito.
4.	Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5.	Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6.	Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene bloccato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet ed è diretta verso il server Web, le regole 3 (consenti) e 5 (nega) saranno applicabili, ma poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul server Web. Se lo stesso traffico prova a raggiungere il server DNS01, la regola 5 (nega) sarà la prima applicabile e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) impedisce alla subnet front-end di comunicare con la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4), proteggendo la rete back-end nel caso in cui un utente malintenzionato comprometta l'applicazione Web sul front-end. L'utente malintenzionato avrà infatti accesso limitato alla rete "protetta" back-end, ovvero solo alle risorse esposte nel server AppVM01.

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è necessario il routing definito dall'utente, illustrato nella sezione "Esempio 3" più avanti.

#### Conclusioni
Questo è un modo relativamente semplice e diretto per isolare la subnet back-end dal traffico in ingresso. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale con script di PowerShell
- Come creare la rete perimetrale con un modello ARM
- Descrizioni dettagliate di ogni comando del gruppo di sicurezza di rete
- Scenari dettagliati del flusso di traffico, con informazioni sul modo in cui il traffico viene consentito o negato in ogni livello

sono disponibili nella pagina di [istruzioni di compilazione dettagliate][Example1].

### Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example2]

![Rete perimetrale in ingresso con dispositivo virtuale di rete e gruppo di sicurezza di rete][8]

#### Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

- Due servizi cloud, "FrontEnd001" e "BackEnd001"
- Una rete virtuale, "CorpNetwork", con due subnet, "FrontEnd" e "BackEnd".
- Un singolo gruppo di sicurezza di rete applicato a entrambe le subnet.
- Un dispositivo virtuale di rete, in questo esempio un firewall, connesso alla subnet front-end.
- Un server Windows che rappresenta un server Web applicazioni ("IIS01").
- Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
- Un server Windows che rappresenta un server DNS ("DNS01").

Per creare questo esempio, che fornisce script e un modello ARM, sono necessari altri dettagli, disponibili nella pagina di [istruzioni di creazione dettagliate][Example2].

#### Descrizione del gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

>[AZURE.TIP] In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega" più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una regola specifica, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita, dal punto di vista della subnet.

A livello dichiarativo, le righe seguenti vengono create per il traffico in ingresso:

1.	Il traffico DNS interno (porta 53) è consentito.
2.	Il traffico RDP (porta 3389) da Internet a qualsiasi VM è consentito.
3.	Tutto il traffico Internet (tutte le porte) verso il dispositivo virtuale di rete (firewall) è consentito.
4.	Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5.	Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6.	Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene negato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet verso il firewall, le regole 3 (consenti) e 5 (nega) saranno applicabili, ma poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul firewall. Se lo stesso traffico prova a raggiungere il server IIS01, anche se si trova sulla subnet front-end, la regola 5 (nega) verrà applicata e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) impedisce alla subnet front-end di comunicare con la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4), proteggendo la rete back-end nel caso in cui un utente malintenzionato comprometta l'applicazione Web sul front-end. L'utente malintenzionato avrà infatti accesso limitato alla rete "protetta" back-end, ovvero solo alle risorse esposte nel server AppVM01.

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è necessario il routing definito dall'utente, illustrato nella sezione "Esempio 3" più avanti.

#### Descrizione della regola del firewall
Sul firewall sarà necessario creare regole di inoltro. Poiché questo esempio indirizza solo il traffico Internet in ingresso verso il firewall e quindi verso il server Web, sarà necessaria solo una regola del processo NAT di inoltro.

La regola di inoltro accetterà qualsiasi indirizzo di origine in ingresso che raggiunge il firewall nel tentativo di raggiungere HTTP (porta 80 o 443 per HTTPS). Gli indirizzi verranno trasmessi all'interfaccia locale del firewall e reindirizzati al server Web con indirizzo IP 10.0.1.5.

#### Conclusioni
Questo è un modo relativamente semplice per proteggere l'applicazione con un firewall e isolare la subnet back-end dal traffico in ingresso. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale di esempio con script di PowerShell
- Come creare la rete perimetrale di esempio con un modello ARM
- Descrizioni dettagliate di ogni comando del gruppo di sicurezza di rete e di ogni regola del firewall
- Scenari dettagliati del flusso di traffico, con informazioni sul modo in cui il traffico viene consentito o negato in ogni livello

sono disponibili nella pagina di [istruzioni di compilazione dettagliate][Example2].

### Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example3]

![Rete perimetrale bidirezionale con dispositivo virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente][9]

#### Configurazione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

- Tre servizi cloud, "SecSvc001", "FrontEnd001" e "BackEnd001".
- Una rete virtuale, "CorpNetwork", con tre subnet, "SecNet", "FrontEnd" e "BackEnd".
- Un dispositivo virtuale di rete, in questo esempio un firewall, connesso alla subnet SecNet.
- Un server Windows che rappresenta un server Web applicazioni ("IIS01").
- Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
- Un server Windows che rappresenta un server DNS ("DNS01").

Per creare questo esempio, che fornisce script e un modello ARM, sono necessari altri dettagli, disponibili nella pagina di [istruzioni di creazione dettagliate][Example3].

#### Descrizione del routing definito dall'utente
Per impostazione predefinita, le route di sistema seguenti sono definite in questo modo:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal corrisponde sempre al prefisso o ai prefissi dell'indirizzo definito della rete virtuale per la rete specifica, ovvero cambierà da rete virtuale a rete virtuale in base alla modalità di definizione di ogni rete virtuale specifica. Le route di sistema rimanenti sono statiche e usano i valori predefiniti illustrati in precedenza.

In questo esempio vengono create due tabelle di routing, una per la subnet front-end e una per la subnet back-end. In ogni tabella vengono caricate le route statiche appropriate per la subnet specifica. Ai fini di questo esempio, ogni tabella ha tre route che indirizzano tutto il traffico (0.0.0.0/0) attraverso il firewall (hop successivo = indirizzo IP del dispositivo virtuale):

1. Traffico della subnet locale senza la definizione di un hop successivo per consentire al traffico della subnet locale di ignorare il firewall.
2. Traffico della rete virtuale con la definizione di un hop successivo come firewall, in modo da eseguire l'override della regola predefinita che consente l'indirizzamento diretto del traffico della rete virtuale locale.
3. Tutto il traffico rimanente (0/0) con un hop successivo definito come firewall.

Dopo la creazione, le tabelle di routing vengono associate alle rispettive subnet. Dopo la creazione e l'associazione alla subnet, la tabella di routing della subnet front-end avrà un aspetto analogo al seguente:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
		 {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] Sono previste limitazioni per l'uso del routing definito dall'utente (User Defined Routing, UDR) con ExpressRoute, a causa della complessità del routing dinamico usato nel gateway virtuale di Azure. Queste limitazioni sono elencate di seguito:
>
> 1. Il routing definito dall'utente non deve essere applicato alla subnet del gateway in cui è connessa l'istanza di ExpressRoute collegata al gateway virtuale di Azure.
> 2. L'istanza di ExpressRoute collegata al gateway virtuale di Azure non può essere il dispositivo NextHop usato per altre subnet associate al routing definito dall'utente.
>
>La possibilità di integrare il routing definito dall'utente ed ExpressRoute verrà abilitata in una versione futura. Per esempi relativi a come abilitare la rete perimetrale con ExpressRoute o con la rete da sito a sito, vedere gli esempi 3 e 4 più avanti.


#### Descrizione dell'inoltro IP
L'inoltro IP è una funzionalità complementare del routing definito dall'utente. Si tratta di un'impostazione in un dispositivo virtuale che consente al dispositivo di ricevere traffico non indirizzato in modo specifico ad esso e quindi di inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01, il routing definito dall'utente indirizzerà tale richiesta al firewall. Se l'inoltro IP è abilitato, il traffico per la destinazione DNS01 (10.0.2.4) verrà accettato dal dispositivo (10.0.0.4) e quindi inoltrato alla destinazione finale (10.0.2.4). Se l'inoltro IP non è abilitato sul firewall, il traffico non verrà accettato dal dispositivo, anche se la tabella di route specifica il firewall come hop successivo. Per usare un dispositivo virtuale, è essenziale ricordare di abilitare l'inoltro IP insieme al routing definito dall'utente.

#### Descrizione del gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui viene poi caricata una singola regola. Questo gruppo viene quindi associato solo alle subnet front-end e back-end, non a SecNet. La regola seguente viene creata in modo dichiarativo:

1.	Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (tutte le subnet) viene negato.

Anche se in questo esempio vengono usati i gruppi di sicurezza di rete, lo scopo principale consiste nel creare un secondo livello di difesa contro errori di configurazione manuale. Si vuole bloccare tutto il traffico in ingresso da Internet verso le subnet front-end o back-end. Il traffico deve solo attraversare la subnet SecNet verso il firewall e quindi, se appropriato, deve raggiungere le subnet front-end o back-end. Con l'abilitazione delle regole del routing definito dall'utente, inoltre, tutto il traffico che raggiunge le subnet front-end o back-end verrà indirizzato verso il firewall, grazie al routing definito dall'utente. Il firewall considera questo traffico come un flusso asimmetrico e rimuove il traffico in uscita. Sono quindi disponibili tre livelli di sicurezza per la protezione delle subnet front-end e back-end: 1) nessun endpoint aperto nei servizi cloud FrontEnd001 e BackEnd001, 2) gruppi di sicurezza di rete che non consentono il traffico da Internet, 3) il firewall che rimuove il traffico asimmetrico.

Un aspetto interessante del gruppo di sicurezza di rete di questo esempio consiste nel fatto che include solo una regola, ovvero non consentire il traffico Internet nell'intera rete virtuale, inclusa la subnet di sicurezza. Poiché, tuttavia, il gruppo di sicurezza di rete è associato solo alle subnet front-end e back-end, la regola non viene elaborata sul traffico in ingresso verso la subnet di sicurezza. Anche se la regola del gruppo di sicurezza di rete non consente il traffico Internet verso qualsiasi indirizzo sulla rete virtuale, perché il gruppo di sicurezza di rete non è mai stato associato alla subnet di sicurezza, il traffico raggiungerà quindi la subnet di sicurezza.

#### Regole del firewall
Sul firewall sarà necessario creare regole di inoltro. Poiché il firewall blocca o inoltra tutto il traffico in ingresso, in uscita e tra le reti virtuali, sono necessarie molte regole del firewall. Tutto il traffico in ingresso, inoltre, raggiungerà l'indirizzo IP pubblico del servizio di sicurezza su porte diverse, per l'elaborazione da parte del firewall. Una procedura consigliata consiste nel creare un diagramma dei flussi logici prima di configurare le regole delle subnet e del firewall, per evitare modifiche successive. La figura seguente è una visualizzazione logica delle regole del firewall per questo esempio:
 
![Visualizzazione logica delle regole del firewall][10]

>[AZURE.NOTE] Le porte di gestione dipendono dal dispositivo virtuale di rete usato. In questo esempio si fa riferimento a Barracuda NextGen Firewall, che usa le porte 22, 801 e 807. Per informazioni precise sulle porte usate per la gestione del dispositivo in uso, vedere la documentazione del fornitore del dispositivo.

#### Descrizione delle regole del firewall
Nel diagramma logico precedente la subnet di sicurezza non viene mostrata, perché il firewall è l'unica risorsa su tale subnet e questo diagramma illustra le regole del firewall e il modo in cui le regole consentono o negano logicamente i flussi di traffico, non il percorso indirizzato effettivo. Le porte esterne selezionate per il traffico RDP, inoltre, sono le porte incluse negli intervalli più elevati (8014 - 8026) e sono state selezionate per allinearsi almeno in parte con gli ultimi due ottetti dell'indirizzo IP locale per facilitarne la lettura. Ad esempio, l'indirizzo del server locale 10.0.1.4 è associato alla porta esterna 8014. È tuttavia possibile usare qualsiasi porta superiore non in conflitto.

Per questo esempio sono necessari 7 tipi di regole, ovvero:

- Regole esterne (per il traffico in ingresso):
  1.	Regola di gestione del firewall: questa regola di reindirizzamento dell'app consente al traffico di raggiungere le porte di gestione del dispositivo virtuale di rete.
  2.	Regole RDP (per ogni server Windows): queste quattro regole, una per ogni server, consentiranno la gestione dei singoli server tramite RDP. È anche possibile raggruppare queste regole in una sola regola, in base alle capacità del dispositivo di rete virtuale usato.
  3.	Regole del traffico dell'applicazione: sono disponibili due regole del traffico dell'applicazione, la prima per il traffico Web front-end e la seconda per il traffico back-end, ad esempio dal server Web al livello dati. La configurazione di queste regole dipenderà dall'architettura di rete, ovvero dove sono posizionati i server, e dai flussi di traffico, ovvero la direzione dei flussi di traffico e le porte usate.
      - La prima regola consentirà al traffico effettivo dell'applicazione di raggiungere il server applicazioni. Anche se altre regole consentono la sicurezza, la gestione e così via, le regole delle applicazioni consentono a utenti o servizi esterni di accedere alle applicazioni. Per questo esempio è presente un singolo server Web sulla porta 80, quindi una singola regola delle applicazioni per il firewall reindirizzerà il traffico in ingresso all'indirizzo IP esterno, verso l'indirizzo IP interno dei server Web. La sessione di traffico reindirizzato sarà sottoposta al processo NAT verso il server interno.
      - La seconda regola del traffico delle applicazioni è la regola back-end per consentire al server Web di comunicare con il server AppVM01, ma non con AppVM02, tramite qualsiasi porta.
- Regole interne (per il traffico tra reti virtuali)
  4.	Regola in uscita verso Internet: questa regola consentirà al traffico proveniente da qualsiasi rete di passare alle reti selezionate. Questa regola è in genere una regola predefinita già disponibile sul firewall, ma con stato disabilitato. È consigliabile abilitarla per questo esempio.
  5.	Regola DNS: questa regola consente solo al traffico DNS (porta 53) di passare al server DNS. Per questo ambiente la maggior parte del traffico dal front-end e dal back-end è bloccato. Questa regola consente in modo specifico il traffico DNS da qualsiasi subnet locale.
  6.	Regola da subnet a subnet: questa regola consente a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end, ma non viceversa.
- Regola alternativa (per il traffico che non soddisfa alcuna condizione precedente):
  7.	Regola per negare tutto il traffico: deve essere sempre la regola finale, a livello di priorità. Se i flussi di traffico non corrispondono ad alcuna regola precedente, verranno eliminati da questa regola. Questa è una regola predefinita ed è solitamente attivata, quindi non sono in genere necessarie modifiche.

>[AZURE.TIP] Nella seconda regola per il traffico delle applicazioni tutte le porte sono autorizzate per semplificare l'esempio. In uno scenario reale è consigliabile usare la porta e gli intervalli di indirizzi più specifici per ridurre la superficie di attacco di questa regola.

Dopo la creazione di tutte le regole indicate, è importante esaminare la priorità di ogni regola, per assicurare che il traffico venga consentito o negato in base a quanto previsto. Per questo esempio le regole sono elencate in ordine di priorità.

#### Conclusioni
Questo è un modo più complesso ma più completo per proteggere e isolare la rete. L'esempio 2 protegge solo l'applicazione e l'esempio 1 isola solo le subnet. Questa struttura consente di monitorare il traffico in entrambe le direzioni e non protegge solo il server applicazioni in ingresso, ma applica anche i criteri di sicurezza di rete per tutti i server in questa rete. In base al dispositivo usato, è anche possibile ottenere il controllo e il riconoscimento di tutto il traffico. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale di esempio con script di PowerShell
- Come creare la rete perimetrale di esempio con un modello ARM
- Descrizioni dettagliate di ogni routing definito dall'utente, ogni comando del gruppo di sicurezza di rete e ogni regola del firewall
- Scenari dettagliati del flusso di traffico, con informazioni sul modo in cui il traffico viene consentito o negato in ogni livello

sono disponibili nella pagina di [istruzioni di compilazione dettagliate][Example3].

### Esempio 4: Aggiunta di una connessione ibrida con una VPN di dispositivo virtuale da sito a sito
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con dispositivo virtuale di rete connesso tramite rete ibrida][11]

#### Configurazione dell'ambiente
Le reti ibride che usano un dispositivo virtuale di rete possono essere aggiunte a qualsiasi tipo di rete perimetrale illustrato negli esempi 1, 2 o 3.

Come illustrato nella figura precedente, una connessione VPN su Internet (da sito a sito) viene usata per connettere una rete locale a una rete virtuale di Azure tramite un dispositivo virtuale di rete.

>[AZURE.NOTE] Se si usa ExpressRoute con l'opzione relativa al peering pubblico di Azure abilitata, sarà necessario creare una route statica per l'indirizzamento verso l'indirizzo IP VPN del dispositivo virtuale di rete oltre il margine Internet aziendale e non tramite il margine WAN di ExpressRoute. Ciò è dovuto al processo NAT necessario per l'opzione relativa al peering pubblico di Azure di ExpressRoute, che molto probabilmente interromperà la sessione VPN, perché in genere IPSec non è compatibile con i processi NAT.

Dopo la configurazione della VPN, il dispositivo virtuale di rete diventa l'hub centrale per tutte le reti e le subnet. Le regole di inoltro del firewall determinano i flussi di traffico consentiti, sottoposti a NAT, reindirizzati o eliminati, anche per i flussi di traffico tra la rete locale e Azure, se i flussi sono progettati in questo modo.

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di progettazione, in base al caso di utilizzo specifico.

Se si usa l'ambiente creato nell'esempio 3, "Creare una rete perimetrale per proteggere le reti con un firewall, routing definito dall'utente e un gruppo di sicurezza di rete", e quindi si aggiunge una connessione di rete ibrida VPN da sito a sito, si otterrà la struttura seguente:

![Rete perimetrale con dispositivo virtuale di rete connesso tramite VPN da sito a sito][12]

Nella struttura precedente il client VPN corrisponde al router locale o a eventuali altri dispositivi di rete compatibili con il dispositivo virtuale di rete per la VPN. Questo dispositivo fisico sarà responsabile per l'inizializzazione e la gestione della connessione VPN con il dispositivo virtuale di rete.

Per il dispositivo virtuale di rete, a livello logico, la rete è analoga a quattro "aree di sicurezza" distinte, con le regole del dispositivo virtuale di rete usate come strumento di indirizzamento primario per il traffico tra queste aree. La struttura logica sarà analoga alla seguente:

![Rete logica dal punto di vista del dispositivo virtuale di rete][13]

#### Conclusioni
L'aggiunta di una connessione di rete ibrida VPN da sito a sito a una rete virtuale di Azure può estendere la rete locale in Azure in modo sicuro. Se si usa una connessione VPN, il traffico verrà crittografato e indirizzato tramite Internet. Se si usa il dispositivo virtuale di rete, come illustrato in questo esempio, si otterrà una posizione locale per l'applicazione e la gestione dei criteri di sicurezza. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale di esempio con script di PowerShell
- Come creare la rete perimetrale di esempio con un modello ARM
- Scenari dettagliati di flussi di traffico che illustrano il modo in qui il traffico scorre nella struttura

saranno presto disponibili, con collegamenti in questa pagina.

### Esempio 5: Aggiunta di una connessione ibrida con una VPN di gateway di Azure da sito a sito
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con gateway connesso tramite rete ibrida][14]

#### Configurazione dell'ambiente
È possibile aggiungere reti ibride che usano un gateway VPN di Azure a qualsiasi tipo di rete perimetrale illustrato negli esempi 1 e 2.

Come illustrato nella figura precedente, una connessione VPN su Internet (da sito a sito) viene usata per connettere una rete locale a una rete virtuale di Azure tramite un gateway VPN di Azure.

>[AZURE.NOTE] Se si usa ExpressRoute con l'opzione relativa al peering pubblico di Azure abilitata, sarà necessario creare una route statica per l'indirizzamento verso l'indirizzo IP del gateway VPN di Azure oltre il margine Internet aziendale e non tramite il margine WAN di ExpressRoute. Ciò è dovuto al processo NAT necessario per l'opzione relativa al peering pubblico di Azure di ExpressRoute, che molto probabilmente interromperà la sessione VPN, perché in genere IPSec non è compatibile con i processi NAT.

Come illustrato di seguito, con questa opzione l'ambiente include ora due margini di rete. Nel primo margine il dispositivo virtuale di rete e i gruppi di sicurezza di rete controllano i flussi di traffico per le reti interne di Azure e tra Azure e Internet, mentre il secondo margine è il gateway VPN di Azure, ovvero un margine di rete completamente separato e isolato tra le reti locali e Azure.

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di progettazione, in base al caso di utilizzo specifico.

Se si usa l'ambiente creato nell'esempio 1, "Creare una rete perimetrale per proteggere le applicazioni con gruppi di sicurezza di rete", e quindi si aggiunge una connessione di rete ibrida VPN da sito a sito, si otterrà la struttura seguente:

![Rete perimetrale con gateway connesso tramite una connessione ExpressRoute][15]

#### Conclusioni
L'aggiunta di una connessione di rete ibrida VPN da sito a sito a una rete virtuale di Azure può estendere la rete locale in Azure in modo sicuro. Se si usa un gateway VPN di Azure nativo, il traffico verrà crittografato con IPSec e verrà indirizzato tramite Internet. L'uso del gateway VPN di Azure offre anche un'opzione a costi ridotti, a differenza dei dispositivi virtuali di rete che comportano costi di licenza aggiuntivi. Questa soluzione risulta estremamente conveniente nell'esempio 1, in cui non vengono usati dispositivi virtuali di rete. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale di esempio con script di PowerShell
- Come creare la rete perimetrale di esempio con un modello ARM
- Scenari dettagliati di flussi di traffico che illustrano il modo in qui il traffico scorre nella struttura

saranno presto disponibili, con collegamenti in questa pagina.

### Esempio 6: Aggiunta di una connessione ibrida con ExpressRoute
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con gateway connesso tramite rete ibrida][16]

#### Configurazione dell'ambiente
È possibile aggiungere reti ibride che usano una connessione con peering privato di ExpressRoute a qualsiasi tipo di rete perimetrale illustrato negli esempi 1 o 2.

Come illustrato nella figura precedente, il peering privato di ExpressRoute fornisce una connessione diretta tra la rete locale e la rete virtuale di Azure. Il traffico attraversa solo la rete del provider del servizio e la rete Microsoft/Azure, senza contatto con Internet.

>[AZURE.NOTE] Sono previste limitazioni per l'uso del routing definito dall'utente (User Defined Routing, UDR) con ExpressRoute, a causa della complessità del routing dinamico usato nel gateway virtuale di Azure. Queste limitazioni sono elencate di seguito:
>
> 1. Il routing definito dall'utente non deve essere applicato alla subnet del gateway in cui è connessa l'istanza di ExpressRoute collegata al gateway virtuale di Azure.
> 2. L'istanza di ExpressRoute collegata al gateway virtuale di Azure non può essere il dispositivo NextHop usato per altre subnet associate al routing definito dall'utente.
>
>La possibilità di integrare completamente il routing definito dall'utente ed ExpressRoute sarà abilitata in una versione futura di Azure.

<br />

>[AZURE.TIP] L'uso di ExpressRoute mantiene il traffico di rete aziendale lontano da Internet per una protezione e prestazioni significativamente migliori e consente contratti di servizio dal provider ExpressRoute. Poiché è correlato alle prestazioni di ExpressRoute, il gateway di Azure può passare fino a 2 Gbps con ExpressRoute, mentre la velocità massima effettiva con le VPN da sito a sito per il gateway di Azure è di 200 Mbps.

Come illustrato nel diagramma seguente, con questa opzione l'ambiente include ora due margini di rete, ovvero il dispositivo virtuale di rete e i gruppi di sicurezza di rete controllano i flussi di traffico per le reti interne di Azure e tra Azure e Internet, mentre il gateway è un margine di rete completamente separato e isolato tra le reti locali e Azure.

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di progettazione, in base al caso di utilizzo specifico.

Se si usa l'ambiente creato nell'esempio 1, "Creare una rete perimetrale per proteggere le applicazioni con gruppi di sicurezza di rete", e quindi si aggiunge una connessione di rete ibrida ExpressRoute, si otterrà la struttura seguente:

![Rete perimetrale con gateway connesso tramite una connessione ExpressRoute][17]

#### Conclusioni
L'aggiunta di una connessione di rete con peering privato di ExpressRoute può estendere la rete locale in Azure in un modo sicuro, con latenza ridotta e prestazioni migliori. L'uso del gateway nativo di Azure, come illustrato in questo esempio, offre un'opzione a costi ridotti, a differenza dei dispositivi virtuali di rete che comportano costi di licenza aggiuntivi. Altre informazioni su questo esempio, quali:

- Come creare la rete perimetrale di esempio con script di PowerShell
- Come creare la rete perimetrale di esempio con un modello ARM
- Scenari dettagliati di flussi di traffico che illustrano il modo in qui il traffico scorre nella struttura

saranno presto disponibili, con collegamenti in questa pagina.

## Riferimenti
### Siti Web e documentazione utili
- Accesso ad Azure con ARM: 
- Accesso ad Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- Documentazione sulle reti virtuali: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- Documentazione sui gruppi di sicurezza di rete: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- Documentazione sul routing definito dall'utente: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Gateway virtuali di Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- VPN da sito a sito: [https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/](./virtual-network/vpn-gateway-site-to-site-create.md)
- Documentazione su ExpressRoute (assicurarsi di consultare le sezioni relative all'introduzione e alle procedure): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagramma di flusso sulle opzioni di sicurezza"
[1]: ./media/best-practices-network-security/compliancebadges.png "Notifiche sulla conformità di Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Funzionalità di sicurezza di Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Rete perimetrale in una rete aziendale"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architettura di sicurezza di Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Rete perimetrale in una rete virtuale di Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rete ibrida con tre limiti di sicurezza"
[7]: ./media/best-practices-network-security/example1design.png "Rete perimetrale in ingresso con gruppo di sicurezza di rete"
[8]: ./media/best-practices-network-security/example2design.png "Rete perimetrale in ingresso con dispositivo virtuale di rete e gruppo di sicurezza di rete"
[9]: ./media/best-practices-network-security/example3design.png "Rete perimetrale bidirezionale con dispositivo virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Visualizzazione logica delle regole del firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Rete perimetrale con dispositivo virtuale di rete connesso tramite rete ibrida"
[12]: ./media/best-practices-network-security/example4designs2s.png "Rete perimetrale con dispositivo virtuale di rete connesso tramite VPN da sito a sito"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rete logica dal punto di vista del dispositivo virtuale di rete"
[14]: ./media/best-practices-network-security/example5designoptions.png "Rete perimetrale con gateway di Azure connesso tramite rete ibrida da sito a sito"
[15]: ./media/best-practices-network-security/example5designs2s.png "Rete perimetrale con gateway di Azure che usa la VPN da sito a sito"
[16]: ./media/best-practices-network-security/example6designoptions.png "Rete perimetrale con gateway di Azure connesso tramite rete ibrida ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Rete perimetrale con gateway di Azure che usa una connessione ExpressRoute"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md

<!---HONumber=AcomDC_0204_2016-->
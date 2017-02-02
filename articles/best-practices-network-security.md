---
title: Servizi cloud Microsoft e sicurezza della rete | Documentazione Microsoft
description: "Informazioni su alcune funzionalità chiave disponibili in Azure per creare ambienti di rete sicuri."
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jonor;sivae
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 42fe3f1789344f98782c3e24cdc19fe299ac7c02


---
# <a name="microsoft-cloud-services-and-network-security"></a>Servizi cloud Microsoft e sicurezza di rete
I Servizi cloud Microsoft offrono servizi e infrastruttura iperscalabili, capacità di livello aziendale e molte opzioni per la connettività ibrida. I clienti possono scegliere di accedere a questi servizi tramite Internet o con Azure ExpressRoute, che offre connettività di rete privata. La piattaforma Microsoft Azure consente ai clienti di estendere con facilità la propria infrastruttura nel cloud e sviluppare architetture a più livelli. Inoltre, le terze parti possono abilitare capacità avanzate offrendo servizi di sicurezza e appliance virtuali. Questo white paper offre una panoramica dei problemi relativi a sicurezza e architettura che i clienti dovrebbero tenere in considerazione quando usano i Servizi cloud Microsoft con accesso tramite ExpressRoute. Il documento illustra anche come creare servizi più sicuri sulle reti virtuali di Azure.

## <a name="fast-start"></a>Avvio veloce
Il grafico logico seguente indirizza a un esempio specifico delle numerose tecniche di sicurezza disponibili con la piattaforma Azure. Per un riferimento rapido, cercare l'esempio più adatto al proprio caso. Per informazioni complete, proseguire con la lettura del documento.
![Diagramma di flusso sulle opzioni di sicurezza][0]

[Esempio 1: Creare una rete perimetrale (anche nota come DMZ) per proteggere le applicazioni con gruppi di sicurezza di rete (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, route definite dall'utente (UDR) e gruppi di sicurezza di rete.](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Esempio 4: Aggiungere una connessione ibrida con un'appliance virtuale da sito a sito e con una rete virtuale privata (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network)</br>
[Esempio 5: Aggiungere una connessione ibrida con un gateway VPN di Azure da sito a sito.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[Esempio 6: Aggiungere una connessione ibrida con ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Nei prossimi mesi saranno resi disponibili in questo documento esempi per l'aggiunta di connessioni tra reti virtuali, la disponibilità elevata e il concatenamento dei servizi.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Conformità Microsoft e protezione dell'infrastruttura
Microsoft ha assunto una posizione di leadership nel supporto delle iniziative di conformità necessarie per i clienti aziendali. Queste sono alcune certificazioni di conformità ottenute da Azure: ![Notifiche sulla conformità di Azure][1]

Per informazioni dettagliate, vedere le informazioni sulla conformità nel [Centro protezione di Microsoft](https://azure.microsoft.com/support/trust-center/compliance/).

Microsoft offre un approccio completo per la protezione dell'infrastruttura cloud necessaria per l'esecuzione di servizi globali iperscalabili. Oltre ai data center fisici, l'infrastruttura cloud Microsoft include hardware, software, reti e personale con mansioni amministrative e operative.

![Funzionalità di sicurezza di Azure][2]

Questo tipo di approccio offre ai clienti una base più sicura per distribuire i propri servizi nel cloud Microsoft. Il passaggio successivo consiste nella progettazione e nella creazione di un'architettura di sicurezza da parte del cliente per la protezione dei servizi.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Architetture di sicurezza tradizionali e reti perimetrali
Nonostante gli importanti investimenti Microsoft nella protezione dell'infrastruttura cloud, anche i clienti devono proteggere i propri servizi cloud e i gruppi di risorse. Un approccio a più livelli per la sicurezza offre la difesa migliore. Un'area di sicurezza della rete perimetrale protegge le risorse di rete interne da una rete non attendibile. Con rete perimetrale si intendono i perimetri o le parti della rete compresa tra Internet e l'infrastruttura IT aziendale protetta.

Nelle reti aziendali tipiche l'infrastruttura di base viene protetta in modo rilevante nelle aree perimetrali, con più livelli di dispositivi di sicurezza. Il limite di ogni livello è costituito da dispositivi e punti di applicazione dei criteri. I dispositivi possono includere: firewall, prevenzione di attacchi DDoS (Distributed Denial of Service), sistemi di identificazione delle intrusioni o di protezione (IDS/IPS) e dispositivi VPN. L'applicazione dei criteri può essere effettuata tramite criteri del firewall, elenchi di controllo di accesso (ACL) o routing specifico. La prima linea di difesa della rete, che accetta direttamente traffico in arrivo da Internet, è costituita da una combinazione di questi meccanismi per bloccare gli attacchi e il traffico dannoso, senza impedire al tempo stesso il trasferimento di richieste legittime sulla rete. Il traffico si instrada direttamente verso le risorse della rete perimetrale. La risorsa interessata potrà quindi "comunicare" con risorse più interne della rete, attraversando prima il limite successivo per la convalida. Il livello più esterno è definito rete perimetrale perché questa parte della rete è esposta a Internet, in genere con qualche tipo di protezione su entrambi i lati. La figura seguente illustra un esempio di una rete perimetrale con subnet singola all'interno di una rete aziendale e due limiti di sicurezza.

![Una rete perimetrale in una rete aziendale][3]

Per implementare una rete perimetrale vengono usate numerose architetture, da un semplice servizio di bilanciamento del carico davanti a una Web farm a una rete perimetrale con più subnet dotate di diversi meccanismi in ogni limite per bloccare il traffico e proteggere i livelli più interni della rete aziendale. La modalità di costruzione della rete perimetrale dipende dalle esigenze specifiche dell'organizzazione e dalla tolleranza del rischio complessivo ad essa associata.

Quando i clienti spostano i propri carichi di lavoro nei cloud pubblici, è essenziale supportare capacità analoghe per l'architettura della rete perimetrale in Azure al fine di soddisfare i requisiti di conformità e sicurezza. Questo documento offre indicazioni su come creare un ambiente di rete sicuro in Azure. Il documento è incentrato sulla rete perimetrale, ma include anche una discussione approfondita dei numerosi aspetti riguardanti la sicurezza di rete. La discussione risponde alle domande seguenti:

* Come è possibile creare una rete perimetrale in Azure?
* Quali sono alcune delle funzionalità di Azure disponibili per la creazione della rete perimetrale?
* Come è possibile proteggere i carichi di lavoro back-end?
* In che modo vengono controllate le comunicazioni Internet per i carichi di lavoro in Azure?
* In che modo è possibile proteggere le reti locali da distribuzioni in Azure?
* Quando è consigliabile usare le funzionalità di sicurezza native di Azure invece di appliance o servizi di terze parti?

Il diagramma seguente mostra i vari livelli di sicurezza che Azure offre ai clienti. Questi livelli sono costituiti sia da funzionalità native della piattaforma Azure sia da funzionalità definite dal cliente:

![Architettura di sicurezza di Azure][4]

La protezione DDoS protegge il traffico in ingresso da Internet da attacchi su larga scala contro Azure. Il livello successivo è costituito da endpoint pubblici definiti dall'utente, che vengono usati per determinare il traffico che può passare alla rete virtuale attraverso il servizio cloud. L'isolamento nativo della rete virtuale di Azure assicura l'isolamento completo da tutte le altre reti e il flusso di traffico solo tramite percorsi e metodi configurati dall'utente. Questi percorsi e metodi costituiscono il livello successivo, in cui è possibile usare NSG, UDR e appliance virtuali di rete per creare limiti di sicurezza e proteggere le distribuzioni delle applicazioni nella rete protetta.

La sezione che segue fornisce una panoramica delle reti virtuali di Azure. Queste reti virtuali di Azure vengono create dai clienti e i carichi di lavoro distribuiti dei clienti sono collegati ad esse. Le reti virtuali sono alla base di tutte le funzionalità di sicurezza di rete necessarie per stabilire una rete perimetrale in grado di proteggere le distribuzioni ai clienti in Azure.

## <a name="overview-of-azure-virtual-networks"></a>Panoramica delle reti virtuali di Azure
Prima che il traffico Internet possa raggiungere le reti virtuali di Azure, sono disponibili due livelli di sicurezza insiti nella piattaforma Azure:

1. **Protezione DDoS**: la protezione DDoS è un livello della rete fisica di Azure che protegge la piattaforma Azure stessa da attacchi su larga scala basati su Internet. Per questi attacchi vengono usati più nodi "bot" nel tentativo di sovraccaricare un servizio Internet. Azure vanta una robusta rete di protezione DDoS su tutta la connettività Internet in ingresso. Questo livello di protezione DDoS non dispone di attributi configurabili dall'utente e non è accessibile al cliente. Protegge la piattaforma Azure da attacchi su larga scala, ma non proteggerà direttamente le applicazioni dei singoli clienti. Il cliente può configurare livelli aggiuntivi di resilienza contro un attacco localizzato. Ad esempio, se il cliente A ha subito un attacco DDoS su larga scala su un endpoint pubblico, Azure blocca le connessioni a quel servizio. Per ripristinare il servizio, il cliente A può eseguire il failover su un'altra rete virtuale o un altro endpoint di servizio non coinvolto nell'attacco. Si noti che, sebbene sia possibile che il cliente A venga interessato dall'attacco su tale endpoint, nessun altro servizio all'esterno dell'endpoint subirà l'attacco. Altri clienti e servizi, inoltre, non subiranno alcun effetto di tale attacco.
2. **Endpoint di servizio**: gli endpoint consentono ai servizi cloud o ai gruppi di risorse di avere indirizzi IP pubblici su Internet e porte esposti. L'endpoint userà il processo NAT (Network Address Translation) per instradare il traffico fino all'indirizzo e alla porta interni nella rete virtuale di Azure. Questo è il percorso principale che consente al traffico esterno di passare attraverso la rete virtuale. Gli endpoint di servizio possono essere configurati dall'utente per determinare il traffico autorizzato a passare e come/dove viene convertito nella rete virtuale.

Quando il traffico raggiunge la rete virtuale, sono disponibili molte funzionalità. Le reti virtuali di Azure costituiscono la base a cui i clienti collegano i rispettivi carichi di lavoro e a cui si applica la sicurezza a livello di rete di base. Si tratta di una rete privata (un overlay di rete virtuale) in Azure per i clienti, che offre le funzionalità e le caratteristiche seguenti:

* **Isolamento del traffico**: una rete virtuale è il limite di isolamento del traffico nella piattaforma Azure. Le macchine virtuali (VM) in una rete virtuale non possono comunicare direttamente con le VM in una rete virtuale diversa, anche se entrambe le reti virtuali vengono create dallo stesso cliente. Questa è una proprietà essenziale, che assicura che le macchine virtuali e le comunicazioni dei clienti rimangano private entro una rete virtuale.
* **Topologia a più livelli**: le reti virtuali consentono ai clienti di definire una topologia a più livelli, allocando subnet e designando spazi di indirizzi separati per diversi elementi o "livelli" dei rispettivi carichi di lavoro. Questi raggruppamenti logici e queste topologie consentono ai clienti di definire diversi criteri di accesso in base ai tipi di carico di lavoro e anche di controllare i flussi del traffico tra i livelli.
* **Connettività cross-premise**: i clienti possono stabilire la connettività cross-premise tra una rete virtuale e più siti locali o altre reti virtuali in Azure. A tale scopo, i clienti possono usare i gateway VPN di Azure o appliance virtuali di reti di terze parti. Azure supporta VPN da sito a sito (S2S, site-to-site) mediante protocolli IPsec/IKE standard e la connettività privata di ExpressRoute.
* **Gruppo di sicurezza di rete (NSG)** : consente ai clienti di creare regole (ACL) al livello di granularità desiderato, ovvero interfacce di rete, singole VM o subnet virtuali. I clienti possono controllare l'accesso autorizzando o negando la comunicazione tra i carichi di lavoro all'interno di una rete virtuale, da sistemi nelle reti dei clienti tramite la connettività cross-premise oppure la comunicazione Internet diretta.
* La **route definite dall'utente** e l'**inoltro IP** consentono ai clienti di definire i percorsi di comunicazione tra livelli diversi all'interno di una rete virtuale. I clienti possono distribuire un firewall, IDS/IPS e altre appliance virtuali e instradare il traffico di rete attraverso queste appliance di sicurezza per l'applicazione dei criteri relativi ai limiti di sicurezza, il controllo e l'ispezione.
* **Appliance virtuali di rete** in Azure Marketplace: le appliance di sicurezza, ad esempio firewall, servizi di bilanciamento del carico e IDS/IPS, sono disponibili in Azure Marketplace e nella raccolta di immagini delle VM. I clienti possono distribuire queste appliance nelle proprie reti virtuali e, in particolare, nei propri limiti di sicurezza (incluse le subnet della rete perimetrale) per completare un ambiente di rete sicuro a più livelli.

Con queste funzionalità e capacità, ad esempio, è possibile creare in Azure l'architettura di rete perimetrale seguente:

![Una rete perimetrale in una rete virtuale di Azure][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Caratteristiche e requisiti della rete perimetrale
La rete perimetrale è progettata per essere il front-end della rete e si interfaccia direttamente con le comunicazioni provenienti da Internet. I pacchetti in ingresso devono attraversare le appliance di sicurezza, ovvero firewall, IDS e IPS, prima di raggiungere i server back-end. Anche i pacchetti diretti a Internet dai carichi di lavoro possono attraversare le appliance di sicurezza nella rete perimetrale per finalità di applicazione dei criteri, ispezione e controllo, prima di lasciare la rete. Inoltre, la rete perimetrale può essere usata per ospitare gateway VPN cross-premise tra reti virtuali del cliente e reti locali.

### <a name="perimeter-network-characteristics"></a>Caratteristiche della rete perimetrale
Con riferimento alla figura precedente, di seguito vengono riportate alcune caratteristiche di una rete perimetrale efficiente:

* Connessione a Internet:
  * La subnet perimetrale stessa è rivolta a Internet e comunica direttamente con Internet.
  * Gli indirizzi IP pubblici, gli indirizzi VIP e/o gli endpoint di servizio attraversano il traffico Internet per raggiungere la rete e i dispositivi front-end.
  * Il traffico in ingresso da Internet passa attraverso dispositivi di sicurezza prima di altre risorse sulla rete front-end.
  * Se la sicurezza in uscita è abilitata, il traffico attraversa i dispositivi di sicurezza, come passaggio finale, prima di passare a Internet.
* Rete protetta:
  * Non vi sono percorsi diretti da Internet all'infrastruttura di base.
  * I canali verso l'infrastruttura di base devono attraversare i dispositivi di sicurezza, come ad esempio i gruppi di sicurezza di rete, i firewall o i dispositivi VPN.
  * Altri dispositivi non devono eseguire il bridging tra Internet e l'infrastruttura di base.
  * I dispositivi di sicurezza nella rete rivolta a Internet e nella rete protetta rivolta ai limiti della rete perimetrale (ad esempio le due icone del firewall presenti nella figura precedente) possono in effetti costituire una singola appliance virtuale con regole o interfacce differenziate per ogni limite (ovvero, un dispositivo, separato logicamente, che gestisce il carico per entrambi i limiti della rete perimetrale).
* Altri vincoli e procedure comuni:
  * I carichi di lavoro non devono archiviare informazioni essenziali per l'azienda.
  * L'accesso e gli aggiornamenti alle configurazioni e alle distribuzioni della rete perimetrale sono limitati solo agli amministratori autorizzati.

### <a name="perimeter-network-requirements"></a>Requisiti della rete perimetrale
Per abilitare queste caratteristiche, seguire le indicazioni riportate di seguito sui requisiti della rete virtuale necessari per implementare una rete perimetrale efficace:

* **Architettura della subnet:** specificare la rete virtuale in modo che un'intera subnet sia dedicata come rete perimetrale, separata da altre subnet nella stessa rete virtuale. In questo modo si garantisce che il traffico tra la rete perimetrale e altri livelli di subnet interne o private attraversi un firewall o un'appliance virtuale IDS/IPS nei limiti della subnet con route definite dall'utente.
* **Gruppo di sicurezza di rete (NSG):** la subnet perimetrale stessa deve essere aperta per consentire le comunicazioni con Internet, ma ciò non significa che i clienti devono ignorare i gruppi di sicurezza di rete. Osservare le procedure di sicurezza comuni per ridurre al minimo l'esposizione a Internet delle superfici di rete. Bloccare gli intervalli di indirizzi remoti autorizzati ad accedere alle distribuzioni o i protocolli applicativi specifici e le porte aperte. Tuttavia, in alcuni casi non è possibile usare questa procedura. Ad esempio, se i clienti hanno un sito Web esterno in Azure, la rete perimetrale dovrà consentire le richieste Web in ingresso da qualsiasi indirizzo IP pubblico, ma dovrà aprire solo le porte di applicazione Web TCP:80 e TCP:443.
* **Tabella di routing** : la subnet perimetrale stessa dovrebbe essere in grado di comunicare direttamente con Internet, ma non deve consentire le comunicazioni dirette verso e da il back-end o dalle reti locali senza attraversare un firewall o un'appliance di sicurezza.
* **Configurazione dei dispositivi di sicurezza:** per instradare e ispezionare pacchetti tra la rete perimetrale e il resto delle reti protette, le appliance di sicurezza, come ad esempio i firewall e i dispositivi IDS e IPS, possono essere multihomed. Questi dispositivi potrebbero disporre di schede di interfaccia di rete separate per la rete perimetrale e le subnet back-end. Le schede di interfaccia di rete della rete perimetrale comunicano direttamente verso e da Internet, con i gruppi di sicurezza di rete e la tabella di routing della rete perimetrale corrispondenti. Le schede di interfaccia di rete che si connettono alle subnet back-end avranno gruppi di sicurezza di rete e tabelle di routing delle subnet back-end corrispondenti con più restrizioni.
* **Funzionalità dell'appliance di sicurezza:** le appliance di sicurezza distribuite nella rete perimetrale offrono in genere le funzionalità seguenti:
  * Firewall: applicazione delle regole del firewall o dei criteri di controllo di accesso per le richieste in ingresso.
  * Rilevamento e prevenzione delle minacce: rilevamento e mitigazione degli attacchi dannosi provenienti da Internet.
  * Controllo e registrazione: gestione di log dettagliati per il controllo e l'analisi.
  * Proxy inverso: reindirizzamento delle richieste in ingresso verso i server back-end corrispondenti. Questo comporta il mapping e la conversione degli indirizzi di destinazione nei dispositivi front-end, in genere firewall, verso gli indirizzi dei server back-end.
  * Proxy di inoltro: processi NAT ed esecuzione di controlli per le comunicazioni avviate dall'interno della rete virtuale verso Internet.
  * Router: inoltro del traffico in ingresso e tra le subnet all'interno della rete virtuale.
  * Dispositivo VPN: funzione di gateway VPN cross-premise per la connettività VPN cross-premise tra le reti locali dei clienti e le reti virtuali di Azure.
  * Server VPN: accettazione dei client VPN che si connettono alle reti virtuali di Azure.

> [!TIP]
> Mantenere i seguenti due gruppi separati: gli individui autorizzati ad accedere ai dispositivi di sicurezza della rete perimetrale e gli individui autorizzati come amministratori per lo sviluppo, la distribuzione e le operazioni sulle applicazioni. Se si mantengono separati questi gruppi, sarà possibile separare nettamente i compiti e impedire a una singola persona di ignorare i controlli di sicurezza delle applicazioni e della rete.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Domande da porre durante la creazione dei limiti della rete
In questa sezione, se non espressamente specificato, il termine "reti" si riferisce a reti private virtuali di Azure create da un amministratore della sottoscrizione. Il termine non fa riferimento a reti fisiche sottostanti all'interno di Azure.

Le reti virtuali di Azure, inoltre, vengono usate spesso per estendere reti locali tradizionali. È possibile incorporare soluzioni di rete ibride da sito a sito o ExpressRoute con le architetture della rete perimetrale. Questo è un fattore da tenere in considerazione durante la creazione dei limiti di sicurezza della rete.

Le tre domande che seguono sono fondamentali per la creazione di una rete con una rete perimetrale e numerosi limiti di sicurezza.

#### <a name="1-how-many-boundaries-are-needed"></a>1) Quanti limiti servono?
Il primo punto di decisione consiste nel decidere il numero di limiti di sicurezza necessari in un determinato scenario:

* Un singolo limite: un limite nella rete perimetrale front-end tra la rete virtuale e Internet.
* Due limiti: uno sul lato Internet della rete perimetrale, un altro tra la subnet perimetrale e le subnet back-end nelle reti virtuali di Azure.
* Tre limiti: uno sul lato Internet della rete perimetrale, un altro tra la subnet perimetrale e le subnet back-end e l'ultimo tra le subnet back-end e la rete locale.
* Numero di limiti: variabile. In base ai requisiti di sicurezza, non è previsto alcuna restrizione al numero di limiti di sicurezza applicabili in una determinata rete.

Il numero e il tipo di limiti necessari dipende dalla tolleranza del rischio dell'azienda e dallo scenario specifico da implementare. Si tratta spesso di una decisione comune presa da più gruppi di un'organizzazione, spesso in collaborazione con un team responsabile dei rischi e della conformità, un team responsabile della rete e della piattaforma e un team responsabile dello sviluppo di applicazioni. Le persone esperte in ambito di sicurezza, dei dati coinvolti e delle tecnologie da usare dovrebbero poter partecipare a questa decisione, per assicurare la strategia di sicurezza appropriata per ogni implementazione.

> [!TIP]
> Usare il numero minore possibile di limiti in grado di soddisfare i requisiti di sicurezza per una determinata situazione. Un numero maggiore di limiti può rendere più difficili le operazioni e la risoluzione dei problemi, oltre a incrementare l'overhead di gestione correlato alla gestione di più criteri di limite nel tempo. Un numero insufficiente di limiti, tuttavia, comporta un aumento del rischio. È essenziale trovare il giusto equilibrio.
>
>

![Rete ibrida con tre limiti di sicurezza][6]

La figura precedente mostra una visualizzazione generale di una rete con tre limiti di sicurezza. I limiti sono posizionati tra la rete perimetrale e Internet, tra le subnet private front-end e back-end di Azure e tra la subnet back-end di Azure e la rete aziendale locale.

#### <a name="2-where-are-the-boundaries-located"></a>2) Dove sono situati i limiti?
Dopo avere stabilito il numero di limiti, occorre determinare dove implementarli. In genere, sono disponibili tre opzioni:

* Usare un servizio intermediario basato su Internet (ad esempio, un Web application firewall basato su cloud, non trattato in questo documento)
* Usare funzionalità native e/o appliance virtuali di rete in Azure
* Usare dispositivi fisici nella rete locale

In reti puramente Azure le opzioni disponibili sono le funzionalità di Azure native (ad esempio i servizi di bilanciamento del carico di Azure) oppure le appliance virtuali di rete presenti nel ricco ecosistema di partner di Azure (ad esempio i firewall Check Point).

Se è necessario impostare un limite tra Azure e una rete locale, le appliance di sicurezza possono trovarsi su un qualsiasi lato della connessione (o su entrambi i lati). È quindi necessario stabilire in che posizione inserire i dispositivi di sicurezza.

Nella figura precedente i limiti da Internet alla rete perimetrale e dal front-end sono inclusi interamente in Azure e devono essere funzionalità native di Azure o appliance virtuali di rete. Le appliance di sicurezza sul limite tra Azure (subnet back-end) e la rete aziendale possono essere sul lato Azure o sul lato locale oppure è possibile usare una combinazione di appliance su entrambi i lati. Occorre valutare con attenzione i vantaggi e gli svantaggi significativi di entrambe le opzioni.

Ad esempio, l'uso dei dispositivi fisici di sicurezza esistenti sulla rete locale consente di non usare necessariamente nuovi dispositivi. È sufficiente la riconfigurazione dei dispositivi esistenti. Di contro, tutto il traffico deve tornare da Azure alla rete locale per potere essere visualizzato dal dispositivo di sicurezza. Il traffico da Azure ad Azure potrebbe quindi subire una latenza significativa e influire sulle prestazioni delle applicazioni e sull'esperienza utente, se ne è stato imposto il ritorno alla rete locale tramite l'applicazione dei criteri di sicurezza.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) In che modo vengono implementati i limiti?
È probabile che per ogni limite di sicurezza siano previsti requisiti di capacità diversi, ad esempio regole per IDS e firewall sul lato Internet della rete perimetrale, ma solo elenchi di controllo di accesso tra la rete perimetrale e la subnet back-end. La decisione relativa ai dispositivi da usare dipende dallo scenario e dai requisiti di sicurezza. Nella sezione seguente, gli esempi 1, 2 e 3 illustrano alcune opzioni che possono essere utilizzate. La verifica delle funzionalità di rete native di Azure e dei dispositivi disponibili in Azure dall'ecosistema partner mette in luce le numerosissime opzioni disponibili per risolvere quasi tutti gli scenari.

Un altro punto di decisione essenziale per l'implementazione consiste nella modalità di connessione della rete locale con Azure. Sarebbe opportuno usare il gateway virtuale di Azure o un'appliance virtuale di rete? Nella seguente sezione vengono illustrate nel dettaglio queste opzioni (esempi 4, 5 e 6).

Inoltre, potrebbe essere necessario consultare informazioni sul traffico tra reti virtuali all'interno di Azure. Questi scenari verranno aggiunti in un secondo momento.

Una volta ricevute le risposte alle domande precedenti, la sezione [Avvio veloce](#fast-start) può semplificare l'identificazione degli esempi più appropriati per un particolare scenario.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Esempi: Creare limiti di sicurezza con le reti virtuali di Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Esempio 1: Creare una rete perimetrale per proteggere le applicazioni con i gruppi di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example1]

![Rete perimetrale in ingresso con gruppi di sicurezza di rete][7]

#### <a name="environment-description"></a>Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

* Due servizi cloud, "FrontEnd001" e "BackEnd001"
* Una rete virtuale, "CorpNetwork", con due subnet: "FrontEnd" e "BackEnd"
* Un gruppo di sicurezza di rete applicato a entrambe le subnet
* Un server Windows che rappresenta un server Web applicazioni ("IIS01")
* Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
* Un server Windows che rappresenta un server DNS ("DNS01")

Per gli script e un modello di Azure Resource Manager, vedere le [istruzioni di creazione dettagliate][Example1].

#### <a name="nsg-description"></a>Descrizione di un gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

> [!TIP]
> In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega", più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una determinata regola, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita (dal punto di vista della subnet).
>
>

A livello dichiarativo, per il traffico in ingresso vengono create le righe seguenti:

1. Il traffico DNS interno (porta 53) è consentito.
2. Il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale è consentito.
3. Il traffico HTTP (porta 80) da Internet al server Web (IIS01) è consentito.
4. Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5. Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6. Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene bloccato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet verso il server Web,  entrambe le regole 3 (consenti) e 5 (nega) saranno applicabili. Tuttavia, poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul server Web. Se lo stesso traffico prova a raggiungere il server DNS01, la regola 5 (nega) sarà la prima applicabile e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) blocca la comunicazione tra la subnet front-end e la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4). Ciò consente di proteggere la rete di back-end nel caso in cui un utente malintenzionato comprometta l'applicazione web sul front-end. L'utente malintenzionato potrebbe disporre di accesso limitato alla rete di back-end "protetta" (solo verso risorse esposte sul server AppVM01).

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è necessario il routing definito dall'utente (consultare l'esempio 3).

#### <a name="conclusion"></a>Conclusioni
Si tratta di un metodo relativamente semplice e diretto per isolare la subnet back-end dal traffico in ingresso. Per altre informazioni, vedere le [istruzioni di creazione dettagliate][Example1]. Le istruzioni includono:

* Come creare la rete perimetrale con script di PowerShell
* Come creare la rete perimetrale con un modello di Azure Resource Manager
* Descrizioni dettagliate di ogni comando del gruppo di sicurezza di rete
* Scenari dettagliati del flusso di traffico che illustrano il modo in cui il traffico viene consentito o negato su ogni livello

### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Esempio 2: Creare una rete perimetrale per proteggere le applicazioni con un firewall e gruppi di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example2]

![Rete perimetrale in ingresso con appliance virtuali di rete e gruppi di sicurezza di rete][8]

#### <a name="environment-description"></a>Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

* Due servizi cloud, "FrontEnd001" e "BackEnd001"
* Una rete virtuale, "CorpNetwork", con due subnet: "FrontEnd" e "BackEnd"
* Un gruppo di sicurezza di rete applicato a entrambe le subnet
* Un'appliance virtuale di rete, in questo caso un firewall connesso alla subnet front-end
* Un server Windows che rappresenta un server Web applicazioni ("IIS01")
* Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
* Un server Windows che rappresenta un server DNS ("DNS01")

Per gli script e un modello di Azure Resource Manager, vedere le [istruzioni di creazione dettagliate][Example2].

#### <a name="nsg-description"></a>Descrizione di un gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui vengono poi caricate sei regole.

> [!TIP]
> In genere, è consigliabile creare prima di tutto le regole specifiche di tipo "Consenti" e infine le regole di tipo "Nega", più generiche. La priorità assegnata determina quali regole vengono valutate per prime. Quando si rileva che al traffico è applicabile una determinata regola, non vengono valutate altre regole. Le regole del gruppo di sicurezza di rete possono essere applicate nella direzione in ingresso o in uscita (dal punto di vista della subnet).
>
>

A livello dichiarativo, per il traffico in ingresso vengono create le righe seguenti:

1. Il traffico DNS interno (porta 53) è consentito.
2. Il traffico RDP (porta 3389) da Internet a qualsiasi macchina virtuale è consentito.
3. Tutto il traffico Internet (tutte le porte) verso l'appliance virtuale di rete (firewall) è consentito.
4. Tutto il traffico (tutte le porte) da IIS01 ad AppVM1 è consentito.
5. Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (entrambe le subnet) viene bloccato.
6. Tutto il traffico (tutte le porte) dalla subnet front-end alla subnet back-end viene bloccato.

Con queste regole associate a ogni subnet, se una richiesta HTTP proviene da Internet verso il firewall, le regole 3 (consenti) e 5 (nega) saranno applicabili, Tuttavia, poiché la regola 3 ha una priorità maggiore, verrà applicata solo tale regola e la regola 5 non verrà presa in considerazione. La richiesta HTTP verrà quindi consentita sul firewall. Se lo stesso traffico prova a raggiungere il server IIS01, anche se si trova sulla subnet front-end, la regola 5 (nega) verrà applicata e il traffico non sarà autorizzato a passare al server. La regola 6 (nega) blocca la comunicazione tra la subnet front-end e la subnet back-end (ad eccezione del traffico consentito nelle regole 1 e 4). Ciò consente di proteggere la rete di back-end nel caso in cui un utente malintenzionato comprometta l'applicazione web sul front-end. L'utente malintenzionato potrebbe disporre di accesso limitato alla rete di back-end "protetta" (solo verso risorse esposte sul server AppVM01).

Esiste una regola in uscita predefinita che consente il traffico in uscita verso Internet. Per questo esempio si consente il traffico in uscita e non si modificano le regole in uscita. Per bloccare il traffico in entrambe le direzioni, è necessario il routing definito dall'utente (consultare l'esempio 3).

#### <a name="firewall-rule-description"></a>Descrizione della regola del firewall
Sul firewall è necessario creare regole di inoltro. Poiché questo esempio instrada solo il traffico Internet in ingresso verso il firewall e quindi verso il server Web, sarà necessaria solo una regola del processo NAT (Network Address Translation) di inoltro.

La regola di inoltro accetterà qualsiasi indirizzo di origine in ingresso che raggiunge il firewall nel tentativo di raggiungere HTTP (porta 80 o 443 per HTTPS). Gli indirizzi verranno trasmessi all'interfaccia locale del firewall e reindirizzati al server Web con indirizzo IP 10.0.1.5.

#### <a name="conclusion"></a>Conclusioni
Si tratta di un metodo relativamente semplice per proteggere l'applicazione con un firewall e isolare la subnet back-end dal traffico in ingresso. Per altre informazioni, vedere le [istruzioni di creazione dettagliate][Example2]. Le istruzioni includono:

* Come creare la rete perimetrale con script di PowerShell
* Come creare la rete perimetrale di esempio con un modello di Azure Resource Manager
* Descrizioni dettagliate di ogni comando del gruppo di sicurezza di rete e di ogni regola del firewall
* Scenari dettagliati del flusso di traffico che illustrano il modo in cui il traffico viene consentito o negato su ogni livello

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Esempio 3: Creare una rete perimetrale per proteggere le reti con un firewall, route definite dall'utente e gruppi di sicurezza di rete
[Torna all'avvio veloce](#fast-start) | [Istruzioni di creazione dettagliate per questo esempio][Example3]

![Rete perimetrale bidirezionale con appliance virtuali di rete, gruppi di sicurezza di rete e route definite dall'utente][9]

#### <a name="environment-description"></a>Descrizione dell'ambiente
In questo esempio è presente una sottoscrizione che include gli elementi seguenti:

* Tre servizi cloud, "SecSvc001", "FrontEnd001" e "BackEnd001".
* Una rete virtuale, "CorpNetwork", con tre subnet: "SecNet", "FrontEnd" e "BackEnd"
* Un'appliance virtuale di rete, in questo caso un firewall connesso alla subnet "SecNet"
* Un server Windows che rappresenta un server Web applicazioni ("IIS01")
* Due server Windows che rappresentano server back-end applicazioni ("AppVM01", "AppVM02")
* Un server Windows che rappresenta un server DNS ("DNS01")

Per gli script e un modello di Azure Resource Manager, vedere le [istruzioni di creazione dettagliate][Example3].

#### <a name="udr-description"></a>Descrizione delle route definite dall'utente (UDR)
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

VNETLocal è sempre il prefisso degli indirizzi definiti della rete virtuale per la rete specifica (ovvero, cambia da una rete virtuale all'altra, a seconda della definizione di ogni specifica rete virtuale). Le route di sistema rimanenti sono statiche e predefinite come indicato nella tabella.

In questo esempio vengono create due tabelle di routing, una per la subnet front-end e una per la subnet back-end. In ogni tabella vengono caricate le route statiche appropriate per la subnet specifica. Ai fini di questo esempio, ogni tabella contiene tre route che indirizzano tutto il traffico (0.0.0.0/0) attraverso il firewall (hop successivo = indirizzo IP dell'appliance virtuale):

1. Traffico della subnet locale senza la definizione di un hop successivo per consentire al traffico della subnet locale di ignorare il firewall.
2. Traffico della rete virtuale con la definizione di un hop successivo come firewall, in modo da eseguire l'override della regola predefinita che consente l'instradamento diretto del traffico della rete virtuale locale.
3. Tutto il traffico rimanente (0/0) con un hop successivo definito come firewall.

> [!TIP]
> Se la voce della rete locale non è presente nella route definita dall'utente, verranno interrotte le comunicazioni con la subnet locale.
>
> * Nell'esempio è essenziale che 10.0.1.0/24 faccia riferimento a VNETLocal, altrimenti un pacchetto che lascia il server Web Server (10.0.1.4) destinato a un altro server locale (ad esempio, 10.0.1.25) avrà esito negativo perché verrà inviato tramite l'appliance virtuale di rete, che lo invierà alla subnet, e la subnet lo invierà di nuovo all'appliance virtuale di rete e così via.
> * Le possibilità di un loop di routing in genere sono maggiori nelle appliance a più NIC direttamente connesse a ogni subnet con cui sono in comunicazione, come di solito accade per le appliance tradizionali locali.
>
>

Dopo la creazione, le tabelle di routing vengono associate alle rispettive subnet. Dopo la creazione e l'associazione alla subnet, la tabella di routing della subnet front-end avrà un aspetto analogo al seguente:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> La route definita dall'utente ora può essere applicata alla subnet del gateway a cui è connesso il circuito ExpressRoute.
>
> Negli esempi 3 e 4 sono illustrate le modalità per abilitare la rete perimetrale con la rete da sito a sito o con ExpressRoute.
>
>

#### <a name="ip-forwarding-description"></a>Descrizione dell'inoltro IP
L'inoltro IP è una funzionalità complementare delle route definite dall'utente. Si tratta di un'impostazione in un'appliance virtuale che consente al dispositivo di ricevere traffico non indirizzato in modo specifico ad essa e quindi di inoltrare il traffico alla destinazione finale.

Ad esempio, se il traffico da AppVM01 effettua una richiesta al server DNS01, le route definite dall'utente instraderanno tale richiesta al firewall. Se l'inoltro IP è abilitato, il traffico per la destinazione DNS01 (10.0.2.4) viene accettato dall'appliance (10.0.0.4) e quindi inoltrato alla destinazione finale (10.0.2.4). Se l'inoltro IP non è abilitato sul firewall, il traffico non verrà accettato dall'appliance, anche se la tabella di route specifica il firewall come hop successivo. Per usare un dispositivo virtuale, è essenziale ricordare di abilitare l'inoltro IP insieme alle route definite dall'utente.

#### <a name="nsg-description"></a>Descrizione di un gruppo di sicurezza di rete
In questo esempio viene creato un gruppo di sicurezza di rete, in cui viene poi caricata una singola regola. Questo gruppo viene quindi associato solo alle subnet front-end e back-end (non a SecNet). La regola seguente viene creata in modo dichiarativo:

* Tutto il traffico (tutte le porte) da Internet all'intera rete virtuale (tutte le subnet) viene bloccato.

Anche se in questo esempio vengono usati i gruppi di sicurezza di rete: lo scopo principale consiste nel creare un secondo livello di difesa contro errori di configurazione manuale. L'obiettivo è bloccare tutto il traffico in ingresso da Internet verso le subnet front-end o back-end. Il traffico deve solo attraversare la subnet SecNet verso il firewall (e quindi, se appropriato, deve raggiungere le subnet front-end o back-end). Con l'abilitazione delle regole delle route definite dall'utente, inoltre, tutto il traffico che raggiunge le subnet front-end o back-end verrà indirizzato verso il firewall (grazie alle route definite dall'utente). Il firewall considera questo traffico come un flusso asimmetrico e rimuove il traffico in uscita. Sono quindi disponibili tre livelli di sicurezza per la protezione delle subnet:

* Nessun endpoint aperto nei servizi cloud FrontEnd001 e BackEnd001
* Gruppi di sicurezza di rete che non consentono il traffico da Internet
* Rimozione del traffico asimmetrico da parte del firewall

Un aspetto interessante del gruppo di sicurezza di rete di questo esempio consiste nel fatto che include solo una regola, ovvero non consentire il traffico Internet nell'intera rete virtuale, inclusa la subnet di sicurezza. Poiché, tuttavia, il gruppo di sicurezza di rete è associato solo alle subnet front-end e back-end, la regola non viene elaborata sul traffico in ingresso verso la subnet di sicurezza. Di conseguenza, il traffico raggiungerà la subnet di sicurezza.

#### <a name="firewall-rules"></a>Regole del firewall
Sul firewall è necessario creare regole di inoltro. Poiché il firewall blocca o inoltra tutto il traffico in ingresso, in uscita e tra le reti virtuali, sono necessarie molte regole del firewall. Tutto il traffico in ingresso, inoltre, raggiungerà l'indirizzo IP pubblico del servizio di sicurezza su porte diverse, per l'elaborazione da parte del firewall. Una procedura consigliata consiste nel creare un diagramma dei flussi logici prima di configurare le regole delle subnet e del firewall, per evitare modifiche successive. La figura seguente è una visualizzazione logica delle regole del firewall per questo esempio:

![Visualizzazione logica delle regole del firewall][10]

> [!NOTE]
> Le porte di gestione dipendono dal dispositivo virtuale di rete usato. In questo esempio si fa riferimento a Barracuda NextGen Firewall, che usa le porte 22, 801 e 807. Per informazioni precise sulle porte usate per la gestione del dispositivo in uso, consultare la documentazione del fornitore del dispositivo.
>
>

#### <a name="firewall-rules-description"></a>Descrizione delle regole del firewall
Nel diagramma logico precedente la subnet di sicurezza non viene mostrata, perché il firewall è l'unica risorsa su tale subnet e questo diagramma illustra le regole del firewall e il modo in cui le regole consentono o negano logicamente i flussi di traffico, non il percorso instradato effettivo. Le porte esterne selezionate per il traffico RDP, inoltre, sono le porte incluse negli intervalli più elevati (8014 - 8026) e sono state selezionate per allinearsi almeno in parte con gli ultimi due ottetti dell'indirizzo IP locale al fine di facilitarne la lettura (ad esempio, l'indirizzo del server locale 10.0.1.4 è associato alla porta esterna 8014). È tuttavia possibile usare qualsiasi porta superiore non in conflitto.

Per questo esempio, sono necessari sette tipi di regole:

* Regole esterne (per il traffico in ingresso):
  1. Regola di gestione del firewall: questa regola di reindirizzamento dell'app consente al traffico di raggiungere le porte di gestione dell'appliance virtuale di rete.
  2. Regole RDP (per ogni server Windows): queste quattro regole (una per ogni server) consentono la gestione dei singoli server tramite RDP. È anche possibile raggruppare queste regole in una sola regola, in base alle capacità dell'appliance virtuale di rete usata.
  3. Regole del traffico dell'applicazione: sono disponibili due regole, la prima per il traffico Web front-end e la seconda per il traffico back-end (ad esempio dal server Web al livello dati). La configurazione di queste regole dipende dall'architettura di rete (ovvero dove sono posizionati i server) e dai flussi di traffico (ovvero la direzione dei flussi di traffico e le porte usate).
     * La prima regola consente al traffico effettivo dell'applicazione di raggiungere il server applicazioni. Anche se altre regole consentono la sicurezza e la gestione, le regole delle del traffico dell'applicazione consentono a utenti o servizi esterni di accedere alle applicazioni. In questo esempio è presente un singolo server Web sulla porta 80. Pertanto, una singola regola delle applicazioni per il firewall reindirizza il traffico in ingresso verso l'indirizzo IP esterno e verso l'indirizzo IP interno dei server Web. La sessione di traffico reindirizzato sarà trasferita tramite il processo NAT verso il server interno.
     * La seconda regola è la regola back-end per consentire al server Web di comunicare con il server AppVM01 (ma non con AppVM02) tramite qualsiasi porta.
* Regole interne (per il traffico tra le reti virtuali)
  1. Regola in uscita verso Internet: questa regola consente al traffico proveniente da qualsiasi rete di passare alle reti selezionate. Questa regola è in genere una regola predefinita già disponibile sul firewall, ma con stato disabilitato. È consigliabile abilitarla per questo esempio.
  2. Regola DNS: questa regola consente solo al traffico DNS (porta 53) di raggiungere il server DNS. Per questo ambiente la maggior parte del traffico dal front-end e dal back-end è bloccato. Questa regola consente in modo specifico il traffico DNS da qualsiasi subnet locale.
  3. Regola da subnet a subnet: questa regola consente a qualsiasi server sulla subnet back-end di connettersi a qualsiasi server sulla subnet front-end (ma non viceversa).
* Regola alternativa (per il traffico che non soddisfa alcuna condizione precedente):
  1. Regola per negare tutto il traffico: deve essere sempre la regola finale (a livello di priorità). Se i flussi di traffico non corrispondono ad alcuna regola precedente, verranno eliminati da questa regola. Questa è una regola predefinita ed è solitamente attivata, quindi non sono in genere necessarie modifiche.

> [!TIP]
> Per semplificare, nella seconda regola del traffico delle applicazioni è consentita qualsiasi porta. In uno scenario reale, è consigliabile usare la porta e gli intervalli di indirizzi più specifici per ridurre la superficie di attacco di questa regola.
>
>

Dopo la creazione di tutte le regole precedentemente indicate, è importante esaminare la priorità di ogni regola, per assicurare che il traffico venga consentito o negato in base a quanto previsto. Per questo esempio le regole sono elencate in ordine di priorità.

#### <a name="conclusion"></a>Conclusioni
Rispetto agli esempi precedenti, questo è un metodo più complesso ma anche più completo per proteggere e isolare la rete (l'Esempio 2 protegge solo l'applicazione, mentre l'Esempio 1 isola esclusivamente le subnet). Questa struttura consente di monitorare il traffico in entrambe le direzioni e non protegge solo il server applicazioni in ingresso, ma applica anche i criteri di sicurezza di rete su tutti i server in questa rete. In base al dispositivo usato, è anche possibile ottenere il controllo e il riconoscimento di tutto il traffico. Per altre informazioni, vedere le [istruzioni di creazione dettagliate][Example3]. Le istruzioni includono:

* Come creare la rete perimetrale di esempio con script di PowerShell
* Come creare la rete perimetrale di esempio con un modello di Azure Resource Manager
* Descrizioni dettagliate di ogni route definita dall'utente, ogni comando del gruppo di sicurezza di rete e ogni regola del firewall
* Scenari dettagliati del flusso di traffico che illustrano il modo in cui il traffico viene consentito o negato su ogni livello

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network"></a>Esempio 4: Aggiungere una connessione ibrida con un'appliance virtuale da sito a sito e con una rete virtuale privata
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con rete ibrida connessa tramite appliance virtuale di rete][11]

#### <a name="environment-description"></a>Descrizione dell'ambiente
Le reti ibride che usano un'appliance virtuale di rete possono essere aggiunte a qualsiasi tipo di rete perimetrale illustrato negli esempi 1, 2 o 3.

Come illustrato nella figura precedente, una connessione VPN su Internet (da sito a sito) viene usata per connettere una rete locale a una rete virtuale di Azure tramite un'appliance virtuale di rete.

> [!NOTE]
> Se si usa ExpressRoute attivando l'opzione di peering pubblico di Azure, è necessario creare una route statica, che deve condurre all'indirizzo IP VPN dell'appliance virtuale di rete all'esterno dell'Internet aziendale, senza transitare tramite la rete WAN di ExpressRoute. Il protocollo NAT necessario per l'opzione di peering pubblico di Azure su ExpressRoute può interrompere la sessione VPN.
>
>

Dopo la configurazione della VPN, l'appliance virtuale di rete diventa l'hub centrale per tutte le reti e le subnet. Le regole di inoltro del firewall determinano i flussi di traffico consentiti, sottoposti a NAT, reindirizzati o eliminati (anche per i flussi di traffico tra la rete locale e Azure).

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di struttura, in base al caso d'uso specifico.

Se si usa l'ambiente creato nell'esempio 3, e quindi si aggiunge una connessione di rete ibrida VPN da sito a sito, si otterrà la struttura seguente:

![Rete perimetrale con appliance virtuale di rete connesso tramite VPN da sito a sito][12]

Il client VPN corrisponde al router locale o a eventuali altri dispositivi di rete compatibili con l'appliance virtuale di rete per la VPN. Questo dispositivo fisico sarà responsabile per l'inizializzazione e la gestione della connessione VPN con l'appliance virtuale di rete.

Per l'appliance virtuale di rete, a livello logico, la rete è analoga a quattro "aree di sicurezza" distinte, con le regole dell'appliance virtuale di rete usate come strumento di indirizzamento primario per il traffico tra queste aree:

![Rete logica dal punto di vista dell'appliance virtuale di rete][13]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete ibrida VPN da sito a sito a una rete virtuale di Azure può estendere la rete locale in Azure in modo sicuro. Se si usa una connessione VPN, il traffico verrà crittografato e instradato tramite Internet. L'appliance virtuale di rete in questo esempio fornisce una posizione centrale per l'applicazione e la gestione dei criteri di sicurezza. Per altre informazioni, vedere le istruzioni di creazione dettagliate (presto disponibili). Le istruzioni includono:

* Come creare la rete perimetrale di esempio con script di PowerShell
* Come creare la rete perimetrale di esempio con un modello di Azure Resource Manager
* Scenari dettagliati di flussi di traffico che illustrano il modo in cui il traffico scorre nella struttura.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>Esempio 5: Aggiungere una connessione ibrida con un gateway VPN di Azure da sito a sito
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con rete ibrida connessa tramite gateway][14]

#### <a name="environment-description"></a>Descrizione dell'ambiente
È possibile aggiungere reti ibride che usano un gateway VPN di Azure a qualsiasi tipo di rete perimetrale illustrato negli esempi 1 e 2.

Come illustrato nella figura precedente, una connessione VPN su Internet (da sito a sito) viene usata per connettere una rete locale a una rete virtuale di Azure tramite un gateway VPN di Azure.

> [!NOTE]
> Se si usa ExpressRoute attivando l'opzione di peering pubblico di Azure, è necessario creare una route statica, che deve condurre all'indirizzo IP VPN dell'appliance virtuale di rete all'esterno dell'Internet aziendale, senza transitare tramite la rete WAN di ExpressRoute. Il protocollo NAT necessario per l'opzione di peering pubblico di Azure su ExpressRoute può interrompere la sessione VPN.
>
>

La seguente figura mostra i due margini di rete di questa opzione. Sul primo margine, l'appliance virtuale di rete e i gruppi di sicurezza di rete controllano i flussi di traffico tra le reti interne ad Azure e tra Azure e Internet. Il secondo margine è il gateway VPN di Azure, ovvero un margine di rete completamente separato e isolato tra le reti locali e Azure.

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di struttura, in base al caso d'uso specifico.

Se si usa l'ambiente creato nell'esempio 1, e quindi si aggiunge una connessione di rete ibrida VPN da sito a sito, si otterrà la struttura seguente:

![Rete perimetrale con gateway connesso tramite una connessione ExpressRoute][15]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete ibrida VPN da sito a sito a una rete virtuale di Azure può estendere la rete locale in Azure in modo sicuro. Se si usa un gateway VPN nativo di Azure, il traffico viene crittografato con IPSec e instradato tramite Internet. L'uso del gateway VPN di Azure offre inoltre un'opzione a costi ridotti (a differenza delle appliance virtuali di rete di terze parti, che comportano costi di licenza aggiuntivi). Questa soluzione risulta estremamente conveniente nell'esempio 1, in cui non vengono usate appliance virtuali di rete. Per altre informazioni, vedere le istruzioni di creazione dettagliate (presto disponibili). Le istruzioni includono:

* Come creare la rete perimetrale di esempio con script di PowerShell
* Come creare la rete perimetrale di esempio con un modello di Azure Resource Manager
* Scenari dettagliati di flussi di traffico che illustrano il modo in cui il traffico scorre nella struttura.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Esempio 6: Aggiungere una connessione ibrida con ExpressRoute
[Torna all'avvio veloce](#fast-start) | Le istruzioni di creazione dettagliate saranno presto disponibili

![Rete perimetrale con rete ibrida connessa tramite gateway][16]

#### <a name="environment-description"></a>Descrizione dell'ambiente
È possibile aggiungere reti ibride che usano una connessione con peering privato di ExpressRoute a qualsiasi tipo di rete perimetrale illustrato negli esempi 1 o 2.

Come illustrato nella figura precedente, il peering privato di ExpressRoute fornisce una connessione diretta tra la rete locale e la rete virtuale di Azure. Il traffico attraversa solo la rete del provider del servizio e la rete Microsoft Azure, senza contatto con Internet.

> [!NOTE]
> A causa della complessità del routing dinamico usato nel gateway virtuale di Azure, sono previste limitazioni per l'uso delle route definite dall'utente (UDR) con ExpressRoute. Ecco quali sono:
>
> * Le route definite dall'utente non devono essere applicate alla subnet del gateway a cui è connessa l'istanza di ExpressRoute collegata al gateway virtuale di Azure.
> * L'istanza di ExpressRoute collegata al gateway virtuale di Azure non può essere il dispositivo NextHop usato per altre subnet associate alle route definite dall'utente.
>
> <br />
>
> [!TIP]
> L'uso di ExpressRoute mantiene il traffico di rete aziendale lontano da Internet per una protezione ottimizzata e prestazioni significativamente migliori, agevolando contratti di servizio dal provider ExpressRoute. Il gateway di Azure può raggiungere un massimo di 2 Gb/s con ExpressRoute, mentre la velocità massima effettiva con le VPN da sito a sito per il gateway di Azure è di 200 Mb/s.
>
>

Come illustrato nel diagramma che segue, con questa opzione l'ambiente include ora due margini di rete. L'appliance virtuale di rete e i gruppi di sicurezza di rete controllano i flussi di traffico per le reti interne di Azure e tra Azure e Internet, mentre il gateway è un margine di rete completamente separato e isolato tra le reti locali e Azure.

I flussi di traffico devono essere esaminati con attenzione, perché possono essere ottimizzati o danneggiati da questo modello di struttura, in base al caso d'uso specifico.

Se si usa l'ambiente creato nell'esempio 1, e quindi si aggiunge una connessione di rete ibrida di ExpressRoute, si otterrà la struttura seguente:

![Rete perimetrale con gateway connesso tramite una connessione ExpressRoute][17]

#### <a name="conclusion"></a>Conclusioni
L'aggiunta di una connessione di rete con peering privato di ExpressRoute può estendere la rete locale in Azure in un modo sicuro, con latenza ridotta e prestazioni migliori. L'uso del gateway nativo di Azure, come illustrato in questo esempio, offre un'opzione a costi ridotti (a differenza delle appliance virtuali di rete che comportano costi di licenza aggiuntivi). Per altre informazioni, vedere le istruzioni di creazione dettagliate (presto disponibili). Le istruzioni includono:

* Come creare la rete perimetrale di esempio con script di PowerShell
* Come creare la rete perimetrale di esempio con un modello di Azure Resource Manager
* Scenari dettagliati di flussi di traffico che illustrano il modo in cui il traffico scorre nella struttura.

## <a name="references"></a>Riferimenti
### <a name="helpful-websites-and-documentation"></a>Siti Web e documentazione utili
* Accedere ad Azure tramite Azure Resource Manager:
* Accesso ad Azure con PowerShell: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](/powershell/azureps-cmdlets-docs)
* Documentazione sulle reti virtuali: [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
* Documentazione sui gruppi di sicurezza di rete: [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](virtual-network/virtual-networks-nsg.md)
* Documentazione sul routing definito dall'utente: [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](virtual-network/virtual-networks-udr-overview.md)
* Gateway virtuali di Azure: [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
* VPN da sito a sito: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Documentazione su ExpressRoute (assicurarsi di consultare le sezioni relative all'introduzione e alle procedure): [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "Diagramma di flusso sulle opzioni di sicurezza"
[1]: ./media/best-practices-network-security/compliancebadges.png "Notifiche sulla conformità di Azure"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Funzionalità di sicurezza di Azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Rete perimetrale in una rete aziendale"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Architettura di sicurezza di Azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Rete perimetrale in una rete virtuale di Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "Rete ibrida con tre limiti di sicurezza"
[7]: ./media/best-practices-network-security/example1design.png "Rete perimetrale in ingresso con gruppo di sicurezza di rete"
[8]: ./media/best-practices-network-security/example2design.png "Rete perimetrale in ingresso con appliance virtuale di rete e gruppo di sicurezza di rete"
[9]: ./media/best-practices-network-security/example3design.png "Rete perimetrale bidirezionale con appliance virtuale di rete, gruppo di sicurezza di rete e routing definito dall'utente"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "Visualizzazione logica delle regole del firewall"
[11]: ./media/best-practices-network-security/example4designoptions.png "Rete perimetrale con appliance virtuale di rete connesso tramite rete ibrida"
[12]: ./media/best-practices-network-security/example4designs2s.png "Rete perimetrale con appliance virtuale di rete connesso tramite VPN da sito a sito"
[13]: ./media/best-practices-network-security/example4networklogical.png "Rete logica dal punto di vista dell'appliance virtuale di rete"
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



<!--HONumber=Dec16_HO2-->



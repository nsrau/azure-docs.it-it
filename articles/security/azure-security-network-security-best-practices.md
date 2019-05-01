---
title: Le procedure consigliate per la sicurezza di rete - Microsoft Azure
description: Questo articolo fornisce una serie di procedure consigliate per la sicurezza di rete usando le funzionalità integrate di Azure.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: TomSh
ms.openlocfilehash: 5bec7db1c4409165242416df16e437b121381b49
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872551"
---
# <a name="azure-network-security-best-practices"></a>Procedure consigliate per la sicurezza della rete di Azure
Questo articolo illustra un insieme di procedure consigliate per la sicurezza della rete di Azure, derivate dalla nostra esperienza con la rete di Azure e dalle esperienze di altri clienti.

Per ogni procedura consigliata questo articolo spiega:

* Qual è la procedura consigliata
* Il motivo per cui si vuole abilitare tale procedura consigliata
* Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
* Alternative possibili alla procedura consigliata
* Come imparare ad abilitare la procedura consigliata

Il presente articolo sulle procedure consigliate per la sicurezza della rete di Azure si basa su un parere condiviso, nonché sulle capacità e sui set di funzionalità della piattaforma di Azure esistenti al momento della scrittura. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

## <a name="logically-segment-subnets"></a>Segmentare logicamente le subnet
Le reti virtuali di Azure sono simili a una rete LAN nella rete locale. Una rete virtuale di Azure prevede di creare una singola rete basata sullo spazio indirizzi IP privato in cui è possibile inserire tutte le macchine virtuali di Azure. Gli spazi indirizzi IP privati disponibili sono negli intervalli di Classe A (10.0.0.0/8), Classe B (172.16.0.0/12) e Classe C (192.168.0.0/16).

Le procedure consigliate per suddividere logicamente le subnet includono:

**Procedura consigliata**: segmentare lo spazio di indirizzi più ampio in subnet.   
**Dettagli**: usare i principi di suddivisione in subnet basati su [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) per creare le subnet.

**Procedura consigliata**: creare controlli di accesso di rete tra subnet. Il routing tra le subnet viene eseguito automaticamente e non è necessario configurare manualmente le tabelle di routing. Per impostazione predefinita, non sono presenti controlli di accesso di rete tra le subnet create nella rete virtuale di Azure.   
**Dettagli**: usare un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) (NSG). Gli NSG sono semplici dispositivi di ispezione dei pacchetti con stato che usano l'approccio a 5 tuple (indirizzo IP di origine, porta di origine, IP di destinazione, porta di destinazione e protocollo di livello 4) per creare regole allow/deny per il traffico di rete. È possibile consentire o negare il traffico da e verso un singolo indirizzo IP, da e verso più indirizzi IP o da e verso intere subnet.

L'uso di NSG per il controllo di accesso di rete tra subnet consente di inserire nelle proprie subnet le risorse che appartengono alla stessa area di protezione o allo stesso ruolo.

## <a name="control-routing-behavior"></a>Controllare il comportamento di routing
Quando si inserisce una macchina virtuale in una rete virtuale di Azure, la macchina virtuale può connettersi a qualsiasi altra macchina virtuale nella stessa rete virtuale e ad altre macchine virtuali in subnet diverse. Questo è possibile perché un insieme di route di sistema, abilitate per impostazione predefinita, consente questo tipo di comunicazione. Queste route predefinite consentono alle macchine virtuali nella stessa rete virtuale di avviare le connessioni tra loro e con Internet (per le comunicazioni in uscita solo con Internet).

Nonostante le route di sistema predefinite siano utili per molti scenari di distribuzione, in alcuni casi si vuole personalizzare la configurazione del routing per le distribuzioni. È consentito configurare l'indirizzo hop successivo in modo da raggiungere destinazioni specifiche.

È consigliabile configurare [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) quando si distribuisce un dispositivo di sicurezza per una rete virtuale. Questo aspetto viene affrontato in una sezione successiva intitolata [Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali](azure-security-network-security-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Le route definite dall'utente non sono necessarie e le route di sistema predefinite generalmente funzionano.
>
>

## <a name="enable-forced-tunneling"></a>Abilitare il tunneling forzato
Per comprendere meglio il tunneling forzato, è utile comprendere cosa si intende per "split tunneling". L'esempio più comune di split tunneling è visibile nelle connessioni VPN (rete privata virtuale). Si supponga di stabilire una connessione VPN da una camera d'albergo alla propria rete aziendale. Questa connessione consente di accedere alle risorse aziendali. Tutte le comunicazioni con la rete aziendale transitano dal tunnel VPN.

Cosa accade quando ci si vuole connettere a risorse in Internet? Quando è abilitato lo split tunneling, tali connessioni passano a Internet direttamente senza attraversare il tunnel VPN. Alcuni esperti di sicurezza considerano questa opzione potenzialmente rischiosa. Consigliano di disabilitare lo split tunneling per fare in modo che tutte le connessioni, sia quelle destinate a Internet che quelle destinate alle risorse aziendali, passino attraverso il tunnel VPN. Il vantaggio che rappresenta la disabilitazione dello split tunneling è che le connessioni verso Internet vengono obbligate a transitare sui dispositivi di sicurezza della rete aziendale. Questo non accadrebbe se il client VPN fosse connesso a Internet esternamente dal tunnel VPN.

Torniamo ora alle macchine virtuali in una rete virtuale di Azure. Le route predefinite per una rete virtuale di Azure consentono alle macchine virtuali di inviare traffico a Internet. Anche questo può rappresentare un rischio per la sicurezza, perché le connessioni in uscita potrebbero aumentare la superficie di attacco di una macchina virtuale ed essere sfruttate da utenti malintenzionati. Per questo motivo, è consigliabile abilitare il [tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) sulle macchine virtuali quando è disponibile una connettività cross-premise tra la rete virtuale di Azure e la rete locale. Si discuterà della connettività cross-premise più avanti nelle procedure consigliate per le reti.

Se non si dispone di una connessione cross-premise, assicurarsi che sia possibile sfruttare i gruppi di sicurezza di rete, descritti in precedenza, o i dispositivi di sicurezza di rete virtuale di Azure, illustrati di seguito, per bloccare le connessioni in uscita a Internet dalle macchine virtuali di Azure.

## <a name="use-virtual-network-appliances"></a>Usare i dispositivi di rete virtuale
Gli NSG e il routing definito dall'utente possono garantire un certo grado di sicurezza della rete a livello di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model). Ma in alcune situazioni, è preferibile o necessario abilitare la sicurezza ai livelli alti dello stack. In tali situazioni, è consigliabile distribuire i dispositivi di sicurezza di rete virtuale forniti dai partner di Azure.

I dispositivi di sicurezza di rete di Azure possono offrire livelli di sicurezza migliori rispetto a quanto offerto dai controlli a livello di rete. Le funzionalità di sicurezza di rete fornite da dispositivi di sicurezza di rete virtuale includono:


* Funzionalità di firewall
* Rilevamento intrusioni/Prevenzione intrusioni
* Gestione vulnerabilità
* Controllo applicazione
* Rilevamento anomalie basato su rete
* Filtro Web
* Antivirus
* Protezione botnet

Per individuare i dispositivi di sicurezza di rete virtuale di Azure, visitare [Azure Marketplace](https://azure.microsoft.com/marketplace/) e cercare "sicurezza" e "sicurezza di rete".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Distribuire reti perimetrali per le aree di sicurezza
Una [rete perimetrale](https://docs.microsoft.com/azure/best-practices-network-security) è un segmento di rete fisica o logica che fornisce un ulteriore livello di sicurezza tra le risorse e Internet. I dispositivi specializzati per il controllo di accesso alla rete dislocati al margine di una rete perimetrale lasciano entrare nella rete virtuale solo il traffico desiderato.

Le reti perimetrali sono utili perché permettono di concentrare le operazioni di gestione, monitoraggio, registrazione e creazione di report del controllo di accesso alla rete sui dispositivi della rete virtuale di Azure. In questo caso, in genere si abilitano la prevenzione DDoS (Distributed Denial of Service), i sistemi di rilevamento intrusione/prevenzione intrusioni (IDS/IPS), le regole e i criteri dei firewall, il filtro Web, il software antimalware per la rete e molto altro. I dispositivi di sicurezza di rete sono posizionati tra Internet e la rete virtuale di Azure e hanno un'interfaccia su entrambe le reti.

Nonostante questa sia un modello di rete perimetrale base, esistono molti diversi modelli di rete perimetrale, come ad esempio back to back, tri-homed, e multihomed.

Per tutte le distribuzioni di sicurezza elevata è consigliabile prendere in considerazione l'uso di una rete perimetrale per migliorare il livello di sicurezza di rete per le risorse di Azure.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Evitare l'esposizione a Internet con collegamenti WAN dedicati
Molte organizzazioni hanno scelto la strada dell'IT ibrido. Nell'ambiente IT ibrido, alcune delle informazioni sulla società si trovano in Azure, mentre altre rimangono in locale. In molti casi alcuni componenti di un servizio sono eseguiti in Azure mentre altri componenti restano in locale.

In uno scenario IT ibrido generalmente è presente un certo tipo di connettività cross-premise, che consente alla società di connettere le proprie reti locali alle reti virtuali di Azure. Sono disponibili due soluzioni di connettività cross-premise:

* **VPN da sito a sito**: è una tecnologia attendibile, affidabile e consolidata, ma la connessione avviene tramite Internet. La larghezza di banda è vincolata a un massimo di circa 200 Mbps. VPN da sito a sito è una soluzione ottima in alcuni scenari e viene illustrata in dettaglio nella sezione [Disabilitare l'accesso RDP/SSH alle macchine virtuali](#disable-rdpssh-access-to-virtual-machines).
* **Azure ExpressRoute**: per la connettività cross-premise, [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) è la scelta consigliata. ExpressRoute è un collegamento WAN dedicato tra il percorso locale o un provider di hosting di Exchange. Dal momento che si tratta di una connessione di telecomunicazioni, i dati non vengono trasmessi via Internet e quindi non sono esposti a potenziali rischi legati alle comunicazioni Internet.

## <a name="optimize-uptime-and-performance"></a>Ottimizzare il tempo di attività e le prestazioni
Se un servizio non è attivo, non è possibile accedere alle informazioni. Se le prestazioni sono talmente insufficienti da rendere i dati inutilizzabili, è possibile considerare che i dati siano inaccessibili. Dal punto di vista della sicurezza, è necessario fare del proprio meglio per assicurarsi che i servizi offrano sempre prestazioni e tempi di attività ottimali.

Uno dei metodi più diffusi ed efficaci per migliorare le prestazioni e la disponibilità consiste nel ricorrere al bilanciamento del carico. Il bilanciamento del carico è un metodo di distribuzione del traffico di rete tra server che fanno parte di un servizio. Ad esempio, se dei server Web front-end fanno parte del servizio, è possibile usare il bilanciamento del carico per distribuire il traffico tra più server Web front-end.

Questa distribuzione del traffico aumenta la disponibilità perché se uno dei server Web non è più disponibile, il servizio di bilanciamento del carico interrompe l'invio di traffico a tale server, reindirizzandolo verso i server che sono ancora online. Il bilanciamento del carico favorisce anche le prestazioni, perché il sovraccarico di processore, rete e memoria per l'elaborazione delle richieste viene distribuito tra tutti server con carico bilanciato.

È consigliabile impiegare il bilanciamento del carico ogni volta possibile e quando adeguato ai servizi. Di seguito sono descritti degli scenari a livello di rete virtuale di Azure e altri a livello globale, con le opzioni di bilanciamento del carico per ognuno.

**Scenario**: è presente un'applicazione che:

- Necessita che le richieste provengano dalla stessa sessione utente/client per raggiungere la stessa macchina virtuale back-end. Esempi di queste applicazioni sono i carrelli dei siti di acquisti e i server di posta Web.
- Accetta solo connessioni protette, pertanto le comunicazioni non crittografate verso il server non rappresentano un'opzione accettabile.
- Necessita che più richieste HTTP nella stessa connessione TCP con esecuzione prolungata vengano instradate/bilanciate in server back-end diversi.

**Opzione di bilanciamento del carico**: usare il [gateway applicazione di Azure](../application-gateway/application-gateway-introduction.md), un servizio di bilanciamento del carico correlato al traffico Web HTTP. Il gateway applicazione supporta la crittografia SSL e la [terminazione SSL](../application-gateway/application-gateway-introduction.md) sul gateway. I server Web possono quindi essere liberati dal sovraccarico prodotto dai processi di crittografia e decrittografia e il traffico può essere trasmesso in formato non crittografato ai server back-end.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni in ingresso da Internet tra i server che si trovano in una rete virtuale di Azure. Scenari in cui:

- Sono presenti applicazioni senza stato che accettano le richieste in ingresso da Internet.
- Non sono richieste sessioni permanenti o l'offload SSL. La combinazione di sessioni permanenti e del bilanciamento del carico delle applicazioni è un metodo per ottenere l'affinità dei server.

**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico esterno](../load-balancer/quickstart-create-basic-load-balancer-portal.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario applicare il bilanciamento del carico alle connessioni provenienti da macchine virtuali che non si trovano su Internet. Nella maggior parte dei casi, le connessioni che sono accettate per il bilanciamento del carico possono essere avviate da dispositivi nella rete virtuale di Azure, quali le istanze SQL Server o i server Web interni.   
**Opzione di bilanciamento del carico**: usare il portale di Azure per [creare un servizio di bilanciamento del carico interno](../load-balancer/quickstart-create-basic-load-balancer-powershell.md) che distribuisca le richieste in ingresso tra più macchine virtuali per offrire un livello superiore di disponibilità.

**Scenario**: è necessario il bilanciamento del carico globale perché:

- Si dispone di una soluzione cloud ampiamente distribuita tra più aree e che richiede il livello massimo di tempo di attività (disponibilità) possibile.
- Si necessita del massimo livello di tempo di attività possibile per assicurarsi che il servizio sia disponibile anche se un intero data center diventa non disponibile.

**Opzione di bilanciamento del carico**: usare Gestione traffico di Azure. Gestione traffico rende possibile bilanciare il carico delle connessioni ai servizi in base alla posizione dell'utente.

Ad esempio, se l'utente effettua una richiesta al servizio dall'Unione europea, la connessione viene indirizzata ai servizi che si trovano in un data center dell'Unione europea. Questa parte bilanciamento del carico globale di Gestione traffico consente di migliorare le prestazioni perché la connessione al data center più vicino è più veloce rispetto alla connessione ai data center più lontani.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Disabilitare l'accesso RDP/SSH alle macchine virtuali
È possibile raggiungere le macchine virtuali di Azure usando i protocolli [Remote Desktop Protocol](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) e [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Questi protocolli consentono di gestire le macchine virtuali da postazioni remote e sono standard nel computing dei data center.

L'uso di questi protocolli in Internet può provocare tuttavia un potenziale problema di sicurezza perché gli utenti malintenzionati possono usare tecniche di [attacco di forza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) per ottenere l'accesso alle macchine virtuali di Azure. Una volta che gli utenti malintenzionati avranno ottenuto l'accesso, potranno usare la macchina virtuale come punto di partenza per compromettere gli altri computer nella rete virtuale o persino per attaccare i dispositivi di rete all'esterno di Azure.

È consigliabile disabilitare l'accesso diretto RDP e SSH alle macchine virtuali di Azure da Internet. Dopo aver disabilitato l'accesso diretto RDP e SSH da Internet, sono disponibili altre opzioni per accedere a queste macchine virtuali per la gestione remota.

**Scenario**: consentire a un singolo utente di connettersi a una rete virtuale di Azure via Internet.   
**Opzione**: [VPN da punto a sito](../vpn-gateway/vpn-gateway-point-to-site-create.md) è un altro termine per indicare una connessione client/server VPN con accesso remoto. Dopo aver stabilito la connessione da punto a sito, l'utente può usare RDP o SSH per connettersi a qualsiasi macchina virtuale presente nella rete virtuale Azure e a cui l'utente si è connesso con VPN da punto a sito. Ciò presuppone che l'utente sia autorizzato a raggiungere tali macchine virtuali.

La VPN da punto a sito è più sicura delle connessioni dirette RDP o SSH perché l'utente deve autenticarsi due volte prima di potersi connettere a una macchina virtuale. In primo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione VPN da punto a sito. In secondo luogo, l'utente deve eseguire l'autenticazione, e ottenere l'autorizzazione, per stabilire la connessione RDP o SSH.

**Scenario**: consentire agli utenti della rete locale di connettersi alle macchine virtuali nella rete virtuale di Azure.   
**Opzione**: una [VPN da sito a sito](../vpn-gateway/vpn-gateway-site-to-site-create.md) connette un'intera rete a un'altra rete via Internet. È possibile usare una VPN da sito a sito per connettere la rete locale a una rete virtuale di Azure. Gli utenti della rete locale si connettono usando il protocollo RDP o SSH tramite la connessione VPN da sito a sito. Non è necessario consentire l'accesso diretto RDP o SSH via Internet.

**Scenario**: usare un collegamento WAN dedicato per fornire funzionalità simili alla VPN da sito a sito.   
**Opzione**: usare [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Offre funzionalità simili a quelle della VPN da sito a sito. Le differenze principali sono le seguenti:

- Il collegamento WAN dedicato non attraversa Internet.
- I collegamenti WAN dedicati sono in genere più stabili e più efficienti.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali
Usare gli endpoint servizio di rete virtuale per estendere lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.

Gli endpoint di servizio offrono i vantaggi seguenti:

- **Maggiore sicurezza per le risorse dei servizi di Azure**. Con gli endpoint di servizio è possibile associare le risorse dei servizi di Azure alla rete virtuale. In questo modo si ottiene una maggiore sicurezza perché si rimuove completamente l'accesso Internet pubblico alle risorse, consentendo solo il traffico dalla rete virtuale.
- **Routing ottimale per il traffico dei servizi di Azure dalla rete virtuale**. Tutte le route nella rete virtuale che forzano il traffico Internet verso appliance locali e/o virtuali (tunneling forzato) forzano anche il traffico dei servizi di Azure affinché usi la stessa route del traffico Internet. Gli endpoint di servizio forniscono il routing ottimale per il traffico di Azure.

  Gli endpoint instradano sempre il traffico del servizio direttamente dalla rete virtuale al servizio nella rete backbone di Azure. Mantenendo il traffico nella rete backbone di Azure è possibile continuare a monitorare e verificare il traffico Internet in uscita dalle reti virtuali, tramite il tunneling forzato, senza conseguenze per il traffico del servizio. Vedere altre informazioni sulle [route definite dall'utente e il tunneling forzato](../virtual-network/virtual-networks-udr-overview.md).

- **Semplicità di configurazione con sovraccarico di gestione inferiore**. Non sono più necessari indirizzi IP pubblici riservati nelle reti virtuali per proteggere le risorse di Azure tramite un firewall IP. Non sono necessari dispositivi NAT o gateway per configurare gli endpoint di servizio. Un endpoint di servizio può essere configurato con un semplice clic in una subnet. Non c'è alcun sovraccarico aggiuntivo per la gestione degli endpoint.

Per altre informazioni sugli endpoint di servizio, sui servizi di Azure e le aree per cui gli endpoint sono disponibili, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="next-step"></a>Passaggio successivo
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).

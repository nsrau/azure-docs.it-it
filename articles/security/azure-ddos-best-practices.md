---
title: Procedure consigliate per Protezione DDoS di Azure e architetture di riferimento | Microsoft Docs
description: Informazioni sull'uso dei dati di registrazione per ottenere informazioni approfondite sull'applicazione.
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: 29aea0171c767b2188e3eac23b1d61ffbda8e284
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986851"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Procedure consigliate per Protezione DDoS di Azure e architetture di riferimento

Questo articolo è destinato ai responsabili IT e al personale di sicurezza. Presuppone che si abbia familiarità con Azure, la rete e la sicurezza.

La progettazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiede pianificazione e progettazione per un'ampia gamma di modalità di errore. Questo articolo illustra le procedure consigliate per la progettazione di applicazioni in Azure per garantire la resilienza contro gli attacchi DDoS.

## <a name="types-of-attacks"></a>Tipi di attacchi

Il DDoS è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati e di dimensioni e impatto maggiori. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

Azure offre una protezione continua contro gli attacchi DDoS. Questa protezione è integrata nella piattaforma di Azure per impostazione predefinita e senza costi aggiuntivi. 

Oltre alla protezione DDoS di base della piattaforma, [Protezione DDoS di Azure Standard](https://azure.microsoft.com/services/ddos-protection/) offre funzionalità avanzate di mitigazione DDoS contro gli attacchi di rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di nuove reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

![Ruolo di Protezione DDoS di Azure nella protezione dei clienti e di una rete virtuale da un utente malintenzionato.](media/azure-ddos-best-practices/image1.png)

Gli attacchi DDoS possono essere classificati in tre categorie: volumetrici, di protocollo e di risorse.

### <a name="volumetric-attacks"></a>Attacchi volumetrici

Gli attacchi volumetrici sono i tipi più comuni di attacco DDoS. Gli attacchi volumetrici assalti basati sulla forza bruta destinati ai livelli di trasporto e di rete. Cercano di esaurire risorse come i collegamenti di rete. 

Questi attacchi usano spesso più sistemi infetti per inondare i livelli di rete con traffico apparentemente legittimo. Usano protocolli a livello di rete come Internet Control Message Protocol (ICMP), User Datagram Protocol (UDP) e Transmission Control Protocol (TCP).

Gli attacchi DDoS più comunemente usati a livello di rete sono TCP SYN flood, ICMP echo, UDP flooding, DNS, e NTP amplification. Questo tipo di attacco può essere utilizzato non solo per interrompere il servizio, ma anche come copertura per intrusioni di rete più nefaste e indirizzate. Un esempio di attacco volumetrico recente è l'[exploit Memcache](https://www.wired.com/story/github-ddos-memcached/) che ha colpito GitHub. Questo attacco ha avuto come destinatario la porta UDP 11211 e ha generato 1,35 Tb/s del volume di attacco.

### <a name="protocol-attacks"></a>Attacchi di protocollo

Protocolli dell'applicazione destinataria degli attacchi di protocollo. Provano a usare tutte le risorse disponibili nei dispositivi dell'infrastruttura, ad esempio i firewall, i server applicazioni e i servizi di bilanciamento del carico. Gli attacchi di protocollo usano pacchetti che sono in formato non corretto o che contengono anomalie di protocollo. Questi attacchi operano inviando numeri elevati di richieste aperte, i server e altri dispositivi di comunicazione rispondono e attendono una risposta del pacchetto. La destinazione prova a rispondere alle richieste provocando infine l'arresto anomalo del sistema.

L'esempio più comune di attacco DDoS basato sul protocollo è il TCP SYN Flood. In questo tipo di attacco, una successione di richieste TCP SYN tenta di sovraccaricare una destinazione. L'obiettivo è di non avere alcuna risposta dalla destinazione. L'interruzione Dyn 2016, oltre ad essere un attacco a livello di applicazione, è consistita in inondazioni TCP SYN che colpivano la porta 53 dei server DNS Dyn.

### <a name="resource-attacks"></a>Attacchi alle risorse

Gli attacchi alle risorse hanno come destinazione il livello dell'applicazione. Attivano processi di back-end nel tentativo di sovraccaricare un sistema. Gli attacchi alle risorse abusano del traffico che sembra normale, ma che porta query ad utilizzo elevato di CPU al server. Il volume di traffico necessario ad esaurire le risorse è inferiore a quello di altri tipi di attacchi. Il traffico in un attacco alle risorse non è distinguibile dal traffico legittimo, rendendolo difficile da rilevare. Gli attacchi alle risorse più comuni sono su HTTP/HTTPS e sui servizi DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilità condivisa nel cloud

È necessaria una strategia di difesa approfondita per contrastare le crescenti tipologie e i diversi gradi di sofisticazione degli attacchi. La sicurezza è una responsabilità condivisa tra il cliente e Microsoft. Microsoft la definisce un [modello di responsabilità condivisa](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). La figura seguente mostra la divisione di responsabilità:

![Responsabilità del cliente e di Azure](media/azure-ddos-best-practices/image2.png)

I clienti di Azure traggono vantaggio dall'analisi delle procedure consigliate di Microsoft e dalla creazione di applicazioni distribuite a livello globale progettate e testate per errori.

## <a name="fundamental-best-practices"></a>Procedure consigliate fondamentali

Le sezioni seguenti forniscono indicazioni specifiche per la compilazione di servizi resilienti contro gli attacchi DDoS in Azure.

### <a name="design-for-security"></a>Progettare per la sicurezza

Garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità troppo elevata di risorse, provocando un'interruzione del servizio. 

Per proteggere un servizio in esecuzione in Microsoft Azure, i clienti devono avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars).
I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

### <a name="design-for-scalability"></a>Progettazione per la scalabilità

La scalabilità è la capacità di un sistema di gestire carichi elevati. È necessario progettare le applicazioni con [scalabilità orizzontale](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.

Per [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) selezionare un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) che offra più istanze. Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../cloud-services/cloud-services-choose-me.md). Per [Macchine virtuali di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) verificare che l'architettura VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md). Si consiglia di usare [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per le funzionalità di scalabilità automatica.

### <a name="defense-in-depth"></a>Informazioni dettagliate sulla difesa

L'idea che sta dietro alla difesa in profondità consiste nel gestire i rischi con diverse strategie difensive. La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. È consigliabile implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.

Ad esempio, il rischio di attacco aumenta con le dimensioni o la *superficie di attacco* dell'applicazione. È possibile ridurre la superficie usando l'elenco degli elementi consentiti per chiudere lo spazio indirizzi IP esposto e le porte in ascolto non necessarie sui servizi di bilanciamento del carico ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [gateway applicazione di Azure](../application-gateway/application-gateway-create-probe-portal.md)). I [gruppi di sicurezza di rete (NSG)](../virtual-network/security-overview.md) rappresentano un altro modo per ridurre la superficie di attacco.
È possibile usare [tag di servizio](../virtual-network/security-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../virtual-network/security-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita. Tramite gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md), il traffico del servizio passerà all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, è consigliabile ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica. È possibile usare la scalabilità e le funzionalità di protezione DDoS avanzate di Azure distribuendo le entità pubbliche note in Azure. Poiché queste entità pubblicamente accessibili sono spesso una destinazione per gli attacchi DDoS, mettendole in Azure si riduce l'impatto sulle risorse locali.

## <a name="azure-offerings-for-ddos-protection"></a>Offerte di Azure per la protezione DDoS

In Azure sono disponibili due offerte di servizio DDoS che forniscono protezione dagli attacchi alla rete (Livello 3 e 4): Protezione DDoS Basic e Protezione DDoS Standard. 

### <a name="ddos-protection-basic"></a>Protezione DDoS Basic

La protezione di base viene integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. Protezione DDoS Basic non richiede alcuna modifica alla configurazione o all'applicazione da parte dell'utente. Protezione DDoS Basic consente di proteggere tutti i servizi di Azure, compresi i servizi PaaS come DNS di Azure.

![Mappa della rete Azure, con i testi "Global DDoS mitigation presence" (Presenza di mitigazione DDoS globale) e "Leading DDoS mitigation capacity" (Capacità di mitigazione DDoS principale).](media/azure-ddos-best-practices/image3.png)

Protezione DDoS Basic di Azure è costituita da componenti hardware e software. Un piano di controllo del software decide quando, dove, e che tipo di traffico debba essere deviato tramite dispositivi hardware che analizzano e rimuovono il traffico dell'attacco. Un piano di controllo software prende questa decisione in base a *criteri* di Protezione DDoS per tutta l'infrastruttura. Questi criteri sono staticamente definiti e universalmente applicati a tutti i clienti di Azure.

Ad esempio, i criteri di Protezione DDoS specificano a quale volume di traffico debba essere *attivata* la protezione, ovvero quando instradare il traffico del tenant attraverso appliance di esecuzione dello scrubbing. I criteri specificano quindi in che modo le appliance di esecuzione dello scrubbing debbano *mitigare* l'attacco.

Il servizio Protezione DDoS di Azure Basic è destinato alla protezione dell'infrastruttura e della piattaforma di Azure. Mitiga il traffico quando supera una velocità talmente significativa da poter influire negativamente su più clienti in un ambiente multi-tenant. Non fornisce avvisi o i criteri personalizzati per ogni cliente.

### <a name="ddos-protection-standard"></a>Protezione DDoS Standard

La protezione Standard offre funzioni di mitigazione DDoS avanzate. Viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica di applicazioni o risorse. Presenta diversi vantaggi rispetto al servizio Basic, compresa la registrazione, gli avvisi e i dati di telemetria. Le sezioni seguenti descrivono le caratteristiche principali del servizio Protezione DDoS di Azure Standard.

#### <a name="adaptive-real-time-tuning"></a>Ottimizzazione adattiva in tempo reale

Il servizio Protezione DDoS di Azure Basic aiuta a proteggere i clienti e a prevenire gli impatti su altri clienti. Se ad esempio viene eseguito il provisioning di un servizio per un volume tipico di traffico legittimo in entrata minore della *frequenza trigger* dei criteri di Protezione DDoS per l'intera infrastruttura, potrebbe passare inosservato un attacco DDoS alle risorse di quel cliente. Più in generale, la natura complessa degli attacchi recenti (ad esempio DDoS multi-vettore), nonché i comportamenti specifici dell'applicazione dei tenant, richiedono criteri di protezione personalizzati per ogni cliente. Il servizio realizza questa personalizzazione in due modi:

- Apprendimento automatico dei modelli di traffico per ogni cliente (per ogni IP) per i livelli 3 e 4.

- Riduzione al minimo dei falsi positivi, considerando che la scalabilità di Azure consente di assorbire una quantità significativa di traffico.

![Diagramma del funzionamento di Protezione DDoS Standard, con "Policy Generation" (Generazione di criteri) cerchiato](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>Telemetria, monitoraggio e avvisi di Protezione DDoS

Protezione DDoS Standard espone dati di telemetria avanzata tramite [Monitoraggio di Azure](../azure-monitor/overview.md) durante un attacco DDoS. È possibile configurare gli avvisi per qualsiasi metrica di Monitoraggio di Azure usata da Protezione DDoS. La registrazione può essere integrata con Splunk (Hub eventi di Azure), Log Analytics di Azure e Archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di Monitoraggio di Azure.

##### <a name="ddos-mitigation-policies"></a>Criteri di mitigazione della protezione DDoS

Nel portale di Azure selezionare **Monitoraggio** > **Metriche**. Nel riquadro **Metriche** selezionare il gruppo di risorse, il tipo di risorsa **Indirizzo IP pubblico** e l'indirizzo IP pubblico di Azure. Le metriche DDoS sono visibili nel riquadro **Metriche disponibili**.

Protezione DDoS Standard applica tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP) per ogni indirizzo IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando la metrica **Inbound packets to trigger DDoS mitigation** (Pacchetti in ingresso per attivare la mitigazione dei rischi DDoS).

![Metriche disponibili e grafico delle metriche](media/azure-ddos-best-practices/image7.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basata su Machine Learning. La mitigazione dei rischi DDoS ha luogo per un indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>Metrica per un indirizzo IP sotto attacco DDoS

Se l'indirizzo IP pubblico è sotto attacco, il valore della metrica **Under DDoS attack or not** (Sotto attacco DDoS o no) passa a 1 quando Protezione DDoS esegue la mitigazione sul traffico di attacco.

![Metrica e grafico "Under DDoS attack or not" (Sotto attacco DDoS o no)](media/azure-ddos-best-practices/image8.png)

È consigliabile configurare un avviso per questa metrica. Si riceverà quindi una notifica in caso di mitigazione DDoS attiva sull'indirizzo IP pubblico.

Per altre informazioni, vedere [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Web application firewall per attacchi alle risorse

Per gli attacchi specifici alle risorse a livello di applicazione, è necessario configurare un Web Application Firewall (WAF) per contribuire a proteggere le applicazioni Web. Un Web application firewall analizza il traffico Web in ingresso per bloccare attacchi SQL injection, cross-site scripting, DDoS e altri attacchi di livello 7. Azure fornisce un [Web application firewall (WAF) del gateway applicazione](../application-gateway/application-gateway-web-application-firewall-overview.md) per la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. In [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) i partner di Azure mettono a disposizione altri tipi di Web application firewall che possono essere più adatti alle proprie esigenze.

Anche i Web application firewall sono vulnerabili agli attacchi volumetrici e a esaurimento di stato. È consigliabile attivare Protezione DDoS Standard nella rete virtuale WAF per proteggere dagli attacchi volumetrici e di protocollo. Per altre informazioni, vedere la sezione [Architetture di riferimento di Protezione DDoS](#ddos-protection-reference-architectures).

### <a name="protection-planning"></a>Pianificazione della protezione

La pianificazione e la preparazione sono essenziali per comprendere come un sistema si comporterà durante un attacco DDoS. L'elaborazione di un piano di intervento per la gestione di eventi imprevisti rientra in questa attività.

Se è disponibile Protezione DDoS Standard, assicurarsi che sia attivato nella rete virtuale degli endpoint con connessione Internet. La configurazione degli avvisi DDoS consente di monitorare costantemente eventuali attacchi all'infrastruttura. 

È necessario monitorare le applicazioni in modo indipendente e comprendere il comportamento normale di un'applicazione. Prepararsi ad agire se l'applicazione non si comporta come previsto durante un attacco DDoS.

#### <a name="testing-through-simulations"></a>Test tramite simulazioni

È consigliabile testare le ipotesi su come i servizi risponderanno a un attacco effettuando simulazioni periodiche. Durante il test, verificare che i servizi o le applicazioni continuino a funzionare come previsto e che non ci siano interruzioni nell'esperienza dell'utente finale. Identificare le lacune sia dal punto di vista tecnologico che da quello dei processi e integrarle nella strategia di risposta DDoS. È consigliabile eseguire tali test in ambienti di staging o in ore non di punta per ridurre al minimo l'impatto sull'ambiente di produzione.

Microsoft ha collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per creare un'interfaccia in cui i clienti di Azure possano generare traffico sugli endpoint pubblici con Protezione DDoS abilitata per le simulazioni. È possibile usare la simulazione [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per eseguire queste operazioni:

- Convalidare il modo in cui Protezione DDoS di Azure contribuisce a proteggere le risorse di Azure dagli attacchi DDoS.

- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS.

- Documentare la conformità DDoS.

- Eseguire il training dei team di sicurezza di rete.

La sicurezza informatica richiede una costante innovazione nella difesa. Protezione DDoS Standard di Azure è un'offerta all'avanguardia con una soluzione efficace per mitigare gli attacchi DDoS sempre più complessi.

## <a name="components-of-a-ddos-response-strategy"></a>Componenti di una strategia di risposta DDoS

Un attacco DDoS che prende di mira le risorse di Azure richiede solitamente un intervento minimo dal punto di vista dell'utente. L'integrazione della mitigazione DDoS nella strategia di risposta agli eventi imprevisti contribuisce comunque a ridurre al minimo l'impatto sulla continuità aziendale.

### <a name="microsoft-threat-intelligence"></a>Intelligence per le minacce di Microsoft

Microsoft ha a disposizione un'ampia rete di intelligence per le minacce. Questa rete usa le conoscenze collettive di una comunità di sicurezza estesa che supporta Microsoft Online Services, i partner Microsoft e le relazioni all'interno della comunità della sicurezza Internet. 

In qualità di fornitore di infrastrutture critiche, Microsoft riceve avvisi tempestivi sulle minacce e raccoglie informazioni sulle minacce dai propri servizi online e dalla base clienti globale. Microsoft incorpora tutte queste informazioni sulle minacce nei prodotti Protezione DDoS di Azure.

La Microsoft Digital Crimes Unit (DCU) mette inoltre in atto strategie offensive contro le botnet, che sono una fonte comune di comando e controllo per gli attacchi DDoS.

### <a name="risk-evaluation-of-your-azure-resources"></a>Valutazione del rischio delle risorse di Azure.

È imperativo comprendere la portata del rischio di un attacco DDoS su base continuativa. Porsi periodicamente le domande seguenti: 
- Quali nuove risorse di Azure pubblicamente disponibili hanno bisogno di protezione?

- È presente un singolo punto di guasto nel servizio? 

- Come possono essere isolati i servizi per limitare l'impatto di un attacco mantenendo comunque i servizi disponibili per i clienti validi?

- Esistono reti virtuali in cui Protezione DDoS Standard dovrebbe essere abilitata, ma non lo è? 

- I miei servizi sono attivi/non attivi con il failover in più regioni?

### <a name="customer-ddos-response-team"></a>Team di risposta del cliente DDoS

La creazione di un team di risposta DDoS è un passo fondamentale per rispondere a un attacco in modo rapido ed efficace. Identificare i contatti nell'organizzazione che sovrintenderanno sia alla pianificazione che all'esecuzione. Questo team di risposta DDoS deve comprendere a fondo il servizio Protezione DDoS di Azure Standard. Assicurarsi che il team possa identificare e mitigare un attacco coordinandosi con i clienti interni ed esterni, incluso il team di supporto Microsoft.

Per il team di risposta DDoS, è consigliabile usare gli esercizi di simulazione come parte integrante della disponibilità del servizio e della pianificazione della continuità. Tali esercizi devono comprendere test di scalabilità.

### <a name="alerts-during-an-attack"></a>Avvisi durante un attacco

Protezione DDoS di Azure Standard identifica e mitiga gli attacchi DDoS senza alcun intervento dell'utente. Per ricevere notifiche quando è attiva una mitigazione per un indirizzo IP pubblico protetto, è possibile [configurare un avviso](../virtual-network/ddos-protection-manage-portal.md) sulla metrica **Under DDoS attack or not** (Sotto attacco DDoS o no). È possibile scegliere di creare avvisi per le altre metriche DDoS per comprendere la portata dell'attacco, il traffico in corso di eliminazione e altri dettagli.

#### <a name="when-to-contact-microsoft-support"></a>Quando contattare il supporto Microsoft

- Durante un attacco DDoS si rileva che le prestazioni della risorsa protetta sono gravemente danneggiate o la risorsa non è disponibile.

- Si ritiene che il servizio Protezione DDoS non funzioni come previsto. 

  Il servizio Protezione DDoS avvia la mitigazione solo se il valore della metrica **Policy to trigger DDoS mitigation (TCP/TCP SYN/UDP)** (Criteri per l'attivazione della mitigazione DDoS - TCP/TCP SYN/UDP) è inferiore a quello del traffico ricevuto nella risorsa dell'indirizzo IP pubblico protetto.

- Si prevede un evento virale che aumenterà significativamente il traffico di rete.

- Un attore ha minacciato di lanciare un attacco DDoS contro le risorse.

Per gli attacchi che hanno un impatto aziendale critico, creare un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) di gravità A.

### <a name="post-attack-steps"></a>Procedura successiva all'attacco.

È sempre una buona strategia eseguire una valutazione a posteriori dopo un attacco e modificare la strategia di risposta DDoS in base alle esigenze. Ecco alcuni aspetti da considerare:

- C'è stata un'interruzione del servizio o dell'esperienza dell'utente a causa della mancanza di un'architettura scalabile?

- Quali applicazioni o servizi hanno sofferto maggiormente?

- Quanto è stata efficace la strategia di risposta DDoS e come potrebbe essere migliorata?

Se si sospetta di essere sotto attacco DDoS, eseguire l'escalation attraverso i normali canali di supporto di Azure.

## <a name="ddos-protection-reference-architectures"></a>Architetture di riferimento di Protezione DDoS

Protezione DDoS Standard è progettata [per i servizi che vengono distribuiti in una rete virtuale.](../virtual-network/virtual-network-for-azure-services.md) Per altri servizi si applica il servizio Protezione DDoS Basic predefinito. Le architetture di riferimento seguenti sono indicate per scenari, con i modelli di architettura raggruppati.

### <a name="virtual-machine-windowslinux-workloads"></a>Carichi di lavoro della macchina virtuale (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Applicazione in esecuzione in macchine virtuali con bilanciamento del carico

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di più macchine virtuali Windows in un set di scalabilità dietro a un servizio di bilanciamento del carico, in modo da migliorare la disponibilità e la scalabilità. Questa architettura può essere usata per qualsiasi carico di lavoro senza stato, ad esempio un server web.

![Diagramma dell'architettura di riferimento per un'applicazione in esecuzione in macchine virtuali con bilanciamento del carico](media/azure-ddos-best-practices/image9.png)

In questa architettura un carico di lavoro viene distribuito tra più istanze di macchina virtuale. L'indirizzo IP pubblico è unico e il traffico Internet viene distribuito alle macchine virtuali tramite un servizio di bilanciamento del carico. Protezione DDoS Standard è abilitata nella rete virtuale di Azure Load Balancer (Internet) che ha l'indirizzo IP pubblico associato.

Il servizio di bilanciamento del carico distribuisce le richieste Internet in ingresso alle istanze delle macchine virtuali. I set di scalabilità di macchine virtuali consentono di aumentare o ridurre il numero di macchine virtuali manualmente o automaticamente in base a regole predefinite. Questo è importante se la risorsa è sotto attacco DDoS. Vedere questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm) per altre informazioni su questa architettura di riferimento.

#### <a name="application-running-on-windows-n-tier"></a>Applicazione in esecuzione in una VM Windows a più livelli

È possibile implementare un'architettura a più livelli in diversi modi. Il diagramma seguente mostra una tipica applicazione Web a tre livelli. Questa architettura è basata sull'architettura descritta nell'articolo [Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). I livelli Web e business usano macchine virtuali con carico bilanciato.

![Diagramma dell'architettura di riferimento per un'applicazione in esecuzione in macchine virtuali Windows a più livelli](media/azure-ddos-best-practices/image10.png)

In questa architettura, il servizio Protezione DDoS Standard è abilitato nella rete virtuale. Tutti gli indirizzi IP pubblici nella rete virtuale sono protetti da DDoS per il livello 3 e 4. Per la protezione di livello 7, distribuire il gateway applicazione nello SKU del Web application firewall. Vedere questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier) per altre informazioni su questa architettura di riferimento.

#### <a name="paas-web-application"></a>Applicazione Web PaaS

Questa architettura di riferimento mostra l'esecuzione di un'applicazione di servizio App di Azure in una singola area. Questa architettura mostra dei set di procedure collaudate per un'applicazione Web che usa il [servizio app di Azure](https://azure.microsoft.com/documentation/services/app-service/) e [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/).
Viene configurata un'area di stand-by per gli scenari di failover.

![Diagramma dell'architettura di riferimento per un'applicazione Web PaaS](media/azure-ddos-best-practices/image11.png)

Gestione traffico di Azure indirizza le richieste in ingresso al gateway applicazione in una delle aree. Durante il normale funzionamento, le richieste vengono indirizzate al gateway applicazione nell'area attiva. Se quell'area non è più disponibile, Gestione traffico effettua il failover al gateway applicazione nell'area di stand-by.

Tutto il traffico da Internet destinato all'applicazione Web viene incanalato verso l'[indirizzo IP pubblico del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) tramite Gestione traffico. In questo scenario, lo stesso servizio app (app Web) non è esposto direttamente verso l'esterno ed è protetto dal gateway applicazione. 

È consigliabile configurare lo SKU WAF del gateway applicazioni (modalità di prevenzione) per proteggersi dagli attacchi di livello 7 (HTTP/HTTPS/Web Socket). Le app Web sono inoltre configurate per [accettare il traffico solo dall'indirizzo IP del gateway applicazione](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Vedere questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) per altre informazioni su questa architettura di riferimento.

### <a name="mitigation-for-non-web-paas-services"></a>Mitigazione dei rischi per Servizi PaaS non web

#### <a name="hdinsight-on-azure"></a>HDInsight in Azure

Questa architettura di riferimento mostra la configurazione di Protezione DDoS Standard per un [cluster Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/). Assicurarsi che il cluster HDInsight sia collegato a una rete virtuale e che Protezione DDoS sia abilitato sulla rete virtuale.

![Riquadri "HDInsight" e "Impostazioni avanzate", con le impostazioni della rete virtuale](media/azure-ddos-best-practices/image12.png)

![Selezione per l'abilitazione di Protezione DDoS](media/azure-ddos-best-practices/image13.png)

In questa architettura, il traffico destinato al cluster HDInsight da Internet viene indirizzato all'indirizzo IP pubblico associato al servizio di bilanciamento del carico del gateway di HDInsight. Il bilanciamento del carico di gateway invia quindi il traffico ai nodi head o direttamente ai nodi di lavoro. Dato che Protezione DDoS Standard è abilitata nella rete virtuale HDInsight, tutti gli indirizzi IP pubblici della rete virtuale ottengono la protezione DDoS per i livelli 3 e 4. Questa architettura di riferimento può essere combinata con le architetture di riferimento a più livelli e a più areee.

Per altre informazioni su questa architettura di riferimento, vedere la documentazione [Estendere Azure HDInsight usando Rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).


> [!NOTE]
> L'ambiente del servizio app di Azure per PowerApps o la gestione API in una rete virtuale con un IP pubblico non sono supportati in modo nativo.

## <a name="next-steps"></a>Passaggi successivi

* [Pagina del prodotto di Protezione DDoS di Azure](https://azure.microsoft.com/services/ddos-protection/)

* [Blog di Protezione DDoS di Azure](http://aka.ms/ddosblog)

* [Documentazione di Protezione DDoS di Azure ](../virtual-network/ddos-protection-overview.md)

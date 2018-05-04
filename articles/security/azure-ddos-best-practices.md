---
title: Procedure consigliate per la Protezione DDoS di Azure e architettura di riferimento | Microsoft Docs
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
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Procedure consigliate per la Protezione DDoS di Azure e architettura di riferimento

Questo documento è destinato ai responsabili IT e al personale di sicurezza. Si richiede familiarità con Azure, con la rete e con la protezione.

La progettazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiede pianificazione e progettazione per un'ampia gamma di modalità di errore. Questo documento fornisce le procedure consigliate per la progettazione di applicazioni in Azure per garantire la resilienza contro gli attacchi DDoS.

## <a name="types-of-attacks"></a>Tipi di attacchi

Il DDoS è un tipo di attacco utilizzato nel tentativo di esaurire le risorse dell'applicazione. L'obiettivo è quello di influire sulla disponibilità e sulla capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati e di dimensioni e impatto maggiori. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile pubblicamente tramite Internet.

Azure offre una protezione continua contro gli attacchi DDoS. Questa protezione viene integrata nella piattaforma di Azure per impostazione predefinita e senza costi aggiuntivi. Oltre alla protezione DDoS di base fornita nella piattaforma, è a disposizione anche una nuova offerta denominata "[Protezione DDoS di Azure Standard](https://azure.microsoft.com/services/ddos-protection/)", che offre funzionalità avanzate di mitigazione dei rischi di DDoS contro gli attacchi alla e che è automaticamente ottimizzata per proteggere le risorse specifiche di Azure. La protezione è semplice da abilitare durante la creazione di nuove reti virtuali. Può essere eseguita anche dopo la creazione iniziale e non richiede alcuna modifica delle applicazioni o delle risorse.

![](media/azure-ddos-best-practices/image1.png)

Gli attacchi DDoS possono essere ampiamente classificati in tre (3) diverse categorie

### <a name="volumetric-attacks"></a>Attacchi volumetrici

Gli attacchi volumetrici sono i tipi più comuni di attacco DDoS. Gli attacchi volumetrici assalti basati sulla forza bruta destinati ai livelli di trasporto e di rete. Tenteranno di esaurire le risorse quali ad esempio i collegamenti di rete. Questi attacchi usano in genere sistemi multipli infetti per intasare il livello di rete con una notevole quantità di traffico in apparenza legittimo. Diversi protocolli di livello di rete, come ad esempio Internet Control Message Protocol, User Datagram Protocol e Transmission Control Protocol vengono utilizzati in questo tipo di attacco.

Gli attacchi DDoS più comunemente a livello di rete sono TCP SYN flood, ICMP echo, UDP flooding, DNS, e NTP amplification. Questo tipo di attacco può essere utilizzato non solo per interrompere il servizio, ma anche come copertura per intrusioni di rete più nefaste e indirizzate. Un esempio di un attacco volumetrico recente è l'[exploit Memcache](https://www.wired.com/story/github-ddos-memcached/) che ha colpito GitHub. Questo attacco ha avuto come destinatario la porta UDP 11211 e ha generato 1,35 Tb/s del volume di attacco.

### <a name="protocol-attacks"></a>Attacchi di protocollo

Protocolli dell'applicazione destinataria degli attacchi di protocollo. Provano a usare tutte le risorse disponibili nei dispositivi dell'infrastruttura, ad esempio i firewall, i server dell'applicazione e i servizi di bilanciamento del carico. Gli attacchi di protocollo usano pacchetti che sono in formato non corretto o che contengono anomalie di protocollo. Questi attacchi operano inviando numeri maggiori di richieste aperte, i server e altri dispositivi di comunicazione rispondono e attendono una risposta del pacchetto. I tentativi del destinatario di rispondere alle richieste aprire infine causano l'arresto anomalo del sistema di destinazione.

L'esempio più comune di attacco DDoS basato sul protocollo è il TCP SYN Flood. In questo attacco, viene indirizzata una successione di richieste TCP SYN verso una destinazione e può essere usata per il sovraccarico. L'obiettivo è di non avere alcuna risposta dalla destinazione. L'interruzione Dyn 2016, oltre ad essere un attacco a livello di applicazione, è stata anche la porta di destinazione 53 dei flood TCP SYN dei server DNS di Dyn.

### <a name="resource-attacks"></a>Attacchi alle risorse

Gli attacchi alle risorse hanno come destinazione il livello dell'applicazione. Essi attivano processi di back-end nel tentativo di sovraccaricare il sistema di destinazione. Gli attacchi alle risorse abusano del traffico che sembra normale, ma che porta query ad utilizzo elevato di CPU al server. Il volume di traffico necessario ad esaurire le risorse è relativamente inferiore rispetto a quello di altri tipi di attacchi. Il traffico in un attacco alle risorse non è distinguibile dal traffico legittimo, rendendolo difficile da rilevare. Gli attacchi alle risorse più comuni sono su HTTP/HTTPS e sui servizi DNS.

## <a name="shared-responsibility-in-the-cloud"></a>Responsabilità condivisa nel cloud

È necessaria una strategia di difesa approfondita per contrastare le crescenti tipologie e i diversi gradi di sofisticazione degli attacchi. La sicurezza è una responsabilità condivisa tra il cliente e Microsoft. Microsoft fa riferimento a questo come a un [modello di responsabilità condivisa](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). La figura seguente mostra la divisione di responsabilità.

![](media/azure-ddos-best-practices/image2.png)

I clienti di Azure traggono vantaggio dall'analisi delle procedure consigliate e dalla compilazione a livello globale delle applicazioni distribuite progettate e testate per errore.

## <a name="fundamental-best-practices"></a>Procedure consigliate fondamentali

Gli attacchi DDoS esistono e sono in continuo aumento. La sezione seguente fornisce indicazioni specifiche per compilare i servizi resilienti contro i DDoS in Azure.

### <a name="design-for-security"></a>Progettare per la sicurezza

I clienti sono tenuti a garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste configurate per usare una quantità troppo elevata di risorse, provocando un'interruzione del servizio. Per proteggere un servizio in esecuzione su Microsoft Azure, i clienti devono avere una buona conoscenza della propria architettura dell'applicazione e devono concentrarsi sui [5 punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars).
I clienti devono conoscere i volumi di traffico tipico, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Inoltre, garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire una negazione del servizio destinata all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy vengono compilate direttamente nella piattaforma di Azure, iniziando con processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

### <a name="design-for-scalability"></a>Progettazione per la scalabilità

La capacità di un sistema di gestire carichi maggiori. I clienti devono progettare le loro applicazioni da [scalare orizzontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di caricamento amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze migliora sia la resilienza che la scalabilità.

Per [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) selezionare un [Piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) che offra più istanze. Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../cloud-services/cloud-services-choose-me.md). Per [Macchine virtuali di Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), verificare che l'architettura VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md). Si consiglia di usare [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per le funzionalità di scalabilità automatica.

### <a name="defense-in-depth"></a>Informazioni dettagliate sulla difesa

L'idea che sta dietro alla difesa in profondità consiste nel gestire i rischi con diverse strategie difensive. La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. È consigliabile che i clienti implementino le progettazioni protette per le loro applicazioni tramite le funzionalità integrate della piattaforma di Azure.

Ad esempio, il rischio di attacco aumenta con le dimensioni o la superficie di attacco dell'applicazione. Si consiglia la riduzione della superficie di attacco tramite la creazione di elenchi di elementi consentiti per chiudere lo spazio dell'indirizzo IP esposto e le porte di attesa non necessari nei servizi di bilanciamento del carico ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[App Gateway](../application-gateway/application-gateway-create-probe-portal.md)) o tramite [ i gruppi di sicurezza di rete(NSG).](../virtual-network/virtual-networks-nsg.md)
È possibile usare [tag di servizio](/virtual-network/security-overview.md) e [gruppi di sicurezza dell'applicazione](/virtual-network/security-overview.md) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione.

I clienti devono distribuire i servizi di Azure in una [rete virtuale](../virtual-network/virtual-networks-overview.md) laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita. Tramite gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md), il traffico del servizio passerà all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

È possibile notare spesso le risorse locali del cliente che vengono attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, è consigliabile ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica. È possibile usare la scalabilità e le funzionalità di protezione DDoS avanzate di Azure distribuendo le entità pubbliche note in Azure. Poiché queste entità pubblicamente accessibili sono spesso una destinazione per gli attacchi DDoS, mettendole in Azure si riduce l'impatto sulle risorse locali.

## <a name="azure-ddos-protection"></a>Protezione DDoS di Azure

Azure dispone di due offerte di servizio DDoS che forniscono protezione dagli attacchi alla rete (Livello 3 e 4) -protezione di base DDoS e protezione standard DDoS. 

### <a name="azure-ddos-basic-protection"></a>Protezione di base DDoS di Azure

La protezione di base viene integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure fornisce una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base DDoS non richiede alcuna modifica alla configurazione o all'applicazione da parte dell'utente. Tutti i servizi di Azure, compresi i servizi PaaS, come ad esempio DNS Azure, sono protetti dalla protezione di base DDoS.

![](media/azure-ddos-best-practices/image3.png)

La protezione di base DDoS di Azure è costituita da componenti hardware e software. Un piano di controllo del software decide quando, dove, e che tipo di traffico debba essere deviato tramite dispositivi hardware che analizzano e rimuovono il traffico dell'attacco. Il piano di controllo prende tale decisione in base a un *criterio* di protezione DDoS ad ampia infrastruttura, che viene impostato in modo statico e applicato universalmente per tutti i clienti di Azure.

Ad esempio, il criterio di protezione DDoS specifica su quale volume di traffico deve trovarsi *attivata* la protezione (ovvero, il traffico del tenant deve essere indirizzato tramite l'appliance di esecuzione dello scrubbing) e, successivamente, come l'appliance di esecuzione dello scrubbing deve *mitigare* l'attacco.

Il servizio di protezione di base DDoS di Azure è destinato alla protezione dell'infrastruttura e della piattaforma di Azure. Mitiga il traffico quando supera una velocità talmente significativa da poter influire negativamente su più clienti in un ambiente multi-tenant. Non fornisce avvisi o i criteri personalizzati per ogni cliente.

### <a name="azure-ddos-standard-protection"></a>Protezione standard DDoS di Azure

La protezione standard offre funzionalità di mitigazione DDoS avanzate e viene inserita automaticamente per proteggere le risorse di Azure specifiche in una rete virtuale. La protezione è semplice da abilitare in qualsiasi rete virtuale nuova o esistente e non richiede alcuna modifica dell'applicazione o della risorsa. Presenta diversi vantaggi rispetto al servizio di base compresa la registrazione, gli avvisi e i dati di telemetria. Quelle elencate di seguito sono le principali differenze del servizio di protezione standard DDoS di Azure.

#### <a name="adaptive-realtime-tuning"></a>Regolazione adattiva in tempo reale

Il servizio di protezione di base DDoS di Azure consente di proteggere i clienti ma li protegge solo per evitare conseguenze per altri clienti. Se, ad esempio, viene eseguito il provisioning di un servizio per un volume tipico di traffico legittimo in entrata minore della *frequenza trigger* dei criteri di protezione DDoS di ampia infrastruttura, potrebbe passare inosservato un attacco DDoS alle risorse di quel cliente. Più in generale, la natura complessa degli attacchi recenti (ad esempio, DDoS multi-vettore) nonché i comportamenti specifici dell'applicazione di tenant, richiedono criteri di protezione personalizzati per ogni cliente.
Ciò avviene tramite due insights:

1. Modelli di traffico a 3 o 4 dei livelli di apprendimento automatico (per-IP) per ogni cliente e
2. Riducendo al minimo i falsi positivi, dato che la scala di Azure consente di assorbire una quantità significativa di traffico.

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>Telemetria di protezione DDoS, monitoraggio e avvisi

Con la protezione standard DDoS, si espongono dati di telemetria avanzata tramite [Monitoraggio di Azure](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) durante la durata di un attacco DDoS. Gli avvisi possono essere configurati per una delle metriche di monitoraggio di Azure esposte dalla protezione DDoS. La registrazione può essere integrata con Splunk (hub eventi di Azure), Log Analytics di Azure e Archiviazione di Azure per l'analisi avanzata tramite l'interfaccia di diagnostica di monitoraggio di Azure.

##### <a name="ddos-mitigation-policies"></a>Criteri di mitigazione della protezione DDoS

Nel portale di Azure fare clic su **Monitoraggio** \> **Metriche**. Nella schermata **Metriche** selezionare il gruppo di risorse, il tipo di risorsa di Indirizzo IP pubblico e l'indirizzo IP pubblico di Azure. Le metriche DDoS saranno visibili sotto il riquadro delle metriche disponibili.

La protezione DDoS standard applica **tre criteri di mitigazione ottimizzati automaticamente (TCP SYN, TCP e UDP)** per ogni IP pubblico della risorsa protetta, nella rete virtuale in cui è abilitata la protezione DDoS. È possibile visualizzare le soglie dei criteri selezionando la metrica **'Pacchetti in ingresso per attivare la mitigazione dei rischi DDoS'**.

![](media/azure-ddos-best-practices/image7.png)

Le soglie dei criteri vengono configurate automaticamente tramite la profilatura del traffico di rete basato sul Machine Learning. La mitigazione dei rischi DDoS ha luogo per un indirizzo IP sotto attacco solo quando viene superata la soglia dei criteri.

##### <a name="under-ddos-attack"></a>Sotto attacco DDoS

Se l'indirizzo IP pubblico è sotto attacco, il valore della metrica 'sotto attacco DDoS o no' si attiva su 1 quando si esegue la mitigazione dei rischi sul traffico di attacco.

![](media/azure-ddos-best-practices/image8.png)

Si consiglia la configurazione di un avviso su questa metrica, da notificare quando è presente una mitigazione del rischio DDoS eseguita sul proprio indirizzo IP pubblico.

Per altre informazioni, vedere [Gestire Protezione DDoS di Azure Standard nel portale di Azure](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="resource-attacks"></a>Attacchi alle risorse

Per gli attacchi specifici alle risorse a livello di applicazione, i clienti devono configurare Web Application Firewall (WAF) per contribuire a proteggere le applicazioni web. WAF analizza il traffico web in ingresso per bloccare gli inserimenti SQL, il Cross-Site Scripting, DDoS e altri attacchi di livello 7. Azure fornisce un [Web application firewall (WAF) del gateway applicazione](../application-gateway/application-gateway-web-application-firewall-overview.md) per la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Sono disponibili diversi tipi di offerte WAF dai partner di Azure che possono essere più adatti alle proprie esigenze tramite [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Anche gli Web application firewall sono vulnerabili agli attacchi di esaurimento volumetrici e dello stato. È fortemente consigliabile attivare la protezione standard DDoS nella rete virtuale WAF per proteggere dagli attacchi volumetrici & di protocollo. Per altre informazioni, vedere la sezione relativa all'architettura di riferimento.

### <a name="protection-planning"></a>Pianificazione della protezione

La pianificazione e la preparazione sono essenziali per comprendere come un sistema si comporterà durante un attacco DDoS. Pianificazione e preparazione consentiranno anche di progettare un piano di risposta per la gestione degli eventi imprevisti.

I clienti devono assicurarsi che la Protezione DDoS Standard sia abilitata nella rete virtuale dell'endpoint con connessione Internet. La configurazione degli avvisi DDoS consente di controllare in modo costante qualsiasi potenziale attacco all'infrastruttura. I clienti devono monitorare le applicazioni in modo indipendente. È necessario comprendere il comportamento normale dell'applicazione. Se l'applicazione non si comporta come previsto durante un attacco DDoS, è necessario eseguire la procedura.

#### <a name="ddos-attacks-orchestration"></a>Orchestrazione di attacchi DDoS

È consigliabile verificare le supposizioni su come i servizi rispondono a un attacco, anche prima che l'attacco abbia luogo tramite periodiche simulazioni. Durante il test, verificare che i servizi o le applicazioni continuino a funzionare come previsto e che non ci siano interruzioni del servizio per l'esperienza dell'utente finale. Identificare gli spazi vuoti dal punto di vista di una tecnologia e di un processo e incorporarli nella strategia di risposta DDoS. Una raccomandazione generale consiste nell'eseguire tali test negli ambienti di gestione temporanea o durante le ore non di punta per ridurre l'impatto sull'ambiente di produzione.

Abbiamo collaborato con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) per compilare un'interfaccia in cui i clienti di Azure possano generare il traffico per abilitare la protezione DDoS agli endpoint pubblici per simulazioni. La simulazione [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) consentirà di:

- Convalidare come la Protezione DDoS di Microsoft Azure consente di proteggere le risorse di Azure da attacchi DDoS

- Ottimizzare il processo di risposta agli eventi imprevisti durante un attacco DDoS

- Documentare la conformità DDoS

- Eseguire il training di team di sicurezza di rete

La sicurezza informatica è una battaglia infinita che richiede un'innovazione costante nella difesa. La protezione standard DDoS di Azure è un sistema all'avanguardia che offre ai clienti una soluzione efficace per mitigare i rischi degli attacchi DDoS che sono sempre più complessi.

## <a name="components-of-a-ddos-response-strategy"></a>Componenti di una strategia di risposta DDoS

Nella maggior parte dei casi quando un attacco DDoS è indirizzato verso le risorse di Azure, si richiede un intervento minimo dal punto di vista dell'utente finale. Comunque, incorporare la mitigazione dei rischi DDoS nella strategia di risposta agli eventi imprevisti dell'organizzazione garantirà un impatto minimo sulla continuità aziendale.

### <a name="microsoft-threat-intelligence"></a>Intelligence per le minacce di Microsoft

Microsoft ha una rete estesa a livello di intelligence in relazione alle minacce, che usa le conoscenze collettive di una comunità di protezione estesa che supporta i servizi online di Microsoft, i partner di Microsoft e le relazioni all'interno della community di sicurezza di Internet. In qualità di provider di infrastrutture essenziali, Microsoft riceve avvisi anticipati sulle minacce, prende intelligence sulle minacce apprese da altri servizi online di Microsoft e dalla base del cliente globale Microsoft. Tutte le intelligence per le minacce di Microsoft vengono incorporate nuovamente nei prodotti per la protezione DDoS di Azure.

Oltre a ciò, la Digital Crimes Unit di Microsoft (DCU) porta avanti strategie offensive contro i botnet, una fonte comune di comando e controllo per gli attacchi DDoS.

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Eseguire una valutazione dei rischi delle risorse di Azure

è fondamentale per comprendere l'ambito del rischio da un attacco DDoS su base continuativa. I clienti devono periodicamente chiedersi: quali nuove risorse di Azure disponibili pubblicamente hanno bisogno di protezione? C'è un punto singolo di errori nel servizio? Come possono essere isolati i servizi per limitare l'impatto di un attacco mantenendo comunque i servizi disponibili per i clienti validi? Esistono reti virtuali in cui la protezione standard DDoS deve essere abilitata, ma non lo è? I miei servizi sono attivi/non attivi con il failover in più regioni?

### <a name="customer-ddos-response-team"></a>Team di risposta del cliente DDoS

La creazione di un team di risposta DDoS è un passaggio chiave per garantire una risposta efficace e rapida a un attacco. I clienti devono identificare vari contatti nell'organizzazione che gestiranno la pianificazione e l'esecuzione. Il Team di risposta DDoS deve avere una conoscenza approfondita del servizio di protezione standard DDoS di Azure e deve essere adatto ad identificare e mitigare il rischio di attacco per il coordinamento con diversi clienti interni ed esterni, compreso il team di supporto di Microsoft, in base alle esigenze.

Microsoft consiglia di incorporare esercizi di simulazione e la pianificazione del Team di risposta DDoS, compreso i test di scalabilità, come parte normale della disponibilità del servizio e della pianificazione della continuità. 

### <a name="during-an-attack"></a>Durante un attacco

La protezione standard DDoS di Azure identificherà e mitigherà il rischio di attacchi DDoS senza alcun intervento dell'utente. Per ricevere notifiche quando è attiva una mitigazione dei rischi per un indirizzo IP pubblico protetto, è possibile [configurare un avviso](../virtual-network/ddos-protection-manage-portal.md) sulla metrica "sotto attacco DDoS o no". È possibile anche esaminare e creare gli avvisi come desiderato per le altre metriche DDoS al fine di comprendere la scala dell'attacco, il traffico in corso di eliminazione e così via.

#### <a name="when-to-contact-microsoft-support"></a>Quando contattare il supporto Microsoft

- Se durante un attacco DDoS si rileva che le prestazioni della risorsa protetta sono gravemente danneggiate o che la risorsa non è disponibile.

- Se si ritiene che il servizio di protezione DDoS non funzioni come previsto. Il servizio di protezione DDoS avvierà la mitigazione dei rischi solo se sono soddisfatti i seguenti criteri:

    - Il valore della metrica 'Criterio per il trigger della mitigazione DDoS (SYN TCP/TCP/UDP) è inferiore a quello del traffico ricevuto nella risorsa dell'indirizzo IP pubblico protetto.

- Come forse è noto, ci sarà un evento pianificato virale che comporterà un aumento significativo del traffico di rete.

- Se un attore ha minacciato di lanciare un attacco DDoS contro le risorse.

Per problemi che hanno un impatto aziendale critico, creare un [ticket di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) con un livello di gravità A.

### <a name="post-attack-steps"></a>Dopo la procedura di attacco

È sempre una buona strategia eseguire una valutazione a posteriori dopo un attacco e modificare di nuovo la strategia di risposta DDoS in base alle esigenze. Ecco alcuni aspetti da considerare:

- C'è stata un'interruzione del servizio o dell'esperienza dell'utente finale a causa della mancanza di un'architettura scalabile?

- Quali applicazioni o servizi hanno sofferto maggiormente?

- Quanto è stata efficace la strategia di risposta DDoS come potrebbe migliorare ulteriormente?

Si sospetta di essere sotto attacco DDoS, eseguire l'escalation attraverso i normali canali di supporto di Azure.

## <a name="ddos-protection-reference-architectures"></a>Architetture di riferimento di protezione DDoS

La protezione standard DDoS è progettata [per i servizi che vengono distribuiti in una rete virtuale.](../virtual-network/virtual-network-for-azure-services.md) Per altri servizi, si applicherà il valore predefinito della protezione di base DDoS. Le architetture di riferimento specifiche indicate di seguito sono disposte per scenari, con i modelli di architettura raggruppati insieme.

### <a name="virtual-machine-windowslinux-workloads"></a>Carichi di lavoro della macchina virtuale (Windows/Linux)

#### <a name="application-running-on-load-balanced-vms"></a>Applicazione in esecuzione sulle macchine virtuali con bilanciamento del carico

Questa architettura di riferimento mostra un set di procedure consolidate per l'esecuzione di più macchine virtuali Windows in un set di scalabilità dietro a un servizio di bilanciamento del carico, in modo da migliorare la disponibilità e la scalabilità. Questa architettura può essere usata per qualsiasi carico di lavoro senza stato, ad esempio un server web.

![](media/azure-ddos-best-practices/image9.png)

In questa architettura un carico di lavoro viene distribuito tra più istanze di macchina virtuale. L'indirizzo IP pubblico è unico e il traffico Internet viene distribuito alle macchine virtuali tramite un servizio di bilanciamento del carico. La protezione standard DDoS è abilitata nella rete virtuale del bilanciamento del carico di Azure (internet) che ha l'indirizzo IP pubblico associato.

Il servizio di bilanciamento del carico distribuisce le richieste Internet in ingresso alle istanze delle macchine virtuali. I set di scalabilità della VM consentono di aumentare o ridurre il numero di macchine virtuali manualmente o automaticamente in base a regole predefinite. Questo è importante se la risorsa è sotto attacco DDoS. Fare riferimento a questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm), per altre informazioni su questa architettura di riferimento.

#### <a name="applications-running-on-windows-n-tier"></a>Applicazioni in esecuzione su Windows a più livelli

È possibile implementare un'architettura a più livelli in diversi modi. Il diagramma mostra una tipica applicazione Web a 3 livelli. Questa architettura è basata sull'architettura descritta in [Eseguire macchine virtuali con carico bilanciato per la scalabilità e la disponibilità](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). I livelli Web e business usano macchine virtuali con carico bilanciato.

![](media/azure-ddos-best-practices/image10.png)

Nell'architettura precedente, la protezione standard DDoS è abilitata nella rete virtuale. Tutti gli indirizzi IP pubblici nella rete virtuale riceveranno una protezione DDoS livello 3/livello 4. Per la protezione di livello 7, deve essere distribuito il Gateway applicazione presente in WAF SKU. Fare riferimento a questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier), per altre informazioni su questa architettura di riferimento.

#### <a name="paas-web-application"></a>Applicazione Web PaaS

Questa architettura di riferimento mostra l'esecuzione di un'applicazione di servizio App di Azure in una singola area. Questa architettura mostra dei set di procedure collaudate per un'applicazione Web che usa [Servizio app di Azure](https://azure.microsoft.com/documentation/services/app-service/) e [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/).
L'area sospesa viene configurata per gli scenari di failover.

![](media/azure-ddos-best-practices/image11.png)

Gestione traffico indirizza le richieste in ingresso al gateway applicazione in una delle aree. Durante il normale funzionamento, le richieste vengono indirizzate al gateway applicazione nell'area attiva. Se quell'area non è più disponibile, Gestione traffico effettua il failover al gateway applicazione nell'area sospesa.

Tutto il traffico da Internet destinato all'applicazione web viene incanalato verso l'[indirizzo IP pubblico del Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) tramite Gestione traffico. In questo scenario, lo stesso servizio app (app web) non è affiancato esternamente in modo diretto ed è protetto dal Gateway applicazione. Si consiglia di configurare Gateway applicazioni WAF SKU (modalità di prevenzione) per proteggersi dagli attacchi di livello 7 (HTTP/HTTPS/Web Socket). Inoltre, le app Web sono configurate per [accettare il traffico solo dall'indirizzo IP del Gateway applicazione](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).

Fare riferimento a questo [articolo](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region), per altre informazioni su questa architettura di riferimento.

### <a name="mitigation-for-non-web-paas-services"></a>Mitigazione dei rischi per Servizi PaaS non web

#### <a name="hdinsight-on-azure"></a>HDInsight in Azure

Questa architettura di riferimento mostra la configurazione della protezione standard DDoS per [cluster HDInsight](https://docs.microsoft.com/azure/hdinsight/) in Azure. È necessario assicurarsi che il cluster HDInsight sia collegato a una rete virtuale e che la protezione DDoS sia abilitata nella rete virtuale.

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

In questa architettura, il traffico destinato al cluster HDInsight da internet viene indirizzato all'indirizzo IP pubblico associato con il bilanciamento del carico di gateway di HDInsight. Il bilanciamento del carico di gateway invia quindi il traffico ai nodi head o direttamente ai nodi di lavoro. Dato che la protezione standard DDoS è abilitata nella rete virtuale HDInsight, tutti gli indirizzi IP pubblici nella rete virtuale riceveranno protezione DDoS a livello 3/livello 4. L'architettura di riferimento precedente può essere combinata con delle architetture di riferimento a più livelli/a più aree.

Per altre informazioni su questa architettura di riferimento, consultare la documentazione [Estendere Azure HDInsight tramite una rete virtuale di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="azure-api-management"></a>Gestione API di Azure

Questa architettura di riferimento protegge l'endpoint pubblico della risorsa [gestione di API](../api-management/api-management-key-concepts.md) pubblicando le API per i clienti esterni all'organizzazione. Le API devono essere distribuite in una rete virtuale esterna per abilitare la protezione DDoS.

![](media/azure-ddos-best-practices/image15.png)

Configurando la rete virtuale esterna, il gateway di Gestione API e il portale per gli sviluppatori sono accessibili dalla rete internet pubblica tramite un servizio di bilanciamento del carico pubblico. In questa architettura, la protezione standard DDoS è abilitata nella rete virtuale esterna della rete virtuale APIM. Il traffico viene indirizzato da internet verso l'indirizzo IP pubblico di APIM, che è protetto contro gli attacchi di rete di livello 3/livello 4. Per proteggersi dagli attacchi di livello 7 HTTP/HTTPs, è possibile configurare un Gateway applicazione in modalità WAF.

[Qui](../virtual-network/virtual-network-for-azure-services.md) è conservato un elenco dei servizi aggiuntivi che vengono distribuiti in una rete virtuale e che possono essere configurati per la protezione standard DDoS. La protezione standard DDoS supporta solo le risorse di Azure Resource Manager. *La distribuzione dell'ambiente del servizio dell'applicazione (ASE) inserito in una rete virtuale con un indirizzo IP pubblico non è supportata in modo nativo.* Per altre informazioni sulla protezione dell'ambiente ASE, consultare questa sezione.

## <a name="next-steps"></a>Passaggi successivi

* [Pagina del prodotto di Protezione DDoS di Azure](https://azure.microsoft.com/services/ddos-protection/)

* [Blog di Protezione DDoS di Azure](http://aka.ms/ddosblog)

* [Documentazione di Protezione DDoS di Azure ](../virtual-network/ddos-protection-overview.md)

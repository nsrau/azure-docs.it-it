---
title: Panoramica e architettura di SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Panoramica dell&quot;architettura della distribuzione di SAP HANA in Azure (istanze di grandi dimensioni).
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 42a2f5488bac192311cd0652286e45a2280032f2
ms.openlocfilehash: 8bb2c8138d5f5ef76490e5439ba2427cbaf9b33d


---
# <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>Panoramica e architettura di SAP HANA in Azure (istanze di grandi dimensioni)
Questa guida all'architettura e alla distribuzione è suddivisa in cinque parti e include informazioni per facilitare la distribuzione di soluzioni SAP nella nuova piattaforma SAP HANA in Azure (istanze di grandi dimensioni). La guida non è completa e non tratta dettagli specifici per l'installazione di soluzioni SAP, ma offre informazioni utili per la distribuzione iniziale e l'esecuzione delle operazioni. Non usarla in sostituzione della documentazione di SAP correlata all'installazione di SAP HANA o delle varie note di supporto SAP che riguardano l'argomento. La guida fornisce inoltre informazioni dettagliate sull'installazione di SAP HANA in Azure (istanze di grandi dimensioni).


Le cinque parti di questa guida trattano gli argomenti seguenti:

- [Panoramica e architettura](sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruttura e connettività](sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installazione di SAP HANA](sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Disponibilità elevata e ripristino di emergenza](sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Risoluzione dei problemi e monitoraggio](sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definizioni

Nella guida all'architettura e alla distribuzione vengono comunemente usati alcuni termini. Tenere presenti i termini seguenti e i relativi significati:

- **IaaS:** (Infrastructure as a Service) infrastruttura distribuita come servizio
- **PaaS:** (Platform as a Service) piattaforma distribuita come servizio
- **SaaS:** (Software as a Service) software come servizio
- **Componente SAP:** singola applicazione SAP, ad esempio ECC, BW, Solution Manager o EP. I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
- **Ambiente SAP:** uno o più componenti SAP raggruppati in modo logico per eseguire una funzione commerciale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
- **Panorama applicativo SAP:** fa riferimento a tutti gli asset SAP nel panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
- **Sistema SAP:** combinazione del livello DBMS e del livello applicazione di un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Le distribuzioni di Azure non supportano la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve quindi essere distribuito in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale. Nel caso di SAP HANA in Azure (istanze di grandi dimensioni), è supportata l'esecuzione del livello applicazione SAP dei sistemi SAP nelle VM di Azure e l'istanza HANA in un'unità nel modulo per istanze di grandi dimensioni.
- **Modulo per istanze di grandi dimensioni:** uno stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze SAP HANA in Azure.
- **SAP HANA in Azure (istanze di grandi dimensioni):** nome ufficiale dell'offerta in Azure per l'esecuzione di istanze HANA in un ambiente hardware con certificazione SAP HANA TDI e distribuito in moduli per istanze grandi dimensioni in diverse aree di Azure. Il termine **istanze HANA di grandi dimensioni** correlato è la forma breve di SAP HANA in Azure (istanze di grandi dimensioni) ed è ampiamente usato in questa guida sulla distribuzione tecnica.
- **Cross-premise:** indica uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è lo scenario tipico in cui verrà distribuita la maggior parte degli asset SAP. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Creare una rete virtuale con una connessione da sito a sito usando il portale di Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Nell'argomento sulla distribuzione del carico di lavoro SAP nel cloud pubblico di Microsoft Azure sono disponibili numerose risorse aggiuntive. È consigliabile che gli utenti che pianificano una distribuzione di SAP HANA in Azure siano esperti e conoscano le entità di IaaS di Azure e la distribuzione dei carichi di lavoro SAP in IaaS di Azure. Le risorse seguenti forniscono altre informazioni ed è necessario fare riferimento a queste ultime prima di continuare:

- [Uso di SAP in macchine virtuali (VM) Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Uso di soluzioni SAP nelle macchine virtuali di Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificazione

Oltre alla certificazione NetWeaver, SAP richiede una particolare certificazione per il supporto di SAP HANA in determinate infrastrutture, ad esempio IaaS di Azure.

La nota principale SAP in NetWeaver, e in un certo senso anche la certificazione di SAP HANA, è [SAP Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure).

Anche [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota di supporto SAP 2316233 - SAP HANA in Microsoft Azure (istanze di grandi dimensioni)) rappresenta uno strumento utile. Descrive la soluzione illustrata in questa guida. È inoltre supportata l'esecuzione di SAP HANA nel tipo di VM GS5 di Azure. [Informazioni su questo scenario sono pubblicate sul sito Web SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La soluzione SAP HANA in Azure (istanze di grandi dimensioni) a cui si fa riferimento nella nota SAP 2316233 fornisce ai clienti di Microsoft e SAP la possibilità di distribuire carichi di lavoro di grandi dimensioni di SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA o altri carichi di lavoro di SAP HANA in Azure. Si basa sul modulo hardware dedicato con certificazione SAP HANA ([SAP HANA Tailored Datacenter Integration - TDI](https://scn.sap.com/docs/DOC-63140)). L'esecuzione come soluzione configurata per SAP TDI consente di avere la conferma del funzionamento di tutte le applicazioni basate su SAP HANA (incluse SAP Business Suite in SAP HANA, SAP Business Warehouse (BW) in SAP HANA, S4/HANA e BW4/HANA).

Rispetto all'esecuzione di SAP HANA in macchine virtuali di Azure, questa soluzione presenta un vantaggio, ossia consente di sfruttare volumi di memoria molto più grandi. Se si vuole abilitare questa soluzione, è necessario tenere in considerazione alcuni aspetti fondamentali:

- Il livello applicazione SAP e le applicazioni non SAP vengono eseguiti nelle VM di Azure ospitate nei consueti moduli hardware di Azure.
- L'infrastruttura, le distribuzioni dell'applicazione e i data center locali del cliente sono connessi alla piattaforma cloud di Microsoft Azure tramite Azure ExpressRoute (scelta consigliata) o la rete privata virtuale (VPN). Active Directory (AD) e DNS vengono estesi anche in Azure.
- L'istanza del database SAP HANA per il carico di lavoro HANA viene eseguita su SAP HANA in Azure (istanze di grandi dimensioni). Il modulo per istanze di grandi dimensioni è connesso alla rete di Azure. In questo modo, il software in esecuzione nelle VM di Azure può interagire con l'istanza HANA in esecuzione in istanze HANA di grandi dimensioni.
- L'hardware di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato e viene fornito in un'infrastruttura distribuita come servizio (IaaS) con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstallato. Come nel caso delle macchine virtuali di Azure, gli aggiornamenti e la manutenzione del sistema operativo sono responsabilità del cliente.
- L'installazione di HANA o di eventuali componenti aggiuntivi necessari per l'esecuzione di SAP HANA nelle unità delle istanze HANA di grandi dimensioni è responsabilità del cliente, così come tutte le operazioni e le attività di gestione in corso di SAP HANA in Azure.
- Oltre alle soluzioni qui descritte, è possibile installare altri componenti nella sottoscrizione di Azure che si connette a SAP HANA in Azure (istanze di grandi dimensioni).  Ad esempio, componenti che consentono la comunicazione con il database di SAP HANA e/o direttamente con quest'ultimo (jump server, server RDP, SAP HANA Studio, SAP Data Services per gli scenari SAP BI o soluzioni di monitoraggio di rete).
- Come in Azure, le istanze HANA di grandi dimensioni offrono supporto per le funzionalità che abilitano la disponibilità elevata e il ripristino di emergenza.

## <a name="architecture"></a>Architettura

In generale, nella soluzione SAP HANA in Azure (istanze di grandi dimensioni) il livello applicazione SAP si trova nelle VM di Azure e il livello database si trova nell'hardware configurato per SAP TDI, a sua volta collocato in un modulo per istanze di grandi dimensioni nella stessa area di Azure connessa a IaaS di Azure.

> [!NOTE]
> Il livello applicazione SAP deve essere distribuito nella stessa area di Azure con il livello DBMS di SAP. Questo scenario è ben documentato nelle informazioni pubblicate relative al carico di lavoro SAP in Azure.

L'architettura complessiva di SAP HANA in Azure (istanze di grandi dimensioni) offre una configurazione hardware con certificazione SAP TDI (non virtualizzata, bare metal, server a elevate prestazioni per il database SAP HANA) insieme alla capacità e alla flessibilità di Azure per ridimensionare le risorse per il livello applicazione SAP in base alle esigenze.

![Panoramica dell'architettura di SAP HANA in Azure (istanze di grandi dimensioni)](./media/sap-hana-overview-architecture/image1-architecture.png)

L'architettura illustrata sopra è suddivisa in tre sezioni:

- **A destra:** un'infrastruttura locale che esegue diverse applicazioni nei data center con gli utenti finali che accedono ad applicazioni line-of-business, ad esempio SAP. In teoria, questa infrastruttura locale viene connessa ad Azure con Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **In centro:** viene mostrato IaaS di Azure e, in questo caso, l'uso di VM di Azure per ospitare SAP o altre applicazioni che usano SAP HANA come sistema DBMS. Istanze HANA più piccole che funzionano con la memoria delle VM di Azure vengono distribuite in VM di Azure con il relativo livello applicazione. Informazioni sulle [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/).
<br />La rete di Azure viene usata per raggruppare i sistemi SAP insieme ad altre applicazioni nelle reti virtuali di Azure (Vnet). Queste reti virtuali si connettono a sistemi locali e a SAP HANA in Azure (istanze di grandi dimensioni).
<br />Per le applicazioni e i database SAP NetWeaver che supportano l'esecuzione in Microsoft Azure, vedere [SAP Support Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure). Per la documentazione relativa alla distribuzione di soluzioni SAP in Azure, vedere:

  -  [Uso di SAP in macchine virtuali (VM) Windows](../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso di soluzioni SAP nelle macchine virtuali di Microsoft Azure](../virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **A sinistra:** viene mostrato l'hardware con certificazione SAP HANA TDI nel modulo per istanze di grandi dimensioni di Azure. Le unità delle istanze HANA di grandi dimensioni sono connesse alle reti virtuali della sottoscrizione di Azure con la stessa tecnologia usata per la connettività dall'istanza locale ad Azure.

Il modulo per istanze di grandi dimensioni di Azure include i componenti seguenti:

- **Elaborazione:** server basati su processori Intel Xeon E7-8890v3 che forniscono la capacità di elaborazione necessaria e sono certificati per SAP HANA.
- **Rete:** un'infrastruttura di rete unificata ad alta velocità che collega l'elaborazione, l'archiviazione e i componenti della rete LAN.
- **Archiviazione:** un'infrastruttura di archiviazione a cui si accede tramite un'infrastruttura di rete unificata. Viene fornita una capacità di archiviazione specifica a seconda della configurazione di SAP HANA in Azure (istanze di grandi dimensioni) distribuita. È inoltre disponibile ulteriore capacità di archiviazione a un costo mensile aggiuntivo.

Nell'infrastruttura multi-tenant del modulo per istanze di grandi dimensioni i clienti vengono distribuiti come tenant isolati. Questi tenant hanno una relazione 1:1 con la sottoscrizione di Azure. Non è quindi possibile accedere alla stessa istanza di SAP HANA in Azure (istanze di grandi dimensioni) eseguita in un modulo per istanze di grandi dimensioni di Azure da due diverse sottoscrizioni di Azure.

Analogamente alle VM di Azure, SAP HANA in Azure (istanze di grandi dimensioni) è disponibile in più aree di Azure. Per offrire funzionalità di ripristino di emergenza, è possibile fornire il consenso esplicito. I vari moduli per istanze di grandi dimensioni nelle aree di Azure sono collegati tra loro.

Così come è possibile scegliere tra diversi tipi di VM con Macchine virtuali di Azure, è possibile scegliere tra diverse SKU delle istanze HANA di grandi dimensioni personalizzate per tipi diversi di carichi di lavoro di SAP HANA. SAP applica la memoria ai rapporti socket del processore per diversi carichi di lavoro in base alle generazioni del processore Intel. Sono disponibili quattro diversi tipi di SKU:

A partire da dicembre 2016, SAP HANA in Azure (istanze di grandi dimensioni) è disponibile in sei configurazioni per le aree di Azure degli Stati Uniti occidentali e orientali:

| Soluzione SAP | CPU | RAM | Archiviazione |
| --- | --- | --- | --- |
| Ottimizzata per OLAP: SAP BW, BW/4HANA<br /> o SAP HANA per carico di lavoro OLAP generico | SAP HANA in Azure S72<br /> – 2 x processore Intel® Xeon® E7-8890 v3 |  768 GB |  3 TB |
| --- | SAP HANA in Azure S144<br /> – 4 x processore Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB |
| --- | SAP HANA in Azure S192<br /> – 4 x processore Intel® Xeon® E7-8890 v4 |  2 TB |  8 TB |
| Ottimizzata per OLTP: SAP Business Suite<br /> in SAP HANA o S/4HANA (OLTP),<br /> OLTP generico | SAP HANA in Azure S72m<br /> – 2 x processore Intel® Xeon® E7-8890 v3 |  1,5 TB |  6 TB |
|---| SAP HANA in Azure S144m<br /> – 4 x processore Intel® Xeon® E7-8890 v3 |  3 TB |  12 TB |
|---| SAP HANA in Azure S192m<br /> – 4 x processore Intel® Xeon® E7-8890 v4 |  4 TB |  16 TB |

Le diverse configurazioni precedenti vengono descritte in [ SAP Support Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota di supporto SAP 2316233 - SAP HANA in Microsoft Azure (istanze di grandi dimensioni).

Le configurazioni specifiche scelte dipendono dal carico di lavoro, dalle risorse della CPU e dalla memoria desiderata. È possibile che il carico di lavoro OLTP sfrutti le SKU ottimizzate per il carico di lavoro OLAP. Analogamente, è possibile sfruttare le SKU OLTP per il carico di lavoro OLAP HANA. Può tuttavia essere necessario limitare la memoria utilizzata da HANA alla memoria certificata con la generazione del processore Intel E7, come [descritto nel sito Web SAP](http://global12.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/appliances.html) per il tipo di appliance _Scale up BW on HANA_.

È importante notare che l'intera infrastruttura del modulo per istanze di grandi dimensioni non è allocata esclusivamente per l'uso di un singolo cliente. Questo vale per SAP HANA in Azure (istanze di grandi dimensioni) come per i rack delle risorse di calcolo e archiviazione connesse tramite un'infrastruttura di rete distribuita in Azure. L'infrastruttura di istanze HANA di grandi dimensioni, come Azure, consente di distribuire diversi &quot;tenant&quot; dei clienti, isolati l'uno dall'altro tramite l'isolamento di rete. Di conseguenza, queste distribuzioni di istanze HANA di grandi dimensioni non interferiscono, né risultano visibili, tra loro. A un tenant distribuito nel modulo per istanze di grandi dimensioni viene assegnata una singola sottoscrizione di Azure. Se si ha una seconda sottoscrizione di Azure che richiede istanze HANA di grandi dimensioni, questa verrà distribuita in un tenant separato in un modulo per istanze di grandi dimensioni, con l'isolamento di rete necessario per impedire la comunicazione diretta tra queste istanze.

Tuttavia, esistono differenze significative tra l'esecuzione di SAP HANA in istanze HANA di grandi dimensioni e l'esecuzione di SAP HANA in VM di Azure distribuite in Azure:

- Non esiste alcun livello di virtualizzazione per SAP HANA in Azure (istanze di grandi dimensioni). Si ottengono le prestazioni dell'hardware bare metal sottostante.
- A differenza di Azure, il server di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato a un cliente specifico. Un riavvio o l'arresto del server non comporta la distribuzione in un altro server del sistema operativo e di SAP HANA. L'unica eccezione si verifica quando un server riscontra problemi ed è necessario eseguire una ridistribuzione su un altro pannello.
- A differenza di Azure, dove i tipi di processore host vengono selezionati per il miglior rapporto prezzo/prestazioni, i tipi di processore scelti per SAP HANA in Azure (istanze di grandi dimensioni) sono più potenti della linea di processori Intel E7v3.

Più clienti possono distribuire nell'hardware SAP HANA in Azure (istanze di grandi dimensioni) e ognuno è schermato dall'altro perché la distribuzione avviene nelle rispettive reti VLAN. Per connettere le istanze HANA di grandi dimensioni in una rete virtuale di Azure (VNet), i componenti di rete disponibili eseguono NAT (Network Address Translation) tra l'intervallo di indirizzi IP della rete virtuale di Azure e lo spazio degli indirizzi IP VLAN all'interno dell'infrastruttura hardware.

## <a name="operations-model-and-responsibilities"></a>Responsabilità e modello operativo

Il servizio fornito con SAP HANA in Azure (istanze di grandi dimensioni) è allineato ai servizi IaaS di Azure. Il cliente ottiene una delle istanze HANA di grandi dimensioni con un sistema operativo installato ottimizzato per SAP HANA. Come per le VM IaaS di Azure, quasi tutte le attività di protezione avanzata del sistema operativo, installazione di software aggiuntivo necessario, installazione di HANA, funzionamento del sistema operativo e di HANA e aggiornamento del sistema operativo e di HANA sono responsabilità del cliente. Microsoft non esegue gli aggiornamenti del sistema operativo o di HANA per conto del cliente.

![Responsabilità di SAP HANA in Azure (istanze di grandi dimensioni)](./media/sap-hana-overview-architecture/image2-responsibilities.png)

Come illustrato nel diagramma, SAP HANA in Azure (istanze di grandi dimensioni) è un'offerta di infrastruttura distribuita come servizio multi-tenant. Di conseguenza, la divisione delle responsabilità è in gran parte al limite tra infrastruttura e sistema operativo. Microsoft si occupa di tutti gli aspetti del servizio al di sotto della riga relativa al sistema operativo e il cliente è responsabile degli aspetti al di sopra di tale riga, incluso il sistema operativo. Pertanto, è possibile continuare a usare i metodi locali più recenti per la conformità, la sicurezza, la gestione delle applicazioni, la gestione di base e la gestione del sistema operativo. I sistemi vengono visualizzati come facenti parte della propria rete.

Tuttavia, questo servizio è ottimizzato per SAP HANA, pertanto vi sono aree in cui il cliente e Microsoft devono collaborare per usare le funzionalità di infrastruttura sottostanti al fine di ottenere risultati ottimali.

L'elenco seguente fornisce informazioni dettagliate su ogni livello e indica le responsabilità del cliente:

**Rete:** tutte le reti interne del modulo per istanze di grandi dimensioni su cui è in esecuzione SAP HANA, il relativo accesso alle risorse di archiviazione, la connettività tra le istanze (per la scalabilità orizzontale e altre funzioni), la connettività con il panorama applicativo e la connettività con Azure, dove il livello applicazione SAP è ospitato nelle macchine virtuali di Azure. È inclusa inoltre la connettività WAN tra data center di Azure per la replica a scopo di ripristino di emergenza. Tutte le reti vengono partizionate dal tenant e configurate con criteri di qualità del servizio.

**Archiviazione:** archiviazione partizionata virtualizzata per tutti i volumi necessari per i server di SAP HANA e per gli snapshot.

**Server:** server fisici dedicati per l'esecuzione dei database di SAP HANA assegnati ai tenant. Si tratta di hardware virtualizzato.

**SDDC:** software di gestione usato per gestire i data center come entità definite da software. Consente a Microsoft di eseguire il pool delle risorse a scopo di prestazioni, disponibilità e ridimensionamento.

**Sistema operativo:** sistema operativo scelto (SUSE Linux o Red Hat Linux) in esecuzione sui server. Le immagini del sistema operativo che vengono visualizzate sono offerte dal singolo fornitore di Linux a Microsoft allo scopo di eseguire SAP HANA. È necessario disporre di una sottoscrizione con il fornitore di Linux per l'immagine ottimizzate specificamente per SAP HANA. Il cliente dovrà eseguire la registrazione delle immagini con il fornitore del sistema operativo. Dal momento del trasferimento da Microsoft, l'utente è inoltre responsabile per qualsiasi altra applicazione di patch del sistema operativo Linux. Questo è correlato ai pacchetti aggiuntivi che potrebbero essere necessari per una corretta installazione di SAP HANA (fare riferimento alla documentazione di installazione di SAP HANA e alle note SAP) e che non sono stati inseriti dal fornitore specifico di Linux nelle immagini del sistema operativo ottimizzate di SAP HANA. La responsabilità del cliente include anche l'applicazione di patch del sistema operativo correlata al malfunzionamento/ottimizzazione del sistema operativo e ai driver correlati all'hardware del server specifico. O qualsiasi applicazione di patch funzionale e di sicurezza del sistema operativo. La responsabilità del cliente ricade inoltre nel monitoraggio e nella pianificazione della capacità di:

- Utilizzo delle risorse della CPU
- Utilizzo della memoria
- Volumi di disco correlati allo spazio disponibile, input/output al secondo e latenza
- Traffico del volume di rete tra le istanze HANA di grandi dimensioni e il livello applicazione SAP

L'infrastruttura sottostante delle istanze HANA di grandi dimensioni fornisce funzionalità per il backup e il ripristino del volume del sistema operativo. Anche l'utilizzo di questa funzionalità è responsabilità del cliente.

**Middleware:** principalmente l'istanza SAP HANA. L'amministrazione, le operazioni e il monitoraggio sono di responsabilità del cliente. Viene fornita una funzionalità che consente di usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Queste funzionalità sono fornite dall'infrastruttura. Tuttavia, rientra tra le responsabilità del cliente anche la progettazione della disponibilità elevata o del ripristino di emergenza con queste funzionalità, sfruttando queste ultime e monitorando l'esecuzione degli snapshot di archiviazione.

**Dati:** i dati gestiti da SAP HANA e altri dati, ad esempio file di backup nei volumi o condivisioni di file. Le responsabilità del cliente includono il monitoraggio dello spazio disponibile su disco e la gestione del contenuto nei volumi, nonché il monitoraggio della corretta esecuzione dei backup dei volumi di disco e degli snapshot di archiviazione. Tuttavia, la corretta esecuzione della replica dei dati ai siti di ripristino di emergenza è responsabilità di Microsoft.

**Applicazioni:** le istanze di applicazioni SAP o, in caso di applicazioni non SAP, il relativo livello applicazione. Le responsabilità del cliente includono distribuzione, gestione, operazioni e monitoraggio di tali applicazioni relative alla pianificazione della capacità di utilizzo delle risorse della CPU, utilizzo della memoria, utilizzo dell'archiviazione di Azure e della larghezza di banda di rete all'interno di reti virtuali di Azure e da reti virtuali di Azure per SAP HANA in Azure (istanze di grandi dimensioni).

**WAN:** le connessioni stabilite da distribuzioni locali a distribuzioni di Azure per i carichi di lavoro. In caso di carichi di lavoro cruciali, usare Azure ExpressRoute. Questa connessione non fa parte della soluzione SAP HANA in Azure (istanze di grandi dimensioni), pertanto l'impostazione di questa connessione è responsabilità del cliente.

**Archivio:** può essere preferibile archiviare copie dei dati mediante i propri metodi preferiti negli account di archiviazione. Ciò comporta che il cliente si occupi di gestione, conformità, costi e operazioni. Il cliente è responsabile della generazione di copie di archivio e backup in Azure e della relativa archiviazione in modo conforme.

Vedere [Contratto di Servizio per SAP HANA in istanze di grandi dimensioni di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze HANA di grandi dimensioni non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che si vuole spostare da altri RDBMS ad HANA, SAP fornisce una serie di report eseguiti su sistemi SAP esistenti. Questi report controllano i dati e calcolano i requisiti di memoria della tabella se il database viene spostato in HANA. Per altre informazioni su come eseguire i report e su come ottenere patch o versioni più recenti, leggere le note SAP seguenti:

- SAP Note #1793345 - Sizing for SAP Suite on HANA (Nota SAP 1793345 - Ridimensionamento della suite SAP in HANA)
- SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report (Nota SAP 1872170 - Report sul ridimensionamento della suite in HANA e S/4 HANA)
- SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report (Nota SAP 2121330 - Domande frequenti: report sul ridimensionamento di SAP BW in HANA)
- SAP Note #1736976 - Sizing Report for BW on HANA (Nota SAP 1736976 - Report sul ridimensionamento per BW in HANA)
- SAP Note #2296290 - New Sizing Report for BW on HANA (Nota SAP 2296290 - Nuovo report sul ridimensionamento per BW in HANA)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

I requisiti di memoria per HANA aumenteranno in base al volume dei dati, può quindi essere utile tenere presente l'utilizzo della memoria e fare previsioni su ciò che accadrà in futuro. In base ai requisiti della memoria, è possibile associare la richiesta a una delle SKU dell'istanza HANA di grandi dimensioni.

## <a name="requirements"></a>Requisiti

Di seguito sono descritti i requisiti per l'esecuzione di SAP HANA in Azure (istanze di grandi dimensioni).

**Microsoft Azure:**

- Una sottoscrizione di Azure che può essere collegata a SAP HANA in Azure (istanze di grandi dimensioni).
- Supporto Microsoft Premier. Per informazioni specifiche sull'esecuzione di SAP in Azure, vedere [SAP Support Note #2015553 - SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota di supporto SAP 2015553 - SAP in Microsoft Azure: prerequisiti di supporto).
- Conoscenza delle SKU delle istanze HANA di grandi dimensioni necessarie dopo l'esecuzione di un esercizio di ridimensionamento con SAP.

**Connettività di rete:**

- Azure ExpressRoute tra istanza locale e Azure: assicurarsi di ordinare almeno una connessione di 1 Gbps dall'ISP per la connessione del data center locale ad Azure.

**Sistema operativo:**

- Licenze per SUSE Linux Enterprise Server 12 per applicazioni SAP.

> [!NOTE] 
> Il sistema operativo di Microsoft non è registrato con SUSE, né è connesso a un'istanza SMT.

- SUSE Linux Subscription Management Tool (SMT) distribuito in una VM di Azure. In questo modo, la soluzione SAP HANA in Azure (istanze di grandi dimensioni) può essere registrata e rispettivamente aggiornata da SUSE, poiché non è disponibile l'accesso a Internet all'interno del data center delle istanze HANA di grandi dimensioni. 
- Licenze per Red Hat Enterprise Linux 6.7 o 7.2 per SAP HANA.

> [!NOTE]
> Il sistema operativo di Microsoft non è registrato con Red Hat, né è connesso a un'istanza di gestione delle sottoscrizioni di Red Hat.

- Red Hat Subscription Manager distribuito in una VM di Azure. In questo modo, la soluzione SAP HANA in Azure (istanze di grandi dimensioni) può essere registrata e rispettivamente aggiornata da Red Hat, poiché non è disponibile l'accesso diretto a Internet all'interno del tenant distribuito nel modulo per istanze di grandi dimensioni di Azure.
- Il contratto di servizio e supporto con il provider di Linux implicitamente incluso nella sottoscrizione della versione specifica di Linux oppure un altro contratto di servizio e supporto che copre la versione specifica di Linux usata e soddisfa i criteri di SAP.

**Database:**

- Licenze e componenti di installazione software per SAP HANA (edizione Platform o Enterprise).

**Applicazioni:**

- Licenze e componenti di installazione software per le applicazioni SAP che si connettono a SAP HANA e sono correlate ai contratti di supporto di SAP.
- Licenze e componenti di installazione software per tutte le applicazioni non SAP usate in relazione all'ambiente SAP HANA in Azure (istanze di grandi dimensioni) e relativi contratti di supporto.

**Competenze:**

- Esperienza e conoscenza di IaaS di Azure e relativi componenti.
- Esperienza e conoscenza sulla distribuzione del carico di lavoro SAP in Azure.
- Certificazione personale dell'installazione di SAP HANA.
- Competenze relative all'architettura SAP per la progettazione della disponibilità elevata e del ripristino di emergenza in SAP HANA.

## <a name="networking"></a>Rete

L'architettura di rete di Azure è un componente chiave per la distribuzione di applicazioni SAP. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È molto probabile che solo uno o due di questi sistemi SAP siano basati su SAP HANA, è quindi possibile che il proprio panorama applicativo sia una soluzione ibrida che sfrutta gli aspetti seguenti:

- Sistemi SAP distribuiti in locale. A causa delle dimensioni, questi sistemi non possono attualmente essere ospitati in Azure. Un esempio classico può essere un sistema SAP ERP di produzione in esecuzione in Microsoft SQL Server (come il database) con &gt;100 CPU e 1 TB di memoria.
- Sistemi SAP basati su SAP HANA distribuiti in locale. Ad esempio, un sistema SAP ERP che richiede più di 6 TB di memoria per il database SAP HANA.
- Sistemi SAP distribuiti in VM di Azure. Questi sistemi possono essere istanze di sviluppo, test, produzione o sandbox per le applicazioni basate su SAP NetWeaver che è possibile distribuire correttamente in Azure (nelle VM), in base alla richiesta di memoria e utilizzo delle risorse. Questi sistemi possono anche essere basati su database come SQL Server (vedere [SAP Support Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure)) o SAP HANA (vedere [SAP HANA Certified IaaS Platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Piattaforme IaaS certificate per SAP HANA)).
- Server applicazioni SAP distribuiti in Azure (nelle VM) che utilizzano SAP HANA in Azure (istanza di grandi dimensioni) in moduli per istanze di grandi dimensioni in Azure.

Mentre un panorama applicativo SAP ibrido (con quattro o più scenari di distribuzione) è tipico, sono presenti istanze di una panoramica SAP completa in Azure e il numero di soluzioni SAP distribuite in VM di Azure aumenterà in base all'aumento di potenza delle VM di Microsoft Azure.

Le reti Azure nel contesto dei sistemi SAP distribuiti in Azure non sono complicate. Si basano sui principi seguenti:

- Le reti virtuali di Azure (Vnet) devono essere connesse al circuito di Azure ExpressRoute che si connette alla rete locale.
- Un circuito ExpressRoute in genere deve avere una larghezza di banda di almeno 1 Gbps. In questo modo, la larghezza di banda è sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione su VM di Azure (oltre che per la connessione ai sistemi Azure dalle istanze locali degli utenti finali).
- Tutti i sistemi SAP in Azure devono essere configurati in reti virtuali di Azure per comunicare tra loro.
- Active Directory e DNS ospitati in locale sono estesi in Azure tramite ExpressRoute.

**Raccomandazione:** distribuire l'intero panorama SAP in Azure in una singola sottoscrizione di Azure. Molte procedure all'interno di un panorama applicativo SAP richiedono connettività di rete trasparente e possibilmente minore tra le istanze di sviluppo, test e produzione di SAP, inoltre l'architettura SAP NetWeaver ha molti automatismi che si basano su questa connettività di rete trasparente tra le diverse istanze. Pertanto, mantenere l'intero panorama applicativo SAP in una sottoscrizione di Azure, anche quando il panorama viene distribuito in più aree di Azure, è altamente consigliabile.

L'architettura e i processi relativi a SAP HANA in Azure (istanze di grandi dimensioni) si basano sulla raccomandazione di cui sopra.

> [!NOTE] 
> Una singola sottoscrizione di Azure può essere collegata a un solo tenant in un modulo per istanze di grandi dimensioni in un'area specifica di Azure e viceversa un singolo tenant del modulo per istanze di grandi dimensioni può essere collegato a una sola sottoscrizione di Azure.

La distribuzione di SAP HANA in Azure (istanze di grandi dimensioni) in due diverse aree di Azure, comporterà la distribuzione di un tenant separato nel modulo per istanze di grandi dimensioni. Tuttavia, è possibile prevedere che entrambi finiranno nella stessa sottoscrizione di Azure, in quanto queste istanze fanno parte dello stesso panorama applicativo SAP.

> [!IMPORTANT] 
> SAP HANA in Azure (istanze di grandi dimensioni) supporta solo la distribuzione di Azure Resource Management.

### <a name="additional-azure-vnet-information"></a>Informazioni aggiuntive sulla rete virtuale di Azure

Per connettere una rete virtuale di Azure a ExpressRoute, è necessario creare un gateway di Azure (vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Un gateway di Azure può essere usato con ExpressRoute per la connessione a un'infrastruttura all'esterno di Azure (o a un modulo per istanze di grandi dimensioni di Azure) o per la connessione tra reti virtuali di Azure. Vedere [Configurare una connessione da rete virtuale a rete virtuale per Resource Manager usando PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È possibile connettere il gateway di Azure a un massimo di quattro diverse connessioni ExpressRoute se provenienti da diversi router MS Enterprise Exchange (MSEE).

> [!NOTE] 
> La velocità effettiva fornita da un gateway di Azure è diversa per i due casi d'uso. Vedere [Informazioni sul gateway VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La velocità effettiva massima che è possibile ottenere con un gateway di rete virtuale è 10 Gbps, usando una connessione ExpressRoute. Tenere presente che la copia dei file tra una VM di Azure che si trova in una rete virtuale di Azure e un sistema locale (come un flusso di copia singola) non consente di ottenere tutta la velocità effettiva delle SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway di rete virtuale, usare più flussi o copiare più file in flussi paralleli di un singolo file.

Dopo aver letto gli articoli precedenti, prendere nota delle informazioni sulla disponibilità della SKU del gateway UltraPerformance in diverse aree di Azure.

### <a name="networking-for-sap-hana-on-azure"></a>Reti in SAP HANA in Azure

Per la connessione a SAP HANA in Azure (istanze di grandi dimensioni), una rete virtuale di Azure deve essere connessa a una rete locale del cliente tramite il relativo gateway di rete virtuale usando ExpressRoute. È inoltre necessario essere connessi tramite un altro circuito ExpressRoute alle istanze HANA di grandi dimensioni che si trovano in un modulo per istanze di grandi dimensioni di Azure. Il gateway di rete virtuale conterrà almeno due connessioni ExpressRoute ed entrambe condivideranno la larghezza di banda massima del gateway di rete virtuale.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze di grandi dimensioni) sono supportate solo le SKU del gateway HighPerformance o UltraPerformance per le reti virtuali.

![Rete virtuale di Azure connessa a SAP HANA in Azure (istanze di grandi dimensioni) e in locale](./media/sap-hana-overview-architecture/image3-on-premises-infrastructure.png)

### <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata sopra è connessa tramite ExpressRoute ad Azure e il circuito ExpressRoute si connette a un router Microsoft Enterprise Edge (MSEE). Vedere [Panoramica tecnica relativa a ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una volta stabilita la connessione, tale route si connette al backbone di Microsoft Azure e tutte le aree di Azure risultano accessibili.

> [!NOTE] 
> Per l'esecuzione dei panorami applicativi SAP in Azure, connettersi al router MSEE più vicino all'area di Azure nel panorama applicativo SAP. I moduli per istanze di grandi dimensioni di Azure sono connessi tramite dispositivi MSEE dedicati per ridurre al minimo la latenza di rete tra VM di Azure in IaaS di Azure e moduli per istanze di grandi dimensioni.

Il gateway di rete virtuale per le VM di Azure, che ospita le istanze dell'applicazione SAP, è connesso a tale circuito ExpressRoute e la stessa rete virtuale è connessa a un router MSEE dedicato per la connessione a moduli per istanze di grandi dimensioni.

Questo è un esempio semplice di un sistema SAP singolo, in cui il livello applicazione SAP è ospitato in Azure e il database SAP HANA viene eseguito in SAP HANA in Azure (istanze di grandi dimensioni). Il presupposto è che una larghezza di banda del gateway di rete virtuale di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se sistemi SAP multipli o sistemi SAP di grandi dimensioni vengono distribuiti tramite la connessione a SAP HANA in Azure (istanze di grandi dimensioni), è ragionevole presupporre che la velocità effettiva della SKU del gateway di rete virtuale HighPerformance possa diventare un collo di bottiglia. In tal caso, scegliere la SKU UltraPerformance, se disponibile. Tuttavia, se è disponibile solo la SKU HighPerformance (fino a 2 Gbps) oppure la SKU UltraPerformance (fino a 10 Gbps) non è sufficiente, è necessario suddividere i livelli applicazione in più reti virtuali di Azure.

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali di Azure per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale di Azure separata per ogni sistema SAP distribuito, invece di combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

 Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuzione del livello applicazione SAP su più reti virtuali di Azure](./media/sap-hana-overview-architecture/image4-networking-architecture.png)

La distribuzione del livello applicazione SAP o dei componenti su più reti virtuali di Azure, come illustrato sopra, comporta un inevitabile sovraccarico di latenza che si verifica durante la comunicazione tra le applicazioni ospitate nelle reti virtuali di Azure. Per impostazione predefinita, il traffico di rete tra le VM di Azure presenti in diverse reti virtuali viene eseguito tramite i router MSEE in questa configurazione. Tuttavia a partire da settembre 2016 questo si può evitare e ottimizzare. Per ottimizzare e ridurre la latenza di comunicazione tra due reti virtuali è necessario eseguire il peering delle reti virtuali di Azure nella stessa area. Anche se si trovano in sottoscrizioni diverse. Con il peering delle rete virtuali di Azure, la comunicazione tra macchine virtuali in due diverse reti virtuali di Azure può avvenire tramite la parte principale della rete di Azure, dimostrando così una latenza simile a quella che si creerebbe se le macchine virtuali fossero nella stessa rete virtuale. Mentre il traffico indirizzato agli intervalli di indirizzi IP collegati tramite il gateway della rete virtuale di Azure viene instradato attraverso l'unico gateway della rete virtuale. È possibile trovare informazioni dettagliate sul peering della rete virtuale di Azure nell'articolo [Peering reti virtuali](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview).
### <a name="minimal-deployment"></a>Distribuzione minima

Per un sistema SAP di piccole dimensioni (distribuzione minima), le VM di Azure ospitano il livello applicazione SAP in Azure nativo (all'interno di una singola rete virtuale) e si connettono al modulo per istanze di grandi dimensioni tramite ExpressRoute. Per usare SAP HANA in Azure (istanze di grandi dimensioni), seguire questi passaggi:

- Ottenere informazioni specifiche relative a quattro diversi intervalli di indirizzi IP:

  1. Un intervallo di indirizzi /29 per le connessioni P2P da usare per i circuiti ExpressRoute.
  
  2. Un blocco CIDR univoco /24 (scelta consigliata) da usare per l'assegnazione di indirizzi IP specifici necessari per SAP HANA in Azure (istanze di grandi dimensioni).
  3. Uno o più blocchi CIDR /24 (scelta consigliata) per le subnet del tenant della rete virtuale di Azure. Si tratta di subnet nella sottoscrizione di Azure del cliente, in cui si trovano le VM di Azure basate su SAP. Gli indirizzi potranno accedere a SAP HANA in Azure (istanze di grandi dimensioni). Deve essere definito un solo blocco di indirizzi del tenant per ogni subnet e i blocchi possono essere aggregati se sono contigui e si trovano nella stessa rete virtuale.
  4. Un intervallo /28 delle subnet del gateway di rete virtuale (un intervallo /27 deve essere usato per le reti P2S).

  - I primi due intervalli sono necessari (uno per ogni sottoscrizione e area di Azure). Gli intervalli di indirizzi IP indicati ai punti 3 e 4 sono un requisito minimo per ogni rete virtuale di Azure e, se si vogliono più subnet/tenant in una rete virtuale, è necessario specificare più intervalli per il punto 3.
![Intervalli di indirizzi IP necessari nella distribuzione minima di SAP HANA di Azure (istanze di grandi dimensioni)](./media/sap-hana-overview-architecture/image5-ip-address-range-a.png)

  -  Se si configurano più subnet di tenant in una rete virtuale di Azure: ![Intervalli di indirizzi IP con spazio di indirizzi contigui per rete virtuale di Azure](./media/sap-hana-overview-architecture/image6-ip-address-range-b.png)

> [!IMPORTANT] 
> Ogni intervallo di indirizzi IP specificato in precedenza non deve sovrapporsi agli altri intervalli. Ognuno deve essere discreto e non una subnet di qualsiasi altro intervallo. Solo l'indirizzo definito ai punti 3 e 4 deve essere applicato alle reti virtuali di Azure. Tutti gli altri vengono usati per la connettività dell'istanza di grandi dimensioni e il routing. Inoltre, come procedura consigliata, gli intervalli di indirizzi dello spazio degli indirizzi devono corrispondere agli intervalli della subnet e non devono avere spazio vuoto o non assegnato. Se si verificano sovrapposizioni tra gli intervalli definiti ai punti 1 e 2 e quelli definiti ai punti 3 e 4, la rete virtuale di Azure non si connetterà al circuito ExpressRoute.

- Un circuito Express Route viene creato da Microsoft tra la sottoscrizione di Azure e il modulo per istanze di grandi dimensioni.
- Creare un tenant di rete sul modulo per istanze di grandi dimensioni.
- Configurare la rete nell'infrastruttura di SAP HANA in Azure (istanze di grandi dimensioni) per accettare gli indirizzi IP dall'intervallo specificato all'interno della rete virtuale di Azure, che comunicherà con le istanze HANA di grandi dimensioni.
- Impostare NAT (Network Address Translation) nel tenant del cliente del modulo per istanze di grandi dimensioni, in modo che l'indirizzo IP interno del tenant venga associato a un indirizzo IP definito dal tenant per Azure.
- In base alle specifiche SKU di SAP HANA in Azure (istanze di grandi dimensioni) acquistate, assegnare un'unità di calcolo in una rete tenant, allocare e montare la risorsa di archiviazione e installare il sistema operativo (SUSE o RedHat Linux).

Distribuzione minima dell'architettura di rete di SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuzione minima con intervalli di indirizzi IP](./media/sap-hana-overview-architecture/image7-minimal-deployment.png)

### <a name="routing-in-azure"></a>Routing in Azure

Tenere presenti due importanti considerazioni sul routing di rete per SAP HANA in Azure (istanze di grandi dimensioni):

1. SAP HANA in Azure (istanze di grandi dimensioni) è accessibile solo dalle VM di Azure nella connessione ExpressRoute dedicata, non direttamente dall'istanza locale. Pertanto i client di amministrazione e le applicazioni che necessitano di accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA.

2. A SAP HANA in Azure (istanze di grandi dimensioni) è assegnato un indirizzo IP da un pool NAT definito. Questo indirizzo IP è accessibile tramite la sottoscrizione di Azure ed ExpressRoute. Poiché l'indirizzo IP fa parte di un pool NAT, è necessario eseguire un'ulteriore configurazione di rete all'interno dell'ambiente. Per informazioni dettagliate, vedere l'articolo correlato sull'installazione di SAP HANA.

> [!NOTE] 
> Se è necessario connettersi a SAP HANA in Azure (istanze di grandi dimensioni) in uno scenario di _data warehouse_, dove le applicazioni e/o gli utenti finali devono connettersi al database di SAP HANA (in esecuzione diretta), è necessario usare un altro componente di rete: un proxy inverso per l'indirizzamento dei dati. Ad esempio, F5 BIG-IP con Gestione traffico distribuita in Azure come soluzione di routing del traffico o del firewall virtuale.

### <a name="leveraging-in-multiple-regions"></a>Utilizzo in più aree

È possibile distribuire SAP HANA in Azure (istanze di grandi dimensioni) in più aree di Azure per motivi diversi dal ripristino di emergenza. Ad esempio si vuole accedere a istanze HANA di grandi dimensioni da ognuna delle VM distribuite nelle varie reti virtuali nelle aree. Poiché gli indirizzi IP con NAT dei diversi server di istanze HANA di grandi dimensioni non vengono propagati oltre le reti virtuali di Azure (che sono direttamente connesse alle istanze tramite il gateway), si ha una lieve variazione della progettazione della rete virtuale descritta in precedenza: un gateway di rete virtuale di Azure può gestire quattro circuiti ExpressRoute diversi da vari router MSEE e ogni rete virtuale connessa a uno dei moduli per istanze di grandi dimensioni può essere connessa al modulo per istanze di grandi dimensioni presente in un'altra area di Azure.

![Reti virtuali di Azure connesse ai moduli per istanze di grandi dimensioni di Azure in diverse aree di Azure](./media/sap-hana-overview-architecture/image8-multiple-regions.png)

La figura precedente illustra come le diverse reti virtuali di Azure in entrambe le aree siano connesse a due circuiti ExpressRoute diversi usati per la connessione a SAP HANA in Azure (istanze di grandi dimensioni) in entrambe le aree di Azure. Le connessioni appena introdotte sono indicate dalle linee rosse con profilo rettangolare. Tramite queste connessioni dalle reti virtuali di Azure, le VM in esecuzione in una di tali reti possono accedere a ognuna delle diverse unità delle istanze HANA di grandi dimensioni distribuite nelle due aree (se si usa la stessa sottoscrizione). Come risulta evidente dall'immagine precedente, si presuppone che siano configurate due connessioni ExpressRoute dall'infrastruttura locale alle due aree di Azure. Questa configurazione è consigliata per il ripristino di emergenza.

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.





<!--HONumber=Jan17_HO1-->



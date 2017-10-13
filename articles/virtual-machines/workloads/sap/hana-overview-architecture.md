---
title: Panoramica e architettura di SAP HANA in Azure (istanze di grandi dimensioni) | Documentazione Microsoft
description: Panoramica dell'architettura della distribuzione di SAP HANA in Azure (istanze di grandi dimensioni).
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
ms.date: 10/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e1820033e051b72601291c5206772192e68769
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Panoramica e architettura di SAP HANA (istanze Large) in Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (istanze Large)

SAP HANA in Azure (istanza Large) è una soluzione univoca per Azure. Oltre a rendere disponibile Macchine virtuali di Azure per poter distribuire ed eseguire SAP HANA, Azure consente di eseguire e distribuire SAP HANA nei server bare metal dedicati ai singoli clienti. La soluzione SAP HANA in Azure (istanze Large) si basa sull'hardware bare metal host/server non condiviso assegnato ai singoli clienti. L'hardware del server è incorporato in stamp più grandi contenenti l'infrastruttura di calcolo/server, di rete e di archiviazione. Questa combinazione ha la certificazione HANA TDI. L'offerta di servizio di SAP HANA in Azure (istanze Large) offre svariati SKU o dimensioni per i server, da unità con 72 CPU e 768 GB di memoria a unità con 960 CPU e 20 TB di memoria.

L'isolamento dei clienti nello stamp dell'infrastruttura viene eseguito nei tenant, in particolare:

- Rete: isolamento dei clienti nello stack dell'infrastruttura tramite rete virtuali per ogni tenant assegnato al cliente. Un tenant viene assegnato a un singolo cliente. Un cliente può avere più tenant. L'isolamento rete dei tenant impedisce la comunicazione di rete tra i tenant a livello di stamp dell'infrastruttura, anche se i tenant appartengono allo stesso cliente.
- Componenti di archiviazione: isolamento tramite macchine virtuali di archiviazione a cui sono assegnati volumi di archiviazione. I volumi di archiviazione possono essere assegnati a una sola macchina virtuale di archiviazione. Una macchina virtuale di archiviazione viene assegnata esclusivamente a un solo tenant nello stack dell'infrastruttura con certificazione SAP HANA TDI. Di conseguenza, i volumi di archiviazione assegnati a una macchina virtuale di archiviazione sono accessibili in un solo tenant specifico e correlato e non sono visibili tra i diversi tenant distribuiti.
- Server o host: un'unità server o host non viene condivisa tra clienti o tenant. Un server o un host distribuito a un cliente è un'unità di calcolo bare metal atomica assegnata a un solo tenant. **Non** vengono usati partizionamenti hardware o software con i quali un cliente potrebbe ritrovarsi a condividere un host o un server con un altro cliente. I volumi di archiviazione assegnati alla macchina virtuale di archiviazione del tenant specifico vengono montati in tale server. A un tenant può essere assegnata in modo esclusivo una o più unità server di SKU diversi.
- In uno stamp dell'infrastruttura SAP HANA in Azure (istanza Large) più tenant diversi vengono distribuiti e isolati l'uno dall'altro tramite i concetti tenant a livello di rete, archiviazione ed elaborazione. 


Queste unità server bare metal sono supportate solo per l'esecuzione di SAP HANA. Il livello dell'applicazione SAP o il livello del middleware del carico di lavoro è in esecuzione in Macchine virtuali di Microsoft Azure. Gli stamp dell'infrastruttura che eseguono le unità SAP HANA in Azure (istanza Large) sono connessi ai backbone di rete di Azure in modo che venga fornita connettività a bassa latenza tra le unità SAP HANA in Azure (istanza Large) e Macchine virtuali di Azure.

Questo è uno di cinque documenti dedicati all'argomento SAP HANA in Azure (istanza Large). In questo documento vengono illustrati l'architettura di base, le responsabilità, i servizi forniti e, a livello generale, le funzionalità della soluzione. La maggior parte delle aree, ad esempio rete e connettività, verrà approfondita in dettaglio negli altri quattro documenti. La documentazione di SAP HANA in Azure (istanza Large) non illustra l'installazione di SAP NetWeaver o le distribuzioni di SAP NetWeaver nelle VM di Azure. Questo argomento viene trattato in una documentazione separata disponibile nello stesso contenitore. 


Le cinque parti di questa guida trattano gli argomenti seguenti:

- [Panoramica e architettura di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Risoluzione dei problemi e monitoraggio di SAP HANA in Azure (istanze di grandi dimensioni)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="definitions"></a>Definizioni

Nella guida all'architettura e alla distribuzione vengono comunemente usati alcuni termini. Tenere presenti i termini seguenti e i relativi significati.

- **IaaS:** (Infrastructure as a Service) infrastruttura distribuita come servizio
- **PaaS:** (Platform as a Service) piattaforma distribuita come servizio
- **SaaS:** (Software as a Service) software come servizio
- **Componente SAP:** singola applicazione SAP, ad esempio ECC, BW, Solution Manager o EP. I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
- **Ambiente SAP:** raggruppamento logico di uno o più componenti SAP per una funzione aziendale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
- **Panorama applicativo SAP:** fa riferimento a tutti gli asset SAP nel panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
- **Sistema SAP:** combinazione del livello DBMS e del livello applicazione di un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Le distribuzioni di Azure non supportano la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve quindi essere distribuito o in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale. Nel caso di SAP HANA in Azure (istanze Large), è previsto che il livello applicazione SAP dei sistemi SAP sia ospitato in VM di Azure e l'istanza di SAP HANA correlata sia ospitata in un'unità nel modulo per istanze Large di HANA.
- **Modulo per istanze di grandi dimensioni:** uno stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze SAP HANA in Azure.
- **SAP HANA in Azure (istanze di grandi dimensioni):** nome ufficiale dell'offerta in Azure per l'esecuzione di istanze HANA in un ambiente hardware con certificazione SAP HANA TDI e distribuito in moduli per istanze grandi dimensioni in diverse aree di Azure. Il termine **istanze Large di HANA** correlato è un'abbreviazione di SAP HANA in Azure (istanze Large) ed è ampiamente usato in questa guida alla distribuzione tecnica.
- **Cross-premise:** indica uno scenario in cui le VM sono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso agli asset Azure della sottoscrizione di Azure. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è lo scenario tipico in cui verrà distribuita la maggior parte degli asset SAP. Per informazioni più dettagliate, vedere [Pianificazione e progettazione per il gateway VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e l'articolo su come [creare una rete virtuale con una connessione da sito a sito usando il portale di Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant:** un cliente distribuito nello stamp di istanze Large di HANA viene isolato in un "tenant". Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, in modo che le unità di archiviazione e di calcolo assegnate ai diversi tenant non possano vedersi o comunicare tra di esse a livello di stamp di istanze Large di HANA. Un cliente può scegliere di avere distribuzioni in tenant diversi. Neppure in questo caso i tenant possono comunicare tra di essi a livello di stamp di istanze Large di HANA.

Nell'argomento sulla distribuzione del carico di lavoro SAP nel cloud pubblico di Microsoft Azure sono disponibili numerose risorse aggiuntive. È consigliabile che la pianificazione e l'esecuzione di una distribuzione di SAP HANA in Azure vengano eseguite da utenti esperti che conoscono le entità di sicurezza dell'infrastruttura IaaS di Azure e la distribuzione dei carichi di lavoro SAP in tale infrastruttura. Le risorse seguenti forniscono altre informazioni ed è necessario fare riferimento a queste ultime prima di continuare:


- [Uso di soluzioni SAP nelle macchine virtuali di Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="certification"></a>Certificazione

Oltre alla certificazione NetWeaver, SAP richiede una particolare certificazione per il supporto di SAP HANA in determinate infrastrutture, ad esempio IaaS di Azure.

La nota principale SAP in NetWeaver, e in un certo senso anche la certificazione di SAP HANA, è [SAP Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure).

Anche [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota di supporto SAP 2316233 - SAP HANA in Microsoft Azure (istanze di grandi dimensioni)) rappresenta uno strumento utile. Descrive la soluzione illustrata in questa guida. È inoltre supportata l'esecuzione di SAP HANA nel tipo di VM GS5 di Azure. [Informazioni su questo scenario sono pubblicate sul sito Web SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La soluzione SAP HANA in Azure (istanze di grandi dimensioni) a cui si fa riferimento nella nota SAP 2316233 fornisce ai clienti di Microsoft e SAP la possibilità di distribuire carichi di lavoro di grandi dimensioni di SAP Business Suite, SAP Business Warehouse (BW), S/4 HANA, BW/4HANA o altri carichi di lavoro di SAP HANA in Azure. La soluzione è basata sul modulo hardware dedicato con certificazione per SAP HANA ([SAP HANA Tailored Datacenter Integration - TDI](https://scn.sap.com/docs/DOC-63140)). L'esecuzione come soluzione configurata SAP HANA TDI consente di avere la certezza che tutte le applicazioni basate su SAP HANA (incluse SAP Business Suite in SAP HANA, SAP Business Warehouse (BW) in SAP HANA, S4/HANA e BW4/HANA) funzioneranno nell'infrastruttura hardware.

Rispetto all'esecuzione di SAP HANA in macchine virtuali di Azure, questa soluzione presenta un vantaggio, ossia consente di sfruttare volumi di memoria molto più grandi. Se si vuole abilitare questa soluzione, è necessario tenere in considerazione alcuni aspetti fondamentali:

- Il livello applicazione SAP e le applicazioni non SAP vengono eseguiti nelle VM di Azure ospitate nei consueti moduli hardware di Azure.
- L'infrastruttura, le distribuzioni di applicazioni e i data center locali del cliente sono connessi alla piattaforma cloud di Microsoft Azure tramite Azure ExpressRoute (scelta consigliata) o una rete privata virtuale (VPN). Active Directory (AD) e DNS vengono estesi anche in Azure.
- L'istanza del database SAP HANA per il carico di lavoro HANA viene eseguita su SAP HANA in Azure (istanze di grandi dimensioni). Il modulo per istanze di grandi dimensioni è connesso alla rete di Azure. In questo modo, il software in esecuzione nelle VM di Azure può interagire con l'istanza HANA in esecuzione in istanze HANA di grandi dimensioni.
- L'hardware di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato e viene fornito in un'infrastruttura distribuita come servizio (IaaS) con SUSE Linux Enterprise Server o Red Hat Enterprise Linux preinstallato. Come nel caso delle macchine virtuali di Azure, gli aggiornamenti e la manutenzione del sistema operativo sono responsabilità del cliente.
- L'installazione di HANA o di eventuali componenti aggiuntivi necessari per l'esecuzione di SAP HANA nelle unità delle istanze HANA di grandi dimensioni è responsabilità del cliente, così come tutte le operazioni e le attività di gestione in corso di SAP HANA in Azure.
- Oltre alle soluzioni qui descritte, è possibile installare altri componenti nella sottoscrizione di Azure che si connette a SAP HANA in Azure (istanze di grandi dimensioni).  Ad esempio, componenti che consentono la comunicazione con il database di SAP HANA e/o direttamente con quest'ultimo (jump server, server RDP, SAP HANA Studio, SAP Data Services per gli scenari SAP BI o soluzioni di monitoraggio di rete).
- Come in Azure, le istanze Large di HANA offrono il supporto di funzionalità di disponibilità elevata e ripristino di emergenza.

## <a name="architecture"></a>Architettura

In generale, nella soluzione SAP HANA in Azure (istanze di grandi dimensioni) il livello applicazione SAP si trova nelle VM di Azure e il livello database si trova nell'hardware configurato per SAP TDI, a sua volta collocato in un modulo per istanze di grandi dimensioni nella stessa area di Azure connessa a IaaS di Azure.

> [!NOTE]
> Il livello applicazione SAP deve essere distribuito nella stessa area di Azure del livello DBMS SAP. Questa regola è ben documentata nelle informazioni pubblicate relative al carico di lavoro SAP in Azure. 

L'architettura complessiva di SAP HANA in Azure (istanze di grandi dimensioni) offre una configurazione hardware con certificazione SAP TDI (non virtualizzata, bare metal, server a elevate prestazioni per il database SAP HANA) insieme alla capacità e alla flessibilità di Azure per ridimensionare le risorse per il livello applicazione SAP in base alle esigenze.

![Panoramica dell'architettura di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image1-architecture.png)

L'architettura illustrata è suddivisa in tre sezioni.

- **A destra:** un'infrastruttura locale che esegue diverse applicazioni nei data center con gli utenti finali che accedono ad applicazioni line-of-business, ad esempio SAP. In teoria, questa infrastruttura locale viene connessa ad Azure con Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Centro:** è riportata l'infrastruttura IaaS di Azure e, in questo caso, l'uso di VM di Azure per ospitare SAP o altre applicazioni che usano SAP HANA come sistema DBMS. Istanze HANA più piccole che funzionano con la memoria delle VM di Azure vengono distribuite in VM di Azure con il relativo livello applicazione. Informazioni sulle [macchine virtuali](https://azure.microsoft.com/services/virtual-machines/).
<br />La rete di Azure viene usata per raggruppare i sistemi SAP insieme ad altre applicazioni nelle reti virtuali di Azure (Vnet). Queste reti virtuali si connettono a sistemi locali e a SAP HANA in Azure (istanze di grandi dimensioni).
<br />Per le applicazioni e i database SAP NetWeaver che supportano l'esecuzione in Microsoft Azure, vedere [SAP Support Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure). Per la documentazione sulla distribuzione di soluzioni SAP in Azure, vedere:

  -  [Uso di SAP in macchine virtuali (VM) Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Uso di soluzioni SAP nelle macchine virtuali di Microsoft Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **A sinistra:** viene mostrato l'hardware con certificazione SAP HANA TDI nel modulo per istanze di grandi dimensioni di Azure. Le unità delle istanze HANA di grandi dimensioni sono connesse alle reti virtuali della sottoscrizione di Azure con la stessa tecnologia usata per la connettività dall'istanza locale ad Azure.

Il modulo per istanze di grandi dimensioni di Azure include i componenti seguenti:

- **Elaborazione:** server basati su processori Intel Xeon E7-8890v3 o Intel Xeon E7-8890v4 che garantiscono la capacità di elaborazione necessaria e sono certificati per SAP HANA.
- **Rete:** un'infrastruttura di rete unificata ad alta velocità che collega l'elaborazione, l'archiviazione e i componenti della rete LAN.
- **Archiviazione:** un'infrastruttura di archiviazione a cui si accede tramite un'infrastruttura di rete unificata. Viene fornita una capacità di archiviazione specifica a seconda della configurazione di SAP HANA in Azure (istanze di grandi dimensioni) distribuita. È inoltre disponibile ulteriore capacità di archiviazione a un costo mensile aggiuntivo.

Nell'infrastruttura multi-tenant del modulo per istanze di grandi dimensioni i clienti vengono distribuiti come tenant isolati. Al momento della distribuzione del tenant, è necessario specificare una sottoscrizione di Azure nella propria iscrizione ad Azure. Le istanze Large di HANA verranno fatturate a questa sottoscrizione di Azure. Questi tenant hanno una relazione 1:1 con la sottoscrizione di Azure. A livello di rete, è possibile accedere a un'unità di istanze Large di HANA distribuita in un tenant in un'area di Azure da diverse reti virtuali di Azure appartenenti a sottoscrizioni di Azure diverse. Tali sottoscrizioni di Azure, tuttavia, devono appartenere alla stessa iscrizione ad Azure. 

Analogamente alle VM di Azure, SAP HANA in Azure (istanze di grandi dimensioni) è disponibile in più aree di Azure. Per offrire funzionalità di ripristino di emergenza, è possibile fornire il consenso esplicito. I diversi moduli per istanze Large in un'area geopolitica sono collegati tra loro. Ad esempio, i moduli per istanze Large di HANA in Stati Uniti occidentali e Stati Uniti orientali sono connessi tramite un collegamento di rete dedicato ai fini della replica per il ripristino di emergenza. 

Così come è possibile scegliere tra diversi tipi di VM con Macchine virtuali di Azure, è possibile scegliere tra diverse SKU delle istanze HANA di grandi dimensioni personalizzate per tipi diversi di carichi di lavoro di SAP HANA. SAP applica la memoria ai rapporti socket del processore per diversi carichi di lavoro in base alle generazioni del processore Intel. Sono disponibili quattro diversi tipi di SKU:

A partire da luglio 2017, SAP HANA in Azure (istanze Large) è disponibile in diverse configurazioni nelle aree di Azure Stati Uniti occidentali, Stati Uniti orientali, Australia orientale, Australia sud-orientale, Europa occidentale ed Europa settentrionale:

| Soluzione SAP | CPU | Memoria | Archiviazione | Disponibilità |
| --- | --- | --- | --- | --- |
| Ottimizzata per OLAP: SAP BW, BW/4HANA<br /> o SAP HANA per carico di lavoro OLAP generico | SAP HANA in Azure S72<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  768 GB |  3 TB | Disponibile |
| --- | SAP HANA in Azure S144<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  1,5 TB |  6 TB | Non più disponibile |
| --- | SAP HANA in Azure S192<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU |  2 TB |  8 TB | Disponibile |
| --- | SAP HANA in Azure S384<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  4 TB |  16 TB | Disponibile per l'ordine |
| Ottimizzata per OLTP: SAP Business Suite<br /> in SAP HANA o S/4HANA (OLTP),<br /> OLTP generico | SAP HANA in Azure S72m<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  1,5 TB |  6 TB | Disponibile |
|---| SAP HANA in Azure S144m<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  3 TB |  12 TB | Non più disponibile |
|---| SAP HANA in Azure S192m<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU  |  4 TB |  16 TB | Disponibile |
|---| SAP HANA in Azure S384m<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  6 TB |  18 TB | Disponibile per l'ordine |
|---| SAP HANA in Azure S384xm<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  8 TB |  22 TB |  Disponibile per l'ordine |
|---| SAP HANA in Azure S576<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  12 TB |  28 TB | Disponibile per l'ordine |
|---| SAP HANA in Azure S768<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  16 TB |  36 TB | Disponibile per l'ordine |
|---| SAP HANA in Azure S960<br /> – 20 x processore Intel® Xeon® E7-8890 v4<br /> 480 core CPU e 960 thread CPU |  20 TB |  46 TB | Disponibile per l'ordine |

- Core CPU: somma di core CPU senza hyperthreading nella somma dei processori dell'unità server.
- Thread CPU: somma di thread di calcolo forniti da core CPU con hyperthreading nella somma dei processori dell'unità server. Per impostazione predefinita, tutte le unità sono configurate per usare hyperthreading.


Le diverse configurazioni indicate sopra coma "Disponibile" o "Non più disponibile" sono descritte in [ SAP Support Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota di supporto SAP 2316233 - SAP HANA in Microsoft Azure, istanze Large). Le configurazioni contrassegnate come "Disponibile per l'ordine" verranno presto incluse nella nota SAP. Tali SKU delle istanze, tuttavia, possono già essere ordinati per sei diverse aree di Azure in cui è disponibile il servizio delle istanze Large di HANA.

Le configurazioni specifiche scelte dipendono dal carico di lavoro, dalle risorse della CPU e dalla memoria desiderata. È possibile che il carico di lavoro OLTP sfrutti le SKU ottimizzate per il carico di lavoro OLAP. 

La base hardware per tutte le offerte è dotata di certificazione SAP HANA TDI. Si distinguono tuttavia due diverse classi di hardware, che suddividono gli SKU in:

- S72, S72m, S144, S144m, S192 e S192m, definiti SKU di "Classe di tipo I".
- S384, S384m, S384xm, S576, S768 e S960, definiti SKU di "Classe di tipo II".

È importante notare che un intero stamp di istanze Large di HANA non viene allocato esclusivamente per l'uso di un singolo cliente. Ciò vale anche per i rack delle risorse di calcolo e archiviazione connesse tramite un'infrastruttura di rete distribuita in Azure. L'infrastruttura di istanze Large HANA, come Azure, distribuisce diversi &quot;tenant&quot; dei clienti, isolati l'uno dall'altro nei tre livelli seguenti:

- Rete: isolamento tramite reti virtuali nello stamp di istanze Large di HANA.
- Spazio di archiviazione: isolamento tramite macchine virtuali di archiviazione a cui sono assegnati volumi di archiviazione e isolamento dei volumi di archiviazione tra i tenant.
- Calcolo: assegnazione dedicata di unità server a un singolo tenant. Nessun partizionamento hardware o software delle unità server. Nessuna condivisione di una singola unità server o host tra i tenant. 

Le distribuzioni di unità di istanze Large di HANA tra tenant diversi non sono quindi visibili l'una all'altra e le unità di istanze Large di HANA distribuite in tenant diversi non possono comunicare direttamente tra di esse a livello di stamp di istanze Large di HANA. Solo le unità di istanze Large di HANA in un solo tenant possono comunicare tra di esse a livello di stamp di istanze Large di HANA.
In termini di fatturazione, un tenant distribuito nel modulo per istanze Large viene assegnato a una sottoscrizione di Azure. A livello di rete, tuttavia, è accessibile dalle reti virtuali di Azure di altre sottoscrizioni di Azure nella stessa registrazione di Azure. Se si esegue la distribuzione con un'altra sottoscrizione di Azure nella stessa area di Azure, si può anche scegliere di richiedere un tenant per istanze Large di HANA separato.

Esistono differenze significative tra l'esecuzione di SAP HANA in istanze HANA di grandi dimensioni e l'esecuzione di SAP HANA in VM di Azure distribuite in Azure:

- Non esiste alcun livello di virtualizzazione per SAP HANA in Azure (istanze di grandi dimensioni). Si ottengono le prestazioni dell'hardware bare metal sottostante.
- A differenza di Azure, il server di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato a un cliente specifico. Non è in alcun modo possibile che un'unità server o host venga partizionata a livello hardware o software. Di conseguenza, un'unità di istanze Large di HANA viene usata come se fosse assegnata per intero a un tenant e con il tenant assegnato al singolo cliente. Un riavvio o un arresto del server non comporta automaticamente la distribuzione in un altro server del sistema operativo e di SAP HANA. Per gli SKU Classe di tipo I, l'unica eccezione si verifica se un server riscontra problemi ed è necessario eseguire la ridistribuzione in un altro server.
- A differenza di Azure, in cui i tipi di processore host vengono selezionati per il miglior rapporto prezzo/prestazioni, per SAP HANA in Azure (istanze Large) vengono scelti i tipi di processore con le prestazioni più elevate della linea di processori Intel E7v3 ed E7v4.


### <a name="running-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Esecuzione di più istanze di SAP HANA in un'unità di istanze Large di HANA
Nelle unità di istanze Large di HANA è possibile ospitare più istanze attive di SAP HANA. Per offrire comunque le funzionalità degli snapshot di archiviazione e del ripristino di emergenza, una configurazione di questo tipo richiede l'impostazione di un volume per istanza. Attualmente, le unità di istanze Large di HANA possono essere suddivise come segue.

- S72, S72m, S144 e S192: con incrementi di 256 GB, con 256 GB come unità iniziale più piccola. È possibile combinare incrementi diversi, come 256 GB, 512 GB e così via, fino al massimo della memoria dell'unità.
- S144m e S192m: con incrementi di 256 GB, con 512 GB come unità più piccola. È possibile combinare incrementi diversi, come 512 GB, 768 GB e così via, fino al massimo della memoria dell'unità.
- Classe di tipo II: con incrementi di 512 GB, con 2 TB come unità iniziale più piccola. È possibile combinare incrementi diversi, come 512 GB, 1 TB, 1,5 TB e così via, fino al massimo della memoria dell'unità.

Di seguito sono riportati alcuni esempi dell'esecuzione di più istanze di SAP HANA:

| SKU | Dimensione della memoria | Dimensioni di archiviazione | Dimensioni con più database |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 istanza di HANA da 768 GB<br /> o 1 istanza da 512 GB + 1 istanza da 256 GB<br /> o 3 istanze da 256 GB | 
| S72m | 1,5 TB | 6 TB | 3 istanze di HANA da 512 GB<br />o 1 istanza da 512 GB + 1 istanza da 1 TB<br />o 6 istanze da 256 GB<br />o 1 istanza da 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 istanze da 512 GB<br />o 4 istanze da 1 TB<br />o 4 istanze da 512 GB + 2 istanze da 1 TB<br />o 4 istanze da 768 GB + 2 istanze da 512 GB<br />o 1 istanza da 4 TB |
| S384xm | 8 TB | 22 TB | 4 istanze da 2 TB<br />o 2 istanze da 4 TB<br />o 2 istanze da 3 TB + 1 istanza da 2 TB<br />o 2 istanze da 2,5 TB + 1 istanza da 3 TB<br />o 1 istanza da 8 TB |


Questo serve a rendere l'idea. Esistono certamente altre varianti. 

### <a name="using-sap-hana-data-tiering-and-extension-nodes"></a>Uso di nodi di estensione e suddivisione in livelli dei dati di SAP HANA
SAP supporta un modello di suddivisione in livelli dei dati per SAP BW di diverse versioni di SAP NetWeaver e SAP BW/4HANA. I dettagli relativi al modello di suddivisione in livelli dei dati sono disponibili in questo documento e nel blog a cui viene fatto riferimento in questo documento di SAP: [SAP BW/4HANA AND SAP BW ON HANA WITH SAP HANA EXTENSION NODES](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) (SAP BW/4HANA E SAP BW SU HANA CON NODI DI ESTENSIONE SAP HANA).
Con le istanze Large di HANA è possibile usare la configurazione option-1 dei nodi di estensione SAP HANA, come descritto in dettaglio in questi documenti di domande frequenti e del blog di SAP. Le configurazioni option-2 possono essere impostate con le SKU delle istanze Large di HANA seguenti: S72m, S192, S192m, S384 e S384m.  
Esaminando la documentazione, il vantaggio potrebbe non essere immediatamente visibile. Osservando invece le linee guida del dimensionamento di SAP, è possibile individuare un vantaggio usando i nodi di estensione option-1 e option-2 di SAP HANA. Ecco un esempio:

- Le linee guida del dimensionamento di SAP HANA richiedono in genere il doppio del volume di dati per la memoria. In questo modo, quando si esegue l'istanza di SAP HANA con hot data, solo il 50% o meno della memoria è occupata dai dati. Idealmente, il resto della memoria viene mantenuto per il funzionamento di SAP HANA.
- Questo significa che in un'unità S192 di istanza Large di HANA con 2 TB di memoria che esegue un database SAP BW, il volume di dati è di 1 TB.
- Se si usa un nodo di estensione aggiuntivo di tipo option-1 di SAP HANA, anche una SKU S192 di istanza Large di HANA conferirebbe una capacità aggiuntiva di 2 TB per il volume di dati. Nella configurazione option-2, addirittura 4 TB aggiuntivi per il volume di warm data. Rispetto al nodo di tipo hot, è possibile usare la capacità di memoria completa del nodo di estensione warm per l'archiviazione dei dati per option-1 e il doppio della memoria per il volume dei dati nella configurazione del nodo di estensione option-2 di SAP HANA.
- Il risultato: una capacità di 3 TB per i dati e un rapporto hot/warm di 1:2 nella configurazione del nodo di estensione option-1 e una capacità di 5 TB per i dati e un rapporto hot/warm di 1:4 nella configurazione del nodo di estensione option-2.

Tuttavia, maggiore sarà il volume di dati rispetto alla memoria, maggiori saranno le probabilità che i dati warm desiderati si trovino in un'archiviazione su disco.


## <a name="operations-model-and-responsibilities"></a>Responsabilità e modello operativo

Il servizio fornito con SAP HANA in Azure (istanze di grandi dimensioni) è allineato ai servizi IaaS di Azure. Il cliente ottiene una delle istanze HANA di grandi dimensioni con un sistema operativo installato ottimizzato per SAP HANA. Come per le VM IaaS di Azure, quasi tutte le attività di protezione avanzata del sistema operativo, installazione di software aggiuntivo necessario, installazione di HANA, funzionamento del sistema operativo e di HANA e aggiornamento del sistema operativo e di HANA sono responsabilità del cliente. Microsoft non impone al cliente aggiornamenti del sistema operativo o di HANA.

![Responsabilità di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image2-responsibilities.png)

Come illustrato nel diagramma, SAP HANA in Azure (istanze di grandi dimensioni) è un'offerta di infrastruttura distribuita come servizio multi-tenant. Di conseguenza, la divisione delle responsabilità è in gran parte al limite tra infrastruttura e sistema operativo. Microsoft si occupa di tutti gli aspetti del servizio al di sotto della riga relativa al sistema operativo e il cliente è responsabile degli aspetti al di sopra di tale riga, incluso il sistema operativo. È quindi possibile continuare a usare i metodi locali più recenti per la conformità, la sicurezza, la gestione delle applicazioni, la gestione di base e la gestione del sistema operativo. I sistemi vengono visualizzati come facenti parte della propria rete.

Tuttavia, questo servizio è ottimizzato per SAP HANA, pertanto vi sono aree in cui il cliente e Microsoft devono collaborare per usare le funzionalità di infrastruttura sottostanti al fine di ottenere risultati ottimali.

L'elenco seguente fornisce informazioni dettagliate su ogni livello e indica le responsabilità del cliente:

**Rete:** tutte le reti interne del modulo per istanze di grandi dimensioni su cui è in esecuzione SAP HANA, il relativo accesso alle risorse di archiviazione, la connettività tra le istanze per la scalabilità orizzontale e altre funzioni, la connettività con il panorama applicativo e la connettività con Azure, dove il livello applicazione SAP è ospitato nelle macchine virtuali di Azure. È inclusa inoltre la connettività WAN tra data center di Azure per la replica a scopo di ripristino di emergenza. Tutte le reti vengono partizionate dal tenant e configurate con criteri di qualità del servizio.

**Archiviazione:** archiviazione partizionata virtualizzata per tutti i volumi necessari per i server di SAP HANA e per gli snapshot. 

**Server:** server fisici dedicati per l'esecuzione dei database di SAP HANA assegnati ai tenant. I server di SKU Classe di tipo I sono indipendenti dall'hardware. La configurazione di questi tipi di server viene raccolta e mantenuta in profili che possono essere spostati da un hardware fisico a un altro. Tali operazioni di spostamento (manuale) di un profilo sono in parte paragonabili alla correzione del servizio di Azure. I server di SKU Classe di tipo II non offrono tale funzionalità.

**SDDC:** software di gestione usato per gestire i data center come entità definite da software. Consente a Microsoft di creare pool delle risorse ai fini della scalabilità, della disponibilità e delle prestazioni.

**Sistema operativo:** sistema operativo scelto (SUSE Linux o Red Hat Linux) in esecuzione sui server. Le immagini del sistema operativo disponibili sono offerte dal singolo fornitore di Linux a Microsoft per l'esecuzione di SAP HANA. È necessario disporre di una sottoscrizione con il fornitore di Linux per l'immagine ottimizzate specificamente per SAP HANA. Il cliente dovrà eseguire la registrazione delle immagini con il fornitore del sistema operativo. Dal momento del trasferimento da Microsoft, l'utente è inoltre responsabile per qualsiasi altra applicazione di patch del sistema operativo Linux. L'applicazione di patch include i pacchetti aggiuntivi che potrebbero essere necessari per una corretta installazione di SAP HANA (vedere la documentazione relativa all'installazione di SAP HANA e le note SAP) e che non sono stati inseriti dal fornitore specifico di Linux nelle immagini del sistema operativo ottimizzate per SAP HANA. La responsabilità del cliente include anche l'applicazione di patch del sistema operativo correlata al malfunzionamento/ottimizzazione del sistema operativo e ai driver correlati all'hardware del server specifico. O qualsiasi applicazione di patch funzionale e di sicurezza del sistema operativo. La responsabilità del cliente ricade inoltre nel monitoraggio e nella pianificazione della capacità di:

- Utilizzo delle risorse della CPU
- Utilizzo della memoria
- Volumi disco, in relazione a spazio disponibile, operazioni di I/O al secondo e latenza
- Traffico del volume di rete tra le istanze HANA di grandi dimensioni e il livello applicazione SAP

L'infrastruttura sottostante delle istanze HANA di grandi dimensioni fornisce funzionalità per il backup e il ripristino del volume del sistema operativo. Anche l'uso di queste funzionalità è responsabilità del cliente.

**Middleware:** principalmente l'istanza SAP HANA. L'amministrazione, le operazioni e il monitoraggio sono responsabilità del cliente. Viene fornita una funzionalità che consente di usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Queste funzionalità sono offerte dall'infrastruttura. Tra le responsabilità del cliente, tuttavia, rientra anche la progettazione della disponibilità elevata o del ripristino di emergenza con queste funzionalità, il relativo uso e il monitoraggio della corretta esecuzione degli snapshot di archiviazione.

**Dati:** i dati gestiti da SAP HANA e altri dati, ad esempio file di backup nei volumi o condivisioni di file. Le responsabilità del cliente includono il monitoraggio dello spazio disponibile su disco e la gestione del contenuto nei volumi, nonché il monitoraggio della corretta esecuzione dei backup dei volumi di disco e degli snapshot di archiviazione. Tuttavia, la corretta esecuzione della replica dei dati ai siti di ripristino di emergenza è responsabilità di Microsoft.

**Applicazioni:** le istanze di applicazioni SAP o, in caso di applicazioni non SAP, il relativo livello applicazione. Le responsabilità del cliente includono distribuzione, amministrazione, operazioni e monitoraggio di tali applicazioni in relazione alla pianificazione della capacità per l'utilizzo delle risorse della CPU, della memoria, delle risorse di archiviazione di Azure e della larghezza di banda di rete nelle reti virtuali di Azure e dalle reti virtuali di Azure a SAP HANA in Azure (istanze Large).

**WAN:** connessioni stabilite dalle distribuzioni locali alle distribuzioni di Azure per i carichi di lavoro. Per la connettività, tutti i clienti con istanze Large di HANA usano Azure ExpressRoute. Questa connessione non fa parte della soluzione SAP HANA in Azure (istanze di grandi dimensioni), pertanto l'impostazione di questa connessione è responsabilità del cliente.

**Archivio:** può essere preferibile archiviare copie dei dati mediante i propri metodi preferiti negli account di archiviazione. L'archiviazione comporta gestione, conformità, costi e operazioni. Il cliente è responsabile della generazione di copie di archivio e backup in Azure e della relativa archiviazione in modo conforme.

Vedere [Contratto di Servizio per SAP HANA in istanze di grandi dimensioni di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze HANA di grandi dimensioni non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che si vuole spostare da altri RDBMS ad HANA, SAP fornisce una serie di report eseguiti su sistemi SAP esistenti. Se il database viene spostato in HANA, questi report controllano i dati e calcolano i requisiti di memoria per l'istanza di HANA. Per altre informazioni su come eseguire i report e su come ottenere patch o versioni più recenti, leggere le note SAP seguenti:

- [SAP Note #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Nota SAP 1793345: ridimensionamento della suite SAP in HANA)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Nota SAP 1872170: report sul ridimensionamento della suite in HANA e S/4 HANA)
- [SAP Note #2121330 - FAQ: SAP BW on HANA Sizing Report](https://launchpad.support.sap.com/#/notes/2121330) (Nota SAP 2121330: domande frequenti: report sul ridimensionamento di SAP BW in HANA)
- [SAP Note #1736976 - Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Nota SAP 1736976: report sul ridimensionamento per BW in HANA)
- [SAP Note #2296290 - New Sizing Report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nota SAP 2296290: nuovo report sul ridimensionamento per BW in HANA)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

Dato che i requisiti di memoria per HANA aumentano in base al volume dei dati, è opportuno conoscere l'utilizzo della memoria attuale e poter prevedere quello futuro. In base ai requisiti della memoria, è possibile associare la richiesta a una delle SKU dell'istanza HANA di grandi dimensioni.

## <a name="requirements"></a>Requisiti

Questo elenco include i requisiti per l'esecuzione di SAP HANA in Azure (istanze Large).

**Microsoft Azure:**

- Una sottoscrizione di Azure che può essere collegata a SAP HANA in Azure (istanze di grandi dimensioni).
- Contratto di supporto tecnico Premier Microsoft. Per informazioni specifiche sull'esecuzione di SAP in Azure, vedere [SAP Support Note #2015553 - SAP on Microsoft Azure: Support Prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota di supporto SAP 2015553 - SAP in Microsoft Azure: prerequisiti di supporto). Usando istanze Large di HANA con 384 e più CPU, è necessario anche estendere il contratto di supporto tecnico Premier in modo da includere Azure Rapid Response (ARR).
- Conoscenza delle SKU delle istanze HANA di grandi dimensioni necessarie dopo l'esecuzione di un esercizio di ridimensionamento con SAP.

**Connettività di rete:**

- Azure ExpressRoute da locale ad Azure: per la connessione del data center locale ad Azure assicurarsi di ordinare una connessione di almeno 1 Gbps dall'ISP. 

**Sistema operativo:**

- Licenze per SUSE Linux Enterprise Server 12 per applicazioni SAP.

> [!NOTE] 
> Il sistema operativo di Microsoft non è registrato con SUSE, né è connesso a un'istanza SMT.

- SUSE Linux Subscription Management Tool (SMT) distribuito in una VM di Azure. In questo modo, la soluzione SAP HANA in Azure (istanze di grandi dimensioni) può essere registrata e rispettivamente aggiornata da SUSE, poiché non è disponibile l'accesso a Internet all'interno del data center delle istanze HANA di grandi dimensioni. 
- Licenze per Red Hat Enterprise Linux 6.7 o 7.2 per SAP HANA.

> [!NOTE]
> Il sistema operativo di Microsoft non è registrato con Red Hat, né è connesso a un'istanza di gestione delle sottoscrizioni di Red Hat.

- Red Hat Subscription Manager distribuito in una VM di Azure. Con Red Hat Subscription Manager, la soluzione SAP HANA in Azure (istanze Large) può essere registrata e aggiornata da Red Hat, dato non è disponibile un accesso diretto a Internet dal tenant distribuito nel modulo per istanze Large di Azure.
- SAP richiede un contratto di supporto anche con il provider di Linux. Questo requisito non viene cancellato dalla soluzione di istanze di grandi dimensioni HANA o dal fatto che Linux viene eseguito in Azure. A differenza di alcune delle immagini della raccolta di Azure per Linux, la tassa di servizio NON è inclusa nella soluzione offerta delle istanze di grandi dimensioni HANA. È compito del cliente soddisfare i requisiti di SAP in relazione ai contratti di supporto con il distributore di Linux.   
   - Per SUSE Linux, per informazioni sui requisiti del contratto di supporto vedere [SAP Note #1984787 - SUSE LINUX Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota SAP 1984787 - SUSE LINUX Enterprise Server 12: note sull'installazione) e [SAP Note #1056161 - SUSE Priority Support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Nota SAP 1056161 - Supporto SUSE prioritario per applicazioni SAP).
   - Per Red Hat Linux, è necessario avere livelli di sottoscrizione corretti che includano supporto e assistenza, con aggiornamenti dei sistemi operativi delle istanze Large di HANA. Red Hat consiglia di ottenere una sottoscrizione "RHEL for SAP Business Applications". Per informazioni dettagliate su supporto e assistenza, vedere [SAP Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167) (Nota SAP 2002167 - Red Hat Enterprise Linux 7.x: installazione e aggiornamento) e [SAP Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/1496410) (Nota SAP 1496410 - Red Hat Enterprise Linux 6.x: installazione e aggiornamento).

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

**SAP:**

- È previsto che il cliente sia un cliente SAP con un contratto di supporto con SAP.
- Soprattutto per le implementazioni in SKU Classe di tipo II delle istanze Large di HANA, è consigliabile consultare SAP in merito alle versioni di SAP HANA e alle configurazioni finali in hardware di grandi dimensioni con scalabilità verticale.


## <a name="storage"></a>Archiviazione

Il layout di archiviazione per SAP HANA in Azure (istanze Large) viene configurato da SAP HANA in Azure Service Management tramite le linee guida consigliate di SAP, come illustrato nel white paper [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti per le risorse di archiviazione SAP HANA).

Nelle istanze Large Classe di tipo I di HANA è disponibile un volume di archiviazione quattro volte superiore al volume di memoria. Per le unità di istanze Large Classe di tipo II di HANA, lo spazio di archiviazione non sarà il quadruplo. Le unità sono dotate di un volume destinato all'archiviazione dei backup del log delle transazioni di HANA. Per maggiori informazioni vedere [Come installare e configurare SAP HANA (istanze di grandi dimensioni) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Per l'allocazione dello spazio di archiviazione, vedere la tabella seguente, che elenca approssimativamente la capacità dei diversi volumi disponibili nelle diverse unità di istanze Large di HANA.

| SKU delle istanze Large di HANA | hana/data | hana/log | hana/shared | HANA/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576 | 20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768 | 28.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960 | 36.000 GB | 4100 GB | 2050 GB | 4100 GB |


I volumi distribuiti effettivi possono variare leggermente in base alla distribuzione e allo strumento usato per visualizzare le dimensioni dei volumi.

In caso di suddivisione di uno SKU delle istanze Large di HANA, alcuni esempi delle possibili parti della divisione saranno simili ai seguenti:

| Partizione di memoria in GB | hana/data | hana/log | hana/shared | HANA/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Queste dimensioni sono numeri approssimativi che possono variare leggermente in base alla distribuzione e agli strumenti usati per esaminare i volumi. Sono ipotizzabili anche altre dimensioni di partizione, ad esempio 2,5 TB. Tali dimensioni di archiviazione verranno calcolate con una formula simile a quella usata per le partizioni riportate sopra. Il termine "partizione" non indica che le risorse del sistema operativo, della memoria o della CPU sono in qualche modo partizionate. Indica solo le partizioni di archiviazione per le diverse istanze di HANA che si vorranno eventualmente distribuire in una singola unità di istanze Large di HANA. 

Se necessario, il cliente può aggiungere più spazio di archiviazione acquistando altro spazio in unità di 1 TB. Tale spazio di archiviazione può essere aggiunto come ulteriore volume oppure essere usato per estendere uno o più dei volumi esistenti. Non è possibile ridurre le dimensioni dei volumi originariamente distribuiti, in gran parte documentati nelle tabelle precedenti, né modificare i nomi dei volumi o i nomi di montaggio. I volumi di archiviazione descritti sopra vengono collegati alle unità di istanze Large di HANA come volumi NFS4.

Il cliente può scegliere di usare gli snapshot di archiviazione a scopo di backup/ripristino e di ripristino di emergenza. Maggiori dettagli su questo argomento sono presenti on [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi
La memoria usata per le istanze di grandi dimensioni HANA consente una crittografia trasparente dei dati al momento dell'archiviazione su dischi. In fase di distribuzione di un'unità di istanze Large di HANA, è possibile abilitare questo tipo di crittografia. È anche possibile scegliere di passare ai volumi crittografati in seguito alla distribuzione. Lo spostamento da volumi non crittografati a volumi crittografati è trasparente e non richiede un tempo di inattività. 

Con gli SKU Classe di tipo I, il volume in cui è archiviato il LUN di avvio è crittografato. Nel caso degli SKU Classe di tipo II delle istanze Large di HANA, è necessario crittografare il LUN di avvio con i metodi del sistema operativo. 


## <a name="networking"></a>Rete

L'architettura della rete di Azure è un componente chiave per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi SAP siano basati su SAP HANA e che quindi il panorama applicativo SAP sia un ibrido che include quanto segue:

- Sistemi SAP distribuiti in locale. A causa delle dimensioni, questi sistemi non possono attualmente essere ospitati in Azure. Un esempio classico può essere un sistema SAP ERP di produzione in esecuzione in Microsoft SQL Server (come database) che richiede risorse di memoria o CPU superiori a quelle che possono essere disponibili nelle VM di Azure.
- Sistemi SAP basati su SAP HANA distribuiti in locale.
- Sistemi SAP distribuiti in VM di Azure. Questi sistemi possono essere istanze di sviluppo, test, sandbox o produzione per qualsiasi applicazione basata su SAP NetWeaver distribuibile in VM di Azure in base alle richieste di memoria e all'utilizzo delle risorse. Questi sistemi possono anche essere basati su database come SQL Server o SAP HANA. Vedere rispettivamente [SAP Support Note #1928533 - SAP Applications on Azure: Supported Products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure) e [SAP HANA Certified IaaS Platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Piattaforme IaaS certificate per SAP HANA).
- Server applicazioni SAP distribuiti in Azure (nelle VM) che utilizzano SAP HANA in Azure (istanza di grandi dimensioni) in moduli per istanze di grandi dimensioni in Azure.

Nonostante un panorama applicativo SAP ibrido (con quattro o più diversi scenari di distribuzione) sia tipico, in molti casi i clienti hanno un panorama applicativo SAP completo in esecuzione in Azure. Con la maggiore potenza acquisita dalle VM di Microsoft Azure, il numero di clienti che sposta tutte le soluzioni SAP in Azure è in aumento.

Le reti Azure nel contesto dei sistemi SAP distribuiti in Azure non sono complicate. Si basano sui principi seguenti:

- Le reti virtuali di Azure (Vnet) devono essere connesse al circuito di Azure ExpressRoute che si connette alla rete locale.
- Un circuito ExpressRoute per la connessione da locale ad Azure dovrà avere in genere una larghezza di banda di almeno 1 Gbps. Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM di Azure, nonché per la connessione ai sistemi Azure degli utenti finali in locale.
- Tutti i sistemi SAP in Azure devono essere configurati in reti virtuali di Azure per comunicare tra loro.
- Active Directory e DNS ospitati in locale sono estesi in Azure tramite ExpressRoute da locale.


> [!NOTE] 
> Dal punto di vista della fatturazione, solo una singola sottoscrizione di Azure può essere collegata a un singolo tenant in un modulo per istanze Large in un'area specifica di Azure e viceversa un singolo tenant del modulo per istanze Large può essere collegato a una sola sottoscrizione di Azure. Questo aspetto non presenta differenze rispetto a qualsiasi altro oggetto fatturabile in Azure.

La distribuzione di SAP HANA in Azure (istanze Large) in più aree diverse di Azure determina la distribuzione di un tenant separato nel modulo per istanze Large. Tuttavia, è possibile eseguire entrambi nella stessa sottoscrizione di Azure, in quanto queste istanze fanno parte dello stesso panorama SAP. 

> [!IMPORTANT] 
> SAP HANA in Azure (istanze di grandi dimensioni) supporta solo la distribuzione di Azure Resource Management.

 

### <a name="additional-azure-vnet-information"></a>Informazioni aggiuntive sulla rete virtuale di Azure

Per connettere una rete virtuale di Azure a ExpressRoute, è necessario creare un gateway di Azure (vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Un gateway di Azure può essere usato con ExpressRoute per la connessione a un'infrastruttura all'esterno di Azure (o a un modulo per istanze di grandi dimensioni di Azure) o per la connessione tra reti virtuali di Azure. Vedere [Configurare una connessione da rete virtuale a rete virtuale per Resource Manager usando PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È possibile connettere il gateway di Azure a un massimo di quattro diverse connessioni ExpressRoute a condizione che tali connessioni provengano da router MS Enterprise Edges (MSEE) diversi.  Per altre informazioni, vedere [Infrastruttura e connettività di SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> La velocità effettiva fornita da un gateway di Azure è diversa per i due casi d'uso. Vedere [Informazioni sul gateway VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La velocità effettiva massima che è possibile ottenere con un gateway di rete virtuale è 10 Gbps, usando una connessione ExpressRoute. Tenere presente che la copia di file tra una VM di Azure che si trova in una rete virtuale di Azure e un sistema locale (come singolo flusso di copia) non raggiunge l'intera velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway di rete virtuale, usare più flussi o copiare più file in flussi paralleli di un singolo file.


### <a name="networking-architecture-for-hana-large-instances"></a>Architettura di rete per istanze Large di HANA
L'architettura di rete per le istanze Large di HANA, illustrata di seguito, può essere suddivisa in quattro diverse parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è il dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Nell'immagine di seguito è riportata in basso a destra.
- Rete di Azure, illustrata brevemente sopra, con reti virtuali di Azure anche in questo caso con gateway. In quest'area è necessario trovare la progettazione appropriata per i propri requisiti in termini di applicazioni, sicurezza e conformità. Un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e degli SKU del gateway di Azure è l'uso di istanze Large di HANA. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Vedere [Infrastruttura e connettività di SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Rete nelle istanze Large di HANA, in gran parte trasparente per il cliente.

![Rete virtuale di Azure connessa a SAP HANA in Azure (istanze di grandi dimensioni) e in locale](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Il fatto di usare istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute, né sul requisito di una o più reti virtuali che eseguono le VM di Azure che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

La differenza rispetto alle distribuzioni SAP in un ambiente Azure puro consiste in quanto segue:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali di Azure tramite un altro circuito ExpressRoute. Per separare le condizioni di carico, i collegamenti ExpressRoute da locale alle reti virtuali di Azure e i collegamenti tra le reti virtuali di Azure e le istanze Large di HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e l'istanza di HANA è di diversa natura e prevede un'elevata quantità di richieste e trasferimenti dati di tipo burst (set di risultati) di piccole dimensioni da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway di rete virtuale ha almeno due connessioni ExpressRoute ed entrambe condividono la larghezza di banda massima per i dati in ingresso del gateway di rete virtuale.

La latenza di rete rilevata tra le VM di Azure e le unità di istanze Large di HANA può essere superiore alla latenza di andata e ritorno tipica di una rete da VM a VM. A seconda dell'area di Azure, i valori misurati possono superare la latenza di andata e ritorno di 0,7 millisecondi classificata come inferiore alla media in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota SAP 1100926 - Domande frequenti sulle prestazioni di rete). Nonostante ciò, la distribuzione da parte dei clienti di applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA ha avuto esito positivo. I clienti che hanno eseguito la distribuzione hanno registrato importanti miglioramenti eseguendo le applicazioni SAP in SAP HANA con unità di istanze Large di HANA. È comunque consigliabile testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure.
 
Per garantire una latenza di rete deterministica tra le VM di Azure e l'istanza Large di HANA, la scelta dello SKU del gateway di rete virtuale di Azure è essenziale. A differenza dei modelli di traffico tra l'ambiente locale e le VM di Azure, il modello di traffico tra le VM di Azure e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma elevati di richieste e volumi dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per gli SKU Classe di tipo II delle istanze Large di HANA, l'utilizzo dello SKU UltraPerformance come gateway di rete virtuale di Azure è obbligatorio.  

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze di grandi dimensioni) sono supportate solo le SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per gli SKU Classe di tipo II delle istanze Large di HANA, come gateway di rete virtuale di Azure è supportato solo lo SKU UltraPerformance.



### <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata sopra è connessa tramite ExpressRoute ad Azure e il circuito ExpressRoute si connette a un router Microsoft Enterprise Edge (MSEE). Vedere [Panoramica tecnica relativa a ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una volta stabilita la connessione, tale route si connette al backbone di Microsoft Azure e tutte le aree di Azure risultano accessibili.

> [!NOTE] 
> Per l'esecuzione dei panorami applicativi SAP in Azure, connettersi al router MSEE più vicino all'area di Azure nel panorama applicativo SAP. I moduli per istanze di grandi dimensioni di Azure sono connessi tramite dispositivi MSEE dedicati per ridurre al minimo la latenza di rete tra VM di Azure in IaaS di Azure e moduli per istanze di grandi dimensioni.

Il gateway di rete virtuale per le VM di Azure, che ospita le istanze dell'applicazione SAP, è connesso a tale circuito ExpressRoute e la stessa rete virtuale è connessa a un router MSEE dedicato per la connessione a moduli per istanze di grandi dimensioni.

Questo è un esempio semplice di un sistema SAP singolo, in cui il livello applicazione SAP è ospitato in Azure e il database SAP HANA viene eseguito in SAP HANA in Azure (istanze di grandi dimensioni). Si presuppone che la larghezza di banda del gateway di rete virtuale con velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

In caso di distribuzione di più sistemi SAP o sistemi SAP di grandi dimensioni con connessione a SAP HANA in Azure (istanze Large), è ragionevole supporre che la velocità effettiva del gateway di rete virtuale possa diventare un collo di bottiglia. In tal caso, è necessario dividere i livelli applicazione in più reti virtuali di Azure. Può anche essere consigliabile creare reti virtuali speciali che si connettono alle istanze di grandi dimensioni HANA per casi come:

- Esecuzione di backup direttamente dalle istanze di grandi dimensioni HANA a una macchina virtuale in Azure che ospita le condivisioni NFS
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

L'uso di reti virtuali separate che ospitano le VM che gestiscono l'archiviazione evita l'impatto del trasferimento di dati o file di grandi dimensioni dalle istanze Large di HANA ad Azure sul gateway di rete virtuale usato dalle VM che eseguono il livello applicazione SAP. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali di Azure per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale di Azure separata per ogni sistema SAP distribuito, invece di combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

 Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuzione del livello applicazione SAP su più reti virtuali di Azure](./media/hana-overview-architecture/image4-networking-architecture.png)

La distribuzione del livello applicazione SAP o dei componenti su più reti virtuali di Azure, come illustrato sopra, comporta un inevitabile sovraccarico di latenza che si verifica durante la comunicazione tra le applicazioni ospitate nelle reti virtuali di Azure. Per impostazione predefinita, in questa configurazione il traffico di rete tra VM di Azure in reti virtuali diverse viene instradato attraverso i router MSEE. A partire da settembre 2016, tuttavia, questo routing può essere ottimizzato. Per ottimizzare e ridurre la latenza di comunicazione tra due reti virtuali è necessario eseguire il peering delle reti virtuali di Azure nella stessa area. Anche se tali reti virtuali si trovano in sottoscrizioni diverse. Con il peering delle rete virtuali di Azure, la comunicazione tra macchine virtuali in due diverse reti virtuali di Azure può avvenire tramite la parte principale della rete di Azure, dimostrando così una latenza simile a quella che si creerebbe se le macchine virtuali fossero nella stessa rete virtuale. Il traffico indirizzato agli intervalli di indirizzi IP connessi tramite il gateway di rete virtuale di Azure viene invece instradato attraverso il singolo gateway della rete virtuale. È possibile trovare informazioni dettagliate sul peering della rete virtuale di Azure nell'articolo [Peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routing in Azure

Tenere presenti due importanti considerazioni sul routing di rete per SAP HANA in Azure (istanze di grandi dimensioni):

1. SAP HANA in Azure (istanze di grandi dimensioni) è accessibile solo dalle VM di Azure nella connessione ExpressRoute dedicata, non direttamente dall'istanza locale. Alcuni client di amministrazione e le applicazioni che necessitano di accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA.

2. A SAP HANA sulle unità di Azure (istanze di grandi dimensioni) è stato assegnato un indirizzo IP dall'intervallo di indirizzi del pool di indirizzi IP del Server che l'utente, in qualità di cliente, ha inviato. Per altre informazioni vedere [Infrastruttura e connettività a SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  Questo indirizzo IP è accessibile tramite le sottoscrizioni di Azure e il collegamento ExpressRoute che connette le reti virtuali di Azure ad HANA in Azure (istanze Large). L'indirizzo IP assegnato all'esterno dell'intervallo di indirizzi del pool di indirizzi IP del server è stato direttamente assegnato all'unità di hardware e NON è più NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

> [!NOTE] 
> Se è necessario connettersi a SAP HANA in Azure (istanze di grandi dimensioni) in uno scenario di _data warehouse_, dove le applicazioni e/o gli utenti finali devono connettersi al database di SAP HANA (in esecuzione diretta), è necessario usare un altro componente di rete: un proxy inverso per l'indirizzamento dei dati. Ad esempio, F5 BIG-IP, NGINX con Gestione traffico distribuita in Azure come soluzione di routing del traffico o del firewall virtuale.

### <a name="internet-connectivity-of-hana-large-instances"></a>Connettività Internet delle istanze di grandi dimensioni HANA
Le istanze di grandi dimensioni HANA NON hanno una connettività internet diretta. Questo limita la possibilità di eseguire operazioni, ad esempio di registrare l'immagine del sistema operativo direttamente con il fornitore del sistema operativo. Di conseguenza potrebbe essere necessario usare il server SLES SMT locale o la gestione sottoscrizioni RHEL

### <a name="data-encryption-between-azure-vms-and-hana-large-instances"></a>Crittografia dei dati tra istanze di grandi dimensioni HANA e macchine virtuali di Azure
I dati trasferiti tra le istanze di grandi dimensioni HANA e le macchine virtuali di Azure non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBM e le applicazioni basate su JDBC/ODBC è possibile abilitare la crittografia del traffico. Vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="using-hana-large-instance-units-in-multiple-regions"></a>Uso di unità di istanze Large di HANA in più aree

È possibile distribuire SAP HANA in Azure (istanze di grandi dimensioni) in più aree di Azure per motivi diversi dal ripristino di emergenza. Ad esempio si vuole accedere a istanze HANA di grandi dimensioni da ognuna delle VM distribuite nelle varie reti virtuali nelle aree. Poiché gli indirizzi IP assegnati alle diverse unità di istanze di grandi dimensioni HANA non vengono propagati oltre le reti virtuali di Azure, che sono direttamente connesse alle istanze tramite il gateway, si ha una lieve variazione del progetto di rete virtuale descritto in precedenza. Un gateway di rete virtuale di Azure può gestire quattro circuiti ExpressRoute diversi da vari MSEE e ogni rete virtuale connessa a uno dei moduli per istanze di grandi dimensioni può essere connessa al modulo per istanze di grandi dimensioni presente in un'altra area di Azure.

![Reti virtuali di Azure connesse ai moduli per istanze di grandi dimensioni di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura precedente illustra come le diverse reti virtuali di Azure in entrambe le aree siano connesse a due circuiti ExpressRoute diversi usati per la connessione a SAP HANA in Azure (istanze di grandi dimensioni) in entrambe le aree di Azure. Le connessioni appena introdotte sono indicate dalle linee rosse con profilo rettangolare. Con queste connessioni dalle reti virtuali di Azure, le VM in esecuzione in una di tali reti possono accedere a ognuna delle diverse unità di istanze Large di HANA distribuite nelle due aree. Come risulta evidente dall'immagine precedente, si presuppone che siano configurate due connessioni ExpressRoute dall'infrastruttura locale alle due aree di Azure. Questa configurazione è consigliata per il ripristino di emergenza.

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.



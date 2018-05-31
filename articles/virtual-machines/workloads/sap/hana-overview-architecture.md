---
title: Panoramica e architettura di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Panoramica dell'architettura della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/02/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e3342f3057917202d81359a27accf47ba288b128
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077624"
---
# <a name="sap-hana-large-instances-overview-and-architecture-on-azure"></a>Panoramica e architettura di SAP HANA (istanze Large) in Azure

## <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (istanze Large)

SAP HANA in Azure (istanze Large) è una soluzione esclusiva per Azure. Oltre a rendere disponibili macchine virtuali (VM) per distribuire ed eseguire SAP HANA, Azure offre la possibilità di eseguire e distribuire SAP HANA su server bare metal dedicati per i clienti. La soluzione SAP HANA in Azure (istanze Large) si basa su hardware bare metal host/server non condiviso che viene assegnato ai clienti. L'hardware del server è incorporato in stamp più grandi contenenti l'infrastruttura di calcolo/server, di rete e di archiviazione. L'intera infrastruttura è dotata di certificazione HANA TDI (HANA Tailored Data Center Integration). SAP HANA in Azure (istanze Large) offre diversi SKU o dimensioni per i server, da unità con 72 CPU e 768 GB di memoria a unità con 960 CPU e 20 TB di memoria.

L'isolamento dei clienti nel modulo dell'infrastruttura viene realizzato in base a tenant, nel modo seguente:

- **Rete**: isolamento dei clienti nello stack dell'infrastruttura tramite rete virtuali per ogni tenant assegnato al cliente. Un tenant viene assegnato a un singolo cliente. Un cliente può avere più tenant. L'isolamento dei tenant basato su rete impedisce le comunicazioni di rete tra i tenant a livello del modulo dell'infrastruttura, anche se i tenant appartengono allo stesso cliente.
- **Componenti di archiviazione**: isolamento tramite macchine virtuali di archiviazione a cui sono assegnati volumi di archiviazione. I volumi di archiviazione possono essere assegnati a una sola macchina virtuale di archiviazione. Una macchina virtuale di archiviazione viene assegnata esclusivamente a un solo tenant nello stack dell'infrastruttura con certificazione SAP HANA TDI. I volumi di archiviazione assegnati a una macchina virtuale di archiviazione sono pertanto accessibili solo in uno specifico tenant correlato e non sono visibili tra i diversi tenant distribuiti.
- **Server o host**: un'unità server o host non viene condivisa tra clienti o tenant. Un server o un host distribuito a un cliente è un'unità di calcolo bare metal atomica assegnata a un solo tenant. *Non* vengono usati partizionamenti hardware o software con i quali un cliente potrebbe trovarsi a condividere un host o un server con un altro cliente. I volumi di archiviazione assegnati alla macchina virtuale di archiviazione del tenant specifico vengono montati in tale server. A un tenant può essere assegnata in modo esclusivo una o più unità server di SKU diversi.
- In un modulo dell'infrastruttura SAP HANA in Azure (istanze Large) più tenant diversi vengono distribuiti e isolati l'uno dall'altro in base a concetti definiti a livello di rete, archiviazione e calcolo. 


Queste unità server bare metal sono supportate solo per l'esecuzione di SAP HANA. Il livello applicazione SAP o il livello middleware dei carichi di lavoro viene eseguito nelle macchine virtuali. I moduli dell'infrastruttura che eseguono le unità SAP HANA in Azure (istanze Large) sono connessi ai backbone dei servizi di rete di Azure. In questo modo, viene fornita connettività a bassa latenza tra le unità SAP HANA in Azure (istanze Large) e le macchine virtuali.

Questo è uno dei documenti dedicati a SAP HANA in Azure (istanze Large) che presenta l'architettura di base, le responsabilità, i servizi offerti e, a livello generale, le funzionalità della soluzione. Per la maggior parte delle altre aree, ad esempio rete e connettività, sono disponibili altri quattro documenti di approfondimento. La documentazione di SAP HANA in Azure (istanze Large) non tratta gli aspetti relativi all'installazione di SAP NetWeaver o alle distribuzioni di SAP NetWeaver nelle VM. SAP NetWeaver in Azure viene trattato in una documentazione separata disponibile nello stesso contenitore di documenti Azure. 


I vari documenti della guida per HANA (istanza di grandi dimensioni) trattano le aree seguenti:

- [Panoramica e architettura di SAP HANA in Azure (istanze Large)](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Installare e configurare SAP HANA (istanze Large) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Risoluzione dei problemi e monitoraggio di SAP HANA in Azure (istanze Large)](troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Configurazione della disponibilità elevata in SUSE con STONITH](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/ha-setup-with-stonith)
- [Backup e ripristino del sistema operativo per SKU di tipo II](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/os-backup-type-ii-skus)

## <a name="definitions"></a>Definizioni

Nella guida all'architettura e alla distribuzione vengono comunemente usati alcuni termini. Tenere presenti i termini seguenti e i relativi significati.

- **IaaS**: (Infrastructure as a Service) infrastruttura distribuita come servizio.
- **PaaS**: (Platform as a Service) piattaforma distribuita come servizio.
- **SaaS**: (Software as a Service) software come servizio.
- **Componente SAP**: singola applicazione SAP, ad esempio ECC (ERP Central Component), BW (Business Warehouse), Solution Manager o EP (Enterprise Portal). I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
- **Ambiente SAP**: raggruppamento logico di uno o più componenti SAP per l'esecuzione di una funzione aziendale, ad esempio sviluppo, controllo della qualità, formazione, ripristino di emergenza o produzione.
- **Panorama applicativo SAP**: fa riferimento a tutti gli asset SAP nel panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
- **Sistema SAP**: combinazione del livello DBMS e del livello applicazione, ad esempio di un sistema di sviluppo SAP ERP, un sistema di test SAP BW e un sistema di produzione SAP CRM. Le distribuzioni di Azure non supportano la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve essere distribuito o in locale o in Azure. I diversi sistemi di un panorama applicativo SAP possono essere distribuiti in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale. Nel caso di SAP HANA in Azure (istanze Large), è previsto che il livello applicazione SAP dei sistemi SAP sia ospitato in VM e l'istanza di SAP HANA correlata sia ospitata in un'unità nel modulo di SAP HANA in Azure (istanze Large).
- **Modulo per istanze Large**: stack dell'infrastruttura hardware con certificazione SAP HANA TDI, dedicato all'esecuzione di istanze di SAP HANA in Azure.
- **SAP HANA in Azure (istanze Large)**: nome ufficiale dell'offerta in Azure per l'esecuzione di istanze di HANA in hardware con certificazione SAP HANA TDI distribuito in moduli per istanze Large in diverse aree di Azure. Il termine *istanze Large di HANA* correlato è la forma abbreviata di *SAP HANA in Azure (istanze Large)* prevalentemente usata in questa guida tecnica alla distribuzione.
- **Cross-premise**: indica uno scenario in cui le VM vengono distribuite a una sottoscrizione di Azure con connettività da sito a sito, multisito o Azure ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure, questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Azure Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso agli asset di Azure delle sottoscrizioni di Azure. Con questa estensione, le VM possono far parte del dominio locale. 

   Gli utenti del dominio locale possono accedere ai server ed eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra le VM distribuite in locale e quelle distribuite in Azure sono possibili. Questo è lo scenario tipico in cui viene distribuita la maggior parte degli asset SAP. Per altre informazioni, vedere [Pianificazione e progettazione per il gateway VPN](../../../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [Creare una connessione da sito a sito nel portale di Azure](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
- **Tenant**: un cliente distribuito nel modulo per istanze Large di HANA viene isolato in un *tenant*. Un tenant è isolato a livello di rete, archiviazione e calcolo dagli altri tenant, Le unità di archiviazione e calcolo assegnate ai diversi tenant non possono vedersi o comunicare tra loro a livello del modulo per istanze Large di HANA. Un cliente può scegliere di avere distribuzioni in tenant diversi. Neppure in questo caso i tenant possono comunicare tra di essi a livello di stamp di istanze Large di HANA.
- **Categoria SKU**: per le istanze Large di HANA sono disponibili le due categorie di SKU seguenti:
    - **Classe di tipo I**: S72, S72m, S144, S144m, S192 e S192m
    - **Classe di tipo II**: S384, S384m, S384xm, S576m, S768m e S960m


In merito alla distribuzione di un carico di lavoro SAP nel cloud è disponibile un'ampia gamma di risorse aggiuntive. Se si pianifica una distribuzione di SAP HANA in Azure è necessario essere utenti esperti e conoscere i principi dell'infrastruttura IaaS di Azure e la distribuzione dei carichi di lavoro SAP in tale infrastruttura. Prima di continuare, vedere [Uso di soluzioni SAP nelle macchine virtuali di Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per altre informazioni. 

## <a name="certification"></a>Certificazione

Oltre alla certificazione NetWeaver, SAP richiede una particolare certificazione per il supporto di SAP HANA in determinate infrastrutture, ad esempio IaaS di Azure.

La nota principale di SAP su NetWeaver, e in un certo senso anche la certificazione di SAP HANA, è [SAP Note #1928533 - SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure).

Anche [SAP Note #2316233 - SAP HANA on Microsoft Azure (Large Instances)](https://launchpad.support.sap.com/#/notes/2316233/E) (Nota di supporto SAP 2316233 - SAP HANA in Microsoft Azure, istanze Large) è uno strumento utile. Descrive la soluzione illustrata in questa guida. È inoltre supportata l'esecuzione di SAP HANA nel tipo di VM GS5 di Azure. Informazioni su questo scenario sono pubblicate sul [sito Web di SAP](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html).

La soluzione SAP HANA in Azure (istanze Large) a cui si fa riferimento nella nota SAP 2316233 fornisce ai clienti di Microsoft e SAP la possibilità di distribuire in Azure carichi di lavoro di grandi dimensioni di SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA o altri carichi di lavoro di SAP HANA. La soluzione è basata sul modulo hardware dedicato con certificazione per SAP HANA ([SAP HANA Tailored Data Center Integration - TDI](https://scn.sap.com/docs/DOC-63140)). Se si esegue una soluzione configurata per SAP HANA TDI, tutte le applicazioni basate su SAP HANA, come SAP Business Suite in SAP HANA, SAP BW in SAP HANA, S4/HANA e BW4/HANA, funzioneranno nell'infrastruttura hardware.

Rispetto all'esecuzione di SAP HANA nelle VM, questa soluzione presenta un vantaggio poiché consente di sfruttare volumi di memoria molto più grandi. Per abilitare questa soluzione, è necessario comprendere gli aspetti fondamentali seguenti:

- Il livello applicazione SAP e le applicazioni non SAP vengono eseguiti nelle VM ospitate nei consueti moduli hardware di Azure.
- L'infrastruttura, le distribuzioni di applicazioni e i data center locali dei clienti sono connessi alla piattaforma cloud tramite ExpressRoute (scelta consigliata) o una rete privata virtuale (VPN). Anche Active Directory e DNS vengono estesi in Azure.
- L'istanza del database SAP HANA per il carico di lavoro HANA viene eseguita su SAP HANA in Azure (istanze di grandi dimensioni). Il modulo per istanze Large è connesso alla rete di Azure. In questo modo, il software in esecuzione nelle VM può interagire con l'istanza di HANA in esecuzione in istanze Large di HANA.
- L'hardware di SAP HANA in Azure (istanze Large) è dedicato e viene fornito in un'infrastruttura IaaS in cui è preinstallato SUSE Linux Enterprise Server o Red Hat Enterprise Linux. Come nel caso delle macchine virtuali, gli aggiornamenti e la manutenzione del sistema operativo sono responsabilità del cliente.
- L'installazione di HANA o di eventuali componenti aggiuntivi necessari per l'esecuzione di SAP HANA nelle unità di istanze Large di HANA è responsabilità del cliente, così come tutte le operazioni e le attività di amministrazione di SAP HANA in Azure.
- Oltre alle soluzioni qui descritte, è possibile installare altri componenti nella sottoscrizione di Azure che si connette a SAP HANA in Azure (istanze di grandi dimensioni). Ad esempio, i componenti che consentono la comunicazione o la connessione diretta con il database SAP HANA, come jump server, server RDP, SAP HANA Studio, SAP Data Services per gli scenari SAP BI o soluzioni di monitoraggio di rete.
- Come in Azure, le istanze Large di HANA offrono il supporto di funzionalità per la disponibilità elevata e il ripristino di emergenza.

## <a name="architecture"></a>Architecture

In generale, nella soluzione SAP HANA in Azure (istanze Large) il livello applicazione SAP si trova nelle VM. Il livello database si trova nell'hardware configurato per SAP TDI, a sua volta collocato in un modulo per istanze Large nella stessa area di Azure connessa a IaaS di Azure.

> [!NOTE]
> Distribuire il livello applicazione SAP nella stessa area di Azure del livello DBMS di SAP. Questa regola è ben documentata nelle informazioni pubblicate relative ai carichi di lavoro SAP in Azure. 

L'architettura complessiva di SAP HANA in Azure (istanze Large) offre una configurazione hardware con certificazione SAP TDI che consiste in un server non virtualizzato, bare metal e a elevate prestazioni per il database SAP HANA. Offre inoltre la capacità e la flessibilità di Azure per ridimensionare le risorse per il livello applicazione SAP in base alle esigenze.

![Panoramica dell'architettura di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image1-architecture.png)

L'architettura illustrata è suddivisa in tre sezioni.

- **Sezione destra**: mostra un'infrastruttura locale che esegue diverse applicazioni nei data center in modo che gli utenti finali possano accedere ad applicazioni line-of-business, come SAP. In teoria, questa infrastruttura locale viene connessa ad Azure con [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Sezione centrale**: mostra l'infrastruttura IaaS di Azure e, in questo scenario, l'uso di VM per l'hosting di SAP o di altre applicazioni che usano SAP HANA come sistema DBMS. Le istanze di HANA più piccole che funzionano con la memoria delle VM vengono distribuite nelle VM con il relativo livello applicazione. Per altre informazioni sulle macchine virtuali, vedere [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/).

   I servizi di rete di Azure vengono usati per raggruppare i sistemi SAP insieme ad altre applicazioni in reti virtuali che si connettono ai sistemi locali e a SAP HANA in Azure (istanze Large).

   Per le applicazioni e i database SAP NetWeaver che supportano l'esecuzione in Azure, vedere [SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure). Per informazioni su come distribuire soluzioni SAP in Azure, vedere:

  -  [Usare SAP nelle macchine virtuali Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Usare soluzioni SAP nelle macchine virtuali di Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Sezione sinistra**: mostra l'hardware con certificazione SAP HANA TDI nel modulo per istanze Large in Azure. Le unità di istanze Large di HANA sono connesse alle reti virtuali della sottoscrizione con la stessa tecnologia usata per la connettività dall'ambiente locale ad Azure.

Il modulo per istanze di grandi dimensioni di Azure include i componenti seguenti:

- **Calcolo**: server basati su processori Intel Xeon E7-8890v3 o Intel Xeon E7-8890v4 che garantiscono la capacità di calcolo necessaria e sono certificati per SAP HANA.
- **Rete**: un'infrastruttura di rete unificata ad alta velocità che collega i componenti di calcolo, archiviazione e LAN.
- **Archiviazione**: un'infrastruttura di archiviazione a cui si accede tramite un'infrastruttura di rete unificata. La capacità di archiviazione fornita dipende dalla specifica configurazione di SAP HANA in Azure (istanze Large) distribuita. Maggiore capacità di archiviazione è disponibile a un costo mensile aggiuntivo.

Nell'infrastruttura multi-tenant del modulo per istanze di grandi dimensioni i clienti vengono distribuiti come tenant isolati. Al momento della distribuzione del tenant si specifica una sottoscrizione di Azure nell'ambito della propria iscrizione ad Azure. Questa è la sottoscrizione a cui vengono fatturati i costi delle istanze Large di HANA. Questi tenant hanno una relazione 1:1 con la sottoscrizione di Azure. Per una rete, è possibile accedere a un'unità di istanze Large di HANA distribuita in un singolo tenant all'interno di un'area di Azure da diverse reti virtuali appartenenti a diverse sottoscrizioni di Azure. Tali sottoscrizioni devono appartenere alla stessa iscrizione ad Azure. 

Analogamente alle VM, SAP HANA in Azure (istanze Large) è disponibile in più aree di Azure. Per offrire funzionalità di ripristino di emergenza, è possibile fornire il consenso esplicito. I diversi moduli per istanze Large in un'area geopolitica sono collegati tra loro. Ad esempio, i moduli per istanze Large di HANA in Stati Uniti occidentali e Stati Uniti orientali sono connessi tramite un collegamento di rete dedicato ai fini della replica per il ripristino di emergenza. 

Così come è possibile scegliere tra diversi tipi di VM con Macchine virtuali di Azure, è possibile scegliere tra diversi SKU di istanze Large di HANA progettati per diversi tipi di carichi di lavoro di SAP HANA. SAP applica rapporti tra memoria e socket del processore per diversi carichi di lavoro in base alle generazioni del processore Intel. La tabella seguente illustra i tipi di SKU disponibili.

A partire da luglio 2017, SAP HANA in Azure (istanze Large) è disponibile in diverse configurazioni nelle aree di Azure Stati Uniti occidentali, Stati Uniti orientali, Australia orientale, Australia sud-orientale, Europa occidentale ed Europa settentrionale.

| Soluzione SAP | CPU | Memoria | Archiviazione | Disponibilità |
| --- | --- | --- | --- | --- |
| Ottimizzata per OLAP: SAP BW, BW/4HANA<br /> o SAP HANA per carico di lavoro OLAP generico | SAP HANA in Azure S72<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  768 GB |  3 TB | Disponibile |
| --- | SAP HANA in Azure S144<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  1,5 TB |  6 TB | Non più disponibile |
| --- | SAP HANA in Azure S192<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU |  2 TB |  8 TB | Disponibile |
| --- | SAP HANA in Azure S384<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  4 TB |  16 TB | Disponibile |
| Ottimizzata per OLTP: SAP Business Suite<br /> in SAP HANA o S/4HANA (OLTP),<br /> OLTP generico | SAP HANA in Azure S72m<br /> – 2 x processore Intel® Xeon® E7-8890 v3<br /> 36 core CPU e 72 thread CPU |  1,5 TB |  6 TB | Disponibile |
|---| SAP HANA in Azure S144m<br /> – 4 x processore Intel® Xeon® E7-8890 v3<br /> 72 core CPU e 144 thread CPU |  3 TB |  12 TB | Non più disponibile |
|---| SAP HANA in Azure S192m<br /> – 4 x processore Intel® Xeon® E7-8890 v4<br /> 96 core CPU e 192 thread CPU  |  4 TB |  16 TB | Disponibile |
|---| SAP HANA in Azure S384m<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  6 TB |  18 TB | Disponibile |
|---| SAP HANA in Azure S384xm<br /> – 8 x processore Intel® Xeon® E7-8890 v4<br /> 192 core CPU e 384 thread CPU |  8 TB |  22 TB |  Disponibile |
|---| SAP HANA in Azure S576m<br /> – 12 x processore Intel® Xeon® E7-8890 v4<br /> 288 core CPU e 576 thread CPU |  12 TB |  28 TB | Disponibile |
|---| SAP HANA in Azure S768m<br /> – 16 x processore Intel® Xeon® E7-8890 v4<br /> 384 core CPU e 768 thread CPU |  16 TB |  36 TB | Disponibile |
|---| SAP HANA in Azure S960m<br /> – 20 x processore Intel® Xeon® E7-8890 v4<br /> 480 core CPU e 960 thread CPU |  20 TB |  46 TB | Disponibile |

- Core CPU: somma di core CPU senza hyperthreading nella somma dei processori dell'unità server.
- Thread CPU: somma di thread di calcolo forniti da core CPU con hyperthreading nella somma dei processori dell'unità server. Per impostazione predefinita, tutte le unità sono configurate per l'uso di Hyper-Threading Technology.


Le configurazioni specifiche scelte dipendono dal carico di lavoro, dalle risorse della CPU e dalla memoria desiderata. È possibile che il carico di lavoro OLTP sfrutti gli SKU ottimizzati per il carico di lavoro OLAP. 

La base hardware per tutte le offerte è dotata di certificazione SAP HANA TDI. Gli SKU sono suddivisi in due diverse classi di hardware:

- La "classe di tipo I" include gli SKU S72, S72m, S144, S144m, S192 e S192m.
- La "classe di tipo II" include gli SKU S384, S384m, S384xm, S576m, S768m e S960m.

Un intero modulo per istanze Large di HANA non viene allocato esclusivamente per l'uso di un singolo cliente. Ciò vale anche per i rack delle risorse di calcolo e archiviazione connesse tramite un'infrastruttura di rete distribuita in Azure. Analogamente ad Azure, l'infrastruttura di istanze Large di HANA distribuisce diversi &quot;tenant&quot; dei clienti, isolati l'uno dall'altro, nei tre livelli seguenti:

- **Rete**: isolamento tramite reti virtuali nel modulo per istanze Large di HANA.
- **Archiviazione**: isolamento tramite macchine virtuali di archiviazione a cui sono assegnati volumi di archiviazione e isolamento dei volumi di archiviazione tra i tenant.
- **Calcolo**: assegnazione dedicata di unità server a un singolo tenant. Nessun partizionamento hardware o software delle unità server. Nessuna condivisione di una singola unità server o host tra i tenant. 

Le distribuzioni di unità di istanze Large di HANA tra tenant diversi non risultano visibili l'una all'altra. Le unità di istanze Large di HANA distribuite in tenant diversi non possono comunicare direttamente tra loro a livello del modulo per istanze Large di HANA. Questa comunicazione è possibile solo per le unità di istanze Large di HANA all'interno di un solo tenant.

A un tenant distribuito nel modulo per istanze Large viene assegnata una sola sottoscrizione di Azure ai fini della fatturazione. A livello di rete, tuttavia, il tenant è accessibile dalle reti virtuali di altre sottoscrizioni di Azure nell'ambito della stessa iscrizione ad Azure. Se si esegue la distribuzione con un'altra sottoscrizione di Azure nella stessa area di Azure, si può anche scegliere di richiedere un tenant per istanze Large di HANA separato.

Vi sono differenze significative tra l'esecuzione di SAP HANA nelle istanze Large di HANA e quella nelle VM distribuite in Azure:

- Non esiste alcun livello di virtualizzazione per SAP HANA in Azure (istanze di grandi dimensioni). Si ottengono le prestazioni dell'hardware bare metal sottostante.
- A differenza di Azure, il server di SAP HANA in Azure (istanze di grandi dimensioni) è dedicato a un cliente specifico. Un'unità server, o host, non può essere in alcun modo partizionata a livello di hardware o software. Di conseguenza, un'unità di istanze Large di HANA viene usata come se fosse assegnata per intero a un tenant e con il tenant assegnato al singolo cliente. Un riavvio o un arresto del server non comporta automaticamente la distribuzione del sistema operativo e di SAP HANA in un altro server. Per gli SKU della classe di tipo I, l'unica eccezione è data dal caso in cui vengono riscontrati problemi in un server ed è necessario eseguire la ridistribuzione in un altro server.
- A differenza di Azure, in cui i tipi di processore host vengono selezionati per il miglior rapporto prezzo/prestazioni, per SAP HANA in Azure (istanze Large) vengono scelti i tipi di processore con le prestazioni più elevate della linea di processori Intel E7v3 ed E7v4.


### <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Eseguire più istanze di SAP HANA in un'unità di istanze Large di HANA
Nelle unità di istanze Large di HANA è possibile ospitare più istanze attive di SAP HANA. Per offrire le funzionalità degli snapshot di archiviazione e del ripristino di emergenza, una configurazione di questo tipo richiede un set di volumi per istanza. Attualmente, le unità di istanze Large di HANA possono essere suddivise come segue:

- **S72, S72m, S144 e S192**: con incrementi di 256 GB e 256 GB come unità iniziale più piccola. È possibile combinare incrementi diversi, ad esempio 256 GB e 512 GB, fino al massimo della memoria dell'unità.
- **S144m e S192m**: con incrementi di 256 GB e 512 GB come unità più piccola. È possibile combinare incrementi diversi, ad esempio 512 GB e 768 GB, fino al massimo della memoria dell'unità.
- **Classe di tipo II**: con incrementi di 512 GB e 2 TB come unità iniziale più piccola. È possibile combinare incrementi diversi, ad esempio 512 GB, 1 TB e 1,5 TB, fino al massimo della memoria dell'unità.

Di seguito sono riportati alcuni esempi di esecuzione di più istanze di SAP HANA.

| SKU | Dimensioni memoria | Dimensioni della risorsa di archiviazione | Dimensioni con più database |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 istanza di HANA da 768 GB<br /> o 1 istanza da 512 GB + 1 istanza da 256 GB<br /> o 3 istanze da 256 GB | 
| S72m | 1,5 TB | 6 TB | 3 istanze di HANA da 512 GB<br />o 1 istanza da 512 GB + 1 istanza da 1 TB<br />o 6 istanze da 256 GB<br />o 1 istanza da 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 istanze da 512 GB<br />o 4 istanze da 1 TB<br />o 4 istanze da 512 GB + 2 istanze da 1 TB<br />o 4 istanze da 768 GB + 2 istanze da 512 GB<br />o 1 istanza da 4 TB |
| S384xm | 8 TB | 22 TB | 4 istanze da 2 TB<br />o 2 istanze da 4 TB<br />o 2 istanze da 3 TB + 1 istanza da 2 TB<br />o 2 istanze da 2,5 TB + 1 istanza da 3 TB<br />o 1 istanza da 8 TB |


Esistono anche altre varianti. 

### <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Usare nodi di estensione e suddivisione in livelli dei dati di SAP HANA
SAP supporta un modello di suddivisione in livelli dei dati per SAP BW di diverse versioni di SAP NetWeaver e SAP BW/4HANA. Per altre informazioni sul modello di suddivisione in livelli dei dati, vedere il documento di SAP [SAP BW/4HANA and SAP BW on HANA with SAP HANA extension nodes](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#) (SAP BW/4HANA e SAP BW su HANA con nodi di estensione di SAP HANA).
Con le istanze Large di HANA è possibile usare la configurazione option-1 dei nodi di estensione di SAP HANA, come descritto in dettaglio nei documenti delle domande frequenti e del blog di SAP. Le configurazioni option-2 possono essere impostate con le SKU delle istanze Large di HANA seguenti: S72m, S192, S192m, S384 e S384m. 

Quando si esamina la documentazione, il vantaggio può non essere immediatamente visibile. Se tuttavia si leggono le linee guida del dimensionamento di SAP, il vantaggio offerto dai nodi di estensione option-1 e option-2 di SAP HANA è evidente. Ecco alcuni esempi:

- Le linee guida del dimensionamento di SAP HANA richiedono in genere il doppio del volume di dati per la memoria. Quando si esegue l'istanza di SAP HANA con hot data, solo il 50% o una percentuale inferiore della memoria è occupata dai dati. Idealmente, il resto della memoria viene mantenuto per il funzionamento di SAP HANA.
- Questo significa che in un'unità S192 di istanza Large di HANA con 2 TB di memoria che esegue un database SAP BW, il volume di dati è di 1 TB.
- Se si usa un nodo di estensione di SAP HANA aggiuntivo di tipo option-1, anche uno SKU S192 di istanze Large di HANA, si ottiene una capacità aggiuntiva di 2 TB per il volume di dati. Nella configurazione option-2 si ottengono 4 TB aggiuntivi per il volume di warm data. Rispetto al nodo di tipo hot, nel nodo di estensione di tipo warm può essere usata la capacità di memoria completa per l'archiviazione dei dati per la configurazione option-1. Una capacità doppia di memoria può essere usata per il volume di dati nella configurazione del nodo di estensione option-2 di SAP HANA.
- Si ottengono quindi una capacità di 3 TB per i dati, con un rapporto hot/warm di 1:2, nella configurazione del nodo di estensione option-1 e una capacità di 5 TB per i dati, con un rapporto hot/warm di 1:4, nella configurazione del nodo di estensione option-2.

Maggiore sarà il volume di dati rispetto alla memoria, più alte saranno le probabilità che i warm data desiderati si trovino in una risorsa di archiviazione su disco.


## <a name="operations-model-and-responsibilities"></a>Responsabilità e modello operativo

Il servizio fornito con SAP HANA in Azure (istanze di grandi dimensioni) è allineato ai servizi IaaS di Azure. Il cliente ottiene un'istanza Large di HANA con un sistema operativo installato ottimizzato per SAP HANA. Come per le VM IaaS di Azure, quasi tutte le attività di protezione avanzata del sistema operativo, installazione di software aggiuntivo, installazione di HANA, funzionamento e aggiornamento del sistema operativo e di HANA sono responsabilità del cliente. Microsoft non impone al cliente aggiornamenti del sistema operativo o di HANA.

![Responsabilità di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image2-responsibilities.png)

Come illustrato nel diagramma, SAP HANA in Azure (istanze Large) è un'offerta IaaS multi-tenant. La ripartizione di gran parte delle responsabilità è al limite tra sistema operativo e infrastruttura. Microsoft si occupa di tutti gli aspetti del servizio al di sotto della linea relativa al sistema operativo. Il cliente è responsabile degli aspetti al di sopra di tale linea, incluso il sistema operativo. È quindi possibile continuare a usare i metodi adottati più di recente nel sistema locale per la conformità, la sicurezza, la gestione delle applicazioni, le operazioni di base e la gestione del sistema operativo. I sistemi vengono visualizzati come facenti parte della propria rete.

Questo servizio è ottimizzato per SAP HANA. Vi sono pertanto aree in cui il cliente deve collaborare con Microsoft per usare le funzionalità dell'infrastruttura sottostante per ottenere risultati ottimali.

L'elenco seguente fornisce informazioni dettagliate su ogni livello e indica le responsabilità del cliente:

**Rete**: tutte le reti interne del modulo per istanze Large su cui è in esecuzione SAP HANA. Le responsabilità del cliente includono l'accesso alle risorse di archiviazione, la connettività tra le istanze per la scalabilità orizzontale e altre funzioni, la connettività con il panorama applicativo e la connettività con Azure, dove il livello applicazione SAP è ospitato nelle VM. Tra le responsabilità rientra anche la connettività WAN tra data center di Azure per la replica a scopo di ripristino di emergenza. Tutte le reti vengono partizionate dal tenant e configurate con criteri di qualità del servizio.

**Archiviazione**: lo spazio di archiviazione partizionato virtualizzato per tutti i volumi necessari per i server di SAP HANA e per gli snapshot. 

**Server**: i server fisici dedicati per l'esecuzione dei database di SAP HANA assegnati ai tenant. I server di SKU Classe di tipo I sono indipendenti dall'hardware. La configurazione di questi tipi di server viene raccolta e mantenuta in profili che possono essere spostati da un hardware fisico a un altro. Queste operazioni (manuali) di spostamento di un profilo sono in parte paragonabili ad attività di correzione dei servizi di Azure. I server di SKU della classe di tipo II non offrono tale funzionalità.

**SDDC**: il software usato per gestire i data center come entità definite da software. Consente a Microsoft di creare pool delle risorse ai fini della scalabilità, della disponibilità e delle prestazioni.

**Sistema operativo**: il sistema operativo selezionato (SUSE Linux o Red Hat Linux) in esecuzione sui server. Le immagini del sistema operativo disponibili sono state offerte dal singolo fornitore di Linux a Microsoft per l'esecuzione di SAP HANA. È necessario disporre di una sottoscrizione con il fornitore di Linux per la specifica immagine ottimizzata per SAP HANA. Il cliente è responsabile della registrazione delle immagini con il fornitore del sistema operativo. 

Dal momento del trasferimento da Microsoft, l'utente è responsabile di eventuali altre applicazioni di patch del sistema operativo Linux. L'applicazione di patch include i pacchetti aggiuntivi che possono essere necessari per una corretta installazione di SAP HANA e che non sono stati inseriti dallo specifico fornitore di Linux nelle immagini del sistema operativo ottimizzate per SAP HANA. Per altre informazioni, vedere la documentazione e le note SAP relative all'installazione di SAP HANA. 

Il cliente è responsabile dell'applicazione di patch per problemi di malfunzionamento o per l'ottimizzazione del sistema operativo, per i driver relativi all'hardware del server specifico e anche per la sicurezza o le funzionalità del sistema operativo. 

Le responsabilità del cliente includono inoltre il monitoraggio e la pianificazione della capacità in relazione a:

- Utilizzo delle risorse della CPU
- Utilizzo della memoria
- Spazio disponibile, operazioni di I/O al secondo e latenza per i volumi dei dischi
- Traffico del volume di rete tra le istanze Large di HANA e il livello applicazione SAP

L'infrastruttura sottostante delle istanze Large di HANA fornisce funzionalità per il backup e il ripristino del volume del sistema operativo. Anche l'uso di queste funzionalità è responsabilità del cliente.

**Middleware**: principalmente l'istanza di SAP HANA. L'amministrazione, le operazioni e il monitoraggio sono responsabilità del cliente. Sono disponibili funzionalità che consentono di usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Queste funzionalità sono offerte dall'infrastruttura. Tra le responsabilità del cliente rientra anche la progettazione della disponibilità elevata o del ripristino di emergenza con queste funzionalità, il relativo uso e il monitoraggio della corretta esecuzione degli snapshot di archiviazione.

**Dati**: i dati gestiti da SAP HANA e altri dati, ad esempio file di backup nei volumi o nelle condivisioni di file. Le responsabilità del cliente includono il monitoraggio dello spazio disponibile su disco e la gestione del contenuto nei volumi, nonché il monitoraggio della corretta esecuzione dei backup dei volumi dei dischi e degli snapshot di archiviazione. La corretta esecuzione della replica dei dati nei siti di ripristino di emergenza è responsabilità di Microsoft.

**Applicazioni**: le istanze di applicazioni SAP o, in caso di applicazioni non SAP, il relativo livello applicazione. Le responsabilità del cliente includono la distribuzione, l'amministrazione, l'esecuzione di operazioni e il monitoraggio di tali applicazioni. Il cliente è responsabile della pianificazione della capacità per l'utilizzo delle risorse della CPU, della memoria, delle risorse di archiviazione di Azure, della larghezza di banda di rete nelle reti virtuali e per l'utilizzo delle risorse dalle reti virtuali di Azure a SAP HANA in Azure (istanze Large).

**WAN**: le connessioni stabilite dall'ambiente locale alle distribuzioni di Azure per i carichi di lavoro. Tutti i clienti con istanze Large di HANA usano Azure ExpressRoute per la connettività. Questa connessione non fa parte della soluzione SAP HANA in Azure (istanze Large) e pertanto la relativa configurazione è responsabilità del cliente.

**Archivio**: può essere preferibile archiviare copie dei dati usando i metodi preferiti negli account di archiviazione. L'archiviazione comporta gestione, conformità, costi e operazioni. Il cliente è responsabile della generazione di copie di archivio e backup in Azure e della relativa archiviazione in base ai criteri di conformità.

Vedere [Contratto di Servizio per SAP HANA in istanze di grandi dimensioni di Azure](https://azure.microsoft.com/support/legal/sla/sap-hana-large/v1_0/).

## <a name="sizing"></a>Ridimensionamento

Il ridimensionamento per le istanze Large di HANA non è diverso dal ridimensionamento per HANA in generale. Per i sistemi esistenti e distribuiti che devono essere spostati da altri RDBMS ad HANA, SAP fornisce una serie di report che vengono eseguiti sui sistemi SAP esistenti. Se il database viene spostato in HANA, questi report controllano i dati e calcolano i requisiti di memoria per l'istanza di HANA. Per altre informazioni su come eseguire i report e su come ottenere le patch o le versioni più recenti, leggere le note SAP seguenti:

- [SAP Note #1793345 - Sizing for SAP Suite on HANA](https://launchpad.support.sap.com/#/notes/1793345) (Nota SAP 1793345: ridimensionamento della suite SAP in HANA)
- [SAP Note #1872170 - Suite on HANA and S/4 HANA sizing report](https://launchpad.support.sap.com/#/notes/1872170) (Nota SAP 1872170: report sul ridimensionamento della suite in HANA e S/4 HANA)
- [SAP Note #2121330 - FAQ: SAP BW on HANA sizing report](https://launchpad.support.sap.com/#/notes/2121330) (Nota SAP 2121330: domande frequenti: report sul ridimensionamento di SAP BW in HANA)
- [SAP Note #1736976 - Sizing report for BW on HANA](https://launchpad.support.sap.com/#/notes/1736976) (Nota SAP 1736976: report sul ridimensionamento per BW in HANA)
- [SAP Note #2296290 - New sizing report for BW on HANA](https://launchpad.support.sap.com/#/notes/2296290) (Nota SAP 2296290: nuovo report sul ridimensionamento per BW in HANA)

Per le implementazioni "vergini", è disponibile SAP Quick Sizer per calcolare i requisiti di memoria dell'implementazione del software SAP in HANA.

I requisiti di memoria per HANA aumentano in base al volume dei dati. È quindi opportuno conoscere l'utilizzo attuale della memoria per poter prevedere quello futuro. In base ai requisiti di memoria, è possibile associare la propria richiesta a uno degli SKU delle istanze Large di HANA.

## <a name="requirements"></a>Requisiti

Questo elenco include i requisiti per l'esecuzione di SAP HANA in Azure (istanze Large).

**Microsoft Azure**

- Una sottoscrizione di Azure che può essere collegata a SAP HANA in Azure (istanze di grandi dimensioni).
- Contratto di supporto tecnico Premier Microsoft. Per informazioni specifiche sull'esecuzione di SAP in Azure, vedere [SAP Support Note #2015553 - SAP on Microsoft Azure: Support prerequisites](https://launchpad.support.sap.com/#/notes/2015553) (Nota di supporto SAP 2015553 - SAP in Microsoft Azure: prerequisiti di supporto). Se si usano unità di istanze Large di HANA con 384 e più CPU, è necessario anche estendere il contratto di supporto tecnico Premier in modo da includere Azure Rapid Response.
- Conoscenza degli SKU delle istanze Large di HANA necessari dopo l'esecuzione di un esercizio di ridimensionamento con SAP.

**Connettività di rete**

- ExpressRoute tra l'ambiente locale e Azure: per la connessione del data center locale ad Azure assicurarsi di ordinare all'ISP una connessione di almeno 1 Gbps. 

**Sistema operativo**

- Licenze per SUSE Linux Enterprise Server 12 per applicazioni SAP.

   > [!NOTE] 
   > Il sistema operativo di Microsoft non è registrato con SUSE e non è connesso a un'istanza di Subscription Management Tool.

- SUSE Linux Subscription Management Tool distribuito in Azure in una VM. Con questo strumento, la soluzione SAP HANA in Azure (istanze Large) può essere registrata e aggiornata da SUSE. Non è disponibile l'accesso diretto a Internet dall'interno del data center delle istanze Large di HANA. 
- Licenze per Red Hat Enterprise Linux 6.7 o 7.2 per SAP HANA.

   > [!NOTE]
   > Il sistema operativo di Microsoft non è registrato con Red Hat e non è connesso a un'istanza di Red Hat Subscription Manager.

- Red Hat Subscription Manager distribuito in Azure in una VM. Con Red Hat Subscription Manager, la soluzione SAP HANA in Azure (istanze Large) può essere registrata e aggiornata da Red Hat. Non è disponibile l'accesso diretto a Internet dall'interno del tenant distribuito nel modulo per istanze Large in Azure.
- SAP richiede un contratto di supporto anche con il provider di Linux. Questo requisito non viene escluso per il fatto che viene usata la soluzione di istanze Large di HANA o che Linux viene eseguito in Azure. A differenza di alcune delle immagini della raccolta di Azure per Linux, la tariffa del servizio *non* è inclusa nell'offerta della soluzione di istanze Large di HANA. È responsabilità del cliente soddisfare i requisiti di SAP in relazione ai contratti di supporto con il distributore di Linux. 
   - Per SUSE Linux, esaminare i requisiti del contratto di supporto in [SAP Note #1984787 - SUSE Linux Enterprise Server 12: Installation notes](https://launchpad.support.sap.com/#/notes/1984787) (Nota SAP 1984787 - SUSE Linux Enterprise Server 12: note sull'installazione) e [SAP Note #1056161 - SUSE priority support for SAP applications](https://launchpad.support.sap.com/#/notes/1056161) (Nota SAP 1056161 - Supporto SUSE prioritario per applicazioni SAP).
   - Per Red Hat Linux, è necessario avere livelli di sottoscrizione corretti che includano supporto e aggiornamenti di servizio per i sistemi operativi delle istanze Large di HANA. Red Hat consiglia di ottenere una sottoscrizione Red Hat Enterprise Linux per [soluzioni di SAP] (https://access.redhat.com/solutions/3082481). 

Per la matrice di supporto delle diverse versioni di SAP HANA con le varie versioni di Linux, vedere la [nota SAP 2235581](https://launchpad.support.sap.com/#/notes/2235581).

Per la matrice di compatibilità del sistema operativo e le versioni del firmware/ driver HLI, fare riferimento a [OS Upgrade for HLI](os-upgrade-hana-large-instance.md) (Aggiornamento del sistema operativo per HLI).


**Database**

- Licenze e componenti di installazione software per SAP HANA (edizione Platform o Enterprise).

**Applicazioni**

- Licenze e componenti di installazione software per le applicazioni SAP che si connettono a SAP HANA e relativi contratti di supporto SAP.
- Licenze e componenti di installazione software per le applicazioni non SAP usate in relazione agli ambienti SAP HANA in Azure (istanze Large) e relativi contratti di supporto.

**Competenze**

- Esperienza e conoscenza di IaaS di Azure e dei relativi componenti.
- Esperienza e conoscenza della distribuzione di un carico di lavoro SAP in Azure.
- Personale certificato per l'installazione di SAP HANA.
- Competenze relative all'architettura SAP per la progettazione della disponibilità elevata e del ripristino di emergenza in SAP HANA.

**SAP**

- Si presuppone che il cliente sia un cliente SAP che ha stipulato un contratto di supporto con SAP.
- Soprattutto per le implementazioni di SKU della classe di tipo II delle istanze Large di HANA, consultare SAP in merito alle versioni di SAP HANA e alle possibili configurazioni in hardware di grandi dimensioni con scalabilità verticale.


## <a name="storage"></a>Archiviazione

Il layout di archiviazione per SAP HANA in Azure (istanze Large) viene configurato da SAP HANA nel modello di distribuzione classico in base alle linee guida consigliate da SAP, come illustrato nel white paper [SAP HANA storage requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) (Requisiti per le risorse di archiviazione di SAP HANA).

Nelle unità di istanze Large di HANA della classe di tipo I è disponibile un volume di archiviazione quattro volte superiore al volume di memoria. Per le unità di istanze Large di HANA della classe di tipo II, il volume di archiviazione non è di altre quattro volte superiore. Le unità sono dotate di un volume destinato all'archiviazione dei backup del log delle transazioni di HANA. Per altre informazioni, vedere [Come installare e configurare SAP HANA (istanze Large) in Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Per l'allocazione dello spazio di archiviazione, vedere la tabella seguente, che elenca la capacità approssimativa dei diversi volumi disponibili nelle diverse unità di istanze Large di HANA.

| SKU delle istanze Large di HANA | hana/data | hana/log | hana/shared | HANA/log/backup |
| --- | --- | --- | --- | --- |
| S72 | 1280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3328 GB | 768 GB |1280 GB | 768 GB |
| S192 | 4608 GB | 1024 GB | 1536 GB | 1024 GB |
| S192m | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384 | 11.520 GB | 1536 GB | 1792 GB | 1536 GB |
| S384m | 12.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S384xm | 16.000 GB | 2050 GB | 2050 GB | 2040 GB |
| S576m | 20.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S768m | 28.000 GB | 3100 GB | 2050 GB | 3100 GB |
| S960m | 36.000 GB | 4100 GB | 2050 GB | 4100 GB |


I volumi distribuiti effettivi possono variare in base alla distribuzione e allo strumento usato per visualizzare le dimensioni dei volumi.

In caso di suddivisione di uno SKU delle istanze Large di HANA, ecco alcuni esempi delle possibili dimensioni delle partizioni:

| Partizione di memoria in GB | hana/data | hana/log | hana/shared | HANA/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1.024 | 1792 GB | 640 GB | 1024 GB | 640 GB |
| 1.536 | 3328 GB | 768 GB | 1280 GB | 768 GB |


Queste dimensioni sono approssimative e possono variare leggermente in base alla distribuzione e agli strumenti usati per esaminare i volumi. Sono ipotizzabili anche altre dimensioni per le partizioni, ad esempio 2,5 TB. Queste dimensioni di archiviazione vengono calcolate con una formula simile a quella usata per le partizioni riportate sopra. Il termine "partizione" non significa che le risorse del sistema operativo, della memoria o della CPU sono in qualche modo partizionate. Indica le partizioni di archiviazione per le diverse istanze di HANA che si vorranno eventualmente distribuire in una singola unità di istanze Large di HANA. 

Se è necessario altro spazio di archiviazione, il cliente può acquistarne altro in unità da 1 TB. Questo spazio di archiviazione può essere aggiunto come un nuovo volume oppure essere usato per estendere uno o più dei volumi esistenti. Non è possibile ridurre le dimensioni dei volumi originariamente distribuiti, in gran parte documentati nelle tabelle precedenti, né modificare i nomi dei volumi o i nomi di montaggio. I volumi di archiviazione descritti in precedenza vengono collegati alle unità di istanze Large di HANA come volumi NFS4.

È possibile usare gli snapshot di archiviazione a scopo di backup/ripristino e ripristino di emergenza. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze Large) in Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="encryption-of-data-at-rest"></a>Crittografia dei dati inattivi
Le risorse di archiviazione usate per le istanze Large di HANA consentono una crittografia trasparente dei dati al momento dell'archiviazione sui dischi. Quando un'unità di istanze Large di HANA viene distribuita, è possibile abilitare questo tipo di crittografia. È anche possibile passare ai volumi crittografati in seguito alla distribuzione. Il passaggio dai volumi non crittografati a quelli crittografati è trasparente e non comporta tempi di inattività. 

Con gli SKU della classe di tipo I, il volume in cui è archiviato il LUN di avvio è crittografato. Nel caso degli SKU della classe di tipo II delle istanze Large di HANA, è necessario crittografare il LUN di avvio con i metodi del sistema operativo. Per altre informazioni, contattare il team di gestione dei servizi Microsoft.


## <a name="networking"></a>Rete

L'architettura dei servizi di rete di Azure è un componente fondamentale per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi SAP siano basati su SAP HANA e che il panorama applicativo SAP sia un ibrido con i componenti seguenti:

- Sistemi SAP distribuiti in locale. A causa delle dimensioni, attualmente questi sistemi non possono essere ospitati in Azure. Un esempio può essere un sistema SAP ERP di produzione che viene eseguito su SQL Server (come database) e richiede risorse di memoria o CPU superiori a quelle che possono essere disponibili nelle VM.
- Sistemi SAP basati su SAP HANA distribuiti in locale.
- Sistemi SAP distribuiti nelle VM. Questi sistemi possono essere istanze di sviluppo, test, sandbox o produzione per qualsiasi applicazione basata su SAP NetWeaver distribuibile in VM di Azure in base alle richieste di memoria e all'utilizzo delle risorse. Questi sistemi possono essere basati su database come SQL Server. Per altre informazioni, vedere [SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533/E) (Nota SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure) Possono inoltre essere basati su database come SAP HANA. Per altre informazioni, vedere [SAP HANA certified IaaS platforms](http://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Piattaforme IaaS certificate per SAP HANA).
- Server applicazioni SAP distribuiti in Azure (nelle VM) che utilizzano SAP HANA in Azure (istanze Large) in moduli per istanze Large in Azure.

Il caso tipico consiste in un panorama applicativo SAP ibrido con quattro o più diversi scenari di distribuzione. In molti casi, tuttavia, i clienti hanno un panorama applicativo SAP completo in esecuzione in Azure. Grazie alla potenza sempre maggiore delle VM, il numero di clienti che sposta tutte le soluzioni SAP in Azure è in aumento.

Le reti di Azure nel contesto dei sistemi SAP distribuiti in Azure non sono complicate e si basano sui principi seguenti:

- Le reti virtuali di Azure devono essere connesse al circuito di ExpressRoute che si connette a una rete locale.
- Un circuito ExpressRoute che connette l'ambiente locale ad Azure deve avere in genere una larghezza di banda di almeno 1 Gbps. Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM e per la connessione degli utenti locali ai sistemi di Azure.
- Per comunicare tra loro, tutti i sistemi SAP in Azure devono essere configurati in reti virtuali.
- Active Directory e DNS ospitati in locale vengono estesi dall'ambiente locale ad Azure tramite ExpressRoute.


> [!NOTE] 
> Dal punto di vista della fatturazione, una singola sottoscrizione di Azure può essere collegata a un solo tenant in un modulo per istanze Large in un'area specifica di Azure. Viceversa, un singolo tenant del modulo per istanze Large può essere collegato a una sola sottoscrizione di Azure. Questo requisito è coerente con quello di altri oggetti fatturabili in Azure.

Se si distribuisce SAP HANA in Azure (istanze Large) in diverse aree di Azure, viene distribuito un tenant separato nel modulo per istanze Large. È possibile eseguire entrambi nella stessa sottoscrizione di Azure purché queste istanze facciano parte dello stesso panorama applicativo SAP. 

> [!IMPORTANT] 
> Con SAP HANA in Azure (istanze Large) è supportata solo la distribuzione di Azure Resource Manager.

 

### <a name="additional-virtual-network-information"></a>Informazioni aggiuntive sulle reti virtuali

Per connettere una rete virtuale a ExpressRoute, è necessario creare un gateway di Azure. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Un gateway di Azure può essere usato con ExpressRoute per la connessione a un'infrastruttura all'esterno di Azure o a un modulo per istanze Large in Azure. Può inoltre essere usato anche per la connessione tra reti virtuali. Per altre informazioni, vedere [Configurare una connessione tra reti per Resource Manager usando PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È possibile connettere il gateway di Azure a un massimo di quattro diverse connessioni ExpressRoute a condizione che tali connessioni provengano da router Microsoft Enterprise Edge diversi. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> La velocità effettiva fornita da un gateway di Azure è diversa per i due casi d'uso. Per altre informazioni, vedere [Informazioni sul gateway VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La velocità effettiva massima che è possibile ottenere con un gateway di rete virtuale, usando una connessione ExpressRoute, è 10 Gbps. La copia di file tra una VM che si trova in una rete virtuale e un sistema locale (come singolo flusso di copia) non raggiunge la totale velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway di rete virtuale, usare più flussi. In alternativa, è necessario copiare file diversi in flussi paralleli di un singolo file.


### <a name="networking-architecture-for-hana-large-instance"></a>Architettura di rete per le istanze Large di HANA
L'architettura di rete per le istanze Large di HANA può essere suddivisa in quattro parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è costituita dal dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Nell'immagine seguente è riportata in basso a destra.
- Servizi di rete di Azure, come illustrati in precedenza, con reti virtuali, anche in questo caso con gateway. Questa parte è costituita da un'area che richiede una progettazione adatta ai propri requisiti in termini di applicazioni, sicurezza e conformità. L'eventuale uso di istanze Large di HANA è un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e di SKU del gateway di Azure. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Rete nelle istanze Large di HANA, in gran parte trasparente per il cliente.

![Rete virtuale connessa a SAP HANA in Azure (istanze Large) e in locale](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

L'uso di istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute e nemmeno sul requisito di una o più reti virtuali su cui vengono eseguite le VM che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

Le differenze rispetto alle distribuzioni SAP in Azure sono le seguenti:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali tramite un altro circuito ExpressRoute. Per separare le condizioni di carico, i collegamenti ExpressRoute dall'ambiente locale alle reti virtuali e i collegamenti tra le reti virtuali di Azure e le istanze Large di HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e le istanze Large di HANA è di diversa natura e prevede un'elevata quantità di richieste e burst di piccole dimensioni come i trasferimenti di dati (set di risultati) da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway di rete virtuale ha almeno due connessioni ExpressRoute. Entrambe condividono la larghezza di banda massima per i dati in ingresso del gateway di rete virtuale.

La latenza di rete rilevata tra le VM e le unità di istanze Large di HANA può essere superiore alla latenza di andata e ritorno tipica di una rete da VM a VM. A seconda dell'area di Azure, i valori misurati possono superare la latenza di andata e ritorno di 0,7 millisecondi classificata come inferiore alla media in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota SAP 1100926 - Domande frequenti sulle prestazioni di rete). Nonostante ciò, i clienti distribuiscono applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA con ottimi risultati. I clienti che hanno scelto questa distribuzione registrano miglioramenti significativi nell'esecuzione di applicazioni SAP in SAP HANA con unità di istanze Large di HANA. È importante testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure.
 
Per garantire una latenza di rete deterministica tra le VM e le istanze Large di HANA, la scelta dello SKU del gateway di rete virtuale è essenziale. A differenza dei modelli di traffico tra l'ambiente locale e le VM, il modello di traffico tra le VM e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma frequenti di richieste e volumi di dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per gli SKU della classe di tipo II delle istanze Large di HANA, l'uso dello SKU UltraPerformance come gateway di rete virtuale è obbligatorio.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze Large) sono supportati solo gli SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per gli SKU della classe di tipo II delle istanze Large di HANA, come gateway di rete virtuale è supportato solo lo SKU UltraPerformance.



### <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata in precedenza è connessa ad Azure tramite ExpressRoute. Il circuito ExpressRoute è connesso a sua volta a un router Enterprise Edge. Per altre informazioni, vedere [Panoramica tecnica di ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una volta stabilita la route, questa si connette al backbone di Azure e tutte le aree di Azure risultano accessibili.

> [!NOTE] 
> Per eseguire panorami applicativi SAP in Azure, connettersi al router Enterprise Edge più vicino all'area di Azure nel panorama applicativo SAP. I moduli per istanze Large in Azure sono connessi tramite dispositivi router Enterprise Edge dedicati per ridurre al minimo la latenza di rete tra VM in IaaS di Azure e moduli per istanze Large.

Il gateway di rete virtuale per le VM che ospitano le istanze dell'applicazione SAP è connesso al circuito ExpressRoute. La stessa rete virtuale è connessa a un router Enterprise Edge per la connessione a moduli per istanze Large.

Questo sistema è un esempio semplice di un sistema SAP singolo. Il livello applicazione SAP è ospitato in Azure. Il database SAP HANA viene eseguito su SAP HANA in Azure (istanze Large). Si presuppone che la larghezza di banda del gateway di rete virtuale con velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

### <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se vengono distribuiti più sistemi SAP o sistemi SAP di grandi dimensioni per connettersi a SAP HANA in Azure (istanze Large), la velocità effettiva del gateway di rete virtuale potrebbe diventare un collo di bottiglia. In tal caso, dividere i livelli applicazione in più reti virtuali. È anche possibile creare una rete virtuale speciale che si connette alle istanze Large di HANA in casi come questi:

- Esecuzione di backup direttamente da istanze Large di HANA a una VM in Azure che ospita le condivisioni NFS.
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

Usare una rete virtuale separata per ospitare le VM che gestiscono l'archiviazione. In questo modo è possibile evitare gli effetti del trasferimento di dati o file di grandi dimensioni dalle istanze Large di HANA ad Azure sul gateway di rete virtuale usato dalle VM che eseguono il livello applicazione SAP. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale separata per ogni sistema SAP distribuito anziché combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

 Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuire il livello applicazione SAP su più reti virtuali](./media/hana-overview-architecture/image4-networking-architecture.png)

La figura mostra la distribuzione del livello applicazione SAP, o dei componenti, su più reti virtuali di Azure. Questa configurazione ha comportato un inevitabile sovraccarico di latenza che si è verificato durante la comunicazione tra le applicazioni ospitate nelle reti virtuali. Per impostazione predefinita, in questa configurazione il traffico di rete tra VM in reti virtuali diverse viene instradato attraverso i router Enterprise Edge. A partire dal mese di settembre 2016, questo routing può essere ottimizzato. 

Per ottimizzare e ridurre la latenza di comunicazione tra due reti virtuali è necessario eseguire il peering delle reti virtuali nella stessa area. Questo metodo funziona anche se le reti virtuali si trovano in sottoscrizioni diverse. Con il peering delle rete virtuali, la comunicazione diretta tra VM in due diverse reti virtuali di Azure può avvenire tramite il backbone della rete di Azure. La latenza risulta simile a quella generata da VM presenti nella stessa rete virtuale. Il traffico indirizzato agli intervalli di indirizzi IP connessi tramite il gateway di rete virtuale di Azure viene instradato attraverso il singolo gateway della rete virtuale. 

Per altre informazioni sul peering delle reti virtuali, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


### <a name="routing-in-azure"></a>Routing in Azure

Per SAP HANA in Azure (istanze Large) sono importanti tre considerazioni sul routing di rete:

* SAP HANA in Azure (istanze Large) è accessibile solo tramite le VM e la connessione ExpressRoute dedicata, non direttamente dall'ambiente locale. L'accesso diretto dall'ambiente locale alle unità di istanze Large di HANA, come distribuite da Microsoft, non è consentito nell'immediato. Le restrizioni relative al routing temporaneo sono dovute all'attuale architettura di rete di Azure usata per le istanze Large di SAP HANA. Alcuni client di amministrazione e le applicazioni che richiedono l'accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA.

* Se si hanno unità di istanze Large di HANA distribuite in due diverse aree di Azure allo scopo di ripristino di emergenza, vengono applicate le stesse restrizioni relative al routing temporaneo. In altre parole, gli indirizzi IP di un'unità di istanze Large di HANA in un'area, ad esempio Stati Uniti occidentali, non vengono instradati a un'unità di istanze Large di HANA distribuita in un'altra area, ad esempio Stati Uniti orientali. Questa restrizione è indipendente dall'uso del peering di rete di Azure tra le aree o del collegamento incrociato dei circuiti ExpressRoute che connettono le unità di istanze Large di HANA alle reti virtuali. Per una rappresentazione grafica, vedere la figura nella sezione "Usare le unità di istanze Large di HANA in più aree". Questa restrizione, integrata nell'architettura distribuita, impedisce l'uso immediato della replica del sistema HANA come funzionalità di ripristino di emergenza.

* Alle unità SAP HANA in Azure (istanze Large) viene assegnato un indirizzo IP dall'intervallo di indirizzi del pool IP del server che è stato inviato. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo indirizzo IP è accessibile tramite le sottoscrizioni di Azure ed ExpressRoute che connette le reti virtuali di Azure ad HANA in Azure (istanze Large). L'indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server viene assegnato direttamente all'unità hardware. *Non* viene più assegnato tramite NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

> [!NOTE] 
> Per superare la restrizione relativa al routing temporaneo, come illustrato nelle prime due voci dell'elenco precedente, usare componenti aggiuntivi per il routing. I componenti che consentono di superare la restrizione possono essere:

> * Un proxy inverso per instradare i dati in modo bidirezionale. Ad esempio, F5 BIG-IP, NGINX con Gestione traffico distribuita in Azure come soluzione di routing del traffico o del firewall virtuale.
> * [Regole di IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in una VM Linux per abilitare il routing tra le posizioni locali e le unità di istanze Large di HANA o fra tali unità in aree diverse.

> Tenere presente che Microsoft non fornisce l'implementazione e il supporto per soluzioni personalizzate che riguardano IPTables o appliance di rete di terze parti. Il supporto deve essere reso disponibile dal fornitore del componente usato o dall'integratore. 

### <a name="internet-connectivity-of-hana-large-instance"></a>Connettività Internet delle istanze Large di HANA
Le istanze Large di HANA *non* hanno una connettività Internet diretta. Questo limita ad esempio la possibilità di registrare l'immagine del sistema operativo direttamente con il relativo fornitore. Può quindi essere necessario usare il server SUSE Linux Enterprise Server Subscription Management Tool locale o Red Hat Enterprise Linux Subscription Manager.

### <a name="data-encryption-between-vms-and-hana-large-instance"></a>Crittografia dei dati tra VM e istanze Large di HANA
I dati trasferiti tra le istanze Large di HANA e le VM non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBMS e le applicazioni basate su JDBC/ODBC, è possibile abilitare la crittografia del traffico. Per altre informazioni, vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

### <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usare le unità di istanze Large di HANA in più aree

È possibile distribuire SAP HANA in Azure (istanze Large) in più aree di Azure per motivi diversi dal ripristino di emergenza. Ad esempio, se si vuole accedere a istanze Large di HANA da ognuna delle VM distribuite nelle diverse reti virtuali nelle aree. Gli indirizzi IP assegnati alle diverse unità di istanze Large di HANA non vengono propagati oltre le reti virtuali di Azure, che sono direttamente connesse alle istanze tramite il gateway. Si ha pertanto una lieve variazione del progetto di rete virtuale. Un gateway di rete virtuale può gestire quattro circuiti ExpressRoute diversi da vari router Enterprise Edge. Ogni rete virtuale connessa a uno dei moduli per istanze Large può essere connessa a tale modulo in un'altra area di Azure.

![Rete virtuale connessa ai moduli per istanze Large di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura mostra come le diverse reti virtuali nelle due aree siano connesse a due circuiti ExpressRoute diversi usati per la connessione a SAP HANA in Azure (istanze Large) in entrambe le aree di Azure. Le connessioni appena introdotte sono indicate dalle linee rosse con profilo rettangolare. Con queste connessioni dalle reti virtuali, le VM in esecuzione in una di tali reti possono accedere a ognuna delle diverse unità di istanze Large di HANA distribuite nelle due aree. Come mostrato nella figura, si presuppone che siano configurate due connessioni ExpressRoute dall'ambiente locale alle due aree di Azure. Questa configurazione è consigliata per il ripristino di emergenza.

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.



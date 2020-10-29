---
title: 'SAP in Azure: scenari supportati con macchine virtuali di Azure'
description: Scenari supportati per macchine virtuali di Azure con carico di lavoro SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ec29b6489712eeb67783aef03261a3606a390125
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926615"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carico di lavoro SAP negli scenari supportati da macchine virtuali di Azure
La progettazione di un'architettura di sistemi SAP NetWeaver, Business One `Hybris` o S/4HANA in Azure offre molte opportunità diverse per diverse architetture e strumenti da usare per ottenere una distribuzione scalabile, efficiente e a disponibilità elevata. Tuttavia, a seconda del sistema operativo o del sistema DBMS utilizzato, esistono restrizioni. Inoltre, non tutti gli scenari supportati in locale sono supportati nello stesso modo in Azure. In questo documento vengono illustrate le configurazioni di disponibilità elevata e le configurazioni a disponibilità elevata supportate e le architetture con le macchine virtuali di Azure in modo esclusivo. Per gli scenari supportati con le [istanze large di Hana](./hana-overview-architecture.md), vedere l'articolo [scenari supportati per le istanze large di Hana](./hana-supported-scenario.md). 


## <a name="2-tier-configuration"></a>configurazione a 2 livelli
Una configurazione di SAP a 2 livelli viene considerata compilata da un livello combinato di SAP DBMS e del livello dell'applicazione che vengono eseguiti nello stesso server o unità di VM. Il secondo livello viene considerato il livello dell'interfaccia utente. Nel caso di una configurazione a 2 livelli, il livello dell'applicazione DBMS e SAP condivide le risorse della macchina virtuale di Azure. Di conseguenza, è necessario configurare i diversi componenti in modo che questi non competono per le risorse. È anche necessario prestare attenzione a non sovrascrivere le risorse della macchina virtuale. Una configurazione di questo tipo non fornisce una disponibilità elevata, oltre ai [contratti di servizio di Azure](https://azure.microsoft.com/support/legal/sla/) dei diversi componenti di Azure necessari.

Una rappresentazione grafica di tale configurazione può avere un aspetto simile al seguente:

![Semplice configurazione a 2 livelli](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Tali configurazioni sono supportate con Windows, Red Hat, SUSE e Oracle Linux per i sistemi DBMS di SQL Server, Oracle, DB2, maxDB e SAP ASE per i casi di produzione e non di produzione. Per SAP HANA come DBMS, questo tipo di configurazioni è supportato solo per i case non di produzione. Questo include anche il caso di distribuzione delle [istanze large di Azure Hana](./hana-overview-architecture.md) .
Per tutte le combinazioni di sistema operativo/DBMS supportate in Azure, questo tipo di configurazione è supportato. È tuttavia obbligatorio impostare la configurazione del sistema DBMS e dei componenti SAP in modo che i componenti DBMS e SAP non competono per le risorse di memoria e CPU e quindi superino le risorse fisiche disponibili. Questa operazione deve essere eseguita limitando la memoria che il sistema DBMS può allocare. È anche necessario limitare la memoria estesa di SAP nelle istanze dell'applicazione. È anche necessario monitorare il consumo di CPU della VM complessiva per assicurarsi che i componenti non richiedano l'ottimizzazione delle risorse della CPU. 

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile disporre di disponibilità elevata e di eventuali configurazioni di ripristino di emergenza aggiuntive, come descritto più avanti in questo documento.


## <a name="3-tier-configuration"></a>configurazione a 3 livelli
In queste configurazioni è possibile separare il livello dell'applicazione SAP e il livello DBMS in macchine virtuali diverse. Questa operazione viene in genere eseguita per i sistemi più grandi e per motivi di maggiore flessibilità sulle risorse del livello applicazione SAP. Nell'installazione più semplice, non esiste una disponibilità elevata oltre ai [contratti di servizio di Azure](https://azure.microsoft.com/support/legal/sla/) dei diversi componenti di Azure necessari. 

La rappresentazione grafica ha un aspetto simile al seguente:

![Diagramma che illustra una semplice configurazione a tre livelli.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Questo tipo di configurazione è supportato in Windows, Red Hat, SUSE e Oracle Linux per i sistemi DBMS di SQL Server, Oracle, DB2, SAP HANA, maxDB e SAP ASE per i casi di produzione e non di produzione. Questa è la configurazione di distribuzione predefinita per le [istanze large di Azure Hana](./hana-overview-architecture.md). Per semplificare, non è stata fatta alcuna distinzione tra i servizi SAP Central e le istanze della finestra di dialogo SAP nel livello applicazione SAP. In questa semplice configurazione a tre livelli, non sarà disponibile una protezione a disponibilità elevata per i servizi SAP Central.

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile disporre di disponibilità elevata e di eventuali configurazioni di ripristino di emergenza aggiuntive, come descritto più avanti in questo documento.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Più istanze di DBMS per macchina virtuale o unità di istanze large di HANA
In questo tipo di configurazione sono ospitate più istanze di DBMS per macchina virtuale di Azure o unità di istanze large di HANA. La motivazione può essere costituita da un minor numero di sistemi operativi da gestire e con costi ridotti. Altre motivazioni possono essere la maggiore flessibilità e maggiore efficienza grazie alla condivisione delle risorse di una macchina virtuale di grandi dimensioni o di un'unità di istanze large di HANA tra più istanze di DBMS. Fino a questo punto, queste configurazioni venivano visualizzate principalmente per i sistemi non di produzione.

Una configurazione simile potrebbe essere simile alla seguente:

![Più istanze di DBMS in un'unità](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Questo tipo di distribuzione DBMS è supportato per:

- SQL Server in Windows
- IBM DB2. Trovare i dettagli nell'articolo [più istanze (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Per Oracle. Per informazioni dettagliate, vedere la [Nota di supporto sap #1778431](https://launchpad.support.sap.com/#/notes/1778431) e le note SAP correlate
- Per SAP HANA, più istanze in una macchina virtuale, SAP chiama questo metodo di distribuzione MCOS, è supportato. Per informazioni dettagliate, vedere l'articolo SAP [più sistemi di SAP HANA in un host (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Eseguendo più istanze di database in un host, è necessario assicurarsi che le diverse istanze non siano in competizione per le risorse e quindi superino i limiti di risorse fisiche della macchina virtuale. Questa operazione è particolarmente valida per la memoria in cui è necessario limitare la memoria che tutti gli utenti che condividono la VM possono allocare. Questo potrebbe anche essere vero per le risorse della CPU che possono essere sfruttate dalle diverse istanze di database. A tutti i sistemi DBMS indicati sono associate configurazioni che consentono di limitare l'allocazione di memoria e le risorse della CPU a livello di istanza.
Per ottenere supporto per una configurazione di questo tipo per le macchine virtuali di Azure, è previsto che i dischi o i volumi usati per i file di log di dati e log/rollforward dei database gestiti dalle diverse istanze siano separati. In alternativa, in altre parole, i file di log o log/rollforward dei database gestiti da un'istanza di DBMS diversa non dovrebbero condividere gli stessi dischi o volumi. 

La configurazione del disco per le istanze large di HANA viene distribuita ed è descritta in dettaglio negli [scenari supportati per le istanze large di Hana](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile disporre di disponibilità elevata e di eventuali configurazioni di ripristino di emergenza aggiuntive, come descritto più avanti in questo documento. Le macchine virtuali con più istanze DBMS non sono supportate con le configurazioni a disponibilità elevata descritte più avanti in questo documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Più istanze di finestra di dialogo SAP in una macchina virtuale
In molti casi, più istanze della finestra di dialogo sono state distribuite su Server Bare Metal o anche in macchine virtuali in esecuzione in cloud privati. Il motivo di tali configurazioni è stato quello di adattare determinate istanze di finestre di dialogo SAP a determinati tipi di carico di lavoro, funzionalità aziendali o carichi di lavoro. Il motivo per cui non è possibile isolare le istanze in macchine virtuali separate è stato la fatica della manutenzione e delle operazioni del sistema operativo. O in molti casi i costi nel caso in cui l'host o l'operatore della macchina virtuale chieda una tariffa mensile per ogni macchina virtuale gestita e amministrata. In Azure, scenario di hosting di più istanze di finestre di dialogo SAP all'interno di una singola macchina virtuale, sono supportati per scopi di produzione e non di produzione nei sistemi operativi di Windows, Red Hat, SUSE e Oracle Linux. È necessario impostare il parametro del kernel SAP PHYS_MEMSIZE, disponibile nei kernel Linux e Windows moderni, se più istanze del server applicazioni SAP sono in esecuzione in una singola macchina virtuale. È inoltre consigliabile limitare l'espansione della memoria estesa di SAP nei sistemi operativi, ad esempio Windows, in cui è implementata la crescita automatica della memoria estesa di SAP. Questa operazione può essere eseguita con il parametro del profilo SAP `em/max_size_MB` .

Alla configurazione a 3 livelli in cui vengono eseguite più istanze di finestra di dialogo SAP nelle VM di Azure può essere simile a:

![Diagramma che illustra una configurazione a 3 livelli in cui vengono eseguite più istanze di finestra di dialogo SAP nelle VM di Azure.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Per semplificare, non è stata fatta alcuna distinzione tra i servizi SAP Central e le istanze della finestra di dialogo SAP nel livello applicazione SAP. In questa semplice configurazione a tre livelli, non sarà disponibile una protezione a disponibilità elevata per i servizi SAP Central. Per i sistemi di produzione, non è consigliabile lasciare non protetti i servizi SAP Central. Per informazioni specifiche su, denominate configurazioni a più SID per le istanze di SAP Central e disponibilità elevata di tali configurazioni a più SID, vedere le sezioni successive di questo documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Protezione della disponibilità elevata per il livello DBMS di SAP
Quando si cerca di distribuire sistemi di produzione SAP, è necessario prendere in considerazione hot standby tipo di configurazioni a disponibilità elevata. Soprattutto con SAP HANA, in cui i dati devono essere caricati in memoria prima di poter ottenere le prestazioni complete e la scalabilità, la correzione dei servizi di Azure non è una misura ideale per la disponibilità elevata. 

In generale Microsoft supporta solo le configurazioni a disponibilità elevata e i pacchetti software descritti nella sezione del carico di lavoro SAP in docs.microsoft.com. È possibile leggere la stessa istruzione nella nota SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Microsoft non offrirà supporto per altri Framework software di terze parti a disponibilità elevata che non sono documentati da Microsoft insieme al carico di lavoro SAP. In questi casi, il fornitore di terze parti del Framework a disponibilità elevata è l'entità di supporto per la configurazione a disponibilità elevata che deve essere impegnata dall'utente come cliente nel processo di supporto. Le eccezioni verranno indicate in questo articolo. 

In generale Microsoft supporta un set limitato di configurazioni a disponibilità elevata in macchine virtuali di Azure o unità di istanze large di HANA. Per gli scenari supportati delle istanze large di HANA, leggere gli [scenari supportati per le istanze large di Hana](./hana-supported-scenario.md).

Per le macchine virtuali di Azure, le configurazioni a disponibilità elevata seguenti sono supportate a livello di DBMS:

- SAP HANA la replica di sistema basata su pacemaker Linux in SUSE e Red Hat. Vedere gli articoli dettagliati:
    - [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA configurazioni con scalabilità orizzontale n + m usando [Azure NetApp files](https://azure.microsoft.com/services/netapp/) su SUSE e Red Hat. I dettagli sono elencati in questi articoli:
    - [Distribuire un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files in SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Distribuire un sistema di SAP HANA con scale-out con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server cluster di failover basato su Servizi file Windows Scale-Out. Tuttavia, per i sistemi di produzione è consigliabile utilizzare SQL Server Always On anziché il clustering. SQL Server Always On garantisce una maggiore disponibilità usando un'archiviazione separata. I dettagli sono descritti in questo articolo: 
    - [Configurare un'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On è supportato con il sistema operativo Windows per SQL Server in Azure. Si tratta dell'indicazione predefinita per le istanze di SQL Server di produzione in Azure. I dettagli sono descritti in questi articoli:
    - [Panoramica sui gruppi di disponibilità Always On di SQL Server in macchine virtuali di Azure](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)
    - [Configurare un gruppo di disponibilità Always On in macchine virtuali di Azure in aree diverse](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
    - [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità Always On in Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)
- Oracle Data Guard per Windows e Oracle Linux. Per informazioni dettagliate su Oracle Linux, vedere questo articolo:
    - [Implementare Oracle Data Guard su una macchina virtuale Linux di Azure](../oracle/configure-oracle-dataguard.md)
- La documentazione dettagliata di IBM DB2 HADR su SUSE e RHEL per SUSE e RHEL con pacemaker è disponibile qui:
    - [Disponibilità elevata di IBM DB2 LUW in macchine virtuali di Azure in SUSE Linux Enterprise Server con pacemaker](./dbms-guide-ha-ibm.md)
    - [Disponibilità elevata di IBN Db2 LUW in macchine virtuali di Azure su Red Hat Enterprise Linux Server](./high-availability-guide-rhel-ibm-db2-luw.md)
- Configurazione di SAP ASE e SAP maxDB come descritto in dettaglio in questi documenti:
    - [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](./dbms_guide_sapase.md)
    - [Distribuzione di SAP MaxDB, SAP liveCache e SAP Content Server in macchine virtuali di Azure](./dbms_guide_maxdb.md)
- Gli scenari di disponibilità elevata di HANA in istanze large sono descritti in dettaglio in:
    - [Scenari supportati per le istanze large di HANA-HSR con STONITH per la disponibilità elevata](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [Scenari supportati per le istanze large di HANA-failover automatico dell'host (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> Per nessuno degli scenari descritti in precedenza, sono supportate configurazioni di più istanze di DBMS in una macchina virtuale. Indica che in ogni caso è possibile distribuire una sola istanza di database per ogni macchina virtuale e proteggerla con i metodi di disponibilità elevata descritti. La protezione di più istanze di DBMS nello stesso cluster di failover di Windows o pacemaker **non** è supportata in questo momento. Oracle Data Guard è inoltre supportato solo per i casi di distribuzione a istanza singola per macchina virtuale. 

Vari sistemi di database consentono di ospitare più database in un'istanza di DBMS. Come nel caso di SAP HANA, più database possono essere ospitati in più contenitori di database (MDC). Per i casi in cui queste configurazioni con più database funzionano all'interno di una risorsa cluster di failover, queste configurazioni sono supportate. Le configurazioni non supportate sono casi in cui sono necessarie più risorse cluster. Come per le configurazioni in cui definire più gruppi di disponibilità SQL Server, in un'unica istanza di SQL Server.


![Configurazione di disponibilità elevata DBMS](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

A seconda del sistema operativo DBMS, i componenti come Azure Load Balancer potrebbero o meno essere necessari come parte dell'architettura della soluzione. 

In particolare per maxDB, la configurazione dell'archiviazione deve essere diversa. In maxDB i dati e i file di log devono trovarsi nello spazio di archiviazione condiviso per le configurazioni a disponibilità elevata. Solo nel caso di maxDB, lo spazio di archiviazione condiviso è supportato per la disponibilità elevata. Per tutti gli altri stack di archiviazione separati da DBMS per nodo sono disponibili solo le configurazioni del disco supportate.

Esistono anche altri Framework a disponibilità elevata che sono noti per essere eseguiti anche in Microsoft Azure. Tuttavia, Microsoft non ha testato tali Framework. Se si vuole compilare la configurazione a disponibilità elevata con questi Framework, è necessario collaborare con il provider del software per:

- Sviluppare un'architettura di distribuzione
- Distribuzione dell'architettura
- Supporto dell'architettura

> [!IMPORTANT]
> Microsoft Azure Marketplace offre un'ampia gamma di appliance soft che offrono soluzioni di archiviazione basate sull'archiviazione nativa di Azure. Queste appliance Soft possono essere usate per creare condivisioni NFS e che teoricamente potrebbero essere usate nelle distribuzioni con scalabilità orizzontale SAP HANA in cui è necessario un nodo standby. A causa di diversi motivi, nessuno di questi dispositivi soft storage è supportato per le distribuzioni DBMS da parte di Microsoft e SAP in Azure. Le distribuzioni di DBMS in condivisioni SMB non sono attualmente supportate in questo momento. Le distribuzioni di DBMS in condivisioni NFS sono limitate alle condivisioni NFS 4,1 in [Azure NetApp files](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Disponibilità elevata per il servizio SAP Central
SAP Central Services è un secondo singolo punto di guasto della configurazione SAP. Di conseguenza, è necessario proteggere anche questi processi di servizi centrali. L'offerta supportata e documentata per il carico di lavoro SAP è simile alla seguente:

- Server del cluster di failover di Windows che usa i servizi file di scalabilità orizzontale di Windows per sapmnt e la directory di trasporto globale. I dettagli sono descritti nell'articolo:
    - [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file in Azure](./sap-high-availability-guide-wsfc-file-share.md)
    - [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)
- Server del cluster di failover di Windows che utilizza la condivisione SMB basata su [Azure NetApp files](https://azure.microsoft.com/services/netapp/) per sapmnt e la directory di trasporto globale. I dettagli sono elencati nell'articolo:
    - [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAP](./high-availability-guide-windows-netapp-files-smb.md)
- Server del cluster di failover di Windows basato sul tipo di base `Datakeeper` . Anche se documentato da Microsoft, è necessaria una relazione di supporto con un valore di base, in modo che sia possibile interagire con il supporto del valore di base quando si usa questa soluzione. I dettagli sono descritti nell'articolo:
    - [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker sul sistema operativo SUSE con la creazione di una condivisione NFS a disponibilità elevata con due VM SUSE e `drdb` per la replica di file. I dettagli sono documentati nell'articolo
    - [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](./high-availability-guide-suse.md)
    - [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- Sistema operativo SUSE pacemaker con le condivisioni NFS fornite da [Azure NetApp files](https://azure.microsoft.com/services/netapp/). I dettagli sono documentati in
    - [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP](./high-availability-guide-suse-netapp-files.md)
- Pacemaker sul sistema operativo Red Hat con condivisione NFS ospitata in un `glusterfs` cluster. Informazioni dettagliate sono disponibili negli articoli
    - [Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` in macchine virtuali di Azure in Red Hat Enterprise Linux per SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- Pacemaker sul sistema operativo Red Hat con condivisione NFS ospitata in [Azure NetApp files](https://azure.microsoft.com/services/netapp/). I dettagli sono descritti nell'articolo
    - [Disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP](./high-availability-guide-rhel-netapp-files.md)

Tra le soluzioni elencate, è necessaria una relazione di supporto con il supporto per il `Datakeeper` prodotto e per coinvolgere direttamente il codice in caso di problemi. A seconda del modo in cui è stato concesso in licenza il sistema operativo Windows, Red Hat e/o SUSE, potrebbe essere necessario disporre di un contratto di supporto con il provider del sistema operativo per ottenere supporto completo delle configurazioni di disponibilità elevata elencate.

La configurazione può essere visualizzata come segue:

![Configurazione della disponibilità elevata DBMS e ASC](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Sul lato destro della grafica, vengono visualizzati i servizi SAP Central a disponibilità elevata. Oltre ad avere protetto i servizi SAP Central con un Framework del cluster di failover in cui è possibile eseguire il failover in caso di problemi, è necessaria una condivisione NFS o SMB a disponibilità elevata oppure un disco condiviso di Windows per assicurarsi che la directory sapmnt e il trasporto globale siano disponibili indipendentemente dall'esistenza di una singola macchina virtuale. Altre soluzioni, ad esempio server del cluster di failover Windows e pacemaker, richiedono un servizio di bilanciamento del carico di Azure per indirizzare o reindirizzare il traffico a un nodo integro.

Nell'elenco visualizzato non è presente alcuna menzione del sistema operativo Oracle Linux. Oracle Linux non supporta pacemaker come Framework di cluster. Se si vuole distribuire il sistema SAP in Oracle Linux ed è necessario un Framework a disponibilità elevata per Oracle Linux, è necessario collaborare con i fornitori di terze parti. Uno dei fornitori è l'uso di un gruppo di protezione per Linux supportato da SAP in Azure. Per altre informazioni, vedere la nota SAP [#1662610-dettagli di supporto per la protezione di un gruppo di](https://launchpad.support.sap.com/#/notes/1662610) elementi per Linux.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Archiviazione supportata con gli scenari SAP Central Services elencati sopra
Poiché solo un subset di tipi di archiviazione di Azure fornisce una condivisione NFS o SMB a disponibilità elevata, tale qualità per l'utilizzo nei cluster SAP Central Services cluster rappresenta un elenco di tipi di archiviazione supportati

- Il server del cluster di failover Windows con file server di scalabilità orizzontale di Windows può essere distribuito in tutti i tipi di archiviazione nativi di Azure, ad eccezione Azure NetApp Files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio di livello superiore in velocità effettiva e IOPS.
- Il server del cluster di failover di Windows con SMB su Azure NetApp Files è supportato su Azure NetApp Files. Le condivisioni SMB nei servizi file di Azure **non** sono supportate in questo momento.
- Il server del cluster di failover Windows con disco condiviso di Windows basato su un tipo di nodo `Datakeeper` può essere distribuito in tutti i tipi di archiviazione nativi di Azure, ad eccezione Azure NetApp files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio di livello superiore in velocità effettiva e IOPS.
- SUSE o Red Hat pacemaker con condivisioni NFS in Azure NetApp Files è supportato in Azure NetApp Files. 
- SUSE pacemaker con una `drdb` configurazione tra due macchine virtuali è supportato con i tipi di archiviazione nativi di Azure, ad eccezione Azure NetApp files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio di livello superiore in velocità effettiva e IOPS.
- Red Hat pacemaker che usa `glusterfs` per fornire la condivisione NFS è supportato con i tipi di archiviazione nativi di Azure, ad eccezione Azure NetApp files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio di livello superiore in velocità effettiva e IOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace offre un'ampia gamma di appliance soft che offrono soluzioni di archiviazione basate sull'archiviazione nativa di Azure. Queste appliance Soft possono essere usate anche per creare condivisioni NFS o SMB, che teoricamente potrebbero essere usate anche nei servizi di SAP Central cluster di failover. Queste soluzioni non sono direttamente supportate per il carico di lavoro SAP da Microsoft. Se si decide di usare una soluzione di questo tipo per creare una condivisione NFS o SMB, il supporto per la configurazione di SAP Central Service deve essere fornito da terze parti che possiedono il software nell'appliance software di archiviazione.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Cluster di failover di servizi centrali SAP a più SID
Per ridurre il numero di macchine virtuali necessarie per i panoramici SAP di grandi dimensioni, SAP consente di eseguire istanze di SAP Central Services di più sistemi SAP diversi nella configurazione del cluster di failover. Nei casi in cui sono presenti 30 o più sistemi di produzione NetWeaver o S/4HANA. Senza il clustering a più SID, queste configurazioni richiedono 60 o più macchine virtuali in 30 o più configurazioni del cluster di failover di Windows o pacemaker. Oltre ai cluster di failover DBMS necessari. La distribuzione di più servizi SAP Central in due nodi in una configurazione di cluster di failover può ridurre significativamente il numero di macchine virtuali. Tuttavia, la distribuzione di più istanze di SAP Central Services in una singola configurazione cluster a due nodi presenta anche alcuni svantaggi. I problemi relativi a una singola macchina virtuale nella configurazione del cluster si applicano a più sistemi SAP. La manutenzione del sistema operativo guest in esecuzione nella configurazione del cluster richiede un maggior coordinamento poiché sono interessati più sistemi SAP di produzione. Strumenti come SAP LaMa non supportano il clustering a più SID nel processo di clonazione del sistema.

In Azure è supportata la configurazione di un cluster a più SID per il sistema operativo Windows con ENSA1 e ENSA2. Si consiglia di non combinare la precedente architettura del servizio di replica di Accodamento (ENSA1) con la nuova architettura (ENSA2) in un cluster a più SID. Informazioni dettagliate su tale architettura sono descritte negli articoli

- [Disponibilità elevata multi-SID dell'istanza SAP ASCS/SCS con Windows Server Failover Clustering e i dischi condivisi in Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Disponibilità elevata multi-SID dell'istanza ASCS/SCS di SAP con Windows Server Failover Clustering e condivisione file in Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

Per SUSE è supportato anche un cluster a più SID basato su pacemaker. Fino a questo momento la configurazione è supportata per:

- Un massimo di cinque istanze di SAP ASC/SCS
- L'architettura del server di replica dell'accodamento precedente (ENSA1)
- Configurazioni del cluster pacemaker a due nodi

La configurazione è documentata in [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server per le applicazioni SAP guida](./high-availability-guide-suse-multi-sid.md) a più SID

Un cluster a più SID con server di replica di accodamento è schematicamente simile a

![Diagramma che mostra un cluster a più SID con server di replica di Accodamento.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Scenari di scalabilità orizzontale SAP HANA
SAP HANA scenari di scalabilità orizzontale sono supportati per un subset di macchine virtuali di Azure certificate HANA, come elencato nella [directory hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Tutte le VM contrassegnate con ' Yes ' nella colonna ' clustering ' possono essere usate per la scalabilità orizzontale OLAP o S/4HANA. Le configurazioni senza standby sono supportate con i tipi di archiviazione di Azure: 

- Archiviazione Premium di Azure, incluso l'acceleratore di scrittura di Azure per il volume/Hana/log
- [Disco Ultra](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

SAP HANA configurazioni con scalabilità orizzontale per OLAP o S/4HANA con nodi standby sono supportate in modo esclusivo con NFS Shared ospitato su Azure NetApp Files.

Per ulteriori informazioni sulle configurazioni di archiviazione esatte con o senza nodo standby, vedere gli articoli:

- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](./hana-vm-operations-storage.md) 
- [Distribuire un sistema di SAP HANA con scale-out con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Distribuire un sistema di SAP HANA con scale-out con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [Nota di supporto SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Per informazioni dettagliate sulle configurazioni Hana con scalabilità orizzontale supportate, si applica la seguente documentazione:

- [Scenari supportati per le istanze large di HANA con scalabilità orizzontale con standby](./hana-supported-scenario.md#scale-out-with-standby)
- [Scenari supportati per la scalabilità orizzontale di istanze large di HANA senza standby](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenario di ripristino di emergenza
Sono supportati diversi scenari di ripristino di emergenza. Si definiscono le architetture di emergenza come architetture che devono compensare le aree di Azure complete che passano dalla griglia. Ciò significa che la destinazione di ripristino di emergenza deve essere un'area di Azure diversa come destinazione per l'esecuzione del panorama applicativo SAP. Si separano metodi e configurazioni nel livello DBMS e nel livello non DBMS. 

### <a name="dbms-layer"></a>Livello DBMS
Per il livello DBMS, sono supportate le configurazioni che usano i meccanismi di replica nativa di DBMS, ad esempio Always On, Oracle Data Guard, DB2 HADR, SAP ASE always on o la replica di sistema HANA. È obbligatorio che il flusso di replica in questi casi sia asincrono, anziché sincrono come negli scenari di disponibilità elevata tipici distribuiti all'interno di una singola area di Azure. Un esempio tipico di una configurazione di ripristino di emergenza DBMS supportata è descritto nell'articolo [SAP Hana disponibilità nelle aree di Azure](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions). Il secondo grafico in questa sezione descrive uno scenario con HANA come esempio. I database principali supportati per le applicazioni SAP sono tutti in grado di essere distribuiti in uno scenario di questo tipo.

È supportata l'uso di una macchina virtuale di dimensioni inferiori come istanza di destinazione nell'area di ripristino di emergenza, poiché tale macchina virtuale non verifica il traffico completo del carico di lavoro. In questo modo, è necessario tenere presenti le considerazioni seguenti:

- I tipi di VM più piccoli non consentono che molti dischi siano collegati a VM più piccole
- VM più piccole hanno una velocità effettiva di rete e di archiviazione inferiore
- Il ridimensionamento tra le famiglie di macchine virtuali può costituire un problema quando si raccolgono macchine virtuali diverse in un set di disponibilità di Azure o quando si verifica il ridimensionamento tra la famiglia della serie M e la famiglia di macchine virtuali Mv2
- Utilizzo della CPU e della memoria per l'istanza del database in grado di ricevere il flusso di modifiche con ritardo minimo e risorse di CPU e memoria sufficienti per applicare queste modifiche con un ritardo minimo ai dati  

Altre informazioni sulle limitazioni delle diverse dimensioni delle macchine virtuali sono disponibili [qui](../../sizes.md) 

Un altro metodo supportato per la distribuzione di una destinazione di ripristino di emergenza consiste nel disporre di una seconda istanza di DBMS installata in una macchina virtuale che esegue un'istanza DBMS non di produzione di un'istanza SAP non di produzione. Questo può essere un po' più complesso, poiché è necessario capire in che modo la memoria, le risorse della CPU, la larghezza di banda di rete e la larghezza di banda di archiviazione sono necessarie per le istanze di destinazione specifiche che dovrebbero funzionare come istanza principale nello scenario di ripristino di emergenza. In particolare, si consiglia di configurare l'istanza di che funziona come destinazione di ripristino di emergenza in un host condiviso, in modo che i dati non siano pre-caricati nell'istanza di destinazione di ripristino di emergenza.

Per gli scenari di ripristino di emergenza di istanze large di HANA, controllare questi documenti:

- [Singolo nodo con ripristino di emergenza che usa la replica di archiviazione](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Singolo nodo con ripristino di emergenza (Multipurpose) con replica di archiviazione](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Singolo nodo con ripristino di emergenza (Multipurpose) con replica di archiviazione](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazione](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazione](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Singolo nodo con ripristino di emergenza con HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [HSR a nodo singolo al ripristino di emergenza (costo ottimizzato)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Disponibilità elevata e ripristino di emergenza con HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Disponibilità elevata e ripristino di emergenza con HSR (costo ottimizzato)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Scalabilità orizzontale con il ripristino di emergenza con HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> L'utilizzo di [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) non è stato testato per le distribuzioni DBMS nel carico di lavoro SAP. Di conseguenza, non è supportato per il livello DBMS dei sistemi SAP in questo momento. Altri metodi di replica di Microsoft e SAP non elencati non sono supportati. L'uso di software di terze parti per la replica del livello DBMS dei sistemi SAP tra diverse aree di Azure deve essere supportato dal fornitore del software e non sarà supportato tramite i canali di supporto Microsoft e SAP. 
 
## <a name="non-dbms-layer"></a>Livello non DBMS
Per il livello dell'applicazione SAP e per le condivisioni finali o i percorsi di archiviazione necessari, i due scenari principali vengono utilizzati dai clienti:

- Le destinazioni di ripristino di emergenza nella seconda area di Azure non vengono usate per scopi di produzione o non di produzione. In questo scenario le VM che funzionano come destinazione del ripristino di emergenza non vengono distribuite idealmente e l'immagine e le modifiche apportate alle immagini del livello applicazione SAP di produzione vengono replicate nell'area di ripristino di emergenza. Una funzionalità che può eseguire tale attività è [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery supportano scenari di replica da Azure ad Azure come questo. 
- Le destinazioni di ripristino di emergenza sono macchine virtuali effettivamente utilizzate dai sistemi non di produzione. L'intero panorama applicativo SAP è distribuito in due diverse aree di Azure con sistemi di produzione in genere in un'area e in sistemi non di produzione in un'altra area. In numerose distribuzioni dei clienti, il cliente dispone di un sistema non di produzione equivalente a un sistema di produzione. Il cliente dispone di istanze di applicazioni di produzione preinstallate nei sistemi non di produzione a livello di applicazione. In caso di failover, le istanze non di produzione verranno arrestate, i nomi virtuali delle macchine virtuali di produzione vengono spostati nelle VM non di produzione (dopo l'assegnazione di nuovi indirizzi IP in DNS) e vengono avviate le istanze di produzione preinstallate

### <a name="sap-central-services-clusters"></a>Cluster SAP Central Services
I cluster di servizi centrali SAP che usano dischi condivisi (Windows), condivisioni SMB (Windows) o condivisioni NFS sono un po' più difficili da replicare. Sul lato Windows, la replica di archiviazione di Windows è una possibile soluzione. In Linux rsync è una soluzione valida.



## <a name="non-supported-scenario"></a>Scenario non supportato
È disponibile un elenco di scenari, che non sono supportati per il carico di lavoro SAP nelle architetture di Azure. **Non supportato** significa che SAP e Microsoft non saranno in grado di supportare queste configurazioni ed è necessario rinviare a un eventuale componente di terze parti che ha fornito il software per stabilire tali architetture. Due delle categorie sono:

- Appliance Soft Appliance: è disponibile una serie di appliance di archiviazione di Azure Marketplace. Alcuni fornitori offrono una documentazione personalizzata su come usare questi dispositivi soft storage in Azure correlati al software SAP. Il supporto per le configurazioni o le distribuzioni che coinvolgono questi dispositivi soft di archiviazione deve essere fornito dal fornitore di tali appliance di archiviazione. Questo fatto è anche manifesto nella [Nota di supporto SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Framework a disponibilità elevata: solo pacemaker e cluster di failover di Windows Server sono Framework di disponibilità elevata supportati per il carico di lavoro SAP in Azure. Come indicato in precedenza, la soluzione di base `Datakeeper` è descritta e documentata da Microsoft. Tuttavia, è necessario che i componenti di un oggetto di tale componente `Datakeeper` siano supportati tramite il sistema di supporto come fornitore che fornisce tali componenti. SAP ha elencato anche altri Framework di disponibilità elevata certificati in diverse note SAP. Alcuni di essi sono stati certificati anche dal fornitore di terze parti per Azure. Tuttavia, il supporto per le configurazioni che utilizzano tali prodotti deve essere fornito dal fornitore del prodotto. Fornitori diversi hanno un'integrazione diversa nei processi di supporto SAP. È necessario chiarire quale processo di supporto funziona meglio per il fornitore specifico prima di decidere di usare il prodotto nelle configurazioni SAP distribuite in Azure.
- I cluster di dischi condivisi in cui risiedono i file di database nei dischi condivisi non sono supportati ad eccezione di maxDB. Per tutti gli altri database, la soluzione supportata consiste nel disporre di posizioni di archiviazione separate anziché una condivisione SMB o NFS o un disco condiviso per la configurazione di scenari a disponibilità elevata

Altri scenari, che non sono supportati, sono scenari come:

- Scenari di distribuzione che introducono una latenza di rete più ampia tra il livello applicazione SAP e il livello DBMS SAP nell'architettura comune di SAP, come illustrato in NetWeaver, S/4HANA e ad `Hybris` esempio. ad esempio:
    - Distribuzione di uno dei livelli in locale, mentre l'altro livello viene distribuito in Azure
    - Distribuzione del livello applicazione SAP di un sistema in un'area di Azure diversa rispetto al livello DBMS
    - Distribuzione di un livello nei data center con percorso condiviso in Azure e nell'altro livello in Azure, tranne nel caso di modelli di architettura di questo tipo forniti da un servizio nativo di Azure
    - Distribuzione di appliance virtuali di rete tra il livello applicazione SAP e il livello DBMS
    - Uso dello spazio di archiviazione ospitato nei data center con percorso condiviso nel Data Center di Azure per il livello DBMS SAP o la directory di trasporto globale SAP
    - Distribuzione dei due livelli con due fornitori di cloud diversi. Ad esempio, distribuendo il livello DBMS nell'infrastruttura cloud Oracle e il livello applicazione in Azure
- Configurazioni del cluster per pacemaker HANA a istanze diverse
- Configurazioni del cluster Windows con dischi condivisi tramite SOFS o SMB in e per database SAP supportati in Windows. È invece consigliabile usare la replica di disponibilità elevata nativa dei database specifici e usare stack di archiviazione separati
- Distribuzione di database SAP supportati in Linux con file di database che si trovano in condivisioni NFS su e, ad eccezione di SAP HANA
- Distribuzione di Oracle DBMS in qualsiasi altro sistema operativo guest rispetto a Windows e Oracle Linux. Vedere anche la [Nota di supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Uno o più scenari che non sono stati testati e che quindi non hanno alcuna esperienza con un elenco simile al seguente:

- Azure Site Recovery la replica di VM di livello DBMS. Di conseguenza, è consigliabile sfruttare la funzionalità di replica asincrona nativa del database per la configurazione del ripristino di emergenza potenziale.
 

## <a name="next-steps"></a>Passaggi successivi
Leggere i passaggi successivi in [pianificazione e implementazione di macchine virtuali di Azure per SAP NetWeaver](./planning-guide.md)




  
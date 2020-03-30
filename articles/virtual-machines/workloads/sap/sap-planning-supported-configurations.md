---
title: 'SAP on Azure: Supported Scenarios with Azure VMs'
description: Scenari supportati da Macchine virtuali di Azure con carico di lavoro SAPAzure Virtual Machines supported scenarios with SAP workload
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
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137629"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carico di lavoro SAP negli scenari supportati da macchine virtuali di AzureSAP workload on Azure virtual machine supported scenarios
La progettazione dell'architettura `Hybris` di sistemi SAP NetWeaver, Business one o S/4HANA in Azure offre molte opportunità diverse per varie architetture e strumenti da usare per ottenere una distribuzione scalabile, efficiente e a disponibilità elevata. Anche se dipende dal sistema operativo o DBMS utilizzato, ci sono restrizioni. Inoltre, non tutti gli scenari supportati in locale sono supportati nello stesso modo in Azure.Also, not all scenarios that are supported on-premises are supported in the same way in Azure. Questo documento guiderà attraverso le configurazioni non a disponibilità elevata supportate e le configurazioni e le architetture a disponibilità elevata usando esclusivamente le macchine virtuali di Azure.This document will lead through the supported non-high-availability configurations and high-availability configurations and architectures using Azure VMs exclusively. Per gli scenari supportati con [le istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture), vedere l'articolo [Scenari supportati per le istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario). 


## <a name="2-tier-configuration"></a>Configurazione a 2 livelli
Una configurazione SAP 2 livelli è considerata costituita da un livello combinato del DBMS SAP e del livello applicazione che viene eseguito sullo stesso server o unità VM. Il secondo livello è considerato il livello dell'interfaccia utente. Nel caso di una configurazione a 2 livelli, il DBMS e il livello applicazione SAP condividono le risorse della macchina virtuale di Azure.In the case of a 2-Tier configuration, the DBMS and SAP application layer share the resources of the Azure VM. Di conseguenza, è necessario configurare i diversi componenti in modo che non competano per le risorse. È inoltre necessario fare attenzione a non eseguire l'oversubscription delle risorse della macchina virtuale. Una configurazione di questo tipo non fornisce alcuna disponibilità elevata, oltre ai [contratti di servizio](https://azure.microsoft.com/support/legal/sla/) di Azure dei diversi componenti di Azure coinvolti.

Una rappresentazione grafica di tale configurazione può essere simile alla seguente:A graphical representation of such a configuration can look like:

![Semplice configurazione a 2 livelli](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Tali configurazioni sono supportate con Windows, Red Hat, SUSE e Oracle Linux per i sistemi DBMS di SQL Server, Oracle, Db2, maxDB e SAP ASE per i casi di produzione e non di produzione. Per SAP HANA come DBMS, tale tipo di configurazioni è supportato solo per i casi non di produzione. Ciò include anche il caso di distribuzione di istanze di [grandi dimensioni di Azure HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
Per tutte le combinazioni OS/DBMS supportate in Azure, questo tipo di configurazione è supportato. Tuttavia, è obbligatorio impostare la configurazione dei componenti DBMS e SAP in modo che i componenti DBMS e SAP non competano per le risorse di memoria e CPU e quindi superino le risorse fisiche disponibili. Questa operazione deve essere eseguita limitando la memoria che il DBMS è autorizzato ad allocare. È inoltre necessario limitare la memoria estesa SAP nelle istanze dell'applicazione. È inoltre necessario monitorare l'utilizzo complessivo della CPU della macchina virtuale per assicurarsi che i componenti non copionino le risorse della CPU. 

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile ulteriori configurazioni di disponibilità elevata ed eventuale ripristino di emergenza come descritto più avanti in questo documento


## <a name="3-tier-configuration"></a>Configurazione a 3 livelli
In tali configurazioni, si separano il livello dell'applicazione SAP e il livello DBMS in macchine virtuali diverse. In genere questa operazione viene eseguita per i sistemi più grandi e per motivi di maggiore flessibilità nelle risorse del livello applicazione SAP. Nell'installazione più semplice, non esiste disponibilità elevata oltre ai [contratti di servizio](https://azure.microsoft.com/support/legal/sla/) di Azure dei diversi componenti di Azure coinvolti. 

La rappresentazione grafica è simile alla:

![Semplice configurazione a 2 livelli](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Questo tipo di configurazione è supportato in Windows, Red Hat, SUSE e Oracle Linux per i sistemi DBMS di SQL Server, Oracle, Db2, SAP HANA, maxDB e SAP ASE per i casi di produzione e non di produzione. Questa è la configurazione di distribuzione predefinita per le istanze di [grandi dimensioni di Azure HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Per la semplificazione, non sono stati compresi tra le istanze delle finestre di dialogo SAP Central Services e SAP nel livello dell'applicazione SAP. In questa semplice configurazione a 3 livelli, non vi sarebbe alcuna protezione della disponibilità elevata per SAP Central Services.

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile ulteriori configurazioni di disponibilità elevata ed eventuale ripristino di emergenza come descritto più avanti in questo documento


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Più istanze DBMS per macchina virtuale o unità di istanza HANA large
In questo tipo di configurazione si ospitano più istanze DBMS per ogni macchina virtuale di Azure o per unità di istanza di grandi dimensioni HANA. La motivazione può essere quella di avere meno sistemi operativi da mantenere e con questo ridotto i costi. Altre motivazioni possono essere più flessibilità ed efficienza condividendo le risorse di una macchina virtuale o di un'unità di istanza di grandi dimensioni HANA tra più istanze DBMS. Finora queste configurazioni sono state visualizzate principalmente per i sistemi non di produzione.

Una configurazione del genere potrebbe essere simile alla seguente:A configuration like that could look like:

![Più istanze DBMS in un'unica unitàMultiple DBMS instances in one unit](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Questo tipo di distribuzione DBMS è supportato per:

- SQL Server in Windows
- IBM Db2. Per informazioni dettagliate, vedere l'articolo Istanze multiple [(Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Per Oracle. Per informazioni dettagliate, vedere [NOTa](https://launchpad.support.sap.com/#/notes/1778431) di supporto SAP #1778431 e note SAP correlate
- Per SAP HANA, sono supportate più istanze in una macchina virtuale, chiamate questo metodo di distribuzione MCOS. Per informazioni dettagliate, vedere l'articolo SAP [Multiple SAP HANA Systems on One Host (MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Se si eseguono più istanze di database in un host, è necessario assicurarsi che le diverse istanze non siano in competizione per le risorse e quindi superino i limiti delle risorse fisiche della macchina virtuale. Ciò è particolarmente vero per la memoria in cui è necessario captare la memoria che chiunque delle istanze che condividono la macchina virtuale può allocare. Questo potrebbe essere vero anche per le risorse della CPU che le diverse istanze di database possono sfruttare. Tutti i DBMS menzionati dispongono di configurazioni che consentono di limitare l'allocazione di memoria e le risorse della CPU a livello di istanza.
Per avere il supporto per una configurazione di questo tipo per le macchine virtuali di Azure, è previsto che i dischi o i volumi usati per i file di log/redo dei database gestiti dalle diverse istanze siano separati. In altre parole, i dati o i file di log di log/redo dei database gestiti da un'istanza DBMS diversa non dovrebbero condividere gli stessi dischi o volumi. 

La configurazione del disco per le istanze di grandi dimensioni HANA viene fornita configurata ed è descritta in dettaglio in [Scenari supportati per istanze di grandi dimensioni HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) 

> [!NOTE]
> Per i sistemi SAP di produzione, è consigliabile aggiungere ulteriori configurazioni di disponibilità elevata ed eventuale ripristino di emergenza, come descritto più avanti in questo documento. Le macchine virtuali con più istanze DBMS non sono supportate con le configurazioni a disponibilità elevata descritte più avanti in questo documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Più istanze della finestra di dialogo SAP in una macchina virtualeMultiple SAP Dialog instances in one VM
In molti casi, più istanze della finestra di dialogo sono state distribuite su server bare metal o anche in macchine virtuali in esecuzione in cloud privati. Il motivo di tali configurazioni è stato quello di adattare alcune istanze della finestra di dialogo SAP a determinati tipi di carico di lavoro, funzionalità aziendali o tipi di carico di lavoro. Motivo per non isolare tali istanze in macchine virtuali separate è stato lo sforzo di manutenzione e operazioni del sistema operativo. O in numerosi casi i costi nel caso in cui l'hoster o l'operatore della macchina virtuale sta chiedendo un canone mensile per VM gestito e amministrato. In Azure, uno scenario di hosting di più istanze di finestra di dialogo SAP all'interno di una singola macchina virtuale us supportato per scopi di produzione e non di produzione nei sistemi operativi di Windows, Red Hat, SUSE e Oracle Linux. Il parametro del kernel SAP PHYS_MEMSIZE, disponibile nei kernel Windows e Linux moderni, deve essere impostato se più istanze del server applicazioni SAP sono in esecuzione in una singola macchina virtuale. Si consiglia inoltre di limitare l'espansione della memoria estesa SAP sui sistemi operativi, come Windows in cui viene implementata la crescita automatica della memoria estesa SAP. Questa operazione può essere eseguita con il parametro `em/max_size_MB`del profilo SAP.

Nella configurazione a 3 livelli in cui vengono eseguite più istanze della finestra di dialogo SAP all'interno di macchine virtuali di Azure può essere simile al seguente:At 3-Tier configuration where multiple SAP dialog instances are run within Azure VMs can look like:

![Più istanze DBMS in un'unica unitàMultiple DBMS instances in one unit](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Per la semplificazione, non sono stati compresi tra le istanze delle finestre di dialogo SAP Central Services e SAP nel livello dell'applicazione SAP. In questa semplice configurazione a 3 livelli, non vi sarebbe alcuna protezione della disponibilità elevata per SAP Central Services. Per i sistemi di produzione, non è consigliabile lasciare sap Central Services non protetti. Per informazioni specifiche sulle cosiddette configurazioni multi-SID relative alle istanze sap Central e sulla disponibilità elevata di tali configurazioni multi-SID, vedere le sezioni successive di questo documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Protezione della disponibilità elevata per il livello SAP DBMS
Quando si guarda alla distribuzione di sistemi di produzione SAP, è necessario considerare il tipo hot standby delle configurazioni a disponibilità elevata. Soprattutto con SAP HANA, in cui i dati devono essere caricati in memoria prima di poter ottenere le prestazioni complete e la scalabilità, la correzione del servizio Di Azure non è una misura ideale per la disponibilità elevata. 

In generale Microsoft supporta solo le configurazioni a disponibilità elevata e i pacchetti software descritti nella sezione Carico di lavoro SAP di docs.microsoft.com. È possibile leggere la stessa dichiarazione nella nota SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Microsoft non fornirà supporto per altri framework software di terze parti ad alta disponibilità non documentati da Microsoft insieme al carico di lavoro SAP. In questi casi, il fornitore di terze parti del framework di disponibilità elevata è la parte di supporto per la configurazione di disponibilità elevata che deve essere assunto dall'utente come cliente nel processo di supporto. Le eccezioni saranno menzionate in questo articolo. 

In generale Microsoft supporta un set limitato di configurazioni a disponibilità elevata nelle macchine virtuali di Azure o nelle unità di istanze di grandi dimensioni HANA. Per gli scenari supportati delle istanze di grandi dimensioni HANA, leggere il documento [Scenari supportati per le istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Per le macchine virtuali di Azure, le configurazioni di disponibilità elevata seguenti sono supportate a livello di DBMS:For Azure VMs, the following high availability configurations are supported on DBMS level:

- Replica del sistema SAP HANA basata su Linux Pacemaker su SUSE e Red Hat. Vedere gli articoli dettagliati:
    - [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- Le configurazioni di n-m con scalabilità orizzontale SAP HANA usano [i file NetApp](https://azure.microsoft.com/services/netapp/) di Azure su SUSE e Red Hat. I dettagli sono elencati in questi articoli:
    - [Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file NetApp di Azure in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file di Azure NetApp in Red Hat Enterprise LinuxDeploy a SAP HANA scale-out system with standby node on Azure VMs by using Azure NetApp Files on Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- SQL Server Failover cluster based on Windows Scale-Out File Services. Anche se consigliper per i sistemi di produzione è quello di utilizzare SQL Server Always On invece di clustering. SQL Server Always On offre una migliore disponibilità utilizzando l'archiviazione separata. I dettagli sono descritti in questo articolo: 
    - [Configurare un'istanza del cluster di failover di SQL Server nelle macchine virtuali di AzureConfigure a SQL Server failover cluster instance on Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- SQL Server Always On is supported with the Windows operating system for SQL Server on Azure. Questa è la raccomandazione predefinita per le istanze di SQL Server di produzione in Azure.This is the default recommendation for production SQL Server instances on Azure. I dettagli sono descritti in questi articoli:Details are described in these articles:
    - Introduzione ai gruppi di disponibilità AlwaysOn di SQL Server nelle macchine virtuali di [Azure.Introducing SQL Server Always On availability groups on Azure virtual machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
    - [Configurare un gruppo di disponibilità AlwaysOn AlwaysOn nelle macchine virtuali di Azure in aree diverse.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
    - [Configurare un servizio di bilanciamento del carico per un gruppo di disponibilità AlwaysOnAlways On availability group in Azure.Configure a load balancer for an Always On availability group in Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)
- Oracle Data Guard per Windows e Oracle Linux. I dettagli per Oracle Linux sono disponibili in questo articolo:Details for Oracle Linux can be found in this article:
    - [Implementare Oracle Data Guard su una macchina virtuale Linux di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR su SUSE e RHEL La documentazione dettagliata per SUSE e RHEL utilizzando Pacemaker è disponibile qui:
    - [Disponibilità elevata di IBM Db2 LUW in macchine virtuali di Azure su SUSE Linux Enterprise Server con Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Disponibilità elevata di IBN Db2 LUW in macchine virtuali di Azure su Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Configurazione di SAP ASE e SAP maxDB come descritto in dettaglio in questi documenti:
    - [Distribuzione DBMS per SAP ASE di Macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [Distribuzione di SAP MaxDB, SAP liveCache e SAP Content Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Gli scenari di disponibilità elevata delle istanze di grandi dimensioni HANA sono descritti in dettaglio:HANA Large Instances high availability scenarios are detailed in:
    - [Scenari supportati per istanze di grandi dimensioni HANA - HSR con STONITH per la disponibilità elevataSupported scenarios for HANA Large Instances- HSR with STONITH for high availability](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Scenari supportati per le istanze di grandi dimensioni HANA - Failover automatico dell'host (1-1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Per nessuno degli scenari descritti in precedenza, sono supportate configurazioni di più istanze DBMS in una sola macchina virtuale. In ogni caso, è possibile distribuire una sola istanza di database per ogni macchina virtuale e protetta con i metodi di disponibilità elevata descritti. La protezione di più istanze DBMS nello stesso cluster di failover di Windows o Pacemaker **NON** è supportata in questo momento. Oracle Data Guard è supportato anche per i casi di distribuzione di una singola istanza per ogni macchina virtuale. 

Vari sistemi di database consentono di ospitare più database in un'istanza DBMS. Come nel caso di SAP HANA, più database possono essere ospitati in più contenitori di database (MDC). Per i casi in cui queste configurazioni con più database funzionano all'interno di una risorsa cluster di failover, queste configurazioni sono supportate. Le configurazioni non supportate sono i casi in cui sono necessarie più risorse cluster. Per quanto riguarda le configurazioni in cui è necessario definire più gruppi di disponibilità di SQL Server, in un'unica istanza di SQL Server.As for configurations where you would define multiple SQL Server Availability Groups, under one SQL Server instance.


![Configurazione DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

A seconda del sistema DBMS e/o operativo, componenti come Azure load balancer potrebbero essere necessari o meno come parte dell'architettura della soluzione. 

In particolare per maxDB, la configurazione di archiviazione deve essere diversa. In maxDB i file di dati e di log devono trovarsi nell'archiviazione condivisa per le configurazioni a disponibilità elevata. Solo nel caso di maxDB, l'archiviazione condivisa è supportata per la disponibilità elevata. Per tutti gli altri stack di archiviazione separati DBMS per nodo sono le uniche configurazioni disco supportate.

Altri framework di disponibilità elevata sono noti per esistere e sono noti per l'esecuzione in Microsoft Azure pure. Tuttavia, Microsoft non ha testato tali quadri. Se si desidera creare la configurazione a disponibilità elevata con tali framework, è necessario collaborare con il provider di tale software per:

- Sviluppare un'architettura di distribuzioneDevelop a deployment architecture
- Distribuzione dell'architettura
- Supporto dell'architettura

> [!IMPORTANT]
> Microsoft Azure Marketplace offre un'ampia gamma di appliance soft che forniscono soluzioni di archiviazione in un'archiviazione nativa di Azure.Microsoft Azure Marketplace offers a variety of soft appliances that provide storage solutions on top of Azure native storage. Queste appliance soft possono essere utilizzate per creare condivisioni NFS, che teoricamente potrebbero essere utilizzate nelle distribuzioni con scalabilità orizzontale SAP HANA in cui è necessario un nodo standby. Per vari motivi, nessuna di queste appliance software di archiviazione è supportata per qualsiasi distribuzione DBMS da Microsoft e SAP in Azure. Le distribuzioni di DBMS su condivisioni SMB non sono affatto supportate in questo momento. Le distribuzioni di DBMS in condivisioni NFS sono limitate alle condivisioni NFS 4.1 nei file NetApp di [Azure.](https://azure.microsoft.com/services/netapp/)


## <a name="high-availability-for-sap-central-service"></a>Disponibilità elevata per il servizio SAP Central
SAP Central Services è un secondo singolo punto di errore della configurazione SAP. Di conseguenza, è necessario proteggere anche questi processi di Servizi centrali. L'offerta supportata e documentata per il carico di lavoro SAP è simile alla:

- Server cluster di failover di Windows che utilizza Servizi file di scalabilità orizzontale di Windows per la directory di trasporto globale e sapmnt. I dettagli sono descritti nell'articolo:
    - [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Server cluster di failover di Windows usando la condivisione SMB basata su [File NetApp](https://azure.microsoft.com/services/netapp/) di Azure per la directory di trasporto globale e sapmnt. I dettagli sono elencati nell'articolo:
    - [Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in Windows con Azure NetApp Files (SMB) per le applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on Windows with Azure NetApp Files(SMB) for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Server cluster di failover di `Datakeeper`Windows basato su SIOS . Anche se documentato da Microsoft, è necessario una relazione di supporto con SIOS, in modo che è possibile interagire con il supporto SIOS quando si utilizza questa soluzione. I dettagli sono descritti nell'articolo:
    - [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Pacemaker nel sistema operativo SUSE con la creazione di una `drdb` condivisione NFS a disponibilità elevata utilizzando due macchine virtuali SUSE e per la replica dei file. I dettagli sono documentati nell'articolo
    - [Disponibilità elevata per SAP NetWeaver su macchina virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Pacemaker Sistema operativo SUSE con condivisione NFS fornite da [File NetApp](https://azure.microsoft.com/services/netapp/)di Azure. I dettagli sono documentati in
    - [Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in SUSE Linux Enterprise Server con file NetApp di Azure per le applicazioni SAPHigh availability for SAP NetWeaver on Azure VMs on SUSE Linux Enterprise Server with Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Pacemaker sul sistema operativo Red Hat con `glusterfs` condivisione NFS ospitata in un cluster. I dettagli sono disponibili negli articoli
    - [Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`nelle macchine virtuali di Azure in Red Hat Enterprise Linux per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Pacemaker nel sistema operativo Red Hat con condivisione NFS ospitata in [File NetApp](https://azure.microsoft.com/services/netapp/)di Azure . I dettagli sono descritti nell'articolo
    - [Disponibilità elevata di Macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con file NetApp di Azure per le applicazioni SAPAzure Virtual Machines high availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Tra le soluzioni elencate, è necessaria una relazione `Datakeeper` di supporto con SIOS per supportare il prodotto e interagire direttamente con SIOS in caso di problemi. A seconda del modo in cui hai concesso in licenza Windows, Red Hat e/o SUSE OS, potrebbe anche essere necessario avere un contratto di supporto con il provider del sistema operativo per avere il supporto completo delle configurazioni di disponibilità elevata elencate.

La configurazione può anche essere visualizzata come:

![Configurazione DI DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

Sul lato destro della grafica, viene visualizzato il SAP Central Services a disponibilità elevata. Oltre a proteggere i servizi SAP Central con un framework del cluster di failover in grado di eseguire il failover in caso di problemi, è necessario una condivisione NFS o SMB a disponibilità elevata o un disco condiviso di Windows per assicurarsi che la directory di trasporto globale e sapmnt disponibili indipendentemente dall'esistenza di una singola macchina virtuale. Altre soluzioni, ad esempio Windows Failover Cluster Server e Pacemaker, richiederanno un servizio di bilanciamento del carico di Azure per indirizzare o reindirizzare il traffico a un nodo integro.

Nell'elenco mostrato, non vi è alcuna menzione del sistema operativo Oracle Linux. Oracle Linux non supporta Pacemaker come framework cluster. Se si desidera distribuire il sistema SAP su Oracle Linux ed è necessario un framework di disponibilità elevata per Oracle Linux, è necessario collaborare con fornitori di terze parti. Uno dei fornitori è SIOS con la suite di protezione per Linux supportata da SAP in Azure. Per ulteriori informazioni, leggere nota #1662610 SAP [- Dettagli di supporto per SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610) per ulteriori dettagli.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Archiviazione supportata con gli scenari SAP Central Services elencati in precedenza
Poiché solo un sottoinsieme di tipi di archiviazione di Azure fornisce condivisioni NFS o SMB a disponibilità elevata tale qualità per l'utilizzo negli scenari cluster di SAP Central Services, un elenco dei tipi di archiviazione supportati

- Windows Failover Cluster Server with Windows Scale-out File Server can be deployed on all native Azure storage types, except Azure NetApp Files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio superiori in velocità effettiva e iOPS.
- Windows Failover Cluster Server with SMB on Azure NetApp Files is supported on Azure NetApp Files. Le condivisioni SMB nei servizi File di Azure **NON** sono supportate in questo momento.
- Il server Cluster di Failover di Windows `Datakeeper` con disco condiviso windows basato su SIOS può essere distribuito in tutti i tipi di archiviazione di Azure nativi, ad eccezione di File di Azure NetApp.Windows Failover Cluster Server with windows shared disk based on SIOS can be deployed on all native Azure storage types, except Azure NetApp Files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio superiori in velocità effettiva e iOPS.
- SUSE or Red Hat Pacemaker using NFS shares on Azure NetApp Files is supported on Azure NetApp Files. 
- SUSE Pacemaker `drdb` usando una configurazione tra due macchine virtuali è supportato usando tipi di archiviazione di Azure nativi, ad eccezione dei file NetApp di Azure.SUSE Pacemaker using a configuration between two VMs is supported using native Azure storage types, except Azure NetApp Files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio superiori in velocità effettiva e iOPS.
- Red Hat Pacemaker usato per fornire la condivisione NFS è supportato utilizzando i tipi di archiviazione di Azure nativi, ad eccezione di file di Rete di Azure.Red Hat Pacemaker using `glusterfs` for providing NFS share is supported using native Azure storage types, except Azure NetApp Files. Tuttavia, è consigliabile sfruttare l'archiviazione Premium a causa di contratti di servizio superiori in velocità effettiva e iOPS.

> [!IMPORTANT]
> Microsoft Azure Marketplace offre un'ampia gamma di appliance soft che forniscono soluzioni di archiviazione in un'archiviazione nativa di Azure.Microsoft Azure Marketplace offers a variety of soft appliances that provide storage solutions on top of Azure native storage. Queste appliance soft possono essere utilizzate anche per creare condivisioni NFS o SMB che teoricamente potrebbero essere utilizzate anche nei servizi SAP Central del cluster di failover. Queste soluzioni non sono supportate direttamente per il carico di lavoro SAP da Microsoft.These solutions are not directly supported for SAP workload by Microsoft. Se si decide di utilizzare una soluzione di questo tipo per creare la condivisione NFS o SMB, il supporto per la configurazione del servizio SAP Central deve essere fornito dalla terza parte proprietaria del software nell'appliance software di archiviazione.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Cluster di failover di Sap Central Services multi-SID
Per ridurre il numero di macchine virtuali necessarie in grandi paesaggi SAP, SAP consente di eseguire istanze dei servizi centrali SAP di più sistemi SAP diversi nella configurazione del cluster di failover. Immaginate i casi in cui si dispone di 30 o più sistemi di produzione NetWeaver o S/4HANA. Senza il clustering multi-SID, queste configurazioni richiederebbero 60 o più macchine virtuali in 30 o più configurazioni del cluster di failover di Windows o Pacemaker.Without multi-SID clustering, these configurations would require 60 o more VMs in 30 or more Windows or Pacemaker failover cluster configurations. Oltre ai cluster di failover DBMS necessari. La distribuzione di più servizi centrali SAP tra due nodi in una configurazione del cluster di failover può ridurre in modo significativo il numero di macchine virtuali. Tuttavia, la distribuzione di più istanze dei servizi SAP Central in una configurazione cluster a due nodi a due nodi presenta anche alcuni svantaggi. I problemi relativi a una singola macchina virtuale nella configurazione del cluster si applicano a più sistemi SAP. La manutenzione del sistema operativo guest in esecuzione nella configurazione del cluster richiede un maggiore coordinamento poiché sono interessati più sistemi SAP di produzione. Strumenti come SAP LaMa non supportano il clustering multi-SID nel processo di clonazione del sistema.

In Azure è supportata una configurazione cluster multi-SID per il sistema operativo Windows con ENSA1 e ENSA2. Si consiglia di non combinare l'architettura precedente del servizio di replica enqueue (ENSA1) con la nuova architettura (ENSA2) in un cluster multi-SID. I dettagli su tale architettura sono documentati negli articoli

- [Disponibilità elevata multi-SID dell'istanza SAP ASCS/SCS con Windows Server Failover Clustering e i dischi condivisi in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Disponibilità elevata multi-SID dell'istanza ASCS/SCS di SAP con Windows Server Failover Clustering e condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Per SUSE, è supportato anche un cluster multi-SID basato su Pacemaker. Finora la configurazione è supportata per:

- Un massimo di cinque istanze SAP ASCS/SCS
- L'architettura del ghiaccio del server di replica della coda precedente (ENSA1)
- Configurazioni cluster Pacemaker a due nodi

La configurazione è documentata in [Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP Applications multi-SID guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Un cluster multi-SID con il server di replica Enqueue si presenta schematicamente

![Configurazione DI DBMS e ASCS HA](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Scenari di scalabilità orizzontale SAP HANA
Gli scenari di scalabilità orizzontale SAP HANA sono supportati per un sottoinsieme delle macchine virtuali di Azure certificate HANA elencate nella [directory hardware SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Tutte le macchine virtuali contrassegnate con "Sì" nella colonna "Clustering" possono essere utilizzate per la scalabilità OLAP o S/4HANA. Le configurazioni senza standby sono supportate con i tipi di Archiviazione di Azure di:Configurations without standby are supported with the Azure Storage types of: 

- Archiviazione Premium di Azure, incluso l'acceleratore di scrittura di Azure per il volume /hana/logAzure Premium Storage, including Azure Write accelerator for the /hana/log volume
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

Le configurazioni di scalabilità orizzontale SAP HANA per OLAP o S/4HANA con nodi di standby sono supportate esclusivamente con NFS condiviso ospitato nei file NetApp di Azure.

Per ulteriori informazioni sulle configurazioni esatte di archiviazione con o senza nodo standby, consulta gli articoli:

- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file di Azure NetApp in SUSE Linux Enterprise ServerDeploy a SAP HANA scale-out system with standby node on Azure VMs by using Azure NetApp Files on SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file di Azure NetApp in Red Hat Enterprise LinuxDeploy a SAP HANA scale-out system with standby node on Azure VMs by using Azure NetApp Files on Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Nota del supporto SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Per informazioni dettagliate sulle configurazioni di scalabilità orizzontale HANA large Instances supportate, si applica la documentazione seguente:

- [Scenari supportati per la scalabilità orizzontale delle istanze di grandi dimensioni HANA con standbySupported scenarios for HANA Large Instances scale-out with standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Scenari supportati per la scalabilità orizzontale delle istanze di grandi dimensioni HANA senza standbySupported scenarios for HANA Large Instances scale-out without standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Scenario di ripristino di emergenza
Sono supportati diversi scenari di ripristino di emergenza. Definiamo le architetture di emergenza come architetture che devono compensare un'area di Azure completa che non è più veloce. Ciò significa che è necessario che l'obiettivo di ripristino di emergenza sia un'area di Azure diversa come destinazione per eseguire il panorama SAP. I metodi e le configurazioni vengono separati nel livello DBMS e nel livello non DBMS. 

### <a name="dbms-layer"></a>Livello DBMS
Per il livello DBMS, sono supportate configurazioni che utilizzano i meccanismi di replica nativa DBMS, ad esempio Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always-On o HANA System Replication. È obbligatorio che il flusso di replica in questi casi sia asincrono, anziché sincrono come in scenari di disponibilità elevata tipici distribuiti all'interno di una singola area di Azure.It is mandatory that the replication stream in such cases is asynchronous, instead of synchronous as in typical high availability scenarios that are deployed within a single Azure region. Un esempio tipico di tale configurazione di ripristino di emergenza DBMS supportata è descritto nell'articolo Disponibilità DI SAP HANA tra aree di Azure.A typical example of such a supported DBMS disaster recovery configuration is described in the [article SAP HANA availability across Azure regions.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions) Il secondo elemento grafico di tale sezione descrive uno scenario con HANA come esempio. I database principali supportati per le applicazioni SAP possono essere tutti distribuiti in uno scenario di questo tipo.

È supportato per usare una macchina virtuale più piccola come istanza di destinazione nell'area di ripristino di emergenza poiché tale macchina virtuale non si verifica il traffico del carico di lavoro completo. In questo modo, è necessario tenere presenti le seguenti considerazioni:

- Tipi di macchine virtuali più piccoli non consentono che molti dischi collegati rispetto alle macchine virtuali più piccole
- Le macchine virtuali più piccole hanno meno velocità di rete e di archiviazione
- Re-sizing across VM families can be a problem when the Different VMs are collected in one Azure Availability Set or when the re-sizing should happen between the M-Series family and Mv2 family of VMs
- Consumo di CPU e memoria per l'istanza del database in grado di ricevere il flusso di modifiche con un ritardo minimo e risorse di CPU e memoria sufficienti per applicare queste modifiche con un ritardo minimo ai dati  

Ulteriori dettagli sulle limitazioni di diverse dimensioni di VM sono disponibili [qui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Un altro metodo supportato per la distribuzione di una destinazione di ripristino di emergenza consiste nel installare una seconda istanza DBMS in una macchina virtuale che esegue un'istanza DBMS non di produzione di un'istanza SAP non di produzione. Questo può essere un po 'più impegnativo poiché è necessario capire che cosa su memoria, risorse della CPU, larghezza di banda di rete e larghezza di banda di archiviazione è necessario per le istanze di destinazione particolari che dovrebbero funzionare come istanza principale nello scenario di ripristino di emergenza. In particolare in HANA è consigliabile configurare l'istanza che funge da destinazione di ripristino di emergenza in un host condiviso in modo che i dati non vengano precaricati nell'istanza di destinazione del ripristino di emergenza.

Per gli scenari di ripristino di emergenza ad istanza di grandi dimensioni, controllare questi documenti:For HANA Large Instance DR scenarios check these documents:

- [Singolo nodo con ripristino di emergenza tramite la replica di archiviazioneSingle node with DR using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Singolo nodo con ripristino di emergenza (multiuso) tramite la replica di archiviazioneSingle node with DR (multipurpose) using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Singolo nodo con ripristino di emergenza (multiuso) tramite la replica di archiviazioneSingle node with DR (multipurpose) using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazioneHigh availability with HSR and DR with storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazioneScale-out with DR using storage replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Singolo nodo con DR con HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [Singolo nodo da HSR a DR (costo ottimizzato)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Disponibilità elevata e ripristino di emergenza con HSRHigh availability and disaster recovery with HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Disponibilità elevata e ripristino di emergenza con HSR (ottimizzazione dei costi)High availability and disaster recovery with HSR (cost optimized)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Scalabilità orizzontale con DR con HSRScale-out with DR using HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> L'utilizzo di [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) non è stato testato per le distribuzioni DBMS nel carico di lavoro SAP. Di conseguenza, al momento non è supportato per il livello DBMS dei sistemi SAP. Altri metodi di replica da Microsoft e SAP che non sono elencati non sono supportati. L'utilizzo di software di terze parti per la replica del livello DBMS dei sistemi SAP tra diverse aree di Azure deve essere supportato dal fornitore del software e non sarà supportato tramite i canali di supporto Microsoft e SAP. 
 
## <a name="non-dbms-layer"></a>Livello non DBMS
Per il livello dell'applicazione SAP e le eventuali condivisioni o percorsi di archiviazione necessari, i due scenari principali vengono sfruttati dai clienti:

- Gli obiettivi di ripristino di emergenza nella seconda area di Azure non vengono usati per scopi di produzione o non di produzione. In questo scenario, le macchine virtuali che funzionano come destinazione di ripristino di emergenza idealmente non vengono distribuite e l'immagine e le modifiche alle immagini del livello applicazione SAP di produzione vengono replicate nell'area di ripristino di emergenza. Una funzionalità in grado di eseguire un'attività di questo tipo è [Azure Site Recovery.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview) Azure Site Recovery supporta scenari di replica da Azure ad Azure come questo. 
- Gli obiettivi di ripristino di emergenza sono macchine virtuali effettivamente utilizzate da sistemi non di produzione. L'intero panorama SAP è distribuito in due diverse aree di Azure con sistemi di produzione in genere in un'area e sistemi non di produzione in un'altra area. In molte distribuzioni dei clienti, il cliente dispone di un sistema non di produzione equivalente a un sistema di produzione. Il cliente dispone di istanze dell'applicazione di produzione preinstallate nei sistemi non di produzione a livello di applicazione. In caso di failover, le istanze non di produzione verrebbero arrestate, i nomi virtuali delle macchine virtuali di produzione spostati nelle macchine virtuali non di produzione (dopo l'assegnazione di nuovi indirizzi IP in DNS) e le istanze di produzione preinstallate vengono avviate

### <a name="sap-central-services-clusters"></a>Cluster sap Central Services
I cluster SAP Central Services che utilizzano dischi condivisi (Windows), condivisioni SMB (Windows) o condivisioni NFS sono un po' più difficili da replicare. Sul lato Windows, Windows Storage Replication è una possibile soluzione. Su Linux rsync è una soluzione praticabile.



## <a name="non-supported-scenario"></a>Scenario non supportato
È disponibile un elenco di scenari che non sono supportati per il carico di lavoro SAP nelle architetture di Azure.There is a list of scenario, which are not supported for SAP workload on Azure architectures. **Non supportato** significa che SAP e Microsoft non saranno in grado di supportare queste configurazioni e devono rimandare a un'eventuale terza parte coinvolta che ha fornito software per stabilire tali architetture. Due delle categorie sono:

- Appliance software di archiviazione: in Azure offre numerose appliance software di archiviazione. Alcuni fornitori offrono la propria documentazione su come usare tali dispositivi software di archiviazione in Azure correlati al software SAP. Il supporto di configurazioni o distribuzioni che coinvolgono tali appliance software di storage deve essere fornito dal fornitore di tali appliance software di archiviazione. Questo fatto si manifesta anche nella [nota del supporto SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- High Availability frameworks: Only Pacemaker and Windows Server Failover Cluster are supported high availability frameworks for SAP workload on Azure. Come accennato in precedenza, la soluzione di SIOS `Datakeeper` è descritta e documentata da Microsoft. Tuttavia, i componenti `Datakeeper` di SIOS devono essere supportati tramite SIOS come fornitore che fornisce tali componenti. SAP ha inoltre elencato altri framework certificati per la disponibilità elevata in varie note SAP. Alcuni di essi sono stati certificati dal fornitore di terze parti per Azure pure. Tuttavia, il supporto per le configurazioni che utilizzano tali prodotti deve essere fornito dal fornitore del prodotto. Fornitori diversi hanno un'integrazione diversa nei processi di supporto SAP. È necessario chiarire il processo di supporto più adatto per il fornitore specifico prima di decidere di usare il prodotto nelle configurazioni SAP distribuite in Azure.You should clarify what support process works best for the particular vendor before deciding to use the product in SAP configurations deployed on Azure.
- I cluster di dischi condivisi in cui i file di database risiedono nei dischi condivisi non sono supportati ad eccezione di maxDB. Per tutti gli altri database, la soluzione supportata prevede percorsi di archiviazione separati anziché una condivisione SMB o NFS o un disco condiviso per configurare scenari a disponibilità elevata

Altri scenari, che non sono supportati, sono scenari come:

- Scenari di distribuzione che introducono una latenza di rete maggiore tra il livello applicazione SAP e il livello SAP DBMS `Hybris`nell'architettura comune di SAP, come illustrato in NetWeaver, S/4HANA ed e.g. ad esempio:
    - Distribuzione di uno dei livelli in locale mentre l'altro livello viene distribuito in AzureDeploying one of the tiers on-premise whereas the other tier is deployed in Azure
    - Distribuzione del livello applicazione SAP di un sistema in un'area di Azure diversa rispetto al livello DBMSDeploying the SAP application tier of a system in a different Azure region than the DBMS tier
    - Distribuzione di un livello nei data center che si trovano in Azure e l'altro livello in Azure, ad eccezione dei casi in cui tali modelli di architettura vengono forniti da un servizio nativo di AzureDeploying one tier in datacenters that are co-located to Azure and the other tier in Azure, except where such an architecture patterns are provided by an Azure native service
    - Distribuzione di appliance virtuali di rete tra il livello applicazione SAP e il livello DBMS
    - Utilizzo dello spazio di archiviazione ospitato in data center collocati in Azure per il livello SAP DBMS o la directory di trasporto globale SAP
    - Distribuzione dei due livelli con due diversi fornitori di cloud. Ad esempio, la distribuzione del livello DBMS nell'infrastruttura Oracle Cloud e del livello applicazione in AzureFor example, deploying the DBMS tier in Oracle Cloud Infrastructure and the application tier in Azure
- Configurazioni cluster HANA Pacemaker a più istanze
- Configurazioni del cluster di Windows con dischi condivisi tramite SOFS o SMB su ANF per database SAP supportati in Windows. È invece consigliabile utilizzare la replica nativa a disponibilità elevata dei database specifici e utilizzare stack di archiviazione separati
- Distribuzione di database SAP supportati su Linux con file di database che si trovano in condivisioni NFS sopra ANF, ad eccezione di SAP HANA
- Distribuzione di Oracle DBMS in qualsiasi altro sistema operativo guest diverso da Windows e Oracle Linux. Vedere anche [nota del supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Scenari che non abbiamo testato e quindi non abbiamo esperienza con l'elenco come:

- Macchine virtuali del livello DBMS di Azure Site Recovery di replica. Di conseguenza, è consigliabile sfruttare la funzionalità di replica asincrona nativa del database per una potenziale configurazione di ripristino di emergenza
 

## <a name="next-steps"></a>Passaggi successivi
Leggere i passaggi successivi nella pianificazione e nell'implementazione di Macchine virtuali di [Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  




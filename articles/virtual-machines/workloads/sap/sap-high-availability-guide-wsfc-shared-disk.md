---
title: Istanza di SAP ASC/SCS del cluster in WSFC usando un disco condiviso in Azure | Microsoft Docs
description: Informazioni sul clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b286812ba0a418d74738837fd5cfb7a7b617a9fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854443"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure

> ![Sistema operativo Windows][Logo_Windows] Windows
>

Windows Server Failover Clustering è alla base di un'istallazione ASCS/SCS di SAP a disponibilità elevata e di un sistema DBMS in Windows.

Un cluster di failover è un gruppo di 1 + n server, ovvero nodi, indipendenti che funzionano insieme per aumentare la disponibilità di applicazioni e servizi. Se si verifica un errore in un nodo, Windows Server Failover Clustering calcola il numero di errori che possono verificarsi e mantiene un cluster integro per fornire applicazioni e servizi. A questo scopo è possibile scegliere tra diverse modalità quorum per ottenere il clustering di failover.

## <a name="prerequisites"></a>Prerequisiti
Prima di svolgere le attività descritte in questo articolo, leggere l'articolo seguente:

* [Scenari e architettura di disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server Failover Clustering in Azure

Windows Server failover clustering con macchine virtuali di Azure richiede passaggi di configurazione aggiuntivi. Quando si crea un cluster, è necessario impostare diversi indirizzi IP e nomi host virtuali per l'istanza ASCS/SCS di SAP.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Risoluzione dei nomi in Azure e nome host virtuale del cluster

La piattaforma cloud di Azure non consente di configurare indirizzi IP virtuali, ad esempio indirizzi IP mobili. È necessaria una soluzione alternativa per configurare un indirizzo IP virtuale per poter raggiungere la risorsa cluster nel cloud. 

Il servizio Azure Load Balancer include un servizio di *bilanciamento del carico interno* per Azure. Con il servizio di bilanciamento del carico interno, i client raggiungono il cluster tramite l'indirizzo IP virtuale del cluster. 

È necessario distribuire il servizio di bilanciamento del carico interno nel gruppo di risorse che contiene i nodi del cluster. Configurare quindi tutte le necessarie regole di port forwarding usando le porte probe del servizio di bilanciamento del carico interno. I client possono connettersi tramite il nome host virtuale. Il server DNS risolve l'indirizzo IP del cluster e il servizio di bilanciamento del carico interno gestisce il port forwarding al nodo attivo del cluster.

![Figura 1: Configurazione del servizio Clustering di failover di Windows in Azure senza disco condiviso][sap-ha-guide-figure-1001]

_Configurazione di Windows Server failover clustering in Azure senza disco condiviso_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>Disponibilità elevata di SAP ASCS/SCS con i dischi condivisi del cluster
In Windows, un'istanza di SAP ASCS/SCS contiene i servizi centrali SAP, il server di messaggistica SAP, i processi del server di accodamento e i file host globali di SAP. I file host globali di SAP archiviano i file centrali per l'intero sistema SAP.

Un'istanza di SAP ASCS/SCS include i componenti seguenti:

* Servizi centrali SAP:
    * Due processi, un server di messaggi e di Accodamento e un oggetto \<ASCS/SCS virtual host name> , usato per accedere a questi due processi.
    * Struttura di file: S:\usr\sap \\ &lt; SID &gt; \ ASC/SCS\<instance number\>


* File host globali di SAP:
  * Struttura del file: S:\usr\sap\\&lt;SID&gt;\SYS\..
  * La condivisione file sapmnt, che consente l'accesso a questi file globali S:\usr\sap\\&lt;SID&gt;\SYS\... usando il percorso UNC seguente:

    \\\\<nome host virtuale ASC/SCS \> \Sapmnt \\ &lt; SID &gt; \sys \. ..


![Figura 2: Processi, struttura del file e condivisione file sapmnt host globale di un'istanza di SAP ASCS/SCS][sap-ha-guide-figure-8001]

_Processi, struttura dei file e condivisione file sapmnt host globale di un'istanza di SAP ASC/SCS_

In un'impostazione con disponibilità elevata eseguire il clustering delle istanze di SAP ASCS/SCS. Per inserire i file SAP ASCS/SCS e i file host globali di SAP vengono usati i *dischi condivisi del cluster*, unità S nel nostro esempio.

![Figura 3: Architettura a disponibilità elevata di SAP ASCS/SCS con disco condiviso][sap-ha-guide-figure-8002]

_Architettura a disponibilità elevata di SAP ASC/SCS con disco condiviso_


Con l'architettura di replica server di Accodamento 1:
* Lo stesso \<ASCS/SCS virtual host name> viene usato per accedere ai processi del server e di Accodamento dei messaggi SAP e dei file dell'host globale SAP tramite la condivisione file sapmnt.
* Lo stesso disco condiviso del cluster S viene condiviso tra loro.  

Con l'architettura di replica 2 del server di Accodamento: 
* Lo stesso \<ASCS/SCS virtual host name> viene usato per accedere al processo del server dei messaggi SAP e ai file dell'host globale SAP tramite la condivisione file sapmnt.
* Lo stesso disco condiviso del cluster S viene condiviso tra loro.
* È disponibile \<ERS virtual host name> un separato per accedere al processo del server di Accodamento  

![Figura 4: Architettura a disponibilità elevata di SAP ASCS/SCS con disco condiviso][sap-ha-guide-figure-8003]

_Architettura a disponibilità elevata di SAP ASC/SCS con disco condiviso_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Disco condiviso e server di replica di Accodamento 

1. Il disco condiviso è supportato con l'architettura della replica server di Accodamento 1, in cui l'istanza del server di replica di Accodamento (ERS):   

   - non è in cluster
   - Usa il `localhost` nome
   - viene distribuito nei dischi locali in ognuno dei nodi del cluster

2. Il disco condiviso è supportato anche con l'architettura della replica 2 del server di Accodamento, in cui l'istanza del server di replica di Accodamento 2 (ERS2):  

   - in cluster
   - Usa il nome host virtuale/rete dedicato
   - richiede che l'indirizzo IP del nome host virtuale ERS sia configurato in Load Balancer interni di Azure, oltre all'indirizzo IP (A) SCS
   - viene distribuito nei **dischi locali** in ognuno dei nodi del cluster, pertanto non è necessario un disco condiviso

   > [!TIP]
   > Per ulteriori informazioni su come accodare il server di replica 1 e 2 (ERS1 e ERS2), vedere:  
   > [Accodare il server di replica in un cluster di failover Microsoft](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Nuovo replicatore di Accodamento negli ambienti cluster di failover](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Opzioni per i dischi condivisi in Azure per i carichi di lavoro SAP

Sono disponibili due opzioni per il disco condiviso in un cluster di failover di Windows in Azure:

- [Azure Shared disks](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared) : funzionalità che consente di aggiungere il disco gestito di Azure a più macchine virtuali contemporaneamente. 
- Uso del software di terze parti [Datakeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster) per creare una risorsa di archiviazione con mirroring che simula l'archiviazione condivisa del cluster. 

Quando si seleziona la tecnologia per il disco condiviso, tenere presenti le considerazioni seguenti:

**Disco condiviso di Azure per carichi di lavoro SAP**
- Consente di alleghi il disco gestito di Azure a più macchine virtuali contemporaneamente senza la necessità di software aggiuntivo per la manutenzione e il funzionamento 
- Si funzionerà con un singolo disco condiviso di Azure in un cluster di archiviazione. Ciò influisca sull'affidabilità della soluzione SAP.
- Attualmente, l'unica distribuzione supportata è il disco Premium condiviso di Azure in un set di disponibilità. Il disco condiviso di Azure non è supportato nella distribuzione di zona.     
- Assicurarsi di eseguire il provisioning di un disco Premium di Azure con dimensioni minime del disco come specificato in [SSD Premium intervalli](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared#disk-sizes) per potersi associare al numero di VM necessario simultaneamente (in genere 2 per il cluster di failover di SAP ASC Windows). 
- Il disco Ultra condiviso di Azure non è supportato per i carichi di lavoro SAP, perché non supporta la distribuzione nel set di disponibilità o nella distribuzione di zona.  
 
**SIOS**
- La soluzione di questo periodo fornisce la replica dei dati sincroni in tempo reale tra due dischi
- Con la soluzione per i tipi di unità di gestione che si opera con due dischi gestiti e se si usano i set di disponibilità o le zone di disponibilità, i dischi gestiti si troveranno in cluster di archiviazione diversi. 
- La distribuzione in zone di disponibilità è supportata
- Richiede l'installazione e l'utilizzo di software di terze parti, che sarà necessario acquistare anche

### <a name="shared-disk-using-azure-shared-disk"></a>Disco condiviso con il disco condiviso di Azure

Microsoft offre [dischi condivisi di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared), che possono essere usati per implementare la disponibilità elevata di SAP ASC/SCS con un'opzione di disco condiviso.

#### <a name="prerequisites-and-limitations"></a>Prerequisiti e limiti

Attualmente è possibile usare i dischi di Azure SSD Premium come disco condiviso di Azure per l'istanza di SAP ASC/SCS. Sono attualmente disponibili le seguenti limitazioni:

-  Il [disco Azure ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) non è supportato come disco condiviso di Azure per i carichi di lavoro SAP. Attualmente non è possibile inserire macchine virtuali di Azure usando il disco Ultra di Azure in un set di disponibilità
-  Il [disco condiviso di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared) con dischi SSD Premium è supportato solo con le VM nel set di disponibilità. Non è supportata nella distribuzione di zone di disponibilità. 
-  Il valore [maxShares](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared-enable?tabs=azure-cli#disk-sizes) del disco condiviso di Azure determina il numero di nodi del cluster che possono usare il disco condiviso. In genere, per l'istanza di SAP ASC/SCS verranno configurati due nodi nel cluster di failover di Windows, pertanto il valore di `maxShares` deve essere impostato su due.
-  Tutte le macchine virtuali del cluster SAP ASC/SCS devono essere distribuite nello stesso [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups).   
   Sebbene sia possibile distribuire le macchine virtuali del cluster Windows nel set di disponibilità con il disco condiviso di Azure senza PPG, PPG assicurerà la vicinanza fisica dei dischi condivisi di Azure e delle macchine virtuali del cluster, ottenendo quindi una latenza inferiore tra le macchine virtuali e il livello di archiviazione.    

Per altri dettagli sulle limitazioni per il disco condiviso di Azure, vedere la sezione [limitazioni](https://docs.microsoft.com/azure/virtual-machines/linux/disks-shared#limitations) della documentazione relativa ai dischi condivisi di Azure.

> [!IMPORTANT]
> Quando si distribuisce un cluster di failover di Windows SAP ASC/SCS con disco condiviso di Azure, tenere presente che la distribuzione funzionerà con un singolo disco condiviso in un cluster di archiviazione. L'istanza di SAP ASC/SCS potrebbe avere un effetto, in caso di problemi con il cluster di archiviazione, in cui viene distribuito il disco condiviso di Azure.    

> [!TIP]
> Per considerazioni importanti, quando si pianifica la distribuzione di SAP, vedere la [Guida alla pianificazione di SAP NetWeaver in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) e la [Guida all'archiviazione di Azure per i carichi di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage) .

### <a name="supported-os-versions"></a>Versioni dei sistemi operativi supportate

Sono supportati sia Windows Server 2016 che 2019 (usare le immagini data center più recenti).

Si consiglia vivamente di utilizzare **Windows Server 2019 datacenter**, come:
- Il servizio cluster di failover di Windows 2019 è in grado di riconoscere Azure
- Sono stati aggiunti l'integrazione e la conoscenza della manutenzione dell'host di Azure e l'esperienza migliorata monitorando gli eventi di pianificazione di Azure.
- È possibile utilizzare il nome di rete distribuita (opzione predefinita). Non è pertanto necessario disporre di un indirizzo IP dedicato per il nome di rete del cluster. Non è inoltre necessario configurare questo indirizzo IP nel Load Balancer interno di Azure. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Dischi condivisi in Azure con SIOS DataKeeper

Un'altra opzione per il disco condiviso prevede l'uso di software di terze parti datakeeper cluster Edition per creare una risorsa di archiviazione con mirroring che simula lo spazio di archiviazione condiviso del cluster. La soluzione SIOS fornisce la replica di dati sincrona in tempo reale.

Per creare una risorsa disco condiviso per un cluster:

1. Collegare un disco aggiuntivo a ogni macchina virtuale in una configurazione cluster Windows.
2. Eseguire SIOS DataKeeper Cluster Edition in entrambi i nodi delle macchine virtuali.
3. Configurare SIOS DataKeeper Cluster Edition per eseguire il mirroring del contenuto del volume collegato del disco aggiuntivo dalla macchina virtuale di origine al volume collegato del disco aggiuntivo della macchina virtuale di destinazione. SIOS DataKeeper astrae i volumi locali di origine e di destinazione e quindi li presenta a Windows Server Failover Clustering come un disco condiviso.

Altre informazioni su [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Figura 5: Configurazione di Windows Server Failover Clustering in Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

_Configurazione del clustering di failover di Windows in Azure con il tipo di nodo datakeeper_

> [!NOTE]
> Non sono necessari dischi condivisi per la disponibilità elevata con alcuni prodotti DBMS, ad esempio SQL Server. La funzionalità SQL Server AlwaysOn replica i file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster al disco locale di un altro nodo del cluster. In questo caso, la configurazione del cluster Windows non richiede un disco condiviso.
>

## <a name="next-steps"></a>Passaggi successivi

* [Preparazione dell'infrastruttura di Azure per la disponibilità elevata di SAP con il cluster di failover Windows e i dischi condivisi per l'istanza SAP (A)SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Installazione della disponibilità elevata di SAP NetWeaver nel cluster di failover Windows e nei dischi condivisi per l'istanza SAP (A)SCS in Azure][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Configurazione della disponibilità elevata per SAP a più SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

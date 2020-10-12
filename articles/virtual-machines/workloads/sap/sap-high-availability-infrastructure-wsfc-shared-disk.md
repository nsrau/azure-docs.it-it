---
title: Infrastruttura di Azure per SAP ASC/SCS con WSFC&disco condiviso | Microsoft Docs
description: Informazioni su come preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per un'istanza di SAP ASCS/SCS.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f389581d8fbeb912507b303c46109dd08fcab8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88871517"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Sistema operativo Windows][Logo_Windows] Windows


Questo articolo descrive i passaggi necessari per preparare l'infrastruttura di Azure per l'installazione e la configurazione di un'istanza di SAP ASC/SCS a disponibilità elevata in un cluster di failover Windows tramite un *disco condiviso del cluster* come opzione per il clustering di un'istanza di SAP ASC.
Nella documentazione vengono presentate due alternative per i *dischi condivisi del cluster* :

- [Dischi condivisi di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-shared)
- Uso di il [clustering Datakeeper cluster Edition](https://us.sios.com/products/datakeeper-cluster/) per creare l'archiviazione con mirroring, che simula il disco condiviso del cluster 

La configurazione presentata si basa sui [gruppi di posizionamento di prossimità (PPG) di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) per ottenere una latenza di rete ottimale per i carichi di lavoro SAP. La documentazione non copre il livello di database.  

> [!NOTE]
> I gruppi di posizionamento di prossimità di Azure sono prerequisiti per l'uso del disco condiviso di Azure.
 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'installazione, esaminare questo articolo:

* [Architecture guide: Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk][sap-high-availability-guide-wsfc-shared-disk](Guida all'architettura: clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows che usa un disco condiviso del cluster)

## <a name="create-the-ascs-vms"></a>Creare le macchine virtuali ASC

Per il cluster SAP ASC/SCS distribuire due VM nel set di disponibilità di Azure. Distribuire le macchine virtuali nello stesso gruppo di posizionamento di prossimità. Una volta distribuite le macchine virtuali:  
- Creare Load Balancer interni di Azure per l'istanza di SAP ASC/SCS 
- Aggiungere macchine virtuali Windows al dominio AD

I nomi host e gli indirizzi IP per lo scenario presentato sono:

| Ruolo nome host | Nome host | Indirizzo IP statico | Set di disponibilità | Gruppo posizionamento prossimità |
| --- | --- | --- |---| ---|
| primo cluster ASC/SCS del nodo cluster |PR1-ASC-10 |10.0.0.4 |PR1-ASC-avset |PR1PPG |
| 2 ° nodo cluster ASC/SCS cluster |PR1-ASC-11 |10.0.0.5 |PR1-ASC-avset |PR1PPG |
| Nome rete di cluster | pr1clust |10.0.0.42 (**solo** per il cluster Win 2016) | n/d | n/d |
| Nome rete cluster ASC | PR1-ascscl |10.0.0.43 | n/d | n/d |
| Nome di rete del cluster ERS (**solo** per ERS2) | PR1-erscl |10.0.0.44 | n/d | n/d |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Creare un servizio di bilanciamento del carico interno di Azure

SAP ASC, SAP SCS e la nuova ERS2 SAP, usano il nome host virtuale e gli indirizzi IP virtuali. In Azure è necessario un servizio di [bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) per usare un indirizzo IP virtuale. Si consiglia vivamente di usare [Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). 


Nell'elenco seguente viene illustrata la configurazione del servizio di bilanciamento del carico (A) SCS/ERS. La configurazione di SAP ASC e ERS2 in è stata eseguita nello stesso servizio di bilanciamento del carico di Azure.  

**(A)SCS**
- Configurazione front-end
    - Indirizzo IP ASC/SCS statico **10.0.0.43**
- Configurazione back-end  
    Aggiungere tutte le macchine virtuali che devono far parte del cluster (A) SCS/ERS. In questo esempio VM **Pr1-ASC-10** e **Pr1-ASC-11**.
- Porta probe
    - Porta 620**Nr** lasciare l'opzione predefinita per protocollo (TCP), intervallo (5), soglia non integro (2)
- Regole di bilanciamento del carico
    - Se si usa Load Balancer Standard, selezionare Porte a disponibilità elevata
    - Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
        - 32**nr** TCP
        - 36**nr** TCP
        - 39**nr** TCP
        - 81**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - Verificare che il timeout di inattività (minuti) sia impostato sul valore max 30 e che l'indirizzo IP mobile (Direct Server Return) sia abilitato.

**ERS2**

Poiché il server di replica di Accodamento 2 (ERS2) è anche in cluster, è necessario configurare anche l'indirizzo IP virtuale ERS2 in Azure ILB, oltre a quello di SAP ASC/SCS IP. Questa sezione si applica solo se si usa l'architettura del server di replica di Accodamento 2.  
- seconda configurazione front-end
    - Indirizzo IP di SAP ERS2 statico **10.0.0.44**

- Configurazione back-end  
  Le macchine virtuali sono già state aggiunte al pool back-end ILB.  

- seconda porta Probe
    - Porta 621**nr**  
    Lasciare l'opzione predefinita per protocollo (TCP), intervallo (5), soglia non integro (2)

- seconda regola di bilanciamento del carico
    - Se si usa Load Balancer Standard, selezionare Porte a disponibilità elevata
    - Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
        - 32**nr** TCP
        - 33**nr** TCP
        - 5**nr**13 TCP
        - 5**nr**14 TCP
        - 5**nr**16 TCP

    - Verificare che il timeout di inattività (minuti) sia impostato sul valore max 30 e che l'indirizzo IP mobile (Direct Server Return) sia abilitato.


> [!TIP]
> Con il [modello di Azure Resource Manager per WSFC per l'istanza di SAP ASC/SCS con disco condiviso di Azure](https://github.com/robotechredmond/301-shared-disk-sap), è possibile automatizzare la preparazione dell'infrastruttura, usando un disco condiviso di Azure per un SID SAP con ERS1.  
> Il modello ARM di Azure creerà due macchine virtuali Windows 2019 o 2016, creerà il disco condiviso di Azure e si connetterà alle macchine virtuali. Verranno creati e configurati anche Load Balancer interni di Azure. Per informazioni dettagliate, vedere il modello ARM. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Aggiungere voci del registro di sistema in entrambi i nodi del cluster dell'istanza di ASC/SCS

Azure Load Balancer possibile chiudere le connessioni, se le connessioni sono inattive per un periodo e superano il timeout di inattività. Il lavoro SAP elabora le connessioni al processo di Accodamento SAP non appena deve essere inviata la prima richiesta di Accodamento/rimozione dalla coda. Per evitare di interrompere queste connessioni, modificare i valori TCP/IP KeepAliveTime e KeepAliveInterval in entrambi i nodi del cluster. Se si usa ERS1, è anche necessario aggiungere i parametri del profilo SAP, come descritto più avanti in questo articolo.
È necessario modificare le seguenti voci del registro di sistema in entrambi i nodi del cluster:

- KeepAliveTime
- KeepAliveInterval

| Percorso| Nome variabile | Tipo di variabile  | Valore | Documentazione |
| --- | --- | --- |---| ---|
| HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD (decimale) |120000 |[KeepAliveTime](https://technet.microsoft.com/library/cc957549.aspx) |
| HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD (decimale) |120000 |[KeepAliveInterval](https://technet.microsoft.com/library/cc957548.aspx) |


Per applicare le modifiche, riavviare entrambi i nodi del cluster.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Aggiungere le macchine virtuali Windows al dominio
Dopo aver assegnato gli indirizzi IP statici alle macchine virtuali, aggiungere le macchine virtuali al dominio. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Installare e configurare il cluster di failover di Windows 

### <a name="install-the-windows-failover-cluster-feature"></a>Installare la funzionalità cluster di failover di Windows

Eseguire questo comando in uno dei nodi del cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Al termine dell'installazione della funzionalità, riavviare entrambi i nodi del cluster.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testare e configurare il cluster di failover di Windows 

In Windows 2019, il cluster rileverà automaticamente che è in esecuzione in Azure e come opzione predefinita per l'IP di gestione cluster verrà usato il nome di rete distribuita. Pertanto, utilizzerà uno degli indirizzi IP locali dei nodi del cluster. Di conseguenza, non è necessario un nome di rete dedicato (virtuale) per il cluster e non è necessario configurare questo indirizzo IP nel Load Balancer interno di Azure.

Per ulteriori informazioni, vedere [Windows Server 2019 failover clustering New features](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029) eseguire questo comando in uno dei nodi del cluster:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Configurare il quorum del cloud del cluster
Quando si usa Windows Server 2016 o 2019, è consigliabile configurare [Azure cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)come quorum del cluster.

Eseguire questo comando in uno dei nodi del cluster:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Ottimizzazione delle soglie del cluster di failover di Windows
 
Dopo aver installato correttamente il cluster di failover di Windows, è necessario modificare alcune soglie per adattarle ai cluster distribuiti in Azure. I parametri da modificare sono documentati nel blog [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) (Definire le soglie di rete per il cluster di failover). Supponendo che le due VM che compongono la configurazione del cluster Windows per ASCS/SCS siano nella stessa subnet, modificare i parametri seguenti impostando i valori indicati:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Queste impostazioni sono state testate con i clienti e offrono un valido compromesso. Sono sufficientemente resilienti, ma forniscono anche il failover sufficientemente rapido per le condizioni di errore reale nei carichi di lavoro SAP o negli errori delle macchine virtuali.  

## <a name="configure-azure-shared-disk"></a>Configurare il disco condiviso di Azure
Questa sezione è applicabile solo se si usa il disco condiviso di Azure. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Creare e associare un disco condiviso di Azure con PowerShell
Eseguire questo comando in uno dei nodi del cluster. Sarà necessario modificare i valori per il gruppo di risorse, l'area di Azure, SAPSID e così via.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formattare il disco condiviso con PowerShell
1. Ottenere il numero del disco. Eseguire questi comandi di PowerShell in uno dei nodi del cluster:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formattare il disco. In questo esempio si tratta del disco numero 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Verificare che il disco sia ora visibile come disco del cluster.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registrare il disco nel cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> L'edizione del cluster di ASC/SCS per il disco di condivisione del cluster SAP/SCS
Questa sezione è applicabile solo se si usa il software di terze parti datakeeper cluster Edition per creare una risorsa di archiviazione con mirroring che simula il disco condiviso del cluster.  

A questo punto è disponibile una configurazione funzionante di Windows Server failover clustering in Azure. Per installare un'istanza di SAP ASCS/SCS, è necessaria una risorsa disco condiviso. Una delle opzioni consiste nell'usare l'edizione di clustering datakeeper del cluster è una soluzione di terze parti che è possibile usare per creare risorse disco condivise.  

L'installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS prevede queste attività:
- Se necessario, aggiungere Microsoft .NET Framework. Vedere la [documentazione relativa ai dati di riferimento] ( https://us.sios.com/products/datakeeper-cluster/) per i requisiti di .NET Framework più aggiornati 
-  Installare SIOS DataKeeper
- Configurare il filekeeper

### <a name="install-sios-datakeeper"></a> Installare SIOS DataKeeper
Installare SIOS DataKeeper Cluster Edition in ogni nodo del cluster. Per creare una risorsa di archiviazione condivisa virtuale con SIOS DataKeeper, creare un mirror sincronizzato e quindi simulare la risorsa di archiviazione condivisa del cluster.

Prima di installare il software SIOS, creare l'utente di dominio DataKeeperSvc.

> [!NOTE]
> Aggiungere l'utente DataKeeperSvc al gruppo Administrators locale in entrambi i nodi del cluster.
>

1. Installare il software SIOS in entrambi i nodi del cluster.

   ![Programma di installazione SIOS][sap-ha-guide-figure-3030]

   ![Figura 31: Prima schermata dell'installazione di SIOS DataKeeper][sap-ha-guide-figure-3031]

   _Prima pagina dell'installazione di il nome di oggetto di datakeeper_

2. Nella finestra di dialogo selezionare **Sì**.

   ![Figura 32: DataKeeper segnala che un servizio verrà disabilitato][sap-ha-guide-figure-3032]

   _Datakeeper informa che un servizio verrà disabilitato_

3. Nella finestra di dialogo si consiglia di selezionare **Domain or Server account** (Account di dominio o server).

   ![Figura 33: Selezione dell'utente per SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Selezione dell'utente per SIOS DataKeeper_

4. Specificare il nome utente dell'account di dominio e la password creati per SIOS DataKeeper.

   ![Figura 34: Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper][sap-ha-guide-figure-3034]

   _Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper_

5. Installare la chiave di licenza per l'istanza di SIOS DataKeeper, come illustrato nella figura 35.

   ![Figura 35: Specificare la chiave di licenza di SIOS DataKeeper][sap-ha-guide-figure-3035]

   _Immettere il codice di licenza per il datakeeper_

6. Quando richiesto, riavviare la macchina virtuale.

### <a name="configure-sios-datakeeper"></a>Configurare il filekeeper
Dopo l'installazione di SIOS DataKeeper su entrambi i nodi, avviare la configurazione. L'obiettivo della configurazione è eseguire la replica di dati sincrona tra i dischi aggiuntivi collegati a ogni macchina virtuale.

1. Avviare lo strumento di configurazione e gestione di DataKeeper e selezionare il collegamento **Connect Server** (Connetti server).

   ![Figura 36: Strumento di configurazione e gestione di SIOS DataKeeper][sap-ha-guide-figure-3036]

   _Strumento di configurazione e gestione di SIOS DataKeeper_

2. Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di configurazione e gestione; eseguire l'operazione in un altro passaggio per il secondo nodo.

   ![Figura 37: Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di gestione e configurazione; eseguire l'operazione in un altro passaggio per il secondo nodo][sap-ha-guide-figure-3037]

   _Inserire il nome o l'indirizzo TCP/IP del primo nodo a cui deve connettersi lo strumento di configurazione e gestione e, in un secondo passaggio, il secondo nodo_

3. Creare il processo di replica tra i due nodi.

   ![Figura 38: Creare un processo di replica][sap-ha-guide-figure-3038]

   _Creazione di un processo di replica_

   Per la creazione di un processo di replica è disponibile una procedura guidata.

4. Definire il nome del processo di replica.

   ![Figura 39: Definire il nome del processo di replica][sap-ha-guide-figure-3039]

   _Definire il nome del processo di replica_

   ![Figura 40: Definire i dati di base per il nodo che deve essere il nodo di origine corrente][sap-ha-guide-figure-3040]

   _Definire i dati di base per il nodo che deve essere il nodo di origine corrente_

5. Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione.

   ![Figura 41: Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione corrente][sap-ha-guide-figure-3041]

   _Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione corrente_

6. Definire gli algoritmi di compressione. Nell'esempio è consigliabile comprimere il flusso di replica. Soprattutto in caso di risincronizzazione, la compressione del flusso di replica riduce notevolmente il tempo necessario per l'operazione. La compressione usa le risorse di CPU e RAM di una macchina virtuale. Con l'aumentare del tasso di compressione aumenta anche il volume delle risorse di CPU usate. È possibile modificare questa impostazione in un secondo momento.

7. Un'altra impostazione da verificare è se la replica viene eseguita in modalità sincrona o asincrona. Per proteggere le configurazioni di SAP ASCS/SCS, è necessario usare la replica sincrona.  

   ![Figura 42: Definire i dettagli della replica][sap-ha-guide-figure-3042]

   _Definire i dettagli della replica_

8. Definire se il volume replicato dal processo di replica deve essere rappresentato in una configurazione di cluster Windows Server Failover Cluster come disco condiviso. Per la configurazione di SAP ASCS/SCS è necessario scegliere **Yes** in modo che il cluster di Windows rilevi il volume replicato come disco condiviso che può essere usato come volume del cluster.

   ![Figura 43: Selezionare Sì per impostare il volume replicato come volume del cluster][sap-ha-guide-figure-3043]

   _Selezionare **Sì** per impostare il volume replicato come volume del cluster_

   Dopo aver creato il volume, lo strumento di configurazione e gestione di DataKeeper mostra che il processo di replica è attivo.

   ![Figura 44: Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo][sap-ha-guide-figure-3044]

   _Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo_

   Gestione cluster di failover visualizza ora il disco come disco di DataKeeper, come illustrato nella figura 45:

   ![Figura 45: Gestione cluster di failover visualizza il disco replicato da DataKeeper][sap-ha-guide-figure-3045]

   _Gestione cluster di failover visualizza il disco replicato da DataKeeper_


## <a name="next-steps"></a>Passaggi successivi

* [Installazione della disponibilità elevata di SAP NetWeaver nel cluster di failover Windows e nei dischi condivisi per l'istanza SAP (A)SCS in Azure][sap-high-availability-installation-wsfc-shared-disk]

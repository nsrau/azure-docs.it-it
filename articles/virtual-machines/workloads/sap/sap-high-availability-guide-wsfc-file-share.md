---
title: Clustering dell'istanza SAP (A)SCS nel cluster di failover di Windows usando la condivisione file di Azure | Microsoft Docs
description: Clustering dell'istanza SAP (A)SCS nel cluster di failover di Windows usando la condivisione file
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 472c3f35e2ae32550be62826407689f93101041f
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dv2-series
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#ds-series

[sap-installation-guides]:http://service.sap.com/instguides

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/en-us/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP multi-SID high-availability configuration)

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


# <a name="clustering-sap-ascs-instance-on-windows-failover-cluster-using-file-share-on-azure"></a>Clustering dell'istanza SAP (A)SCS nel cluster di failover di Windows usando la condivisione file di Azure

> ![Windows][Logo_Windows] Windows
>

Windows Server Failover Clustering è alla base di un'installazione SAP ASCS/SCS e di DBMS.

Un cluster di failover è un gruppo di 1 + n server (nodi) indipendenti che funzionano insieme per aumentare la disponibilità di applicazioni e servizi. Se si verifica un errore in un nodo, Windows Server Failover Clustering calcola il numero di errori che possono verificarsi e mantiene un cluster integro per fornire le applicazioni e i servizi. A questo scopo è possibile scegliere tra diverse modalità quorum per ottenere il clustering di failover.

## <a name="prerequisite"></a>Prerequisito
Prima di consultare questo articolo vedere l'articolo seguente:

* [Scenario e architettura per la disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
>Il clustering delle istanze di SAP (A)SCS con condivisione di file è supportato per i prodotti **SAP NetWeaver 7.40 (e versioni successive)** con **kernel SAP versione 7.49 o successiva**.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server Failover Clustering in Azure

Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un Windows Server Failover Clustering in Macchine virtuali di Azure. Quando si crea un cluster è necessario impostare diversi indirizzi IP e nomi host virtuali per l'istanza SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-cluster-virtual-host-name"></a>Risoluzione dei nomi in Azure e nome host virtuale del cluster

La piattaforma cloud di Azure non consente di configurare indirizzi IP virtuali, ad esempio indirizzi IP mobili. È necessaria una soluzione alternativa per configurare un indirizzo IP virtuale per poter raggiungere la risorsa cluster nel cloud. Azure include un **bilanciamento del carico interno** nel servizio Azure Load Balancer. Con il servizio di bilanciamento del carico interno, i client raggiungono il cluster tramite l'indirizzo IP virtuale del cluster. È necessario distribuire il servizio di bilanciamento del carico interni nel gruppo di risorse che contiene i nodi del cluster. Configurare quindi tutte le necessarie regole di port forwarding con le porte probe del servizio di bilanciamento del carico interno. I client possono connettersi tramite il nome host virtuale. Il server DNS risolve l'indirizzo IP del cluster e il servizio di bilanciamento del carico interno gestisce il port forwarding al nodo attivo del cluster.

![Figura 1: Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso][sap-ha-guide-figure-1001]

_**Figura 1:** Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso_

## <a name="sap-ascs-ha-with-file-share"></a>SAP (A)SCS a disponibilità elevata con condivisione file

SAP ha sviluppato un nuovo approccio e un'alternativa per i dischi condivisi del cluster per il clustering dell'istanza SAP (A)SCS nel cluster di failover di Windows.

Nell'esempio la **condivisione file SMB** viene usata come un'opzione per distribuire i **file della cartella SAP GLOBAL HOST**.

> [!NOTE]
>La condivisione file SMB è un'opzione aggiuntiva per i dischi condivisi cluster per il clustering delle istanze di SAP (A)SCS.  
>

I seguenti elementi sono specifici di questa architettura:

* **I servizi di SAP Central Services, con la propria struttura di file e i propri processi di messaggistica e accodamento, sono separati dai file dell'host di SAP GLOBAL**
* **I servizi di SAP Central Services vengono eseguiti nell'istanza SAP (A)SCS**
* L'istanza SAP (A)SCS viene aggiunta al cluster ed è accessibile usando il nome host virtuale **<(A)SCSVirtualHostName>**
* I file di SAP GLOBAL vengono inseriti nella condivisione file SMB e sono accessibili usando <SAPGLOBALHost> nome host \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\..
* L'istanza SAP (A)SCS viene installata in un disco locale in entrambi i nodi del cluster
* Il nome di rete **<(A)SCSVirtualHostName>** è diverso da **<SAPGLOBALHost>**

![Figura 2: Nuova architettura SAP (A)SCS a disponibilità elevata con condivisione file SMB][sap-ha-guide-figure-8004]

_**Figura 2:** Nuova architettura SAP (A)SCS a disponibilità elevata con condivisione file SMB_

Prerequisiti per la condivisione file SMB:

* Protocollo SMB 3.0 (o versione successiva)
* Possibilità di impostare elenchi di controllo di accesso di Active Directory (AD) per **gruppi di utenti di Active Directory** e **l'oggetto computer di tipo computer$**
* La condivisione file deve essere abilitata per la disponibilità elevata:
    * I dischi usati per archiviare i file non devono essere un singolo punto di guasto
    * È necessario verificare che il tempo di inattività dei server o delle macchine virtuali non causi tempo di inattività della condivisione file

A questo punto, il ruolo del cluster **SAP &lt;SID&gt;** non contiene dischi condivisi cluster o la risorsa cluster condivisione file generica.


![Figura 3: Risorse del ruolo del cluster SAP <SID> quando si usa una condivisione file][sap-ha-guide-figure-8005]

_**Figura 3:** Risorse del ruolo del cluster**SAP &lt;SID&gt;** quando si usa una condivisione file_


## <a name="scale-out-file-share-sofs-with-storage-spaces-direct-s2d-on-azure-as-sapmnt-file-share"></a>Condivisione file a scalabilità orizzontale (SOFS) con Spazi di archiviazione diretta (S2D) in Azure come condivisione file SAPMNT

È possibile usare la condivisione SOFS per ospitare e proteggere i file dell'host di SAP GLOBAL e offrire il servizio di condivisione file SAPMNT a disponibilità elevata.

![Figura 4: Condivisione file SOFS usata per proteggere i file dell'host di SAP GLOBAL][sap-ha-guide-figure-8006]

_**Figura 4:** Condivisione file SOFS usata per proteggere i file dell'host di SAP GLOBAL_

> [!IMPORTANT]
>La condivisione file SOFS è completamente supportata sia nel cloud di Microsoft Azure che negli ambienti locali.
>

L'opzione **SOFS** offre una condivisione file SAPMNT a disponibilità elevata e scalabilità orizzontale.

L'opzione **Spazi di archiviazione diretta (S2D)** viene usata come **disco condiviso** per SOFS, consente la compilazione di una risorsa di archiviazione estremamente disponibile e scalabile usando server di archiviazione locale. Di conseguenza, la risorsa di archiviazione condivisa usata per SOFS, ad esempio, per i file di SAP GLOBALHOST non è un singolo punto di guasto (SPOF).

> [!IMPORTANT]
>Se si prevede di impostare il ripristino di emergenza, SOFS è la soluzione consigliata per la condivisione di file a disponibilità elevata in Azure.
>

### <a name="sap-prerequisites-for-sofs-in-azure"></a>Prerequisiti SAP per SOFS in Azure

Per la condivisione file SOFS è necessario quanto segue:

* Almeno due nodi del cluster per la condivisione SOFS

* Ogni nodo deve avere almeno due dischi locali

* Per motivi di prestazioni, è necessario usare la **resilienza del mirroring**:
    * mirroring **a due vie** per la condivisione SOFS con due nodi del cluster
    * mirroring **a tre vie** per la condivisione SOFS con tre o più nodi del cluster


* Si **consiglia di usare tre o più nodi del cluster per le condivisioni SOFS con mirroring a tre vie**.
Questa configurazione offre una maggiore scalabilità e una maggiore resilienza di archiviazione rispetto alla configurazione SOFS con due nodi del cluster e il mirroring a due vie.

* È necessario usare il **disco Premium di Azure**

* Si **consiglia** di usare **Azure Managed Disks**

* Si **consiglia** di formattare i volumi con il nuovo **Resilient File System (ReFS)**
    * [Nota SAP 1869038 - supporto SAP per file system ReFs] [1869038]
    * Vedere il capitolo [Scelta del file system][planning-volumes-s2d-choosing-filesystem] dell'articolo Pianificazione dei volumi in Spazi di archiviazione diretta.
    * Assicurarsi di installare l'[aggiornamento cumulativo **KB4025334** di Microsoft][kb4025334].


* È possibile usare le dimensioni **Serie DS** o **Serie DSv2** delle macchine virtuali di Azure

* Per ottenere prestazioni ottimali della rete tra le macchine virtuali, fattore necessario per la sincronizzazione dei dischi di Spazi di archiviazione diretta, usare un tipo di macchina virtuale che abbia una **larghezza di banda come minimo "alta"**.
Per informazioni dettagliate, vedere la specifica relativa alla [Serie DSv2][dv2-series] e alla [Serie DS][ds-series].

* Si **consiglia** di **non allocare e riservare una certa capacità nel pool di archiviazione**. In questo modo restano disponibili volumi di spazio da ripristinare in caso di errori delle unità e migliorano le prestazioni e la sicurezza dei dati.

 Per maggiori dettagli, vedere [Scelta delle dimensioni dei volumi][choosing-the-size-of-volumes-s2d]


* Le macchine virtuali di Azure con condivisione file SOFS devono essere distribuite in **un proprio set di disponibilità di Azure**

* Non è necessario configurare il servizio di bilanciamento del carico interno di Azure per il nome di rete della condivisione file SOFS, ad esempio <SAPGlobalHostName>, come avviene per <(A)SCSVirtualHostname> dell'istanza SAP (A)SCS o per il sistema di gestione database. La condivisione SOFS distribuisce orizzontalmente il carico in tutti i nodi del cluster, quindi <SAPGlobalHostName> usa l'indirizzo IP locale di tutti i nodi del cluster.


> [!IMPORTANT]
>La condivisione file SAPMNT che fa riferimento a SAPGLOBALHOST non può essere modificata. SAP non supporta un nome di condivisione diverso da sapmnt.
>[Nota SAP 2492395 - Si può modificare il nome sapmnt della condivisione?][2492395]

### <a name="configuring-sap-ascs-instances-and-sofs-in-two-clusters"></a>Configurazione delle istanze di SAP (A)SCS e della condivisione file SOFS in due cluster

Si ha la possibilità di distribuire le istanze di SAP (A)SCS in un cluster, con il proprio ruolo del cluster SAP <SID>. La condivisione file SOFS è configurata in un altro cluster con un altro ruolo del cluster.

> [!IMPORTANT]
>In questo scenario l'istanza SAP (A)SCS è configurata per accedere a SAP GLOBALHost usando il percorso UNC \\\\&lt;SAPGLOBALHost&gt;\sapmnt\\&lt;SID&gt;\SYS\..
>

![Figura 5: istanza SAP (A)SCS e condivisione SOFS distribuite in DUE cluster][sap-ha-guide-figure-8007]

_**Figura 5:** istanza SAP (A)SCS e condivisione SOFS distribuite in DUE cluster_

> [!IMPORTANT]
>Nel cloud di Azure ogni cluster usato per SAP e per le condivisioni file SOFS deve essere distribuito nel proprio set di disponibilità di Azure, per garantire la distribuzione della posizione delle macchine virtuali del cluster nell'infrastruttura di Azure sottostante.
>

## <a name="generic-file-share-with-sios-as-cluster-shared-disks"></a>Condivisione file generica con SIOS come disco condiviso del cluster


> [!IMPORTANT]
>La condivisione SOFS è la soluzione consigliata per una condivisione file a disponibilità elevata.
>
>Tuttavia, se si prevede di impostare anche il **ripristino di emergenza** per la condivisione file a disponibilità elevata, è necessario usare la condivisione file generica e SISO come tecnologia per i dischi condivisi del cluster.
>

La condivisione file generica è un'altra opzione che consente di ottenere una condivisione file a disponibilità elevata.

In questo caso come disco condiviso del cluster è possibile usare la soluzione SIOS di terze parti.

## <a name="next-steps"></a>Passaggi successivi

* [Preparazione dell'infrastruttura di Azure per la disponibilità elevata di SAP con il cluster di failover Windows e la condivisione file per l'istanza SAP (A)SCS][sap-high-availability-infrastructure-wsfc-file-share]

* [Installazione della disponibilità elevata di SAP NetWeaver nel cluster di failover Windows e nella condivisione file per l'istanza SAP (A)SCS][sap-high-availability-installation-wsfc-shared-disk]

* [Deploy a two-node Storage Spaces Direct scale-out file server for UPD storage in Azure][deploy-sofs-s2d-in-azure] (Distribuire un file server di scalabilità orizzontale a due nodi di Spazi di archiviazione diretta per l'archiviazione UPD in Azure)

* [Spazi di archiviazione diretta in Windows Server 2016][s2d-in-win-2016]

* [Approfondimento sui volumi in Spazi di archiviazione diretta][deep-dive-volumes-in-s2d]

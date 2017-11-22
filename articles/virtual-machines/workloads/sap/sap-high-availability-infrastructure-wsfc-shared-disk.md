---
title: "Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per SAP ASCS/SCS | Microsoft Docs"
description: "Informazioni su come preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per un'istanza di SAP ASCS/SCS."
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2547d40ac39bc8188511f6682911fa302cf3e8a5
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e dischi condivisi per SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/

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
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

Questo articolo descrive i passaggi per preparare l'infrastruttura di Azure per l'installazione e la configurazione di un sistema SAP a disponibilità elevata in un cluster di failover di Windows usando un *disco condiviso del cluster* come opzione per il clustering di un'istanza di SAP ASCS.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare l'installazione, esaminare questo articolo:

* [Architecture guide: Cluster an SAP ASCS/SCS instance on a Windows failover cluster by using a cluster shared disk][sap-high-availability-guide-wsfc-shared-disk](Guida all'architettura: clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows che usa un disco condiviso del cluster)

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Preparare l'infrastruttura per il modello architetturale 1
I modelli di Azure Resource Manager per SAP consentono di semplificare la distribuzione delle risorse necessarie.

I modelli a tre livelli in Azure Resource Manager supportano anche scenari a disponibilità elevata, ad esempio il modello architetturale 1 con due cluster. Ogni cluster è un singolo punto di guasto SAP per SAP ASCS/SCS e DBMS.

Ecco dove è possibile ottenere i modelli di Azure Resource Manager per questo scenario di esempio descritto in questo articolo:

* [Immagine di Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Immagine di Azure Marketplace usando Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Immagine personalizzata usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

Per preparare l'infrastruttura per il modello architetturale 1:

- Nella casella **SYSTEMAVAILABILITY** nel riquadro **Parametri** del portale di Azure selezionare **HA**.

  ![Figura 1: Impostare i parametri di Azure Resource Manager di disponibilità elevata per SAP][sap-ha-guide-figure-3000]

_**Figura 1:** Impostare i parametri di Azure Resource Manager di disponibilità elevata per SAP_


  I modelli creano:

  * **Macchine virtuali**:
    * Macchine virtuali del server applicazioni SAP: \<SIDSistemaSAP\>-di-\<Numero\>
    * Macchine virtuali del cluster ASCS/SCS: \<SIDSistemaSAP\>-ascs-\<Numero\>
    * Cluster DBMS: \<SIDSistemaSAP\>-db-\<Numero\>

  * **Schede di rete per tutte le macchine virtuali, con gli indirizzi IP associati**:
    * \<SIDSistemaSAP\>-nic-di-\<Numero\>
    * \<SIDSistemaSAP\>-nic-ascs-\<Numero\>
    * \<SIDSistemaSAP\>-nic-db-\<Numero\>

  * **Account di archiviazione di Azure (solo dischi non gestiti)**:

  * **Gruppi di disponibilità** per:
    * Macchine virtuali del server applicazioni SAP: \<SIDSistemaSAP\>-avset-di
    * Macchine virtuali del cluster SAP ASCS/SCS: \<SIDSistemaSAP\>-avset-ascs
    * Macchine virtuali del cluster DBMS: \<SIDSistemaSAP\>-avset-db

  * **Servizio di bilanciamento del carico interno di Azure**:
    * Con tutte le porte per l'istanza di ASCS/SCS e l'indirizzo IP \<SIDSistemaSAP\>-lb-ascs
    * Con tutte le porte per l'istanza di SQL Server DBMS e l'indirizzo IP \<SIDSistemaSAP\>-lb-db

  * **Gruppo di sicurezza di rete**: \<SIDSistemaSAP\>-nsg-ascs-0  
    * Con una porta Remote Desktop Protocol (RDP) esterna aperta alla macchina virtuale \<SIDSistemaSAP\>-ascs-0

> [!NOTE]
> Tutti gli indirizzi IP delle schede di rete e i servizi di bilanciamento del carico interno di Azure sono dinamici per impostazione predefinita. Impostarli come indirizzi IP statici. Questo argomento verrà illustrato più avanti nell'articolo.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuire macchine virtuali con connettività di rete aziendale (cross-premise) da usare in fase di produzione
Per i sistemi SAP di produzione, distribuire le macchine virtuali di Azure con la [connettività di rete aziendale (cross-premise)][planning-guide-2.2] usando il gateway VPN di Azure o Azure ExpressRoute.

> [!NOTE]
> È possibile usare l'istanza di Rete virtuale di Azure. La rete virtuale e la subnet sono già state create e preparate.
>
>

1.  Nella casella **NEWOREXISTINGSUBNET** nel pannello **Parametri** del portale di Azure selezionare **existing**.
2.  Nella casella**SUBNETID** aggiungere la stringa completa dell'ID di subnet relativo alla rete di Azure preparata, in cui si prevede di distribuire le macchine virtuali di Azure.
3.  Per ottenere un elenco di tutte le subnet di rete di Azure, eseguire questo comando di PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
  ```

  Il campo **ID** contiene il valore dell'ID di subnet.
4. Per ottenere un elenco di tutti i valori di ID di subnet, eseguire questo comando PowerShell:

  ```PowerShell
  (Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
  ```

  Il valore dell'ID di subnet è il seguente:

  ```
  /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
  ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuire delle istanze di SAP solo cloud a scopo di test e dimostrazione
È possibile distribuire il sistema SAP a disponibilità elevata in un modello di distribuzione solo cloud. Questo tipo di distribuzione è utile soprattutto per i casi d'uso di demo e test. Non è adatto per i casi d'uso in un ambiente di produzione.

- Nella casella **NEWOREXISTINGSUBNET** nel pannello **Parametri** del portale di Azure selezionare **new**. Lasciare vuoto il campo **SUBNETID**.

  Il modello di Azure Resource Manager per SAP crea automaticamente la rete virtuale di Azure e la subnet.

> [!NOTE]
> È anche necessario distribuire almeno una macchina virtuale dedicata per i servizi Active Directory e DNS nella stessa istanza di Rete virtuale di Azure. Il modello non crea queste macchine virtuali.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Preparare l'infrastruttura per il modello architetturale 2

È possibile usare questo modello di Azure Resource Manager in modo che SAP semplifichi la distribuzione delle risorse di infrastruttura necessarie per il modello di SAP 2.

Ecco dove è possibile ottenere i modelli di Azure Resource Manager per questo scenario di distribuzione:

* [Immagine di Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Immagine di Azure Marketplace usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Immagine personalizzata usando Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Preparare l'infrastruttura per il modello architetturale 3

È possibile preparare l'infrastruttura e configurare SAP per più SID. Ad esempio, è possibile aggiungere un'istanza di SAP ASCS/SCS aggiuntiva in una configurazione di cluster *esistente*. Per altre informazioni, vedere [Configurare un'istanza ASCS/SCS di SAP aggiuntiva in una configurazione di cluster esistente per creare una configurazione di SAP a più SID - Azure Resource Manager][sap-ha-multi-sid-guide].

Per creare un nuovo cluster con più SID, è possibile usare i [modelli di avvio rapido su GitHub](https://github.com/Azure/azure-quickstart-templates) relativi a più SID.

Per creare un nuovo cluster a più SID, è necessario distribuire i tre modelli seguenti:

* [Modello di ASCS/SCS](#ASCS-SCS-template)
* [Modello di database](#database-template)
* [Modello dei server applicazioni](#application-servers-template)

Le sezioni seguenti includono informazioni dettagliate sui modelli e sui parametri che è necessario fornire nei modelli.

### <a name="ASCS-SCS-template"></a> Modello di ASCS/SCS

Il modello di ASCS/SCS distribuisce due macchine virtuali che possono essere usate per creare un cluster di failover di Windows che ospita più istanze di ASCS/SCS.

Per configurare il modello a più SID di ASCS/SCS, nel [modello a più SID di ASCS/SCS][sap-templates-3-tier-multisid-xscs-marketplace-image] o nel [modello a più SID di ASCS/SCS usando Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md], immettere i valori per i parametri seguenti:

  - **Resource Prefix**: impostare il prefisso della risorsa, che viene usato per assegnare un prefisso a tutte le risorse create durante la distribuzione. Poiché le risorse non appartengono a un solo sistema SAP, il prefisso della risorsa non è il SID di un sistema SAP.  Il prefisso deve essere compreso fra tre e sei caratteri.
  - **Stack Type**: selezionare il tipo di stack del sistema SAP. A seconda del tipo di stack, Azure Load Balancer dispone di un indirizzo IP (solo ABAP o Java) o due indirizzi IP (ABAP+Java) privati per ogni sistema SAP.
  -  **OS Type**: selezionare il sistema operativo delle macchine virtuali.
  -  **SAP System Count**: selezionare il numero di sistemi SAP da installare in questo cluster.
  -  **System Availability**: selezionare **HA**.
  -  **Admin Username e Admin Password**: Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **New Or Existing Subnet**: impostare un valore per scegliere se creare una nuova rete virtuale e una nuova subnet o usare una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare **existing**.
  -  **Subnet Id**: impostare l'ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale ExpressRoute o VPN per connettere la macchina virtuale alla rete locale. L'ID in genere è simile al seguente:

   /subscriptions/\<ID sottoscrizione\>/resourceGroups/\<nome gruppo risorse\>/providers/Microsoft.Network/virtualNetworks/\<nome rete virtuale\>/subnets/\<nome subnet\>

Il modello distribuisce un'istanza di Azure Load Balancer che supporta più sistemi SAP:

- Le istanze ASCS sono configurate per i numeri di istanza 00, 10, 20...
- Le istanze SCS sono configurate per i numeri di istanza 01, 11, 21...
- Le istanze di ASCS Enqueue Replication Server (ERS) (Linux) sono configurate per i numeri di istanza 02, 12, 22...
- Le istanze SCS ERS (solo Linux) sono configurate per i numeri di istanza 03, 13, 23...

Il bilanciamento del carico contiene 1 indirizzo VIP (2 per Linux), 1 indirizzo VIP per ASCS/SCS e 1 indirizzo VIP per ERS (solo Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> Porte di SAP ASCS/SCS
L'elenco seguente contiene tutte le regole di bilanciamento del carico (dove x è il numero del sistema SAP, ad esempio, 1, 2, 3...):
- Porte specifiche di Windows per ogni sistema SAP 445, 5985
- Porte ASCS (numero di istanza x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- Porte SCS (numero di istanza x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- Porte ASCS ERS in Linux (numero di istanza x2): 33x2, 5x213, 5x214, 5x216
- Porte SCS ERS in Linux (numero di istanza x3): 33x3, 5x313, 5x314, 5x316

Il servizio di bilanciamento del carico viene configurato per l'uso delle porte probe seguenti (dove x è il numero del sistema SAP, ad esempio 1, 2, 3...):
- Porta probe del servizio di bilanciamento del carico interno ASCS/SCS: 620x0
- Porta probe del servizio di bilanciamento del carico interno ERS (solo Linux): 621x2

### <a name="database-template"></a> Modello di database

Il modello di database distribuisce una o due macchine virtuali che è possibile usare per installare il sistema di gestione di database relazionali (RDBMS) per un sistema SAP. Se, ad esempio, si distribuisce un modello ASCS/SCS per 5 sistemi SAP, è necessario distribuire questo modello cinque volte.

Per configurare il modello a più SID di database nel [modello a più SID di database][sap-templates-3-tier-multisid-db-marketplace-image] o nel [modello a più SID di database usando Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md], immettere i valori per i parametri seguenti:

  -  **Sap System Id**: immettere l'ID del sistema SAP che si vuole installare. L'ID viene usato come prefisso per le risorse distribuite.
  -  **Os Type**: selezionare il sistema operativo delle macchine virtuali.
  -  **Dbtype**: selezionare il tipo di database che si vuole installare nel cluster. Selezionare **SQL** se si vuole installare Microsoft SQL Server. Selezionare **HANA** se si prevede di installare SAP HANA nelle macchine virtuali. Assicurarsi di selezionare il tipo di sistema operativo corretto. Selezionare **Windows** per SQL e selezionare una distribuzione di Linux per HANA. Azure Load Balancer connesso alle macchine virtuali viene configurato per supportare il tipo di database selezionato:
    * **SQL**: il servizio di bilanciamento del carico carica la porta di bilanciamento 1433. Verificare di usare questa porta per l'installazione di SQL Server Always On.
    * **HANA**: il servizio di bilanciamento del carico carica le porte di bilanciamento 35015 e 35017. Assicurarsi di installare SAP HANA con il numero di istanza **50**.
    Il servizio bilanciamento del carico usa la porta probe 62550.
  -  **Sap System Size**: imposta il numero di SAPS indicato dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
  -  **System Availability**: selezionare **HA**.
  -  **Admin Username e Admin Password**: Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **Subnet Id**: immettere l'ID della subnet usata durante la distribuzione del modello di ASCS/SCS o l'ID della subnet creata come parte della distribuzione del modello di ASCS/SCS.

### <a name="application-servers-template"></a> Modello dei server applicazioni

Il modello dei server applicazioni consente di distribuire due o più macchine virtuali che possono essere usate come istanze del server applicazioni SAP per un sistema SAP. Se, ad esempio, si distribuisce un modello ASCS/SCS per 5 sistemi SAP, è necessario distribuire questo modello cinque volte.

Per configurare il modello a più SID dei server applicazioni nel [modello a più SID dei server applicazioni][sap-templates-3-tier-multisid-apps-marketplace-image] o nel [modello a più SID dei server applicazioni usando Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md], immettere i valori per i parametri seguenti:

  -  **Sap System Id**: immettere l'ID del sistema SAP che si vuole installare. L'ID viene usato come prefisso per le risorse distribuite.
  -  **Os Type**: selezionare il sistema operativo delle macchine virtuali.
  -  **Sap System Size**: il numero di SAPS indicato dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP.
  -  **System Availability**: selezionare **HA**.
  -  **Admin Username e Admin Password**: Creare un nuovo utente che può essere usato per accedere alla macchina.
  -  **Subnet Id**: immettere l'ID della subnet usata durante la distribuzione del modello di ASCS/SCS o l'ID della subnet creata come parte della distribuzione del modello di ASCS/SCS.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rete virtuale di Azure
In questo esempio lo spazio degli indirizzi dell'istanza di Rete virtuale di Azure è 10.0.0.0/16. Esiste una subnet denominata Subnet con un intervallo di indirizzi di 10.0.0.0/24. Tutte le macchine virtuali e i servizi di bilanciamento del carico interni vengono distribuiti in questa rete virtuale.

> [!IMPORTANT]
> Non apportare modifiche alle impostazioni di rete nel sistema operativo guest, inclusi indirizzi IP, server DNS e la subnet. Configurare tutte le impostazioni di rete in Azure. Il servizio Dynamic Host Configuration Protocol (DHCP) propaga le impostazioni.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Indirizzi IP DNS

Per impostare gli indirizzi IP DNS necessari, attenersi alla procedura seguente:

1.  Nel riquadro **Server DSN** del portale di Azure verificare che l'opzione **Server DNS** della rete virtuale sia impostata su **DNS personalizzato**.
2.  Selezionare le impostazioni in base al tipo di rete esistente. Per altre informazioni, vedere le seguenti risorse:
    * [Connettività di rete aziendale (cross-premise)][planning-guide-2.2]: aggiungere gli indirizzi IP dei server DNS locali.  
    È possibile estendere i server DNS locali alle macchine virtuali in esecuzione in Azure. In tale scenario è possibile aggiungere gli indirizzi IP delle macchine virtuali di Azure in cui si esegue il servizio DNS.
    * [Distribuzione solo cloud][planning-guide-2.1]: distribuire una macchina virtuale aggiuntiva nella stessa istanza di Rete virtuale che funge da server DNS. Aggiungere gli indirizzi IP delle macchine virtuali di Azure configurate per l'esecuzione del servizio DNS.

    ![Figura 2: Configurare i server DNS per Rete virtuale di Azure][sap-ha-guide-figure-3001]

    _**Figura 2:** Configurare i server DNS per Rete virtuale di Azure_

  > [!NOTE]
  > Se si modificano gli indirizzi IP dei server DNS, è necessario riavviare le macchine virtuali di Azure per applicare la modifica e propagare i nuovi server DNS.
  >
  >

Nell'esempio il servizio DNS viene installato e configurato in queste macchine virtuali Windows:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Primo server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Secondo server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomi host e indirizzi IP statici per l'istanza in cluster di SAP ASCS/SCS e l'istanza in cluster di DBMS

Per una distribuzione locale, sono necessari questi nomi host e indirizzi IP riservati:

| Ruolo nome host virtuale | Nome host virtuale | Indirizzo IP statico virtuale |
| --- | --- | --- |
| Primo nome host virtuale del cluster SAP ASCS/SCS (per la gestione del cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome host virtuale dell'istanza di SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Secondo nome host virtuale del cluster SAP DBMS (gestione del cluster) |pr1-dbms-vir |10.0.0.32 |

Quando si crea il cluster, creare i nomi host virtuali pr1-ascs-vir e pr1-dbms-vir e gli indirizzi IP associati che gestiscono il cluster stesso. Per informazioni su come eseguire questa operazione, vedere [Raccogliere i nodi del cluster in una configurazione cluster][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config].

È possibile creare manualmente gli altri due nomi host virtuali, pr1-ascs-sap e pr1-dbms-sap, e gli indirizzi IP associati, nel server DNS. L'istanza di SAP ASCS/SCS in cluster e l'istanza di DBMS in cluster usano queste risorse, Per informazioni su come eseguire questa operazione, vedere [Creare un nome host virtuale per un'istanza di SAP ASCS/SCS in cluster][sap-ha-guide-9.1.1].

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Impostare gli indirizzi IP statici per le macchine virtuali SAP
Dopo avere distribuito le macchine virtuali da usare nel cluster, è necessario impostare gli indirizzi IP statici per tutte le macchine virtuali. Eseguire questa operazione nella configurazione di Rete virtuale di Azure e non nel sistema operativo guest.

1.  Nel portale di Azure selezionare **Gruppo di risorse** > **Scheda di rete** > **Impostazioni** > **Indirizzo IP**.
2.  Nel riquadro **Indirizzi IP** in **Assegnazione** selezionare **Statica**. Nella casella **Indirizzo IP** immettere l'indirizzo IP da usare.

  > [!NOTE]
  > Se si modifica l'indirizzo IP della scheda di rete, è necessario riavviare le macchine virtuali di Azure per applicare la modifica.  
  >
  >

  ![Figura 3: Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale][sap-ha-guide-figure-3002]

  _**Figura 3:** Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale_

  Ripetere questo passaggio per tutte le interfacce di rete, ovvero per tutte le macchine virtuali, incluse le macchine virtuali che si vuole usare per il servizio Active Directory o DNS.

In questo esempio vengono usate le macchine virtuali e gli indirizzi IP statici seguenti:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Prima istanza del server applicazioni SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Seconda istanza del server applicazioni SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ultima istanza del server applicazioni SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Secondo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primo nodo del cluster per l'istanza di DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Secondo nodo del cluster per l'istanza di DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Impostare un indirizzo IP statico per il servizio di bilanciamento del carico interno di Azure

Il modello di Azure Resource Manager per SAP crea un servizio di bilanciamento del carico interno di Azure usato per il cluster dell'istanza di SAP ASCS/SCS e per il cluster DBMS.

> [!IMPORTANT]
> L'indirizzo IP del nome host virtuale di SAP ASCS/SCS è lo stesso del servizio di bilanciamento del carico interno di SAP ASCS/SCS: pr1-lb-ascs.
> L'indirizzo IP del nome virtuale di DBMS è lo stesso del servizio di bilanciamento del carico interno di DBMS: pr1-lb-dbms.
>
>

Per impostare un indirizzo IP statico per il servizio di bilanciamento del carico interno di Azure:

1.  La distribuzione iniziale imposta l'indirizzo IP del servizio di bilanciamento del carico interno come **dinamico**. Nel pannello **Indirizzi IP** del portale di Azure, in **Assegnazione** selezionare **Statica**.
2.  Impostare l'indirizzo IP del bilanciamento del carico interno **pr1-lb-ascs** sull'indirizzo IP del nome host virtuale dell'istanza di SAP ASCS/SCS.
3.  Impostare l'indirizzo IP del bilanciamento del carico interno **pr1-lb-dbms** sull'indirizzo IP del nome host virtuale dell'istanza di DBMS.

  ![Figura 4: Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS][sap-ha-guide-figure-3003]

  _**Figura 4:** Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS_

Nell'esempio sono presenti due servizi di bilanciamento del carico interno di Azure con questi indirizzi IP statici:

| Ruolo del servizio di bilanciamento del carico interno di Azure | Nome del servizio di bilanciamento del carico interno di Azure | Indirizzo IP statico |
| --- | --- | --- |
| Servizio di bilanciamento del carico interno dell'istanza di SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Servizio di bilanciamento del carico interno di SAP DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure

Il modello di Azure Resource Manager per SAP crea le porte necessarie:
* Un'istanza di ABAP ASCS, con il numero di istanza predefinito 00
* Un'istanza di Java SCS, con il numero di istanza predefinito 01

Quando si installa l'istanza di SAP ASCS/SCS, è necessario usare il numero di istanza predefinito 00 per l'istanza di ABAP ASCS e il numero di istanza predefinito 01 per l'istanza di Java SCS.

Creare quindi gli endpoint di bilanciamento del carico interno obbligatori per le porte di SAP NetWeaver.

Per creare gli endpoint di bilanciamento del carico interno obbligatori, creare prima questi endpoint per le porte di SAP NetWeaver ABAP ASCS:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza ASCS con l'istanza numero 00) (ERS con 10) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3200* |32\<NumeroIstanza\> |3200 |
| Server messaggi ABAP/ *lbrule3600* |36\<NumeroIstanza\> |3600 |
| Messaggio ABAP interno/ *lbrule3900* |39\<NumeroIstanza\> |3900 |
| HTTP server messaggi/ *Lbrule8100* |81\<NumeroIstanza\> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5\<NumeroIstanza\>13 |50013 |
| SAP Start Service ASCS HTTPS / *Lbrule50014* |5\<NumeroIstanza\>14 |50014 |
| Replica di accodamento/ *Lbrule50016* |5\<NumeroIstanza\>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5\<NumeroIstanza\>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5\<NumeroIstanza\>14 |51014 |
| Gestione remota Windows (WinRM) *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

**Tabella 1:** numeri di porta delle istanze di SAP NetWeaver ABAP ASCS

Creare quindi questi endpoint di bilanciamento del carico per le porte SCS di SAP NetWeaver Java:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza SCS con numero di istanza 01) (ERS con 11) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3201* |32\<NumeroIstanza\> |3201 |
| Server gateway/ *lbrule3301* |33\<NumeroIstanza\> |3301 |
| Server messaggi Java/ *lbrule3900* |39\<NumeroIstanza\> |3901 |
| HTTP server messaggi/ *Lbrule8101* |81\<NumeroIstanza\> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5\<NumeroIstanza\>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5\<NumeroIstanza\>14 |50114 |
| Replica di accodamento/ *Lbrule50116* |5\<NumeroIstanza\>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5\<NumeroIstanza\>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5\<NumeroIstanza\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

**Tabella 2:** numeri di porta delle istanze di SAP NetWeaver Java SCS

![Figura 5: Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3004]

_**Figura 5:** Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure_

Impostare l'indirizzo IP del servizio di bilanciamento del carico pr1-lb-dbms sull'indirizzo IP del nome host virtuale dell'istanza di DBMS.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure

Per usare numeri diversi per le istanze di SAP ASCS o SCS, è necessario cambiare i nomi e i valori di queste porte rispetto ai valori predefiniti.

1.  Nel portale di Azure selezionare **\<SID\>-lb-ascs load balancer** > **Regole di bilanciamento del carico**.
2.  Per tutte le regole di bilanciamento del carico appartenenti all'istanza di SAP ASCS o SCS, modificare questi valori:

  * Nome
  * Porta
  * Porta back-end

  Ad esempio, per sostituire il numero di istanza di ASCS predefinito 00 con 31, è necessario apportare le modifiche per tutte le porte elencate nella tabella 1.

  Ecco un esempio di aggiornamento per la porta *lbrule3200*.

  ![Figura 6: Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3005]

  _**Figura 6:** Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Aggiungere macchine virtuali Windows al dominio

Dopo avere assegnato un indirizzo IP statico alle macchine virtuali, aggiungere le macchine virtuali al dominio.

![Figura 7: Aggiungere una macchina virtuale a un dominio][sap-ha-guide-figure-3006]

_**Figura 7:** Aggiungere una macchina virtuale a un dominio_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster per l'istanza di SAP ASCS/SCS

Azure Load Balancer ha un servizio di bilanciamento del carico interno che chiude le connessioni quando rimangono inattive per un periodo di tempo impostato (timeout di inattività). I processi di lavoro SAP nelle istanze delle finestre di dialogo aprono le connessioni al processo di accodamento SAP non appena deve essere inviata la prima richiesta di accodamento/rimozione dalla coda. Queste connessioni restano in genere stabilite fino al riavvio del processo di lavoro o del processo di accodamento. Se tuttavia la connessione rimane inattiva per un determinato periodo di tempo, il servizio di bilanciamento del carico interno di Azure chiude le connessioni. Questo non è un problema perché il processo di lavoro SAP ristabilisce la connessione al processo di accodamento se non esiste più. Queste attività sono documentate nelle tracce degli sviluppatori dei processi SAP, ma creano una grande quantità di contenuti aggiuntivi in tali tracce. È consigliabile modificare i parametri `KeepAliveTime` e `KeepAliveInterval` TCP/IP in entrambi i nodi del cluster. Combinare queste modifiche nei parametri TCP/IP con i parametri del profilo SAP, illustrati più avanti nell'articolo.

Per aggiungere le voci del Registro di sistema in entrambi i nodi del cluster dell'istanza di SAP ASCS/SCS, aggiungere prima queste voci del Registro di sistema di Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| Path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveTime` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabella 3:** modificare il primo parametro TCP/IP

Aggiungere quindi questa voce del Registro di sistema Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| Path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveInterval` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

**Tabella 4:** modificare il secondo parametro TCP/IP

Per applicare le modifiche, riavviare entrambi i nodi del cluster.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurare un cluster Windows Server Failover Cluster per un'istanza di SAP ASCS/SCS

La configurazione di un cluster Windows Server Failover Cluster per un'istanza di SAP ASCS/SCS prevede queste attività:

- Raccogliere i nodi del cluster in una configurazione cluster.
- Configurare il controllo di condivisione file del cluster.

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Raccogliere i nodi del cluster in una configurazione cluster

1.  Nella procedura guidata Aggiungi ruoli e funzionalità aggiungere il clustering di failover a entrambi i nodi del cluster.
2.  Configurare il cluster di failover usando Gestione cluster di failover. In Gestione cluster di failover selezionare **Crea cluster** e quindi aggiungere solo il nome del primo cluster (nodo A). Non aggiungere ancora il secondo nodo. Attendere un passaggio successivo.

  ![Figura 8: Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster][sap-ha-guide-figure-3007]

  _**Figura 8:** Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster_

3.  Immettere il nome della rete (nome host virtuale) del cluster.

  ![Figura 9: Definire il nome del cluster][sap-ha-guide-figure-3008]

  _**Figura 9:** Definire il nome del cluster_

4.  Dopo avere creato il cluster, eseguire un test di convalida del cluster.

  ![Figura 10: Eseguire il controllo di convalida del cluster][sap-ha-guide-figure-3009]

  _**Figura 10:** Eseguire il controllo di convalida del cluster_

  A questo punto del processo è possibile ignorare gli avvisi sui dischi. Più avanti si aggiungeranno un controllo di condivisione file e i dischi condivisi SIOS. In questa fase, non è necessario un quorum.

  ![Figura 11: Nessun disco quorum trovato][sap-ha-guide-figure-3010]

  _**Figura 11:** Nessun disco quorum trovato_

  ![Figura 12: Una risorsa del cluster principale richiede un nuovo indirizzo IP][sap-ha-guide-figure-3011]

  _**Figura 12:** Una risorsa del cluster principale richiede un nuovo indirizzo IP_

5.  Modificare l'indirizzo IP del servizio cluster principale. Il cluster non può essere avviato finché non si cambia l'indirizzo IP del servizio cluster principale perché l'indirizzo IP del server punta a uno dei nodi delle macchine virtuali. Eseguire questa operazione nella pagina **Proprietà** della risorsa IP del servizio cluster principale.

  Ad esempio, è necessario assegnare un indirizzo IP (in questo esempio 10.0.0.42) per il nome host virtuale del cluster pr1-ascs-vir.

  ![Figura 13: Nella finestra di dialogo Proprietà modificare l'indirizzo IP][sap-ha-guide-figure-3012]

  _**Figura 13:** Nella finestra di dialogo **Proprietà** modificare l'indirizzo IP_

  ![Figura 14: Assegnare l'indirizzo IP riservato per il cluster][sap-ha-guide-figure-3013]

  _**Figura 14:** Assegnare l'indirizzo IP riservato per il cluster_

6.  Portare online il nome host virtuale del cluster.

  ![Figura 15: Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto][sap-ha-guide-figure-3014]

  _**Figura 15:** Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto_

7.  Aggiungere il secondo nodo del cluster.

  Ora che il servizio principale del cluster è attivo e in esecuzione, è possibile aggiungere il secondo nodo del cluster.

  ![Figura 16: Aggiungere il secondo nodo del cluster][sap-ha-guide-figure-3015]

  _**Figura 16:** Aggiungere il secondo nodo del cluster_

8.  Immettere un nome host del secondo nodo cluster.

  ![Figura 17: Immettere il nome host del secondo nodo cluster][sap-ha-guide-figure-3016]

  _**Figura 17:** Immettere il nome host del secondo nodo cluster_

  > [!IMPORTANT]
  > Assicurarsi che la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** *non* sia selezionata.  
  >
  >

  ![Figura 18: Non selezionare la casella di controllo][sap-ha-guide-figure-3017]

  _**Figura 18:** *Non* selezionare la casella di controllo_

  È possibile ignorare gli avvisi relativi al quorum e ai dischi. Si imposterà il quorum e si condividerà il disco in un secondo momento, come descritto in [Installing SIOS DataKeeper Cluster Edition for SAP ASCS/SCS cluster share disk][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios] (Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS).

  ![Figura 19: Ignorare gli avvisi sul quorum del disco][sap-ha-guide-figure-3018]

  _**Figura 19:** Ignorare gli avvisi sul quorum del disco_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurare il controllo di condivisione file del cluster

La configurazione di un controllo di condivisione file del cluster prevede queste attività:

- Creare una condivisione file.
- Impostare il quorum del controllo di condivisione file in Gestione cluster di failover.

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creare una condivisione file

1.  Selezionare un controllo di condivisione file invece di un disco quorum. SIOS DataKeeper supporta questa opzione.

  Negli esempi di questo articolo, il controllo di condivisione file è nel server Active Directory o DNS in esecuzione in Azure. Il controllo di condivisione file è denominato domcontr-0. Poiché sarà stata configurata una connessione VPN ad Azure (tramite gateway VPN o Azure ExpressRoute), il servizio Active Directory o DNS è locale e non è adatto all'esecuzione di un controllo di condivisione file.

  > [!NOTE]
  > Se il servizio Active Directory o DNS viene eseguito solo in locale, non configurare il controllo di condivisione file nel sistema operativo Windows con Active Directory o DNS eseguito in locale. La latenza di rete tra i nodi del cluster in esecuzione in Azure e Active Directory o DNS in locale potrebbe essere eccessiva e causare problemi di connettività. Assicurarsi di configurare il controllo di condivisione file in una macchina virtuale di Azure in esecuzione vicino al nodo del cluster.  
  >
  >

  L'unità del quorum richiede almeno 1.024 MB di spazio disponibile. Si consiglia un valore di 2.048 MB di spazio disponibile per l'unità quorum.

2.  Aggiungere l'oggetto del nome cluster.

  ![Figura 20: Assegnare le autorizzazioni nella condivisione per l'oggetto del nome cluster][sap-ha-guide-figure-3019]

  _**Figura 20:** Assegnare le autorizzazioni nella condivisione per l'oggetto del nome cluster_

  Verificare che le autorizzazioni includano l'autorità di modificare i dati nella condivisione per l'oggetto nome cluster (in questo esempio pr1-ascs-vir$).

3.  Per aggiungere l'oggetto nome cluster all'elenco, selezionare **Aggiungi**. Modificare il filtro per cercare gli oggetti computer, oltre a quelli illustrati nella figura 22.

  ![Figura 21: Modificare i tipi di oggetto per includere i computer][sap-ha-guide-figure-3020]

  _**Figura 21:** Modificare i **tipi di oggetto** per includere i computer_

  ![Figura 22: Selezionare la casella di controllo Computer][sap-ha-guide-figure-3021]

  _**Figura 22:** Selezionare la casella di controllo **Computer**_

4.  Immettere l'oggetto nome del cluster, come illustrato nella figura 21. Poiché il record è già stato creato, è possibile modificare le autorizzazioni, come illustrato nella figura 20.

5.  Selezionare la scheda **Sicurezza** della condivisione e quindi impostare autorizzazioni più dettagliate per l'oggetto del nome cluster.

  ![Figura 23: Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file][sap-ha-guide-figure-3022]

  _**Figura 23:** Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Impostare il quorum del controllo di condivisione file in Gestione cluster di failover

1.  Aprire Configurazione guidata quorum del cluster.

  ![Figura 24: Avviare la procedura guidata Configure Cluster Quorum Setting (Configura impostazioni quorum del cluster)][sap-ha-guide-figure-3023]

  _**Figura 24:** Avviare la procedura guidata Configure Cluster Quorum Setting (Configura impostazioni quorum del cluster)_

2.  Nella pagina **Selezione opzione configurazione quorum** selezionare **Seleziona il quorum di controllo**.

  ![Figura 25: Configurazioni del quorum tra cui è possibile scegliere][sap-ha-guide-figure-3024]

  _**Figura 25:** Configurazioni del quorum tra cui è possibile scegliere_

3.  Nella pagina **Seleziona il quorum di controllo** selezionare **Configura condivisione file di controllo**.

  ![Figura 26: Selezionare il controllo di condivisione file][sap-ha-guide-figure-3025]

  _**Figura 26:** Selezionare il controllo di condivisione file_

4.  Immettere il percorso UNC della condivisione file (in questo esempio \\domcontr-0\FSW) . Selezionare **Avanti** per visualizzare un elenco delle modifiche che è possibile apportare.

  ![Figura 27: Definire il percorso della condivisione file per la condivisione di controllo][sap-ha-guide-figure-3026]

  _**Figura 27:** Definire il percorso della condivisione file per la condivisione di controllo_

5.  Selezionare le modifiche desiderate e quindi selezionare **Avanti**. È necessario riconfigurare correttamente il cluster, come illustrato nella figura 28:  

  ![Figura 28: Conferma della riconfigurazione del cluster][sap-ha-guide-figure-3027]

  _**Figura 28:** Conferma della riconfigurazione del cluster_

Dopo aver installato correttamente il cluster di failover Windows, sarà necessario modificare alcune soglie per adattare il rilevamento del failover alle condizioni in Azure. I parametri da modificare sono documentati nel blog [Tuning Failover Cluster Network Thresholds][tuning-failover-cluster-network-thresholds] (Definire le soglie di rete per il cluster di failover). Supponendo che le due VM che compongono la configurazione del cluster Windows per ASCS/SCS siano nella stessa subnet, modificare i parametri seguenti impostando i valori indicati:

- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Queste impostazioni sono state testate con i clienti e rappresentano un buon compromesso. Sono sufficientemente resilienti, ma forniscono anche un failover abbastanza rapido in condizioni di errore reali in un software SAP o in un nodo o un errore di VM.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installare SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS

Ora è disponibile una configurazione funzionante di Windows Server Failover Clustering in Azure. Per installare un'istanza di SAP ASCS/SCS, è necessaria una risorsa disco condiviso. Non è possibile creare le risorse del disco condiviso necessarie in Azure. SIOS DataKeeper Cluster Edition è una soluzione di terze parti che è possibile usare per creare le risorse disco condiviso.

L'installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS prevede queste attività:

- Aggiungere Microsoft .NET Framework 3.5.
- Installare SIOS DataKeeper.
- Configurare SIOS DataKeeper.

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Aggiungere .NET Framework 3.5
.NET Framework 3.5 non viene attivato né installato automaticamente in Windows Server 2012 R2. Poiché SIOS DataKeeper richiede l'installazione di .NET in tutti i nodi in cui si installa DataKeeper, è necessario installare .NET Framework 3.5 nel sistema operativo guest di tutte le macchine virtuali nel cluster.

È possibile aggiungere .NET Framework 3.5 in due modi:

- Usare l'Aggiunta guidata ruoli e funzionalità in Windows come illustrato nella figura 29:

  ![Figura 29: Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3028]

  _**Figura 29:** Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità_

  ![Figura 30: Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3029]

  _**Figura 30:** Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità_

- Usare lo strumento da riga di comando dism.exe. Per questo tipo di installazione è necessario accedere alla directory SxS nei supporti di installazione di Windows. Eseguire questo comando a un prompt dei comandi con privilegi elevati:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installare SIOS DataKeeper

Installare SIOS DataKeeper Cluster Edition in ogni nodo del cluster. Per creare una risorsa di archiviazione condivisa virtuale con SIOS DataKeeper, creare un mirror sincronizzato e quindi simulare la risorsa di archiviazione condivisa del cluster.

Prima di installare il software SIOS, creare l'utente di dominio DataKeeperSvc.

> [!NOTE]
> Aggiungere l'utente DataKeeperSvc al gruppo Administrators locale in entrambi i nodi del cluster.
>
>

Per installare SIOS DataKeeper:

1.  Installare il software SIOS in entrambi i nodi del cluster.

  ![Programma di installazione SIOS][sap-ha-guide-figure-3030]

  ![Figura 31: Prima schermata dell'installazione di SIOS DataKeeper][sap-ha-guide-figure-3031]

  _**Figura 31:** Prima schermata dell'installazione di SIOS DataKeeper_

2.  Nella finestra di dialogo selezionare **Sì**.

  ![Figura 32: DataKeeper segnala che un servizio verrà disabilitato][sap-ha-guide-figure-3032]

  _**Figura 32:** DataKeeper segnala che un servizio verrà disabilitato_

3.  Nella finestra di dialogo si consiglia di selezionare **Domain or Server account** (Account di dominio o server).

  ![Figura 33: Selezione dell'utente per SIOS DataKeeper][sap-ha-guide-figure-3033]

  _**Figura 33:** Selezione dell'utente per SIOS DataKeeper_

4.  Specificare il nome utente dell'account di dominio e la password creati per SIOS DataKeeper.

  ![Figura 34: Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper][sap-ha-guide-figure-3034]

  _**Figura 34:** Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper_

5.  Installare la chiave di licenza per l'istanza di SIOS DataKeeper, come illustrato nella figura 35.

  ![Figura 35: Specificare la chiave di licenza di SIOS DataKeeper][sap-ha-guide-figure-3035]

  _**Figura 35:** Specificare la chiave di licenza di SIOS DataKeeper_

6.  Quando richiesto, riavviare la macchina virtuale.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurare SIOS DataKeeper

Dopo l'installazione di SIOS DataKeeper su entrambi i nodi, avviare la configurazione. L'obiettivo della configurazione è eseguire la replica di dati sincrona tra i dischi aggiuntivi collegati a ogni macchina virtuale.

1.  Avviare lo strumento di configurazione e gestione di DataKeeper e selezionare il collegamento **Connect Server** (Connetti server).

  ![Figura 36: Strumento di configurazione e gestione di SIOS DataKeeper][sap-ha-guide-figure-3036]

  _**Figura 36:** Strumento di configurazione e gestione di SIOS DataKeeper_

2.  Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di configurazione e gestione; eseguire l'operazione in un altro passaggio per il secondo nodo.

  ![Figura 37: Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di gestione e configurazione; eseguire l'operazione in un altro passaggio per il secondo nodo][sap-ha-guide-figure-3037]

  _**Figura 37:** Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di gestione e configurazione; eseguire l'operazione in un altro passaggio per il secondo nodo_

3.  Creare il processo di replica tra i due nodi.

  ![Figura 38: Creare un processo di replica][sap-ha-guide-figure-3038]

  _**Figura 38:** Creare un processo di replica_

  Per la creazione di un processo di replica è disponibile una procedura guidata.

4.  Definire il nome del processo di replica.

  ![Figura 39: Definire il nome del processo di replica][sap-ha-guide-figure-3039]

  _**Figura 39:** Definire il nome del processo di replica_

  ![Figura 40: Definire i dati di base per il nodo che deve essere il nodo di origine corrente][sap-ha-guide-figure-3040]

  _**Figura 40:** Definire i dati di base per il nodo che deve essere il nodo di origine corrente_

5.  Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione.

  ![Figura 41: Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione corrente][sap-ha-guide-figure-3041]

  _**Figura 41:** Definire il nome, l'indirizzo TCP/IP e un volume del disco del nodo di destinazione corrente_

6.  Definire gli algoritmi di compressione. Nell'esempio è consigliabile comprimere il flusso di replica. Soprattutto in caso di risincronizzazione, la compressione del flusso di replica riduce notevolmente il tempo necessario per l'operazione. La compressione usa le risorse di CPU e RAM di una macchina virtuale. Con l'aumentare del tasso di compressione aumenta anche il volume delle risorse di CPU usate. È possibile modificare questa impostazione in un secondo momento.

7.  Un'altra impostazione da verificare è se la replica viene eseguita in modalità sincrona o asincrona. Per proteggere le configurazioni di SAP ASCS/SCS, è necessario usare la replica sincrona.  

  ![Figura 42: Definire i dettagli della replica][sap-ha-guide-figure-3042]

  _**Figura 42:** Definire i dettagli della replica_

8.  Definire se il volume replicato dal processo di replica deve essere rappresentato in una configurazione di cluster Windows Server Failover Cluster come disco condiviso. Per la configurazione di SAP ASCS/SCS è necessario scegliere **Yes** in modo che il cluster di Windows rilevi il volume replicato come disco condiviso che può essere usato come volume del cluster.

  ![Figura 43: Selezionare Sì per impostare il volume replicato come volume del cluster][sap-ha-guide-figure-3043]

  _**Figura 43:** Selezionare **Yes** per impostare il volume replicato come volume del cluster_

  Dopo aver creato il volume, lo strumento di configurazione e gestione di DataKeeper mostra che il processo di replica è attivo.

  ![Figura 44: Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo][sap-ha-guide-figure-3044]

  _**Figura 44:** Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo_

  Gestione cluster di failover visualizza ora il disco come disco di DataKeeper, come illustrato nella figura 45:

  ![Figura 45: Gestione cluster di failover visualizza il disco replicato da DataKeeper][sap-ha-guide-figure-3045]

  _**Figura 45:** Gestione cluster di failover visualizza il disco replicato da DataKeeper_

## <a name="next-steps"></a>Passaggi successivi

* [Installazione della disponibilità elevata di SAP NetWeaver nel cluster di failover Windows e nei dischi condivisi per l'istanza SAP (A)SCS in Azure][sap-high-availability-installation-wsfc-shared-disk]

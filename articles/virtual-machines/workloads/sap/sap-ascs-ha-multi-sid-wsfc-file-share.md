---
title: "Disponibilità elevata multi-SID dell'istanza SAP A(SCS) con Windows Server Failover Clustering e condivisione file in Azure | Microsoft Docs"
description: "Disponibilità elevata multi-SID per l'istanza SAP A(SCS) con Windows Server Failover Clustering e condivisione file in Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9f0beae1c92c40bb89decc6ca567aed98c5e29b4
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2017
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-official-ha-file-share-document]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[sofs-overview]:https://technet.microsoft.com/library/hh831349(v=ws.11).aspx
[new-in-win-2016-storage]:https://docs.microsoft.com/windows-server/storage/whats-new-in-storage

[sap-installation-guides]:http://service.sap.com/instguides
[sap-installation-guides-file-share]:https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

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

[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md#25e358f8-92e5-4e8d-a1e5-df7580a39cb0
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

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

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

# <a name="sap-ascs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-file-share-on-azure"></a>Disponibilità elevata multi-SID dell'istanza SAP A(SCS) con Windows Server Failover Clustering e condivisione file in Azure

> ![Windows][Logo_Windows] Windows
>

A settembre 2016, Microsoft ha rilasciato una funzionalità con cui è possibile gestire più indirizzi IP virtuali usando un [servizio di bilanciamento del carico interno di Azure][load-balancer-multivip-overview]. Questa funzionalità esiste già nel servizio di bilanciamento del carico esterno di Azure.

Se si dispone di una distribuzione di SAP, è possibile usare un bilanciamento del carico interno per creare una configurazione del cluster Windows per SAP ASCS/SCS.

Questo articolo si concentra sul passaggio da un'installazione di ASCS/SCS singola a una configurazione di SAP multi-SID tramite l'installazione di istanze aggiuntive del cluster ASCS/SCS di SAP in un cluster di Windows Server Failover Clustering con la **condivisione file**. Al termine di questo processo, verrà configurato un cluster multi-SID di SAP.

> [!NOTE]
>
> Questa funzionalità è disponibile solo nel modello di distribuzione **Azure Resource Manager**.
>
>È previsto un limite di indirizzi IP front-end privati per ogni servizio di bilanciamento del carico interno di Azure.
>
>Il numero massimo di istanze di SAP ASCS/SCS in un cluster WSFC è uguale al numero massimo di indirizzi IP front-end privati per ogni servizio di bilanciamento del carico interno di Azure.
>

Per informazioni sui limiti del servizio di bilanciamento del carico vedere "IP front-end privato per ogni servizio di bilanciamento del carico" in [Limiti relativi alle reti - Azure Resource Manager][networking-limits-azure-resource-manager].

## <a name="prerequisites"></a>Prerequisiti

È già stato configurato un cluster WSFC usato per **una sola** istanza SAP ASCS/SCS con la **condivisione file**, come illustrato nella figura seguente.

![Figura 1: Istanza SAP (A)SCS e SOFS distribuito in DUE cluster][sap-ha-guide-figure-8007]

_**Figura 1:** Istanza SAP (A)SCS e SOFS distribuito in DUE cluster_

> [!IMPORTANT]
> Il programma di installazione deve soddisfare le condizioni seguenti:
> * Le istanze di SAP ASCS/SCS devono condividere lo stesso cluster WSFC.
> * Ogni SID DBMS deve avere un cluster WSFC dedicato.
> * I server applicazioni SAP che fanno parte di un SID del sistema SAP devono avere proprie VM dedicate.

## <a name="sap-ascs-multi-sid-architecture-with-file-share"></a>Architettura multi-SID di SAP (A)SCS con condivisione file

L'obiettivo è l'installazione di più istanze cluster di SAP ABAP ASCS o SAP Java SCS nello stesso cluster WSFC, come illustrato di seguito:

![Figura 2: Configurazione multi-SID di SAP in due cluster][sap-ha-guide-figure-8008]

_**Figura 2:** Configurazione multi-SID di SAP in due cluster_

L'installazione di un altro sistema SAP **&lt;SID2&gt;** è identica all'installazione di un sistema <SID>. Sono necessari due passaggi di preparazione aggiuntivi nel cluster (A)SCS e nel cluster SOFS in condivisione file.

## <a name="infrastructure-preparation-for-sap-multi-sid-scenario"></a>Preparazione dell'infrastruttura per lo scenario di multi-SID di SAP

### <a name="infrastructure-preparation-on-domain-controller"></a>Preparazione dell'infrastruttura in Controller di dominio

Creare il gruppo di dominio **&lt;Domain&gt;\SAP_&lt;SID2&gt;_GlobalAdmin**, ad esempio, con &lt;SID2&gt; = PR2. Il nome del gruppo di dominio è <Domain>\SAP_PR2_GlobalAdmin

### <a name="infrastructure-preparation-on-ascs-cluster"></a>Preparazione dell'infrastruttura nel cluster (A)SCS

È necessario preparare l'infrastruttura nel cluster (A)SCS esistente, per un secondo SAP &lt;SID&gt;:

* Creare un nome host virtuale per l'istanza di SAP ASCS/SCS in cluster sul server DNS
* Aggiungere un indirizzo IP a un servizio di bilanciamento del carico interno di Azure esistente usando PowerShell

Questa procedura è descritta in [Preparazione dell'infrastruttura per lo scenario di multi-SID di SAP][sap-ascs-ha-multi-sid-wsfc-shared-disk-infrast-prepare].


### <a name="infrastructure-preparation-on-sofs-cluster-using-existing-sap-global-host"></a>Preparazione dell'infrastruttura nel cluster SOFS con l'host SAP GLOBALP esistente

È possibile scegliere di usare di nuovo **&lt;SAPGLOBALHost&gt;** e **Volume1** del primo sistema SAP <SID1>.

![Figura 3: File server di scalabilità orizzontale multi-SID con lo stesso nome host di SAP GLOBAL][sap-ha-guide-figure-8014]

_**Figura 3:** File server di scalabilità orizzontale multi-SID con lo stesso nome host di SAP GLOBAL_

> [!IMPORTANT]
>Per il secondo sistema **SAP &lt;SID2&gt;**, viene usato lo stesso Volume1 e la stessa rete **&lt;SAPGlobalHost&gt;**.
>Se si desidera usare di nuovo lo stesso nome di rete **&lt;SAPGlobalHost&gt;**, poiché è stato definito il nome condivisione **SAPMNT** per diversi sistemi SAP, è obbligatorio usare lo stesso **Volume1**.
>
>Il percorso del file per l'host globale <SID2> è: C:\ClusterStorage\\**Volume1**\usr\sap\<SID2>\SYS\
>

È necessario preparare la cartella SAP GLOBAL HOST **...\SYS\..** nel cluster SOFS per il sistema &lt;SID2&gt;.

Eseguire lo script di PowerShell seguente per preparare SAP GLOBAL HOST all'istanza &lt;SID2&gt;:


```PowerShell
##################
# SAP Multi-SID
##################

$SAPSID2 = "PR2"
$DomainName2 = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName2 = "$DomainName2\SAP_" + $SAPSID2 + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSCluster2Node1 = "ja1-ascs-0"
$ASCSCluster2Node2 = "ja1-ascs-1"

# Define SAP (A)SCS cluster node computer objects
$ASCSCluster2ObjectNode1 = "$DomainName2\$ASCSCluster2Node1$"
$ASCSCluster2ObjectNode2 = "$DomainName2\$ASCSCluster2Node2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder2 = "C:\ClusterStorage\Volume1\usr\sap\$SAPSID2\SYS"
New-Item -Path $SAPGlobalFOlder2 -ItemType Directory

# Add permissions for SAP SID2 system
Grant-SmbShareAccess -Name sapmnt -AccountName $SAPSIDGlobalAdminGroupName2, $ASCSCluster2ObjectNode1, $ASCSCluster2ObjectNode2 -AccessRight Full -Force


$UsrSAPFolder = "C:\ClusterStorage\Volume1\usr\sap\"

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName2,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSCluster2ObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

### <a name="infrastructure-preparation-on-sofs-cluster-using-different-sap-global-host"></a>Preparazione dell'infrastruttura nel cluster SOFS con un host SAP GLOBALP diverso

È possibile configurare il secondo SOFS, ad esempio il secondo ruolo del cluster SOFS con **&lt;SAPGlobalHost2&gt;** e **Voulme2** diverso per il secondo **&lt;SID2&gt;**.

![Figura 4: File server di scalabilità orizzontale multi-SID con lo stesso nome host 2 di SAP GLOBAL][sap-ha-guide-figure-8015]

_**Figura 4:** File server di scalabilità orizzontale multi-SID con lo stesso nome host 2 di SAP GLOBAL_

Eseguire questo script di PowerShell per creare il secondo ruolo SOFS con &lt;SAPGlobalHost2&gt;:

```PowerShell
# Create SOFS with SAP Global Host Name 2
$SAPGlobalHostName = "sapglobal2"
Add-ClusterScaleOutFileServerRole -Name $SAPGlobalHostName
```

Creare il secondo **Volume2**. Eseguire questo script di PowerShell:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName SAPPR2 -FileSystem CSVFS_ReFS -Size 5GB -ResiliencySettingName Mirror
```

![Figura 5: File server di scalabilità orizzontale multi-SID con lo stesso nome host 2 di SAP GLOBAL][sap-ha-guide-figure-8016]

_**Figura 5:** Secondo **Volume2** in Gestione Cluster di Failover_

Creare una cartella SAP GLOBAL per il secondo **&lt;SID2&gt;** e impostare la sicurezza.

Eseguire questo script di PowerShell:

```PowerShell
# Create folder for <SID2> on second Volume2 and set file security
$SAPSID = "PR2"
$DomainName = "SAPCLUSTER"
$SAPSIDGlobalAdminGroupName = "$DomainName\SAP_" + $SAPSID + "_GlobalAdmin"

# SAP (A)SCS cluster nodes
$ASCSClusterNode1 = "ascs-1"
$ASCSClusterNode2 = "ascs-2"

# Define SAP (A)SCS cluster node computer objects
$ASCSClusterObjectNode1 = "$DomainName\$ASCSClusterNode1$"
$ASCSClusterObjectNode2 = "$DomainName\$ASCSClusterNode2$"

# Create usr\sap\.. folders on CSV
$SAPGlobalFolder = "C:\ClusterStorage\Volume2\usr\sap\$SAPSID\SYS"
New-Item -Path $SAPGlobalFOlder -ItemType Directory

$UsrSAPFolder = "C:\ClusterStorage\Volume2\usr\sap\"

# Set files & folder security
$Acl = Get-Acl $UsrSAPFolder

# Add file security object of SAP_<sid>_GlobalAdmin group
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($SAPSIDGlobalAdminGroupName,"FullControl", 'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode1$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode1,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Add security object of clusternode2$ computer object
$Ar = New-Object  system.security.accesscontrol.filesystemaccessrule($ASCSClusterObjectNode2,"FullControl",'ContainerInherit,ObjectInherit', 'None', 'Allow')
$Acl.SetAccessRule($Ar)

# Set security
Set-Acl $UsrSAPFolder $Acl -Verbose
```

Per creare una condivisione di file SAPMNT su **Volume2** con il nome host **&lt;SAPGlobalHost2&gt;**, per il secondo SAP **&lt;SID2&gt;**, avviare la seguente procedura guidata in Gestione cluster di failover.

Fare clic con il pulsante destro del mouse sul gruppo di cluster SOFS saoglobal2 e scegliere "Aggiungi condivisione file".

![Figura 6: Avviare la procedura guidata "Aggiungi condivisione file"][sap-ha-guide-figure-8017]

_**Figura 6:** Avviare la procedura guidata "Aggiungi condivisione file"_

![Figura 7: Scegliere Condivisione SMB - rapida][sap-ha-guide-figure-8018]

_**Figura 7:** Scegliere Condivisione SMB - rapida_

![Figura 8: Scegliere sapglobalhost2 e specificare un percorso su Volume2][sap-ha-guide-figure-8019]

_**Figura 8:** Scegliere **sapglobalhost2** e specificare un percorso su Volume2_


![Figura 9: Impostare il nome di condivisione file su sapmnt][sap-ha-guide-figure-8020]

_**Figura 9:** Impostare il nome di condivisione file su **sapmnt**_

![Figura 10: Disabilitare tutte le impostazioni][sap-ha-guide-figure-8021]

_**Figura 10:** Disabilitare tutte le impostazioni_

Assicurarsi di impostare "Controllo completo" su file e condivisione sapmnt per:
* il gruppo utente di dominio **SAP_&lt;SID&gt;_GlobalAdmin**
* l'oggetto computer dei nodi del cluster (A)SCS **ascs-1$** e **ascs-2$**

![Figura 11: Verificare di impostare l'accesso completo agli account del gruppo utenti e del computer][sap-ha-guide-figure-8022]

_**Figura 11:** Verificare di impostare l'accesso completo agli account del gruppo utenti e del computer_

![Figura 12: Fare clic su Crea][sap-ha-guide-figure-8023]

_**Figura 12:** Fare clic su Crea_

![Figura 13: Viene creato il secondo sapmnt associato all'host sapglobal2 e Volume2][sap-ha-guide-figure-8024]

_**Figura 13:** Viene creato il **secondo sapmnt** associato all'host **sapglobal2** e **Volume2**_

## <a name="sap-netweaver-multi-sid-installation"></a>Installazione di SAP NetWeaver multi-SID

### <a name="sap-ltsid2gt-ascs-and-ers-instances-installation"></a>Installazione delle istanze SAP &lt;SID2&gt; (A)SCS ed ERS

Seguire la stessa procedura di installazione e configurazione descritta per il SAP &lt;SID&gt;.

### <a name="install-dbms-and-sap-application-servers"></a>Installare i server applicazioni DBMS e SAP
Installare i server applicazioni DBMS e SAP come descritto in precedenza.



## <a name="next-steps"></a>Passaggi successivi

* [Installation of an (A)SCS Instance on a Failover Cluster with no Shared Disks (Installazione di un'istanza di (A)SCS in un cluster di failover senza dischi condivisi) - Linee guida SAP ufficiali per la condivisione di file e la disponibilità elevata][sap-official-ha-file-share-document]:

* [Spazi di archiviazione diretta in Windows Server 2016][s2d-in-win-2016]

* [Panoramica di File server di scalabilità orizzontale per dati applicazioni][sofs-overview]

* [Novità nell'archiviazione in Windows Server 2016][new-in-win-2016-storage]

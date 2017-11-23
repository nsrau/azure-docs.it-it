---
title: "Architettura e scenari di disponibilità elevata in Macchine virtuali di Azure per SAP NetWeaver | Microsoft Docs"
description: "Architettura e scenari di disponibilità elevata per SAP NetWeaver in Macchine virtuali di Azure"
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 31f3765d807882e65a247819a5999c191f9e7ac5
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architettura e scenari di disponibilità elevata per SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:http://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:http://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability
[azure-storage-redundancy]:http://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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


## <a name="terminology-definitions"></a>Definizioni della terminologia

**Disponibilità elevata**: il termine si riferisce a un set di tecnologie che riduce al minimo i disservizi IT assicurando la continuità aziendale dei servizi IT tramite componenti ridondanti, a tolleranza di errore o protetti con failover all'interno dello *stesso* data center. In questo caso, il data center si trova all'interno di un'area di Azure.

**Ripristino di emergenza**: il termine indica anche ridurre al minimo le interruzioni per i servizi IT e il relativo ripristino, ma attraverso *vari* data center che potrebbero essere distanti anche centinaia di chilometri l'uno dall'altro. In questo caso, i data center potrebbero trovarsi in varie aree di Azure all'interno della stessa area geopolitica o in posizioni stabilite dal cliente.


## <a name="overview-of-high-availability"></a>Panoramica della disponibilità elevata
La disponibilità elevata di SAP in Azure può essere di tre tipi:

* **Disponibilità elevata dell'infrastruttura di Azure**: 

    la disponibilità elevata può includere, ad esempio, l'infrastruttura di calcolo (macchine virtuali), rete o archiviazione e i relativi vantaggi per aumentare la disponibilità delle applicazioni SAP.

* **Uso del riavvio delle macchine virtuali dell'infrastruttura di Azure per ottenere una *maggiore disponibilità* delle applicazioni SAP**: 

    se si decide di non usare funzionalità come Windows Server Failover Clustering (WSFC) o Pacemaker in Linux, viene usato il riavvio delle macchine virtuali di Azure. Questa funzionalità protegge i sistemi SAP da tempi di inattività pianificati e non pianificati dell'infrastruttura di server fisici di Azure e della piattaforma Azure sottostante nel suo complesso.

* **Disponibilità elevata delle applicazioni SAP**: 

    Per ottenere una disponibilità elevata completa del sistema SAP, è necessario proteggere tutti i componenti SAP critici del sistema. Ad esempio:
    * Server di applicazioni SAP ridondanti.
    * Componenti univoci. Un esempio potrebbe essere un componente singolo punto di guasto (SPOF), come un'istanza di SAP ASCS/SCS o un sistema di gestione di database (DBMS).

La disponibilità elevata di SAP in Azure presenta alcune differenze rispetto alla disponibilità elevata di SAP in un ambiente fisico o virtuale locale. Il documento seguente, [AP NetWeaver high availability and business continuity in virtual environments with VMware and Hyper-V on Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] (Disponibilità elevata e continuità aziendale di SAP NetWeaver negli ambienti virtuali con VMware e Hyper-V in Microsoft Windows) descrive le configurazioni standard di disponibilità elevata SAP in ambienti virtualizzati di Windows.

Per Linux non è disponibile alcuna configurazione a disponibilità elevata di SAP integrata in SAPinst come quella disponibile per Windows. Per informazioni sulla disponibilità elevata di SAP in locale per Linux, vedere [High availability partner information][sap-ha-partner-information] (Informazioni di disponibilità elevata sui partner).

## <a name="azure-infrastructure-high-availability"></a>Disponibilità elevata dell'infrastruttura di Azure

### <a name="sla-for-single-instance-virtual-machines"></a>Contratto di servizio per le macchine virtuali a istanza singola

Al momento è previsto un contratto di servizio a singola macchina virtuale del 99,9% con archiviazione premium. Per avere un'idea di come potrebbe essere la disponibilità di una singola macchina virtuale, è possibile calcolare il prodotto dei vari [contratti di servizio di Azure][azure-sla] disponibili.

La base per il calcolo è di 30 giorni al mese o 43.200 minuti. Ad esempio, un tempo di inattività dello 0,05% corrisponde a 21,6 minuti. Come di consueto, la disponibilità dei vari servizi viene calcolata nel modo seguente:

(Disponibilità servizio n. 1/100) * (Disponibilità servizio n. 2/100) * (Disponibilità servizio n. 3/100) \*…

ad esempio:

(99,95/100) + (99,9/100) + (99,9/100) = 0,9975 o una disponibilità complessiva del 99,75%.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Istanze multiple di macchine virtuali nello stesso set di disponibilità
Per tutte le macchine virtuali con due o più istanze distribuite nello stesso *set di disponibilità*, Microsoft garantisce la connettività ad almeno un'istanza per almeno il 99,95% del tempo.

Quando due o più macchine virtuali appartengono allo stesso set di disponibilità, a ciascuna macchina virtuale nel set di disponibilità viene assegnato un *dominio di aggiornamento* e un *dominio di errore* dalla piattaforma Azure sottostante.

* I **domini di aggiornamento** garantiscono che più macchine virtuali non vengano riavviate contemporaneamente durante la manutenzione pianificata dell'infrastruttura di Azure, ma che venga riavviata una sola macchina virtuale alla volta.

* I **domini di errore** garantiscono che le macchine virtuali vengano distribuite su componenti hardware che non condividono alimentatori e commutatori di rete. In caso di inattività non pianificata di server, commutatori di rete o alimentatori, ne risente solo una macchina virtuale.

Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure][azure-virtual-machines-manage-availability].

Un set di disponibilità viene usato per ottenere una disponibilità elevata di:

* Server di applicazioni SAP ridondanti.  
* Cluster con due o più nodi (macchine virtuali, ad esempio) che proteggono SPOF, come un'istanza di SAP ASCS/SCS o un sistema DBMS.

### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Manutenzione pianificata e non pianificata delle macchine virtuali

Due tipi di eventi della piattaforma Azure possono influire sulla disponibilità delle macchine virtuali:

* Gli eventi di **manutenzione pianificata** sono gli aggiornamenti periodici eseguiti da Microsoft per la piattaforma Azure sottostante. Gli aggiornamenti migliorano l'affidabilità, le prestazioni e la sicurezza complessive dell'infrastruttura della piattaforma su cui vengono eseguite le macchine virtuali.

* Gli eventi di **manutenzione non pianificata** hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dal server fisico non integro su cui è in esecuzione a un server fisico integro. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure
I dati dell'account di archiviazione vengono sempre replicati per assicurare durabilità e disponibilità elevata nonché il rispetto del contratto di servizio di Archiviazione di Azure anche in caso di errori hardware temporanei.

Dato che Archiviazione di Azure mantiene tre immagini dei dati per impostazione predefinita, l'uso di RAID 5 o RAID 1 su più dischi di Azure non è necessario.

Per altre informazioni, vedere [Replica di Archiviazione di Azure][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
I dischi offerti dal servizio Managed Disks sono un nuovo tipo di risorsa in Azure Resource Manager, utilizzabile al posto dei dischi rigidi virtuali (VHD) archiviati negli account di archiviazione di Azure. I dischi gestiti vengono allineati automaticamente al set di disponibilità della macchina virtuale a cui sono collegati e aumentano la disponibilità della macchina virtuale e dei servizi eseguiti su di essa.

Per altre informazioni, vedere [Panoramica del servizio Managed Disks][azure-storage-managed-disks-overview].

Si consiglia di usare i dischi gestiti, perché semplificano la distribuzione e la gestione delle macchine virtuali.

SAP supporta attualmente solo il servizio Managed Disks Premium. Per altre informazioni, vedere la nota SAP [1928533].

## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Uso della disponibilità elevata dell'infrastruttura di Azure per ottenere una *maggiore disponibilità* delle applicazioni SAP

Se si decide di non usare funzionalità come WSFC o Pacemaker in Linux (attualmente supportato solo per SUSE Linux Enterprise Server [SLES] 12 e versioni successive), viene usato il riavvio delle macchine virtuali di Azure. Questa funzionalità protegge i sistemi SAP da tempi di inattività pianificati e non pianificati dell'infrastruttura di server fisici di Azure e della piattaforma Azure sottostante nel suo complesso.

Per altre informazioni su questo approccio, vedere [Utilize Azure infrastructure VM restart to achieve higher availability of the SAP system][sap-higher-availability] (Usare il riavvio delle macchine virtuali nell'infrastruttura di Azure per ottenere una maggiore disponibilità del sistema SAP).

## <a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Disponibilità elevata delle applicazioni SAP su IaaS di Azure

Per ottenere una disponibilità elevata completa del sistema SAP, è necessario proteggere tutti i componenti SAP critici del sistema. ad esempio:
  * Server di applicazioni SAP ridondanti.
  * Componenti univoci. Un esempio potrebbe essere un componente singolo punto di guasto (SPOF), come un'istanza di SAP ASCS/SCS o un sistema di gestione di database (DBMS).

Le sezioni seguenti illustrano come ottenere la disponibilità elevata per tutti e tre i componenti critici del sistema SAP.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Architettura di disponibilità elevata per i server applicazioni SAP

> Questa sezione si applica a:
>
> ![Windows][Logo_Windows] Windows e ![Linux][Logo_Linux] Linux
>

In genere non è necessaria una specifica soluzione a disponibilità elevata per le istanze del server applicazioni SAP e le istanze di dialogo. La disponibilità elevata si ottiene tramite la ridondanza e si dovranno configurare più istanze di dialogo in varie istanze di macchine virtuali di Azure. È necessario avere almeno due istanze dell'applicazione SAP installate in due istanze di macchine virtuali di Azure.

![Figura 1: server applicazioni SAP a disponibilità elevata][sap-ha-guide-figure-2000]

_**Figura 1:** server applicazioni SAP a disponibilità elevata_

È necessario inserire tutte le macchine virtuali che ospitano le istanze del server applicazioni SAP nello stesso set di disponibilità di Azure. Un set di disponibilità di Azure assicura che:

* Tutte le macchine virtuali facciano parte dello stesso dominio di aggiornamento.  
    Un dominio di aggiornamento assicura che le macchine virtuali non vengano aggiornate contemporaneamente durante i tempi di inattività per la manutenzione pianificata.

    La funzionalità di base, che si poggia su diversi domini di aggiornamento e di errore all'interno di un'unità di scala di Azure, è stata già presentata nella sezione [Domini di aggiornamento][planning-guide-3.2.2].

* Tutte le macchine virtuali facciano parte dello stesso dominio di errore.  
    Un dominio di errore assicura che le macchine virtuali vengano distribuite in modo che nessun singolo punto di guasto influisca sulla disponibilità di tutte le macchine virtuali.

Il numero di domini di aggiornamento e di errore che può essere usato da un set di disponibilità di Azure all'interno di un'unità di scala di Azure è limitato. Se si continuano ad aggiungere macchine virtuali a un singolo set di disponibilità, due o più macchine virtuali finiranno nello stesso dominio di errore o di aggiornamento.

Se si distribuiscono poche istanze dei server applicazioni SAP nelle relative macchine virtuali dedicate e si suppone che siano presenti cinque domini di aggiornamento, emerge il quadro seguente. Il numero massimo effettivo di domini di aggiornamento e di errore all'interno di un set di disponibilità potrebbe cambiare in futuro:

![Figura 2: disponibilità elevata dei server applicazioni SAP in un set di disponibilità di Azure][planning-guide-figure-3000]
_**Figura 2:** disponibilità elevata dei server applicazioni SAP in un set di disponibilità di Azure_

Per altre informazioni, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure][azure-virtual-machines-manage-availability].

Per altre informazioni, vedere la sezione [Set di disponibilità di Azure][planning-guide-3.2.3] del documento Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver.

**Solo dischi non gestiti:** poiché l'account di archiviazione di Azure è un potenziale singolo punto di guasto, è importante avere almeno due account di archiviazione di Azure, in cui distribuire almeno due macchine virtuali. In una configurazione ideale, il disco di ogni macchina virtuale che esegue l'istanza di una finestra di dialogo SAP verrà distribuito in un account di archiviazione diverso.

> [!IMPORTANT]
> Si consiglia di usare Azure Managed Disks per le installazioni di SAP a disponibilità elevata. Dato che i dischi gestiti si allineano automaticamente al set di disponibilità della macchina virtuale a cui sono collegati, aumentano la disponibilità della macchina virtuale e dei servizi in esecuzione su di essa.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Architettura di disponibilità elevata per un'istanza di SAP ASCS/SCS in Windows

> ![Windows][Logo_Windows] Windows
>

È possibile usare una soluzione WSFC per proteggere l'istanza di SAP ASCS/SCS. La soluzione presenta due varianti:

* **Inserire l'istanza di SAP ASCS/SCS in un cluster usando i dischi condivisi cluster**: per altre informazioni su questa architettura, vedere [Clustering dell'istanza di SAP ASCS/SCS nel cluster di failover Windows con i dischi condivisi del cluster in Azure] [ sap-high-availability-guide-wsfc-shared-disk].   

* **Inserire l'istanza di SAP ASCS/SCS in un cluster usando una condivisione file**: per altre informazioni su questa architettura, vedere [Clustering dell'istanza di SAP ASCS/SCS nel cluster di failover di Windows usando la condivisione file di Azure] [ sap-high-availability-guide-wsfc-file-share].

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Architettura di disponibilità elevata per un'istanza di SAP ASCS/SCS in Linux

> ![Linux][Logo_Linux] Linux
>
Per altre informazioni sul clustering dell'istanza di SAP ASCS/SCS tramite il framework di clustering SLES, vedere [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server per le applicazioni SAP][sap-suse-ascs-ha].

### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>Configurazione multi-SID dell'istanza di SAP ASCS/SCS con cluster di SAP NetWeaver

> ![Windows][Logo_Windows] Windows
>
> Attualmente, la configurazione multi-SID è supportata solo con WSFC. La configurazione multi-SID è supportata tramite la condivisione file e il disco condiviso.
>
Per altre informazioni sull'architettura di disponibilità elevata multi-SID, vedere:

* [Disponibilità elevata multi-SID dell'istanza di SAP ASCS/SCS con Windows Server Failover Clustering e condivisione file][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Disponibilità elevata multi-SID dell'istanza di SAP ASCS/SCS con Windows Server Failover Clustering e disco condiviso][sap-ascs-ha-multi-sid-wsfc-shared-disk]

### <a name="high-availability-dbms-instance"></a>Istanza di DBMS a disponibilità elevata

Anche DBMS è un singolo punto di contatto in un sistema SAP. È necessario proteggerlo usando una soluzione a disponibilità elevata. La figura seguente illustra una soluzione a disponibilità elevata SQL Server AlwaysOn in Azure con Windows Server Failover Clustering e il bilanciamento del carico interno di Azure. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log DBMS tramite la replica propria di DBMS. In questo caso, non sono necessari dischi condivisi di cluster, pertanto l'intera configurazione risulta semplificata.

![Figura 3: esempio di DBMS SAP a disponibilità elevata con SQL Server AlwaysOn][sap-ha-guide-figure-2003]

_**Figura 3:** esempio di DBMS SAP a disponibilità elevata con SQL Server AlwaysOn_

Per altre informazioni sul clustering di DBMS di SQL Server in Azure con il modello di distribuzione di Azure Resource Manager, vedere questi articoli:

* [Panoramica sui gruppi di disponibilità AlwaysOn di SQL Server in macchine virtuali di Azure][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Per altre informazioni sul clustering di DBMS di SAP HANA in Azure con il modello di distribuzione Azure Resource Manager, vedere [Disponibilità elevata di SAP HANA in Macchine virtuali di Azure (VM)][sap-hana-ha].

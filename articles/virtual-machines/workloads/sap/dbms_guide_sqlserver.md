---
title: Distribuzione DBMS per SQL Server di macchine virtuali di Azure per un carico di lavoro SAP | Microsoft Docs
description: Distribuzione DBMS per SQL Server di macchine virtuali di Azure per un carico di lavoro SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/11/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: db0d796a407c8e33501b0a312c78e8508f17297d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075306"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Distribuzione DBMS per SQL Server di macchine virtuali di Azure per un SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/premium-storage.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Il presente documento illustra le numerose aree da valutare quando si distribuisce SQL Server per un carico di lavoro SAP in IaaS di Azure. Prima di questo documento è consigliabile avere letto il documento [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP), nonché le altre guide disponibili nella [documentazione relativa a un carico di lavoro SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 



> [!IMPORTANT]
> L'ambito di questo documento è la versione Windows di SQL Server. SAP non supporta la versione Linux di SQL Server con qualsiasi software SAP. Questo documento non illustra il database SQL di Microsoft Azure, una piattaforma distribuita come servizio offerta nell'ambito della piattaforma Microsoft Azure. Il documento illustra l'esecuzione del prodotto SQL Server, noto per le distribuzioni locali in macchine virtuali di Azure, tramite la funzionalità di infrastruttura distribuita come servizio di Azure. Le funzionalità di database di queste due offerte sono diverse e non devono essere confuse tra loro. Vedere anche: <https://azure.microsoft.com/services/sql-database/>
> 
>

Per eseguire i carichi di lavoro SAP in IaaS di Azure, è consigliabile in linea generale usare le versioni più recenti di SQL Server. Le versioni più recenti di SQL Server offrono una migliore integrazione con alcuni dei servizi e delle funzionalità di Azure o contengono modifiche che ottimizzano le operazioni in un'infrastruttura IaaS di Azure.

È consigliabile esaminare [questa][virtual-machines-sql-server-infrastructure-services] documentazione prima di proseguire.

Nelle sezioni seguenti vengono aggregate e citate parti della documentazione disponibile visitando il collegamento indicato. Vengono anche riportate le specifiche relative a SAP e alcuni concetti vengono descritti in modo più dettagliato. Tuttavia, è consigliabile esaminare la documentazione indicata prima di leggere la documentazione specifica di SQL Server.

Di seguito sono indicate alcune informazioni specifiche su SQL Server in IaaS che è necessario conoscere per poter continuare:

* **Supporto della versione SQL**: per i clienti SAP, le macchine virtuali di Microsoft Azure supportano SQL Server 2008 R2 e versioni successive. Le versioni precedenti non sono supportate. Per altre informazioni, vedere questa [informativa sul supporto](https://support.microsoft.com/kb/956893) di carattere generale. Anche SQL Server 2008 è in genere supportato da Microsoft. A causa delle importanti funzionalità per SAP introdotte con SQL Server 2008 R2, tuttavia, quest'ultima è la versione minima per SAP. Per eseguire i carichi di lavoro SAP in IaaS di Azure, è consigliabile in linea generale usare le versioni più recenti di SQL Server. Le versioni più recenti di SQL Server offrono una migliore integrazione con alcuni dei servizi e delle funzionalità di Azure o contengono modifiche che ottimizzano le operazioni in un'infrastruttura IaaS di Azure. Il documento è quindi limitato a SQL Server 2016 e SQL Server 2017.
* **Prestazioni SQL**: anche se le prestazioni delle macchine virtuali ospitate in Microsoft Azure sono elevate rispetto ad altre offerte per la virtualizzazione del cloud pubblico, i singoli risultati ottenuti possono variare. Leggere l'articolo [Performance best practices for SQL Server in Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance) (Procedure consigliate sulle prestazioni per SQL Server nelle macchine virtuali di Azure).
* **Uso di immagini disponibili in Azure Marketplace**: il modo più rapido per distribuire una nuova VM di Microsoft Azure consiste nell'usare un'immagine disponibile in Azure Marketplace. In Azure Marketplace sono presenti immagini contenenti le versioni più recenti di SQL Server. Non è però possibile usare subito per le applicazioni SAP NetWeaver le immagini in cui SQL Server è già installato. In tali immagini sono infatti installate le regole di confronto predefinite di SQL Server e non quelle richieste dai sistemi SAP NetWeaver. Per usare queste immagini, vedere la procedura documentata nel capitolo [Uso di un'immagine di SQL Server da Microsoft Azure Marketplace][dbms-guide-5.6]. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Raccomandazioni sulla struttura di VM/dischi rigidi virtuali per distribuzioni di SQL Server correlate a SAP
In base alla descrizione generale, gli eseguibili di SQL Server devono essere salvati o installati nell'unità di sistema del disco del sistema operativo della VM (unità C:\).  In genere, la maggior parte dei database di sistema di SQL Server non viene pienamente sfruttata dal carico di lavoro di SAP NetWeaver. È di conseguenza possibile lasciare sull'unità C:\ anche i database di sistema di SQL Server (master, msdb e model). Fa eccezione il database tempdb, che, nel caso di alcuni carichi di lavoro SAP, può richiedere un volume dati o un volume di operazioni di I/O superiore. È consigliabile che il carico di lavoro di operazioni di I/O non venga applicato al disco rigido virtuale del sistema operativo. Per tali sistemi, è consigliabile eseguire la procedura seguente:


* Con tutti i tipi di VM con certificazione SAP (vedere la Nota SAP [1928533]), eccetto le VM serie A, i dati di tempdb e i file di log possono essere memorizzati nell'unità D:\ non persistente. 
* È tuttavia consigliabile usare più file di dati di tempdb. Tenere presente che i volumi delle unità D:\ sono diversi in base al tipo di VM. Per le dimensioni esatte dell'unità D:\ delle diverse VM, vedere l'articolo [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

Con queste configurazioni il database tempdb può consumare più spazio di quello che l'unità di sistema è in grado di offrire. L'unità D:\ non persistente offre inoltre una migliore latenza delle operazioni di I/O e una migliore velocità effettiva (fatta eccezione per le macchine virtuali serie A). Per determinare le dimensioni corrette del database tempdb, è possibile controllare le dimensioni di tempdb nei sistemi esistenti. 

>[!NOTE]
> Se si memorizzano i file di dati di tempdb e il file di log in una cartella nell'unità D:\ creata personalmente, è necessario assicurarsi che la cartella esista dopo un riavvio della VM. Poiché l'unità D:\ viene inizializzata del tutto dopo il riavvio di una macchina virtuale tutte le strutture di file e di directory vengono cancellate. Una possibilità per ricreare le strutture di directory nell'unità D:\ prima dell'avvio del servizio SQL Server è documentata in [questo articolo](http://www.sqlserver.co.uk/index.php/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Una configurazione di VM che esegue SQL Server con un database SAP e in cui i dati e il file di log di tempdb si trovano nell'unità D:\ è simile a quella illustrata nella figura seguente:

![Diagramma di una semplice configurazione di disco di una macchina virtuale per SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Il diagramma soprastante mostra un caso semplice. Come accennato nell'articolo [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP), il numero e la dimensione dei dischi di Archiviazione Premium dipendono da diversi fattori. È tuttavia consigliabile:

- Usare spazi di archiviazione per formare uno o più numeri di volumi che contengono i file di dati SQL Server. Il motivo alla base di questa configurazione è che nella vita reale vengono usati molti database SAP con file di database di dimensioni diverse con carichi di lavoro I/O diversi.
- Usare gli spazi di archiviazione per eseguire sufficienti operazioni di I/O al secondo e per il file registro transazioni SQL Server. Il potenziale carico di lavoro delle operazioni di I/O al secondo è spesso la linea guida usata per la determinazione delle dimensioni del volume del registro transazioni, anziché il potenziale volume del volume di transazioni SQL.
- Usare l'unità D:\ per tempdb fintanto che le prestazioni sono sufficientemente buone. Se il carico di lavoro complessivo è limitato nelle prestazioni dal fatto che tempdb si trova nell'unità D:\, potrebbe essere necessario provare a spostare tempdb in dischi di Archiviazione Premium come consigliato in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).


### <a name="special-for-m-series-vms"></a>Speciale per le macchine virtuali serie M
Per la VM serie M di Azure la latenza di scrittura nel log delle transazioni può essere ridotta di alcuni fattori, in confronto alle prestazioni di Archiviazione Premium di Azure, quando si usa l'acceleratore di scrittura di Azure. È quindi consigliabile distribuire l'acceleratore di scrittura di Azure per i dischi rigidi virtuali che formano il volume per il log delle transazioni SQL Server. I dettagli sono disponibili nel documento relativo all'[acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).
  

### <a name="formatting-the-disks"></a>Formattazione dei dischi
Per i dischi contenenti i file di dati e di log di SQL Server è necessario un blocco NTFS di dimensioni pari a 64 kB. Non è necessario formattare l'unità D:\ perché viene fornita preformattata.

Per evitare che in seguito al ripristino o alla creazione di database i file di dati non vengano inizializzati e il loro contenuto venga azzerato, è necessario assicurarsi che per il contesto utente in cui viene eseguito il servizio SQL Server siano disponibili determinate autorizzazioni. Queste autorizzazioni sono in genere disponibili per gli utenti del gruppo Administrators di Windows. Se il servizio SQL Server viene eseguito nel contesto utente di un utente non appartenente al gruppo Administrators di Windows, è necessario assegnare a tale utente il diritto **Esecuzione attività di manutenzione volume**.  Per informazioni dettagliate, vedere l'articolo <https://support.microsoft.com/kb/2574695> di Microsoft Knowledge Base

### <a name="impact-of-database-compression"></a>Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS come Azure. Se quindi non è stato ancora fatto, sia SAP che Microsoft consigliano di applicare la compressione di pagina di SQL Server prima di caricare un database SAP esistente in Azure.

Sono tre i motivi per cui è consigliabile eseguire la compressione del database prima del caricamento in Azure:

* La quantità di dati da caricare è minore.
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale.
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione del database nelle macchine virtuali di Azure è analogo a quello in locale. Per altre informazioni su come comprimere i database esistenti di SQL Server per SAP NetWeaver, vedere l'articolo [Improved SAP compression tool MSSCOMPRESS](https://blogs.msdn.microsoft.com/saponsqlserver/2016/11/25/improved-sap-compression-tool-msscompress/) (Strumento di compressione SAP migliorato MSSCOMPRESS). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 e versioni successive: archiviazione dei file di database direttamente in Archiviazione BLOB di Azure
In SQL Server 2014 e versioni successive è possibile archiviare file di database direttamente in Archiviazione BLOB di Azure senza il "wrapper" di un disco rigido virtuale. Soprattutto quando si usa Archiviazione Standard di Microsoft Azure o tipi di VM di dimensioni ridotte, questo tipo di distribuzione può essere applicato in scenari in cui è possibile ovviare ai limiti di operazioni di I/O al secondo che sarebbero imposti dal numero limitato di dischi montabili in alcuni tipi di VM di dimensioni ridotte. Si tratta però di una distribuzione valida per i database utente e non per i database di sistema di SQL Server, che funziona anche per i file di log e di dati di SQL Server. Se si preferisce distribuire un database di SQL Server per SAP in questo modo invece di eseguirne il "wrapping" in dischi rigidi virtuali, tenere presente quanto segue:

* L'account di archiviazione usato deve trovarsi nella stessa area di Azure di quello usato per distribuire la VM in cui è in esecuzione SQL Server.
* Le considerazioni elencate in precedenza in merito alla distribuzione di dischi rigidi virtuali in diversi account di archiviazione di Azure sono valide anche per questo metodo di distribuzione. Questo significa che il numero di operazioni di I/O viene tenuto in considerazione ai fini dei limiti dell'account di archiviazione di Azure.
* Invece di considerare la quota di operazioni di I/O di archiviazione della VM, il traffico rispetto ai BLOB di archiviazione che rappresenta i file di log e i file di dati SQL Server viene tenuto in considerazione nella larghezza di banda della rete della VM del tipo di VM specifico. Per la larghezza di banda della rete di un tipo di VM specifico, vedere l'articolo [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
* Gli obiettivi di prestazione delle operazioni di I/O al secondo e della velocità effettiva delle operazioni di I/O che Archiviazione Premium di Azure specifica per le diverse dimensioni di disco non si applicano più. Ciò vale anche se i BLOB creati si trovano in Archiviazione Premium di Azure. Gli obiettivi sono documentati nell'articolo [Archiviazione Premium a prestazioni elevate e dischi gestiti per le VM](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage#scalability-and-performance-targets). In conseguenza della memorizzazione di file di dati e dei file di log SQL Server direttamente in BLOB che sono memorizzati in Archiviazione Premium di Azure, le caratteristiche delle prestazioni possono essere diverse in confronto ai dischi rigidi virtuali in Archiviazione Premium di Azure.
* Nelle macchine virtuali serie M l'acceleratore di scrittura di Azure non può essere usato per supportare operazioni di scrittura inferiori al millisecondo rispetto al file registro transazioni di SQL Server. 

I dettagli di questa funzionalità sono disponibili nell'articolo [File di dati di SQL Server in Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-2017)

Per i sistemi di produzione è consigliabile evitare questa configurazione e scegliere invece di posizionare i file di dati e di registro SQL Server nei dischi rigidi virtuali di Archiviazione Premium di Azure anziché direttamente nei BLOB di Azure.


## <a name="sql-server-2014-buffer-pool-extension"></a>Estensione del pool di buffer di SQL Server 2014
In SQL Server 2014 è stata introdotta una nuova funzionalità denominata [estensione del pool di buffer](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension?view=sql-server-2017). Questa funzionalità estende il pool di buffer di SQL Server, che è mantenuto in memoria con una cache di secondo livello supportata da unità SSD locali di un server o una VM. L'estensione del pool di buffer consente di mantenere "in memoria" un working set di dati di dimensioni maggiori. Rispetto all'accesso ad Archiviazione Standard di Azure, l'accesso nell'estensione del pool di buffer archiviato nelle unità SSD locali di una VM di Azure è notevolmente più veloce. Se si confronta l'estensione del pool di buffer con la cache in lettura di Archiviazione Premium di Azure, come consigliato per i file di dati SQL Server, non sono previsti vantaggi significativi per le estensioni del pool di buffer. Il motivo è che entrambe le cache (estensione del pool di buffer di SQL Server e cache in lettura di Archiviazione Premium) usano i dischi locali del nodo di calcolo di Azure.

Le esperienze vissute nel frattempo con l'estensione del pool di buffer di SQL Server con il carico di lavoro SAP sono miste e non consentono ancora di suggerire con sicurezza se conviene usarla o meno in tutti i casi. Il caso ideale è che il working set richiesto dall'applicazione SAP rientri nella memoria principale. Con Azure che nel frattempo offre VM con memoria fino a 4 TB, dovrebbe essere possibile mantenere il working set nella memoria. L'utilizzo dell'estensione del pool di buffer è quindi limitato ad alcuni casi rari e non deve essere un caso di uso comune.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Considerazioni sul backup/ripristino per SQL Server
Quando si distribuisce SQL Server in Azure, è necessario rivedere la metodologia di backup. Anche se il sistema non è usato in produzione, è necessario eseguire periodicamente il backup del database SAP ospitato da SQL Server. Dal momento che in Archiviazione di Azure vengono mantenute tre immagini, ora un backup è meno importante rispetto alla compensazione di un arresto anomalo della risorsa di archiviazione. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. 

Per esaminare le varie possibilità di backup per SQL Server in Azure, leggere l'articolo [Backup e ripristino per SQL Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery). L'articolo descrive molti modi diversi.

### <a name="manual-backups"></a>Backup manuali
Esistono varie possibilità di eseguire il backup "manuale":

1. Eseguire backup di SQL Server convenzionali in dischi di Azure direttamente collegati. Questa modalità offre il vantaggio di avere i backup subito a disposizione per i ripristini del sistema e la creazione di nuovi sistemi come copia di sistemi SAP esistenti.
2.  SQL Server 2012 CU4 e versioni successive supportano il backup di database a un URL di archiviazione di Azure.
3.  Backup di snapshot di file per i file di database in Archiviazione BLOB di Azure. Questa modalità funziona solo quando i file di dati e di log SQL Server si trovano nella risorsa di archiviazione BLOB di Azure.

La prima modalità è molto nota e si applica anche in molti casi in locale. Lascia tuttavia all'amministratore il compito di risolvere il problema della posizione dei backup a lungo termine. Poiché non è utile mantenere i backup per 30 giorni e oltre nella risorsa di archiviazione di Azure collegata localmente, è necessario usare il servizio Backup di Microsoft Azure o un altro strumento di backup/ripristino di terze parti che includa la gestione degli accessi e della conservazione per i backup. In alternativa si può creare un file server di grandi dimensioni in Azure usando gli spazi di archiviazione di Windows.

La seconda modalità è descritta più in dettaglio nell'articolo [Backup di SQL Server nell'URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017). Le diverse versioni di SQL Server presentano alcune varianti di questa funzionalità. È quindi consigliabile verificare la documentazione specifica della versione di SQL Server in uso. È importante notare che questo articolo elenca molte restrizioni. Esiste anche la possibilità di eseguire il backup in:

- Un singolo BLOB di pagine di Azure, che quindi limita la dimensione del backup a 1000 GB. Ciò limita anche la velocità effettiva che è possibile ottenere.
- Più BLOB in blocchi di Azure (fino a 64), che consente teoricamente una dimensione del backup di 12 TB. I test condotti con i database dei clienti hanno tuttavia rivelato che le dimensioni massime di backup possono essere inferiori al limite teorico. In questo caso, spetta all'amministratore gestire la conservazione dei backup, nonché l'accesso ai backup.


### <a name="automated-backup-for-sql-server"></a>Backup automatizzato per SQL Server
Backup automatizzato offre un servizio di backup automatico per le edizioni di SQL Server Standard ed Enterprise in esecuzione in una macchina virtuale di Azure. Il servizio viene reso disponibile dall'[Estensione SQL Server IaaS Agent](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), installata automaticamente nelle immagini di macchine virtuali Windows di SQL Server nel portale di Azure. Se si distribuiscono le proprie immagini del sistema operativo con SQL Server installato, è necessario installare le estensioni di macchina virtuale separatamente. I passaggi necessari sono documentati in questo [articolo](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

Altre informazioni sulle funzionalità di questa modalità sono disponibili negli articoli seguenti:

- SQL Server 2014: [Backup automatico per macchine virtuali SQL Server 2014 (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)
- SQL Server 2016/2017: [Backup automatico v2 per macchine virtuali in Azure (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2)

Se si cerca nella documentazione, è possibile vedere che la funzionalità è migliorata con le versioni più recenti di SQL Server. Altre informazioni sui backup automatizzati di SQL Server sono disponibili nell'articolo [Backup gestito di SQL Server in Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-2017). Il limite teorico di dimensione del backup è 12 TB.  I backup automatizzati possono essere una buona soluzione per i backup di dimensioni fino a 12 TB. Poiché le operazioni di scrittura in più BLOB avvengono in parallelo, ci si può aspettare una velocità effettiva superiore a 100 MB/sec. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Backup di Azure per macchine virtuali di SQL Server
Questa nuova modalità di backup di SQL Server è disponibile come anteprima pubblica dai servizi Backup di Azure a partire da giugno 2018. Il modo di eseguire il backup di SQL Server è lo stesso usato dagli altri strumenti di terze parti, vale a dire l'interfaccia VSS/VDI di SQL Server per eseguire lo streaming dei backup in un percorso di destinazione. In questo caso il percorso di destinazione è l'insieme di credenziali del servizio Azure Site Recovery.

Una descrizione più dettagliata di questa modalità di backup, che aggiunge molti vantaggi per le configurazioni, il monitoraggio e l'amministrazione centrali dei backup è disponibile [qui](https://docs.microsoft.com/azure/backup/backup-azure-sql-database). 


### <a name="third-party-backup-solutions"></a>Soluzioni di backup di terze parti
Per un certo numero di clienti SAP non è possibile rincominciare da zero e introdurre soluzioni di backup completamente nuove per la parte del panorama SAP in esecuzione in Azure. È quindi necessario usare ed estendere in Azure le soluzioni di backup esistenti. L'estensione in Azure delle soluzioni di backup esistenti risulta essere una soluzione adeguata con la maggior parte dei fornitori principali in quest'area. 


## <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Uso di un'immagine di SQL Server da Microsoft Azure Marketplace
In Microsoft Azure Marketplace sono disponibili VM che contengono già versioni di SQL Server. Per i clienti SAP che necessitano delle licenze per SQL Server e Windows l'uso di queste immagini può essere un'opportunità per ottenere le licenze necessarie scegliendo le VM con SQL Server già installato. Per usare tali immagini per SAP, è necessario considerare quanto segue:

* I costi delle versioni di SQL Server non di valutazione sono più elevati rispetto a una VM solo Windows distribuita da Azure Marketplace. Vedere gli articoli seguenti per confrontare i prezzi: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> e <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* È possibile usare solo versioni di SQL Server supportate da SAP.
* Le regole di confronto dell'istanza di SQL Server installata nelle VM offerte in Azure Marketplace non sono quelle che devono essere eseguite nell'istanza di SQL Server per SAP NetWeaver. È comunque possibile modificare le regole di confronto attenendosi alle istruzioni della sezione seguente.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Modifica delle regole di confronto SQL Server di una VM Microsoft Windows/SQL Server
Dato che le immagini di SQL Server in Azure Marketplace non sono configurate per l'uso delle regole di confronto richieste dalle applicazioni SAP NetWeaver, è necessario modificarle subito dopo la distribuzione. In SQL Server queste modifiche delle regole di confronto possono essere eseguite attenendosi alla procedura seguente non appena la VM è stata distribuita e un amministratore è in grado di accedervi:

* Aprire una finestra di comando di Windows come amministratore.
* Passare alla directory C:\Programmi\SQL Server\110\Setup Bootstrap\SQLServer2012.
* Eseguire il comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`&gt; è l'account definito come account amministratore quando si distribuisce la VM per la prima volta tramite la raccolta.

Il processo dovrebbe richiedere solo alcuni minuti. Per verificare la correttezza del risultato finale del passaggio, seguire questa procedura:

* Aprire SQL Server Management Studio.
* Aprire una finestra di query.
* Eseguire il comando sp_helpsort nel database master di SQL Server.

Il risultato dovrebbe essere simile a quello della figura seguente:

    Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se il risultato non corrisponde a quello nella figura, ARRESTARE la distribuzione di SAP e scoprire perché il comando di configurazione non ha funzionato nel modo previsto. La distribuzione di applicazioni SAP NetWeaver in un'istanza di SQL Server con tabelle codici di SQL Server diverse da quella indicata in precedenza **NON** è supportata.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>Disponibilità elevata di SQL Server per SAP in Azure
Se si usa SQL Server nelle distribuzioni IaaS di Azure per SAP, sono disponibili molte possibilità per distribuire il livello DBMS a disponibilità elevata. Come già accennato nell'articolo [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP), Azure offre vari contratti di licenza a scadenza per una singola macchina virtuale o una coppia di macchine virtuali distribuite in un set di disponibilità di Azure. Si supponga di preferire un contratto di licenza a scadenza per le proprie distribuzioni nella produzione che richieda la distribuzione in set di disponibilità di Azure. In tal caso, è necessario distribuire almeno due macchine virtuali nel set di disponibilità. Una macchina virtuale viene eseguita nell'istanza di SQL Server attiva. L'altra macchina virtuale viene eseguita nell'istanza passiva.

### <a name="sql-server-clustering-using-windows-scale-out-file-server"></a>Creazione di cluster SQL Server tramite il file server scale-out di Windows
Con Windows Server 2016, Microsoft ha introdotto la funzionalità [Spazi di archiviazione diretta](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview). Basato sulla distribuzione di Spazi di archiviazione diretta, è supportato il clustering di FCI SQL Server. I dettagli sono disponibili nell'articolo [Configurare l'istanza del cluster di failover di SQL Server nelle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster). La soluzione richiede l'uso di un servizio di bilanciamento del carico di Azure, nonché di gestire l'indirizzo IP virtuale delle risorse del cluster. I file di database di SQL Server vengono archiviati in spazi di archiviazione. È quindi sicuramente necessario creare gli spazi di archiviazione Windows in base ad Archiviazione Premium di Azure. Poiché questa soluzione è supportata da poco tempo, non si conoscono clienti SAP che usano questa soluzione in scenari di produzione SAP.  

### <a name="sql-server-log-shipping"></a>Log shipping SQL Server
Uno dei metodi di disponibilità elevata utilizzabili è il log shipping SQL Server. Se le VM incluse nella configurazione a disponibilità elevata hanno una risoluzione dei nomi funzionante, non sussistono problemi e la configurazione in Azure non sarà diversa da quelle eseguite in locale. Per quanto riguarda la configurazione del log shipping e i principi su cui si basa, sono disponibili informazioni dettagliate nell'articolo [Informazioni sul log shipping (SQL Server)](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server?view=sql-server-2017).

La funzionalità di log shipping di SQL Server viene usata raramente in Azure per ottenere la disponibilità elevata in una singola area di Azure. Negli scenari seguenti i clienti SAP usano il log shipping con successo insieme ad Azure:

- Scenari di ripristino di emergenza da un'area di Azure in un'altra area di Azure
- Configurazione di ripristino di emergenza da un'area locale in un'area di Azure
- Scenari di cutover da un'area locale ad Azure. In questi casi, il log shipping viene usato per sincronizzare la nuova distribuzione DBMS in Azure con l'ambiente di produzione in uso nel sistema locale. Al momento del cutover, la produzione viene arrestata e si controlla che gli ultimi backup del log delle transazioni e quelli più recenti siano stati trasferiti nella distribuzione DBMS di Azure. La distribuzione DBMS in Azure viene quindi aperta per la produzione.  



### <a name="database-mirroring"></a>Mirroring del database
Il mirroring del database supportato da SAP (vedere la nota SAP [965908]) si basa sulla definizione di un partner di failover nella stringa di connessione SAP. In caso di cross-premise, si presuppone che le due VM appartengano allo stesso dominio e che il contesto utente in cui vengono eseguite le due istanze di SQL Server sia un utente di dominio e abbia privilegi sufficienti nelle due istanze di SQL Server interessate. La configurazione del mirroring del database in Azure non è quindi diversa da una configurazione tipica locale.

A partire dalle distribuzioni solo cloud, il metodo più semplice prevede la configurazione di un altro dominio in Azure allo scopo di avere in un unico dominio le VM DBMS e idealmente le VM SAP dedicate.

Se non è possibile predisporre un unico dominio, è anche possibile usare i certificati per gli endpoint di mirroring del database, come descritto qui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Un'esercitazione per configurare il mirroring del database in Azure è disponibile qui: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server AlwaysOn
Poiché la funzionalità Always On supportata per SAP in locale (vedere la Nota SAP [1772688]), viene supportata anche per la combinazione di SAP in Azure. La distribuzione del listener del gruppo di disponibilità di SQL Server, da non confondere con il set di disponibilità di Azure, presenta alcune particolarità da considerare, poiché al momento Azure non consente di creare un oggetto AD/DNS, mentre questa operazione è consentita in locale. È quindi necessario eseguire alcuni passaggi di installazione diversi per ovviare al comportamento specifico di Azure.

Di seguito sono elencate alcune considerazioni relative all'uso di un listener del gruppo di disponibilità.

* L'uso di un listener del gruppo di disponibilità è possibile solo se il sistema operativo guest della VM è Windows Server 2012 o versioni successive. Per Windows Server 2012 è necessario assicurarsi che sia applicata la patch seguente: <https://support.microsoft.com/kb/2854082> 
* Per Windows Server 2008 R2, questa patch non esiste ed è necessario usare AlwaysOn così come il mirroring del database, specificando un partner di failover nella stringa di connessione tramite il parametro dbs/mss/server del file SAP default.pfl. Vedere la nota SAP [965908].
* Quando si usa un listener del gruppo di disponibilità, le VM di database devono essere connesse a un Load Balancer dedicato. Per evitare che Azure assegni nuovi indirizzi IP nei casi in cui entrambe le VM vengano arrestate accidentalmente, è consigliabile assegnare indirizzi IP statici alle interfacce di rete di tali VM nella configurazione Always On. Per informazioni sulla definizione di un indirizzo IP statico, vedere [questo][virtual-networks-reserved-private-ip] articolo.
* La procedura di creazione della configurazione cluster WSFC prevede speciali passaggi quando è necessario assegnare al cluster un indirizzo IP particolare, dal momento che Azure con la funzionalità corrente assegna al nome del cluster lo stesso indirizzo IP del nodo in cui viene creato il cluster. È quindi necessario eseguire un passaggio manuale per assegnare al cluster un indirizzo IP diverso.
* Il listener del gruppo di disponibilità verrà creato in Azure con gli endpoint TCP/IP assegnati alle VM che eseguono le repliche primaria e secondaria del gruppo di disponibilità.
* Potrebbe essere necessario proteggere questi endpoint con ACL.

Di seguito è elencata la documentazione dettagliata sulla distribuzione di Always On con SQL Server in macchine virtuali di Azure:

- [Panoramica sui gruppi di disponibilità Always On di SQL Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
- [Configurare un gruppo di disponibilità Always On in macchine virtuali di Azure in aree diverse](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr)
- [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità Always On in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener)

>[!NOTE]
> Se si intende configurare il servizio di bilanciamento del carico di Azure per l'indirizzo IP virtuale del listener del gruppo di disponibilità, assicurarsi che sia configurata l'opzione DirectServerReturn. La configurazione di questa opzione consente di ridurre la latenza di andata e ritorno della rete tra il livello dell'applicazione SAP e il livello DBMS. 

SQL Server AlwaysOn è la funzionalità di disponibilità elevata e ripristino di emergenza più comunemente usata in Azure per le distribuzioni del carico di lavoro SAP. La maggior parte dei clienti usa Always On per la disponibilità elevata all'interno di una singola area di Azure. Se la distribuzione è limitata a due soli nodi, sono disponibili due opzioni per la connettività:

- Utilizzo del listener del gruppo di disponibilità. Con il listener del gruppo di disponibilità, è necessario distribuire un servizio di bilanciamento del carico di Azure. Si tratta in genere della modalità di distribuzione predefinita. Le applicazioni SAP potrebbero essere configurate per la connessione rispetto ai listener del gruppo di disponibilità e non un singolo nodo
- Utilizzo dei parametri di connettività del mirroring del database SQL Server. In questo caso, è necessario configurare la connettività delle applicazioni SAP in modo che entrambi i nomi dei nodi siano nominati. I dettagli precisi di tale configurazione lato SAP sono illustrati nella nota SAP [#965908](https://launchpad.support.sap.com/#/notes/965908). Se si usa questa opzione, è necessario configurare un listener del gruppo di disponibilità. Con il listener non è necessario alcun servizio di bilanciamento del carico di Azure per la disponibilità elevata di SQL Server. La latenza della rete tra il livello dell'applicazione SAP e il livello DBMS risulta quindi inferiore poiché il traffico in entrata all'istanza del livello SQL Server non viene instradato attraverso il servizio di bilanciamento del carico di Azure. Si deve tuttavia tenere presente che questa opzione funziona solo se si limita il gruppo di disponibilità a due istanze. 

Un certo numero di clienti sfrutta la funzionalità SQL Server AlwaysOn per supportare la funzionalità di ripristino di emergenza tra aree di Azure. Molti clienti usano anche la possibilità di eseguire il backup da una replica secondaria. 

## <a name="sql-server-transparent-data-encryption"></a>Transparent Data Encryption di SQL Server
Un certo numero di clienti usa la funzionalità [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) di SQL Server per la distribuzione dei database SQL Server per SAP in Azure. La funzionalità TDE di SQL Server è completamente supportata da SAP (vedere la nota SAP [#1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Applicazione della funzionalità TDE di SQL Server
Nei casi in cui si esegue una migrazione eterogenea da un altro DBMS in esecuzione in locale verso Windows/SQL Server in esecuzione in Azure, è consigliabile creare in anticipo il database di destinazione vuoto in SQL Server. Il passaggio successivo prevede l'applicazione della funzionalità TDE di SQL Server. Nel frattempo si continua a eseguire il sistema di produzione locale. Il motivo per cui conviene attenersi a questa sequenza è che il processo di crittografia del database vuoto può richiedere molto tempo. I processi di importazione SAP importano quindi i dati nel database crittografato durante la fase di inattività. Nel complesso l'importazione in un database crittografato ha un impatto temporale inferiore rispetto alla crittografia del database dopo la fase di esportazione nella fase di inattività. Esperienze negative si sono verificate nel tentativo di applicare la funzionalità TDE con il carico di lavoro SAP in esecuzione sopra al database. È pertanto consigliabile gestire la distribuzione di TDE come un'attività da svolgere senza carichi di lavoro SAP su un database specifico.

Nei casi in cui si spostano i database di SQL Server per SAP dal livello locale in Azure, è consigliabile testare in quale infrastruttura è possibile applicare la crittografia più velocemente. Per questo motivo, tenere presenti queste considerazioni:

- Non è possibile definire il numero di thread che viene usato per applicare la crittografia dei dati al database. Il numero di thread dipende principalmente dal numero di volumi di disco in cui sono distribuiti i file di dati e di log SQL Server. Questo significa che maggiore è il numero di volumi distinti (lettere delle unità), maggiore sarà il numero di thread impegnati in parallelo per eseguire la crittografia. Tale configurazione è un po' in contrasto con il suggerimento precedente sulla configurazione del disco che prevede la creazione di un numero inferiore di spazi di archiviazione, o di uno solo, per i file di database di SQL Server in macchine virtuali di Azure. Una configurazione con un numero ridotto di volumi potrebbe far sì che la crittografia venga eseguita da un numero ridotto di thread. La crittografia a thread singolo legge gli extent di 64 kB, li crittografa e quindi scrivere un record nel file registro transazioni, indicando che l'extent è stato crittografato. Il carico sul log delle transazioni risulta così moderato.
- Nelle versioni precedenti di SQL Server la compressione dei backup non è più efficiente quando si crittografa il database SQL Server. Questo comportamento potrebbe trasformarsi in un problema quando si pianifica di crittografare il database SQL locale e quindi copiare un backup in Azure per ripristinare il database in Azure. La compressione dei backup di SQL Server raggiunge in genere un rapporto di compressione di fattore 4.
- Con SQL Server 2016, è stata introdotta una nuova funzionalità che consente anche la compressione efficiente dei database crittografati. Per i dettagli, vedere [questi blog](https://blogs.msdn.microsoft.com/sqlcat/2016/06/20/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases/).
 
Considerando l'applicazione della crittografia TDE solo con carico di lavoro SAP ridotto, è consigliabile testare in una configurazione specifica se è preferibile applicare la funzionalità Transparent Data Encryption al database SAP in locale o eseguire questa operazione in Azure. In Azure è senz'altro maggiore la flessibilità in termini di provisioning eccessivo dell'infrastruttura e di riduzione della stessa dopo l'applicazione di TDE.

### <a name="using-azure-key-vault"></a>Utilizzo di Azure Key Vault
Azure offre il servizio [Key Vault](https://azure.microsoft.com/services/key-vault/) per archiviare le chiavi di crittografia. SQL Server offre invece un connettore per sfruttare Azure Key Vault come archivio per i certificati TDE.

Altre informazioni sull'utilizzo di Azure Key Vault per TDE di SQL Server sono disponibili in:

- [Extensible Key Management tramite l'insieme di credenziali delle chiavi di Azure (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server?view=sql-server-2017).
- [Extensible Key Management TDE di SQL Server con Azure Key Vault - Passaggi di configurazione](https://docs.microsoft.com/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault?view=sql-server-2017).
- [Manutenzione e risoluzione dei problemi di Connettore SQL Server](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting?view=sql-server-2017).
- [More Questions From Customers About SQL Server Transparent Data Encryption – TDE + Azure Key Vault](https://blogs.msdn.microsoft.com/saponsqlserver/2017/04/04/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault/) (Altre domande dei clienti sulla funzionalità Transparent Data Encryption di SQL Server - TDE + Azure Key Vault).


>[!IMPORTANT]
>Quando si usa la funzionalità TDE di SQL Server, in particolare con Azure Key Vault, è consigliabile usare le patch più recenti di SQL Server 2014, SQL Server 2016 e SQL Server 2017. Il motivo è che in base ai commenti e suggerimenti ricevuti dai clienti, al codice sono state applicate ottimizzazioni e correzioni. Controllare ad esempio [KBA #4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Riepilogo generale su SQL Server per SAP in Azure
Questa guida contiene numerosi consigli che è preferibile leggere più volte prima di pianificare la distribuzione di Azure. In linea generale, assicurarsi di seguire i principali consigli generali, specifici per la distribuzione di DBMS in Azure:

1. Usare la versione più recente di DBMS, come SQL Server 2017, che offre maggiori vantaggi in Azure. 
2. Pianificare attentamente l'infrastruttura di sistema SAP in Azure per bilanciare il layout dei file di dati e le restrizioni di Azure:
   * Non usare troppi dischi, ma un numero sufficiente a garantire il numero necessario di operazioni di I/O al secondo.
   * Se non si usa Managed Disks, tenere presente che il numero di operazioni di I/O al secondo consentito varia a seconda dell'account di archiviazione di Azure e che gli account di archiviazione sono limitati nelle singole sottoscrizioni di Azure ([altri dettagli][azure-subscription-service-limits]). 
   * Se è necessaria una velocità effettiva maggiore, eseguire lo striping solo tra dischi.
3. Non installare mai software né inserire file che richiedono la persistenza nell'unità D:\ perché non è permanente e qualsiasi elemento in tale unità andrà perso al riavvio di Windows.
4. Non usare il caching dei dischi per Archiviazione Standard di Azure.
5. Non usare account di archiviazione Standard con replica geografica di Azure.  Usare l'opzione Con ridondanza locale per i carichi di lavoro DBMS.
6. Usare la soluzione di disponibilità elevata/ripristino di emergenza del fornitore del sistema DBMS per replicare i dati del database.
7. Usare sempre la risoluzione dei nomi e non fare affidamento sugli indirizzi IP.
8. Quando si usa la funzionalità TDE di SQL Server, applicare le patch più recenti di SQL Server.
9. Usare la massima compressione possibile del database. Si tratta della compressione di pagina per SQL Server.
10. Prestare attenzione quando si usano immagini di SQL da Azure Marketplace. Se si usa quella di SQL Server, è necessario modificare le regole di confronto dell'istanza prima di installarvi un qualsiasi sistema SAP NetWeaver.
11. Installare e configurare la funzionalità di monitoraggio dell'host SAP per Azure come illustrato nella [Deployment Guide][deployment-guide] (Guida alla distribuzione).
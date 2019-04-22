---
title: Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP | Microsoft Docs
description: Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP
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
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f50f013020c704ddc294a59f8c6c5dac24bbd5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58850962"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Distribuzione DBMS per SAP ASE di macchine virtuali di Azure per un carico di lavoro SAP

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

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
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

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

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

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

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
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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



Questo documento illustra le numerose aree da valutare quando si distribuisce SAP ASE in IaaS di Azure. Prima di questo documento è consigliabile avere letto il documento [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP) e le altre guide disponibili nella [documentazione relativa a un carico di lavoro SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). 

## <a name="specifics-to-sap-ase-on-windows"></a>Specifiche per SAP ASE in Windows
Grazie a Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in macchine virtuali di Azure. L'uso di SAP ASE in una macchina virtuale di Azure consente di ridurre il costo totale di proprietà associato a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni a Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

I contratti di servizio per Macchine virtuali di Azure, sono disponibili qui: <https://azure.microsoft.com/support/legal/sla/virtual-machines>

Microsoft Azure offre vari tipi di macchina virtuale che consentono di eseguire dai più piccoli panorami applicativi e sistemi SAP ai panorami applicativi e sistemi SAP più grandi che comprendono migliaia di utenti. I numeri SAPS relativi al ridimensionamento SAP dei diversi SKU di VM con certificazione SAP sono riportati nella nota SAP [1928533].

Le istruzioni e i suggerimenti relativi all'utilizzo di Archiviazione di Azure, alla distribuzione di VM SAP o al monitoraggio di SAP presenti in [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP) si applicano anche alle distribuzioni di SAP ASE.

### <a name="sap-ase-version-support"></a>Supporto della versione di SAP ASE
SAP supporta attualmente SAP ASE versione 16.0 per l'uso con i prodotti SAP Business Suite. Tutti gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC da usare con i prodotti SAP Business Suite vengono forniti esclusivamente in SAP Service Marketplace su: <https://support.sap.com/swdc>.

Non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente dai siti Web Sybase. Per informazioni dettagliate sulle patch supportate per l'uso con i prodotti SAP in locale e nelle macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]

Le informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE sono disponibili in [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Linee guida per la configurazione di SAP ASE per le installazioni di SAP ASE correlate a SAP nelle VM di Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struttura della distribuzione SAP ASE
Gli eseguibili di SAP ASE devono essere salvati o installati nell'unità di sistema del disco del sistema operativo della VM (unità C:\)). La maggior parte dei database di sistema e di strumenti di SAP ASE generalmente non è soggetta a carichi di lavoro elevati. È quindi possibile lasciare i database di sistema e di strumenti (master, model, saptools, sybmgmtdb, sybsystemdb) nell'unità C:\. 

Può fare eccezione il database temporaneo, che, nel caso di alcuni carichi di lavoro SAP ERP e di tutti i carichi di lavoro BW, può richiedere un volume di dati o un volume di operazioni di I/O superiore. Volumi od operazioni I/O al secondo che non possono essere fornite dal disco del sistema operativo della VM (unità C:\)).

A seconda della versione di SAPInst/SWPM usata per l'installazione, la configurazione dell'istanza SAP ASE è simile alla seguente:

* Un solo tempdb SAP ASE creato durante l'installazione di SAP ASE
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un saptempdb aggiuntivo creato dalla routine di installazione SAP
* Un tempdb SAP ASE creato con l'installazione di SAP ASE e un tempdb aggiuntivo creato manualmente (ad esempio, seguendo la nota SAP [1752266]) per soddisfare i requisiti specifici di ERP/BW per tempdb

A prescindere dai motivi di prestazioni per carichi di lavoro ERP specifici o tutti i carichi di lavoro BW, può essere utile archiviare i dispositivi tempdb del tempdb aggiuntivo in un'unità diversa da C:\. Se non esistono tempdb aggiuntivi, è consigliabile crearne uno (nota SAP [1752266]).

Per tali sistemi, è consigliabile eseguire questa procedura per il tempdb aggiuntivo creato:

* Spostare il primo dispositivo tempdb nel primo dispositivo del database SAP
* Aggiungere dispositivi tempdb a ogni disco rigido virtuale contenente un dispositivo del database SAP

Questa configurazione consente al tempdb di usare più spazio di quello offerto dall'unità di sistema. Come riferimento è possibile controllare le dimensioni del dispositivo tempdb nei sistemi esistenti eseguiti in locale. In alternativa, con una tale configurazione potrà essere eseguito su tempdb un numero di operazioni di I/O al secondo che l'unità di sistema non può garantire. I sistemi in esecuzione in locale possono essere usati per monitorare il carico di lavoro di operazioni di I/O rispetto al tempdb.

Non spostare mai dispositivi SAP ASE nell'unità D:\ della VM. Per SAP ASE, questo consiglio vale anche per il tempdb, anche se gli oggetti in esso presenti sono solo temporanei.

Per le distribuzioni dei file di dati e dei file registro transazioni, le istruzioni e i suggerimenti sono disponibili in [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP). Nel caso di distribuzioni basate su Windows, è consigliabile usare gli spazi di archiviazione Windows per compilare i set con striping con operazioni di I/O al secondo, velocità effettiva e volume sufficienti.  

#### <a name="impact-of-database-compression"></a>Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS come Azure. È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

I motivi per cui è consigliabile comprimere il database prima di caricarlo in Azure sono molti:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per altri dettagli su come controllare se la compressione è già in uso in un database SAP ASE esistente, vedere la nota SAP [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Uso di DBACockpit per monitorare le istanze di database
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. La funzionalità completa per monitorare e gestire il database è tuttavia disponibile solo nell'implementazione Webdynpro di DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Per abilitare l'utilizzo di Webdynpro e generare le istanze necessarie, vedere la nota SAP [1245200]. Quando si seguono le istruzioni delle note precedenti, si configura anche Internet Communication Manager (ICM) insieme alle porte da usare per le connessioni HTTP e HTTPS. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

I collegamenti generati nella transazione DBACockpit sono simili ai seguenti:

> https:\//\<fullyqualifiedhostname >: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname >: 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

A seconda del modo in cui la macchina virtuale di Azure ospitante il sistema SAP è connessa ad Active Directory e DNS, è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta aprendo DBACockpit. Per informazioni su come ICM determina il nome host completo in base ai parametri del profilo e su come impostare il parametro icm/host_name_full in modo esplicito, se necessario, vedere la nota SAP [773830].

Se la VM è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra l'ambiente locale e Azure, è necessario definire un indirizzo IP pubblico e un'etichetta di dominio. Il formato del nome DNS pubblico della VM si presenta come segue:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Altri dettagli relativi al nome DNS sono disponibili [qui][virtual-machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host_name_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere regole in ingresso al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP usate per comunicare con ICM
* Aggiungere regole in ingresso alla configurazione di Windows Firewall per le porte TCP/IP usate per comunicare con ICM

Per importare automaticamente tutte le correzioni disponibili, è consigliabile applicare periodicamente la nota SAP della raccolta di correzioni applicabile alla propria versione di SAP:

* [1558958]
* [1619967]
* [1882376]

Per altre informazioni su DBA Cockpit per SAP ASE, vedere le note SAP seguenti:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerazioni sul backup/ripristino per SAP ASE
Quando si distribuisce SAP ASE in Azure, è necessario rivedere la metodologia di backup. Anche per i sistemi non di produzione, è necessario eseguire backup periodici dei database SAP. Poiché in Archiviazione di Azure vengono conservate tre immagini, un backup potrebbe essere meno importante rispetto alla compensazione di un arresto anomalo. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. 

Il backup e il ripristino di un database in Azure funzionano esattamente come in locale. Vedere le note SAP seguenti:

* [1588316]
* [1585981]

per i dettagli sulla creazione di configurazioni di dump e sulla pianificazione dei backup. A seconda della strategia e delle esigenze, è possibile configurare i dump di database e di log su disco in uno dei dischi esistenti oppure aggiungere un altro disco per il backup. Per limitare il rischio di perdita dei dati in caso di errore, è consigliabile usare un disco in cui non sono presenti file del database.

Oltre alla compressione dati e LOB, SAP ASE offre anche la compressione del backup. Per occupare meno spazio con i dump di database e di log, si può usare la compressione del backup. Per altre informazioni, vedere la nota SAP [1588316]. La compressione del backup è fondamentale anche per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenenti dump di backup dalla macchina virtuale di Azure in locale.

Non usare l'unità D:\ come destinazione dei dump di database o di log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerazioni sulle prestazioni per backup/ripristini
Come nelle distribuzioni bare metal, le prestazioni dei backup/ripristini dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. Tenere presente che la compressione dei backup consuma risorse della CPU. Il consumo della CPU richiesto per la compressione del backup può avere un ruolo significativo nelle VM con un numero di thread della CPU ridotto. Si può quindi presumere quanto segue:

* Minore è il numero dei dischi usati per l'archiviazione dei dispositivi del database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Minore è il numero di destinazioni (directory di striping o dischi) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, sono disponibili due opzioni che possono essere usate/combinate a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Creazione di una configurazione dump a livello di SAP ASE che usa più directory di destinazione per la scrittura del dump

Lo striping di un volume del disco su più dischi montati è stato illustrato in [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP). Per altre informazioni sull'uso di più directory nella configurazione dump SAP ASE, vedere la documentazione sulla stored procedure sp_config_dump che viene usata per creare la configurazione dump in [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Ripristino di emergenza con VM di Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replica dei dati con SAP Sybase Replication Server
Con SAP Sybase Replication Server (SRS), SAP ASE offre una nuova soluzione warm standby per trasferire in modo asincrono le transazioni di database in una posizione distante. 

L'installazione e il funzionamento di SRS sono altrettanto efficaci sia in una VM ospitata nei servizi Macchine virtuali di Azure che in locale.

SAP ASE HADR non richiede un servizio di bilanciamento del carico interno di Azure e non dispone di dipendenze dal clustering a livello di sistema operativo e funziona su macchine virtuali Windows e Linux di Azure. Per informazioni dettagliate su SAP ASE HADR, leggere il [manuale dell'utente di SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Specifiche per SAP ASE in Linux
A partire da Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in macchine virtuali di Azure. SAP ASE in una macchina virtuale consente di ridurre il costo totale di proprietà associato a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni a Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

Per distribuire le VM di Azure, è importante conoscere i contratti di servizio ufficiali disponibili all'indirizzo <https://azure.microsoft.com/support/legal/sla>

Le informazioni sul ridimensionamento di SAP e un elenco degli SKU di VM con certificazione SAP sono disponibili nella nota SAP [1928533]. Altri documenti sul ridimensionamento SAP per le macchine virtuali di Azure sono disponibili agli indirizzi <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Le istruzioni e i suggerimenti relativi all'utilizzo di Archiviazione di Azure, alla distribuzione di VM SAP o al monitoraggio di SAP si applicano alle distribuzioni di SAP ASE insieme alle applicazioni SAP, come indicato nei primi quattro capitoli di questo documento.

Le due note SAP seguenti includono informazioni generali su ASE in Linux e su ASE nel cloud:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Supporto della versione di SAP ASE
SAP supporta attualmente SAP ASE versione 16.0 per l'uso con i prodotti SAP Business Suite. Tutti gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC da usare con i prodotti SAP Business Suite vengono forniti esclusivamente in SAP Service Marketplace su: <https://support.sap.com/swdc>.

Come per le installazioni locali, non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente dai siti Web Sybase. Per informazioni dettagliate sulle patch supportate per l'uso con i prodotti SAP Business Suite in locale e nelle macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]

Le informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE sono disponibili in [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>Linee guida per la configurazione di SAP ASE per le installazioni di SAP ASE correlate a SAP nelle VM di Azure
#### <a name="structure-of-the-sap-ase-deployment"></a>Struttura della distribuzione SAP ASE
Gli eseguibili di SAP ASE devono essere salvati o installati nel file system radice della macchina virtuale (/sybase). La maggior parte dei database di sistema e di strumenti di SAP ASE generalmente non è soggetta a carichi di lavoro elevati. I database di sistema e di strumenti (master, model, saptools, sybmgmtdb, sybsystemdb) possono quindi essere memorizzati nel file system radice. 

Può fare eccezione il database temporaneo, che, nel caso di alcuni carichi di lavoro SAP ERP e di tutti i carichi di lavoro BW, può richiedere un volume di dati o un volume di operazioni di I/O superiore. Volumi od operazioni I/O al secondo che non possono essere fornite dal disco del sistema operativo della VM 

A seconda della versione di SAPInst/SWPM usata per installare il sistema, il database può contenere:

* Un solo tempdb SAP ASE creato durante l'installazione di SAP ASE
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un saptempdb aggiuntivo creato dalla routine di installazione SAP
* Un tempdb SAP ASE creato con l'installazione di SAP ASE e un tempdb aggiuntivo creato manualmente (ad esempio, seguendo la nota SAP [1752266]) per soddisfare i requisiti specifici di ERP/BW per tempdb

A prescindere dai motivi di prestazioni per carichi di lavoro ERP specifici o tutti i carichi di lavoro BW, può essere utile archiviare i dispositivi tempdb del tempdb aggiuntivo (da SWPM o manualmente) in un file system separato, che può essere rappresentato da un singolo disco dati di Azure o un disco RAID Linux che si estende in più dischi dati di Azure. Se non esistono tempdb aggiuntivi, è consigliabile crearne uno (nota SAP [1752266]).

Per tali sistemi, eseguire questa procedura per il tempdb aggiuntivo creato:

* Spostare la prima directory tempdb nel primo file system del database SAP
* Aggiungere le directory tempdb a ogni disco contenente un file system del database SAP

Questa configurazione consente al tempdb di usare più spazio di quello offerto dall'unità di sistema. Come riferimento è possibile controllare le dimensioni del dispositivo tempdb nei sistemi esistenti eseguiti in locale. In alternativa, con una tale configurazione potrà essere eseguito su tempdb un numero di operazioni di I/O al secondo che l'unità di sistema non può garantire. I sistemi in esecuzione in locale possono essere usati per monitorare il carico di lavoro di operazioni di I/O rispetto al tempdb.

Non inserire mai le directory SAP ASE in /mnt o /mnt/resource nella VM. Per SAP ASE, questo consiglio vale anche per il tempdb, anche se gli oggetti in esso presenti sono solo temporanei. Questo perché /mnt o /mnt/resource è uno spazio temporaneo predefinito della VM di Azure, che non è persistente. Per altri dettagli sullo spazio temporaneo della macchina virtuale di Azure, vedere [questo articolo][virtual-machines-linux-how-to-attach-disk]

Per le distribuzioni dei file di dati e dei file registro transazioni, le istruzioni e i suggerimenti sono disponibili in [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP). In caso di distribuzioni basate su Linux, è consigliabile usare LVM o MDADM per creare set con striping con operazioni di I/O al secondo, velocità effettiva e volume sufficienti. 

#### <a name="impact-of-database-compression"></a>Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS come Azure. È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

I motivi per cui è consigliabile comprimere il database prima di caricarlo in Azure sono molti:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per altri dettagli su come controllare se la compressione è già in uso in un database SAP ASE esistente, vedere la nota SAP [1750510]. Per altre informazioni sulla compressione del database, vedere la nota SAP [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Uso di DBACockpit per monitorare le istanze di database
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. La funzionalità completa per monitorare e gestire il database è tuttavia disponibile solo nell'implementazione Webdynpro di DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Per abilitare l'utilizzo di Webdynpro e generare le istanze necessarie, vedere la nota SAP [1245200]. Quando si seguono le istruzioni delle note precedenti, si configura anche Internet Communication Manager (ICM) insieme alle porte da usare per le connessioni HTTP e HTTPS. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

I collegamenti generati nella transazione DBACockpit saranno simili ai seguenti:

> https:\//\<fullyqualifiedhostname >: 44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<fullyqualifiedhostname >: 8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

A seconda del modo in cui la macchina virtuale di Azure ospitante il sistema SAP è connessa ad Active Directory e DNS, è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta aprendo DBACockpit. Per informazioni su come ICM determina il nome host completo in base ai parametri del profilo e su come impostare il parametro icm/host_name_full in modo esplicito, se necessario, vedere la nota SAP [773830].

Se la VM è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra l'ambiente locale e Azure, è necessario definire un indirizzo IP pubblico e un'etichetta di dominio. Il formato del nome DNS pubblico della VM si presenta come segue:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Altri dettagli relativi al nome DNS sono disponibili [qui][virtual-machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host_name_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

In questo caso è necessario assicurarsi di:

* Aggiungere regole in ingresso al gruppo di sicurezza di rete nel portale di Azure per le porte TCP/IP usate per comunicare con ICM
* Aggiungere regole in ingresso alla configurazione di Windows Firewall per le porte TCP/IP usate per comunicare con ICM

Per importare automaticamente tutte le correzioni disponibili, è consigliabile applicare periodicamente la nota SAP della raccolta di correzioni applicabile alla propria versione di SAP:

* [1558958]
* [1619967]
* [1882376]

Per altre informazioni su DBA Cockpit per SAP ASE, vedere le note SAP seguenti:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Considerazioni sul backup/ripristino per SAP ASE
Quando si distribuisce SAP ASE in Azure, è necessario rivedere la metodologia di backup. Anche per i sistemi non di produzione, è necessario eseguire backup periodici dei database SAP. Poiché in Archiviazione di Azure vengono conservate tre immagini, un backup potrebbe essere meno importante rispetto alla compensazione di un arresto anomalo. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. 

Il backup e il ripristino di un database in Azure funzionano esattamente come in locale. Vedere le note SAP seguenti:

* [1588316]
* [1585981]

per i dettagli sulla creazione di configurazioni di dump e sulla pianificazione dei backup. A seconda della strategia e delle esigenze, è possibile configurare i dump di database e di log su disco in uno dei dischi esistenti oppure aggiungere un altro disco per il backup. Per limitare il rischio di perdita dei dati in caso di errore, è consigliabile usare un disco senza directory/file per il database.

Oltre alla compressione dati e LOB, SAP ASE offre anche la compressione del backup. Per occupare meno spazio con i dump di database e di log, si può usare la compressione del backup. Per altre informazioni, vedere la nota SAP [1588316]. La compressione del backup è fondamentale anche per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenenti dump di backup dalla macchina virtuale di Azure in locale.

Non usare lo spazio temporaneo della VM di Azure /mnt o /mnt/resource come destinazione dei dump di database o di log.

#### <a name="performance-considerations-for-backupsrestores"></a>Considerazioni sulle prestazioni per backup/ripristini
Come nelle distribuzioni bare metal, le prestazioni dei backup/ripristini dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. Tenere presente che la compressione dei backup consuma risorse della CPU. Il consumo della CPU richiesto per la compressione del backup può avere un ruolo significativo nelle VM con un numero di thread della CPU ridotto.  Si può quindi presumere quanto segue:

* Minore è il numero dei dischi usati per l'archiviazione dei dispositivi del database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Minore è il numero di destinazioni (RAID software Linux o dischi) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, sono disponibili due opzioni che possono essere usate/combinate a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Creazione di una configurazione dump a livello di SAP ASE che usa più directory di destinazione per la scrittura del dump

Lo striping di un volume del disco su più dischi montati è stato illustrato in [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP). Per altre informazioni sull'uso di più directory nella configurazione dump SAP ASE, vedere la documentazione sulla stored procedure sp_config_dump che viene usata per creare la configurazione dump in [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### <a name="disaster-recovery-with-azure-vms"></a>Ripristino di emergenza con VM di Azure
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Replica dei dati con SAP Sybase Replication Server
Con SAP Sybase Replication Server (SRS), SAP ASE offre una nuova soluzione warm standby per trasferire in modo asincrono le transazioni di database in una posizione distante. 

L'installazione e il funzionamento di SRS sono altrettanto efficaci sia in una VM ospitata nei servizi Macchine virtuali di Azure che in locale.

È supportato ASE HADR tramite SAP Replication Server. È altamente consigliabile usare SAP ASE 16.03 per provare questo tipo di configurazione. Istruzioni più dettagliate sull'installazione di tali configurazioni sono reperibili in questo [blog](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).

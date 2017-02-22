---
title: SAP NetWeaver in macchine virtuali (VM) di Azure - Guida alla distribuzione | Microsoft Docs
description: SAP NetWeaver in macchine virtuali (VM) di Azure - Guida alla distribuzione
services: virtual-machines-windows
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 22aaa98c-bb9f-472f-b546-0b294e033cda
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 3b3250c17f0086ee0fd25b5c7fb8822eb52a26f8
ms.openlocfilehash: 25865776748ae0605310eb86aefca2afebac2799


---
# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>SAP NetWeaver in macchine virtuali (VM) di Azure - Guida alla distribuzione
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver in macchine virtuali Azure: guida alla distribuzione DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching per VM e dischi rigidi virtuali) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID software) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Archiviazione di Microsoft Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struttura di una distribuzione RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Disponibilità elevata e ripristino di emergenza con VM di Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e versioni successive) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versioni precedenti) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Riepilogo generale su SQL Server per SAP in Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifiche per RDBMS SQL Server) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configurazione dell'archiviazione) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e ripristino) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerazioni sulle prestazioni per il backup e il ripristino) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Altro) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver in macchine virtuali Azure: guida alla distribuzione) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Risorse SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuzione di una VM con un'immagine personalizzata) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenari di distribuzione di VM per SAP in Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuzione dei cmdlet di Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Scaricare e importare i cmdlet di PowerShell pertinenti per SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Aggiungere la VM a un dominio locale (solo per Windows)) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Scaricare, installare e abilitare l'agente di VM di Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfaccia della riga di comando di Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurare l'estensione di monitoraggio avanzato di Azure per SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver in macchine virtuali Azure: guida alla pianificazione e all'implementazione) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Risorse) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Disponibilità elevata per i server applicazioni SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso dell'avvio automatico per le istanze di SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Aree di Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domini di errore) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domini di aggiornamento) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Set di disponibilità di Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concetto di macchina virtuale di Microsoft Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Spostamento di una VM da locale ad Azure con un disco non generalizzato) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuzione di una VM con un'immagine specifica del cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparazione di VM con SAP per Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Differenza tra un disco di Azure e un'immagine di Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Caricamento di un disco rigido virtuale da locale ad Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia di dischi tra account di archiviazione di Azure) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struttura di VM/dischi rigidi virtuali per le distribuzioni SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Impostazione del montaggio automatico per dischi collegati) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concetti della distribuzione solo cloud di istanze di SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Soluzione di monitoraggio di Azure per SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-windows-create-vm-specialized]:virtual-machines-windows-create-vm-specialized.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../azure-resource-manager/xplat-cli-azure-resource-manager.md

Microsoft Azure consente alle aziende di acquisire risorse di calcolo e di archiviazione in poco tempo, senza lunghi cicli di approvvigionamento. Macchine virtuali di Azure consente alle aziende di distribuire in Azure applicazioni classiche, ad esempio applicazioni basate su SAP NetWeaver, e di estenderne l'affidabilità e la disponibilità senza risorse aggiuntive in locale. Microsoft Azure supporta anche la connettività cross-premise, che consente alle aziende di integrare attivamente macchine virtuali di Azure nei domini locali, in cloud privati e nel landscape del sistema SAP.

Questo white paper descrive in modo dettagliato la preparazione di una macchina virtuale di Azure per la distribuzione di applicazioni basate su SAP NetWeaver. Presuppone che si conoscano le informazioni contenute nella [Guida alla pianificazione e all'implementazione][planning-guide]. In caso contrario, è consigliabile leggere prima il documento corrispondente.

Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

## <a name="introduction"></a>Introduzione
Numerose aziende in tutto il mondo usano applicazioni basate su SAP NetWeaver, prevalentemente SAP Business Suite, per eseguire processi aziendali cruciali. Di conseguenza, l'integrità del sistema è un asset fondamentale e la capacità di assicurare supporto aziendale in caso di malfunzionamento, ad esempio eventi imprevisti riguardanti le prestazioni, diventa un requisito essenziale.
Microsoft Azure offre una strumentazione di piattaforma superiore per soddisfare i requisiti in termini di supporto di tutte le applicazioni aziendali critiche. Questa guida garantisce che una macchina virtuale di Microsoft Azure di destinazione di una distribuzione di software SAP sia configurata in modo da consentire il supporto aziendale, indipendentemente dal fatto che la macchina virtuale venga creata a partire da Azure Marketplace o da un'immagine specifica del cliente.
Di seguito sono descritti in dettaglio tutti i passaggi di configurazione necessari.

## <a name="prerequisites-and-resources"></a>Prerequisiti e risorse
### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare che siano soddisfatti i prerequisiti descritti nei capitoli seguenti.

#### <a name="local-personal-computer"></a>Personal computer locale
La configurazione di una macchina virtuale di Azure per la distribuzione di software SAP include diversi passaggi. Per gestire VM Windows o Linux, è necessario usare uno script PowerShell e il portale di Microsoft Azure. A tale scopo, è necessario un personal computer che esegue Windows 7 o versioni successive. Se si vogliono gestire solo VM Linux usando per questa attività un computer Linux, è anche possibile usare l'interfaccia della riga di comando di Azure.

#### <a name="internet-connection"></a>Connessione Internet
Per scaricare ed eseguire gli strumenti e gli script obbligatori, è necessaria una connessione Internet. La macchina virtuale di Microsoft Azure che esegue l'estensione di monitoraggio avanzato di Azure per SAP, inoltre, deve avere accesso a Internet. Se questa VM di Azure fa parte di una rete virtuale di Azure o di un dominio locale, verificare che le impostazioni proxy pertinenti siano configurate come descritto nel capitolo [Configurare il proxy][deployment-guide-configure-proxy] in questo documento.

#### <a name="microsoft-azure-subscription"></a>Sottoscrizione di Microsoft Azure
Esiste già un account Azure e si conoscono le credenziali di accesso corrispondenti.

#### <a name="topology-consideration-and-networking"></a>Considerazione sulla topologia e rete
La topologia e l'architettura della distribuzione SAP in Azure devono essere definite. In relazione all'architettura:

* Account di archiviazione di Microsoft Azure da usare
* Rete virtuale in cui distribuire il sistema SAP
* Gruppo di risorse in cui distribuire il sistema SAP
* Area di Azure per la distribuzione del sistema SAP
* Configurazione SAP (a 2 o 3 livelli)
* Dimensioni delle VM e numero di dischi rigidi virtuali aggiuntivi da montare nelle VM
* Configurazione del sistema di correzione e trasporto SAP

Le reti virtuali di Azure o gli account di archiviazione di Azure dovrebbero essere già stati creati e configurati. Le modalità di creazione e configurazione sono illustrate nella [Guida alla pianificazione e all'implementazione][planning-guide].

#### <a name="sap-sizing"></a>Dimensionamento di SAP
* Il carico di lavoro SAP previsto è stato determinato, ad esempio con SAP Quick Sizer, e il numero di SAPS corrispondente è noto 
* L'uso di memoria e risorse della CPU necessario per il sistema SAP dovrebbe essere noto
* Le operazioni di I/O al secondo necessarie dovrebbero essere note
* La larghezza di banda di rete necessaria per l'eventuale comunicazione tra diverse VM in Azure è nota
* La larghezza di banda di rete necessaria tra gli asset locali e i sistemi SAP distribuiti in Azure è nota

#### <a name="resource-groups"></a>Gruppi di risorse
I gruppi di risorse fanno parte di un nuovo concetto di distribuzione. Tutte le risorse di un gruppo di risorse hanno lo stesso ciclo di vita, ad esempio vengono create ed eliminate contemporaneamente. Per altre informazioni sui gruppi di risorse, leggere [questo articolo][resource-group-overview]. 

### <a name="a-name42ee2bdb-1efc-4ec7-ab31-fe4c22769b94asap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Risorse SAP
Durante le operazioni di configurazione, sono necessarie le risorse seguenti:

* Nota SAP [1928533] contenente:
  * Elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione di software SAP 
  * Informazioni importanti sulla capacità in base alle dimensioni delle macchine virtuali di Azure
  * Software SAP e combinazioni di sistema operativo e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure

* Nota SAP [2015553] contenente l'elenco dei prerequisiti che devono essere supportati da SAP quando si distribuisce software SAP in Microsoft Azure.
* Nota SAP [2178632] contenente informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Microsoft Azure. 
* Nota SAP [1409604] contenente la versione dell'agente host SAP per Windows necessaria in Microsoft Azure.
* Nota SAP [2191498] contenente la versione dell'agente host SAP per Linux necessaria in Microsoft Azure.
* Nota SAP [2243692] contenente informazioni sulle licenze per SAP in Linux in Azure.
* Nota SAP [1984787] contenente informazioni generali su SUSE LINUX Enterprise Server 12.
* Nota SAP [2002167] contenente informazioni generali su Red Hat Enterprise Linux 7.x.
* Nota SAP [1999351] contenente informazioni aggiuntive sulla risoluzione dei problemi per il monitoraggio avanzato di Azure per SAP.
* [WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contenente tutte le note su SAP necessarie per Linux.
* Cmdlet di PowerShell specifici di SAP inclusi in [Azure PowerShell][azure-ps].
* Interfaccia della riga di comando di Azure specifica di SAP inclusa nell'[interfaccia della riga di comando di Azure][azure-cli].
* [Portale di Microsoft Azure][azure-portal]

[comment]: <> (MSSedusch TODO Add ARM patch level for SAP Host Agent in SAP Note 1409604)

L'argomento di SAP in Microsoft Azure viene trattato anche nelle guide seguenti:

* [SAP NetWeaver in macchine virtuali di Azure - Guida alla pianificazione e all'implementazione][planning-guide]
* [SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione (questo documento)][deployment-guide]
* [SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione DBMS][dbms-guide]
* [SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione di funzionalità di disponibilità elevata][ha-guide]

## <a name="a-nameb3253ee3-d63b-4d74-a49b-185e76c4088eadeployment-scenarios-of-vms-for-sap-on-microsoft-azure"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Scenari di distribuzione di VM per SAP in Microsoft Azure
Questo capitolo illustra le diverse modalità di distribuzione e i singoli passaggi per ogni tipo di distribuzione.

### <a name="deployment-of-vms-for-sap"></a>Distribuzione di VM per SAP
Microsoft Azure offre diversi modi per distribuire le VM e i dischi associati. È quindi molto importante comprendere le differenze perché le operazioni di preparazione delle VM possono variare in base alla modalità di distribuzione. In generale, vengono esaminati gli scenari seguenti:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuzione di una VM da Azure Marketplace
Per distribuire la VM si sceglie di usare un'immagine offerta da Microsoft o terze parti in Azure Marketplace. Dopo aver distribuito la VM in Microsoft Azure, si installa il software SAP all'interno della VM usando le stesse linee guida e gli stessi strumenti che verrebbero impiegati in un ambiente locale. Per l'installazione del software SAP all'interno della macchina virtuale di Azure, SAP e Microsoft consigliano di caricare e archiviare il supporto di installazione SAP in dischi rigidi virtuali di Azure o di creare una VM di Azure che funge da "file server" contenente tutti i supporti di installazione SAP necessari.

[comment]: <> (MSSedusch TODO why do we need to recommend a file management e.g. File Server or VHD? Is that so different from on-premises?)

Per altri dettagli, vedere il capitolo [Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP][deployment-guide-3.2].

#### <a name="a-name3688666f-281f-425b-a312-a77e7db2dfabadeploying-a-vm-with-a-custom-image"></a><a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Distribuzione di una VM con un'immagine personalizzata
Le immagini disponibili in Azure Marketplace potrebbero non soddisfare le esigenze a causa di specifici requisiti di patch riguardanti la versione del sistema operativo o DBMS in uso. Per questo motivo, potrebbe essere necessario creare una VM usando un'immagine di sistema operativo/database "privata" per la VM che potrà essere in seguito distribuita diverse volte.
La procedura per creare un'immagine privata presenta differenze tra Windows e Linux.

- - -
> ![Windows][Logo_Windows] Windows
> 
> Per preparare un'immagine Windows che possa essere usata per distribuire più macchine virtuali, è necessario eseguire l'astrazione/generalizzazione delle impostazioni di Windows (come il nome host e il SID di Windows) nella VM locale. A tale scopo, è possibile usare Sysprep come descritto all'indirizzo <https://msdn.microsoft.com/library/hh825084.aspx>.
> 
> ![Linux][Logo_Linux] Linux
> 
> Per preparare un'immagine Linux che possa essere usata per distribuire più macchine virtuali, è necessario eseguire l'astrazione/generalizzazione di alcune impostazioni di Linux nella VM locale. A tale scopo, è possibile usare waagent -deprovision come descritto in [questo articolo][virtual-machines-linux-capture-image] oppure in [questo][virtual-machines-linux-agent-user-guide-command-line-options].
> 
> 

- - -
È possibile configurare il contenuto del database usando SAP Software Provision Manager per installare un nuovo sistema SAP, ripristinare un backup del database da un disco rigido virtuale collegato alla macchina virtuale oppure ripristinare un backup del database direttamente da Archiviazione di Azure, se l'operazione è supportata dal sistema DBMS. Vedere la [guida alla distribuzione DBMS][dbms-guide]. Se è già stato installato un sistema SAP nella macchina virtuale locale (soprattutto per sistemi a 2 livelli), è possibile adattare le impostazioni del sistema SAP dopo la distribuzione della macchina virtuale di Azure con la procedura di ridenominazione del sistema supportata da SAP Software Provisioning Manager (nota SAP [1619720]). In caso contrario, è possibile installare il software SAP in un secondo momento dopo la distribuzione della macchina virtuale di Azure.

Per altri dettagli, vedere il capitolo [Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP][deployment-guide-3.3].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Spostamento di una VM da locale a Microsoft Azure con un disco non generalizzato
Si può pianificare di spostare un sistema SAP specifico da locale a Microsoft Azure. A tale scopo è possibile caricare in Microsoft Azure il disco rigido virtuale contenente il sistema operativo, i file binari SAP e gli eventuali file binari DBMS nonché i dischi rigidi virtuali con i file di dati e di log del sistema DBMS. A differenza dello scenario descritto nel precedente capitolo [Distribuzione di una VM con un'immagine personalizzata][deployment-guide-3.1.2], il nome host, il SID SAP e gli account utente SAP nella VM di Azure vengono mantenuti perché sono stati configurati nell'ambiente locale. Di conseguenza, non è necessario generalizzare il sistema operativo. Questo caso si applicherà prevalentemente a scenari cross-premise in cui il landscape SAP viene eseguito in parte in locale e in parte in Microsoft Azure.

Per altri dettagli, vedere il capitolo [Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP][deployment-guide-3.4].

### <a name="a-namedb477013-9060-4602-9ad4-b0316f8bb281ascenario-1-deploying-a-vm-out-of-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP
Microsoft Azure offre la possibilità di distribuire un'istanza di VM da Azure Marketplace, in cui sono disponibili alcune immagini del sistema operativo standard per Windows Server e diverse distribuzioni Linux. È anche possibile distribuire un'immagine che include SKU di DBMS, ad esempio MS SQL Server. Per informazioni dettagliate sull'uso delle immagini con SKU di DBMS, vedere la [Guida alla distribuzione DBMS][dbms-guide].

La sequenza di passaggi specifica di SAP per distribuire una VM da Azure Marketplace si presenta come segue:

![Diagramma di flusso della distribuzione di VM per sistemi SAP con un'immagine di VM di Azure Marketplace][deployment-guide-figure-100]

Come illustrato nel diagramma di flusso, è necessario seguire questa procedura:

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Creare una macchina virtuale con il portale di Azure
Il modo più semplice per creare una nuova macchina virtuale usando un'immagine di Azure Marketplace è tramite il portale di Azure. Passare a <https://portal.azure.com/#create> o fare clic su "+" sul lato sinistro del portale di Azure. Immettere il tipo di sistema operativo che si vuole distribuire nel campo di ricerca, ad esempio Windows, SLES or RHEL e selezionare la versione. Assicurarsi di selezionare il modello di distribuzione "Resource Manager" e fare clic su Crea.

La procedura guidata illustra in dettaglio i parametri necessari per creare la macchina virtuale nonché tutte le risorse necessarie, come le interfacce di rete o gli account di archiviazione. Ecco alcuni di questi parametri:

1. Nozioni di base
   1. Nome: nome della risorsa, ovvero della macchina virtuale.
   1. Nome utente e password/chiave pubblica SSH: immettere il nome utente e la password dell'utente creato durante il provisioning. Per una macchina virtuale Linux, è anche possibile immettere la chiave pubblica SSH che si vuole usare per accedere alla macchina con SSH.
   1. Sottoscrizione: selezionare la sottoscrizione che si vuole usare per effettuare il provisioning della nuova macchina virtuale.
   1. Gruppo di risorse: nome del gruppo di risorse. È possibile inserire il nome di un nuovo gruppo di risorse o di uno già esistente.
   1. Percorso: selezionare il percorso in cui deve essere distribuita la nuova macchina virtuale. Se si vuole connettere la macchina virtuale alla rete locale, verificare di selezionare il percorso della rete virtuale che connette Azure alla rete locale. Per altri dettagli, vedere il capitolo [Rete di Microsoft Azure][planning-guide-microsoft-azure-networking] nella [Guida alla pianificazione][planning-guide].
1. Dimensione

   Per un elenco dei tipi di macchine virtuali supportati, leggere la nota SAP [1928533]. Se si vuole usare Archiviazione Premium, verificare anche di selezionare il tipo corretto. Non tutti i tipi di VM supportano Archiviazione Premium. Per altri dettagli, vedere i capitoli [Archiviazione: Archiviazione di Microsoft Azure e dischi dati][planning-guide-storage-microsoft-azure-storage-and-data-disks] e [Archiviazione Premium di Azure][planning-guide-azure-premium-storage] nella [Guida alla pianificazione][planning-guide].

1. Impostazioni
   1. Account di archiviazione: selezionare un account di archiviazione esistente o crearne uno nuovo. Per altri dettagli sui diversi tipi di archiviazione, leggere il capitolo [Archiviazione di Microsoft Azure][dbms-guide-2.3] della [Guida alla distribuzione DBMS][dbms-guide]. Si noti che non tutti i tipi di archiviazione sono supportati per l'esecuzione di applicazioni SAP.
   1. Rete virtuale e Subnet: se si vuole integrare la macchina virtuale nella Intranet, selezionare la rete virtuale connessa alla rete locale.
   1. Indirizzo IP pubblico: selezionare l'indirizzo IP pubblico che si vuole usare oppure immettere i parametri per crearne uno nuovo. È possibile usare un indirizzo IP pubblico per accedere alla macchina virtuale tramite Internet. Verificare anche di creare un gruppo di sicurezza di rete per filtrare l'accesso alla macchina virtuale.
   1. Gruppo di sicurezza di rete: per altri dettagli, vedere [Che cos'è un gruppo di sicurezza di rete][virtual-networks-nsg].
   1. Disponibilità: selezionare un set di disponibilità oppure immettere i parametri per crearne uno nuovo. Per altre informazioni, vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3].
   1. Monitoraggio: è possibile disabilitare l'impostazione di diagnostica. Verrà abilitata automaticamente quando si eseguono i comandi per abilitare il monitoraggio avanzato di Azure come descritto nel capitolo [Configurare il monitoraggio][deployment-guide-configure-monitoring-scenario-1].
   
1. Riepilogo: convalidare le informazioni visualizzate nella pagina di riepilogo e fare clic su OK.

Al termine della procedura guidata, la macchina virtuale verrà distribuita nel gruppo di risorse selezionato.

#### <a name="create-virtual-machine-using-a-template"></a>Creare una macchina virtuale con un modello
È anche possibile creare una distribuzione usando uno dei modelli SAP pubblicati nel [repository di GitHub azure-quickstart-templates][azure-quickstart-templates-github]. In alternativa, si può creare una macchina virtuale manualmente con il [portale di Azure][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o l'[interfaccia della riga di comando di Azure][virtual-machines-linux-tutorial].

* [Modello per configurazione a 2 livelli (una sola macchina virtuale) (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]: usare questo modello se si vuole creare un sistema a 2 livelli con una sola macchina virtuale.
* [Modello per configurazione a 3 livelli (più macchine virtuali) (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]: usare questo modello se si vuole creare un sistema a 3 livelli con più macchine virtuali.

Dopo l'apertura di uno dei modelli precedenti, nel portale di Azure viene visualizzata una schermata in cui è possibile immettere i parametri per il modello. Immettere le seguenti informazioni:

1. Nozioni di base
  * **Sottoscrizione**: sottoscrizione in cui si vuole distribuire il modello.
  * **Gruppo di risorse**: gruppo di risorse in cui si vuole distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione selezionata.
  * **Percorso**: percorso per la distribuzione del modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso del gruppo di risorse.
1. Impostazioni
  * **Sap System Id**: ID del sistema SAP
  * **Os Type**: sistema operativo che si vuole distribuire, ad esempio Windows Server 2012 R2, SLES 12 o RHEL 7.2
    * L'elenco non contiene tutti i sistemi operativi supportati, ad esempio non contiene Windows Server 2008 R2 anche se è supportato da SAP. Per un elenco dei sistemi operativi supportati, leggere la nota SAP [1928533].
  * **Sap System Size**: dimensioni del sistema SAP
    * Quantità di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP
  * **System Availability**: (solo per modello a&3; livelli): disponibilità del sistema
    * Selezionare "HA" per una configurazione adatta a un'installazione a disponibilità elevata. Verranno creati due server di database e due server per ASCS.
  * **Storage Type**: (solo per modello a&2; livelli): tipo di archiviazione da usare
    * Per sistemi di maggiori dimensioni, è consigliabile usare Archiviazione Premium. Per altre informazioni sui diversi tipi di archiviazione, leggere quanto segue:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD di Azure Premium per l'istanza DBMS di SAP)
      * [Archiviazione di Microsoft Azure] [dbms-Guida-2.3] della [Guida alla distribuzione DBMS][dbms-guide]
      * [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure][storage-premium-storage-preview-portal]
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
  * **Admin Username** e **Admin Password**: nome utente e password dell'amministratore
    * Verrà creato un nuovo utente con cui è possibile accedere alla macchina
  * **New Or Existing Subnet**: determina se devono essere create una nuova rete virtuale e una nuova subnet o deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare "existing".
  * **Subnet Id**: ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale Express Route o VPN per connettere la macchina virtuale alla rete locale. L'ID si presenta in genere come segue: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. Condizioni  
    Leggere e accettare le note legali.

Dopo aver immesso tutti i parametri, confermare la schermata facendo clic su Acquista. 

Si noti che quando si usa un'immagine di Azure Marketplace, per impostazione predefinita viene distribuito l'agente di macchine virtuali di Azure.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy
A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella macchina virtuale, se è connessa alla rete locale tramite VPN o Express Route. In caso contrario, la macchina virtuale potrebbe non riuscire ad accedere a Internet e quindi a scaricare le estensioni necessarie o raccogliere i dati di monitoraggio. Vedere il capitolo [Configurare il proxy][deployment-guide-configure-proxy] di questo documento.

#### <a name="join-domain-windows-only"></a>Aggiungere un dominio (solo per Windows)
In caso di distribuzione in Azure connessa ad AD/DNS locale tramite Express Route o connessione da sito a sito di Azure (denominata anche "cross-premise" nella [Guida alla pianificazione e all'implementazione][planning-guide]), è previsto che la VM venga aggiunta a un dominio locale. Le considerazioni relative a questo passaggio sono descritte nel capitolo [Aggiungere la VM a un dominio locale (solo per Windows)][deployment-guide-4.3] di questo documento.

#### <a name="a-nameec323ac3-1de9-4c3a-b770-4ff701def65baconfigure-monitoring"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurare il monitoraggio
Per ottenere un ambiente SAP sicuramente supportato, configurare l'estensione di monitoraggio avanzato di Azure per SAP come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5] di questo documento.

Controllare i prerequisiti per il monitoraggio di SAP verificando le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate nel capitolo [Risorse SAP][deployment-guide-2.2] di questo documento.

#### <a name="monitoring-check"></a>Controllo del monitoraggio
Controllare che il monitoraggio funzioni come descritto nel capitolo [Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Passaggi post-distribuzione
Al termine della creazione, la macchina virtuale viene distribuita ed è necessario installare tutti i componenti software richiesti nella macchina virtuale. Per questo tipo di distribuzione di macchina virtuale è quindi necessario che il software da installare sia già disponibile in Microsoft Azure in un'altra macchina virtuale o sotto forma di disco collegabile. Negli scenari cross-premise, invece, la connettività agli asset locali (condivisioni di installazione) è data per scontata.

### <a name="a-name54a1fc6d-24fd-4feb-9c57-ac588a55dff2ascenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP
Come descritto nella [Guida alla pianificazione e all'implementazione][planning-guide], è possibile preparare e creare un'immagine personalizzata e usarla per creare più macchine virtuali nuove. In un diagramma di flusso, la sequenza di passaggi si presenta come segue:

![Diagramma di flusso della distribuzione di VM per sistemi SAP con un'immagine di VM di marketplace privato][deployment-guide-figure-300]

Come illustrato nel diagramma di flusso, è necessario seguire questa procedura:

#### <a name="create-the-virtual-machine"></a>Creare la macchina virtuale
Per creare una distribuzione usando un'immagine del sistema operativo privata tramite il portale di Azure, usare uno dei modelli SAP elencati di seguito. Questi modelli sono pubblicati nel [repository di GitHub azure-quickstart-templates][azure-quickstart-templates-github].
È anche possibile creare una macchina virtuale manualmente con [PowerShell][virtual-machines-upload-image-windows-resource-manager]. 

* [Modello per configurazione a 2 livelli (una sola macchina virtuale) (sap-2-tier-user-image)][sap-templates-2-tier-user-image]: usare questo modello se si vuole creare un sistema a 2 livelli con una sola macchina virtuale e un'immagine del sistema operativo personalizzata.
* [Modello per configurazione a 3 livelli (più macchine virtuali) (sap-3-tier-user-image)][sap-templates-3-tier-user-image]: usare questo modello se si vuole creare un sistema a 3 livelli con più macchine virtuali e un'immagine del sistema operativo personalizzata.

Dopo l'apertura di uno dei modelli precedenti, nel portale di Azure viene visualizzata una schermata in cui è possibile immettere i parametri per il modello. Immettere le seguenti informazioni:

1. Nozioni di base
  * **Sottoscrizione**: sottoscrizione in cui si vuole distribuire il modello.
  * **Gruppo di risorse**: gruppo di risorse in cui si vuole distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione selezionata.
  * **Percorso**: percorso per la distribuzione del modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso del gruppo di risorse.
1. Impostazioni
  * **Sap System Id**: ID del sistema SAP
  * **Os Type**: tipo di sistema operativo da distribuire (Windows o Linux)
  * **Sap System Size**: dimensioni del sistema SAP
    * Quantità di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP
  * **System Availability**: (solo per modello a&3; livelli): disponibilità del sistema 
    * Selezionare "HA" per una configurazione adatta a un'installazione a disponibilità elevata. Verranno creati due server di database e due server per ASCS.
  * **Storage Type**: (solo per modello a&2; livelli): tipo di archiviazione da usare 
    * Per sistemi di maggiori dimensioni, è consigliabile usare Archiviazione Premium. Per altre informazioni sui diversi tipi di archiviazione, leggere quanto segue: 
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD di Azure Premium per l'istanza DBMS di SAP)
      * [Archiviazione di Microsoft Azure] [dbms-Guida-2.3] della [Guida alla distribuzione DBMS][dbms-guide]
      * [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure][storage-premium-storage-preview-portal]
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
  * **User Image Vhd Uri**: URI del disco rigido virtuale dell'immagine del sistema operativo privata, ad esempio https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
  * **User Image Storage Account`<accountname`: nome dell'account di archiviazione in cui viene archiviata l'immagine del sistema operativo privata, ad esempio **> nel precedente URI di esempio
  * **Admin Username** e **Admin Password**: nome utente e password dell'amministratore
    * Verrà creato un nuovo utente con cui è possibile accedere alla macchina
  * **New Or Existing Subnet**: determina se devono essere create una nuova rete virtuale e una nuova subnet o deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare "existing".
  * **Subnet Id**: ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale Express Route o VPN per connettere la macchina virtuale alla rete locale. L'ID si presenta in genere come segue: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. Condizioni  
    Leggere e accettare le note legali.

Dopo aver immesso tutti i parametri, confermare la schermata facendo clic su Acquista.

#### <a name="install-vm-agent-linux-only"></a>Installare l'agente di macchine virtuali (solo per Linux)
Per usare i modelli descritti sopra, è necessario che l'agente Linux sia già installato nell'immagine utente. In caso contrario, la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine virtuali nell'immagine utente come descritto nel capitolo [Scaricare, installare e abilitare l'agente di macchine virtuali di Azure][deployment-guide-4.4] di questo documento.
Se non si usano i modelli descritti sopra, si può anche installare l'agente di macchine virtuali in un secondo momento.

#### <a name="join-domain-windows-only"></a>Aggiungere un dominio (solo per Windows)
In caso di distribuzione in Azure connessa ad AD/DNS locale tramite Express Route o connessione da sito a sito di Azure (denominata anche "cross-premise" nella [Guida alla pianificazione e all'implementazione][planning-guide]), è previsto che la VM venga aggiunta a un dominio locale. Le considerazioni relative a questo passaggio sono descritte nel capitolo [Aggiungere la VM a un dominio locale (solo per Windows)][deployment-guide-4.3] di questo documento.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy
A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella macchina virtuale, se è connessa alla rete locale tramite VPN o Express Route. In caso contrario, la macchina virtuale potrebbe non riuscire ad accedere a Internet e quindi a scaricare le estensioni necessarie o raccogliere i dati di monitoraggio. Vedere il capitolo [Configurare il proxy][deployment-guide-configure-proxy] di questo documento.

#### <a name="configure-monitoring"></a>Configurare il monitoraggio
Per ottenere un ambiente SAP sicuramente supportato, configurare l'estensione di monitoraggio di Azure per SAP come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5] di questo documento.
Controllare i prerequisiti per il monitoraggio di SAP verificando le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate nel capitolo [Risorse SAP][deployment-guide-2.2] di questo documento.

#### <a name="monitoring-check"></a>Controllo del monitoraggio
Controllare che il monitoraggio funzioni come descritto nel capitolo [Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure][deployment-guide-troubleshooting-chapter].

### <a name="a-namea9a60133-a763-4de8-8986-ac0fa33aa8c1ascenario-3-moving-a-vm-from-on-premises-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP
Se si prevede di spostare il sistema SAP nel formato e nella forma correnti (stesso nome host e SID di SAP) da locale ad Azure, il disco rigido virtuale viene usato direttamente come disco del sistema operativo e non si fa riferimento a tale disco come immagine durante la distribuzione. In questo caso, l'agente di macchine virtuali non verrà installato automaticamente durante la distribuzione. Dato che l'agente di macchine virtuali e l'estensione di monitoraggio avanzato di Azure per SAP sono un prerequisito per il supporto di SAP, è necessario scaricare, installare e abilitare entrambi i componenti manualmente dopo la creazione della macchina virtuale. 

Per informazioni dettagliate sull'agente di macchine virtuali di Azure, vedere questo articolo:

[comment]: <> (MSSedusch TODO Update Windows Link below) 

- - -
> ![Windows][Logo_Windows] Windows
> 
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> [Guida dell'utente dell'agente Linux di Azure][virtual-machines-linux-agent-user-guide]
> 
> 

- - -
Il flusso di lavoro dei diversi passaggi si presenta come segue:

![Diagramma di flusso della distribuzione di VM per sistemi SAP con un disco della VM][deployment-guide-figure-400]

Supponendo che il disco sia già caricato e definito in Azure (vedere la [Guida alla pianificazione e all'implementazione][planning-guide]), seguire questa procedura:

#### <a name="create-virtual-machine"></a>Creare la macchina virtuale
Per creare una distribuzione usando un disco del sistema operativo privato tramite il portale di Azure, usare il modello SAP pubblicato nel [repository di GitHub azure-quickstart-templates][azure-quickstart-templates-github]. È anche possibile creare una macchina virtuale manualmente con [PowerShell][virtual-machines-windows-create-vm-specialized].

* [Modello per configurazione a&2; livelli (una sola macchina virtuale) (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]
  * : usare questo modello se si vuole creare un sistema a 2 livelli con una sola macchina virtuale.

Dopo l'apertura di uno dei modelli precedenti, nel portale di Azure viene visualizzata una schermata in cui è possibile immettere i parametri per il modello. Immettere le seguenti informazioni:

1. Nozioni di base
  * **Sottoscrizione**: sottoscrizione in cui si vuole distribuire il modello.
  * **Gruppo di risorse**: gruppo di risorse in cui si vuole distribuire il modello. È possibile creare un nuovo gruppo di risorse o selezionarne uno esistente nella sottoscrizione selezionata.
  * **Percorso**: percorso per la distribuzione del modello. Se si seleziona un gruppo di risorse esistente, viene usato il percorso del gruppo di risorse.
1. Impostazioni
  * **Sap System Id**: ID del sistema SAP
  * **Os Type**: tipo di sistema operativo da distribuire (Windows o Linux)
  * **Sap System Size**: dimensioni del sistema SAP
    * Quantità di SAPS forniti dal nuovo sistema. Se non si è certi del numero di SAPS necessari per il sistema, chiedere all'integratore di sistemi o al partner tecnologico SAP
  * **Storage Type**: (solo per modello a&2; livelli): tipo di archiviazione da usare 
    * Per sistemi di maggiori dimensioni, è consigliabile usare Archiviazione Premium. Per altre informazioni sui diversi tipi di archiviazione, leggere quanto segue:
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance][2367194] (Uso dell'archiviazione unità SSD di Azure Premium per l'istanza DBMS di SAP) 
      * [Archiviazione di Microsoft Azure] [dbms-Guida-2.3] della [Guida alla distribuzione DBMS][dbms-guide]
      * [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure][storage-premium-storage-preview-portal]
      * [Introduzione ad Archiviazione di Microsoft Azure][storage-introduction]
  * **Os Disk Vhd Uri**: URI del disco del sistema operativo privato, ad esempio https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
  * **New Or Existing Subnet**: determina se devono essere create una nuova rete virtuale e una nuova subnet o deve essere usata una subnet esistente. Se è già presente una rete virtuale connessa alla rete locale, selezionare "existing".
  * **Subnet Id**: ID della subnet a cui devono essere connesse le macchine virtuali. Selezionare la subnet della rete virtuale Express Route o VPN per connettere la macchina virtuale alla rete locale. L'ID si presenta in genere come segue: /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. Condizioni  
    Leggere e accettare le note legali.

Dopo aver immesso tutti i parametri, confermare la schermata facendo clic su Acquista.

#### <a name="install-vm-agent"></a>Installare l'agente di macchine virtuali
Per usare i modelli descritti sopra, è necessario che l'agente di macchine virtuali sia installato nel disco del sistema operativo. In caso contrario, la distribuzione avrà esito negativo. Scaricare e installare l'agente di macchine virtuali nella macchina virtuale come descritto nel capitolo [Scaricare, installare e abilitare l'agente di macchine virtuali di Azure][deployment-guide-4.4] di questo documento.

Se non si usano i modelli descritti sopra, si può anche installare l'agente di macchine virtuali in un secondo momento.

#### <a name="join-domain-windows-only"></a>Aggiungere un dominio (solo per Windows)
In caso di distribuzione in Azure connessa ad AD/DNS locale tramite Express Route o connessione da sito a sito di Azure (denominata anche "cross-premise" nella [Guida alla pianificazione e all'implementazione][planning-guide]), è previsto che la VM venga aggiunta a un dominio locale. Le considerazioni relative a questo passaggio sono descritte nel capitolo [Aggiungere la VM a un dominio locale (solo per Windows)][deployment-guide-4.3] di questo documento.

#### <a name="configure-proxy-settings"></a>Configurare le impostazioni proxy
A seconda della configurazione della rete locale, potrebbe essere necessario configurare il proxy nella macchina virtuale, se è connessa alla rete locale tramite VPN o Express Route. In caso contrario, la macchina virtuale potrebbe non riuscire ad accedere a Internet e quindi a scaricare le estensioni necessarie o raccogliere i dati di monitoraggio. Vedere il capitolo [Configurare il proxy][deployment-guide-configure-proxy] di questo documento.

#### <a name="configure-monitoring"></a>Configurare il monitoraggio
Per ottenere un ambiente SAP sicuramente supportato, configurare l'estensione di monitoraggio avanzato di Azure per SAP come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5] di questo documento.

Controllare i prerequisiti per il monitoraggio di SAP verificando le versioni minime obbligatorie del kernel SAP e dell'agente host SAP nelle risorse elencate nel capitolo [Risorse SAP][deployment-guide-2.2] di questo documento.

#### <a name="monitoring-check"></a>Controllo del monitoraggio
Controllare che il monitoraggio funzioni come descritto nel capitolo [Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Scenario 4: Aggiornamento della configurazione di monitoraggio per SAP
Nei casi seguenti è necessario aggiornare la configurazione di monitoraggio di SAP:

* Il team congiunto MS/SAP ha esteso le funzionalità di monitoraggio e richiede più o meno contatori. 
* Microsoft introduce una nuova versione dell'infrastruttura di Azure sottostante che fornisce i dati di monitoraggio e l'estensione di monitoraggio avanzato di Azure per SAP deve essere adattata a tali modifiche.
* Si montano altri dischi rigidi virtuali nella macchina virtuale di Azure o si rimuove un disco rigido virtuale. In questo caso, è necessario aggiornare la raccolta dei dati correlati all'archiviazione. Se si modifica la configurazione aggiungendo o eliminando endpoint o assegnando un indirizzo IP a una VM, ciò non influirà sulla configurazione di monitoraggio.
* Si modificano le dimensioni della macchina virtuale di Azure, ad esempio da A5 a qualsiasi altra dimensione di macchina virtuale.
* Si aggiungono nuove interfacce di rete alla VM di Azure.

Per aggiornare la configurazione di monitoraggio, procedere come segue:

* Aggiornare l'infrastruttura di monitoraggio seguendo la procedura illustrata nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5] di questo documento. Rieseguendo lo script descritto in questo capitolo verrà rilevata la distribuzione di una configurazione di monitoraggio e verranno apportate le modifiche necessarie a tale configurazione. 

## <a name="detailed-single-deployment-steps"></a>Singoli passaggi di distribuzione dettagliati
### <a name="a-name604bcec2-8b6e-48d2-a944-61b0f5dee2f7adeploying-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Distribuzione dei cmdlet di Azure PowerShell
* Passare a <https://azure.microsoft.com/downloads/>
* Nella sezione "Strumenti da riga di comando" è presente una sezione "PowerShell". Selezionare il collegamento "Windows - Installazione".
* Verrà visualizzato Microsoft Download Manager con una voce con estensione exe. Selezionare l'opzione "Esegui".
* Verrà visualizzata una finestra popup in cui viene chiesto se si vuole eseguire l'Installazione guidata piattaforma Web Microsoft. Scegliere SÌ.
* Verrà visualizzata una schermata come la seguente:

![Schermata di installazione dei cmdlet di Azure PowerShell][deployment-guide-figure-500]
 <a name="figure-5"></a>

* Scegliere Installa e accettare il contratto di licenza con l'utente finale.

Controllare spesso se i cmdlet di PowerShell sono stati aggiornati. In genere sono disponibili aggiornamenti con cadenza mensile. Il modo più semplice per eseguire questa operazione consiste nel seguire la procedura di installazione descritta sopra fino alla schermata di installazione illustrata in [questa][deployment-guide-figure-5] figura. In questa schermata vengono visualizzati la data di rilascio dei cmdlet e il numero di versione effettivo. Se non diversamente indicato nella nota SAP [1928533] o [2015553], è consigliabile usare la versione più recente dei cmdlet di Azure PowerShell.

È possibile verificare la versione attualmente installata dei cmdlet di Azure nel PC desktop/portatile con il comando PowerShell seguente:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

Il risultato verrà presentato come illustrato di seguito in [questa][deployment-guide-figure-6] figura.

![Risultato del controllo della versione dei cmdlet di Azure PowerShell][deployment-guide-figure-600]
 <a name="figure-6"></a>

Se la versione dei cmdlet di Azure installata nel PC desktop/portatile è quella corrente, la prima schermata dopo l'avvio dell'Installazione guidata piattaforma Web Microsoft avrà un aspetto leggermente diverso rispetto a [questa][deployment-guide-figure-5] figura.

Si noti il riquadro rosso nella [figura][deployment-guide-figure-7] di seguito.

![Schermata di installazione dei cmdlet di Azure PowerShell che indica che è installata la versione più recente dei cmdlet di Azure PowerShell][deployment-guide-figure-700]
 <a name="figure-7"></a>

Se la schermata si presenta come [sopra][deployment-guide-figure-7] e indica quindi che è già installata la versione più recente dei cmdlet di Azure, non è necessario continuare con l'installazione. In questo caso, è possibile uscire dall'installazione a questo punto.

### <a name="a-name1ded9453-1330-442a-86ea-e0fd8ae8cab3adeploying-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Distribuzione dell'interfaccia della riga di comando di Azure
* Passare a <https://azure.microsoft.com/downloads/>
* Nella sezione "Interfaccia della riga di comando di Azure" all'interno della sezione "Strumenti da riga di comando" selezionare il collegamento Installazione relativo al sistema operativo in uso.

Controllare spesso se l'interfaccia della riga di comando di Azure è stata aggiornata. In genere sono disponibili aggiornamenti con cadenza mensile. Il modo più semplice per eseguire questa operazione consiste nel seguire la procedura di installazione descritta sopra.

È possibile verificare la versione attualmente installata dell'interfaccia della riga di comando di Azure nel PC desktop/portatile con il comando seguente:

```
azure --version
```

Il risultato verrà presentato come illustrato di seguito in [questa][deployment-guide-figure-azure-cli-version] figura.

![Risultato del controllo della versione dell'interfaccia della riga di comando di Azure][deployment-guide-figure-760]
 <a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="a-name31d9ecd6-b136-4c73-b61e-da4a29bbc9ccajoin-vm-into-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Aggiungere la VM a un dominio locale (solo per Windows)
In caso di distribuzione di macchine virtuali SAP in uno scenario cross-premise in cui AD e DNS locali sono estesi in Azure, è previsto che le macchine virtuali vengano aggiunte a un dominio locale. La procedura dettagliata per aggiungere una VM a un dominio locale e il software necessario per fare parte di un dominio locale dipendono dal cliente. L'aggiunta di una VM a un dominio locale comporta in genere l'installazione di software aggiuntivo come software di protezione da malware o di monitoraggio o vari agenti di backup.

Nei casi in cui con l'aggiunta a un dominio vengono forzate le impostazioni di proxy Internet, inoltre, è necessario verificare che queste impostazioni siano presenti anche nell'account di sistema locale di Windows (S-1-5-18) nella VM guest. Il modo più semplice consiste nel forzare il proxy con Criteri di gruppo di dominio che si applicano ai sistemi nel dominio.

### <a name="a-namec7cbb0dc-52a4-49db-8e03-83e7edc2927dadownload-install-and-enable-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Scaricare, installare e abilitare l'agente di macchine virtuali di Azure
Per le macchine virtuali distribuite da un'immagine del sistema operativo non generalizzata (ad esempio, non sottoposta a Sysprep per Windows), è necessario scaricare, installare e abilitare manualmente l'agente di macchine virtuali di Azure. 

Per le macchine virtuali distribuite da Azure Marketplace, questo passaggio non è necessario perché queste immagini contengono già l'agente di macchine virtuali di Azure.

#### <a name="a-nameb2db5c9a-a076-42c6-9835-16945868e866awindows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
* Scaricare l'agente di macchine virtuali di Azure:
  * Scaricare il pacchetto di installazione dell'agente di macchine virtuali di Azure dall'indirizzo <https://go.microsoft.com/fwlink/?LinkId=394789>
  * Archiviare il pacchetto MSI dell'agente di macchine virtuali in locale nel portatile o in un server
* Installare l'agente di macchine virtuali di Azure:
  * Connettersi alla VM di Azure distribuita con Desktop remoto (RDP)
  * Aprire una finestra di Esplora risorse nella macchina virtuale e quindi scegliere la directory di destinazione per il file MSI dell'agente di macchine virtuali
  * Trascinare file MSI di installazione dell'agente di macchine virtuali di Azure dal server/portatile locale alla directory di destinazione dell'agente di macchine virtuali nella VM
  * Fare doppio clic sul file MSI nella macchina virtuale
  * Per le macchine virtuali aggiunte a domini locali, verificare che le eventuali impostazioni di proxy Internet si applichino anche all'account di sistema locale di Windows (S-1-5-18) nella macchina virtuale, come descritto nel capitolo [Configurare il proxy][deployment-guide-configure-proxy]. L'agente di macchine virtuali verrà eseguito in questo contesto e deve potersi connettere ad Azure.

Per l'aggiornamento dell'agente di macchine virtuali di Azure non è necessario alcun intervento dell'utente. L'agente di macchine virtuali si aggiorna automaticamente senza che sia necessario un riavvio della VM.

#### <a name="a-name6889ff12-eaaf-4f3c-97e1-7c9edc7f7542alinux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Installare l'agente di macchine virtuali per Linux con il comando seguente:

* **SLES**

```
sudo zypper install WALinuxAgent
```
* **RHEL**

```
sudo yum install WALinuxAgent
```

Per aggiornare l'agente Linux di Azure se è già installato, seguire la procedura descritta in [questo articolo][virtual-machines-linux-update-agent]. 

### <a name="a-namebaccae00-6f79-4307-ade4-40292ce4e02daconfigure-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurare il proxy
La procedura per configurare il proxy presenta differenze tra Windows e Linux.

#### <a name="windows"></a>Windows
Queste impostazioni devono essere valide anche per consentire all'account LocalSystem di accedere a Internet. Se le impostazioni proxy non sono impostate con Criteri di gruppo, si possono configurare per l'account LocalSystem. A tale scopo, seguire questa procedura:

1. Aprire gpedit.msc
2. Passare a Configurazione computer -> Modelli amministrativi -> Componenti di Windows -> Internet Explorer. Assicurarsi che l'impostazione "Basa impostazioni proxy sul computer (non sull'utente)" sia impostata su Abilitato.
3. Aprire il Pannello di controllo e accedere a Rete e Internet -> Opzioni Internet
4. Aprire la scheda Connessioni e fare clic su Impostazioni LAN
5. Disabilitare "Rileva automaticamente impostazioni"
6. Abilitare "Usa un server di proxy per la rete LAN" e immettere porta e indirizzo del proxy
7. Fare clic su Avanzate
8. Aggiungere l'indirizzo IP 168.63.129.16 all'elenco delle eccezioni

#### <a name="linux"></a>Linux
Configurare il proxy corretto nel file di configurazione dell'agente guest di Microsoft Azure, disponibile in /etc/waagent.conf. È necessario impostare i parametri seguenti:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Al termine della modifica della configurazione, riavviare l'agente con

```
sudo service waagent restart
```

Le impostazioni proxy in /etc/waagent.conf si applicano anche alle estensioni di VM necessarie. Se si vogliono usare i repository di Azure, verificare che il traffico verso i repository non attraversi la Intranet locale. Se sono state create route definite dall'utente per abilitare il tunneling forzato, verificare di aggiungere una route che indirizza il traffico verso i repository direttamente in Internet e non tramite la connessione da sito a sito.

* **SLES** È anche possibile aggiungere route per gli indirizzi IP elencati in /etc/regionserverclnt.cfg. Un esempio è riportato nello screenshot seguente. 
* **RHEL** È necessario anche aggiungere route per gli indirizzi IP degli host elencati in /etc/yum.repos.d/rhui-load-balancers. Un esempio è riportato nello screenshot seguente.

Per altri dettagli sulle route definite dall'utente, vedere [questo articolo][virtual-networks-udr-overview].

![Tunneling forzato][deployment-guide-figure-50]

### <a name="a-named98edcd3-f2a1-49f7-b26a-07448ceb60caaconfigure-azure-enhanced-monitoring-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurare l'estensione di monitoraggio avanzato di Azure per SAP
Dopo che la macchina virtuale è stata preparata come descritto nel capitolo [Scenari di distribuzione di VM per SAP in Microsoft Azure][deployment-guide-3], l'agente di macchine virtuali di Azure è installato nella macchina virtuale. Il passaggio importante successivo consiste nel distribuire l'estensione di monitoraggio avanzato di Azure per SAP, disponibile nel repository di estensioni di Azure nei data center globali di Microsoft Azure. Per altri dettagli, vedere la [Guida alla pianificazione e all'implementazione][planning-guide-9.1]. 

Per installare e configurare l'estensione di monitoraggio avanzato di Azure per SAP, è possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure. Per installare l'estensione in una VM Linux o Windows usando un computer Windows, leggere il capitolo relativo ad [Azure PowerShell][deployment-guide-4.5.1]. Per installare l'estensione in una VM Linux usando un PC desktop Linux, leggere il capitolo relativo all'[interfaccia della riga di comando di Azure][deployment-guide-4.5.2].

#### <a name="a-name987cf279-d713-4b4c-8143-6b11589bb9d4aazure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell per VM Linux e Windows
Per eseguire l'attività di installazione dell'estensione di monitoraggio avanzato di Azure per SAP, seguire questa procedura:

* Verificare di aver installato l'ultima versione dei cmdlet di Microsoft Azure PowerShell. Vedere il capitolo [Distribuzione dei cmdlet di Azure PowerShell][deployment-guide-4.1] di questo documento.  
* Eseguire il cmdlet di PowerShell seguente.
    Per un elenco degli ambienti disponibili, eseguire il cmdlet Get-AzureRmEnvironment. Se si vuole usare il cloud pubblico di Azure, l'ambiente è AzureCloud. Per Azure in Cina, selezionare AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Dopo che si sono specificati i dati dell'account e la macchina virtuale di Azure, lo script distribuisce le estensioni necessarie e abilita le funzionalità obbligatorie. Ciò può richiedere alcuni minuti.
Per altre informazioni su Set-AzureRmVMAEMExtension, leggere [questo articolo di MSDN][msdn-set-azurermvmaemextension].

![Schermata risultante al termine dell'esecuzione del cmdlet di Azure specifico di SAP Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Se eseguito correttamente, Set-AzureRmVMAEMExtension completerà tutti i passaggi necessari per configurare la funzionalità di monitoraggio host per SAP. 

L'output dello script dovrebbe essere simile al seguente:

* Conferma che la configurazione di monitoraggio per il disco rigido virtuale di base (contenente il sistema operativo) e tutti i dischi rigidi virtuali aggiuntivi montati nella VM è stata definita.
* I due messaggi successivi confermano la configurazione delle metriche di archiviazione per uno specifico account di archiviazione. 
* Una riga dell'output contiene lo stato dell'aggiornamento effettivo della configurazione di monitoraggio.
* Un'altra viene visualizzata per confermare che la configurazione è stata distribuita o aggiornata.
* L'ultima riga dell'output contiene un messaggio informativo che segnala la possibilità di testare la configurazione di monitoraggio.
* Per verificare che tutti i passaggi del monitoraggio avanzato di Azure siano stati completati e che l'infrastruttura di Azure fornisca i dati necessari, procedere con il controllo di conformità dell'estensione di monitoraggio avanzato di Azure per SAP, come descritto nel capitolo [Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP][deployment-guide-5.1] di questo documento. 
* Per continuare la procedura, attendere 15-30 minuti finché Diagnostica di Azure non avrà raccolto tutti i dati pertinenti.

#### <a name="a-name408f3779-f422-4413-82f8-c57a23b4fc2faazure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Interfaccia della riga di comando di Azure per VM Linux
Per eseguire l'attività di installazione dell'estensione di monitoraggio avanzato di Azure per SAP con l'interfaccia della riga di comando di Azure, seguire questa procedura:

1. Installare l'interfaccia della riga di comando di Azure come descritto in [questo][azure-cli] articolo
2. Effettuare l'accesso con il proprio account di Azure
   
    ```
    azure login
    ```
3. Passare alla modalità Azure Resource Manager
   
    ```
    azure config mode arm
    ```
4. Abilitare il monitoraggio avanzato di Azure
   
    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
5. Verificare che il monitoraggio avanzato di Azure sia attivo nella VM di Linux Azure. Verificare l'esistenza del file /var/lib/AzureEnhancedMonitor/PerfCounters. Se esiste, visualizzare le informazioni raccolte da AEM con:
   
    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    L'output ottenuto sarà simile a:
   
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="a-name564adb4f-5c95-4041-9616-6635e83a810bachecks-and-troubleshooting-for-end-to-end-monitoring-setup-for-sap-on-azure"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure
Al termine della distribuzione della VM di Azure e della configurazione dell'infrastruttura di monitoraggio di Azure pertinente, verificare se tutti i componenti del monitoraggio avanzato di Azure funzionano in modo corretto. 

Eseguire quindi il controllo di conformità dell'estensione di monitoraggio avanzato di Azure per SAP come descritto nel capitolo [Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP][deployment-guide-5.1]. Se il risultato del controllo è positivo e si ottengono tutti i contatori delle prestazioni pertinenti, il monitoraggio di Azure è stato configurato correttamente. In questo caso, procedere con l'installazione dell'agente host SAP come descritto nelle note SAP elencate nel capitolo [Risorse SAP][deployment-guide-2.2] di questo documento. Se il risultato del controllo di conformità indica che alcuni contatori non sono presenti, procedere con l'esecuzione del controllo dell'infrastruttura di monitoraggio di Azure come descritto nel capitolo [Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure][deployment-guide-5.2]. In caso di problemi con la configurazione di monitoraggio di Azure, per altre informazioni sulla risoluzione dei problemi vedere il capitolo [Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-5.3].

### <a name="a-namebb61ce92-8c5c-461f-8c53-39f5e5ed91f2areadiness-check-for-azure-enhanced-monitoring-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP
Questo controllo consente di verificare che le metriche che verranno visualizzate nell'applicazione SAP provengano interamente dall'infrastruttura di monitoraggio di Azure sottostante. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Eseguire il controllo dello stato di preparazione in una VM Windows
Per eseguire il controllo dello stato di preparazione, accedere alla macchina virtuale di Azure (non è necessario un account amministratore) e seguire questa procedura:

* Aprire il prompt dei comandi di Windows e passare alla cartella di installazione dell'estensione di monitoraggio di Azure per SAP C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`>\drop

La versione specificata nel percorso dell'estensione di monitoraggio riportato sopra può variare. Se nella cartella di installazione risultano presenti più cartelle per la versione dell'estensione di monitoraggio, controllare la configurazione del servizio di Windows "AzureEnhancedMonitoring" e passare alla cartella indicata come "Percorso file eseguibile".

![Proprietà del servizio che esegue l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-figure-1000]

* Eseguire azperflib.exe nella finestra di comando senza alcun parametro.

> [!NOTE]
> azperflib.exe viene eseguito in ciclo e aggiorna i contatori raccolti ogni 60 secondi. Per terminare il ciclo, chiudere la finestra di comando.
> 
> 

Se l'estensione di monitoraggio avanzato di Azure non è installata o il servizio "AzureEnhancedMonitoring" non è in esecuzione, l'estensione non è stata configurata correttamente. In questo caso, per istruzioni dettagliate su come ridistribuire l'estensione vedere il capitolo [Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-5.3].

##### <a name="check-the-output-of-azperflibexe"></a>Controllare l'output di azperflib.exe
Nell'output di azperflib.exe vengono visualizzati tutti i contatori delle prestazioni di Azure popolati per SAP. Alla fine dell'elenco dei contatori raccolti sono riportati un riepilogo e un indicatore di integrità, che segnala lo stato del monitoraggio di Azure. 

![Output del controllo di integrità eseguito con azperflib.exe che indica che non sono presenti problemi][deployment-guide-figure-1100]
<a name="figure-11"></a>

Controllare nell'output il risultato restituito in merito al numero di contatori vuoti segnalati in "Counters total" e al controllo di integrità ("Health check"), come illustrato nella figura [precedente][deployment-guide-figure-11].

I valori dei risultati possono essere interpretati come segue:

| Valori dei risultati di azperflib.exe | Stato di integrità del monitoraggio di Azure |
| --- | --- |
| **API Calls - not available** | I contatori che non sono disponibili possono essere non applicabili alla configurazione della macchina virtuale o errori, vedere lo stato di integrità | 
| **Counters total: empty** |Possono essere vuoti i 2 contatori relativi all'archiviazione di Azure seguenti:  <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Tutti gli altri contatori devono contenere valori. |
| **Health check** |È positivo solo se lo stato restituito è OK. |
| **Diagnostica** |Informazioni dettagliate sullo stato di integrità |

Se il valore del controllo di integrità non è OK, seguire le istruzioni relative al controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure disponibili nel capitolo [Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure][deployment-guide-5.2] riportato di seguito.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Eseguire il controllo dello stato di preparazione in una VM Linux
Per eseguire il controllo dello stato di preparazione, connettersi con SSH alla macchina virtuale di Azure e seguire questa procedura:

* Controllare l'output dell'estensione di monitoraggio avanzato di Azure
  * more /var/lib/AzureEnhancedMonitor/PerfCounters
    * Dovrebbe offrire un elenco dei contatori delle prestazioni. Il file non deve essere vuoto
  * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
    * Dovrebbe restituire una riga in cui l'errore è "none", ad esempio: 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
  * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
    * Dovrebbe essere vuoto o inesistente
* Se il primo controllo descritto sopra non ha esito positivo, eseguire questi test aggiuntivi:
  * Verificare che waagent sia installato e avviato
    * sudo ls -al /var/lib/waagent/
      * Dovrebbe elencare il contenuto della directory waagent
    * ps -ax | grep waagent
      * Dovrebbe visualizzare una voce simile alla seguente: 'python /usr/sbin/waagent -daemon'
  * Verificare che l'estensione di diagnostica per Linux sia installata e avviata
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
      * Dovrebbe elencare il contenuto della directory dell'estensione di diagnostica per Linux
    * ps -ax | grep diagnostic
      * Dovrebbe visualizzare una voce simile alla seguente: 'python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon'
  * Verificare che l'estensione di monitoraggio avanzato di Azure sia installata e avviata
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
      * Dovrebbe elencare il contenuto della directory dell'estensione di monitoraggio avanzato di Azure
    * ps -ax | grep AzureEnhanced
      * Dovrebbe visualizzare una voce simile alla seguente: 'python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon'
* Installare l'agente host SAP come descritto nella nota SAP [1031096] e controllare l'output di saposcol
  * Eseguire /usr/sap/hostctrl/exe/saposcol -d
  * Eseguire dump ccm
  * Controllare se la metrica "Virtualization_Configuration\Enhanced Monitoring Access" è true
* Se è già installato un server applicazioni SAP NetWeaver ABAP, aprire la transazione ST06 e controllare se il monitoraggio avanzato è abilitato.

Se uno dei controlli precedenti ha esito negativo, vedere il capitolo [Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-5.3] per istruzioni dettagliate su come ridistribuire l'estensione.

### <a name="a-namee2d592ff-b4ea-4a53-a91a-e5521edb6cd1ahealth-check-for-azure-monitoring-infrastructure-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure
Se alcuni dati di monitoraggio non vengono forniti correttamente, come indicato nel test descritto nel precedente capitolo [Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP][deployment-guide-5.1], eseguire il cmdlet Test-AzureRmVMAEMExtension per testare se la configurazione corrente dell'infrastruttura di monitoraggio di Azure e dell'estensione di monitoraggio per SAP è corretta.

Per testare la configurazione di monitoraggio, eseguire questa sequenza:

* Verificare di aver installato la versione più recente del cmdlet di Microsoft Azure PowerShell come descritto nel capitolo [Distribuzione dei cmdlet di Azure PowerShell][deployment-guide-4.1] di questo documento.
* Eseguire il cmdlet di PowerShell seguente. Per un elenco degli ambienti disponibili, eseguire il cmdlet Get-AzureRmEnvironment. Se si vuole usare il cloud pubblico di Azure, l'ambiente è AzureCloud. Per Azure in Cina, selezionare AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Dopo che si sono specificati i dati dell'account e la macchina virtuale di Azure, lo script testa la configurazione della macchina virtuale scelta.

![Schermata di input del cmdlet di Azure specifico di SAP Test-VMConfigForSAP_GUI][deployment-guide-figure-1200]

Dopo che si sono immesse le informazioni relative all'account e alla macchina virtuale di Azure, lo script testa la configurazione della macchina virtuale scelta.

![Output di un test riuscito dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-figure-1300]

Verificare che ogni controllo sia contrassegnato con OK. Se per alcuni controlli non è indicato OK, eseguire il cmdlet di aggiornamento come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5] di questo documento. Attendere altri 15 minuti ed eseguire di nuovo i controlli descritti nei capitoli [Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP][deployment-guide-5.1] e [Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure][deployment-guide-5.2]. Se i controlli segnalano ancora un problema con alcuni o con tutti i contatori, passare al capitolo [Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP][deployment-guide-5.3].

### <a name="a-namefe25a7da-4e4e-4388-8907-8abc2d33cfd8afurther-troubleshooting-of-azure-monitoring-infrastructure-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP
#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] I contatori delle prestazioni di Azure non vengono visualizzati
La raccolta delle metriche delle prestazioni in Azure viene eseguita dal servizio di Windows "AzureEnhancedMonitoring". Se il servizio non è stato installato correttamente o non è in esecuzione nella VM, non può essere raccolta alcuna metrica delle prestazioni.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>La directory di installazione dell'estensione di monitoraggio avanzato di Azure è vuota
###### <a name="issue"></a>Problema
La directory di installazione C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\`<version`>\drop è vuota.

###### <a name="solution"></a>Soluzione
L'estensione non è installata. Controllare se il problema riguarda il proxy (come descritto in precedenza). Potrebbe essere necessario riavviare la macchina e/o rieseguire lo script di configurazione Set-AzureRmVMAEMExtension.

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Il servizio per il monitoraggio avanzato di Azure non esiste
###### <a name="issue"></a>Problema
Il servizio di Windows "AzureEnhancedMonitoring" non esiste. L'output di azperflib.exe restituisce un errore, come illustrato nella [figura di seguito][deployment-guide-figure-14].

![Esecuzione di azperflib.exe che indica che il servizio dell'estensione di monitoraggio avanzato di Azure per SAP non è in esecuzione][deployment-guide-figure-1400]
 <a name="figure-14"></a>

###### <a name="solution"></a>Soluzione
Se il servizio non esiste, come illustrato nella [figura precedente][deployment-guide-figure-14], l'estensione di monitoraggio di Azure per SAP non è stata installata correttamente. Ridistribuire l'estensione seguendo la procedura descritta per lo scenario di distribuzione specifico nel capitolo [Scenari di distribuzione di VM per SAP in Microsoft Azure][deployment-guide-3]. 

Al termine della distribuzione dell'estensione, controllare di nuovo se dopo 1 ora i contatori delle prestazioni di Azure sono presenti nella VM di Azure.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Il servizio per il monitoraggio avanzato di Azure esiste ma non si avvia
###### <a name="issue"></a>Problema
Il servizio di Windows "AzureEnhancedMonitoring" esiste ed è abilitato, ma non si avvia. Per altre informazioni, controllare il log eventi dell'applicazione.

###### <a name="solution"></a>Soluzione
La configurazione non è corretta. Abilitare di nuovo l'estensione di monitoraggio per la VM come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Alcuni contatori delle prestazioni di Azure non sono presenti
La raccolta delle metriche delle prestazioni in Azure viene eseguita dal servizio di Windows "AzureEnhancedMonitoring", che ottiene i dati da diverse origini. Alcuni dati di configurazione vengono raccolti in locale, le metriche delle prestazioni vengono lette da Diagnostica di Azure e i contatori relativi all'archiviazione provengono dalla registrazione a livello di sottoscrizione di archiviazione.

Se la risoluzione dei problemi in base alla nota SAP [1999351] non è sufficiente, rieseguire lo script di configurazione Set-AzureRmVMAEMExtension. Potrebbe essere necessario attendere un'ora perché i contatori relativi all'analisi dell'archiviazione o alla diagnostica non possono essere creati subito dopo essere stati abilitati. Se il problema persiste, inviare un messaggio all'assistenza clienti SAP in merito al componente BC-OP-NT-AZR per Windows o al componente BC-OP-LNX-AZR per una macchina virtuale Linux.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] I contatori delle prestazioni di Azure non vengono visualizzati
La raccolta delle metriche delle prestazioni in Azure viene eseguita da un daemon. Se il daemon non è in esecuzione, non può essere raccolta alcuna metrica delle prestazioni.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>La directory di installazione dell'estensione di monitoraggio avanzato di Azure è vuota
###### <a name="issue"></a>Problema
La directory /var/lib/waagent/ non contiene una sottodirectory per l'estensione di monitoraggio avanzato di Azure.

###### <a name="solution"></a>Soluzione
L'estensione non è installata. Controllare se il problema riguarda il proxy (come descritto in precedenza). Potrebbe essere necessario riavviare la macchina e/o rieseguire lo script di configurazione Set-AzureRmVMAEMExtension.

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Alcuni contatori delle prestazioni di Azure non sono presenti
La raccolta delle metriche delle prestazioni in Azure viene eseguita da un daemon, che ottiene i dati da diverse origini. Alcuni dati di configurazione vengono raccolti in locale, le metriche delle prestazioni vengono lette da Diagnostica di Azure e i contatori relativi all'archiviazione provengono dalla registrazione a livello di sottoscrizione di archiviazione.

Per un elenco completo e aggiornato dei problemi noti, vedere la nota SAP [1999351] , contenente informazioni aggiuntive sulla risoluzione dei problemi per il monitoraggio avanzato di Azure per SAP.

Se la risoluzione dei problemi in base alla nota SAP [1999351] non è sufficiente, rieseguire lo script di configurazione Set-AzureRmVMAEMExtension come descritto nel capitolo [Configurare l'estensione di monitoraggio avanzato di Azure per SAP][deployment-guide-4.5]. Potrebbe essere necessario attendere un'ora perché i contatori relativi all'analisi dell'archiviazione o alla diagnostica non possono essere creati subito dopo essere stati abilitati. Se il problema persiste, inviare un messaggio all'assistenza clienti SAP in merito al componente BC-OP-NT-AZR per Windows o al componente BC-OP-LNX-AZR per una macchina virtuale Linux.




<!--HONumber=Jan17_HO4-->



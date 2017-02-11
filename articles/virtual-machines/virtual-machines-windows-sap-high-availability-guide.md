---
title: "SAP NetWeaver in macchine virtuali Windows: guida alle funzionalità di disponibilità elevata | Documentazione Microsoft"
description: "Guida alle funzionalità di disponibilità elevata per SAP NetWeaver in macchine virtuali Windows"
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
ms.date: 08/18/2016
ms.author: goraco
translationtype: Human Translation
ms.sourcegitcommit: 6f2e187a4767ac75350a629ace6e15e3ba3eee33
ms.openlocfilehash: ead66f3578328a2ece9c2d2e7505b667f8746d34


---
# <a name="sap-netweaver-on-windows-virtual-machines-vms---high-availability-guide"></a>SAP NetWeaver in macchine virtuali Windows: guida alle funzionalità di disponibilità elevata
[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching per VM e dischi rigidi virtuali) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID software) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Archiviazione di Microsoft Azure) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struttura di una distribuzione RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Disponibilità elevata e ripristino di emergenza con VM di Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e versioni successive) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versioni precedenti) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Riepilogo generale su SQL Server per SAP in Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifiche per RDBMS SQL Server) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configurazione dell'archiviazione) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e ripristino) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerazioni sulle prestazioni per il backup e il ripristino) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Altro) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Risorse SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuzione di una VM con un'immagine personalizzata) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenari di distribuzione di VM per SAP in Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuzione dei cmdlet di Azure PowerShell) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Scaricare e importare i cmdlet di PowerShell pertinenti per SAP) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Aggiungere la VM a un dominio locale (solo per Windows)) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Scaricare, installare e abilitare l'agente di VM di Azure) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfaccia della riga di comando di Azure) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurare l'estensione di monitoraggio avanzato di Azure per SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP)

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

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

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

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Risorse) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Disponibilità elevata per i server applicazioni SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso dell'avvio automatico per le istanze di SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Aree di Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domini di errore) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domini di aggiornamento) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Set di disponibilità di Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concetto di macchina virtuale di Microsoft Azure) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Spostamento di una VM da locale ad Azure con un disco non generalizzato) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuzione di una VM con un'immagine specifica del cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparazione di VM con SAP per Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Differenza tra un disco di Azure e un'immagine di Azure) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Caricamento di un disco rigido virtuale da locale ad Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia di dischi tra account di archiviazione di Azure) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struttura di VM/dischi rigidi virtuali per le distribuzioni SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Impostazione del montaggio automatico per dischi collegati) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concetti della distribuzione solo cloud di istanze di SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Soluzione di monitoraggio di Azure per SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)

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

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Disponibilità elevata di SAP NetWeaver nell'infrastruttura distribuita come servizio (IaaS) di Azure) [sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (Server applicazioni SAP a disponibilità elevata) [sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (Istanza di SAP ASCS/SCS a disponibilità elevata) [sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (Istanza di SAP ASCS/SCS a disponibilità elevata con Windows Server Failover Clustering in Azure) [sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (Istanza DBMS a disponibilità elevata) [sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (Scenari di distribuzione end-to-end a disponibilità elevata) [sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (Preparare l'infrastruttura) [sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (Distribuire macchine virtuali con connettività di rete aziendale (cross-premise) da usare in fase di produzione) [sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (Distribuzione solo cloud delle istanze di SAP per test e demo) [sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Rete virtuale di Azure) [sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (Indirizzi IP DNS) [sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (Nomi host e indirizzi IP statici per l'istanza di SAP ASCS/SCS in cluster e l'istanza di DBMS in cluster) [sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (Impostare gli indirizzi IP statici per le macchine virtuali SAP) [sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (Creare un indirizzo IP statico per il servizio di bilanciamento del carico interno di Azure) [sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure) [sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure) [sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (Aggiungere macchine virtuali Windows al dominio) [sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (Aggiungere le voci del Registro di sistema in entrambi i nodi di cluster dell'istanza di SAP ASCS/SCS) [sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (Configurare un cluster di Windows Server Failover Clustering per un'istanza di SAP ASCS/SCS) [sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (Raccogliere i nodi del cluster in una configurazione cluster) [sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (Configurare un controllo di condivisione file del cluster) [sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (Creare una condivisione file) [sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (Impostare il quorum del controllo di condivisione file in Gestione cluster di failover) [sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (Installare SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS) [sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (Aggiungere .NET Framework 3.5) [sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (Installare SIOS DataKeeper) [sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (Configurare SIOS DataKeeper) [sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (Installare il sistema SAP NetWeaver) [sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (Installare di SAP con un'istanza di ASCS/SCS a disponibilità elevata) [sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (Creare un nome host virtuale per l'istanza di SAP ASCS/SCS in cluster) [sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (Installare il primo nodo del cluster SAP) [sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (Modificare il profilo SAP dell'istanza di ASCS/SCS) [sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (Aggiungere una porta probe) [sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (Installare l'istanza di database) [sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Installare il secondo nodo del cluster) [sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (Cambiare il tipo di avvio dell'istanza del servizio Windows di SAP ERS) [sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (Installare Primary Application Server SAP) [sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (Installare Additional Application Server SAP) [sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (Testare il failover e la replica SIOS dell'istanza di SAP ASCS/SCS) [sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (Istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster) [sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (Failover dal nodo A al nodo B) [sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (Istanza di SAP ASCS/SCS in esecuzione nel nodo B del cluster))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
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


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
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
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md
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
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
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
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


Macchine virtuali di Azure è la soluzione ideale per le organizzazioni che necessitano di risorse di calcolo, di archiviazione e di rete in tempi minimi e con cicli di approvvigionamento brevi. È possibile usare Macchine virtuali di Azure per distribuire applicazioni classiche, ad esempio ABAP basato su SAP NetWeaver, Java e uno stack ABAP+Java. È possibile estendere l'affidabilità e la disponibilità senza risorse locali aggiuntive. Poiché Macchine virtuali di Azure supporta la connettività cross-premise, l'organizzazione può integrare Macchine virtuali di Azure nei domini locali, nei cloud privati e nel panorama applicativo del sistema SAP.

In questo articolo viene illustrata la procedura che è possibile eseguire per distribuire sistemi SAP a disponibilità elevata in Azure, usando il modello di distribuzione Azure Resource Manager. Le attività principali che verranno illustrate sono le seguenti:

* Trovare le guide all'installazione e le note di SAP appropriate, elencate nella sezione [Risorse][sap-ha-guide-2]. Questo articolo integra la documentazione sull'installazione di SAP e le note su SAP, che sono le risorse principali per l'installazione e la distribuzione del software SAP in piattaforme specifiche.
* Apprendere le differenze tra il modello di distribuzione classica di Azure e il modello di distribuzione Azure Resource Manager.
* Comprendere le modalità quorum di Windows Server Failover Clustering, per selezionare il modello appropriato per una distribuzione specifica di Azure.
* Comprendere l'archiviazione condivisa di Windows Server Failover Clustering nei servizi di Azure.
* Apprendere come proteggere i componenti con un singolo punto di guasto, ad esempio ABAP SAP Central Services (ASCS)/SAP Central Services (SCS) e i sistemi di gestione di database (DBMS), e i componenti ridondanti, ad esempio i server applicazioni SAP, in Azure.
* Seguire un esempio dettagliato di installazione e configurazione di un sistema SAP a disponibilità elevata in un cluster di Windows Server Failover Clustering usando Azure come piattaforma, con Azure Resource Manager.
* Apprendere gli altri passaggi obbligatori per usare Windows Server Failover Clustering in Azure, ma non necessari in una distribuzione locale.

Per semplificare la distribuzione e la configurazione, in questo articolo verranno usati i nuovi modelli di Resource Manager a disponibilità elevata a tre livelli per SAP. Il modelli automatizzano la distribuzione dell'intera infrastruttura necessaria per un sistema SAP a disponibilità elevata. L'infrastruttura supporta anche il ridimensionamento SAPS del sistema SAP.

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="a-name217c5479-5595-4cd8-870d-15ab00d4f84ca-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Prerequisiti
Prima di iniziare, verificare che siano soddisfatti i prerequisiti descritti nei capitoli seguenti. Assicurarsi anche di controllare tutte le risorse elencate nella sezione [Risorse][sap-ha-guide-2].

In questo articolo vengono usati i modelli di Azure Resource Manager per [SAP NetWeaver a tre livelli](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/). Per una panoramica dei modelli, vedere i [modelli di Azure Resource Manager per SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/).

## <a name="a-name42b8f600-7ba3-4606-b8a5-53c4f026da08a-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Risorse
Anche queste guide descrivono le distribuzioni SAP in Azure:

* [SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione][planning-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione][deployment-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione DBMS][dbms-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alle funzionalità di disponibilità elevata [questa guida] ][sap-ha-guide]

> [!NOTE]
> Quando è possibile, viene fornito un collegamento alla guida all'installazione SAP di riferimento. Vedere le [guide all'installazione di SAP][sap-installation-guides]. Per i prerequisiti e per informazioni sul processo di installazione, è consigliabile leggere attentamente le guide all'installazione di SAP NetWeaver. Questo articolo illustra solo attività specifiche per i sistemi basati su SAP NetWeaver che è possibile usare con Macchine virtuali di Azure.
>
>

Queste note su SAP sono correlate all'argomento relativo a SAP in Azure:

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: dimensioni e prodotti supportati |
| [2015553] |SAP in Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1999351] |Virtualizzazione in Windows: monitoraggio avanzato |

Sono disponibili altre informazioni sulle [limitazioni delle sottoscrizioni di Azure][azure-subscription-service-limits-subscription], incluse le limitazioni predefinite generali e le limitazioni massime.

## <a name="a-name42156640c6-01cf-45a9-b225-4baa678b24f1ahigh-availability-sap-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>SAP a disponibilità elevata con il modello di distribuzione Azure Resource Manager e classica
Sono due le differenze principali tra i modelli di distribuzione Azure Resource Manager e classica:

* Gruppi di risorse
* Requisiti di clustering

### <a name="a-namef76af273-1993-4d83-b12d-65deeae23686a-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Gruppi di risorse
In Azure Resource Manager è possibile usare i gruppi di risorse per gestire tutte le risorse dell'applicazione nella sottoscrizione di Azure. In un approccio integrato tutte le risorse di un gruppo di risorse hanno lo stesso ciclo di vita. Ad esempio, tutte le risorse vengono create contemporaneamente ed eliminate contemporaneamente. Sono disponibili altre informazioni sui [gruppi di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups).

### <a name="a-name3e85fbe0-84b1-4892-87af-d9b65ff91860a-clustering-with-azure-resource-manager-vs-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>Clustering con il modello di distribuzione Azure Resource Manager e classica
Nel modello di Azure Resource Manager non è necessario un servizio cloud per usare il bilanciamento del carico interno di Azure per la disponibilità elevata.

Per usare il modello classico di Azure, seguire le procedure descritte in [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver in Azure: clustering di istanze SAP ASCS/SCS tramite Windows Server Failover Clustering in Azure con SIOS DataKeeper).

> [!NOTE]
> Per le installazioni SAP, è consigliabile usare il modello di distribuzione Azure Resource Manager perché offre molti vantaggi non disponibili nel modello di distribuzione classica. Sono disponibili altre informazioni sui [modelli di distribuzione][virtual-machines-azure-resource-manager-architecture-benefits-arm] di Azure.   
>
>

## <a name="a-name8ecf3ba0-67c0-4495-9c14-feec1a2255b7a-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering
Windows Server Failover Clustering è alla base di un'installazione SAP ASCS/SCS e di DBMS.

Un cluster di failover è un gruppo di 1 + n server (nodi) indipendenti che funzionano insieme per aumentare la disponibilità di applicazioni e servizi. Se si verifica un errore in un nodo, Windows Server Failover Clustering calcola il numero di errori che possono verificarsi e mantiene un cluster integro per fornire le applicazioni e i servizi definiti. A questo scopo, è possibile scegliere tra diverse modalità quorum.

### <a name="a-name1a3c5408-b168-46d6-99f5-4219ad1b1ff2a-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modalità quorum
È possibile scegliere tra quattro modalità quorum quando si usa Windows Server Failover Clustering:

* **Maggioranza dei nodi**. Ogni nodo del cluster può votare. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. È consigliabile scegliere questa opzione per i cluster con un numero dispari di nodi. Ad esempio, anche se si verificano errori in tre nodi di un cluster con sette nodi, il cluster ottiene comunque la maggioranza e continua l'esecuzione.  
* **Maggioranza dei nodi e dei dischi**. Ogni nodo e un disco designato (un disco di controllo) nello spazio di archiviazione del cluster possono votare quando sono disponibili e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi. Se metà dei nodi e il disco sono online, il cluster rimane in uno stato di integrità.
* **Maggioranza dei nodi e delle condivisioni file**. Ogni nodo e una condivisione file designata (controllo di condivisione file) creata dall'amministratore possono votare, indipendentemente dal fatto che siano disponibili e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire con più della metà dei voti. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi. È simile alla modalità Maggioranza dei nodi e dei dischi, ma usa una condivisione file di controllo invece di un disco di controllo. Questa modalità è facile da implementare, ma la condivisione file potrebbe diventare un singolo punto di guasto, se non è a disponibilità elevata.
* **Non di maggioranza - solo disco**. Il cluster ha un quorum se un nodo è disponibile e in comunicazione con un disco specifico nello spazio di archiviazione del cluster. Solo i nodi che sono anche in comunicazione con questo disco possano aggiungersi al cluster. Si consiglia di non usare questa modalità.  

  ## <a name="a-namefdfee875-6e66-483a-a343-14bbaee33275a-windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server Failover Clustering locale
  L'esempio nella figura 1 illustra un cluster con due nodi. Se la connessione di rete tra i nodi viene interrotta ed entrambi i nodi rimangono operativi, un disco quorum o una condivisione file determina quale nodo continuerà a fornire le applicazioni e i servizi del cluster. Il nodo che ha accesso alla condivisione file o al disco quorum è il nodo che garantisce la continuità dei servizi.

Poiché questo esempio usa un cluster con due nodi, viene usata la modalità quorum Maggioranza dei nodi e delle condivisioni file. Anche l'opzione Maggioranza dei nodi e dei dischi è valida. In un ambiente di produzione è consigliabile usare un disco quorum. Per fare in modo che sia a disponibilità elevata, è possibile usare una tecnologia basata su un sistema di archiviazione e sulla rete.

![Figura 1: Esempio di configurazione di Windows Server Failover Clustering proposta per SAP ASCS/SCS in Azure][sap-ha-guide-figure-1000]

***Figura 1:** Esempio di configurazione di Windows Server Failover Clustering proposta per SAP ASCS/SCS in Azure*

### <a name="a-namebe21cf3e-fb01-402b-9955-54fbecf66592a-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Spazio di archiviazione condiviso
La figura 1 illustra anche un cluster di archiviazione condiviso a due nodi. In un cluster di archiviazione condiviso locale tutti i nodi del cluster rilevano lo spazio di archiviazione condiviso. Un meccanismo di blocco protegge i dati da danneggiamenti. Tutti i nodi possono rilevare quando si verifica un errore in un altro nodo. Se si verifica un errore in un nodo, il nodo rimanente assume la proprietà delle risorse di archiviazione e assicura la disponibilità dei servizi.

> [!NOTE]
> Non sono necessari dischi condivisi per la disponibilità elevata con alcune applicazioni DBMS, ad esempio SQL Server. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. In tal caso, la configurazione del cluster Windows non richiede un disco condiviso.
>
>

### <a name="a-nameff7a9a06-2bc5-4b20-860a-46cdb44669cda-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Rete e risoluzione dei nomi
I computer client raggiungono il cluster tramite un indirizzo IP virtuale e un nome host virtuale forniti dal server DNS. I nodi locali e il server DNS possono gestire più indirizzi IP.

In un'installazione tipica si usano due o più connessioni di rete:

* Una connessione dedicata all'archiviazione
* Una connessione di rete interna al cluster per l'heartbeat
* Una rete pubblica usata dai client per la connessione al cluster

## <a name="a-name2ddba413-a7f5-4e4e-9a51-87908879c10aa-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server Failover Clustering in Azure
Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un Windows Server Failover Clustering in Macchine virtuali di Azure. Quando si crea un disco cluster condiviso, sarà necessario impostare diversi indirizzi IP e nomi host virtuali per l'istanza di SAP ASCS/SCS.

In questo articolo vengono illustrati i concetti chiave e i passaggi aggiuntivi necessari quando si crea un cluster di servizi centrale a disponibilità elevata SAP in Azure. Viene illustrato come configurare lo strumento SIOS DataKeeper di terze parti e come configurare il servizio di bilanciamento del carico interno di Azure. È possibile usare questi strumenti per creare un cluster di failover Windows con un controllo di condivisione file in Azure.

![Figura 2: Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso][sap-ha-guide-figure-1001]

***Figura 2:** Configurazione di Windows Server Failover Clustering in Azure senza disco condiviso*

### <a name="a-name1a464091-922b-48d7-9d08-7cecf757f341a-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco condiviso in Azure con SIOS DataKeeper
È necessario spazio di archiviazione condiviso del cluster per un'istanza di SAP ASCS/SCS a disponibilità elevata. A partire da settembre 2016, Azure non offre spazio di archiviazione condiviso da usare per creare un cluster di archiviazione condiviso. È possibile usare il software SIOS DataKeeper Cluster Edition di terze parti per creare una risorsa di archiviazione con mirroring che simula la risorsa di archiviazione condivisa del cluster. La soluzione SIOS fornisce la replica di dati sincrona in tempo reale. Per creare una risorsa disco condiviso per un cluster, seguire questa procedura:

1. Collegare un disco rigido virtuale di Azure aggiuntivo a ogni macchina virtuale in una configurazione cluster Windows.
2. Eseguire SIOS DataKeeper Cluster Edition in entrambi i nodi delle macchine virtuali.
3. Configurare SIOS DataKeeper Cluster Edition per eseguire il mirroring del contenuto del volume collegato del disco rigido virtuale aggiuntivo dalla macchina virtuale di origine al volume collegato del disco rigido virtuale aggiuntivo della macchina virtuale di destinazione. SIOS DataKeeper astrae i volumi locali di origine e di destinazione e quindi li presenta a Windows Server Failover Clustering come un disco condiviso.

Altre informazioni su [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/).

 ![Figura 3: Configurazione di Windows Server Failover Clustering in Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figura 3:** Configurazione di Windows Server Failover Clustering in Azure con SIOS DataKeeper*

> [!NOTE]
> Non sono necessari dischi condivisi per la disponibilità elevata con alcuni prodotti DBMS, ad esempio SQL Server. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. In questo caso, la configurazione del cluster Windows non richiede un disco condiviso.
>
>

### <a name="a-name44641e18-a94e-431f-95ff-303ab65e0bcba-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>Risoluzione dei nomi in Azure
 La piattaforma cloud Azure non consente di configurare indirizzi IP virtuali, ad esempio IP mobili. Per questo motivo, è necessaria una soluzione alternativa per configurare un indirizzo IP virtuale per poter raggiungere la risorsa cluster nel cloud.
Azure include un servizio di bilanciamento del carico interno nel servizio Azure Load Balancer. Con il servizio di bilanciamento del carico interno, i client raggiungono il cluster tramite l'indirizzo IP virtuale del cluster.
È necessario distribuire il servizio di bilanciamento del carico interni nel gruppo di risorse che contiene i nodi del cluster. Configurare quindi tutte le necessarie regole di port forwarding con le porte probe del servizio di bilanciamento del carico interno.
I client possono connettersi tramite il nome host virtuale. Il server DNS risolve l'indirizzo IP del cluster e il servizio di bilanciamento del carico interno gestisce il port forwarding al nodo attivo del cluster.

## <a name="a-name2e3fec50-241e-441b-8708-0b1864f66dfaa-high-availability-sap-netweaver-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> SAP NetWeaver a disponibilità elevata nell'infrastruttura distribuita come servizio (IaaS) in Azure
Per ottenere la disponibilità elevata per le applicazioni SAP, ad esempio per i componenti software SAP, è necessario proteggere i componenti seguenti. Questo argomento è illustrato più dettagliatamente in [SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione][planning-guide-11].

* Server applicazioni SAP
* Istanza di SAP ASCS/SCS
* Server DBMS

### <a name="a-name93faa747-907e-440a-b00a-1ae0a89b1c0ea-high-availability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Server applicazioni SAP a disponibilità elevata
In genere non è necessaria una specifica soluzione a disponibilità elevata per i server applicazioni SAP e le istanze delle finestre di dialogo. La disponibilità elevata si ottiene tramite la ridondanza e si dovranno configurare più istanze delle finestre di dialogo in istanze diverse di Macchine virtuali di Azure. È necessario avere almeno due istanze dell'applicazione SAP installate in due istanze di Macchine virtuali di Azure.

![Figura 4: Server applicazioni SAP a disponibilità elevata][sap-ha-guide-figure-2000]

***Figura 4:** Server applicazioni SAP a disponibilità elevata*

È necessario inserire tutte le macchine virtuali che ospitano i server applicazioni SAP nello stesso set di disponibilità di Azure. Un set di disponibilità di Azure assicura che:

* Tutte le macchine virtuali facciano parte dello stesso dominio di aggiornamento. Un dominio di aggiornamento, ad esempio, verifica che le macchine virtuali non vengano aggiornate contemporaneamente durante i tempi di inattività per la manutenzione pianificati.
* Tutte le macchine virtuali facciano parte dello stesso dominio di errore. Un dominio di errore, ad esempio, verifica che le macchine virtuali vengano distribuite in modo che nessun singolo punto di guasto influisca sulla disponibilità di tutte le macchine virtuali.

Sono disponibili informazioni su come [gestire la disponibilità delle macchine virtuali][virtual-machines-manage-availability].

Poiché l'account di archiviazione di Azure è un potenziale singolo punto di guasto, è importante avere almeno due account di archiviazione di Azure, in cui verranno distribuite almeno due macchine virtuali. In una configurazione ideale ogni macchina virtuale che esegue l'istanza di una finestra di dialogo SAP verrà distribuita in un account di archiviazione diverso.

### <a name="a-namef559c285-ee68-4eec-add1-f60fe7b978dba-high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Istanza di SAP ASCS/SCS a disponibilità elevata
![Figura 5: Istanza di SAP ASCS/SCS a disponibilità elevata][sap-ha-guide-figure-2001]

***Figura 5:** Istanza di SAP ASCS/SCS a disponibilità elevata*

#### <a name="a-nameb5b1fd0b-1db4-4d49-9162-de07a0132a51a-high-availability-sap-ascsscs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Istanza di SAP ASCS/SCS a disponibilità elevata con Windows Server Failover Clustering in Azure
Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un Windows Server Failover Clustering in Macchine virtuali di Azure. Per creare un cluster di failover Windows, sono necessari un disco cluster condiviso, più indirizzi IP, più nomi host virtuali e un servizio di bilanciamento del carico interno di Azure per il clustering di un'istanza di SAP ASCS/SCS.

Questo argomento viene illustrato più dettagliatamente più avanti nell'articolo.

![Figura 6: Windows Server Failover Clustering per una configurazione SAP ASCS/SCS in Azure con SIOS DataKeeper][sap-ha-guide-figure-1002]

***Figura 6:** Windows Server Failover Clustering per una configurazione SAP ASCS/SCS in Azure con SIOS DataKeeper*

### <a name="a-nameddd878a0-9c2f-4b8e-8968-26ce60be1027a-high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Istanza di DBMS a disponibilità elevata
DBMS è anche un singolo punto di contatto di un sistema SAP. È necessario proteggerlo usando una soluzione a disponibilità elevata. La figura 7 illustra un esempio di soluzione a disponibilità elevata SQL Server AlwaysOn in Azure usando Windows Server Failover Clustering e il servizio di bilanciamento del carico interno di Azure. SQL Server AlwaysOn replica i file di dati e di log DBMS usando la replica DBMS. In questo caso, non sono necessari dischi condivisi cluster, semplificando così l'intera impostazione.

![Figura 7: Esempio di SAP DBMS a disponibilità elevata: SQL Server AlwaysOn][sap-ha-guide-figure-2003]

***Figura 7:** Esempio di SAP DBMS a disponibilità elevata: SQL Server AlwaysOn*

Per altre informazioni sul clustering di SQL Server in Azure con il modello di distribuzione Azure Resource Manager, vedere questi articoli:

* [Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="a-name045252ed-0277-4fc8-8f46-c5a29694a816a-end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Scenari di distribuzione a disponibilità elevata end-to-end
La figura 8 illustra un esempio di architettura a disponibilità elevata SAP NetWeaver in Azure. In questo scenario vengono usati un cluster dedicato per l'istanza di SAP ASCS/SCS e un altro per DBMS.

![Figura 8: Modello di architettura a disponibilità elevata per SAP 1, con un cluster dedicato per ASCS/SCS e un cluster dedicato per l'istanza di DBMS][sap-ha-guide-figure-2004]

***Figura 8:** Modello di architettura a disponibilità elevata per SAP 1: cluster dedicati per ASCS/SCS e DBMS*

## <a name="a-name78092dbe-165b-454c-92f5-4972bdbef9bfa-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparare l'infrastruttura
I modelli di Azure Resource Manager per SAP consentono di semplificare la distribuzione delle risorse necessarie.

I modelli a tre livelli supportano anche scenari a disponibilità elevata, ad esempio il modello di architettura 1, che ha due cluster. Ogni cluster è un singolo punto di guasto SAP per SAP ASCS/SCS e DBMS.

Ecco dove è possibile ottenere i modelli di Azure Resource Manager per lo scenario 1:

* [Immagine di Azure Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Immagine personalizzata](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Quando si seleziona l'immagine del Marketplace a tre livelli SAP, nel portale di Azure viene visualizzata questa schermata:

![Figura 9: Specificare i parametri di Azure Resource Manager di disponibilità elevata per SAP][sap-ha-guide-figure-3000]

***Figura 9:** Specificare i parametri di Azure Resource Manager di disponibilità elevata per SAP*

In **SYSTEMAVAILABILITY** selezionare **HA**.

I modelli creano:

* **Macchine virtuali**:
  * Macchine virtuali del server applicazioni SAP: <*SIDSistemaSAP*>-di-<*Numero*>
  * Macchine virtuali del cluster ASCS/SCS: <*SIDSistemaSAP*>-ascs-<*Numero*>
  * Un cluster DBMS: <*SIDSistemaSAP*>-db-<*Numero*>
* **Schede di rete per tutte le macchine virtuali, con gli indirizzi IP associati**:
  * <*SIDSistemaSAP*>-nic-di-<*Numero*>
  * <*SIDSistemaSAP*>-nic-ascs-<*Numero*>
  * <*SIDSistemaSAP*>-nic-db-<*Numero*>
* **Account di archiviazione di Azure**
* **Gruppi di disponibilità** per:
  * Macchine virtuali del server applicazioni SAP: <*SIDSistemaSAP*>-avset-di
  * Macchine virtuali del cluster SAP ASCS/SCS: <*SIDSistemaSAP*>-avset-ascs
  * Macchine virtuali del cluster DBMS: <*SIDSistemaSAP*>-avset-db
* **Servizio di bilanciamento del carico interno di Azure**:
  * Con tutte le porte per l'istanza di ASCS/SCS e l'indirizzo IP <*SIDSistemaSAP*>-lb-ascs
  * Con tutte le porte per l'istanza di SQL Server DBMS e l'indirizzo IP <*SIDSistemaSAP*>-lb-db
* **Gruppo di sicurezza di rete**: <*SIDSistemaSAP*>-nsg-ascs-0  
  * Con una porta Remote Desktop Protocol (RDP) esterna aperta alla macchina virtuale <*SIDSistemaSAP*>-ascs-0

> [!NOTE]
> Tutti gli indirizzi IP delle schede di rete e i servizi di bilanciamento del carico interno di Azure sono **dinamici** per impostazione predefinita. Impostarli come indirizzi IP **statici**, come verrà illustrato più avanti nell'articolo.
>
>

### <a name="a-namec87a8d3f-b1dc-4d2f-b23c-da4b72977489a-deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuire macchine virtuali con connettività di rete aziendale (cross-premise) da usare in fase di produzione
Per i sistemi SAP di produzione, distribuire le macchine virtuali di Azure con la [connettività di rete aziendale (cross-premise)][planning-guide-2.2] usando la rete VPN da sito a sito di Azure o Azure ExpressRoute.

> [!NOTE]
> È possibile usare l'istanza di Rete virtuale di Azure. La rete virtuale e la subnet sono già state create e preparate.
>
>

In **NEWOREXISTINGSUBNET**, selezionare **existing**.

In **SUBNETID** aggiungere la stringa completa del campo SubnetID relativo alla rete di Azure preparata, in cui si prevede di distribuire le macchine virtuali VM di Azure.

Eseguire questo comando di PowerShell per ottenere un elenco di tutte le subnet di rete di Azure:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

Il campo **ID** contiene il valore di **SUBNETID**.

Per recuperare un elenco di tutti i valori di **SUBNETID**, usare questo comando di PowerShell:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

Il valore di **SUBNETID** è il seguente:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="a-name7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310a-cloud-only-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuzione solo cloud delle istanze di SAP a fini di test e demo
È anche possibile distribuire il sistema SAP a disponibilità elevata in un modello di distribuzione solo cloud.

Questo tipo di distribuzione è utile soprattutto per i casi d'uso di demo o test. Non è adatto per i casi d'uso in un ambiente di produzione.

In **NEWOREXISTINGSUBNET**, selezionare **new**. Lasciare vuoto il campo **SUBNETID**.

Il modello di Azure Resource Manager per SAP crea automaticamente la rete virtuale di Azure e la subnet.

> [!NOTE]
> È anche necessario distribuire almeno una macchina virtuale dedicata per Active Directory e DNS nella stessa istanza di Rete virtuale di Azure. Il modello non crea queste macchine virtuali.
>
>

### <a name="a-name47d5300a-a830-41d4-83dd-1a0d1ffdbe6aa-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rete virtuale di Azure
In questo esempio lo spazio degli indirizzi della rete virtuale di Azure è 10.0.0.0/16. Esiste una subnet denominata **Subnet**, con un intervallo di indirizzi di 10.0.0.0/24. Tutte le macchine virtuali e i servizi di bilanciamento del carico interni vengono distribuiti in questa rete virtuale.

> [!NOTE]
> Non apportare modifiche alle impostazioni di rete nel sistema operativo guest, inclusi indirizzi IP, server DNS e la subnet. Configurare tutte le impostazioni di rete in Azure. Il servizio Dynamic Host Configuration Protocol (DHCP) propaga le impostazioni.
>
>

### <a name="a-nameb22d7b3b-4343-40ff-a319-097e13f62f9ea-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Indirizzi IP DNS
Verificare che l'opzione **Server DNS** della rete virtuale sia impostata su **DNS personalizzato**.
Selezionare quindi le impostazioni in base al tipo di rete:

* [Connettività di rete aziendale (cross-premise)][planning-guide-2.2]: aggiungere gli indirizzi IP dei server DNS locali.  

    È possibile estendere i server DNS locali alle macchine virtuali in esecuzione in Azure. In tale scenario è possibile aggiungere gli indirizzi IP delle macchine virtuali di Azure in cui si esegue il servizio DNS.
* [Distribuzione solo cloud][planning-guide-2.1]: distribuire una macchina virtuale aggiuntiva nella stessa istanza di Rete virtuale che funge da server DNS. Aggiungere gli indirizzi IP delle macchine virtuali di Azure configurate per l'esecuzione del servizio DNS.

![Figura 10: Configurare i server DNS per Rete virtuale di Azure][sap-ha-guide-figure-3001]

***Figura 10:** Configurare i server DNS per Rete virtuale di Azure*

> [!NOTE]
> Se si modificano gli indirizzi IP dei server DNS, è necessario riavviare le macchine virtuali di Azure per applicare la modifica e propagare i nuovi server DNS.
>
>

Nell'esempio il servizio DNS viene installato e configurato in queste macchine virtuali Windows:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Primo server DNS |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Secondo server DNS |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="a-name9fbd43c0-5850-4965-9726-2a921d85d73fa-host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomi host e indirizzi IP statici per l'istanza in cluster di SAP ASCS/SCS e l'istanza in cluster di DBMS
Per una distribuzione locale, sono necessari questi nomi host e indirizzi IP riservati:

| Ruolo nome host virtuale | Nome host virtuale | Indirizzo IP statico virtuale |
| --- | --- | --- |
| Primo nome host virtuale del cluster SAP ASCS/SCS (per la gestione del cluster) |pr1-ascs-vir |10.0.0.42 |
| Nome host virtuale dell'istanza di SAP ASCS/SCS |pr1-ascs-sap |10.0.0.43 |
| Secondo nome host virtuale del cluster SAP DBMS (gestione del cluster) |pr1-dbms-vir |10.0.0.32 |

Quando si crea il cluster, creare i nomi host virtuale **pr1-ascs-vir** e **pr1-dbms-vir** e gli indirizzi IP associati che gestiscono il cluster stesso. Il processo è descritto in [Raccogliere i nodi del cluster in una configurazione cluster][sap-ha-guide-8.12.1].

È possibile creare manualmente gli altri due nomi host virtuale, **pr1-ascs-sap** e **pr1-dbms-sap** e gli indirizzi IP associati, nel server DNS. L'istanza di SAP ASCS/SCS in cluster e l'istanza di DBMS in cluster usano queste risorse, come illustrato in [Creare un nome host virtuale per SAP ASCS/SCS in cluster][sap-ha-guide-9.1.1].

### <a name="a-name84c019fe-8c58-4dac-9e54-173efd4b2c30a-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Impostare gli indirizzi IP statici per le macchine virtuali SAP
Dopo avere distribuito le macchine virtuali da usare nel cluster, è necessario impostare gli indirizzi IP statici per tutte le macchine virtuali. Eseguire questa operazione nella configurazione di Rete virtuale di Azure e non nel sistema operativo guest.

Per impostare un indirizzo IP statico, è possibile usare il portale di Azure. Nel portale di Azure andare a **Gruppo di risorse** > **Scheda di rete** > **Impostazioni** > **Indirizzo IP**.

In **Assegnazione** selezionare **Statico**. Nel campo **Indirizzo IP** immettere l'indirizzo IP che si vuole usare.

> [!NOTE]
> Se si modifica l'indirizzo IP della scheda di rete, è necessario riavviare le macchine virtuali di Azure per applicare la modifica.  
>
>

![Figura 11: Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale][sap-ha-guide-figure-3002]

***Figura 11:** Impostare gli indirizzi IP statici per la scheda di rete di ogni macchina virtuale*

Ripetere questo passaggio per tutte le interfacce di rete, ovvero per tutte le macchine virtuali, incluse le macchine virtuali che si vuole usare per il servizio Active Directory/DNS.

In questo esempio vengono usate le macchine virtuali e gli indirizzi IP statici seguenti:

| Ruolo macchina virtuale | Nome host macchina virtuale | Nome scheda di rete | Indirizzo IP statico |
| --- | --- | --- | --- |
| Primo server applicazioni SAP |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Secondo server applicazioni SAP |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Ultimo server applicazioni SAP |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Primo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Secondo nodo del cluster per l'istanza di ASCS/SCS |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Primo nodo del cluster per l'istanza di DBMS |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Secondo nodo del cluster per l'istanza di DBMS |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="a-name7a8f3e9b-0624-4051-9e41-b73fff816a9ea-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Impostare un indirizzo IP statico per il servizio di bilanciamento del carico interno
Il modello di Azure Resource Manager per SAP crea un servizio di bilanciamento del carico interno di Azure usato per il cluster dell'istanza di SAP ASCS/SCS e per il cluster DBMS.

La distribuzione iniziale imposta l'indirizzo IP del servizio di bilanciamento del carico interno come **dinamico**. È importante modificare l'indirizzo IP in **statico**.

Nell'esempio sono presenti due servizi di bilanciamento del carico interno di Azure con questi indirizzi IP statici:

| Ruolo del servizio di bilanciamento del carico interno di Azure | Nome del servizio di bilanciamento del carico interno di Azure | Indirizzo IP statico |
| --- | --- | --- |
| Servizio di bilanciamento del carico interno dell'istanza di SAP ASCS/SCS |pr1-lb-ascs |10.0.0.43 |
| Servizio di bilanciamento del carico interno di SAP DBMS |pr1-lb-dbms |10.0.0.33 |

> [!NOTE]
> L'indirizzo IP del nome host virtuale di SAP ASCS/SCS è lo stesso del servizio di bilanciamento del carico interno di SAP ASCS/SCS pr1-lb-ascs.
> L'indirizzo IP del nome virtuale di DBMS è lo stesso del servizio di bilanciamento del carico interno di DBMS pr1-lb-dbms.
>
>

Nell'esempio l'indirizzo IP del servizio di bilanciamento del carico interno **pr1-lb-ascs** viene impostato sull'indirizzo IP del nome host virtuale dell'istanza di SAP ASCS/SCS (nell'esempio, **10.0.0.43**).

![Figura 12: Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS][sap-ha-guide-figure-3003]

***Figura 12:** Impostare indirizzi IP statici per il servizio di bilanciamento del carico interno per l'istanza di SAP ASCS/SCS*

Impostare l'indirizzo IP del servizio di bilanciamento del carico interno **pr1-lb-dbms** sull'indirizzo IP del nome host virtuale dell'istanza di DBMS (nell'esempio **10.0.0.33**).

### <a name="a-namef19bd997-154d-4583-a46e-7f5a69d0153ca-default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure
Il modello di Azure Resource Manager per SAP crea le porte necessarie:

* Un'istanza di ABAP ASCS, con il numero di istanza predefinito **00**
* Un'istanza di Java SCS, con il numero di istanza predefinito **01**

Quando si installa l'istanza di SAP ASCS/SCS, è necessario usare il numero di istanza predefinito 00 per l'istanza di ABAP ASCS e il numero di istanza predefinito 01 per l'istanza di Java SCS.

Creare quindi questi endpoint di bilanciamento del carico interno obbligatori per le porte di SAP NetWeaver ABAP ASCS:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza ASCS con l'istanza numero 00) (ERS con 10) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3200* |32<*NumeroIstanza*> |3200 |
| Server messaggi ABAP/ *lbrule3600* |36<*NumeroIstanza*> |3600 |
| Messaggio ABAP interno/ *lbrule3900* |39<*NumeroIstanza*> |3900 |
| HTTP server messaggi/ *Lbrule8100* |81<*NumeroIstanza*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*NumeroIstanza*>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*NumeroIstanza*>14 |50014 |
| Replica di accodamento/ *Lbrule50016* |5<*NumeroIstanza*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*NumeroIstanza*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*NumeroIstanza*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

***Tabella 1:** Numeri di porta delle istanze di SAP NetWeaver ABAP ASCS*

Creare quindi questi endpoint di bilanciamento del carico interno obbligatori per le porte di SAP NetWeaver Java SCS:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza SCS con numero di istanza 01) (ERS con 11) |
| --- | --- | --- |
| Server di accodamento/ *lbrule3201* |32<*NumeroIstanza*> |3201 |
| Server gateway/ *lbrule3301* |33<*NumeroIstanza*> |3301 |
| Server messaggi Java/ *lbrule3900* |39<*NumeroIstanza*> |3901 |
| HTTP server messaggi/ *Lbrule8101* |81<*NumeroIstanza*> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5<*NumeroIstanza*>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*NumeroIstanza*>14 |50114 |
| Replica di accodamento/ *Lbrule50116* |5<*NumeroIstanza*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*NumeroIstanza*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*NumeroIstanza*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Condivisione file *Lbrule445* | |445 |

***Tabella 2:** Numeri di porta delle istanze di SAP NetWeaver Java SCS*

![Figura 13: Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3004]

***Figura 13:** Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure*

Impostare l'indirizzo IP del servizio di bilanciamento del carico **pr1-lb-dbms** sull'indirizzo IP del nome host virtuale dell'istanza di DBMS (nell'esempio **10.0.0.33**).

### <a name="a-namefe0bd8b5-2b43-45e3-8295-80bee5415716a-change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure
Per usare numeri diversi per le istanze di SAP ASCS o SCS, è necessario aggiornare i nomi e i valori di queste porte.

Per aggiornare i numeri di istanza, è possibile usare il portale di Azure:

Andare al **servizio di bilanciamento del carico <*SID*>-lb-ascs** > ****.

Per tutte le regole di bilanciamento del carico appartenenti all'istanza di SAP ASCS o SCS, modificare questi valori:

* Nome
* Porta
* Porta back-end

Ad esempio, per sostituire il numero di istanza di ASCS predefinito 00 con 31, è necessario apportare le modifiche per tutte le porte elencate nella tabella 1.

Ecco un esempio di aggiornamento per la porta *lbrule3200*.

![Figura 14: Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-figure-3005]

***Figura 14:** Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure*

### <a name="a-namee69e9a34-4601-47a3-a41c-d2e11c626c0ca-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Aggiungere macchine virtuali Windows al dominio
Dopo avere assegnato un indirizzo IP statico alle macchine virtuali, aggiungere le macchine virtuali al dominio.

![Figura 15: Aggiungere una macchina virtuale a un dominio][sap-ha-guide-figure-3006]

***Figura 15:** Aggiungere una macchina virtuale a un dominio*

### <a name="a-name661035b2-4d0f-4d31-86f8-dc0a50d78158a-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster per l'istanza di SAP ASCS/SCS
Azure Load Balancer ha un servizio di bilanciamento del carico interno che chiude le connessioni quando rimangono inattive per un periodo di tempo impostato (timeout di inattività). I processi di lavoro SAP nelle istanze delle finestre di dialogo aprono le connessioni al processo di accodamento SAP non appena deve essere inviata la prima richiesta di accodamento/rimozione dalla coda. Queste connessioni restano in genere stabilite fino al riavvio del processo di lavoro o del processo di accodamento. Se tuttavia la connessione rimane inattiva per un determinato periodo di tempo, il servizio di bilanciamento del carico interno di Azure chiude le connessioni. Questo non è un problema perché il processo di lavoro SAP ristabilisce la connessione al processo di accodamento se non esiste più. Queste attività sono documentate nelle tracce degli sviluppatori dei processi SAP, ma creano una grande quantità di contenuti aggiuntivi in tali tracce. È consigliabile modificare i parametri `KeepAliveTime` e `KeepAliveInterval` TCP/IP in entrambi i nodi del cluster. Combinare queste modifiche nei parametri TCP/IP con i parametri del profilo SAP, illustrati più avanti nell'articolo.

Aggiungere le voci del Registro di sistema Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| Path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveTime` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

***Tabella 3:** Modificare il primo parametro TCP/IP*

| Path | HKLM\System\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Nome variabile |`KeepAliveInterval` |
| Tipo di variabile |REG_DWORD (decimale) |
| Valore |120000 |
| Collegamento alla documentazione |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

***Tabella 4:** Modificare il secondo parametro TCP/IP*

Per applicare le modifiche, riavviare entrambi i nodi del cluster.

### <a name="a-name0d67f090-7928-43e0-8772-5ccbf8f59aaba-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurare un cluster di Windows Server Failover Clustering per un'istanza di SAP ASCS/SCS
#### <a name="a-name5eecb071-c703-4ccc-ba6d-fe9c6ded9d79a-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Raccogliere i nodi del cluster in una configurazione cluster
Il primo passaggio consiste nell'aggiungere il clustering del failover a entrambi i nodi del cluster. Usare l'Aggiunta guidata ruoli e funzionalità.

Il secondo passaggio consiste nel configurare il cluster di failover usando Gestione cluster di failover.

In Gestione cluster di failover selezionare **Crea cluster** e quindi aggiungere solo il nome del primo nodo A del cluster. Ad esempio, aggiungere **pr1-ascs-0**. Non aggiungere ancora il secondo nodo. Il secondo nodo verrà aggiunto in un passaggio successivo.

![Figura 16: Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster][sap-ha-guide-figure-3007]

***Figura 16:** Aggiungere il nome del server o della macchina virtuale del primo nodo del cluster*

Viene quindi chiesto il nome della rete (nome host virtuale) del cluster.

![Figura 17: Definire il nome del cluster][sap-ha-guide-figure-3008]

***Figura 17:** Definire il nome del cluster*

Dopo avere creato il cluster, eseguire un test di convalida del cluster.

![Figura 18: Eseguire il controllo di convalida del cluster][sap-ha-guide-figure-3009]

***Figura 18:** Eseguire il controllo di convalida del cluster*

![Figura 19: Nessun disco quorum trovato][sap-ha-guide-figure-3010]

***Figura 19:** Nessun disco quorum trovato*

A questo punto del processo è possibile ignorare gli avvisi sui dischi. Più avanti si aggiungeranno un controllo di condivisione file e i dischi condivisi SIOS. In questa fase, non è necessario un quorum.

![Figura 20: La risorsa del cluster principale richiede un nuovo indirizzo IP][sap-ha-guide-figure-3011]

***Figura 20:** La risorsa del cluster principale richiede un nuovo indirizzo IP*

Il cluster non può essere avviato perché l'indirizzo IP del server fa riferimento a uno dei nodi delle macchine virtuali. È necessario modificare l'indirizzo IP del servizio cluster principale.

Ad esempio, è necessario assegnare un indirizzo IP (in questo esempio **10.0.0.42**) per il nome host virtuale del cluster **pr1-ascs-vir**. Eseguire questa operazione nella pagina delle proprietà della risorsa IP del servizio cluster principale, illustrata nella figura 21.

![Figura 21: Nella finestra di dialogo **Proprietà** modificare l'indirizzo IP][sap-ha-guide-figure-3012]

***Figura 21:** Nella finestra di dialogo **Proprietà** modificare l'indirizzo IP*

![Figura 22: Assegnare l'indirizzo IP riservato per il cluster][sap-ha-guide-figure-3013]

***Figura 22:** Assegnare l'indirizzo IP riservato per il cluster*

Dopo avere modificato l'indirizzo IP, portare online il nome host virtuale del cluster.

![Figura 23: Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto][sap-ha-guide-figure-3014]

***Figura 23:** Il servizio principale del cluster è in esecuzione con l'indirizzo IP corretto*

Ora che il servizio principale del cluster è attivo e in esecuzione, è possibile aggiungere il secondo nodo del cluster.

![Figura 24: Aggiungere il secondo nodo del cluster][sap-ha-guide-figure-3015]

***Figura 24:** Aggiungere il secondo nodo del cluster*

![Figura 25: Aggiungere il nome host del secondo nodo del cluster, ad esempio pr1-ascs-1][sap-ha-guide-figure-3016]

***Figura 25:** Aggiungere il nome host del secondo nodo del cluster, ad esempio **pr1-ascs-1***

![Figura 26: Non selezionare la casella di controllo][sap-ha-guide-figure-3017]

***Figura 26:** *Non* selezionare la casella di controllo*

> [!IMPORTANT]
> Assicurarsi che la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster** *non* sia selezionata.  
>
>

![Figura 27: Ignorare gli avvisi sul quorum del disco][sap-ha-guide-figure-3018]

***Figura 27:** Ignorare gli avvisi sul quorum del disco*

È possibile ignorare gli avvisi relativi al quorum e ai dischi. Si imposterà il quorum e si condividerà il disco in un secondo momento, come descritto in [Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS][sap-ha-guide-8.12.3].

#### <a name="a-namee49a4529-50c9-4dcf-bde7-15a0c21d21caa-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurare il controllo di condivisione file del cluster
##### <a name="a-name06260b30-d697-4c4d-b1c9-d22c0bd64855a-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creare una condivisione file
Selezionare un controllo di condivisione file invece di un disco quorum. SIOS DataKeeper supporta questa opzione.

Negli esempi di questo articolo, il controllo di condivisione file è nel server Active Directory/DNS in esecuzione in Azure. Il controllo di condivisione file è denominato **domcontr-0**. Poiché sarà stata configurata una connessione di rete privata virtuale (VPN) ad Azure (tramite la VPN da sito a sito o Azure ExpressRoute), il servizio Active Directory/DNS è locale e non è adatto all'esecuzione di un controllo di condivisione file.

> [!NOTE]
> Se il servizio Active Directory/DNS viene eseguito solo in locale, non configurare il controllo di condivisione file nel sistema operativo Windows con Active Directory/DNS eseguito in locale. La latenza di rete tra i nodi del cluster in esecuzione in Azure e Active Directory/DNS in locale potrebbe essere eccessiva e causare problemi di connettività. Assicurarsi di configurare il controllo di condivisione file in una macchina virtuale di Azure in esecuzione vicino al nodo del cluster.  
>
>

L'unità del quorum richiede almeno 1.024 MB di spazio disponibile. Sono consigliabili 2.048 MB di spazio disponibile.

Il primo passaggio consiste nell'aggiungere l'oggetto nome cluster.

![Figura 28: Assegnare le autorizzazioni nella condivisione per l'oggetto nome cluster][sap-ha-guide-figure-3019]

***Figura 28:** Assegnare le autorizzazioni nella condivisione per l'oggetto nome cluster*

Assicurarsi che le autorizzazioni includano l'autorità di modificare i dati nella condivisione per l'oggetto nome cluster (in questo esempio **pr1-ascs-vir$**). Per aggiungere l'oggetto nome cluster all'elenco, selezionare **Aggiungi**. Modificare il filtro per cercare gli oggetti computer, oltre a quelli illustrati nella figura 29:

![Figura 29: Modificare il tipo di oggetto per includere gli oggetti computer][sap-ha-guide-figure-3020]

***Figura 29:** Modificare il tipo di oggetto per includere gli oggetti computer*

![Figura 30: Selezionare la casella di controllo per gli oggetti computer][sap-ha-guide-figure-3021]

***Figura 30:** Selezionare la casella di controllo per gli oggetti computer*

Immettere quindi l'oggetto nome del cluster, come illustrato nella figura 29. Poiché il record è già stato creato, è possibile modificare le autorizzazioni, come illustrato nella figura 28.

Selezionare poi la scheda **Sicurezza** della condivisione e quindi impostare autorizzazioni più dettagliate per l'oggetto nome cluster.

![Figura 31: Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file][sap-ha-guide-figure-3022]

***Figura 31:** Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file*

##### <a name="a-name4c08c387-78a0-46b1-9d27-b497b08cac3da-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Impostare il quorum del controllo di condivisione file in Gestione cluster di failover
In Gestione cluster di failover modificare la configurazione del cluster in un controllo di condivisione file.

![Figura 32: Avviare la procedura guidata Configura impostazioni quorum del cluster][sap-ha-guide-figure-3023]

***Figura 32:** Avviare la procedura guidata Configura impostazioni quorum del cluster*

![Figura 33: Configurazioni del quorum tra cui è possibile scegliere][sap-ha-guide-figure-3024]

***Figura 33:** Configurazioni del quorum tra cui è possibile scegliere*

Selezionare **Seleziona il quorum di controllo**.

![Figura 34: Selezionare il controllo di condivisione file][sap-ha-guide-figure-3025]

***Figura 34:** Selezionare il controllo di condivisione file*

Selezionare **Configura controllo di condivisione file**.

![Figura 35: Definire il percorso della condivisione file per la condivisione di controllo][sap-ha-guide-figure-3026]

***Figura 35:** Definire il percorso della condivisione file per la condivisione di controllo*

Immettere il percorso UNC della condivisione file (in questo esempio \\domcontr-0\FSW) .

Selezionare **Avanti** per visualizzare l'elenco delle modifiche che è possibile apportare. Selezionare le modifiche desiderate e quindi selezionare **Avanti**.

![Figura 36: Conferma della riconfigurazione del cluster][sap-ha-guide-figure-3027]

***Figura 36:** Conferma della riconfigurazione del cluster*

In quest'ultimo passaggio è necessario riconfigurare correttamente la configurazione del cluster, come illustrato nella figura 36.  

### <a name="a-name5c8e5482-841e-45e1-a89d-a05c0907c868a-install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installare SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS
Ora è disponibile una configurazione funzionante di Windows Server Failover Clustering in Azure, ma, per installare un'istanza di SAP ASCS/SCS, è necessaria una risorsa disco condiviso. Non è possibile creare le risorse disco condiviso necessarie in Azure. SIOS DataKeeper Cluster Edition è una soluzione di terze parti che è possibile usare per creare le risorse disco condiviso.

#### <a name="a-name1c2788c3-3648-4e82-9e0d-e058e475e2a3a-add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Aggiungere .NET Framework 3.5
Microsoft .NET Framework 3.5 non viene attivato né installato automaticamente in Windows Server 2012 R2, ma con SIOS DataKeeper .NET Framework deve essere in tutti i nodi in cui si installa DataKeeper. Per questo motivo, è necessario installare .NET Framework 3.5 nel sistema operativo guest di tutte le macchine virtuali nel cluster.

È possibile aggiungere .NET Framework 3.5 in due modi. Il primo prevede l'uso dell'Aggiunta guidata ruoli e funzionalità in Windows, illustrata nella figura 37.

![Figura 37: Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3028]

***Figura 37:** Installare .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità*

![Figura 38: Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 usando l'Aggiunta guidata ruoli e funzionalità][sap-ha-guide-figure-3029]

***Figura 38:** Indicatore di stato dell'installazione quando si installa .NET Framework 3.5 con l'Aggiunta guidata ruoli e funzionalità*

La seconda opzione per attivare la funzionalità .NET Framework 3.5 prevede l'uso dello strumento da riga di comando dism.exe. Per questo tipo di installazione è necessario accedere alla directory SxS nei supporti di installazione di Windows. Eseguire questo comando a un prompt dei comandi con privilegi elevati:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="a-namedd41d5a2-8083-415b-9878-839652812102a-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installare SIOS DataKeeper
Installare SIOS DataKeeper Cluster Edition in ogni nodo del cluster. Con SIOS DataKeeper, per creare una risorsa di archiviazione condivisa virtuale, creare un mirror sincronizzato e quindi simulare la risorsa di archiviazione condivisa del cluster.

Prima di installare il software SIOS, creare l'utente di dominio **DataKeeperSvc**.

> [!NOTE]
> Aggiungere l'utente **DataKeeperSvc** al gruppo **Administrators locale** in entrambi i nodi del cluster.
>
>

Installare il software SIOS in entrambi i nodi del cluster.

![Programma di installazione SIOS][sap-ha-guide-figure-3030]

![Figura 39: Prima schermata dell'installazione di SIOS DataKeeper][sap-ha-guide-figure-3031]

***Figura 39:** Prima schermata dell'installazione di SIOS DataKeeper*

![Figura 40: DataKeeper segnala che un servizio verrà disabilitato][sap-ha-guide-figure-3032]

***Figura 40:** DataKeeper segnala che un servizio verrà disabilitato*

Nella finestra di dialogo illustrata nella figura 40, selezionare **Sì**.

![Figura 41: Selezione dell'utente per SIOS DataKeeper][sap-ha-guide-figure-3033]

***Figura 41:** Selezione dell'utente per SIOS DataKeeper*

Nella schermata illustrata nella figura 41, si consiglia di selezionare **Domain or Server account** (Account di dominio o server).

![Figura 42: Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper][sap-ha-guide-figure-3034]

***Figura 42:** Specificare il nome utente di dominio e la password per l'installazione di SIOS DataKeeper*

Specificare il nome utente dell'account di dominio e le password creati per SIOS DataKeeper.

![Figura 43: Specificare la licenza di SIOS DataKeeper][sap-ha-guide-figure-3035]

***Figura 43**: Specificare la licenza di SIOS DataKeeper*

Installare la chiave di licenza per l'istanza di SIOS DataKeeper, come illustrato nella figura 43. Al termine dell'installazione verrà chiesto di riavviare la macchina virtuale.

#### <a name="a-named9c1fc8e-8710-4dff-bec2-1f535db7b006a-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Configurare SIOS DataKeeper
Dopo l'installazione di SIOS DataKeeper su entrambi i nodi è necessario avviare la configurazione. L'obiettivo della configurazione è eseguire la replica di dati sincrona tra i dischi rigidi virtuali aggiuntivi collegati a ogni macchina virtuale. Di seguito sono illustrati i passaggi per la configurazione di entrambi i nodi.

![Figura 44: Strumento di configurazione e gestione di SIOS DataKeeper][sap-ha-guide-figure-3036]

***Figura 44:** Strumento di configurazione e gestione di SIOS DataKeeper*

Avviare lo strumento di configurazione e gestione di DataKeeper e selezionare il collegamento **Connect Server** (Connetti server). Nella figura 44, questa opzione è racchiusa in un cerchio rosso.

![Figura 45: Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di gestione e configurazione; eseguire l'operazione in un altro passaggio per il secondo nodo][sap-ha-guide-figure-3037]

***Figura 45:** Inserire il nome o l'indirizzo TCP/IP del primo nodo cui deve connettersi lo strumento di gestione e configurazione; eseguire l'operazione in un altro passaggio per il secondo nodo*

Il passaggio successivo consiste nel creare il processo di replica tra i due nodi.

![Figura 46: Creare un processo di replica][sap-ha-guide-figure-3038]

***Figura 46:** Creare un processo di replica*

Per la creazione di un processo di replica è disponibile una procedura guidata.

![Figura 47: Definire il nome del processo di replica][sap-ha-guide-figure-3039]

***Figura 47:** Definire il nome del processo di replica*

![Figura 48: Definire i dati di base per il nodo che deve essere il nodo di origine corrente][sap-ha-guide-figure-3040]

***Figura 48:** Definire i dati di base per il nodo che deve essere il nodo di origine corrente*

Nel primo passaggio è necessario definire il nome, l'indirizzo TCP/IP e il volume del disco del nodo di origine. Il secondo passaggio consiste nel definire il nodo di destinazione. Come spiegato in precedenza, è necessario definire il nome, l'indirizzo TCP/IP e il volume del disco del nodo di origine.

![Figura 49: Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente][sap-ha-guide-figure-3041]

***Figura 49:** Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente*

Definire quindi gli algoritmi di compressione. Nell'esempio è consigliabile comprimere il flusso di replica. Soprattutto in caso di risincronizzazione, la compressione del flusso di replica riduce notevolmente il tempo necessario per l'operazione. Si noti che la compressione usa le risorse di CPU e RAM di una macchina virtuale. Con l'aumentare del tasso di compressione aumenta anche il volume delle risorse di CPU usate. È possibile modificare questa impostazione in un secondo momento.

Un'altra impostazione da verificare è se la replica viene eseguita in modalità sincrona o asincrona. *Per proteggere le configurazioni di SAP ASCS/SCS, è necessario usare la replica sincrona*.  

![Figura 50: Definire i dettagli della replica][sap-ha-guide-figure-3042]

***Figura 50:** Definire i dettagli della replica*

Il passaggio finale consiste nel definire se il volume replicato dal processo di replica deve essere rappresentato in una configurazione di cluster WSFC (Windows Server Failover Clustering) come disco condiviso. Per la configurazione di SAP ASCS/SCS è necessario scegliere **Yes** in modo che il cluster di Windows rilevi il volume replicato come disco condiviso che può essere usato come volume del cluster.

![Figura 51: Selezionare **Yes** per impostare il volume replicato come volume del cluster][sap-ha-guide-figure-3043]

***Figura 51:** Selezionare **Yes** per impostare il volume replicato come volume del cluster*

Dopo aver creato il volume, lo strumento di configurazione e gestione di DataKeeper mostra che il processo di replica è attivo.

![Figura 52: Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo][sap-ha-guide-figure-3044]

***Figura 52:** Il mirroring sincrono di DataKeeper per il disco condiviso di SAP ASCS/SCS è attivo*

Gestione cluster di failover visualizza ora il disco come disco di DataKeeper, come illustrato nella figura 53.

![Figura 53: Gestione cluster di failover visualizza il disco replicato da DataKeeper][sap-ha-guide-figure-3045]

***Figura 53:** Gestione cluster di failover visualizza il disco replicato da DataKeeper*

## <a name="a-namea06f0b49-8a7a-42bf-8b0d-c12026c5746ba-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installare il sistema SAP NetWeaver
La configurazione del sistema DBMS non viene descritta perché varia a seconda del sistema DBMS usato. Si presuppone tuttavia che i problemi di disponibilità elevata del sistema DBMS vengano risolti con le funzionalità supportate dai diversi fornitori di sistemi DBMS per Azure, ad esempio AlwaysOn o mirroring del database per SQL Server e Oracle Data Guard per Oracle. Nello scenario usato in questo articolo, non sono state aggiunte altre funzionalità di protezione per il sistema DBMS.

Non esistono particolari considerazioni per il caso in cui servizi DBMS differenti interagiscono con questa configurazione di SAP ASCS/SCS in cluster in Azure.

> [!NOTE]
> La procedura di installazione dei sistemi SAP NetWeaver ABAP, Java e ABAP + Java è praticamente identica. La differenza principale è che un sistema SAP ABAP ha un'istanza di ASCS. Il sistema SAP Java ha un'istanza di SCS. Il sistema SAP ABAP + Java ha un'istanza ASCS e un'istanza SCS in esecuzione nello stesso gruppo di cluster di failover Microsoft. Eventuali differenze di installazione per ogni stack di installazione di SAP NetWeaver verranno indicate in modo esplicito. Si presume che tutte le altre parti siano uguali.  
>
>

### <a name="a-name31c6bd4f-51df-4057-9fdf-3fcbc619c170a-install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installare SAP con un'istanza di ASCS/SCS a disponibilità elevata
> [!IMPORTANT]
> Non inserire il file di paging nei volumi con mirroring di DataKeeper. DataKeeper non supporta i volumi con mirroring. È possibile lasciare il file di paging nell'unità temporanea D di una macchina virtuale di Azure, ovvero l'impostazione predefinita. Se non è già presente, spostare il file di paging di Windows nell'unità D della macchina virtuale di Azure.
>
>

#### <a name="a-namea97ad604-9094-44fe-a364-f89cb39bf097a-create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Creare un nome host virtuale per l'istanza di SAP ASCS/SCS in cluster
In Gestore DNS di Windows creare una voce DNS per il nome host virtuale dell'istanza di ASCS/SCS. Definire quindi l'indirizzo IP assegnato al nome host virtuale.

> [!NOTE]
> L'indirizzo IP assegnato al nome host virtuale dell'istanza ASCS/SCS deve essere lo stesso indirizzo IP assegnato ad Azure Load Balancer (<*SID*>-lb-ascs).  
>
>

L'indirizzo IP del nome host virtuale di SAP ASCS/SCS (pr1-ascs-sap) è lo stesso indirizzo IP di Azure Load Balancer (pr1-lb-ascs).

È possibile eseguire un solo ruolo di cluster di failover SAP in un cluster di failover di Windows Server in Azure. Ciò significa, ad esempio, un'istanza ASCS per il sistema ABAP e un'istanza SCS per il sistema Java. Per ABAP + Java, si avrà un'istanza ASCS e un'istanza SCS.

> [!NOTE]
> Il clustering con più SID descritto nelle guide all'installazione di SAP (vedere le [guide all'installazione di SAP][sap-installation-guides]) attualmente non funziona in Azure.
>
>

![Figura 54: Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

***Figura 54:** Definire la voce DNS per il nome virtuale e l'indirizzo TCP/IP del cluster SAP ASCS/SCS*

La voce viene mostrata in Gestore DNS all'interno del dominio, come illustrato nella figura 55.

![Figura 55: Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster di SAP ASCS/SCS][sap-ha-guide-figure-3047]

***Figura 55:** Nuovo nome virtuale e indirizzo TCP/IP per la configurazione del cluster di SAP ASCS/SCS*

#### <a name="a-nameeb5af918-b42f-4803-bb50-eff41f84b0b0a-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installare il primo nodo del cluster SAP
Per installare il primo cluster SAP, eseguire l'opzione del primo nodo del cluster nel nodo A, ad esempio nell'host **pr1-ascs-0**.

Per mantenere le porte predefinite per il servizio di bilanciamento del carico interno di Azure, selezionare:

* Per il **sistema ABAP**: **ASCS** numero di istanza **00**
* Per il **sistema Java**: **SCS** numero di istanza **01**
* Per il **sistema ABAP + JAVA**: **ASCS** numero di istanza **00** e **SCS** numero di istanza **01**

Per usare altri numeri di istanza rispetto a 00 per l'istanza di ABAP ASCS e rispetto a 01 per l'istanza di Java SCS, è prima necessario modificare le regole predefinite del servizio di bilanciamento del carico interno di Azure, come descritto in [Modificare le regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno di Azure][sap-ha-guide-8.9].

Eseguire quindi alcuni passaggi non descritti nella documentazione di installazione generale di SAP.

> [!NOTE]
> La documentazione di installazione di SAP descrive come installare il primo nodo del cluster ASCS/SCS.
>
>

#### <a name="a-namee4caaab2-e90f-4f2c-bc84-2cd2e12a9556a-modify-the-sap-profile-of-the-ascsscs-ipowershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificare il profilo SAP dell'istanza di PowerShell di ASCS/SCS
È necessario aggiungere un nuovo parametro del profilo. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Lo scenario del problema è stato descritto nella sezione [Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster usati per l'istanza di SAP ASCS/SCS][sap-ha-guide-8.11] di questo articolo. In quella sezione sono anche illustrate due modifiche ad alcuni parametri della connessione TCP/IP di base. Nel secondo passaggio è necessario impostare il server di accodamento per l'invio di un segnale **keep_alive** per impedire alle connessioni di raggiungere la soglia di inattività del servizio di bilanciamento del carico interno di Azure.

Aggiungere questo parametro al profilo dell'istanza di SAP ASCS/SCS:

```
enque/encni/set_so_keepalive = true
```
Nell'esempio il percorso è:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

Ad esempio, al profilo dell'istanza di SAP SCS e al percorso corrispondente:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

#### <a name="a-name10822f4f-32e7-4871-b63a-9b86c76ce761a-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Aggiungere una porta probe
Usare la funzionalità probe del servizio di bilanciamento del carico interno per il corretto funzionamento della configurazione del cluster con Load Balancer. Il servizio di bilanciamento del carico interno di Azure distribuisce in genere il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali. Questa operazione non funziona tuttavia in alcune configurazioni di cluster perché è attiva una sola istanza. L'altra istanza è passiva e non può accettare carico di lavoro. La funzionalità probe è utile quando il servizio di bilanciamento del carico interno di Azure assegna il carico di lavoro solo a un'istanza attiva. Con la funzionalità probe, il servizio di bilanciamento del carico interno può rilevare l'istanza attiva e usare solo questa istanza come destinazione del carico di lavoro.

Verificare prima l'impostazione di **ProbePort** corrente con questo comando di PowerShell. Eseguire il comando all'interno di una delle macchine virtuali della configurazione del cluster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 56: La porta probe della configurazione del cluster è 0 per impostazione predefinita][sap-ha-guide-figure-3048]

***Figura 56:** La porta probe della configurazione del cluster è 0 per impostazione predefinita*

Definire quindi una porta probe. Il numero predefinito della porta probe è 0. In questo esempio viene usata la porta probe **62300**.

Il numero della porta è definito nei modelli di Azure Resource Manager per SAP. È possibile assegnare il numero della porta in PowerShell.

Ottenere prima la risorsa cluster con nome host virtuale **SAP WAC IP**.

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

Impostare quindi la porta probe su **62300**.

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

Per attivare le modifiche è necessario arrestare e avviare il ruolo cluster **SAP PR1**.

Dopo avere portato online il ruolo cluster **SAP PR1**, verificare che **ProbePort** sia impostato sul nuovo valore:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![Figura 57: Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore][sap-ha-guide-figure-3049]

***Figura 57:** Eseguire il probe della porta del cluster dopo aver impostato il nuovo valore*

**ProbePort** è impostato su **62300**. Ora è possibile accedere alla condivisione file **\\\ascsha-clsap\sapmnt** da altri host come **ascsha-dbas**.

### <a name="a-name85d78414-b21d-4097-92b6-34d8bcb724b7a-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installare l'istanza di database
Per installare l'istanza di database, seguire la procedura descritta nella documentazione sull'installazione di SAP.

### <a name="a-name8a276e16-f507-4071-b829-cdc0a4d36748a-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installare il secondo nodo del cluster
Per installare il secondo nodo del cluster, seguire i passaggi descritti nella guida all'installazione di SAP.

### <a name="a-name094bc895-31d4-4471-91cc-1513b64e406aa-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambiare il tipo di avvio dell'istanza del servizio Windows SAP ERS
Cambiare il tipo di avvio del servizio Windows SAP ERS (Enqueue Replication Server) in **Automatico (avvio ritardato)** in entrambi i nodi del cluster.

![Figura 58: Cambiare il tipo di avvio del servizio per l'istanza di SAP ERS in Automatico (avvio ritardato)][sap-ha-guide-figure-3050]

***Figura 58:** Cambiare il tipo di avvio del servizio per l'istanza di SAP ERS in Automatico (avvio ritardato)*

### <a name="a-name2477e58f-c5a7-4a5d-9ae3-7b91022cafb5a-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installare Primary Application Server (PAS) SAP
Installare l'istanza di Primary Application Server (PAS) <*SID*>-di-0 nella macchina virtuale designata per ospitare PAS. Non sono presenti dipendenze da specifiche di Azure o DataKeeper.

### <a name="a-name0ba4a6c1-cc37-4bcf-a8dc-025de4263772a-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installare Additional Application Server (AAS) SAP
Installare l'istanza di Additional Application Server (AAS) SAP in tutte le macchine virtuali designate per ospitare un server applicazioni SAP. Ad esempio, in <*SID*>-di-1 fino a <*SID*>-di-<n>.

## <a name="a-name18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9a-test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testare il failover e la replica SIOS dell'istanza di SAP ASCS/SCS
È possibile testare e monitorare con facilità un failover dell'istanza di SAP ASCS/SCS e una replica del disco SIOS usando Gestione cluster di failover e l'interfaccia utente di SIOS DataKeeper.

### <a name="a-name65fdef0f-9f94-41f9-b314-ea45bbfea445a-sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster
Il gruppo di cluster **SAP WAC** è in esecuzione nel nodo A del cluster, ad esempio in **ascsha-clna**. Assegnare al nodo A del cluster l'unità disco condivisa S, appartenente al gruppo di cluster **SAP WAC** e usata dall'istanza di ASCS/SCS.

![Figura 59: Gestione cluster di failover: il gruppo di cluster <*SID*> SAP è in esecuzione nel nodo A del cluster][sap-ha-guide-figure-5000]

***Figura 59:** Gestione cluster di failover: il gruppo di cluster <*SID*> SAP è in esecuzione nel nodo A del cluster*

Con l'interfaccia utente di SIOS DataKeeper è possibile vedere che i dati dei dischi condivisi vengono replicati in modo sincrono dall'unità S del volume di origine nel nodo A del cluster all'unità S del volume di destinazione nel nodo B del cluster, ad esempio da **ascsha-clna [10.0.0.41]** a **ascsha-clnb [10.0.0.42]**.

![Figura 60: In SIOS DataKeeper, replicare il volume locale dal nodo A al nodo B del cluster][sap-ha-guide-figure-5001]

***Figura 60:** In SIOS DataKeeper, replicare il volume locale dal nodo A al nodo B del cluster*

### <a name="a-name5e959fa9-8fcd-49e5-a12c-37f6ba07b916a-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover dal nodo A al nodo B
È possibile usare una di queste opzioni per avviare un failover del gruppo di cluster <*SID*> SAP dal nodo A al nodo B del cluster:

* Usare Gestione cluster di failover  
* Usare PowerShell per Clustering di failover
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
* Riavviare il nodo A del cluster nel sistema operativo guest di Windows. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B  
* Riavviare il nodo A del cluster nel portale di Azure. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B  
* Riavviare il nodo A del cluster usando Azure PowerShell. Verrà avviato un failover automatico del gruppo di cluster <*SID*> SAP dal nodo A al nodo B

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

Il gruppo di cluster <*SID*> SAP è in esecuzione nel nodo B del cluster, ad esempio in **ascsha-clnb**.

![Figura 61: In Gestione cluster di failover, il gruppo di cluster <*SID*> SAP è in esecuzione nel nodo B del cluster][sap-ha-guide-figure-5002]

***Figura 61**: In Gestione cluster di failover, il gruppo di cluster <*SID*> SAP è in esecuzione nel nodo B del cluster*

Il disco condiviso è ora montato nel nodo B del cluster. SIOS DataKeeper replica i dati dall'unità S del volume di origine nel nodo B del cluster all'unità S del volume di destinazione nel nodo A del cluster, ad esempio da **ascsha-clnb [10.0.0.42]** a **ascsha-clna [10.0.0.41]**.

![Figura 62: SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster][sap-ha-guide-figure-5003]

***Figura 62:** SIOS DataKeeper replica il volume locale dal nodo B al nodo A del cluster*



<!--HONumber=Nov16_HO3-->



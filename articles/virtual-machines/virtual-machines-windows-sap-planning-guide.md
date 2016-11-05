---
title: 'SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all''implementazione | Microsoft Docs'
description: 'SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all''implementazione'
services: virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: sedusch

---
# <a name="sap-netweaver-on-windows-virtual-machines-vms-planning-and-implementation-guide"></a>SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione
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

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione DBMS)
[dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching per VM e dischi rigidi virtuali)
[dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (RAID software)
[dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Archiviazione di Microsoft Azure)
[dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struttura di una distribuzione RDBMS)
[dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Disponibilità elevata e ripristino di emergenza con VM di Azure)
[dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 e versioni successive)
[dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 e versioni precedenti)
[dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace)
[dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Riepilogo generale su SQL Server per SAP in Azure)
[dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Specifiche per RDBMS SQL Server)
[dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Configurazione dell'archiviazione)
[dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Backup e ripristino)
[dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Considerazioni sulle prestazioni per il backup e il ripristino)
[dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Altro)
[dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione)
[deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (Risorse SAP)
[deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Distribuzione di una VM con un'immagine personalizzata)
[deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP)
[deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP)
[deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP)
[deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Scenari di distribuzione di VM per SAP in Microsoft Azure)
[deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Distribuzione dei cmdlet di Azure PowerShell)
[deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Scaricare e importare i cmdlet di PowerShell pertinenti per SAP)
[deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Aggiungere la VM a un dominio locale (solo per Windows))
[deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Scaricare, installare e abilitare l'agente di VM di Azure)
[deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Interfaccia della riga di comando di Azure)
[deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Configurare l'estensione di monitoraggio avanzato di Azure per SAP)
[deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP)
[deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure)
[deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP)

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

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione)
[planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Risorse)
[planning-guide-11.4]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure)
[planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Disponibilità elevata per i server applicazioni SAP)
[planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Uso dell'avvio automatico per le istanze di SAP)
[planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti)
[planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale)
[planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Aree di Azure)
[planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Domini di errore)
[planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Domini di aggiornamento)
[planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Set di disponibilità di Azure)
[planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Concetto di macchina virtuale di Microsoft Azure)
[planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)
[planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Spostamento di una VM da locale ad Azure con un disco non generalizzato)
[planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Distribuzione di una VM con un'immagine specifica del cliente)
[planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato)
[planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP)
[planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Preparazione di VM con SAP per Azure)
[planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Differenza tra un disco di Azure e un'immagine di Azure)
[planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Caricamento di un disco rigido virtuale da locale ad Azure)
[planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Copia di dischi tra account di archiviazione di Azure)
[planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (Struttura di VM/dischi rigidi virtuali per le distribuzioni SAP)
[planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Impostazione del montaggio automatico per dischi collegati)
[planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver)
[planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (Concetti della distribuzione solo cloud di istanze di SAP)
[planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Soluzione di monitoraggio di Azure per SAP)
[planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Archiviazione Premium di Azure)

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
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-create-vm-generalized.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:virtual-machines-windows-create-vm-generalized.md
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

Microsoft Azure consente alle aziende di acquisire risorse di calcolo e di archiviazione in poco tempo, senza lunghi cicli di approvvigionamento. Macchine virtuali di Azure consente alle aziende di distribuire applicazioni classiche, ad esempio applicazioni basate su SAP NetWeaver, in Azure e di estenderne l'affidabilità e la disponibilità senza risorse aggiuntive disponibili in locale. I servizi Macchine virtuali di Azure supportano anche la connettività cross-premise, che consente alle aziende di integrare in modo attivo le Macchine virtuali di Azure nei propri domini locali, i propri cloud privati e nel panorama applicativo del sistema SAP.
Questo white paper illustra i concetti fondamentali relativi alle Macchine virtuali di Microsoft Azure e fornisce considerazioni dettagliate sulla pianificazione e sull'implementazione per installazioni di SAP NetWeaver in Azure. È quindi necessario leggere questo documento prima di avviare le distribuzioni effettive di SAP NetWeaver in Azure.
Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="summary"></a>Riepilogo
Cloud computing è un termine ampiamente diffuso che sta assumendo un'importanza sempre più rilevante nel settore IT, dalle piccole imprese fino alle grandi aziende e alle multinazionali.

Microsoft Azure è la piattaforma di servizi cloud di Microsoft che offre un'ampia gamma di nuove possibilità. I clienti possono infatti effettuare rapidamente il provisioning e il deprovisioning di applicazioni come servizi sul cloud, senza essere soggetti a eventuali limiti tecnici o di budget. Anziché investire tempo e denaro nell'infrastruttura hardware, le aziende possono concentrarsi sull'applicazione, sui processi aziendali e sui vantaggi per clienti e utenti.

Grazie ai servizi inclusi in Macchine virtuali di Microsoft Azure, Microsoft offre una piattaforma di infrastruttura distribuita come servizio (IaaS) completa. Le applicazioni basate su SAP NetWeaver sono supportate in Macchine virtuali di Azure (IaaS). Questo white paper descriverà come pianificare e implementare applicazioni basate su SAP NetWeaver nella piattaforma Microsoft Azure.

Il documento stesso è incentrato su due aspetti principali:

* La prima parte illustrerà due modelli di distribuzione supportati per applicazioni basate su SAP NetWeaver in Azure. Descriverà anche la gestione generale di Azure con particolare attenzione alle distribuzioni SAP.
* La seconda parte illustrerà in modo dettagliato l'implementazione dei due scenari diversi illustrati nella prima parte.

Per altre risorse, vedere il capitolo [Risorse][planning-guide-1.2] in questo documento.

### <a name="definitions-upfront"></a>Definizioni
Nel documento verranno usati i termini seguenti:

* IaaS (Infrastructure as a Service): infrastruttura distribuita come servizio.
* PaaS (Platform as a Service): piattaforma distribuita come servizio.
* SaaS (Software as a Service): software come un servizio.
* ARM: Azure Resource Manager
* Componente SAP: singola applicazione SAP, ad esempio ECC, BW, Solution Manager o EP.  I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* Ambiente SAP: uno o più componenti SAP raggruppati in modo logico per eseguire una funzione commerciale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
* Panorama applicativo SAP: fa riferimento a tutte le risorse SAP in un panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
* Sistema SAP: combinazione del livello DBMS e del livello applicazione, ad esempio un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Nelle distribuzioni di Azure non è supportata la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve quindi essere distribuito o in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale.
* Distribuzione solo cloud: una distribuzione in cui la sottoscrizione di Azure non è connessa tramite una connessione da sito a sito o ExpressRoute all'infrastruttura di rete locale. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche distribuzioni "solo cloud". L'accesso alle macchine virtuali distribuite con questo metodo viene effettuato tramite Internet e un indirizzo IP pubblico e/o un nome DNS pubblico assegnato alle VM in Azure. Per Microsoft Windows l'istanza locale di Active Directory (AD) e DNS non vengono estesi ad Azure in questi tipi di distribuzioni. Le macchine virtuali non sono quindi parte dell'istanza locale di Active Directory. Queste considerazioni sono applicabili anche alle implementazioni Linux che usano ad esempio OpenLDAP + Kerberos.

> [!NOTE]
> Le distribuzioni solo cloud in questo documento vengono definite come panorami applicativi SAP completi e vengono eseguite esclusivamente in Azure senza estensione di Active Directory/OpenLDAP o risoluzione dei nomi da locale a cloud pubblico. Le configurazioni solo cloud non sono supportate per sistemi SAP di produzione o per configurazioni in cui è necessario usare SAP STMS o altre risorse locali tra i sistemi SAP ospitati in Azure e le risorse che risiedono in locale.
> 
> 

* Cross-premise: indica uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory/OpenLDAP e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è lo scenario in cui si prevede che verrà distribuita la maggior parte delle risorse SAP.  Per altre informazioni, vedere [questo][vpn-gateway-cross-premises-options] articolo e [questo][vpn-gateway-site-to-site-create].

> [!NOTE]
> Le distribuzioni cross-premise di sistemi SAP in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che queste VM siano parte di un dominio locale e di ADS/OpenLDAP. In versioni precedenti della documentazione sono stati illustrati scenari IT ibridi, in cui il termine "ibrido" indica la presenza di una connettività cross-premise tra l'istanza locale e Azure, oltre al fatto che le macchine virtuali in Azure sono parte dell'istanza locale di Active Directory/OpenLDAP.
> 
> 

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni DBMS a disponibilità elevata. Nel caso dei documenti correlati a SAP, lo scenario è essenzialmente basato sulla presenza di connettività da sito a sito o privata (ExpressRoute) e sul fatto che il panorama applicativo SAP è distribuito tra l'istanza locale e Azure.  

### <a name="a-namee55d1e22c2c8460b989764622a34fdffaresources"></a><a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Risorse
Per informazioni sull'argomento relativo alle distribuzioni SAP in Azure sono disponibili le seguenti guide aggiuntive:

* [SAP NetWeaver in macchine virtuali Windows: guida alla pianificazione e all'implementazione (questo documento)][planning-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione][deployment-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione DBMS][dbms-guide]
* [SAP NetWeaver in macchine virtuali Windows: guida alla distribuzione di funzionalità di disponibilità elevata][ha-guide]

> [!IMPORTANT]
> Dove possibile, viene usato un collegamento alla guida all'installazione SAP di riferimento (vedere InstGuide-01 in <http://service.sap.com/instguides>). Per quanto riguarda i prerequisiti e il processo di installazione, è necessario leggere sempre con attenzione la Guida all'installazione di SAP NetWeaver, perché il presente documento illustra solo attività specifiche per i sistemi SAP NetWeaver installati in Macchine virtuali di Microsoft Azure.
> 
> 

Le note seguenti su SAP sono correlate all'argomento relativo a SAP in Azure:

| Numero della nota | Titolo |
| --- | --- |
| [1928533] |Applicazioni SAP in Azure: dimensioni e prodotti supportati |
| [2015553] |SAP in Microsoft Azure: prerequisiti per il supporto |
| [1999351] |Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP |
| [2178632] |Metriche chiave del monitoraggio per SAP in Microsoft Azure |
| [1409604] |Virtualizzazione in Windows: monitoraggio avanzato |
| [2191498] |SAP in Linux con Azure: monitoraggio avanzato |
| [2243692] |Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: note di installazione |
| [2002167] |Red Hat Enterprise Linux 7. x: installazione e aggiornamento |

Leggere anche il [wiki su SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contenente tutte le note SAP per Linux.

Le limitazioni predefinite generali e le limitazioni massime delle sottoscrizioni di Azure sono disponibili in [questo articolo][azure-subscription-service-limits-subscription] 

## <a name="possible-scenarios"></a>Scenari possibili
SAP è spesso considerata una delle applicazioni più cruciali nelle aziende. L'architettura e le operazioni di queste applicazioni sono in genere molto complesse ed è quindi molto importante assicurare il rispetto dei requisiti per la disponibilità e le prestazioni.

Le aziende devono quindi valutare con attenzione le applicazioni da eseguire in un ambiente di tipo cloud pubblico, indipendentemente dal provider di servizi cloud scelto.

Ecco un elenco dei possibili tipi di sistema per la distribuzione di applicazioni basate su SAP NetWeaver in ambienti cloud pubblico:

1. Sistemi di produzione di medie dimensioni
2. Sistemi di sviluppo
3. Sistemi di test
4. Sistemi prototipo
5. Sistemi di apprendimento/dimostrativi

Per distribuire correttamente i sistemi SAP in IaaS di Azure o IaaS generica, è importate comprendere le differenze significative tra le offerte di outsourcer o provider di servizi di hosting tradizionali e le offerte IaaS. Mentre il provider di servizi di hosting o l'outsourcer tradizionale adatterà l'infrastruttura (rete, risorse di archiviazione e tipo di server) al carico di lavoro che un cliente vuole ospitare, il cliente è invece responsabile della scelta del carico di lavoro appropriato per le distribuzioni IaaS.

Il cliente deve prima di tutto verificare gli elementi seguenti:

* I tipi di macchine virtuali di Azure supportati da SAP
* I prodotti o le versioni in Azure supportati da SAP
* I sistemi operativi e le versioni DBMS per le versioni SAP specifiche in Azure
* La velocità effettiva di SAPS fornita da diversi SKU di Azure

Le risposte a queste domande sono disponibili nella nota SAP [1928533]. 

Come secondo passaggio è necessario confrontare le risorse di Azure e le limitazioni per la larghezza di banda con l'utilizzo effettivo delle risorse dei sistemi locali. I clienti devono quindi acquisire familiarità con le diverse funzionalità dei tipi di Azure supportati con SAP in queste aree:

* Risorse di CPU e di memoria per diversi tipi di macchine virtuali
* Larghezza di banda IOPS per diversi tipi di macchine virtuali 
* Funzionalità di rete dei diversi tipi di macchine virtuali

La maggior parte dei dati è disponibile [qui][virtual-machines-sizes].

Occorre ricordare che i limiti elencati nel collegamento precedente sono i limiti superiori. Questo non significa che i limiti per qualsiasi risorsa, ad esempio IOPS, possono essere forniti in qualsiasi circostanza. Le eccezioni sono tuttavia costituite dalle risorse di CPU e di memoria di un tipo di VM selezionato. Per i tipi di VM supportati da SAP, le risorse di CPU e memoria vengono riservate e risultano quindi disponibili in qualsiasi momento per l'utilizzo nella VM.

La piattaforma Microsoft Azure, analogamente ad altre piattaforme IaaS, è una piattaforma multi-tenant. Le risorse di archiviazione, di rete e di altro tipo sono quindi condivise tra i tenant. Una logica intelligente di limitazioni e quote viene usata per impedire a un tenant di influire drasticamente sulle prestazioni di un altro tenant. Anche se la logica in Azure prova a limitare le varianze rilevate nella larghezza di banda, le piattaforme con un livello elevato di condivisione tendono a presentare varianze per la disponibilità di risorse/larghezza di banda superiori a quelle usuali nelle distribuzioni locali di molti clienti. È quindi possibile che si rilevino diversi livelli di larghezza di banda rispetto alla rete o alle operazioni di I/O di archiviazione, ovvero volumi e latenza, da un minuto all'altro. Occorre tenere in considerazione la probabilità che un sistema SAP in Azure presenti varianze superiori rispetto a un sistema locale.

L'ultimo passaggio consiste nel valutare i requisiti di disponibilità. Può accadere che sia necessario aggiornare l'infrastruttura sottostante di Azure e che sia necessario il riavvio degli host che eseguono le macchine virtuali. In questi casi vengono arrestate e riavviate anche le macchine virtuali in esecuzione in questi host. Questi interventi di manutenzione vengono eseguiti al di fuori dei regolari orari di ufficio per una determinata area, ma la potenziale finestra di poche ore durante cui si verifica un riavvio è relativamente ampia. La piattaforma Azure include varie tecnologie che è possibile configurare per alleviare l'impatto di questi aggiornamenti. Sono stati progettati miglioramenti futuri specifici per la piattaforma Azure, DBMS e l'applicazione SAP, in modo da ridurre al minimo l'impatto di questi riavvii. 

Per distribuire correttamente un sistema SAP in Azure, è necessario che i sistemi operativi dei sistemi SAP locali, il database e le applicazioni SAP siano visualizzati nella matrice di supporto di Azure per SAP, che siano conformi alle risorse che l'infrastruttura di Azure può fornire e che siano compatibili con i Contratti di servizio relativi alla disponibilità offerti da Microsoft Azure. Quando questi sistemi vengono identificati, è necessario scegliere uno dei due scenari di distribuzione seguenti.

### <a name="a-name1625df664cc64d609202de8a0b77f803acloudonly-virtual-machine-deployments-into-azure-without-dependencies-on-the-onpremises-customer-network"></a><a name="1625df66-4cc6-4d60-9202-de8a0b77f803"></a>Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti
![Singola macchina virtuale con scenario dimostrativo o di formazione SAP distribuito in Azure][planning-guide-figure-100]

Questo è lo scenario tipico per sistemi di formazione o dimostrativi, in cui tutti i componenti software SAP e non SAP vengono installati in una singola VM. I sistemi SAP di produzione non sono supportati in questo scenario di distribuzione. Questo scenario rispetta in genere i requisiti seguenti:

* Le macchine virtuali stesse sono accessibili tramite la rete pubblica. La connettività di rete diretta per le applicazioni in esecuzione nelle VM con la rete locale dell'azienda proprietaria dei contenuti dimostrativi o di formazione oppure del cliente non è necessaria. 
* Nel caso di più macchine virtuali che rappresentano lo scenario di formazione o dimostrativo, le comunicazioni di rete e la risoluzione dei nomi devono essere abilitate tra le macchine virtuali. Le comunicazioni tra il set di macchine virtuali devono essere tuttavia isolate, in modo che sia possibile distribuire più set di VM affiancati, senza che si verifichino interferenze.  
* La connettività Internet è necessaria per consentire all'utente finale di accedere in modalità remota alle VM ospitate in Azure. In base al sistema operativo guest, è necessario usare Servizi terminal/Servizi Desktop remoto o VNC/ssh per accedere alla macchina virtuale per completare le attività di formazione o eseguire le demo. Se è possibile esporre anche le porte SAP 3200, 3300 e 3600, l'istanza dell'applicazione SAP risulterà accessibile da qualsiasi computer desktop connesso a Internet.
* I sistemi SAP e le macchine virtuali rappresentano uno scenario autonomo in Azure che richiede solo la connettività Internet pubblica per l'accesso degli utenti finali e non richiede una connessione ad altre VM in Azure.
* SAP GUI e un browser vengono installati e vengono eseguiti direttamente nella macchina virtuale. 
* È necessario eseguire un ripristino rapido dello stato originale della VM e una nuova distribuzione di tale stato originale. 
* Nel caso di scenari dimostrativi e di formazione realizzati in più macchine virtuali, è necessario avere un'istanza di Active Directory/OpenLDAP e/o il servizio DNS per ogni set di VM.

![Gruppo di VM che rappresenta un scenario dimostrativo o di formazione in un servizio cloud di Azure][planning-guide-figure-200]

È importante ricordare che le macchine virtuali in ogni set devono essere distribuite in parallelo e che i nomi delle macchine virtuali in ogni set sono uguali.

### <a name="a-namef5b3b18c302c4bd89ab2c388f1ab3d10acrosspremise-deployment-of-single-or-multiple-sap-vms-into-azure-with-the-requirement-of-being-fully-integrated-into-the-onpremises-network"></a><a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale
![VPN con connettività da sito a sito (cross-premise)][planning-guide-figure-300]

Questo scenario è uno scenario cross-premise con molti modelli di distribuzione possibili. Può essere descritto semplicemente come l'esecuzione di alcune parti del panorama applicativo SAP in locale e di altre parti del panorama applicativo SAP in Azure. Tutti gli aspetti dell'esecuzione di parte dei componenti SAP in Azure devono essere trasparenti per gli utenti finali. Il sistema STMS (SAP Correction and Transport System), i servizi di comunicazione RFC, stampa e sicurezza, ad esempio l'accesso Single Sign-On, e così via funzioneranno perfettamente per i sistemi SAP in esecuzione in Azure. Lo scenario cross-premise tuttavia descrive anche uno scenario in cui il panorama applicativo SAP completo viene eseguito in Azure con l'estensione del dominio e del DNS del cliente in Azure. 

> [!NOTE]
> Questo è lo scenario di distribuzione supportato per l'esecuzione di sistemi SAP di produzione.
> 
> 

Per altre informazioni sulla connessione della rete locale a Microsoft Azure, vedere [questo articolo][vpn-gateway-create-site-to-site-rm-powershell]

> [!IMPORTANT]
> Quando si esaminano gli scenari cross-premise tra Azure e le distribuzioni locali dei clienti, si esamina la granularità degli interi sistemi SAP. Ecco gli scenari *non supportati* per gli scenari cross-premise:
> 
> * Esecuzione di livelli diversi di applicazioni SAP in diversi metodi di distribuzione, ad esempio esecuzione del livello DBMS in locale mentre il livello dell'applicazione SAP nelle VM è distribuito come VM di Azure o viceversa.
> * Alcuni componenti di un livello SAP in Azure e altri in locale, ad esempio suddivisione di istanze del livello dell'applicazione SAP tra istanza locale e VM di Azure. 
> * La distribuzione di macchine virtuali che eseguono istanze SAP di un sistema in più aree di Azure non è supportata.
> 
> Il motivo alla base di queste limitazioni è costituito dal requisito relativo a una rete con latenza molto bassa e prestazioni elevate in un sistema SAP, in particolare tra le istanze dell'applicazione e il livello DBMS di un sistema SAP.
> 
> 

### <a name="supported-os-and-database-releases"></a>Sistemi operativi e versioni di database supportati
* Il software server Microsoft supportato per i servizi Macchine virtuali di Azure è elencato in questo articolo: <http://support.microsoft.com/kb/2721672>. 
* Le versioni supportate del sistema operativo e le versioni dei sistemi database supportate nei servizi di macchine virtuali di Azure insieme al software SAP sono indicate nella nota SAP [1928533]. 
* Le applicazioni e le versioni SAP supportate nei servizi di macchine virtuali di Azure sono indicate nella nota SAP [1928533].
* Sono supportate solo immagini a 64 bit per l'esecuzione come macchine virtuali guest in Azure per scenari SAP. Sono quindi supportati solo database e applicazioni SAP a 64 bit.

## <a name="microsoft-azure-virtual-machine-services"></a>Servizi Macchine virtuali di Microsoft Azure
La piattaforma Microsoft Azure è una piattaforma di servizi cloud su scala Internet ospitata e gestita nei data center Microsoft. La piattaforma include i servizi Macchine virtuali di Microsoft Azure (infrastruttura distribuita come servizio o IaaS) e un set di funzionalità avanzate per la piattaforma distribuita come servizio (PaaS).

La piattaforma Azure riduce la necessità di acquisti iniziali di tecnologia e infrastruttura. Semplifica la manutenzione e la gestione delle applicazioni offrendo risorse di calcolo e di archiviazione su richiesta per ospitare, ridimensionare e gestire applicazioni Web e applicazioni connesse. La gestione dell'infrastruttura viene automatizzata mediante una piattaforma progettata per la disponibilità elevata e il ridimensionamento dinamico per adeguarsi alle esigenze di utilizzo, con la possibilità di scegliere un modello di determinazione prezzi con pagamento in base al consumo.

![Posizionamento di Servizi Macchine virtuali di Microsoft Azure][planning-guide-figure-400]

I servizi Macchine virtuali di Azure consentono di distribuire immagini server personalizzate in Azure come istanze IaaS (vedere la figura 4). Le macchine virtuali in Azure sono basate sui dischi rigidi virtuali (VHD, Virtual Hard Drive) Hyper-V e possono eseguire sistemi operativi diversi come sistemi operativi guest.

Dal punto di vista operativo, il servizio Macchine virtuali di Azure offre un'esperienza analoga alle macchine virtuali distribuite in locale. Il vantaggio significativo offerto tuttavia è costituito dal fatto che non è necessario procurarsi, amministrare e gestire l'infrastruttura. Gli sviluppatori e gli amministratori hanno il controllo completo dell'immagine del sistema operativo in queste macchine virtuali. Gli amministratori possono accedere in modalità remota alle macchine virtuali per eseguire attività di manutenzione e risoluzione dei problemi, oltre ad attività di distribuzione del software. Per quanto riguarda la distribuzione, le uniche limitazioni previste sono relative alle dimensioni e alle funzionalità delle VM di Azure. È possibile che la configurazione non sia dettagliata quanto la configurazione locale. È possibile scegliere tipi di VM basati su una combinazione degli elementi seguenti:

* Numero di vCPU
* Memoria
* Numero di dischi rigidi virtuali che è possibile collegare
* Larghezza di banda di rete e di archiviazione

Le dimensioni e le limitazioni delle diverse dimensioni offerte per le varie macchine virtuali sono illustrate in una tabella in [questo articolo][virtual-machines-sizes].

Come si può notare, sono disponibili diverse famiglie o serie di macchine virtuali. Da dicembre 2015 sono disponibili le famiglie seguenti di VM:

* Tipi di VM A0-A7: non tutti questi tipi sono certificati per SAP. Si tratta della prima serie di VM in cui è stata introdotta la soluzione IaaS per Azure.
* Tipi di VM A8-A11: istanze HPC (High-Performance Computing). In esecuzione in host di calcolo diversi, con prestazioni migliori rispetto alle altre VM della serie A.
* Tipi di VM di serie D: prestazioni migliori rispetto ad A0-A7. Non tutti i tipi di VM sono certificati per SAP.
* Tipi di VM di serie DS: usano gli stessi host della serie D, ma possono connettersi all'archiviazione Premium di Azure. Vedere il capitolo [Archiviazione Premium di Azure][planning-guide-3.3.2] di questo documento. Anche in questo caso, non tutti i tipi di VM sono certificati per SAP.
* Tipi di VM di serie G: tipi di macchine virtuali con memoria elevata. 
* Tipi di VM di serie GS: analoghi alla serie G, ma con la possibilità di usare l'archiviazione Premium di Azure. Vedere il capitolo [Archiviazione Premium di Azure][planning-guide-3.3.2] di questo documento. Quando si usano macchine virtuali della serie GS come server di database, è obbligatorio usare l'Archiviazione Premium per i dati del database e i file di log delle transazioni.

È possibile che diverse serie di VM presentino le stesse configurazioni di CPU e memoria. Quando si verificano le prestazioni della velocità effettiva delle VM delle diverse serie, è tuttavia possibile che si notino differenze significative, indipendentemente dal fatto che la configurazione di CPU e memoria sia uguale. Ciò dipende dal fatto che l'hardware del server host sottostante presentava caratteristiche di velocità effettiva diverse al momento dell'introduzione dei diversi tipi di VM.  La differenza rilevata a livello di prestazioni della velocità effettiva si riflette in genere sul prezzo delle diverse VM.

Si noti che è possibile che non tutte le diverse serie di VM vengano offerte in ogni area di Azure. Per informazioni sulle aree di Azure, vedere il capitolo successivo. Occorre anche notare che non tutte le VM o le serie di VM sono certificate per SAP.

> [!IMPORTANT]
> Per l'uso di applicazioni basate su SAP NetWeaver, sono supportati solo le configurazioni e i sottoinsiemi di tipi di VM elencati nella nota SAP [1928533] .
> 
> 

### <a name="a-namebe80d1b9a4634845bd35f4cebdb5424aaazure-regions"></a><a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Aree di Azure
Microsoft consente di distribuire macchine virtuali in quelle che vengono definite "aree di Azure". Un'area di Azure può essere costituita da uno o più data center che si trovano in posizioni molto vicine. Microsoft ha almeno due aree di Azure per la maggior parte delle aree geopolitiche del mondo, ad esempio in Europa sono disponibili le aree di Azure "Europa settentrionale" ed "Europa occidentale". Due aree di Azure entro un'area geopolitica sono separate da una distanza sufficiente da evitare che emergenze tecniche o calamità naturale influiscano su entrambe le aree di Azure nella stessa area geopolitica. Poiché Microsoft crea costantemente nuove aree di Azure in diverse aree geopolitiche a livello globale, il numero di queste aree è in continua crescita e da dicembre 2015 sono state raggiunte le 20 aree di Azure, con altre aree già annunciate. I clienti possono distribuire sistemi SAP in tutte queste aree, incluse le due aree di Azure in Cina. Per informazioni aggiornate sulle aree di Azure, vedere il sito Web <https://azure.microsoft.com/regions/>

### <a name="a-name8d8ad4b860934b91ac36ea56d80dbf77athe-microsoft-azure-virtual-machine-concept"></a><a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Concetto di macchina virtuale di Microsoft Azure
Microsoft Azure offre una soluzione IaaS (Infrastructure as a Service) per ospitare macchine virtuali con funzionalità simili come soluzione di virtualizzazione locale. È possibile creare macchine virtuali dal portale di Azure, mediante PowerShell o tramite l'interfaccia della riga di comando, che offre anche funzionalità di distribuzione e gestione.

Gestione risorse di Azure consente di effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Si usa lo stesso modello per distribuire più volte l'applicazione durante ogni fase del ciclo di vita dell'applicazione.

Altre informazioni sull'uso dei modelli di Azure Resource Manager sono disponibili qui:

* [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure][virtual-machines-linux-cli-deploy-templates]
* [Gestire macchine virtuali di Azure con Resource Manager e PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Un'altra funzionalità interessante è costituita dalla possibilità di creare immagini dalle macchine virtuali, in modo da potere preparare alcuni repository da cui sarà possibile distribuire rapidamente istanze di macchine virtuali che soddisfano i requisiti specifici.

Per altre informazioni sulla creazione di immagini da macchine virtuali, vedere i relativi articoli per [Windows][virtual-machines-windows-capture-image] e per [Linux][virtual-machines-linux-capture-image].

#### <a name="a-namedf49dc09141b4f34a4a2990913b30358afault-domains"></a><a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Domini di errore
I domini di errore rappresentano un'unità fisica di errore, strettamente correlata all'infrastruttura fisica contenuta nei data center. Anche se è possibile considerare un pannello fisico o un rack come dominio di errore, non è disponibile alcun mapping diretto uno-a-uno tra i due. 

Quando si distribuiscono più macchine virtuali come parte di un sistema SAP nei servizi Macchine virtuali di Microsoft Azure, è possibile influenzare il controller di infrastruttura di Azure in modo da distribuire l'applicazione in domini di errore diversi, rispettando quindi i requisiti del Contratto di servizio di Microsoft Azure. L'utente non ha alcun controllo diretto sulla distribuzione di domini di errore in un'unità di scala di Azure, ovvero una raccolta di centinaia di nodi di calcolo o di nodi di archiviazione e di risorse di rete, oppure sull'assegnazione di macchine virtuali a un dominio di errore specifico. Per fare in modo che il controller di infrastruttura di Azure distribuisca un set di macchine virtuali in diversi domini di errore, è necessario assegnare un set di disponibilità di Azure alle VM durante la fase di distribuzione. Per altre informazioni sui set di disponibilità di Azure, vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] in questo documento.

#### <a name="a-namefc1ac8b2e54a487c8581d3cc6625e560aupgrade-domains"></a><a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Domini di aggiornamento
I domini di aggiornamento rappresentano un'unità logica utile per determinare il modo in cui verrà aggiornata una VM in un sistema SAP, costituito da istanze di SAP in esecuzione in più macchine virtuali. Quando si verifica un aggiornamento, Microsoft Azure esegue il processo di aggiornamento dei domini di aggiornamento, uno alla volta. Suddividendo le macchine virtuali in diversi domini di aggiornamento durante la fase di distribuzione, è possibile proteggere parzialmente il sistema SAP da potenziali tempi di inattività. Per imporre ad Azure la distribuzione delle macchine virtuali in un sistema SAP suddivise in diversi domini di aggiornamento, è necessario impostare un attributo specifico in fase di distribuzione di ogni VM. Analogamente ai domini di errore, un'unità di scala di Azure viene divisa in più domini di aggiornamento. Per fare in modo che il controller di infrastruttura di Azure distribuisca un set di macchine virtuali in diversi domini di aggiornamento, è necessario assegnare un set di disponibilità di Azure alle VM durante la fase di distribuzione. Per altre informazioni sui set di disponibilità di Azure, vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] di seguito.

#### <a name="a-name18810088f9be4c97958a27996255c665aazure-availability-sets"></a><a name="18810088-f9be-4c97-958a-27996255c665"></a>Set di disponibilità di Azure
Le macchine virtuali di Azure in un set di disponibilità di Azure verranno distribuite dal controller di infrastruttura di Azure in diversi domini di errore e di aggiornamento. Lo scopo della distribuzione in diversi domini di errore e di aggiornamento consiste nell'evitare che tutte le macchine virtuali di un sistema SAP vengano arrestate in caso di manutenzione dell'infrastruttura o in caso di errore in un dominio di errore. Per impostazione predefinita, le VM non fanno parte di un set di disponibilità. La partecipazione di una macchina virtuale a un set di disponibilità viene definita in fase di distribuzione oppure successivamente mediante una riconfigurazione e una ridistribuzione di una VM.

Per informazioni sul concetto di set di disponibilità di Azure e sulla correlazione tra i set di disponibilità e i domini di errore e di aggiornamento, vedere [questo articolo][virtual-machines-manage-availability].

Per definire i set di disponibilità per Azure Resource Manager tramite un modello JSON, vedere le [specifiche dell'API REST](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json) e cercare le sezioni relative alla disponibilità.

### <a name="a-namea72afa264bf44a258cf7855d6032157fastorage-microsoft-azure-storage-and-data-disks"></a><a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Archiviazione: Archiviazione di Microsoft Azure e dischi dati
Le macchine virtuali di Microsoft Azure utilizzano diversi tipi di archiviazione. Quando si implementa SAP nei servizi Macchine virtuali di Azure, è importante comprendere le differenze tra questi due tipi principali di archiviazione:

* Archiviazione volatile, non permanente.
* Archiviazione permanente.

L'archiviazione non permanente è collegata direttamente alle macchine virtuali in esecuzione e risiede nei nodi di calcolo, ovvero l'istanza locale (archiviazione temporanea). Le dimensioni dipendono da quelle della macchina virtuale scelta all'avvio della distribuzione. Questo tipo di archiviazione è volatile e quindi il disco viene inizializzato al riavvio di un'istanza di macchina virtuale. In genere il file di paging per il sistema operativo si trova in questo disco temporaneo.

- - -
> ![ Windows][Logo_Windows] Windows
> 
> Nelle macchine virtuali Windows l'unità temporanea viene montata come unità D:\ in una VM distribuita.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Nelle macchine virtuali Linux viene montata come /mnt/resource o /mnt. Per informazioni dettagliate, vedere:
> 
> * [Come collegare un disco dati a una macchina virtuale Linux][virtual-machines-linux-how-to-attach-disk]
> * <http://blogs.msdn.com/b/mast/Archive/2013/12/07/Understanding-the-Temporary-drive-on-Windows-Azure-Virtual-Machines.aspx>
> 
> 

- - -
L'unità effettiva è volatile perché viene archiviata nel server host stesso. Se la macchina virtuale viene spostata durante una ridistribuzione, ad esempio a causa di interventi di manutenzione dell'host o di un arresto seguito da un riavvio, il contenuto dell'unità va perso. Non è quindi consigliabile archiviare dati importanti in questa unità. Il tipo di supporto usato per questo tipo di archiviazione risulta diverso tra le diverse serie di VM e offre caratteristiche molto diverse a livello di prestazioni, che da giugno 2015 sono analoghe alle seguenti:

* A5-A7: prestazioni estremamente limitate. Consigliato solo per i file di paging.
* A8-A11: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva >1 GB/sec.
* Serie D: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva >1 GB/sec.
* Serie DS: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva >1 GB/sec.
* Serie G: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva >1 GB/sec.
* Serie GS: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva >1 GB/sec.

Le indicazioni precedenti sono applicabili ai tipi di macchine virtuali certificati per SAP. Le serie di VM con valori eccellenti per IOPS e velocità effettiva sono idonee per l'uso da parte di alcune funzionalità DBMS. Per altri dettagli, vedere la [guida alla distribuzione DBMS][dbms-guide].

L'Archiviazione di Microsoft Azure offre archiviazione permanente e i tipici livelli di protezione e ridondanza disponibili nelle soluzioni di archiviazione SAN. I dischi basati su Archiviazione di Azure sono dischi rigidi virtuali (VHD) situati nei servizi di Archiviazione di Azure. Il disco del sistema operativo locale (Windows C:\, Linux / ( /dev/sda1 )) è archiviato in Archiviazione di Azure, dove sono presenti anche volumi e dischi aggiuntivi montati nella macchina virtuale.

È possibile caricare un disco rigido virtuale esistente dall'ambiente locale o creare dischi rigidi virtuali vuoti da Azure e collegarli alle macchine virtuali distribuite. Questi dischi rigidi virtuali vengono definiti dischi di Azure. 

Dopo la creazione o il caricamento di un disco rigido virtuale in Archiviazione di Azure, è possibile montare e collegare il disco a una macchina virtuale esistente e copiare un disco rigido virtuale esistente (non montato).

Poiché questi dischi rigidi virtuali sono permanenti, i dati e le modifiche apportate al loro interno rimangono al sicuro dopo il riavvio e la ricreazione di un'istanza di macchina virtuale. Anche se viene eliminata un'istanza, questi dischi rigidi virtuali rimangono sicuri e possono essere ridistribuiti oppure, in caso di dischi non relativi al sistema operativo, possono essere montati in altre macchine virtuali.

Nella rete di Archiviazione di Azure è possibile configurare diversi livelli di ridondanza:

* Il livello minimo selezionabile è costituito dalla "ridondanza locale", che equivale a tre repliche dei dati nello stesso data center di un'area di Azure. Vedere il capitolo [Aree di Azure][planning-guide-3.1]. 
* L'archiviazione con ridondanza della zona distribuirà le tre immagini in data center diversi nella stessa area di Azure.
* Il livello di ridondanza predefinito è la ridondanza geografica, che replica in modo asincrono il contenuto in altre tre immagini dei dati in un'altra area di Azure ospitata nella stessa area geopolitica.

Per informazioni sulle diverse opzioni di ridondanza, vedere anche la tabella all'inizio di questo articolo: <https://azure.microsoft.com/pricing/details/storage/> 

Altre informazioni su Archiviazione di Azure sono disponibili qui: 

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://azure.microsoft.com/services/site-recovery>
* <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Archiviazione Standard di Azure
L'archiviazione BLOB Standard di Azure era il tipo di archiviazione disponibile al momento del rilascio della soluzione IaaS di Azure. Prevedeva l'applicazione di quote di IOPS per ogni disco rigido virtuale. Il livello di latenza disponibile non era analogo a quello dei dispositivi SAN/NAS distribuiti in genere per sistemi SAP di fascia alta ospitati in locale. L'Archiviazione Standard di Azure, tuttavia, si è rivelata sufficiente per molte centinaia di sistemi SAP distribuiti in Azure nel frattempo.

Gli addebiti per l'Archiviazione Standard di Azure vengono applicati in base ai dati effettivi archiviati, al volume delle transazioni di archiviazione, ai trasferimenti di dati in uscita e all'opzione di ridondanza scelta. È possibile creare molti dischi rigidi virtuali nel livello massimo di dimensioni pari a 1 TB, ma se tali dischi rimangono vuoti non viene applicato alcun addebito. Se si inseriscono 100 GB in ogni disco rigido virtuale, verrà addebitata l'archiviazione di 100 GB, non le dimensioni nominali specificate per la creazione del disco rigido virtuale.

#### <a name="a-nameff5ad0f9f7f440229102af07aef3bc92aazure-premium-storage"></a><a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Archiviazione Premium di Azure
Nel mese di aprile 2015 Microsoft ha introdotto l'Archiviazione Premium di Azure, con l'obiettivo di offrire i vantaggi seguenti:

* Migliore latenza di I/O.
* Migliore velocità effettiva.
* Minore variabilità della latenza di I/O.

A tale scopo, sono state introdotte molte modifiche e le due modifiche più importanti sono le seguenti:

* Utilizzo dei dischi SSD nei nodi di Archiviazione di Azure
* Nuova cache di lettura basata sul disco SSD locale di un nodo di calcolo di Azure

A differenza dell'archiviazione Standard, in cui le funzionalità non cambiano in base alle dimensioni del disco o del disco rigido virtuale, l'archiviazione Premium offre attualmente 3 diverse categorie di disco, illustrate alla fine di questo articolo prima della sezione Domande frequenti: <https://azure.microsoft.com/pricing/details/storage/>

Come si può notare, i valori di IOPS/VHD e di velocità effettiva/VHD dipendono dalla categoria di dimensioni dei dischi.

Nel caso dell'Archiviazione Premium i costi non sono basati sui volumi di dati effettivi archiviati in tali dischi rigidi virtuali, ma sulla categoria di dimensioni di un disco rigido virtuale, indipendentemente dalla quantità di dati archiviati nel disco rigido virtuale.

È anche possibile creare dischi rigidi virtuali nell'Archiviazione Premium privi di mapping diretto alle categorie di dimensioni illustrate, ad esempio in caso di copia di dischi rigidi virtuali dall'Archiviazione Standard nell'Archiviazione Premium. In questi casi viene eseguito il mapping all'opzione successiva superiore per i dischi di Archiviazione Premium. 

Si noti che solo alcune serie di VM possono usare l'Archiviazione Premium di Azure. Da dicembre 2015 si tratta delle serie DS e GS. La serie DS è essenzialmente uguale alla serie D, con l'eccezione che la serie DS è in grado di montare macchine virtuali basate sull'Archiviazione Premium, oltre ai dischi rigidi virtuali ospitati nell'Archiviazione Standard di Azure. Le stesse considerazioni sono applicabili alla serie G rispetto alla serie GS.

Se si verifica la sezione di [questo articolo][virtual-machines-sizes] relativa alle VM di serie DS, si noterà anche che sono previste limitazioni ai volumi dei dati per i dischi rigidi virtuali dell'archiviazione Premium a livello di VM. Diverse VM di serie DS o GS prevedono anche limitazioni diverse rispetto al numero di dischi rigidi virtuali che è possibile montare. Questi limiti sono documentati nell'articolo indicato in precedenza. Ciò significa essenzialmente che se, ad esempio, si montano 32 x P30 dischi/dischi rigidi virtuali in una singola VM DS14 NON sarà possibile ottenere 32 x la velocità massima effettiva di un disco P30. La velocità massima effettiva del livello di VM, in base a quanto documentato nell'articolo, limiterà invece la velocità effettiva dei dati. 

Altre informazioni sull'archiviazione Premium sono disponibili qui: <http://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Account di archiviazione di Azure
Quando si distribuiscono servizi o macchine virtuali in Azure, la distribuzione di dischi rigidi virtuali e immagini di VM deve essere organizzata in unità definite account di archiviazione di Azure. Quando si pianifica una distribuzione di Azure, è necessario considerare con attenzione le restrizioni di Azure. Da un lato, è disponibile un numero limitato di account di archiviazione per ogni sottoscrizione di Azure. Anche se ogni account di archiviazione di Azure può includere un numero elevato di file VHD, è previsto un limite fisso sul totale di IOPS per ogni account di archiviazione. Quando si distribuiscono centinaia di VM SAP con sistemi DBMS che creano un numero significativo di chiamate di I/O, è consigliabile distribuire macchine virtuali con DBMS a livelli elevati di IOPS tra più account di archiviazione di Azure. Occorre prestare attenzione e non superare il limite attuale di account di archiviazione di Azure per sottoscrizione. Poiché l'archiviazione è una parte essenziale della distribuzione di database per un sistema SAP, questo concetto viene illustrato in maggior dettaglio nella [guida alla distribuzione DBMS][dbms-guide] già citata.

Per altre informazioni sugli account di archiviazione di Azure, vedere [questo articolo][storage-scalability-targets]. Leggendo questo articolo si noteranno differenze nelle limitazioni tra gli account di archiviazione Standard di Azure e gli account di archiviazione Premium. Le differenze principali sono relative al volume di dati che è possibile archiviare in tali account di archiviazione. Nell'Archiviazione Standard il volume risulta superiore rispetto all'Archiviazione Premium. D'altra parte, l'account di archiviazione Standard presenta notevoli limitazioni di IOPS (vedere la colonna "Frequenza di richiesta totale"), mentre per l'account di archiviazione Premium di Azure non sono previste tali limitazioni. I dettagli e i risultati di queste differenze verranno illustrati durante l'analisi delle distribuzioni di sistemi SAP e in particolare dei server DBMS.

In un account di archiviazione è possibile creare diversi contenitori per finalità di organizzazione e classificazione di diversi dischi rigidi virtuali. Questi contenitori vengono in genere usati ad esempio per separare dischi rigidi virtuali di diverse macchine virtuali. L'uso di un solo contenitore o di più contenitori in un singolo account di archiviazione di Azure non comporta alcuna implicazione a livello di prestazioni.

In Azure viene usata la convenzione di denominazione seguente per il nome di un disco rigido virtuale, che prevede che venga specificato un nome univoco per il disco rigido virtuale in Azure:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Come indicato, la stringa precedente deve identificare in modo univoco il disco rigido virtuale archiviato in Archiviazione di Azure.

### <a name="a-name616783878868435d9f8c450b2424f5bdamicrosoft-azure-networking"></a><a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Rete di Microsoft Azure
Microsoft Azure offrirà un'infrastruttura di rete che consente il mapping di tutti gli scenari da realizzare con il software SAP. Ecco le funzionalità disponibili:

* Accesso dall'esterno direttamente alle macchine virtuali tramite Servizi terminal di Windows o ssh/VNC
* Accesso ai servizi e a porte specifiche usate dalle applicazioni entro le macchine virtuali
* Comunicazioni interne e risoluzione dei nomi tra un gruppo di macchine virtuali distribuite come macchine virtuali di Azure
* Connettività cross-premise tra la rete locale di un cliente e la rete di Azure
* Connettività tra aree o data center di Azure tra i siti di Azure 

Altre informazioni sono disponibili qui: <https://azure.microsoft.com/documentation/services/virtual-network/>

Esistono numerose possibilità diverse per configurare la risoluzione di nomi e IP in Azure. In questo documento gli scenari solo cloud si basano sull'impostazione predefinita, che prevede di usare DNS di Azure invece di definire un servizio DNS personalizzato. È anche disponibile un nuovo servizio DNS di Azure, che può essere usato invece di configurare un server DNS personalizzato. Per altre informazioni, vedere [questo articolo][virtual-networks-manage-dns-in-vnet] e [questa pagina](https://azure.microsoft.com/services/dns/).

Per gli scenari cross-premise ci si basa sul fatto che le istanze locali di AD/OpenLDAP/DNS sono state estese tramite VPN o connessione privata ad Azure. Per determinati scenari illustrati in questo documento, può essere necessario che in Azure sia installata una replica di AD/OpenLDAP.

Poiché la rete e la risoluzione dei nomi sono parti essenziali della distribuzione di database per un sistema SAP, questo concetto viene illustrato in maggior dettaglio nella [guida alla distribuzione DBMS][dbms-guide].

##### <a name="azure-virtual-networks"></a>Reti virtuali di Azure
La creazione di una rete virtuale di Azure consente di definire l'intervallo di indirizzi degli indirizzi IP privati allocati dalla funzionalità DHCP di Azure. Negli scenari cross-premise l'intervallo di indirizzi IP definito verrà comunque allocato usando DHCP di Azure. La risoluzione dei nomi di dominio verrà tuttavia eseguita in locale, presupponendo che le VM facciano parte di un dominio locale, e sarà quindi possibile risolvere indirizzi che si trovano al di là di diversi servizi cloud di Azure.

[comment]: <> (MSSedusch still needed? TODO Originally an Azure Virtual Network was bound to an Affinity Group. Questa configurazione comportava la limitazione di una rete virtuale di Azure all'unità di scala di Azure a cui era stato assegnato il gruppo di affinità. La rete virtuale era quindi limitata alle risorse disponibili nell'unità di scala di Azure. Questa impostazione è stata modificata e ora le reti virtuali di Azure possono essere estese su più unità di scala di Azure. È tuttavia necessario che le reti virtuali di Azure **NON** siano più associate a gruppi di affinità in fase di creazione. Come indicato in precedenza, contrariamente a quanto consigliato un anno fa, **NON usare più gruppi di affinità di Azure**. Per informazioni dettagliate, vedere <https://azure.microsoft.com/blog/regional-virtual-networks/>)

Ogni macchina virtuale di Azure deve essere connessa a una rete virtuale.

Per altri dettagli, vedere [questo articolo][resource-groups-networking] e [questa pagina](https://azure.microsoft.com/documentation/services/virtual-network/).

[comment]: <> (MShermannd TODO Couldn't find an article which includes the OpenLDAP topic + ARM; )
[comment]: <> (MSSedusch <https://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL>)

> [!NOTE]
> Per impostazione predefinita, dopo la distribuzione di una macchina virtuale non è possibile modificare la configurazione della rete virtuale. Le impostazioni di TCP/IP devono essere lasciate sul server DHCP di Azure. Il comportamento predefinito prevede l'assegnazione di IP dinamici.
> 
> 

L'indirizzo MAC della scheda di rete virtuale potrebbe cambiare, ad esempio dopo il ridimensionamento, e il sistema operativo guest Windows o Linux rileverà la nuova scheda di rete e userà automaticamente DHCP per assegnare gli indirizzi IP e DNS in questo caso.

##### <a name="static-ip-assignment"></a>Assegnazione di indirizzi IP statici
È possibile assegnare indirizzi IP fissi o riservati a macchine virtuali in una rete virtuale di Azure. L'esecuzione di VM in una rete virtuale di Azure offre la possibilità di sfruttare i vantaggi di questa funzionalità se necessario o richiesto per alcuni scenari. L'assegnazione di indirizzi IP rimane valida nel corso dell'intera esistenza della macchina virtuale, indipendentemente dal fatto che la VM sia in esecuzione o che sia stata arrestata. Sarà quindi necessario tenere in considerazione il numero complessivo di macchine virtuali, in esecuzione e arrestate, durante la definizione dell'intervallo di indirizzi IP per la rete virtuale. L'indirizzo IP rimane assegnato fino all'eliminazione della macchina virtuale e della rispettiva interfaccia di rete o fino al nuovo annullamento dell'assegnazione dell'indirizzo IP. Per informazioni dettagliate, vedere [questo articolo][virtual-networks-static-private-ip-arm-pportal].

##### <a name="multiple-nics-per-vm"></a>Più schede di interfaccia di rete per ogni VM
È possibile definire più schede di interfaccia di rete virtuali (vNIC, Virtual Network Interface Card) per una macchina virtuale di Azure. La possibilità di usare più vNIC consente di iniziare a configurare la separazione del traffico di rete, ad esempio per indirizzare il traffico client tramite una vNIC e il traffico back-end tramite una seconda vNIC. In base al tipo di macchina virtuale, sono previste diverse limitazioni rispetto al numero di vNIC. Per informazioni dettagliate precise, funzionalità e limitazioni, vedere questi articoli:

* [Creare una VM con più schede di interfaccia di rete][virtual-networks-multiple-nics]
* [Distribuire VM con più schede di interfaccia rete tramite un modello][virtual-network-deploy-multinic-arm-template]
* [Distribuire VM con più schede di interfaccia rete tramite PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Distribuire VM con più schede di interfaccia rete tramite l'interfaccia della riga di comando di Azure][virtual-network-deploy-multinic-arm-cli]

#### <a name="sitetosite-connectivity"></a>Connettività da sito a sito
La soluzione cross-premise è costituita da macchine virtuali di Azure e locali collegate tramite una connessione VPN trasparente e permanente. Questo diventerà probabilmente il modello di distribuzione SAP più comune in Azure. Si presuppone che le procedure e i processi operativi relativi alle istanze SAP in Azure debbano funzionare in modo trasparente. In altri termini, dovrebbe essere possibile ottenere output da questi sistemi e usare il sistema STMS (SAP Correction and Transport System) per trasferire le modifiche da un sistema di distribuzione in Azure a un sistema di test distribuito in locale. Per altre informazioni sulla modalità da sito a sito, vedere [questo articolo][vpn-gateway-create-site-to-site-rm-powershell]

##### <a name="vpn-tunnel-device"></a>Dispositivo tunnel VPN
Per creare una connessione da sito a sito da un data center locale a un data center di Azure, è necessario ottenere e configurare un dispositivo VPN oppure usare il servizio Routing e Accesso remoto, introdotto come componente software con Windows Server 2012. 

* [Creare una rete virtuale con una connessione da sito a sito usando PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informazioni sui dispositivi VPN per le connessioni gateway VPN da sito a sito][vpn-gateway-about-vpn-devices]
* [Domande frequenti su Gateway VPN][vpn-gateway-vpn-faq]

![Connessione da sito a sito tra rete locale e Azure][planning-guide-figure-600]

La figura precedente mostra due sottoscrizioni di Azure con intervalli secondari di indirizzi IP riservati per l'uso nelle reti virtuali in Azure. La connettività dalla rete locale ad Azure viene stabilita tramite VPN.

#### <a name="pointtosite-vpn"></a>VPN da punto a sito
La VPN da punto a sito richiede che ogni computer client si connetta ad Azure mediante la propria VPN. La connettività da punto a sito non risulta pratica per gli scenari SAP esaminati. Non verranno quindi forniti altri riferimenti alla connettività VPN da punto a sito.

[comment]: <> (MSSedusch -- More information can be found here)
[comment]: <> (MShermannd TODO Link no longer valid; But ARM is anyway not supported - see next link below)
[comment]: <> (MSSedusch -- <http://msdn.microsoft.com/library/azure/dn133798.aspx>.)
[comment]: <> (MShermannd TODO Point to Site not supported yet with ARM )
[comment]: <> (MSSedusch -- <https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/>)

#### <a name="multisite-vpn"></a>VPN multisito
Azure offre attualmente anche la possibilità di creare connettività VPN multisito per una sottoscrizione di Azure. In precedenza una singola sottoscrizione era limitata a una connessione VPN da sito a sito. Questa limitazione è stata rimossa grazie alle connessioni VPN multisito per una singola sottoscrizione. Ciò consente di usare più aree di Azure per una sottoscrizione specifica tramite le configurazioni cross-premise.

Per altre informazioni, vedere [questo articolo][vpn-gateway-create-site-to-site-rm-powershell]

[comment]: <> (MShermannd TODO found no ARM docu link)

#### <a name="vnet-to-vnet-connection"></a>Connessione da rete virtuale a rete virtuale
Se si usa una VPN multisito, è necessario configurare una rete virtuale di Azure separata in ogni area. Molto spesso è tuttavia necessario che i componenti software delle diverse aree comunichino tra loro. Queste comunicazioni non dovrebbero essere idealmente indirizzate da un'area di Azure all'ambiente locale e quindi all'altra area di Azure. Per semplificare, Azure offre la possibilità di configurare una connessione da una rete virtuale di Azure in un'area a un'altra rete virtuale di Azure ospitata in un'altra area. Questa funzionalità viene definita connessione da rete virtuale a rete virtuale. Altri dettagli su questa funzionalità sono disponibili qui: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Connessione privata ad Azure: ExpressRoute
Microsoft Azure ExpressRoute consente la creazione di connessioni private tra i data center di Azure e l'infrastruttura locale del cliente o in un ambiente con condivisione percorso dati. ExpressRoute viene offerto da diversi provider di VPN MPLS (con scambio di pacchetto) o da altri provider di servizi di rete. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Le connessioni ExpressRoute offrono un livello di sicurezza superiore, maggiore affidabilità tramite più circuiti paralleli, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali. 

Altri dettagli su Azure ExpressRoute e sulle offerte sono disponibili qui:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

ExpressRoute abilita più sottoscrizioni di Azure tramite un circuito ExpressRoute, come illustrato qui 

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/> 
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-crosspremise"></a>Tunneling forzato in caso di scenari cross-premise
Per le VM aggiunte a domini locali tramite connessioni da sito a sito, da punto a sito o ExpressRoute, è necessario assicurarsi che le impostazioni del proxy Internet vengano distribuite anche per tutti gli utenti in tali macchine virtuali. Per impostazione predefinita, il software in esecuzione in queste VM o gli utenti che usano un browser per accedere a Internet non passano attraverso il proxy aziendale, ma si connettono direttamente a Internet tramite Azure. L'impostazione del proxy, tuttavia, non costituisce una soluzione perfetta per l'indirizzamento del traffico attraverso il proxy aziendale, poiché il software e i servizi sono responsabili della verifica del proxy. Se il software in esecuzione nella VM non esegue la verifica o se un amministratore modifica le impostazioni, il traffico verso Internet può essere reindirizzato di nuovo direttamente a Internet tramite Azure. 

Per evitare questo problema, è possibile configurare il tunneling forzato con connettività da sito a sito tra l'ambiente locale e Azure. La descrizione dettagliata della funzionalità di tunneling forzato è stata pubblicata qui <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/> 

Il tunneling forzato con ExpressRoute viene abilitato dai clienti mediante l'annuncio di una route predefinita tramite le sessioni di peering BGP ExpressRoute.

#### <a name="summary-of-azure-networking"></a>Riepilogo dei servizi di rete di Azure
Questo capitolo include molti aspetti importanti relativi ai servizi di rete di Azure. Ecco un riepilogo degli aspetti principali:

* Le reti virtuali di Azure consentono di configurare la rete in base alle proprie esigenze.
* È possibile usare le reti virtuali di Azure per assegnare intervalli di indirizzi IP a macchine virtuali o assegnare indirizzi IP fissi alle VM.
* Per configurare una connessione da sito a sito o da punto a sito, è necessario creare prima di tutto una rete virtuale di Azure.
* Dopo la distribuzione di una macchina virtuale, non è più possibile cambiare la rete virtuale assegnata alla VM.

### <a name="quotas-in-azure-virtual-machine-services"></a>Quote nei servizi Macchine virtuali di Azure
È importante chiarire che l'infrastruttura di archiviazione e di rete è condivisa tra macchine virtuali che eseguono un'ampia varietà di servizi nell'infrastruttura di Azure. Analogamente ai data center personalizzati del cliente, l'overprovisioning di alcune risorse dell'infrastruttura viene effettuato solo parzialmente. La piattaforma Microsoft Azure usa quote di dischi, CPU, rete e di altro tipo per limitare l'utilizzo di risorse e preservare prestazioni coerenti e deterministiche.  I diversi tipi di VM (A5, A6 e così via) prevedono quote diverse per il numero di dischi e per le risorse di CPU, RAM e rete.

> [!NOTE]
> Le risorse di CPU e di memoria dei tipi di macchine virtuali supportati da SAP vengono preallocate nei nodi host. Dopo la distribuzione della VM, le risorse sull'host saranno quindi disponibili in base a quanto definito dal tipo di macchina virtuale.
> 
> 

Quando esegue la pianificazione per SAP e se ne definiscono le dimensioni in soluzioni di Azure, è necessario tenere in considerazione le quote per ogni dimensione di macchina virtuale.  Una descrizione delle quote delle VM è disponibile [qui][virtual-machines-sizes].

Le quote descritte rappresentano i valori massimi teorici.  Il limite di IOPS per ogni disco rigido virtuale può essere raggiunto con valori I/O ridotti (8 KB), ma è possibile che non venga raggiunto con valori I/O elevati (1 MB).  Il limite di IOPS viene applicato a livello dei singoli dischi rigidi virtuali.

È possibile usare l'albero delle decisioni seguente come strumento approssimativo per decidere se un sistema SAP è adatto ai servizi inclusi in Macchine virtuali di Azure e alle relative funzionalità oppure se un sistema esistente deve essere configurato in modo diverso per essere distribuito in Azure.

![Albero delle decisioni relativo alla possibilità di distribuire SAP in Azure][planning-guide-figure-700]

**Passaggio 1**: le informazioni più importanti da cui iniziare sono i requisiti SAPS per uno specifico sistema SAP. I requisiti SAPS devono essere suddivisi tra la parte DBMS e la parte dell'applicazione SAP, anche se il sistema SAP è già distribuito in locale in una configurazione a 2 livelli. Per i sistemi esistenti, i valori SAPS correlati all'hardware in uso spesso possono essere determinati o stimati in base agli attuali benchmark SAP. I risultati sono disponibili qui: <http://global.sap.com/campaigns/benchmark/index.epx>. Per i sistemi SAP appena distribuiti, è necessario eseguire un esercizio di ridimensionamento per determinare i requisiti SAPS del sistema.
Per informazioni sul ridimensionamento di SAP in Azure, vedere anche questo post di blog e il documento allegato: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Passaggio 2**: per i sistemi esistenti, è necessario misurare il volume e le operazioni I/O al secondo nel server DBMS. Per i sistemi appena pianificati, l'esercizio di ridimensionamento per il nuovo sistema deve fornire anche un'idea approssimativa dei requisiti I/O sul lato DBMS. In caso di dubbi, è opportuno eseguire un modello di verifica.

**Passaggio 3**: confrontare i requisiti SAPS per il server DBMS con i valori SAPS forniti dai diversi tipi di VM di Azure. Le informazioni sui valori SAPS dei diversi tipi di VM di Azure sono documentate nella nota SAP [1928533]. È opportuno concentrarsi prima di tutto sulla macchina virtuale DBMS perché, nella maggior parte delle distribuzioni, il livello di database è il livello di un sistema SAP NetWeaver che viene ridimensionato. Al contrario, il livello applicazione SAP può essere scalato orizzontalmente. Se nessuno dei tipi di macchine virtuali di Azure supportati da SAP riesce a offrire i valori SAPS richiesti, il carico di lavoro del sistema SAP pianificato non può essere eseguito in Azure. È necessario distribuire il sistema locale o modificare il volume del carico di lavoro per il sistema.

**Passaggio 4**: come documentato [qui][virtual-machines-sizes], Azure applica una quota di operazioni di I/O al secondo a ogni disco rigido virtuale a prescindere dall'uso dell'archiviazione Standard o Premium. A seconda del tipo di macchina virtuale, il numero di dischi rigidi virtuali che può essere montato varia. Di conseguenza, è possibile calcolare un numero massimo di operazioni di I/O al secondo che può essere ottenuto con i singoli tipi di macchine virtuali. A seconda del layout del file di database, è possibile eseguire lo striping dei dischi rigidi virtuali in modo da ottenere un solo volume nel sistema operativo guest. Tuttavia, se il volume corrente di operazioni di I/O al secondo di un sistema SAP distribuito supera i limiti calcolati per il tipo di macchina virtuale più esteso di Azure e se non è possibile alcuna compensazione aggiungendo altra memoria, gli effetti sul carico di lavoro del sistema SAP possono essere significativi. In questi casi, si arriva a un punto in cui non è opportuno distribuire il sistema in Azure.

**Passaggio 5**: soprattutto nei sistemi SAP distribuiti in locale in configurazioni a 2 livelli, è probabile che il sistema debba essere configurato in Azure con una configurazione a 3 livelli. In questo passaggio è necessario controllare se nel livello applicazione SAP è presente un componente che non può essere scalato orizzontalmente e non può essere adattato per le risorse di CPU e memoria offerte dai diversi tipi di macchine virtuali di Azure. Se è effettivamente presente un componente di questo tipo, il sistema SAP e il relativo carico di lavoro non possono essere distribuiti in Azure. Tuttavia, se è possibile scalare orizzontalmente i componenti dell'applicazione SAP in più macchine virtuali di Azure, il sistema può essere distribuito in Azure. 

**Passaggio 6**: se i componenti dei livelli applicazione SAP e DBMS possono essere eseguiti nelle VM di Azure, è necessario definire la configurazione per:

* Numero di macchine virtuali di Azure
* Tipi di macchine virtuali per i singoli componenti
* Numero di dischi rigidi virtuali nella macchina virtuale DBMS per fornire un numero sufficiente di operazioni di I/O al secondo

## <a name="managing-azure-assets"></a>Gestione degli asset di Azure
### <a name="azure-portal"></a>Portale di Azure
Il portale di Azure è una delle tre interfacce con cui gestire le distribuzioni di macchine virtuali di Azure. Le attività di gestione di base, come la distribuzione di macchine virtuali da immagini, possono essere eseguite dal portale di Azure. Un'altra attività che il portale di Azure riesce a gestire in modo ottimale è la creazione di account di archiviazione, reti virtuali e altri componenti di Azure. Tuttavia, funzionalità quali il caricamento di dischi rigidi virtuali da locale ad Azure o la copia di un disco rigido virtuale in Azure sono attività che richiedono strumenti di terze parti o l'amministrazione con PowerShell o l'interfaccia della riga di comando.

![Portale di Microsoft Azure: panoramica delle macchine virtuali][planning-guide-figure-800]

[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-networks-create-vnet-arm-pportal/>)
[comment]: <> (MSSedusch * <https://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/>)

Le attività di amministrazione e configurazione per l'istanza della macchina virtuale possono essere eseguite dal portale di Azure. 

Oltre a riavviare e arrestare una macchina virtuale, è possibile collegare, scollegare e creare dischi dati per l'istanza della macchina virtuale, acquisire l'istanza per la preparazione dell'immagine e configurare le dimensioni dell'istanza della macchina virtuale.

Il portale di Azure fornisce funzionalità di base per distribuire e configurare le macchine virtuali e molti altri servizi di Azure. Tuttavia non tutte le funzionalità disponibili sono coperte dal portale di Azure, ad esempio, non è possibile:

* Caricare i dischi rigidi virtuali in Azure
* Copiare macchine virtuali

[comment]: <> (MShermannd TODO what about automation service for SAP VMs ? )
[comment]: <> (MSSedusch deployment of multiple VMs os meanwhile possible)
[comment]: <> (MSSedusch Also any type of automation regarding deployment is not possible with the Azure portal. Attività quali la distribuzione con script di più macchine virtuali non possono essere eseguite con il portale di Azure.) 

### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Gestione con i cmdlet di Microsoft Azure PowerShell
Windows PowerShell è un framework potente ed estensibile che è stato ampiamente adottato dai clienti che distribuiscono grandi quantità di sistemi in Azure. Dopo l'installazione dei cmdlet di PowerShell in un desktop, un portatile o una stazione di gestione dedicata, i cmdlet di PowerShell possono essere eseguiti in modalità remota.

Il processo per abilitare un PC desktop/portatile locale per l'utilizzo dei cmdlet di Azure PowerShell e per configurare i cmdlet per l'utilizzo con le sottoscrizioni di Azure è descritto in [questo articolo][powershell-install-configure]. 

Per istruzioni più dettagliate su come installare, aggiornare e configurare i cmdlet di Azure PowerShell, vedere anche [questo capitolo della guida alla distribuzione][deployment-guide-4.1].

In base all'esperienza dei clienti, finora PowerShell (PS) è risultato senza dubbio lo strumento più potente per distribuire le macchine virtuali e per creare istruzioni personalizzate durante la distribuzione di macchine virtuali. Tutti i clienti che eseguono istanze SAP in Azure usano i cmdlet di PS per integrare le attività di gestione che eseguono nel portale di Azure oppure usano i cmdlet in modo esclusivo per gestire le distribuzioni in Azure. I cmdlet specifici di Azure condividono la stessa convenzione di denominazione di oltre 2000 altri cmdlet correlati a Windows, quindi l'uso di questi cmdlet risulta estremamente semplice per gli amministratori di Windows.

Per esempi, vedere qui: <http://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>

[comment]: <> (MShermannd TODO describe new CLI command when tested )
L'estensione di monitoraggio di Azure per SAP (vedere il capitolo [Soluzione di monitoraggio di Azure per SAP][planning-guide-9.1] in questo documento) può essere distribuita solo con PowerShell o l'interfaccia della riga di comando. È quindi necessario installare e configurare PowerShell o l'interfaccia della riga di comando quando si distribuisce o si amministra un sistema SAP NetWeaver in Azure.  

Con l'introduzione di nuove funzionalità in Azure, verranno aggiunti nuovi cmdlet di PS che richiedono un aggiornamento dei cmdlet. Per questo motivo, è opportuno visitare il sito Download di Azure <https://azure.microsoft.com/downloads/> almeno una volta al mese per scaricare una nuova versione dei cmdlet. La nuova versione viene semplicemente installata su quella precedente.

Per un elenco generale dei comandi di PowerShell correlati ad Azure, vedere <https://msdn.microsoft.com/library/azure/dn708514.aspx>. 

### <a name="management-via-microsoft-azure-cli-commands"></a>Gestione con i comandi dell'interfaccia della riga di comando di Microsoft Azure
PowerShell potrebbe non essere la soluzione ottimale per i clienti che usano Linux e vogliono gestire le risorse di Azure. Microsoft offre l'interfaccia della riga di comando di Azure come alternativa.
L'interfaccia della riga di comando di Azure fornisce un insieme di comandi open source e multipiattaforma per utilizzare la piattaforma Azure. Fornisce gran parte delle funzionalità disponibili nel portale di Azure.

Per informazioni sull'installazione, la configurazione e l'uso dei comandi dell'interfaccia della riga di comando per eseguire attività di Azure, vedere

* [Installare l'interfaccia della riga di comando di Azure][xplat-cli]
* [Distribuire e gestire le macchine virtuali usando modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure][virtual-machines-linux-cli-deploy-templates]
* [Usare l'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Azure Resource Manager][xplat-cli-azure-resource-manager]

Vedere anche il capitolo [Interfaccia della riga di comando di Azure per VM Linux][deployment-guide-4.5.2] nella [guida alla distribuzione][planning-guide] per informazioni su come usare l'interfaccia della riga di comando di Azure per distribuire l'estensione di monitoraggio di Azure per SAP.

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Diversi modi per distribuire le macchine virtuali per SAP in Azure
In questo capitolo vengono descritte le diverse modalità per distribuire una macchina virtuale in Azure. Gli argomenti trattati includono le varie procedure di preparazione e la gestione dei dischi rigidi virtuali e delle macchine virtuali in Azure.

### <a name="deployment-of-vms-for-sap"></a>Distribuzione di VM per SAP
Microsoft Azure offre diversi modi per distribuire le VM e i dischi associati. È quindi molto importante comprendere le differenze perché le operazioni di preparazione delle VM possono variare in base al metodo di distribuzione. In generale, verranno analizzati gli scenari seguenti:

#### <a name="a-name4d175f1b735341379d2f817683c26e53amoving-a-vm-from-onpremises-to-azure-with-a-nongeneralized-disk"></a><a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Spostamento di una VM da locale ad Azure con un disco non generalizzato
Si può pianificare di spostare un sistema SAP specifico da locale ad Azure. A tale scopo è possibile caricare in Azure il disco rigido virtuale contenente il sistema operativo, i file binari SAP e gli eventuali file binari DBMS nonché i dischi rigidi virtuali con i file di dati e di log del sistema DBMS. A differenza dello [scenario 2 seguente][planning-guide-5.1.2], il nome host, il SID di SAP e gli account utente SAP vengono mantenuti nella VM di Azure perché sono stati configurati nell'ambiente locale. Di conseguenza, non è necessario generalizzare l'immagine. Per informazioni sulla procedura di preparazione locale e sul caricamento di dischi rigidi virtuali o VM non generalizzate in Azure, vedere il capitolo [Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato][planning-guide-5.2.1] di questo documento. Per istruzioni dettagliate sulla distribuzione di un'immagine di questo tipo in Azure, vedere il capitolo [Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP][deployment-guide-3.4] nella [guida alla distribuzione][deployment-guide-3.4].

#### <a name="a-namee18f7839c0e24385b1e64538453a285cadeploying-a-vm-with-a-customer-specific-image"></a><a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Distribuzione di una VM con un'immagine specifica del cliente
Le immagini disponibili in Azure Marketplace potrebbero non soddisfare le esigenze a causa di specifici requisiti di patch riguardanti la versione del sistema operativo o DBMS in uso. Per questo motivo, potrebbe essere necessario creare una macchina virtuale usando una propria immagine "privata" di macchina virtuale del sistema operativo o DBMS che in seguito potrà essere distribuita diverse volte Per preparare un'immagine "privata" per la duplicazione, devono essere considerati i seguenti elementi:

- - -
> ![Windows][Logo_Windows]  Windows
> 
> Le impostazioni di Windows, ad esempio il SID e il nome host di Windows, devono essere astratte o generalizzate nella macchina virtuale locale con il comando sysprep.
> 
> 

[comment]: <> (MSSedusch > See more details here :)
[comment]: <> (MShermannd TODO first link is about classic model. Didn't find an Azure docu article)
[comment]: <> (MSSedusch > <https://azure.microsoft.com/documentation/articles/virtual-machines-create-upload-vhd-windows-server/>)
[comment]: <> (MSSedusch > <http://blogs.technet.com/b/blainbar/archive/2014/09/12/modernizing-your-infrastructure-with-hybrid-cloud-using-custom-vm-images-and-resource-groups-in-microsoft-azure-part-21-blain-barton.aspx>)
>

> ![ Linux][Logo_Linux]  Linux
> 
> Per preparare un disco rigido virtuale da caricare in Azure, seguire la procedura descritta in questi articoli per [SUSE][virtual-machines-linux-create-upload-vhd-suse] o [Red Hat][virtual-machines-linux-redhat-create-upload-vhd].
> 
> 

- - -
Se sono già stati installati contenuti SAP nella VM locale, in particolare per sistemi a 2 livelli, è possibile adattare le impostazioni di sistema SAP dopo la distribuzione della VM di Azure con la procedura di ridenominazione dell'istanza supportata da SAP Software Provisioning Manager (nota SAP [1619720]). Per informazioni sulla procedura di preparazione locale e sul caricamento di una VM generalizzata in Azure, vedere i capitoli [Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP][planning-guide-5.2.2] e [Caricamento di un disco rigido virtuale da locale ad Azure][planning-guide-5.3.2] di questo documento. Per istruzioni dettagliate sulla distribuzione di un'immagine di questo tipo in Azure, vedere il capitolo [Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP][deployment-guide-3.3] nella [guida alla distribuzione].

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Distribuzione di una VM da Azure Marketplace
Per distribuire la macchina virtuale si sceglie di usare un'immagine di VM offerta da Microsoft o terze parti in Azure Marketplace. Dopo aver distribuito la VM in Azure, si installa il software SAP e/o DBMS all'interno della VM usando le stesse linee guida e gli stessi strumenti usati in un ambiente locale. Per una descrizione più dettagliata della distribuzione, vedere il capitolo [Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP][deployment-guide-3.2] nella [guida alla distribuzione][deployment-guide].

### <a name="a-name6ffb9f41a29240bf9e708204448559e7apreparing-vms-with-sap-for-azure"></a><a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Preparazione di VM con SAP per Azure
Prima di caricare le macchine virtuali in Azure è necessario assicurarsi che le VM e i dischi rigidi virtuali soddisfino determinati requisiti. Esistono piccole differenze a seconda del metodo di distribuzione che viene usato. 

#### <a name="a-name1b287330944b495d9ea794b83aff73efapreparation-for-moving-a-vm-from-onpremises-to-azure-with-a-nongeneralized-disk"></a><a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato
Un metodo di distribuzione comune consiste nello spostare in Azure una macchina virtuale esistente che esegue un sistema SAP in locale. Questa macchina virtuale e il sistema SAP nella VM devono essere eseguiti in Azure usando lo stesso nome host e, probabilmente, lo stesso SID di SAP. In questo caso il sistema operativo guest della macchina virtuale non deve essere generalizzato per più distribuzioni. Se la rete locale è stata estesa in Azure (vedere il capitolo [Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale][planning-guide-2.2] in questo documento), all'interno della VM si possono usare anche gli stessi account di dominio usati in precedenza in locale. 

I requisiti per la preparazione del disco della VM di Azure sono:

* Il disco rigido virtuale che contiene il sistema operativo potrebbe avere inizialmente una dimensione massima di soli 127 GB. Questa limitazione è stata eliminata alla fine del mese di marzo 2015. Ora il disco rigido virtuale che contiene il sistema operativo può raggiungere 1 TB come qualsiasi altro disco rigido virtuale ospitato dell'archiviazione di Azure.

[comment]: <> (MShermannd  TODO have to check if CLI also converts to static )
* Deve essere nel formato disco rigido virtuale fisso. Il disco o i dischi rigidi virtuali dinamici in formato VHDx non sono ancora supportati in Azure. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Anche i dischi rigidi virtuali montati nella macchina virtuale e che devono essere montati nuovamente nella macchina virtuale di Azure devono essere in formato disco rigido virtuale fisso. La limitazione per le dimensioni massime del disco del sistema operativo si applica anche i dischi dati. La dimensione massima dei dischi rigidi virtuali è di 1 TB. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Aggiungere un altro account locale con privilegi di amministratore che può essere usato dal supporto tecnico Microsoft o assegnato come contesto per i servizi le e applicazioni da eseguire finché non viene distribuita la macchina virtuale e non possono essere usati utenti più adatti.
* Per i casi d'uso di uno scenario di distribuzione solo cloud (vedere il capitolo [Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti][planning-guide-2.1] di questo documento) in combinazione con questo metodo di distribuzione, gli account di dominio potrebbero non funzionare dopo la distribuzione del disco di Azure in Azure. Questo vale soprattutto per gli account usati per eseguire servizi come le applicazioni DBMS o SAP. È quindi necessario sostituire questi account di dominio con account locali della macchina virtuale ed eliminare gli account di dominio locali nella macchina virtuale. Mantenere gli utenti di dominio locali nell'immagine di VM non è un problema in caso di distribuzione della VM nello scenario cross-premise, come descritto nel capitolo [Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale][planning-guide-2.2] di questo documento.
* Se sono stati usati account di dominio come account di accesso o utenti DBMS durante l'esecuzione del sistema locale e queste macchine virtuali avrebbero dovuto essere distribuite in scenari solo cloud, gli utenti del dominio devono essere eliminati. È necessario assicurarsi che l'amministratore locale e un altro utente locale della macchina virtuale vengano aggiunti come account di accesso/utente in DBMS con il ruolo di amministratori.
* Aggiungere altri account locali come quelli che potrebbero essere richiesti per lo scenario di distribuzione specifico.

- - -
> ![ Windows][Logo_Windows] Windows
> 
> In questo scenario non è necessaria la generalizzazione (sysprep) della macchina virtuale per caricare e distribuire la macchina virtuale in Azure.
> Verificare che l'unità D:\ non sia usata. Impostare il montaggio automatico dei dischi collegati come descritto nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3] di questo documento.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> In questo scenario non è necessaria la generalizzazione (waagent -deprovision) della macchina virtuale per caricare e distribuire la macchina virtuale in Azure.
> Assicurarsi che /mnt/resource non sia usato e che TUTTI i dischi siano montati con UUID. Per il disco del sistema operativo assicurarsi che la voce bootloader rifletta anche il montaggio basato su UUID.
> 
> 

- - -
#### <a name="a-name57f32b1c0cba4e57ab6ec39fe22b6ec3apreparation-for-deploying-a-vm-with-a-customer-specific-image-for-sap"></a><a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP
I file VHD che contengono un sistema operativo generalizzato vengono archiviati anche in contenitori negli account di archiviazione di Azure. È possibile distribuire una nuova VM da questo tipo di disco rigido virtuale dell'immagine facendovi riferimento come disco rigido virtuale di origine nei file di modello di distribuzione, come descritto nel capitolo [Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP][deployment-guide-3.3] della [guida alla distribuzione][deployment-guide]. 

I requisiti per la preparazione dell'immagine di VM di Azure sono:

* Il disco rigido virtuale che contiene il sistema operativo potrebbe avere inizialmente una dimensione massima di soli 127 GB. Questa limitazione è stata eliminata alla fine del mese di marzo 2015. Ora il disco rigido virtuale che contiene il sistema operativo può raggiungere 1 TB come qualsiasi altro disco rigido virtuale ospitato dell'archiviazione di Azure.

[comment]: <> (MShermannd  TODO have to check if CLI also converts to static )
* Deve essere nel formato disco rigido virtuale fisso. Il disco o i dischi rigidi virtuali dinamici in formato VHDx non sono ancora supportati in Azure. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Anche i dischi rigidi virtuali montati nella macchina virtuale e che devono essere montati nuovamente nella macchina virtuale di Azure devono essere in formato disco rigido virtuale fisso. La limitazione per le dimensioni massime del disco del sistema operativo si applica anche i dischi dati. La dimensione massima dei dischi rigidi virtuali è di 1 TB. I dischi rigidi virtuali dinamici vengono convertiti in dischi rigidi virtuali statici quando si carica il disco rigido virtuale con i cmdlet di PowerShell o l'interfaccia della riga di comando
* Dato che gli utenti di dominio registrati come utenti nella VM non esistono in uno scenario solo cloud (vedere il capitolo [Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti][planning-guide-2.1] di questo documento), i servizi che usano questi account di dominio potrebbero non funzionare dopo la distribuzione dell'immagine in Azure. Questo vale soprattutto per gli account usati per eseguire servizi come le applicazioni DBMS o SAP. È quindi necessario sostituire questi account di dominio con account locali della macchina virtuale ed eliminare gli account di dominio locali nella macchina virtuale. Mantenere gli utenti di dominio locali nell'immagine di VM potrebbe non essere un problema in caso di distribuzione della VM nello scenario cross-premise, come descritto nel capitolo [Cross-premise: distribuzione di una o più VM SAP in Azure che devono essere completamente integrate con la rete locale][planning-guide-2.2] di questo documento.
* Aggiungere un altro account locale con privilegi di amministratore che può essere usato dal supporto tecnico Microsoft per l'analisi dei problemi o assegnato come contesto per i servizi le e applicazioni da eseguire finché non viene distribuita la macchina virtuale e non possono essere usati utenti più adatti.
* Nelle distribuzioni solo cloud e se sono stati usati account di dominio come account di accesso o utenti DBMS durante l'esecuzione del sistema locale, gli utenti del dominio devono essere eliminati. È necessario assicurarsi che l'amministratore locale e un altro utente locale della macchina virtuale vengano aggiunti come account di accesso/utente in DBMS con il ruolo di amministratori.
* Aggiungere altri account locali come quelli che potrebbero essere richiesti per lo scenario di distribuzione specifico.
* Se l'immagine contiene un'installazione di SAP NetWeaver ed è probabile che il nome host venga modificato rispetto al nome originale al momento della distribuzione di Azure, si consiglia di copiare le versioni più recenti del DVD di SAP Software Provisioning Manager nel modello. Questo consentirà di usare facilmente la funzionalità di ridenominazione SAP fornita per adattare il nome host modificato e/o per modificare il SID del sistema SAP all'interno dell'immagine di VM distribuita non appena viene avviata una nuova copia.

- - -
> ![ Windows][Logo_Windows] Windows
> 
> Verificare che l'unità D:\ non sia usata. Impostare il montaggio automatico dei dischi collegati come descritto nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3] di questo documento.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Assicurarsi che /mnt/resource non sia usato e che TUTTI i dischi siano montati con UUID. Per il disco del sistema operativo assicurarsi che la voce bootloader rifletta anche il montaggio basato su UUID.
> 
> 

- - -
* L'interfaccia utente grafica (GUI) SAP può essere preinstallata in questo modello per scopi di amministrazione e installazione.
* È possibile installare altri software necessari per eseguire correttamente le macchine virtuali in scenari cross-premise purché siano compatibili con la ridenominazione della macchina virtuale.

Se la macchina virtuale è stata preparata in modo sufficiente per essere generica ed eventualmente indipendente da account/utenti non disponibili nello scenario di distribuzione di Azure di destinazione, viene eseguito l'ultimo passaggio di preparazione per la generalizzazione di questa immagine.

##### <a name="generalizing-a-vm"></a>Generalizzazione di una macchina virtuale
- - -
[comment]: <> (MShermannd  TODO have to find better articles / docu about generalizing the VMs for ARM )
> ![Windows][Logo_Windows]  Windows
> 
> L'ultimo passaggio consiste nell'accedere a una macchina virtuale con un account amministratore. Aprire una finestra di comando di Windows come "amministratore". Andare in …\windows\system32\sysprep ed eseguire sysprep.exe.
> Viene visualizzata una finestra di piccole dimensioni. È importante selezionare l'opzione "Generalizza", deselezionata per impostazione predefinita, e modificare l'opzione di arresto dall'impostazione predefinita "Riavvia" ad "Arresta". Questa procedura presuppone che il processo sysprep venga eseguito in locale nel sistema operativo guest di una macchina virtuale.
> Per eseguire la procedura con una VM già in esecuzione in Azure, seguire la procedura descritta in [questo articolo][virtual-machines-windows-capture-image].
> 
> ![ Linux][Logo_Linux]  Linux
> 
> [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][virtual-machines-linux-capture-image]
> 
> 

- - -
### <a name="transferring-vms-and-vhds-between-onpremises-to-azure"></a>Trasferimento di macchine virtuali e dischi rigidi virtuali tra il sistema locale e Azure
Il caricamento di immagini di VM e dischi in Azure con il portale di Azure non è consentito, quindi è necessario usare i cmdlet di Azure PowerShell o l'interfaccia della riga di comando. In alternativa, è possibile usare lo strumento "AzCopy". Lo strumento può copiare i dischi rigidi virtuali tra il sistema locale e Azure, in entrambe le direzioni. Può anche copiare i dischi rigidi virtuali tra le diverse aree di Azure. Per il download e l'uso di AzCopy, vedere questo [documento][storage-use-azcopy].

Una terza alternativa prevede l'uso di diversi strumenti per l'interfaccia utente grafica di terze parti. Tuttavia, assicurarsi che questi strumenti supportino i BLOB di pagine di Azure. In questo caso è necessario usare l'archiviazione BLOB di pagine di Azure. Le differenze sono descritte qui: <https://msdn.microsoft.com/library/windowsazure/ee691964.aspx>. Anche gli strumenti forniti da Azure sono molto efficaci per la compressione delle macchine virtuali e dei dischi rigidi virtuali da caricare. Si tratta di un fattore importante perché una compressione efficace riduce il tempo di caricamento, che varia comunque in base al collegamento per il caricamento in Internet dalla struttura locale e all'area di distribuzione di Azure di destinazione. È ragionevole supporre che il caricamento di una macchina virtuale o di un disco rigido virtuale da una località europea ai data center di Azure basati negli Stati Uniti richiederà più tempo rispetto al caricamento delle stesse macchine virtuali/dischi rigidi virtuali in data center di Azure europei. 

#### <a name="a-namea43e40e61acc463398168f095d5a7b6aauploading-a-vhd-from-onpremises-to-azure"></a><a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Caricamento di un disco rigido virtuale da locale ad Azure
Per caricare una VM o un disco rigido virtuale esistente dalla rete locale, è necessario che siano soddisfatti i requisiti elencati nel capitolo [Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato][planning-guide-5.2.1] di questo documento.

Questa macchina virtuale non deve essere generalizzata e può essere caricata nello stato e nella forma in cui si trova dopo l'arresto sul lato locale. Lo stesso vale per i dischi rigidi virtuali aggiuntivi che non contengono sistemi operativi. 

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Caricamento di un disco rigido virtuale e conversione in un disco di Azure
Lo scopo di questo scenario è caricare un file VHD, con o senza sistema operativo incluso, e montarlo in una macchina virtuale come disco dati o usarlo come disco del sistema operativo. Il processo si articola in più passaggi 

**PowerShell**

* Accedere alla sottoscrizione con *Login-AzureRmAccount*
* Impostare la sottoscrizione del contesto con *Set-AzureRmContext* e il parametro SubscriptionId o SubscriptionName. Vedere <https://msdn.microsoft.com/library/mt619263.aspx>
* Caricare il disco rigido virtuale in un account di archiviazione di Azure con *Add-AzureRmVhd*. Vedere <https://msdn.microsoft.com/library/mt603554.aspx>
* Impostare il disco del sistema operativo di una nuova configurazione di VM sul disco rigido virtuale con *Set-AzureRmVMOSDisk*. Vedere <https://msdn.microsoft.com/library/mt603746.aspx>
* Creare una nuova VM dalla configurazione di VM con *New-AzureRmVM*. Vedere <https://msdn.microsoft.com/library/mt603754.aspx>
* Aggiungere un disco dati a una nuova VM con *Add-AzureRmVMDataDisk*. Vedere <https://msdn.microsoft.com/library/mt603673.aspx>

**Interfaccia della riga di comando di Azure**

* Passare alla modalità Azure Resource Manager con *azure config mode arm*
* Accedere alla sottoscrizione con *azure login*
* Selezionare la sottoscrizione con *azure account set `<subscription name or id`>*
* Caricare il disco rigido virtuale con *azure storage blob upload*. Vedere [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure][storage-azure-cli]
* Creare una nuova VM specificando il disco rigido virtuale caricato come disco del sistema operativo con *azure vm create* e il parametro -d
* Aggiungere un disco dati a una nuova VM con *vm disk attach-new*

**Modello**

* Caricare il disco rigido virtuale con Powershell o con l'interfaccia della riga di comando di Azure
* Distribuire la VM con un modello JSON facendo riferimento al disco rigido virtuale, come mostrato in [questo modello JSON di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json).

#### <a name="deployment-of-a-vm-image"></a>Distribuzione di un'immagine di VM
Per caricare una VM o un disco rigido virtuale esistente dalla rete locale per usarlo come immagine di VM di Azure, è necessario che siano soddisfatti i requisiti elencati nel capitolo [Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP][planning-guide-5.2.2] di questo documento.

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la VM. Vedere [Come acquisire una macchina virtuale Windows nel modello di distribuzione Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][virtual-machines-linux-capture-image-capture]
* Accedere alla sottoscrizione con *Login-AzureRmAccount*
* Impostare la sottoscrizione del contesto con *Set-AzureRmContext* e il parametro SubscriptionId o SubscriptionName. Vedere <https://msdn.microsoft.com/library/mt619263.aspx>
* Caricare il disco rigido virtuale in un account di archiviazione di Azure con *Add-AzureRmVhd*. Vedere <https://msdn.microsoft.com/library/mt603554.aspx>
* Impostare il disco del sistema operativo di una nuova configurazione di VM sul disco rigido virtuale con *Set-AzureRmVMOSDisk -SourceImageUri -CreateOption fromImage*. Vedere <https://msdn.microsoft.com/library/mt603746.aspx>
* Creare una nuova VM dalla configurazione di VM con *New-AzureRmVM*. Vedere <https://msdn.microsoft.com/library/mt603754.aspx>

**Interfaccia della riga di comando di Azure**

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la VM. Vedere [Come acquisire una macchina virtuale Windows nel modello di distribuzione Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][virtual-machines-linux-capture-image-capture] per Linux
* Passare alla modalità Azure Resource Manager con *azure config mode arm*
* Accedere alla sottoscrizione con *azure login*
* Selezionare la sottoscrizione con *azure account set `<subscription name or id`>*
* Caricare il disco rigido virtuale con *azure storage blob upload*. Vedere [Uso dell'interfaccia della riga di comando di Azure con Archiviazione di Azure][storage-azure-cli]
* Creare una nuova VM specificando il disco rigido virtuale caricato come disco del sistema operativo con *azure vm create* e il parametro -Q

**Modello**

* Usare *sysprep* in Windows o *waagent-deprovision* in Linux per generalizzare la VM. Vedere [Come acquisire una macchina virtuale Windows nel modello di distribuzione Resource Manager][virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture] o [Come acquisire una macchina virtuale Linux da usare come modello di Resource Manager][virtual-machines-linux-capture-image-capture] per Linux
* Caricare il disco rigido virtuale con Powershell o con l'interfaccia della riga di comando di Azure
* Distribuire la VM con un modello JSON facendo riferimento al disco rigido virtuale dell'immagine, come mostrato in [questo modello JSON di esempio](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-from-user-image/azuredeploy.json).

#### <a name="downloading-vhds-to-onpremises"></a>Download dei dischi rigidi virtuali in locale
L'infrastruttura distribuita come servizio (IaaS) di Azure non consente solo di caricare dischi rigidi virtuali e sistemi SAP, ma anche di spostare i sistemi SAP da Azure ai sistemi locali.

Durante il download, i dischi rigidi virtuali non possono essere attivi. Anche quando si scaricano i dischi rigidi virtuali montati nelle macchine virtuali, la VM deve essere arrestata. Se si vuole scaricare solo il contenuto del database da usare per impostare un nuovo sistema locale e se si vuole che il sistema in Azure resti operativo durante il download e l'installazione del nuovo sistema, è possibile evitare un lungo tempo di inattività eseguendo un backup compresso del database in un disco rigido virtuale e scaricando solo questo disco rigido virtuale invece della macchina virtuale di base del sistema operativo.

#### <a name="powershell"></a>PowerShell
Dopo aver arrestato il sistema SAP e la macchina virtuale, è possibile usare il cmdlet di PowerShell Save-AzureRmVhd nella destinazione locale per scaricare nuovamente i dischi rigidi virtuali nel sistema locale. Per farlo, è necessario l'URL del disco rigido virtuale che si trova nella "sezione Archiviazione" del portale di Azure, raggiungibile dall'account di archiviazione e dal contenitore di archiviazione in cui è stato creato il disco rigido virtuale, ed è necessario sapere dove copiare il disco rigido virtuale.

A questo punto, è possibile usare il comando definendo semplicemente il parametro SourceUri come URL del disco rigido virtuale per il download e LocalFilePath come percorso fisico del disco rigido virtuale, includendo il relativo nome. Il comando può avere un aspetto simile al seguente:

```powerhell
Save-AzureRmVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
```

Per altri dettagli sul cmdlet Save-AzureRmVhd, vedere <https://msdn.microsoft.com/library/mt622705.aspx>. 

#### <a name="cli"></a>CLI
Dopo aver arrestato il sistema SAP e la macchina virtuale, è possibile usare il comando dell'interfaccia della riga di comando di Azure azure storage blob download nella destinazione locale per scaricare nuovamente i dischi rigidi virtuali nel sistema locale. Per farlo, sono necessari il nome e il contenitore del disco rigido virtuale che si trovano nella "sezione Archiviazione" del portale di Azure, raggiungibile dall'account di archiviazione e dal contenitore di archiviazione in cui è stato creato il disco rigido virtuale, ed è necessario sapere dove copiare il disco rigido virtuale.

A questo punto, è possibile usare il comando definendo semplicemente i parametri blob e container del disco rigido virtuale per il download e la destinazione come percorso fisico di destinazione del disco rigido virtuale, includendo il relativo nome. Il comando può avere un aspetto simile al seguente:

```
azure storage blob download --blob <name of the VHD to download> --container <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --destination <destination of the VHD to download> 
```

### <a name="transferring-vms-and-vhds-within-azure"></a>Trasferimento di macchine virtuali e dischi rigidi virtuali in Azure
#### <a name="copying-sap-systems-within-azure"></a>Copia di sistemi SAP in Azure
È probabile che un sistema SAP o anche un server DBMS dedicato che supporta un livello applicazione SAP sia costituito da diversi dischi rigidi virtuali che contengono il sistema operativo con i file binari o i file di dati e di log del database SAP. Le funzionalità di copia e di salvataggio su disco dei dischi rigidi virtuali in Azure non hanno un meccanismo di sincronizzazione in grado di eseguire snapshot di più dischi rigidi virtuali in modo sincrono. Quindi, lo stato dei dischi rigidi virtuali copiati o salvati, anche di quelli montati nella stessa macchina virtuale, risulta diverso. Ciò significa che, in uno scenario in cui sono presenti dati e file di log diversi contenuti nei vari dischi rigidi virtuali, il database risulterà incoerente. 

**Conclusione: per copiare o salvare dischi rigidi virtuali che fanno parte della configurazione di un sistema SAP, è necessario arrestare sia il sistema SAP che la VM distribuita. Solo a questo punto si può copiare o scaricare il set di dischi rigidi virtuali per creare una copia del sistema SAP in Azure o in locale.**

I dischi dati vengono archiviati come file VHD nell'account di archiviazione di Azure e possono essere collegati direttamente a una macchina virtuale o essere usati come immagine. In questo caso, il disco rigido virtuale viene copiato in un'altra posizione prima di essere collegato alla macchina virtuale. Il nome completo del file VHD deve essere univoco in Azure. Come già indicato in precedenza, il nome è un tipo di nome in tre parti simile a: 

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

##### <a name="powershell"></a>PowerShell
È possibile usare i cmdlet di Azure PowerShell per copiare un disco rigido virtuale come illustrato in [questo articolo][storage-powershell-guide-full-copy-vhd].

##### <a name="cli"></a>CLI
È possibile usare l'interfaccia della riga di comando di Azure per copiare un disco rigido virtuale come mostrato in [questo articolo][storage-azure-cli-copy-blobs]

##### <a name="azure-storage-tools"></a>Strumenti di archiviazione di Azure
* <http://azurestorageexplorer.codeplex.com/releases/view/125870>

Edizioni professionali degli strumenti di esplorazione di Archiviazione di Azure sono disponibili qui:

* <http://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer> 

La copia di un disco rigido virtuale all'interno di un account di archiviazione è un processo che richiede solo pochi secondi, analogo a quello di un hardware SAN che crea snapshot con copia lenta e copia su scrittura. Dopo aver creato una copia del file VHD è possibile collegarla a una macchina virtuale o usarla come immagine per collegare le copie del file VHD alle macchine virtuali.

##### <a name="powershell"></a>PowerShell
```powershell
# attach a vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption attach
$vm | Update-AzureRmVM

# attach a copy of the vhd to a vm
$vm = Get-AzureRmVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzureRmVMDataDisk -VM $vm -Name newdatadisk -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun e.g. 0> -CreateOption fromImage
$vm | Update-AzureRmVM
```
##### <a name="cli"></a>CLI
```
azure config mode arm 

# attach a vhd to a vm
azure vm disk attach <resource group name> <vm name> <path to vhd>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.
```

#### <a name="a-name9789b07620114afab2feb07a8aba58a1acopying-disks-between-azure-storage-accounts"></a><a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Copia di dischi tra account di archiviazione di Azure
Questa attività non può essere eseguita nel portale di Azure. È possibile usare il cmdlet di Azure PowerShell, l'interfaccia della riga di comando di Azure o un browser di archiviazione di terze parti. I cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando possono creare e gestire i BLOB, inclusa la possibilità di copiare in modo asincrono i BLOB tra gli account di archiviazione e tra le aree all'interno della sottoscrizione di Azure.

##### <a name="powershell"></a>PowerShell
È anche possibile copiare i dischi rigidi virtuali tra sottoscrizioni. Per altre informazioni, vedere [questo articolo][storage-powershell-guide-full-copy-vhd]. 

Il flusso di base della logica dei cmdlet di PS è simile al seguente:

* Creare un contesto per l'account di archiviazione di origine con *New-AzureStorageContext*. Vedere <https://msdn.microsoft.com/library/dn806380.aspx>
* Creare un contesto per l'account di archiviazione di destinazione con *New-AzureStorageContext*. Vedere <https://msdn.microsoft.com/library/dn806380.aspx>
* Avviare la copia con

```powershell
Start-AzureStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Controllare lo stato della copia in un ciclo con

```powershell
Get-AzureStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Collegare il nuovo disco rigido virtuale a una macchina virtuale come descritto in precedenza.

Per trovare alcuni esempi, vedere [questo articolo][storage-powershell-guide-full-copy-vhd]

##### <a name="cli"></a>CLI
* Avviare la copia con

```
  azure storage blob copy start --source-blob <source blob name> --source-container <source container name> --account-name <source storage account name> --account-key <source storage account key> --dest-container <target container name> --dest-blob <target blob name> --dest-account-name <target storage account name> --dest-account-key <target storage account name>
```

* Controllare lo stato della copia in un ciclo con

```
azure storage blob copy show --blob <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Collegare il nuovo disco rigido virtuale a una macchina virtuale come descritto in precedenza.

Per trovare alcuni esempi, vedere [questo articolo][storage-azure-cli-copy-blobs]

### <a name="disk-handling"></a>Gestione del disco
#### <a name="a-name4efec40191e040c08e64f2dceadff646avmvhd-structure-for-sap-deployments"></a><a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>Struttura di VM/dischi rigidi virtuali per le distribuzioni SAP
In teoria la gestione della struttura di una macchina virtuale e dei dischi rigidi virtuali associati dovrebbe essere molto semplice. Nelle installazioni locali, i clienti hanno sviluppato diverse procedure per strutturare un'installazione server. 

* Un disco rigido virtuale di base che contiene il sistema operativo e tutti i file binari di DBMS e/o SAP. Partire da marzo 2015, le dimensioni del disco rigido virtuale possono raggiungere 1 TB invece dei 127 GB precedenti. 
* Uno o più dischi rigidi virtuali che contengono il file di log DBMS del database SAP e il file di log dell'area di archiviazione temporanea di DBMS, se supportato dal sistema DBMS. Se i requisiti per le operazioni di I/O al secondo del log del database sono elevati, è necessario eseguire lo striping di più dischi rigidi virtuali per raggiungere il volume di operazioni di I/O al secondo richiesto. 
* Numero di dischi rigidi virtuali contenenti uno o due file del database SAP e file di dati temporanei di DBMS, se supportati dal sistema DBMS.

![Configurazione di riferimento di una VM IaaS di Azure per SAP][planning-guide-figure-1300]

[comment]: <> (MShermannd  TODO describe Linux structure  )

- - -
> ![Windows][Logo_Windows] Windows
> 
> Molti clienti hanno adottato configurazioni in cui, ad esempio, i file binari SAP e DBMS non sono stati installati nell'unità c:\ in cui è stato installato il sistema operativo. I motivi alla base di questa configurazione sono diversi, ma, a uno sguardo più attento, questa scelta spesso risale a 10-15 anni fa ed è stata determinata dalle dimensioni ridotte delle unità e dalla necessità di ottenere altro spazio per gli aggiornamenti del sistema operativo. Oggi queste condizioni non si verificano quasi più, perché è possibile eseguire il mapping dell'unità c:\ in macchine virtuali o in dischi con volumi di grandi dimensioni. Per mantenere semplice la struttura delle distribuzioni, è consigliabile seguire il modello di distribuzione seguente per i sistemi SAP NetWeaver in Azure
> 
> Il file di paging del sistema operativo Windows deve trovarsi nell'unità D: (disco non persistente) 
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Posizionare il file di scambio di Linux in /mnt /mnt/resource in Linux, come descritto in [questo articolo][virtual-machines-linux-agent-user-guide]. Il file di scambio può essere configurato nel file di configurazione di /etc/waagent.conf dell'agente Linux. Aggiungere o modificare le impostazioni seguenti:
> 
> 

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Per attivare le modifiche, è necessario riavviare l'agente Linux con

```
sudo service waagent restart
```

Per altre informazioni sulle dimensioni consigliate del file di scambio, leggere la nota SAP [1597355]

- - -
Il numero di dischi rigidi virtuali usati per i file di dati DBMS e il tipo di archiviazione di Azure in cui sono ospitati questi dischi rigidi virtuali devono essere determinati dai requisiti per le operazioni di I/O al secondo e dalla latenza richiesta. Le quote esatte sono descritte in [questo articolo][virtual-machines-sizes]

L'esperienza nelle distribuzioni SAP degli ultimi 2 anni ha insegnato alcune lezioni che possono essere riepilogate come segue:

* Il traffico di operazioni di I/O al secondo per i vari file di dati non è sempre lo stesso perché i sistemi esistenti del cliente potrebbero avere file di dati di dimensioni diverse che rappresentano i relativi database SAP. Di conseguenza, è risultato più efficace l'uso di una configurazione RAID in più dischi rigidi virtuali per posizionare i file di dati definiti dai LUN. In alcune situazioni, in particolare con l'archiviazione standard di Azure, la frequenza di operazioni di I/O al secondo ha raggiunto la quota di un singolo disco rigido virtuale nel log delle transazioni DBMS. In questi scenari è consigliabile usare l'archiviazione Premium o, in alternativa, aggregare più dischi rigidi virtuali dell'archiviazione standard con un software RAID.

- - -
> ![ Windows][Logo_Windows] Windows
> 
> * [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure][virtual-machines-sql-server-performance-best-practices]
> 
> ![ Linux][Logo_Linux]  Linux
> 
> * [Configurare RAID software in Linux][virtual-machines-linux-configure-raid]
> * [Configurare LVM in una macchina virtuale Linux in Azure][virtual-machines-linux-configure-lvm]
> * [Segreti dell'archiviazione di Azure e ottimizzazioni I/O di Linux](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
> 
> 

- - -
* L'archiviazione Premium mostra prestazioni notevolmente migliori, soprattutto per le scritture del log delle transazioni critiche. Per gli scenari SAP che devono offrire prestazioni paragonabili a quelle di produzione, si consiglia di adottare serie di macchine virtuali in grado di usare l'archiviazione Premium di Azure.

Tenere presente che il disco rigido virtuale che contiene il sistema operativo, e come consigliato, i file binari di SAP e il database (VM di base), non è più limitato a 127 GB, ma può raggiungere 1 TB. Questo spazio dovrebbe essere sufficiente per contenere tutti i file necessari, inclusi, ad esempio, i log dei processi batch SAP.

Per altri suggerimenti e dettagli, in particolare per le VM DBMS, vedere la [guida alla distribuzione DBMS][dbms-guide]

#### <a name="disk-handling"></a>Gestione del disco
Nella maggior parte degli scenari è necessario creare altri dischi per distribuire il database SAP nella macchina virtuale. Nel capitolo [Struttura di VM/dischi rigidi virtuali per le distribuzioni SAP][planning-guide-5.5.1] di questo documento sono state presentate le considerazioni in merito al numero di dischi rigidi virtuali. Il portale di Azure consente di collegare e scollegare i dischi dopo aver distribuito una macchina virtuale di base. I dischi possono essere collegati o scollegati sia quando la macchina virtuale è attiva e in esecuzione che quando è arrestata. Quando si collega un disco, il portale di Azure offre di collegare un disco vuoto o un disco esistente che in quello specifico momento non è collegato ad altre macchine virtuali. 

**Nota**: i dischi rigidi virtuali possono essere collegati in qualsiasi momento a una sola VM.

![Collegare e scollegare dischi con l'archiviazione standard di Azure][planning-guide-figure-1400]

È necessario decidere se creare un nuovo disco rigido virtuale vuoto, che verrebbe creato nello stesso account di archiviazione della macchina virtuale di base, o se selezionare un disco rigido virtuale esistente caricato in precedenza da collegare al momento alla macchina virtuale. 

**IMPORTANTE**: **NON** usare la memorizzazione nella cache dell'host con l'archiviazione Standard di Azure, ma mantenere l'impostazione predefinita NESSUNA. Con l'archiviazione Premium di Azure si consiglia di abilitare la memorizzazione nella cache di lettura se la caratteristica I/O è generalmente di lettura, come il normale traffico I/O per i file di dati del database. Nel caso dei file di log delle transazioni del database, la memorizzazione nella cache non è consigliata.

- - -
> ![Windows][Logo_Windows] Windows
> 
> [Come collegare un disco dati nel portale di Azure][virtual-machines-windows-attach-disk-portal]
> 
> Se i dischi sono collegati, è necessario accedere alla macchina virtuale per aprire Gestione disco di Windows. Se il montaggio automatico non è abilitato come consigliato nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3], il volume appena collegato deve essere portato online e inizializzato.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Se i dischi sono collegati, è necessario accedere alla VM e inizializzare i dischi come descritto in [questo articolo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> 
> 

- - -
Se il nuovo disco è vuoto, è necessario anche formattarlo. Per la formattazione, in particolare per i file di dati e di log DBMS, si applicano le stesse raccomandazioni fornite per le distribuzioni bare metal di DBMS.

Come già indicato nel capitolo [Concetto di macchina virtuale di Microsoft Azure][planning-guide-3.2], un account di archiviazione di Azure non offre risorse infinite in termini di volume di I/O, operazioni di I/O al secondo e volume di dati. In genere le macchine virtuali DBMS sono quelle più interessate dal problema. Potrebbe essere preferibile usare un account di archiviazione separato per ogni macchina virtuale se si hanno poche macchine virtuali con volumi I/O elevati da distribuire in modo da rimanere entro il limite del volume per l'account di archiviazione di Azure. In caso contrario, si deve capire come bilanciare queste macchine virtuali tra i diversi account di archiviazione senza raggiungere il limite per ogni singolo account di archiviazione. Altri dettagli sono riportati nella [guida alla distribuzione DBMS][dbms-guide]. Tenere presenti queste limitazioni anche per le macchine virtuali pure del server applicazioni SAP o per altre macchine virtuali che potrebbero richiedere altri dischi rigidi virtuali.

Un altro argomento rilevante per gli account di archiviazione riguarda la replica geografica dei dischi rigidi virtuali in un account di archiviazione. La replica geografica è abilitata o disabilitata al livello di account di archiviazione e non al livello di macchina virtuale. Se la replica geografica è abilitata, i dischi rigidi virtuali all'interno dell'account di archiviazione vengono replicati in un altro data center di Azure della stessa area. Prima di prendere una decisione, è necessario considerare la restrizione seguente:

La replica geografica di Azure funziona in locale in ogni disco rigido virtuale di una macchina virtuale e non replica gli I/O in ordine cronologico tra più dischi rigidi virtuali in una macchina virtuale. Di conseguenza, il disco rigido virtuale che rappresenta la macchina virtuale di base ed eventuali altri dischi rigidi virtuali collegati alla macchina virtuale vengono replicati in modo indipendente. Ciò significa che le modifiche nei vari dischi rigidi virtuali non vengono sincronizzate. Il fatto che gli I/O vengano replicati indipendentemente dall'ordine di scrittura implica che la replica geografica non è significativa per i server di database che contengono database distribuiti in più dischi rigidi virtuali. Oltre a DBMS, potrebbero esserci altre applicazioni in cui i processi scrivono o manipolano i dati in dischi rigidi virtuali diversi e in cui è importante mantenere l'ordine delle modifiche. Se questo è un requisito, la replica geografica in Azure non dovrebbe essere abilitata. Se si deve o si vuole usare la replica geografica solo in determinati set di macchine virtuali, è possibile suddividere le macchine virtuali e i dischi rigidi virtuali correlati in account di archiviazione diversi con replica geografica abilitata o disabilitata.

#### <a name="a-name17e0d5437e8c4160a7dadd7117a1ad9dasetting-automount-for-attached-disks"></a><a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Impostazione del montaggio automatico per dischi collegati
- - -
> ![ Windows][Logo_Windows]  Windows
> 
> Per le macchine virtuali create da immagini o dischi personali, è necessario verificare ed eventualmente impostare il parametro di montaggio automatico. L'impostazione di questo parametro consente alla macchina virtuale di rimontare automaticamente le unità collegate/montate dopo un riavvio o una ridistribuzione in Azure. 
> Il parametro è impostato per le immagini fornite da Microsoft in Azure Marketplace.
> 
> Per impostare il montaggio automatico, vedere la documentazione relativa al file eseguibile della riga di comando diskpart.exe qui: 
> 
> * [Opzioni della riga di comando di DiskPart](https://technet.microsoft.com/library/cc766465.aspx)
> * [Montaggio automatico](http://technet.microsoft.com/library/cc753703.aspx)
> 
> La finestra della riga di comando di Windows deve essere aperta come amministratore.
> 
> Se i dischi sono collegati, è necessario accedere alla macchina virtuale per aprire Gestione disco di Windows. Se il montaggio automatico non è abilitato come consigliato nel capitolo [Impostazione del montaggio automatico per dischi collegati][planning-guide-5.5.3], il volume appena collegato deve essere portato online e inizializzato.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> È necessario inizializzare un disco vuoto appena collegato come descritto in [questo articolo][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux].
> È anche necessario aggiungere nuovi dischi a /etc/fstab.
> 
> 

- - -
### <a name="final-deployment"></a>Distribuzione finale
Per la distribuzione finale e la procedura esatta da seguire, soprattutto in relazione alla distribuzione di SAP Extended Monitoring, vedere la [guida alla distribuzione][deployment-guide].

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Accesso ai sistemi SAP in esecuzione in macchine virtuali di Azure
Per gli scenari cloud only, potrebbe essere opportuna la connessione ai sistemi SAP attraverso la rete Internet pubblica con l'interfaccia utente grafica SAP. In questi casi, è necessario applicare le procedure seguenti.

Più avanti in questo documento verrà illustrato altro scenario importante, ovvero la connessione a sistemi SAP nelle distribuzioni cross-premise che contengono una connessione da sito a sito (tunnel VPN) o la connessione Azure ExpressRoute tra i sistemi locali e Azure.

### <a name="remote-access-to-sap-systems"></a>Accesso remoto ai sistemi SAP
Con Azure Resource Manager non esistono più endpoint predefiniti come nel modello classico precedente. Tutte le porte di una macchina virtuale ARM di Azure sono aperte, purché:

1. Non siano definiti gruppi di sicurezza di rete per la subnet o l'interfaccia di rete. Il traffico di rete verso le macchine virtuali di Azure sia protetto con i cosiddetti "Gruppi di sicurezza di rete". Per altre informazioni, vedere [Informazioni sui gruppi di sicurezza di rete][virtual-networks-nsg]
2. Non sia definito Azure Load Balancer per l'interfaccia di rete   

Per una descrizione della differenza di architettura tra modello classico e Azure Resource Manager, vedere [questo articolo][virtual-machines-azure-resource-manager-architecture].

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-for-cloudonly-scenario"></a>Configurazione della connettività del sistema SAP e dell'interfaccia utente grafica SAP lo per scenario solo cloud
Per informazioni dettagliate su questo argomento, vedere questo articolo: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx> 

#### <a name="changing-firewall-settings-within-vm"></a>Modifica delle impostazioni del firewall all'interno di una macchina virtuale
Potrebbe essere necessario configurare il firewall nelle macchine virtuali per consentire il traffico in ingresso verso il sistema SAP. 

- - -
> ![ Windows][Logo_Windows]  Windows
> 
> Per impostazione predefinita, Windows Firewall all'interno di una macchina virtuale distribuita di Azure è attivato. A questo punto è necessario consentire l'apertura della porta SAP, in caso contrario l'interfaccia utente grafica SAP non potrà connettersi.
> A tale scopo, seguire questa procedura:
> 
> * Aprire "Impostazioni avanzate" in Pannello di controllo\Sistema e sicurezza\Windows Firewall.
> * Fare clic con il pulsante destro del mouse su Regole connessioni in entrata e scegliere "Nuova regola".
> * Nella procedura guidata seguente scegliere di creare una nuova regola "Porta".
> * Nel passaggio successivo della procedura guidata lasciare l'impostazione su TCP e digitare il numero della porta da aprire. L'ID dell'istanza SAP è 00, quindi verrà usato 3200. Se l'istanza ha un numero diverso, è necessario aprire la porta definita in precedenza in base al numero di istanza.
> * Nella parte successiva della procedura guidata lasciare selezionato "Consenti la connessione".
> * Nel passaggio successivo della procedura guidata è necessario definire se la regola si applica al tipo di rete Dominio, Privato o Pubblico. Se necessario, modificarlo in base alle proprie esigenze. Tuttavia, per la connessione con l'interfaccia utente grafica SAP dall'esterno attraverso la rete pubblica è necessario che la regola sia applicata alla rete pubblica.
> * Nell'ultimo passaggio della procedura guidata è necessario assegnare un nome alla regola e quindi salvare la regola facendo clic su "Fine"
> 
> La regola viene applicata immediatamente.
> 
> ![Definizione delle regole delle porte][planning-guide-figure-1600]
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Le immagini di Linux in Azure Marketplace non abilitano il firewall iptables per impostazione predefinita e la connessione al sistema SAP dovrebbe funzionare. Se è stato abilitato iptables o un altro firewall, vedere la documentazione di iptables o del firewall usato per consentire il traffico TCP in ingresso alla porta 32xx, dove xx è il numero del sistema SAP. 
> 
> 

- - -
#### <a name="security-recommendations"></a>Suggerimenti per la sicurezza
L'interfaccia utente grafica SAP non si connette immediatamente alle istanze di SAP (porta 32xx) in esecuzione, ma si connette prima al processo del server dei messaggi SAP (porta 36xx). In passato questa porta era usata dal server dei messaggi per la comunicazione interna verso le istanze dell'applicazione. Per impedire la comunicazione accidentale dei server applicazioni locali con un server dei messaggi in Azure, le porte di comunicazione possono essere modificate. È consigliabile modificare la comunicazione interna tra il server dei messaggi SAP e le relative istanze dell'applicazione e un numero di porta diverso in sistemi clonati da sistemi locali, ad esempio un clone di sviluppo per i test di progetto e così via. Questa operazione può essere eseguita con il parametro di profilo predefinito:

> rdisp/msserv_internal
> 
> 

come illustrato in <https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm> 

## <a name="a-name96a77628a05e475d9df3fb82217e8f14aconcepts-of-cloudonly-deployment-of-sap-instances"></a><a name="96a77628-a05e-475d-9df3-fb82217e8f14"></a>Concetti della distribuzione solo cloud di istanze di SAP
### <a name="a-name3e9c3690da67421abc3f12c520d99a30asingle-vm-with-sap-netweaver-demotraining-scenario"></a><a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver
![Esecuzione di singoli sistemi dimostrativi SAP delle VM con gli stessi nomi di VM, isolati nei servizi cloud di Azure][planning-guide-figure-1700]

In questo scenario (vedere il capitolo [Solo cloud][planning-guide-2.1] di questo documento) viene implementato un tipico scenario di sistema di formazione/dimostrativo, in cui lo scenario completo di formazione/dimostrativo è contenuto all'interno di un'unica VM. Si presuppone che la distribuzione venga eseguita con i modelli di immagine di VM. Si suppone inoltre che più macchine virtuali di formazione/dimostrative debbano essere distribuite con macchine virtuali con lo stesso nome.

Si presuppone che sia stata creata un'immagine di VM come descritto in alcune sezioni del capitolo [Preparazione di VM con SAP per Azure][planning-guide-5.2] di questo documento.

La sequenza di eventi per implementare lo scenario è simile alla seguente:

[comment]: <> (MShermannd  TODO have to provide ARM sample / description using json template + clarification regarding unique VM name within ARM virtual network  )   
##### <a name="powershell"></a>PowerShell
* Creare un nuovo gruppo di risorse per ogni scenario di formazione/dimostrativo

```powershell
$rgName = "SAPERPDemo1"
New-AzureRmResourceGroup -Name $rgName -Location "North Europe"
```

* Creare un nuovo account di archiviazione.

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Creare una nuova rete virtuale per ogni scenario di formazione/dimostrativo consentire l'utilizzo dello stesso nome host e degli indirizzi IP. La rete virtuale è protetta da un gruppo di sicurezza di rete che consente solo il traffico verso la porta 3389 per abilitare l'accesso al Desktop remoto e verso la porta 22 per SSH. 

```powershell
# Create a new Virtual Network
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzureRmNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzureRmNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzureRmVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Creare un nuovo indirizzo IP pubblico da usare per accedere alla macchina virtuale da Internet

```powershell
# Create a public IP address with a DNS name
$pip = New-AzureRmPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Creare una nuova interfaccia di rete per la macchina virtuale

```powershell 
# Create a new Network Interface
$nic = New-AzureRmNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip 
```

* Creare una macchina virtuale. In uno scenario solo cloud tutte le macchine virtuali avranno lo stesso nome. Anche il SID di SAP delle istanze di SAP NetWeaver in queste macchine virtuali sarà lo stesso. Nel gruppo di risorse di Azure il nome della macchina virtuale deve essere univoco, ma in diversi gruppi di risorse di Azure è possibile eseguire macchine virtuali con lo stesso nome. L'account predefinito 'Administrator' di Windows o 'root' di Linux non è valido. È quindi necessario definire un nuovo nome utente dell'amministratore con una password. Devono essere definite anche le dimensioni della macchina virtuale.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES" -Skus "12" -Version "latest"
# $vmconfig = Set-AzureRmVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="os"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzureRmVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzureRmVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzureRmVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzureRmVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vm = New-AzureRmVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Facoltativamente, aggiungere altri dischi e ripristinare il contenuto necessario. Tenere presente che tutti i nomi dei BLOB (URL per i BLOB) devono essere univoci all'interno di Azure.

```powershell
# Optional: Attach additional data disks
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzureRmVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzureRmVM
```

##### <a name="cli"></a>CLI
L'esempio di codice seguente può essere usato in Linux. Per Windows, usare PowerShell come descritto in precedenza oppure adattare l'esempio per usare %rgName% anziché $rgName e impostare la variabile di ambiente con il comando di Windows *set*.

* Creare un nuovo gruppo di risorse per ogni scenario di formazione/dimostrativo

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
azure group create $rgName "North Europe"
```

* Creare un nuovo account di archiviazione.

```
azure storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku-name LRS $rgNameLower
```

* Creare una nuova rete virtuale per ogni scenario di formazione/dimostrativo consentire l'utilizzo dello stesso nome host e degli indirizzi IP. La rete virtuale è protetta da un gruppo di sicurezza di rete che consente solo il traffico verso la porta 3389 per abilitare l'accesso al Desktop remoto e verso la porta 22 per SSH. 

```
azure network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
azure network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

azure network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
azure network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group-name SAPERPDemoNSG
```

* Creare un nuovo indirizzo IP pubblico da usare per accedere alla macchina virtuale da Internet

```
azure network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --domain-name-label $rgNameLower --allocation-method Dynamic
```

* Creare una nuova interfaccia di rete per la macchina virtuale

```
azure network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-name SAPERPDemoPIP --subnet-name Subnet1 --subnet-vnet-name SAPERPDemoVNet 
```

* Creare una macchina virtuale. In uno scenario solo cloud tutte le macchine virtuali avranno lo stesso nome. Anche il SID di SAP delle istanze di SAP NetWeaver in queste macchine virtuali sarà lo stesso. Nel gruppo di risorse di Azure il nome della macchina virtuale deve essere univoco, ma in diversi gruppi di risorse di Azure è possibile eseguire macchine virtuali con lo stesso nome. L'account predefinito 'Administrator' di Windows o 'root' di Linux non è valido. È quindi necessario definire un nuovo nome utente dell'amministratore con una password. Devono essere definite anche le dimensioni della macchina virtuale.

```
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn SUSE:SLES:12:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
# azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --image-urn RedHat:RHEL:7.2:latest --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd --disable-boot-diagnostics
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
#azure vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nic-name SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --vm-size Standard_D11 --os-disk-vhd https://$rgNameLower.blob.core.windows.net/vhds/os.vhd -Q <path to image vhd> --disable-boot-diagnostics
```

* Facoltativamente, aggiungere altri dischi e ripristinare il contenuto necessario. Tenere presente che tutti i nomi dei BLOB (URL per i BLOB) devono essere univoci all'interno di Azure.

```
# Optional: Attach additional data disks
azure vm disk attach-new --resource-group $rgName --vm-name SAPERPDemo --size-in-gb 1023 --vhd-name datadisk
```

##### <a name="template"></a>Modello
È possibile usare i modelli di esempio nel repository azure-quickstart-templates di github.

* [Macchina virtuale semplice di Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Macchina virtuale semplice di Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Macchina virtuale dall'immagine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-which-need-to-communicate-within-azure"></a>Implementare un set di macchine virtuali che devono comunicare all'interno di Azure
Questo scenario solo cloud è uno scenario tipico per scopi di formazione e dimostrativi in cui il software che rappresenta lo scenario di formazione/dimostrativo viene distribuito in più macchine virtuali. I vari componenti installati nelle diverse macchine virtuali devono comunicare tra loro. Anche in questo scenario non è necessaria alcuna comunicazione di rete locale o scenario cross-premise.

Questo scenario è un'estensione dell'installazione descritta nel capitolo [Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver][planning-guide-7.1] di questo documento. In questo caso vengono aggiunte più macchine virtuali a un gruppo di risorse esistente. Nell'esempio seguente lo scenario di formazione è costituito da una macchina virtuale ASCS/SCS SAP, da una macchina virtuale che esegue un sistema DBMS e da una macchina virtuale di un'istanza del server applicazioni SAP.

Prima di compilare questo scenario è necessario considerare le impostazioni di base come descritto nello scenario precedente.

#### <a name="resource-group-and-virtual-machine-naming"></a>Denominazione dei gruppi di risorse e delle macchine virtuali
Tutti i nomi dei gruppi di risorse devono essere univoci. Sviluppare il proprio schema di denominazione delle risorse, ad esempio `<rg-name`>-suffisso. 

Il nome delle macchine virtuali deve essere univoco all'interno del gruppo di risorse. 

#### <a name="setup-network-for-communication-between-the-different-vms"></a>Configurare la rete per la comunicazione tra le diverse macchine VM
![Set di VM all'interno di una rete virtuale di Azure][planning-guide-figure-1900]

Per impedire conflitti di denominazione con cloni dello stesso scenario di formazione/dimostrativo, è necessario creare una rete virtuale di Azure per ogni scenario. La risoluzione dei nomi DNS verrà fornita da Azure oppure è possibile configurare il proprio server DNS all'esterno di Azure (l'argomento non verrà ulteriormente trattato in questo documento). In questo scenario non viene configurato alcun DNS personalizzato. Per tutte le macchine virtuali all'interno di una rete virtuale di Azure verrà abilitata la comunicazione usando nomi host. 

I motivi che giustificano la separazione degli scenari di formazione o dimostrativi in base alle reti virtuali e non solo ai gruppi di risorse potrebbero essere i seguenti:

* Il panorama applicativo SAP, così com'è configurato, richiede un proprio AP/OpenLDAP e un server di dominio deve far parte di ciascuno degli scenari.  
* Il panorama applicativo SAP, così com'è configurato, include componenti che devono interagire con indirizzi IP fissi.

Per altri dettagli sulle reti virtuali di Azure e la relativa definizione, vedere [questo articolo][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-crosspremises"></a>Distribuzione di VM SAP con connettività di rete aziendale (cross-premise)
Si esegue un panorama applicativo SAP e si vuole suddividere la distribuzione tra bare metal per i server DBMS di fascia alta, ambienti virtualizzati locali per i livelli dell'applicazione, sistemi SAP configurati a 2 livelli di dimensioni inferiori e IaaS di Azure. Il presupposto di base è che i sistemi SAP all'interno di un panorama applicativo SAP debbano comunicare tra loro e con molti altri componenti software distribuiti nell'azienda, indipendentemente dalla propria modalità di distribuzione. Inoltre, non devono essere presenti differenze introdotte dalla modalità di distribuzione per l'utente finale che si connette con SAP GUI o altre interfacce. Queste condizioni possono essere soddisfatte solo quando i servizi Active Directory/Open LDAP e DNS locali vengono estesi ai sistemi di Azure usando la connettività da sito a sito/multisito o connessioni private come Azure ExpressRoute.

Per maggiori informazioni sui dettagli di implementazione di SAP in Azure, è consigliabile leggere il capitolo [Concetti della distribuzione solo cloud di istanze di SAP][planning-guide-7] di questo documento che illustra alcuni dei costrutti di base di Azure e come devono essere usati con le applicazioni SAP in Azure.

### <a name="scenario-of-a-sap-landscape"></a>Scenario di un panorama applicativo SAP
Lo scenario cross-premise può essere descritto approssimativamente come nei grafici seguenti:

![Connettività da sito a sito tra asset locali e di Azure][planning-guide-figure-2100]

Lo scenario illustrato in precedenza descrive una situazione in cui i servizi Active Directory/Open LDAP e DNS locali vengono estesi ad Azure. Sul lato locale un determinato intervallo di indirizzi IP è riservato per ogni sottoscrizione di Azure. L'intervallo di indirizzi IP verrà assegnato a una rete virtuale di Azure sul lato Azure.

#### <a name="security-considerations"></a>Considerazioni relative alla sicurezza
Il requisito minimo è l'uso di protocolli di comunicazione protetta, ad esempio SSL/TLS per l'accesso dal browser o connessioni basate su VPN per l'accesso dal sistema ai servizi di Azure. Il presupposto è che le aziende gestiscano la connessione VPN tra la propria rete aziendale e Azure in modo molto diverso. Alcune aziende possono semplicemente aprire tutte le porte, altre potrebbero adottare un approccio più selettivo riguardo alle scelta delle porte da aprire e così via. 

Le porte di comunicazione SAP tipiche sono elencate nella tabella seguente. Sostanzialmente, è sufficiente aprire la porta del gateway SAP.

| Service | Nome della porta | Esempio `<nn`> = 01 | Intervallo predefinito (min-max) | Commento |
| --- | --- | --- | --- | --- |
| Dispatcher |sapdp`<nn>` vedere * |3201 |3200 – 3299 |Dispatcher di SAP, usato dall'interfaccia utente grafica di SAP per Windows e Java |
| Server messaggi |sapms`<sid`> vedere ** |3600 |sapms libero`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> vedere * |3301 |libero |Gateway SAP, usato per le comunicazioni CPIC e RFC |
| Router SAP |sapdp99 |3299 |libero |Solo i nomi del servizio CI (istanza centrale) possono essere riassegnati in /etc/services specificando un valore arbitrario dopo l'installazione. |

*) nn = numero istanza SAP

**) sid = SAP-System-ID

Informazioni più dettagliate sulle porte necessarie per i diversi prodotti o servizi SAP in base ai prodotti SAP sono disponibili qui: <http://scn.sap.com/docs/DOC-17124>. Questo documento fornisce informazioni che consentono di aprire nel dispositivo VPN le porte dedicate necessarie per gli scenari e i prodotti SAP specifici.

Quando si distribuiscono VM in tale scenario è possibile applicare altre misure di sicurezza creando un [gruppo di sicurezza di rete][virtual-networks-nsg] per definire le regole di accesso.

### <a name="dealing-with-different-virtual-machine-series"></a>Gestione di serie di macchine virtuali diverse
Nel corso degli ultimi 12 mesi Microsoft ha aggiunto molti più tipi di VM che si differenziano per numero di vCPU, memoria o, soprattutto, per l'hardware su cui vengono eseguiti. Non tutte queste VM sono supportate con SAP (vedere i tipi di VM supportati nella nota SAP [1928533]). Alcune di queste VM vengono eseguite su generazioni di hardware host diverse, che vengono distribuite a un livello di granularità pari a quella di un'unità di scala Azure. Ciò significa che possono verificarsi casi in cui le diverse dimensioni di VM scelte non possono essere eseguite sulla stessa unità di scala. Un set di disponibilità è limitato nella possibilità di estensione delle unità di scala in base ai vari tipi di hardware.  Ad esempio, se si vuole eseguire il DBMS in VM A5-A11 e il livello dell'applicazione SAP in VM di serie G, è necessario distribuire un singolo sistema SAP o sistemi SAP diversi all'interno di set di disponibilità diversi.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Stampa su una stampante di rete locale dall'istanza SAP in Azure
##### <a name="printing-over-tcpip-in-crosspremises-scenario"></a>Stampa su TCP/IP in uno scenario cross-premise
La configurazione delle stampanti di rete locali basate su TCP/IP in una VM di Azure è complessivamente uguale a quella della rete aziendale, presupponendo che sia disponibile un tunnel VPN da sito a sito o una connessione ExpressRoute. 

- - -
> ![ Windows][Logo_Windows]  Windows
> 
> A tale scopo, seguire questa procedura:
> 
> * Alcune stampanti di rete sono dotate di una configurazione guidata che semplifica la configurazione della stampante in una VM di Azure. Se non è stato distribuito alcun software di procedura guidata con la stampante, il modo "manuale" di configurare la stampante prevede la creazione di una nuova porta stampante TCP/IP.
> * Aprire Pannello di controllo -> Dispositivi e stampanti -> Aggiungi stampante 
> * Scegliere Aggiungi una stampante usando un nome host o un indirizzo TCP/IP
> * Digitare l'indirizzo IP della stampante
> * Porta stampante standard 9100
> * Se necessario, installare manualmente il driver della stampante appropriato. 
> 
> ![ Linux][Logo_Linux]  Linux
> 
> * Come per Windows, seguire la procedura standard di installazione di una stampante di rete.
> * Seguire le guide pubbliche di Linux per [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) o [Red Hat](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html) su come aggiungere una stampante.
> 
> 

- - -
![Stampa di rete][planning-guide-figure-2200]

##### <a name="hostbased-printer-over-smb-shared-printer-in-crosspremises-scenario"></a>Stampante basata su host su SMB (stampante condivisa) in uno scenario cross-premise
Per impostazione predefinita le stampanti basate su host non sono compatibili con la rete. Tuttavia, una stampante basata su host può essere condivisa tra computer in una rete, a condizione che sia connessa a un computer acceso. Connettere la rete aziendale da sito a sito o con ExpressRoute e condividere la stampante locale. Il protocollo SMB usa NetBIOS anziché DNS come nome del servizio. Il nome host NetBIOS può essere diverso dal nome host DNS. Il caso standard prevede che il nome host NetBIOS e il nome host DNS siano identici. L'uso del dominio DNS non ha senso nello spazio dei nomi NetBIOS. Di conseguenza, il nome host DNS completo costituito dal nome host DNS e dal dominio DNS non deve essere usato nello spazio dei nomi NetBIOS.

La condivisione della stampante è identificata da un nome univoco nella rete:

* Nome host dell'host SMB (sempre necessario). 
* Nome della condivisione (sempre necessario). 
* Nome del dominio se la condivisione della stampante non si trova nello stesso dominio del sistema SAP. 
* Inoltre, un nome utente e una password potrebbero essere necessari per accedere alla condivisione della stampante.

Procedura:

- - -
> ![ Windows][Logo_Windows]  Windows
> 
> Condividere la stampante locale.
> Nella VM di Azure aprire Esplora risorse e digitare il nome di condivisione della stampante.
> Un'installazione guidata stampante illustrerà in modo dettagliato il processo di installazione.
> 
> ![ Linux][Logo_Linux]  Linux
> 
> Di seguito sono riportati alcuni esempi di documentazione sulla configurazione delle stampanti di rete in Linux o sull'inclusione di un capitolo relativo alla stampa in Linux. La configurazione è uguale a quella di una VM Linux di Azure, a condizione che la VM faccia parte di una VPN:
> 
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/s1-printing-smb-printer.html>
> 
> 

- - -
##### <a name="usb-printer-printer-forwarding"></a>Stampante USB (inoltro stampante)
In Azure la possibilità di Servizi Desktop remoto di fornire agli utenti l'accesso ai propri dispositivi stampante locale in una sessione remota non è disponibile.

- - -
> ![ Windows][Logo_Windows] Windows
> 
> Altri dettagli sulla stampa con Windows sono disponibili qui: <http://technet.microsoft.com/library/jj590748.aspx>.
> 
> 

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-crosspremises"></a>Integrazione dei sistemi SAP in Azure in Correction and Transport System (TMS) cross-premise
SAP Change and Transport System (TMS) deve essere configurato per esportare e importare la richiesta di trasporto tra sistemi nel panorama applicativo. Si presuppone che le istanze di sviluppo di un sistema SAP (DEV) si trovino in Azure, mentre il controllo di qualità (QA) e i sistemi di produzione (PRD) siano locali. Inoltre, si presuppone che sia presente una directory di trasporto centrale.

##### <a name="configuring-the-transport-domain"></a>Configurazione del dominio di trasporto
Configurare il dominio di trasporto sul sistema designato come controller di dominio di trasporto, come descritto in [Configurazione del controller del dominio di trasporto](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm). Verrà creato un utente di sistema TMSADM e sarà generata la destinazione RFC necessaria. È possibile controllare queste connessioni RFC usando la transazione SM59. La risoluzione dei nomi host deve essere abilitata nel dominio di trasporto. 

Procedura:

* In questo scenario il sistema QAS locale sarà il controller di dominio di CTS. Chiamare la transazione STMS. Viene visualizzata la finestra di dialogo TMS. Viene visualizzata la finestra di dialogo Configure Transport Domain (questa finestra di dialogo viene visualizzata solo se non è stato ancora configurato un dominio di trasporto).
* Verificare che l'utente TMSADM creato automaticamente sia autorizzato selezionando SM59 -> ABAP Connection (Connessione ABAP) -> TMSADM@E61.DOMAIN_E61 -> Details (Dettagli) -> Utilities(M) (Utilità - M) -> Authorization Test (Test autorizzazione). La schermata iniziale della transazione STMS dovrebbe indicare che il sistema SAP ora sta funzionando come controller di dominio di trasporto, come illustrato di seguito:

![Schermata iniziale della transazione STMS sul controller di dominio][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Inclusione dei sistemi SAP nel dominio di trasporto
La sequenza di inclusione di un sistema SAP in un dominio di trasporto è simile alla seguente:

* Nel sistema DEV in Azure passare al sistema di trasporto (Client 000) e chiamare la transazione STMS. Scegliere Other Configuration dalla finestra di dialogo e continuare con Include System in Domain. Specificare il controller di dominio come host di destinazione (vedere[Inclusione di sistemi SAP nel dominio di trasporto](http://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Il sistema ora è in attesa di essere incluso nel dominio di trasporto.
* Per motivi di sicurezza, è necessario tornare al controller di dominio per confermare la richiesta. Scegliere System Overview e Approve of the waiting system. Quindi, confermare la richiesta; la configurazione verrà distribuita.

Il sistema SAP ora contiene le informazioni necessarie su tutti gli altri sistemi SAP nel dominio di trasporto. Allo stesso tempo, i dati degli indirizzi del nuovo sistema SAP vengono inviati a tutti gli altri sistemi SAP e il sistema SAP viene immesso nel profilo di trasporto del programma di controllo del trasporto. Verificare il corretto funzionamento di RFC e dell'accesso alla directory di trasporto del dominio.

Continuare con la configurazione del sistema di trasporto come di consueto seguendo la descrizione fornita nella documentazione [Sistema di trasporto e modifica](http://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Procedura:

* Assicurarsi che il sistema STMS locale sia configurato correttamente.
* Assicurarsi che il nome host del controller di dominio di trasporto possa essere risolto dalla macchina virtuale in Azure e viceversa.
* Chiamare la transazione STMS -> Other Configuration -> Include System in Domain.
* Confermare la connessione nel sistema TMS locale.
* Configurare le route, i gruppi e i livelli di trasporto, come di consueto.

Negli scenari cross-premise connessi da sito a sito, la latenza tra sistema locale e Azure può essere ancora significativa. Se si segue la sequenza del trasporto di oggetti nei sistemi di sviluppo e test fino alla produzione o si valuta la possibilità di applicare trasporti o pacchetti di supporto nei vari sistemi, si comprende che, in base alla posizione della directory di trasporto centrale, per alcuni dei sistemi si verificherà una latenza elevata della lettura o scrittura dei dati nella directory di trasporto centrale. Si tratta di una situazione simile a quella delle configurazioni del panorama applicativo SAP, dove diversi sistemi sono distribuiti in diversi data center posizionati a una notevole distanza l'uno dall'altro.

Per ovviare a problemi come la latenza e fare in modo che i sistemi eseguano rapidamente le operazioni di lettura o scrittura verso o dalla directory di trasporto, è possibile configurare due domini di trasporto STMS (uno da usare in locale e l'altro da usare con i sistemi in Azure) e collegarli. Per informazioni sui principi alla base di questo concetto in SAP TMS, vedere questo documento: <http://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>. 

Procedura:

* Configurare un dominio di trasporto in ogni posizione (in locale e in Azure) usando la transazione STMS <http://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Collegare i domini con un collegamento di dominio e verificare che siano collegati. 
  <http://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Distribuire la configurazione nel sistema collegato.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-onpremises-crosspremises"></a>Traffico RFC tra le istanze SAP in Azure e locali (cross-premise)
Il traffico RFC tra i sistemi locali e in Azure deve funzionare. Per configurare una connessione, chiamare la transazione SM59 in un sistema di origine in cui è necessario definire una connessione RFC verso il sistema di destinazione. La configurazione è simile a quella standard di una connessione RFC.

Si presuppone che nello scenario cross-premise le VM che eseguono sistemi SAP che devono comunicare reciprocamente si trovino nello stesso dominio. Quindi, la configurazione di una connessione RFC tra sistemi SAP non è diversa dalla procedura di configurazione e dagli input degli scenari locali.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Accesso a condivisioni file "locali" da istanze SAP che si trovano in Azure e viceversa
Le istanze di SAP che si trovano in Azure devono accedere alle condivisioni file disponibili nell'ambiente aziendale. Inoltre, le istanze di SAP locali devono accedere alle condivisioni file che si trovano in Azure. Per abilitare le condivisioni file è necessario configurare le autorizzazioni e le opzioni di condivisione nel sistema locale. Assicurarsi di aprire le porte della connessione VPN o ExpressRoute tra Azure e il data center.

## <a name="supportability"></a>Supporto
### <a name="a-name6f0a47f3a2894090a0532521618a28c3aazure-monitoring-solution-for-sap"></a><a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Soluzione di monitoraggio di Azure per SAP
Per abilitare il monitoraggio dei sistemi SAP mission-critical in Azure, gli strumenti di monitoraggio SAP SAPOSCOL o SAP Host Agent ricevono i dati dall'host di Azure con l'estensione di monitoraggio di Azure per SAP. Considerato che le esigenze di SAP erano molto specifiche per le applicazioni SAP, Microsoft ha deciso di non implementare genericamente la funzionalità richiesta in Azure, ma di lasciare che siano i clienti a occuparsi della distribuzione dei componenti e delle configurazioni di monitoraggio necessarie per le loro macchine virtuali in esecuzione in Azure. Tuttavia, la gestione della distribuzione e del ciclo di vita dei componenti di monitoraggio verrà, nella maggior parte dei casi, automatizzata da Azure.

#### <a name="solution-design"></a>Progettazione della soluzione
La soluzione sviluppata per abilitare il monitoraggio di SAP Monitoring si basa sull'architettura del framework dell'agente VM e delle estensioni VM di Azure. L'idea alla base del framework dell'agente VM e delle estensioni VM di Azure è di consentire l'installazione delle applicazioni software disponibili nella raccolta di estensioni VM di Azure all'interno di una VM. Il principio alla base di questo concetto è di consentire (in casi come l'estensione di monitoraggio di Azure per SAP), la distribuzione di funzionalità speciali in una VM e la configurazione del software in fase di distribuzione. 

Dal mese di febbraio 2014 l'agente VM di Azure che consente la gestione di specifiche estensioni VM di Azure all'interno della VM viene inserito nelle VM di Windows per impostazione predefinita durante la creazione di VM nel portale di Azure. Nel caso di SUSE o Red Hat di Linux, l'agente VM fa già parte dell'immagine di Azure Marketplace. Nel caso in cui si voglia caricare una VM Linux da locale in Azure, è necessario installare manualmente l'agente VM.

I blocchi predefiniti di base della soluzione di monitoraggio in Azure per SAP hanno un aspetto simile al seguente:

![Componenti dell'estensione di Microsoft Azure][planning-guide-figure-2400]

Come illustrato nel diagramma a blocchi precedente, una parte della soluzione di monitoraggio per SAP è ospitata nell'immagine VM di Azure e nella raccolta di estensioni di Azure, che è un archivio replicato globalmente gestito da Azure Operations. È responsabilità del team congiunto SAP/MS impegnato nell'implementazione di SAP collaborare con Azure Operations alla pubblicazione di nuove versioni dell'estensione di monitoraggio di Azure per SAP. L'estensione di monitoraggio di Azure per SAP userà l'estensione Diagnostica di Microsoft Azure o l'estensione Diagnostica di Azure per Linux per ottenere le informazioni necessarie. 

Quando si distribuisce una nuova VM Windows, l'agente VM di Azure viene aggiunto automaticamente alla VM. Questo agente ha la funzione di coordinare il caricamento e la configurazione delle estensioni di Azure per il monitoraggio dei sistemi SAP NetWeaver. Per le VM Linux l'agente VM di Azure fa già parte dell'immagine del sistema operativo di Azure Marketplace.

È presente, tuttavia, un passaggio che deve ancora essere eseguito dal cliente, cioè l'abilitazione e la configurazione della raccolta delle prestazioni. Il processo correlato alla configurazione viene automatizzato con uno script di PowerShell o un comando dell'interfaccia della riga di comando. Lo script di PowerShell può essere scaricato nello Script Center di Microsoft Azure come descritto nella [guida alla distribuzione][deployment-guide].

L'architettura complessiva della soluzione di monitoraggio di Azure per SAP ha un aspetto simile al seguente:

![Soluzione di monitoraggio di Azure per SAP NetWeaver][planning-guide-figure-2500]

**Per la procedura dettagliata per usare questi cmdlet di PowerShell o i comandi dell'interfaccia della riga di comando durante le distribuzioni, seguire le istruzioni contenute nella [guida alla distribuzione][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integrazione di un'istanza SAP ubicata in Azure in SAProuter
Le istanze SAP in esecuzione in Azure devono essere accessibili anche da SAProuter.

![Connessione di rete SAP-router][planning-guide-figure-2600]

SAProuter abilita la comunicazione TCP/IP tra i sistemi partecipanti se non esiste alcuna connessione IP diretta. Questo offre il vantaggio di non richiedere alcuna connessione end-to-end tra i partner di comunicazione a livello di rete. SAProuter è in ascolto sulla porta 3299 per impostazione predefinita.
Per connettere le istanze di SAP con SAProuter è necessario assegnare il nome di stringa e dell'host SAProuter a ogni tentativo di connessione.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java
Fino a questo momento ci si è concentrati su SAP NetWeaver in generale o sullo stack ABAP di SAP NetWeaver. In questa piccola sezione sono riportate considerazioni specifiche per lo stack SAP Java. Una delle più importanti applicazioni basate esclusivamente su SAP NetWeaver Java è SAP Enterprise Portal. Altre applicazioni basate su SAP NetWeaver come SAP PI e SAP Solution Manager usano gli stack SAP NetWeaver ABAP e Java, quindi è sicuramente necessario considerare alcuni aspetti specifici correlati anche allo stack SAP NetWeaver Java.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal
L'installazione di un portale SAP in una macchina virtuale di Azure non è diversa da un'installazione locale se si distribuiscono scenari cross-premise. Dato il DNS viene effettuato in locale, le impostazioni delle porte delle singole istanze possono essere eseguite in base alla configurazione locale. Le raccomandazioni e le restrizioni descritte in precedenza in questo documento si applicano ad applicazioni come SAP Enterprise Portal o allo stack SAP NetWeaver Java in generale. 

![SAP Portal esposto][planning-guide-figure-2700]

Uno scenario di distribuzione speciale di alcuni clienti riguarda l'esposizione diretta di SAP Enterprise Portal a Internet mentre l'host della macchina virtuale è connesso alla rete aziendale con tunnel VPN da sito a sito o ExpressRoute. Per tale scenario, è necessario assicurarsi che determinate porte siano aperte e non bloccate da un firewall o da un gruppo di sicurezza di rete. Lo stesso meccanismo dovrà essere applicato per connettersi a un'istanza di Java SAP locale in uno scenario locale solo cloud.

L'URI del portale iniziale è http (s):`<Portalserver`>: 5XX00/irj, dove il valore della porta è costituito da un numero superiore a 50.000 (numero di sistema × 100). L'URI del portale predefinito del sistema SAP 00 è `<dns name`>.`<azure region`>.Cloudapp.azure.com:PublicPort/irj. Per altri dettagli, vedere <http://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm>. 

![Configurazione dell'endpoint][planning-guide-figure-2800]

Se si vuole personalizzare l'URL e/o le porte di SAP Enterprise Portal, consultare la documentazione seguente:

* [Modificare l'URL del portale](http://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL) 
* [Modificare i numeri di porta predefiniti e i numeri di porta del portale](http://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers) 

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure
### <a name="definition-of-terminologies"></a>Definizione dei termini
L'espressione **disponibilità elevata** è in genere correlata a un set di tecnologie che riduce al minimo le interruzioni IT assicurando la continuità aziendale dei servizi IT tramite componenti ridondanti, a tolleranza di errore o protetti con failover all'interno dello **stesso** data center. Nel caso di questo documento, all'interno di un'unica area di Azure.

Anche il **ripristino di emergenza** ha la funzione di ridurre al minimo le interruzioni dei servizi IT e il relativo ripristino, ma tra data center **diversi**, in genere ubicati a centinaia di chilometri di distanza. Nel caso di questo documento, tra aree di Azure diverse all'interno della stessa area geopolitica o come stabilito dal cliente.

### <a name="overview-of-high-availability"></a>Panoramica della disponibilità elevata
È possibile suddividere la discussione sulla disponibilità elevata SAP in Azure in due parti:

* **Disponibilità elevata dell'infrastruttura di Azure**, relativa, ad esempio, al calcolo (VM), alla rete, all'archiviazione e così via e i relativi vantaggi associati all'aumento della disponibilità delle applicazioni SAP.
* **Disponibilità elevata delle applicazioni SAP**, relativa, ad esempio, ai componenti software SAP:
  * Server applicazioni SAP
  * Istanza di SAP ASCS/SCS 
  * Server di database

e al modo in cui è possibile combinarli con la disponibilità elevata dell'infrastruttura di Azure.

La disponibilità elevata di SAP in Azure presenta alcune differenze rispetto alla disponibilità elevata di SAP in un ambiente fisico o virtuale locale. Il documento di SAP seguente descrive le configurazioni a disponibilità elevata standard di SAP in ambienti virtualizzati in Windows: <http://scn.sap.com/docs/DOC-44415>. In Linux non è disponibile alcuna configurazione a disponibilità elevata di SAP integrata in SAPinst come quella disponibile per Windows. Altre informazioni sulla disponibilità elevata di SAP in locale per Linux sono disponibili qui: <http://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Disponibilità elevata dell'infrastruttura di Azure
Attualmente non è disponibile alcun contratto di servizio per singola VM nelle macchine virtuali Azure. Per avere un'idea di come potrebbe presentarsi la disponibilità di una singola VM, è possibile creare semplicemente il prodotto dei diversi contratti di servizio di Azure disponibili: <https://azure.microsoft.com/support/legal/sla/>.

La base per il calcolo è di 30 giorni al mese o 43.200 minuti. Di conseguenza, un tempo di inattività dello 0,05% corrisponde a 21,6 minuti. Come di consueto, la disponibilità dei diversi servizi dovrà essere moltiplicata nel modo seguente:

(Disponibilità servizio n. 1/100) * (Disponibilità servizio n. 2/100) * (Disponibilità servizio n. 3/100) *…

Ad esempio:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 o una disponibilità complessiva del 99,75%.

#### <a name="virtual-machine-vm-high-availability"></a>Disponibilità elevata delle macchine virtuali
Esistono due tipi di eventi della piattaforma Azure che possono incidere sulla disponibilità delle macchine virtuali: la manutenzione pianificata e la manutenzione non pianificata.

* Gli eventi di manutenzione pianificata sono aggiornamenti periodici effettuati da Microsoft sulla piattaforma Azure sottostante per migliorare l'affidabilità, la sicurezza e le prestazioni complessive dell'infrastruttura della piattaforma su cui sono in esecuzione le macchine virtuali.
* Gli eventi di manutenzione non pianificata hanno luogo quando si verifica un guasto dell'hardware o dell'infrastruttura fisica sottostante la macchina virtuale. Può trattarsi, ad esempio, di errori della rete locale, guasti di un disco locale o altri errori a livello di rack. Quando viene rilevato un errore di questo tipo, la piattaforma Azure esegue automaticamente la migrazione della macchina virtuale dal server fisico non integro su cui è in esecuzione a un server fisico integro. Anche se rari, questi eventi possono anche richiedere il riavvio della macchina virtuale.

Per altri dettagli, vedere questo documento: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Ridondanza di Archiviazione di Azure
I dati dell'account di archiviazione di Microsoft Azure vengono sempre replicati per assicurare durabilità e disponibilità elevata nonché il rispetto del contratto di servizio di Archiviazione di Azure anche in caso di errori hardware temporanei.

Considerato che Archiviazione di Azure mantiene tre immagini dei dati per impostazione predefinita, RAID5 o RAID1 tra più dischi di Azure non sono necessari.

Per altri dettagli, vedere questo articolo: <http://azure.microsoft.com/documentation/articles/storage-redundancy/> 

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Uso del riavvio delle VM dell'infrastruttura di Azure per ottenere una "disponibilità più elevata" delle applicazioni SAP
Se si decide di non usare funzionalità come WSFC (Windows Server Failover Clustering) o una funzionalità Linux equivalente (quest'ultimo non è ancora supportato in Azure in combinazione con il software SAP), il riavvio delle VM di Azure viene usato per proteggere un sistema SAP da inattività pianificate e non pianificate dell'infrastruttura di server fisici di Azure e della complessiva piattaforma Azure sottostante. 

> [!NOTE]
> È importante ricordare che il riavvio delle VM di Azure protegge principalmente le VM e non le applicazioni. Il riavvio delle VM non offre una disponibilità elevata per le applicazioni SAP, ma offre un certo livello di disponibilità dell'infrastruttura e quindi, indirettamente, una "disponibilità più elevata" dei sistemi SAP. Inoltre, non è disponibile alcun contratto di servizio relativo al tempo che sarà necessario per il riavvio di una VM dopo un'interruzione dell'host pianificata o non pianificata. Questo metodo di "disponibilità elevata", quindi, non è adatto per i componenti fondamentali di un sistema SAP come (A)SCS o DBMS.
> 
> 

Un altro elemento importante dell'infrastruttura per la disponibilità elevata è l'archiviazione. ad esempio il contratto di servizio di Archiviazione di Azure prevede una disponibilità del 99,9%. Se si effettua la distribuzione di tutte le VM con i relativi dischi in un singolo account di archiviazione di Azure, la potenziale indisponibilità di Archiviazione di Azure causerà l'indisponibilità di tutte le VM presenti in tale account di archiviazione di Azure e anche di tutti i componenti SAP in esecuzione all'interno di tali VM.  

Anziché inserire tutte le VM virtuali in un singolo account di archiviazione di Azure, è possibile anche usare account di archiviazione dedicati per ogni VM e, in questo modo, aumentare la disponibilità complessiva delle VM e delle applicazioni SAP usando più account di archiviazione di Azure indipendenti. 

Un'architettura di esempio di un sistema SAP NetWeaver che usa la disponibilità elevata dell'infrastruttura di Azure potrebbe avere un aspetto simile al seguente:

![Utilizzo della disponibilità elevata dell'infrastruttura di Azure per ottenere una disponibilità "più elevata" delle applicazioni SAP][planning-guide-figure-2900]

Per i componenti SAP critici sono stati ottenuti fino a questo momento i risultati seguenti:

* Disponibilità elevata di server applicazioni SAP

Le istanze dei server applicazioni SAP sono componenti ridondanti. Ogni istanza dei server applicazioni SAP viene distribuita nella relativa VM, in esecuzione in un diverso dominio di errore e di aggiornamento di Azure. Vedere i capitoli [Domini di errore][planning-guide-3.2.1] e [Domini di aggiornamento][planning-guide-3.2.2]. Ciò è garantito dall'uso dei set di disponibilità di Azure. Vedere il capitolo [Set di disponibilità di Azure][planning-guide-3.2.3]. La potenziale indisponibilità pianificata o non pianificata di un dominio di errore o di aggiornamento di Azure causerà l'indisponibilità di un numero limitato di VM con le relative istanze dei server applicazioni SAP. Ogni istanza dei server applicazioni SAP è posizionata nel relativo account di archiviazione di Azure; la potenziale indisponibilità di un account di archiviazione di Azure causerà l'indisponibilità di una sola VM con la relativa istanza dei server applicazioni SAP. Tenere presente, tuttavia, che esiste un limite al numero di account di archiviazione di Azure che è possibile avere all'interno di una sottoscrizione di Azure. Per garantire l'avvio automatico dell'istanza di (A)SCS dopo il riavvio della VM, assicurarsi di impostare il parametro di avvio automatico nel profilo di avvio dell'istanza di (A)SCS descritto nel capitolo [Uso dell'avvio automatico per le istanze di SAP][planning-guide-11.5].
Per altri dettagli, vedere anche il capitolo [Disponibilità elevata per i server applicazioni SAP][planning-guide-11.4.1].

* *più elevata* dell'istanza di SAP (A)SCS

In questo caso il riavvio delle VM di Azure viene usato per proteggere la VM con l'istanza di SAP (A)SCS installata. Nel caso di inattività pianificata o non pianificata dei server di Azure, le VM verranno riavviate in un altro server disponibile. Come accennato in precedenza, il riavvio delle VM di Azure protegge principalmente le VM e NON le applicazioni, in questo casto l'istanza di (A)SCS. Con il riavvio delle VM si otterrà indirettamente una "disponibilità più elevata" dell'istanza di SAP (A)SCS. Per garantire l'avvio automatico dell'istanza di (A)SCS dopo il riavvio della VM, assicurarsi di impostare il parametro di avvio automatico nel profilo di avvio dell'istanza di (A)SCS descritto nel capitolo [Uso dell'avvio automatico per le istanze di SAP][planning-guide-11.5]. Ciò significa che l'istanza di (A)SCS come singolo punto di errore (SPOF) in esecuzione in una singola VM sarà il fattore determinante per la disponibilità dell'intero panorama applicativo SAP. 

* *più elevata* del server DBMS

In questo caso, analogamente al caso d'uso dell'istanza di SAP (A)SCS, il riavvio delle VM di Azure viene usato per proteggere la VM con il software DBMS installato e si ottiene una "disponibilità più elevata" del software DBMS. DBMS in esecuzione in una singola VM è anche uno SPOF ed è il fattore determinante per la disponibilità dell'intero panorama applicativo SAP. 

### <a name="sap-application-high-availability-on-azure-iaas"></a>Disponibilità elevata delle applicazioni SAP su IaaS di Azure
Per ottenere una disponibilità elevata dei sistemi SAP completi, è necessario proteggere tutti componenti dei sistemi SAP critici (ad esempio, i server applicazioni SAP ridondanti), e i componenti univoci (ad esempio, i singoli punti di errore), come istanza di SAP (A)SCS e DBMS. 

#### <a name="a-name5d9d36f99058435d83675ad05f00de77ahigh-availability-for-sap-application-servers"></a><a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Disponibilità elevata per i server applicazioni SAP
Per le istanze dei server/delle finestre di dialogo delle applicazioni SAP non è necessario considerare una specifica soluzione a disponibilità elevata. La disponibilità elevata si ottiene semplicemente con la ridondanza che deve quindi essere presente in livelli sufficienti nelle varie macchine virtuali. Le macchine virtuali devono essere tutte posizionate nel set di disponibilità di Azure per evitare che vengano aggiornate durante il periodo di inattività dovuto alla manutenzione pianificata. La funzionalità di base fondata su diversi domini di aggiornamento e di errore all'interno di un'unità di scala di Azure è stata già introdotta nel capitolo [Domini di aggiornamento][planning-guide-3.2.2]. I set di disponibilità di Azure sono stati presentati nel capitolo [Set di disponibilità di Azure][planning-guide-3.2.3] di questo documento. 

Non esiste alcun numero infinito di domini di errore e di aggiornamento che può essere usato da un set di disponibilità di Azure all'interno di un'unità di scala di Azure. Ciò significa che se si inseriscono molte VM in un set di disponibilità, prima o poi una VM finirà nello stesso dominio di aggiornamento o di errore.

Se si distribuiscono poche istanze dei server applicazioni SAP nelle relative VM dedicate e si suppone che siano presenti cinque domini di aggiornamento, alla fine emerge il quadro seguente. Il numero massimo effettivo di domini di errore e di aggiornamento all'interno di un set di disponibilità potrebbe cambiare in futuro:

![Disponibilità elevata dei i server applicazioni SAP in Azure][planning-guide-figure-3000]

Per altri dettagli, vedere questo documento: <http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-the-sap-ascs-instance-on-windows"></a>Disponibilità elevata per l'istanza di SAP (A)SCS in Windows
WSFC (Windows Server Failover Cluster) è una soluzione usata di frequente per proteggere l'istanza di SAP (A)SCS. È anche integrato in SAPinst in forma di un'installazione a "disponibilità elevata". Attualmente l'infrastruttura di Azure non può fornire la funzionalità di configurazione del cluster Windows Server Failover Cluster necessario seguendo la stessa procedura usata in locale.

Al mese di gennaio 2016 la piattaforma cloud di Azure che esegue il sistema operativo Windows non offre la possibilità di usare un volume condiviso cluster su un disco condiviso tra due VM di Azure.

Tuttavia, una soluzione valida prevede l'utilizzo di software di terze parti che offre un volume condiviso usando la replica disco sincrona e trasparente che può essere integrata in WSFC. Questo approccio implica che solo il nodo del cluster attivo possa accedere a una delle copie del disco in un punto nel tempo. Al mese di gennaio 2016 questa configurazione a disponibilità elevata è supportata per proteggere l'istanza di SAP (A)SCS nel sistema operativo guest Windows su VM di Azure in combinazione con software di terze parti SIOS DataKeeper.

La soluzione SIOS DataKeeper fornisce una risorsa cluster di dischi condivisi ai cluster di failover di Windows con:

* Un disco rigido virtuale di Azure aggiuntivo collegato a ciascuna delle macchine virtuali in una configurazione cluster di Windows
* SIOS DataKeeper Cluster Edition in esecuzione in entrambi i nodi delle VM
* SIOS DataKeeper Cluster Edition configurato per riflettere in modo sincrono il contenuto del volume collegato del disco rigido virtuale aggiuntivo dalle VM di origine al volume collegato del disco rigido virtuale aggiuntivo della VM di destinazione.
* SIOS DataKeeper astrae i volumi locali di origine e di destinazione e li presenta al cluster di failover di Windows come singolo disco condiviso.

È possibile trovare tutti i dettagli su come installare Windows Server Failover Cluster con SIOS Datakeeper e SAP nel white paper [Clustering di istanze SAP ASCS/SCS usando Windows Server Failover Cluster in Azure con SIOS DataKeeper][ha-guide-classic]. 

#### <a name="high-availability-for-the-sap-ascs-instance-on-linux"></a>Disponibilità elevata per l'istanza di SAP (A)SCS in Linux
Al mese di dicembre 2015 non è disponibile alcuna soluzione equivalente a WSFC con disco condiviso per le VM Linux in Azure. Le soluzioni alternative che usano software di terze parti come SIOS per Windows non sono ancora convalidate per l'esecuzione di SAP su Linux in Azure.

#### <a name="high-availability-for-the-sap-database-instance"></a>Disponibilità elevata per l'istanza del database SAP
La tipica configurazione del DBMS di SAP a disponibilità elevata si basa su due VM DBMS in cui la funzionalità di disponibilità elevata DBMS viene usata per replicare i dati dall'istanza di DBMS attiva alla seconda VM in un'istanza di DBMS passiva.

La funzionalità di disponibilità elevata e ripristino di emergenza per DBMS in generale nonché per DBMS specifici è descritta nella [guida alla distribuzione DBMS][dbms-guide].

#### <a name="endtoend-high-availability-for-the-complete-sap-system"></a>Disponibilità elevata end-to-end per il sistema SAP completo
Di seguito sono riportati due esempi di architettura SAP NetWeaver a disponibilità elevata in Azure, uno per Windows e l'altro per Linux.
I concetti illustrati di seguito potrebbero richiedere piccoli compromessi quando si distribuiscono molti sistemi SAP e il numero di VM distribuite supera il limite massimo di account di archiviazione per ogni sottoscrizione. In tali casi, i dischi rigidi virtuali delle VM devono essere combinati all'interno di un account di archiviazione. In genere questa operazione viene effettuata combinando i dischi rigidi virtuali delle VM del livello dell'applicazione SAP con sistemi SAP diversi.  Sono stati anche combinati diversi dischi rigidi virtuali delle VM DBMS diverse su sistemi SAP diversi in un account di archiviazione di Azure. Tenere quindi presenti i limiti di operazioni I/O al secondo degli account di archiviazione di Azure (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>)

##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows]  Disponibilità elevata in Windows
![Architettura a disponibilità elevata dell'applicazione SAP NetWeaver con SQL Server in IaaS di Azure][planning-guide-figure-3200]

I costrutti di Azure seguenti vengono usati per il sistema SAP NetWeaver, allo scopo di ridurre al minimo l'impatto esercitato dai problemi dell'infrastruttura e dall'applicazione di patch agli host:

* Il sistema completo viene distribuito in Azure (obbligatorio; il livello DBMS, l'istanza di (A)SCS e il livello dell'applicazione completa devono essere eseguiti nello stesso percorso).
* Il sistema completo viene eseguito all'interno di una sottoscrizione di Azure (obbligatorio).
* Il sistema completo viene eseguito all'interno di una rete virtuale di Azure (obbligatorio).
* La separazione delle VM di un unico sistema SAP in tre set di disponibilità è possibile anche quando tutte le VM appartengono alla stessa rete virtuale.
* Tutte le VM che eseguono istanze di DBMS di un sistema SAP sono incluse in un unico set di disponibilità. Si suppone che siano presenti più VM che eseguono istanze di DBMS per ciascun sistema perché vengono usate le funzionalità di disponibilità elevata dei DBMS nativi, come SQL Server AlwaysOn oppure Oracle Data Guard.
* Tutte le VM che eseguono istanze di DBMS usano il proprio account di archiviazione. I file di dati e di log di DBMS vengono replicati da un account di archiviazione a un altro account di archiviazione usando le funzioni a disponibilità elevata di DBMS per la sincronizzazione dei dati. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un nodo del cluster di SQL Windows, ma non dell'intero servizio di SQL Server. 
* Tutte le VM che eseguono istanze di (A)SCS di un sistema SAP sono incluse in un unico set di disponibilità. All'interno di tali VM è configurato Windows Server Failover Cluster (WSFC) per proteggere l'istanza di (A)SCS.
* Tutte le VM che eseguono istanze di (A)SCS usano il proprio account di archiviazione. I file delle istanze di (A)SCS e le cartelle globali SAP vengono replicate da un account di archiviazione a un altro account di archiviazione usando la replica SIOS DataKeeper. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un nodo del cluster di Windows (A)SCS, ma non dell'intero servizio di (A)SCS. 
* TUTTE le VM che rappresentano il livello del server applicazioni SAP sono incluse in un terzo set di disponibilità.
* TUTTE le VM che eseguono server applicazioni SAP usano il proprio account di archiviazione. L'indisponibilità di un account di archiviazione causa l'indisponibilità di un server applicazioni SAP, mentre altri server applicazioni SAP continuano a essere eseguiti.

##### <a name="linuxlogolinux-ha-on-linux"></a>![ Linux][Logo_Linux]  Disponibilità elevata in Linux
L'architettura per la disponibilità elevata SAP in Linux su Azure è essenzialmente identica a quella di Windows descritta in precedenza. Al mese di gennaio 2016 sono presenti, tuttavia, due restrizioni:

* solo SAP ASE 16 è attualmente supportato in Linux su Azure senza alcuna funzionalità di replica ASE. 
* non è ancora disponibile alcuna soluzione SAP (A)SCS a disponibilità elevata in Linux su Azure

Di conseguenza, al mese di gennaio 2016 un sistema SAP-Linux-Azure non può ottenere la stessa disponibilità di un sistema SAP-Windows-Azure a causa della mancanza di disponibilità elevata per l'istanza di (A)SCS e per il database SAP ASE a istanza singola.

### <a name="a-name4e165b5874ca474fa7f45e695a93204fausing-autostart-for-sap-instances"></a><a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Uso dell'avvio automatico per le istanze di SAP
SAP ha reso disponibile la funzionalità che consente di avviare le istanze di SAP immediatamente dopo l'avvio del sistema operativo nella VM. La procedura esatta è documentata nell'articolo della Knowledge Base SAP [1909114] relativo a come avviare istanze di SAP automaticamente usando il parametro di avvio automatico. Tuttavia, SAP sconsiglia di continuare a usare questa impostazione perché non offre alcun controllo sull'ordine dei riavvii delle istanze, supponendo che siano interessate più di una VM o vengano eseguite più istanze per ogni VM. Presupponendo uno scenario tipico di Azure di un'istanza del server applicazioni SAP in una VM e il caso di una singola VM che viene alla fine riavviata, l'avvio automatico non è realmente critico e può essere abilitato aggiungendo il parametro seguente:

    Autostart = 1

Nel profilo di avvio dell'istanza di SAP ABAP e/o di Java.

> [!NOTE]
> Il parametro di avvio automatico può presentare anche alcuni inconvenienti. Nello specifico, il parametro attiva l'avvio di un'istanza di SAP ABAP o di Java quando viene avviato il servizio di dell'istanza Windows/Linux correlato. Ciò si verifica sicuramente all'avvio dei sistemi operativi. Tuttavia, i riavvii dei servizi SAP sono comuni anche per la funzionalità di gestione del ciclo di vita di SAP come SUM o altri aggiornamenti. Queste funzionalità non prevedono il riavvio automatico di un'istanza. Quindi, il parametro di avvio automatico deve essere disabilitato prima di eseguire tali attività. Inoltre, il parametro di avvio automatico non deve essere usato per le istanze di SAP in cluster, ad esempio ASCS/SCS/CI.
> 
> 

Maggiori informazioni sull'avvio automatico per le istanze di SAP sono disponibili qui:

* [Avviare/arrestare SAP con l'avvio/l'arresto del server Unix](http://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Avviare e arrestare gli agenti di gestione di SAP NetWeaver](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [Come abilitare l'avvio automatico di Hana Database](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3tier-sap-systems"></a>Sistemi SAP a 3 livelli di maggiori dimensioni
Gli aspetti di disponibilità elevata delle configurazioni SAP a 3 livelli sono già stati esaminati nelle sezioni precedenti. Ora verrà esaminato il caso dei sistemi con requisiti del server DBMS troppo elevati per essere posizionati in Azure, ma con un livello dell'applicazione SAP che potrebbe essere distribuito in Azure.

#### <a name="location-of-3tier-sap-configurations"></a>Posizione delle configurazioni SAP a 3 livelli
La suddivisione del livello di applicazione stesso o dell'applicazione e del livello DBMS tra distribuzione locale e distribuzione in Azure non è supportata. Un sistema SAP deve essere completamente distribuito in locale OPPURE in Azure. Inoltre, non è possibile eseguire alcuni server applicazioni in locale e altri in Azure. Questa premessa rappresenta il punto iniziale della discussione. Inoltre, non è possibile distribuire componenti DBMS di un sistema SAP e il livello del server applicazioni SAP in due diverse aree di Azure. ad esempio DBMS negli Stati Uniti occidentali e il livello dell'applicazione negli Stati Uniti centrali. Il motivo per il quale tali configurazioni non sono supportate è la sensibilità di latenza dell'architettura di SAP NetWeaver.

Tuttavia, nel corso dello scorso anno i partner dei data center hanno sviluppato condivisioni percorso nelle aree di Azure. Queste condivisioni percorso sono spesso molto vicine ai data center fisici di Azure all'interno di un'area di Azure. La breve distanza e la connessione degli asset nella condivisione percorso con ExpressRoute in Azure può determinare una latenza inferiore a 2 ms. In tali casi, è possibile individuare il livello DBMS (inclusa la SAN/NAS di archiviazione) in tale condivisione percorso e il livello dell'applicazione SAP in Azure. Al mese di dicembre 2015 non sono disponibili distribuzioni del genere. Tuttavia, diversi clienti con distribuzioni di applicazioni non SAP stanno già usando questi approcci. 

### <a name="offline-backup-of-sap-systems"></a>Backup offline di sistemi SAP
In base alla configurazione SAP scelta (livello 2 o 3), potrebbe essere necessario eseguire un backup del contenuto della VM stessa oltre che del database. I backup correlati ai DBMS dovrebbero essere eseguiti con metodi di database. Una descrizione dettagliata dei diversi database è disponibile nella [guida alla distribuzione DBMS][dbms-guide]. D'altra parte, i dati SAP possono essere sottoposti a backup in modalità offline (incluso il contenuto del database) come descritto in questa sezione oppure online come descritto nella sezione successiva.

Il backup offline richiederebbe fondamentalmente un arresto della VM usando il portale di Azure e una copia del disco della VM di base, oltre a tutti i dischi rigidi virtuali collegati alla VM. Questo permetterebbe di mantenere un'immagine temporizzata della VM e del relativo disco. Si consiglia di copiare i "backup" in un altro account di archiviazione di Azure. Si applica quindi la procedura descritta nel capitolo [Copia di dischi tra account di archiviazione di Azure][planning-guide-5.4.2] di questo documento.
Oltre che dal portale di Azure, è possibile eseguire l'arresto anche usando PowerShell o l'interfaccia della riga di comando, come descritto qui: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Un ripristino dello stato comporterebbe l'eliminazione della VM di base nonché dei relativi dischi originali e dei dischi rigidi virtuali montati, la copia dei dischi rigidi virtuali salvati nell'account di archiviazione originale e quindi la ridistribuzione del sistema.
Questo articolo illustra un esempio di come generare lo script di questo processo in PowerShell: <http://www.westerndevs.com/azure-snapshots/>

Assicurarsi di installare una nuova licenza SAP perché il ripristino di un backup di VM come descritto in precedenza crea una nuova chiave hardware.

### <a name="online-backup-of-an-sap-system"></a>Backup online di un sistema SAP
Il backup del DBMS viene eseguito con metodi specifici del DBMS, come descritto nella [guida alla distribuzione DBMS][dbms-guide]. 

Altre VM all'interno del sistema SAP possono essere sottoposte a backup usando la funzionalità Backup macchina virtuale di Azure. La funzionalità Backup macchina virtuale di Azure è stata introdotta all'inizio del 2015 ed è diventata un metodo standard per eseguire il backup di una VM completa in Azure. Il backup di Azure archivia i backup in Azure e consente il ripristino di una VM. 

> [!NOTE]
> Al mese di dicembre 2015 il backup delle macchine VM NON mantiene l'ID univoco della VM usato per le licenze SAP. Ciò significa che il ripristino da un backup di VM richiede l'installazione di una nuova chiave di licenza SAP perché la VM ripristinata è considerata una nuova VM e non una sostituzione di quella precedente che era stata salvata. Al mese di gennaio 2016 il backup delle VM di Azure non supporta ancora le VM distribuite con Azure Resource Manager.
> 
> ![ Windows][Logo_Windows] Windows
> 
> In teoria, anche le VM che eseguono database possono essere sottoposte a backup in modo coerente se il sistema DBMS supporta il servizio Copia Shadow del volume di Windows (<https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) come, ad esempio, SQL Server.
> Tenere presente, tuttavia, che in base ai backup di VM di Azure, i ripristini temporizzati dei database non sono possibili. Si consiglia, quindi, di eseguire backup di database usando la funzionalità DBMS invece del backup delle VM di Azure.
> 
> Per acquisire familiarità con la funzionalità Backup macchina virtuale di Azure, iniziare da questo articolo: <https://azure.microsoft.com/documentation/articles/backup-azure-vms/>.
> 
> Altre possibilità prevedono l'uso di una combinazione di Microsoft Data Protection Manager installato in una VM di Azure e del backup di Azure per eseguire il backup e il ripristino di database. Altre informazioni sono disponibili qui: <https://azure.microsoft.com/documentation/articles/backup-azure-dpm-introduction/>.  
> 
> ![ Linux][Logo_Linux]  Linux
> 
> In Linux non esiste alcuna funzionalità equivalente al servizio Copia Shadow del volume di Windows. Quindi, è possibile eseguire solo backup coerenti con i file, ma non con l'applicazione. Il backup del DBMS di SAP deve essere eseguito usando la funzionalità DBMS. Il file system che include i dati correlati a SAP può essere salvato, ad esempio usando tar come descritto qui: <http://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
> 
> 

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure come sito di ripristino di emergenza per panorami applicativi SAP di produzione
Dalla metà del 2014 le estensioni ai vari componenti di Hyper-V, System Center e Azure consentono l'utilizzo di Azure come sito di ripristino di emergenza per le VM in esecuzione in locale basato su Hyper-V. 

Per una descrizione dettagliata di come distribuire questa soluzione, vedere il blog seguente: <http://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>

## <a name="summary"></a>Riepilogo
I punti chiave della disponibilità elevata per i sistemi SAP in Azure sono i seguenti:

* Attualmente il singolo punto di errore di SAP non può essere protetto esattamente nello stesso modo delle distribuzioni locali. Il motivo risiede nel fatto che i cluster di dischi condivisi non possono ancora essere integrati in Azure senza l'uso di software di terze parti.
* Per il livello DBMS è necessario usare la funzionalità DBMS che non si basa sulla tecnologia cluster di dischi condivisi. Informazioni dettagliate sono disponibili nella [guida alla distribuzione DBMS][dbms-guide].
* Per ridurre al minimo l'impatto dei problemi all'interno dei domini di errore nell'infrastruttura di Azure o nella manutenzione di host, è consigliabile usare i set di disponibilità di Azure:
  * È consigliabile avere un set di disponibilità per il livello di applicazione SAP.
  * È consigliabile avere un set di disponibilità separato per il livello DBMS di SAP.
  * NON è consigliabile applicare lo stesso set di disponibilità per le VM di diversi sistemi SAP.
* Per le finalità di backup del livello DBMS di SAP, consultare la [guida alla distribuzione DBMS][dbms-guide].
* Il backup delle istanze delle finestra di dialogo SAP ha poco senso perché in genere la ridistribuzione di semplici istanze di dialogo costituisce un processo più veloce.
* Ha senso, invece, il backup della VM che contiene la directory globale del sistema SAP e di tutti i profili delle varie istanze; tale backup deve essere eseguito con Windows Backup oppure, ad esempio, con tar in Linux. Dato che esistono differenze tra Windows Server 2008 (R2) e Windows Server 2012 (R2), e che il backup risulta più semplice usando le release più recenti di Windows Server, è consigliabile eseguire Windows Server 2012 (R2) come sistema operativo guest Windows. 

<!---HONumber=Oct16_HO2-->



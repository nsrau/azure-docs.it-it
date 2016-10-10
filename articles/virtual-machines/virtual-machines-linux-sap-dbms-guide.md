<properties
   pageTitle="SAP NetWeaver in macchine virtuali Linux - Guida alla distribuzione DBMS | Microsoft Azure"
   description="SAP NetWeaver in macchine virtuali Linux - Guida alla distribuzione DBMS"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione DBMS

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-linux-sap-dbms-guide.md "SAP NetWeaver in macchine virtuali Linux - Guida alla distribuzione DBMS"
[dbms-guide-2.1]: virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Memorizzazione nella cache per VM e dischi rigidi virtuali"
[dbms-guide-2.2]: virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "RAID software"
[dbms-guide-2.3]: virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Archiviazione di Microsoft Azure"
[dbms-guide-2]: virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Struttura di una distribuzione RDBMS"
[dbms-guide-3]: virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Disponibilità elevata e ripristino di emergenza con macchine virtuali di Azure"
[dbms-guide-5.5.1]: virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 e versioni successive"
[dbms-guide-5.5.2]: virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 e versioni precedenti"
[dbms-guide-5.6]: virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Riepilogo generale su SQL Server per SAP in Azure"
[dbms-guide-5]: virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Specifiche per RDBMS SQL Server"
[dbms-guide-8.4.1]: virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configurazione dell'archiviazione"
[dbms-guide-8.4.2]: virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Backup e ripristino"
[dbms-guide-8.4.3]: virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Considerazioni sulle prestazioni per il backup e il ripristino"
[dbms-guide-8.4.4]: virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Altri"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-linux-sap-deployment-guide.md "SAP NetWeaver in macchine virtuali Linux - Guida alla distribuzione"
[deployment-guide-2.2]: virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Risorse SAP"
[deployment-guide-3.1.2]: virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Distribuzione di una VM con un'immagine personalizzata"
[deployment-guide-3.2]: virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP"
[deployment-guide-3.3]: virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP"
[deployment-guide-3.4]: virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP"
[deployment-guide-3]: virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Scenari di distribuzione di VM per SAP in Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Distribuzione dei cmdlet di Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Scaricare e importare i cmdlet di PowerShell pertinenti per SAP"
[deployment-guide-4.3]: virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Aggiungere la VM a un dominio locale (solo per Windows)"
[deployment-guide-4.4.2]: virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Scaricare, installare e abilitare l'agente di VM di Azure"
[deployment-guide-4.5.1]: virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Interfaccia della riga di comando di Azure"
[deployment-guide-4.5]: virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurare l'estensione di monitoraggio avanzato di Azure per SAP"
[deployment-guide-5.1]: virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP"
[deployment-guide-5.2]: virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure"
[deployment-guide-5.3]: virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configurare il monitoraggio"
[deployment-guide-configure-proxy]: virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configurare il proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-linux-sap-get-started.md
[getting-started-dbms]: virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-linux-sap-planning-guide.md "SAP NetWeaver on Linux virtual machines (VMs) – Planning and Implementation Guide (SAP NetWeaver in macchine virtuali Linux - Guida alla pianificazione e all'implementazione)"
[planning-guide-1.2]: virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Risorse"
[planning-guide-11]: virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure"
[planning-guide-11.4.1]: virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Disponibilità elevata per i server applicazioni SAP"
[planning-guide-11.5]: virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Uso dell'avvio automatico per le istanze di SAP"
[planning-guide-2.1]: virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti"
[planning-guide-2.2]: virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Più sedi locali: distribuzione di una singola VM o di più VM SAP in Azure che devono essere completamente integrate con la rete locale"
[planning-guide-3.1]: virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Aree di Azure"
[planning-guide-3.2.1]: virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Domini di errore"
[planning-guide-3.2.2]: virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Domini di aggiornamento"
[planning-guide-3.2.3]: virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Set di disponibilità di Azure"
[planning-guide-3.2]: virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Concetto di macchina virtuale di Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Archiviazione Premium di Azure"
[planning-guide-5.1.1]: virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Spostamento di una macchina virtuale da locale in Azure con un disco non generalizzato"
[planning-guide-5.1.2]: virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Distribuzione di una VM con un'immagine specifica del cliente"
[planning-guide-5.2.1]: virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato"
[planning-guide-5.2.2]: virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP"
[planning-guide-5.2]: virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparazione di VM con SAP per Azure"
[planning-guide-5.3.1]: virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Differenza tra un disco di Azure e un'immagine di Azure"
[planning-guide-5.3.2]: virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Caricamento di un disco rigido virtuale da locale ad Azure"
[planning-guide-5.4.2]: virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copia di dischi tra account di archiviazione di Azure"
[planning-guide-5.5.1]: virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Struttura di VM/dischi rigidi virtuali per distribuzioni SAP"
[planning-guide-5.5.3]: virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Impostazione del montaggio automatico per dischi collegati"
[planning-guide-7.1]: virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver"
[planning-guide-7]: virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Concetti della distribuzione solo cloud di istanze di SAP"
[planning-guide-9.1]: virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Soluzione di monitoraggio di Azure per SAP"
[planning-guide-azure-premium-storage]: virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Archiviazione Premium di Azure"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Rete di Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Archiviazione: Archiviazione di Microsoft Azure e dischi dati"

[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "Product Availability Matrix SAP"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Gestire le macchine virtuali usando Gestione risorse di Azure e PowerShell"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]: virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica.

Questa guida fa parte della documentazione relativa all'implementazione e alla distribuzione del software SAP in Microsoft Azure. Prima di leggere questa Guida, leggere la [Guida alla pianificazione e all'implementazione][planning-guide]. Questo documento illustra la distribuzione di vari sistemi di gestione di database relazionali (RDBMS) e dei prodotti correlati in combinazione con SAP in macchine virtuali di Microsoft Azure tramite le funzionalità di infrastruttura distribuita come servizio (IaaS) di Azure.

Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## Considerazioni generali
Questo capitolo introduce alcune considerazioni relative all'esecuzione di sistemi DBMS correlati a SAP in macchine virtuali di Azure. Nel capitolo sono presenti pochi riferimenti a sistemi DBMS specifici. I sistemi DBMS specifici vengono trattati nei capitoli successivi di questo documento.

### Definizioni
Nel documento verranno usati i termini seguenti:

* IaaS (Infrastructure as a Service): infrastruttura distribuita come servizio.
* PaaS (Platform as a Service): piattaforma distribuita come servizio.
* SaaS (Software as a Service): software come un servizio.
* Componente SAP: singola applicazione SAP, ad esempio ECC, BW, Solution Manager o EP. I componenti SAP possono essere basati su tecnologie ABAP o Java tradizionali o su un'applicazione non basata su NetWeaver, ad esempio Business Objects.
* Ambiente SAP: uno o più componenti SAP raggruppati in modo logico per eseguire una funzione commerciale, ad esempio sviluppo, servizio di controllo della qualità, formazione, ripristino di emergenza o produzione.
* Panorama applicativo SAP: fa riferimento a tutte le risorse SAP in un panorama applicativo IT del cliente. Il panorama applicativo SAP include tutti gli ambienti di produzione e non produzione.
* Sistema SAP: combinazione del livello DBMS e del livello applicazione, ad esempio di un sistema di sviluppo SAP ERP, un sistema di test SAP BW, un sistema di produzione SAP CRM e così via. Nelle distribuzioni di Azure non è supportata la divisione di questi due livelli tra l'istanza locale e Azure. Un sistema SAP deve quindi essere distribuito o in locale o in Azure. È tuttavia possibile distribuire i diversi sistemi di un panorama applicativo SAP in Azure o in locale. È ad esempio possibile distribuire i sistemi di sviluppo e test SAP CRM in Azure e il sistema di produzione SAP CRM in locale.
* Distribuzione solo cloud: una distribuzione in cui la sottoscrizione di Azure non è connessa tramite una connessione da sito a sito o ExpressRoute all'infrastruttura di rete locale. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche distribuzioni "solo cloud". L'accesso alle macchine virtuali distribuite con questo metodo viene effettuato tramite Internet ed endpoint Internet pubblici assegnati alle VM in Azure. L'istanza locale di Active Directory (AD) e il DNS non vengono estesi ad Azure in questi tipi di distribuzioni. Le macchine virtuali non sono quindi parte dell'istanza locale di Active Directory. Nota: le distribuzioni solo cloud in questo documento vengono definite come panorama applicativo SAP completi in esecuzione esclusivamente in Azure, senza estensione di Active Directory o risoluzione dei nomi dall'istanza locale al cloud pubblico. Le configurazioni solo cloud non sono supportate per sistemi SAP di produzione o per configurazioni in cui è necessario usare SAP STMS o altre risorse locali tra i sistemi SAP ospitati in Azure e le risorse che risiedono in locale.
* Cross-premise: indica uno scenario in cui le VM vengono distribuite in una sottoscrizione di Azure con connettività da sito a sito, multisito o ExpressRoute tra i data center locali e Azure. Nella documentazione comune su Azure questi tipi di distribuzioni vengono definiti anche scenari cross-premise. La connessione consente di estendere i domini locali, l'istanza locale di Active Directory e il DNS locale in Azure. Il panorama applicativo locale viene esteso alle risorse di Azure della sottoscrizione. Questa estensione consente alle macchine virtuali di fare parte del dominio locale. Gli utenti di dominio del dominio locale possono accedere ai server e possono eseguire servizi in queste VM, ad esempio i servizi DBMS. La comunicazione e la risoluzione dei nomi tra VM distribuite in locale e VM distribuite in Azure sono consentite. Questo è probabilmente lo scenario più comune per la distribuzione di risorse SAP in Azure. Per altre informazioni, vedere [questo][vpn-gateway-cross-premises-options] articolo e [questa][vpn-gateway-site-to-site-create] pagina.

> [AZURE.NOTE] Le distribuzioni cross-premise di sistemi SAP in cui le macchine virtuali di Azure che eseguono sistemi SAP sono membri di un dominio locale sono supportate per i sistemi SAP di produzione. Le configurazioni cross-premise sono supportate per la distribuzione parziale o completa di panorami applicativi SAP in Azure. Anche l'esecuzione del panorama applicativo SAP completo in Azure richiede che queste VM siano parte di un dominio locale e di ADS. In versioni precedenti della documentazione sono stati illustrati scenari IT ibridi, in cui il termine "ibrido" indica la presenza di una connettività cross-premise tra l'istanza locale e Azure. In questo caso "ibrido" indica anche che le macchine virtuali in Azure fanno parte dell'istanza locale di Active Directory.

Alcuni documenti Microsoft descrivono scenari cross-premise in modo leggermente diverso, in particolare per configurazioni DBMS a disponibilità elevata. Nel caso dei documenti correlati a SAP, lo scenario cross-premise è dato essenzialmente dalla presenza di connettività da sito a sito o privata (ExpressRoute) e dal fatto che il panorama applicativo SAP è distribuito tra l'istanza locale e Azure.

### Risorse
Per informazioni sull'argomento relativo alle distribuzioni SAP in Azure sono disponibili le seguenti guide:

* [SAP NetWeaver in macchine virtuali di Azure - Guida alla pianificazione e all'implementazione][planning-guide]
* [SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione][deployment-guide]
* [SAP NetWeaver in macchine virtuali di Azure - Guida alla distribuzione DBMS (questo documento)][dbms-guide]

Le note seguenti su SAP sono correlate all'argomento relativo a SAP in Azure:

| Numero della nota | Titolo
|------------|--------
| [1928533] | Applicazioni SAP in Azure: prodotti supportati e tipi di macchine virtuali di Azure
| [2015553] | SAP in Microsoft Azure: prerequisiti per il supporto
| [1999351] | Risoluzione dei problemi del monitoraggio avanzato di Azure per SAP
| [2178632] | Metriche chiave del monitoraggio per SAP in Microsoft Azure
| [1409604] | Virtualizzazione in Windows: monitoraggio avanzato
| [2191498] | SAP in Linux con Azure: monitoraggio avanzato
| [2039619] | Applicazioni SAP in Microsoft Azure che usano il database Oracle: prodotti supportati e relative versioni
| [2233094] | DB6: Informazioni aggiuntive sulle applicazioni SAP in Azure che usano IBM DB2 per Linux, UNIX e Windows
| [2243692] | Linux in una macchina virtuale di Microsoft Azure (IaaS): problemi delle licenze SAP
| [1984787] | SUSE LINUX Enterprise Server 12: note di installazione
| [2002167] | Red Hat Enterprise Linux 7. x: installazione e aggiornamento

Leggere anche il [wiki su SCN](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contenente tutte le note SAP per Linux.

È necessario conoscere l'architettura Microsoft Azure e il funzionamento delle macchine virtuali di Microsoft Azure e sapere come queste vengono distribuite. Altre informazioni sono disponibili qui <https://azure.microsoft.com/documentation/>
 
> [AZURE.NOTE] Questo documento **non** illustra le offerte relative alla piattaforma Microsoft Azure come piattaforma distribuita come servizio (PaaS). Questo documento descrive l'esecuzione di un sistema di gestione di database (DBMS) in macchine virtuali di Microsoft Azure (IaaS) come se il sistema DBMS fosse eseguito nell'ambiente locale. Le funzionalità di database di queste due offerte sono molto diverse e non devono essere confuse tra loro. Vedere anche: <https://azure.microsoft.com/services/sql-database/>

Per quanto riguarda la tecnologia IaaS, l'installazione e la configurazione in Windows, Linux e DBMS sono sostanzialmente simili a quanto avviene per qualsiasi macchina virtuale o computer bare metal che venga installato in locale. Quando si usa la tecnologia IaaS, ci sono tuttavia alcune differenze nelle decisioni relative all'architettura e all'implementazione della gestione del sistema. Lo scopo di questo documento è spiegare le differenze specifiche relative all'architettura e alla gestione del sistema che è necessario conoscere quando si usa la tecnologia IaaS.

Il documento prende in esame le differenze generali nelle aree seguenti:

* Pianificazione del layout di VM/VHD appropriato dei sistemi SAP per garantire il layout dei file di dati corretto e la possibilità di raggiungere un numero di IOPS sufficiente per il carico di lavoro.
* Considerazioni sulla rete per l'uso della tecnologia IaaS.
* Uso di funzionalità di database specifiche per ottimizzare il layout dei database.
* Considerazioni sul backup e il ripristino nella tecnologia IaaS.
* Utilizzo di diversi tipi di immagini per la distribuzione.
* Disponibilità elevata nella tecnologia IaaS di Azure.

## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struttura di una distribuzione RDBMS
Per poter seguire questo capitolo è necessario comprendere quanto illustrato in [questo][deployment-guide-3] capitolo della [Guida alla distribuzione][deployment-guide]. Prima di leggere questo capitolo è necessario conoscere le diverse serie di macchine virtuali e le relative differenze, nonché le differenze tra Archiviazione Premium e Standard di Azure.

Fino a marzo 2015, le dimensioni dei dischi rigidi virtuali di Azure contenenti un sistema operativo erano limitate a 127 GB. Questa limitazione è stata rimossa a marzo 2015. Per altre informazioni, vedere <https://azure.microsoft.com/blog/2015/03/25/azure-vm-os-drive-limit-octupled/>. A partire da tale data, i dischi rigidi virtuali contenenti il sistema operativo possono avere le stesse dimensioni di altri dischi rigidi virtuali. È tuttavia preferibile una struttura di distribuzione in cui il sistema operativo, il sistema DBMS ed eventuali file binari SAP siano separati dai file di database. Nei sistemi SAP in esecuzione in macchine virtuali di Azure la macchina virtuale o il disco rigido virtuale di base dovrà quindi essere installato con il sistema operativo, i file eseguibili del sistema di gestione di database e i file eseguibili SAP. I dati e i file di log DBMS verranno memorizzati in Archiviazione di Azure, Standard o Premium, in file VHD separati e collegati come dischi logici all'immagine VM del sistema operativo Azure originale.

A seconda che si usi l'archiviazione Premium o Standard di Azure, ad esempio con le VM serie GS o serie DS, in Azure sono previste altre quote, illustrate [qui][virtual-machines-sizes]. Nella pianificazione dei dischi rigidi virtuali di Azure è necessario trovare l'equilibrio ottimale tra le quote per gli elementi seguenti:

* Numero di file di dati.
* Numero di dischi rigidi virtuali che contengono i file.
* Quote di IOPS per un singolo disco rigido virtuale.
* Velocità effettiva dei dati per ogni disco rigido virtuale.
* Numero di dischi rigidi virtuali aggiuntivi possibili per dimensioni di macchina virtuale.
* La velocità effettiva di archiviazione complessiva che una macchina virtuale può offrire.
 
Azure impone una quota di IOPS per ogni unità disco rigido virtuale. I dischi rigidi virtuali ospitati in Archiviazione Standard di Azure e quelli ospitati in Archiviazione Premium hanno quote diverse. Anche la latenza di I/O è molto diversa tra i due tipi di archiviazione e Archiviazione Premium offre certamente latenze di I/O migliori. A ognuno dei diversi tipi di macchine virtuali è possibile collegare un numero limitato di dischi rigidi virtuali. Un'altra restrizione è data dal fatto che solo determinati tipi di macchine virtuali possono usare Archiviazione Premium di Azure. Ciò significa che la scelta di un determinato tipo di macchina virtuale potrebbe non basarsi soltanto sui requisiti di CPU e memoria, ma anche sui requisiti di IOPS, latenza e velocità effettiva che in genere vengono ridimensionati con il numero di dischi rigidi virtuali o il tipo di dischi di Archiviazione Premium. In particolare con Archiviazione Premium, le dimensioni del disco rigido virtuale possono essere condizionate anche dal numero di IOPS e dalla velocità effettiva necessaria per ogni disco rigido virtuale.

Il fatto che la velocità di IOPS complessiva, il numero di dischi rigidi virtuali montati e le dimensioni della macchina virtuale siano collegati, può determinare delle differenze tra una configurazione di Azure di un sistema SAP e la relativa distribuzione locale. I limiti di IOPS per numero di unità logica (LUN) sono in genere configurabili nelle distribuzioni locali. In Archiviazione di Azure, invece, tali limiti sono fissi oppure dipendono dal tipo di disco, come in Archiviazione Premium. Nelle distribuzioni locali si possono trovare configurazioni dei clienti con server di database che usano molti volumi diversi per file eseguibili speciali come SAP e DBMS o volumi speciali per spazi di tabella o database temporanei. Lo spostamento di un sistema locale come questo viene in Azure può portare a uno spreco di larghezza di banda in IOPS dovuto all'uso di un disco rigido virtuale per file eseguibili o database che eseguono un numero di IOPS molto basso o nullo. Nelle macchine virtuali di Azure è quindi consigliabile che i file eseguibili SAP e DBMS siano installati nel disco del sistema operativo, se possibile.

La posizione dei file di database e dei file di log e il tipo di Archiviazione di Azure usato devono essere definiti in base ai requisiti di IOPS, latenza e velocità effettiva. Per avere un numero di IOPS sufficiente per il log delle transazioni, potrebbe essere necessario usare più dischi rigidi virtuali per il file di log delle transazioni o usare un disco di Archiviazione Premium di dimensioni maggiori. In tal caso sarà sufficiente creare un RAID software, ad esempio un pool di archiviazione per Windows o MDADM e LVM (Logical Volume Manager) per Linux, con i dischi rigidi virtuali che conterranno il log delle transazioni.

___

> ![Windows][Logo_Windows] Windows
>
> In una macchina virtuale di Azure D:\\ è un'unità non persistente supportata da alcuni dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto dell'unità D:\\ vengono perse al riavvio della macchina virtuale. Per "tutte le modifiche" si intende il salvataggio di file, la creazione di directory, l'installazione di applicazioni e così via.
>
> ![Linux][Logo_Linux] Linux
>
> Nelle macchine virtuali Linux di Azure viene montata automaticamente un'unità in /mnt/resource. Si tratta di un'unità non persistente supportata da dischi locali nel nodo di calcolo di Azure. Il fatto che sia non persistente implica che tutte le modifiche apportate al contenuto in /mnt/resource vengono perse al riavvio della macchina virtuale. Per "tutte le modifiche" si intende il salvataggio di file, la creazione di directory, l'installazione di applicazioni e così via.

___

A seconda della serie della macchina virtuale di Azure i dischi locali nel nodo di calcolo mostrano prestazioni differenti, che possono essere classificate come segue:

* A0-A7: prestazioni molto limitate. L'unico uso possibile è il file di paging di Windows.
* A8-A11: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva > 1 GB/sec.
* Serie D: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva > 1 GB/sec.
* Serie DS: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva > 1 GB/sec.
* Serie G: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva > 1 GB/sec.
* Serie GS: caratteristiche di prestazioni molto valide con circa diecimila IOPS e velocità effettiva > 1 GB/sec.

Le indicazioni precedenti sono applicabili ai tipi di macchine virtuali certificati per SAP. Le serie di VM con valori di IOPS e velocità effettiva eccellenti sono adatte all'uso da parte di alcune funzionalità DBMS, come tempdb o lo spazio di tabella temporanea.

### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching per VM e dischi rigidi virtuali
Quando si creano i dischi o i dischi rigidi virtuali tramite il portale o quando si montano i dischi rigidi virtuali caricati nelle macchine virtuali, è possibile scegliere se memorizzare nella cache il traffico di I/O tra la macchina virtuale e i dischi rigidi virtuali presenti in Archiviazione di Azure. Archiviazione Premium e Archiviazione Standard di Azure usano due tecnologie diverse per questo tipo di cache. In entrambi i casi il backup della cache stessa viene eseguito nelle stesse unità usate dal disco temporaneo della macchina virtuale, ovvero D:\\ in Windows o /mnt/resource in Linux.
 
Per Archiviazione Standard di Azure le opzioni di memorizzazione nella cache sono:

* Nessuna memorizzazione nella cache
* Memorizzazione nella cache di lettura
* Memorizzazione nella cache di lettura e scrittura

Per ottenere prestazioni coerenti e deterministiche, è necessario impostare il caching nell'archiviazione Standard di Azure per tutti i dischi rigidi virtuali contenenti **file di dati correlati a DBMS, file di log e spazi di tabella impostati su "NONE"**. Per la memorizzazione nella cache della macchina virtuale è possibile lasciare il valore predefinito.

Per Archiviazione Premium di Azure le opzioni di memorizzazione nella cache sono:

* Nessuna memorizzazione nella cache
* Memorizzazione nella cache di lettura

Per l'archiviazione Premium di Azure si raccomanda di scegliere il **caching di lettura per i file di dati** del database SAP e **nessun caching per i dischi rigidi virtuali dei file di log**.

### <a name="c8e566f9-21b7-4457-9f7f-126036971a91"></a>RAID software
Come già indicato in precedenza, è necessario bilanciare il numero di IOPS necessario per i file di database nel numero di dischi rigidi virtuali che è possibile configurare e il numero massimo di IOPS fornito da una macchina virtuale di Azure per tipo di disco di Archiviazione Premium o VHD. Il modo più semplice per gestire il carico di IOPS nei dischi rigidi virtuali è compilare un RAID software nei diversi VHD. Quindi posizionare alcuni file di dati del DBMS SAP nei LUN ricavati dal RAID software. In base ai requisiti si può prendere in considerazione anche l'uso di Archiviazione Premium, dato che due dei tre diversi dischi di Archiviazione Premium garantiscono una quota di IOPS maggiore dei dischi rigidi virtuali basati su Archiviazione Standard. Archiviazione Premium di Azure offre anche una latenza di I/O notevolmente migliore.

Questo vale anche per il log delle transazioni dei vari sistemi DBMS. Se sono presenti numerosi sistemi DBMS, la semplice aggiunta di file di log delle transazioni non risulta efficace, dal momento che i sistemi DBMS scrivono soltanto in uno dei file alla volta. Se è necessaria una velocità di IOPS maggiore di quella offerta da un singolo disco rigido virtuale basato su Archiviazione Standard, è possibile eseguire lo striping tra più dischi rigidi virtuali di Archiviazione Standard. In alternativa è possibile usare un tipo di disco di Archiviazione Premium di dimensioni maggiori che, oltre a velocità di IOPS superiori, offra una latenza più bassa per le operazioni di I/O di scrittura nel log delle transazioni.
 
Ecco alcuni esempi di situazioni che si sono verificate nelle distribuzioni di Azure e per le quali sarebbe preferibile usare un RAID software:

* Log delle transazioni o log di rollforward che richiedono un numero di IOPS maggiore di quanto sia possibile ottenere con un singolo disco rigido virtuale di Azure. Come indicato in precedenza, questo problema può essere risolto con la compilazione di un LUN in più dischi rigidi virtuali tramite un RAID software.
* Distribuzione non uniforme del carico di lavoro di I/O nei vari file di dati del database SAP. In questi casi uno dei file di dati potrebbe raggiunge la quota piuttosto spesso, mentre gli altri non si avvicinano neppure alla quota di IOPS di un singolo disco rigido virtuale. La soluzione più semplice consiste nella compilazione di un LUN in più dischi rigidi virtuali tramite un RAID software.
* Non si conosce l'esatto carico di lavoro di I/O per ogni file di dati e si conosce solo approssimativamente il carico di lavoro di IOPS complessivo nel sistema DBMS. La soluzione più semplice consiste nel compilare un unico LUN usando un RAID software. La somma delle quote di più dischi rigidi virtuali dietro il LUN dovrebbe quindi fornire la velocità di IOPS nota.

___

> ![Windows][Logo_Windows] Windows
>
> L'uso di Windows Server 2012 o di spazi di archiviazione superiori è preferibile perché risulta più efficiente rispetto allo striping in Windows di versioni precedenti di Windows. Quando si usa Windows Server 2012 come sistema operativo, potrebbe essere necessario creare gli spazi di archiviazione e i pool di archiviazione di Windows tramite comandi di PowerShell. I comandi di PowerShell sono disponibili qui <https://technet.microsoft.com/library/jj851254.aspx>

> 
> ![Linux][Logo_Linux] Linux
>
> Per compilare un RAID software in Linux sono supportati solo MDADM e LVM (Logical Volume Manager). Per altre informazioni, leggere gli articoli seguenti:
>
> * [Configurare RAID software su Linux][virtual-machines-linux-configure-raid] \(per MDADM)
> * [Configurare LVM in una macchina virtuale Linux in Azure][virtual-machines-linux-configure-lvm]


___

Considerazioni tipiche relative all'uso di serie di macchine virtuali compatibili con Archiviazione Premium di Azure:

* Richieste di latenza di I/O che si avvicinano alle possibilità di dispositivi SAN o NAS.
* Richiesta di una latenza di I/O molto superiore alle possibilità di Archiviazione Standard di Azure.
* IOPS per macchina virtuale superiore al numero che è possibile raggiungere con più dischi rigidi virtuali di Archiviazione Standard rispetto a un determinato tipo di macchina virtuale.

Dato che l'Archiviazione di Azure sottostante replica ogni disco rigido virtuale in almeno tre nodi di archiviazione, è possibile usare un semplice striping RAID 0. Non è necessario implementare RAID 5 o RAID 1.

### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Archiviazione di Microsoft Azure
Archiviazione di Microsoft Azure memorizza la macchina virtuale di base, con il sistema operativo e i dischi rigidi virtuali o i BLOB in almeno tre nodi di archiviazione distinti. Quando si crea un account di archiviazione, è possibile scegliere il livello di protezione come illustrato di seguito:

![Replica geografica abilitata per l'account di archiviazione di Azure][dbms-guide-figure-100]

La replica locale di Archiviazione di Azure, con ridondanza locale, offre livelli di protezione contro la perdita di dati a causa di errori dell'infrastruttura che pochi clienti potrebbero permettersi di distribuire. Come mostra la figura, sono disponibili quattro opzioni diverse più una quinta che rappresenta una variante di una delle prime tre. Esaminando tali opzioni più da vicino è possibile distinguere:

* **Archiviazione con ridondanza locale Premium**: l'archiviazione Premium di Azure offre prestazioni elevate e supporto di dischi a bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O intensivo. Sono disponibili tre repliche dei dati all'interno dello stesso data center di Azure in un'area di Azure. Le copie saranno in domini di aggiornamento e di errore diversi. Per i relativi concetti, vedere [questo][planning-guide-3.2] capitolo della [Guida alla pianificazione][planning-guide]. In caso di una replica dei dati fuori servizio a causa di un errore del nodo archiviazione o di un errore del disco, viene generata automaticamente una nuova replica.
* **Archiviazione con ridondanza locale**: in questo caso sono disponibili tre repliche dei dati all'interno dello stesso data center di Azure in un'area di Azure. Le copie saranno in domini di aggiornamento e di errore diversi. Per i relativi concetti, vedere [questo][planning-guide-3.2] capitolo della [Guida alla pianificazione][planning-guide]. In caso di una replica dei dati fuori servizio a causa di un errore del nodo archiviazione o di un errore del disco, viene generata automaticamente una nuova replica.
* **Archiviazione con ridondanza geografica**: in questo caso è presente una replica asincrona che esegue il feed di tre repliche dei dati aggiuntive in un'altra area di Azure. Nella maggior parte dei casi quest'ultima si trova nella stessa area geografica, ad esempio Europa settentrionale ed Europa occidentale. In questo modo si ottengono tre repliche aggiuntive, per un totale di sei repliche. Una variante di questa opzione prevede un'aggiunta in cui i dati nell'area di Azure con replica geografica possono essere usati per la lettura. In tal caso si parla di archiviazione con ridondanza geografica e accesso in lettura.
* **Archiviazione con ridondanza della zona (ZRS)**: in questo caso le tre repliche dei dati rimangono nella stessa area di Azure. Come spiegato in [questo][planning-guide-3.1] capitolo della [Guida alla pianificazione][planning-guide], un'area di Azure può essere costituita da alcuni data center vicini tra loro. Nel caso dell'archiviazione con ridondanza locale le repliche vengono distribuite nei vari data center che costituiscono un'area di Azure.

Ulteriori informazioni sono disponibili [qui][storage-redundancy].
 
> [AZURE.NOTE] Per le distribuzioni DBMS, non è consigliabile usare l'archiviazione con ridondanza geografica.
>
> La replica geografica di Archiviazione di Azure è asincrona. La replica dei singoli dischi rigidi virtuali montati in una singola macchina virtuale non è sincronizzata nel passaggio di blocco. Non è quindi appropriata per la replica di file DBMS che vengono distribuiti tra diversi dischi rigidi virtuali o in un RAID software basato su più VHD. Il software DBMS richiede che l'archiviazione su disco persistente sia sincronizzata con precisione nei vari LUN e nei dischi, nei dischi rigidi virtuali e negli spindle sottostanti. Il software DBMS usa vari meccanismi per ordinare in sequenza le attività di scrittura I/O. Se queste variano anche di pochi millisecondi, un DBMS segnala che l'archiviazione su disco di destinazione della replica è danneggiata. Di conseguenza, se è assolutamente necessario implementare una configurazione database con un database esteso su più dischi rigidi virtuali con replica geografica, tale replica deve essere eseguita con strumenti e funzionalità di database. Per eseguire questo processo non è possibile fare affidamento sulla replica geografica di Archiviazione di Azure.
>
> Il problema risulta più semplice da spiegare con un sistema di esempio. Si supponga di avere un sistema SAP caricato in Azure con otto dischi rigidi virtuali contenenti file di dati del sistema DBMS e un disco rigido virtuale contenente il file di log delle transazioni. In ognuno di questi nove dischi rigidi virtuali i dati verranno scritti con un metodo coerente con il sistema DBMS, sia che vengano scritti in file di dati o in file di log delle transazioni.
>
> Per eseguire correttamente la replica geografica dei dati e mantenere un'immagine coerente del database, la replica geografica del contenuto di tutti i nove dischi rigidi virtuali dovrebbe essere eseguita nell'ordine esatto in cui sono state eseguite le operazioni di I/O nei nove VHD. Tuttavia, la replica geografica di Archiviazione di Azure non consente di dichiarare dipendenze tra i dischi rigidi virtuali. Ciò significa che la replica geografica di Archiviazione di Microsoft Azure ignora che i contenuti dei nove dischi rigidi virtuali sono correlati tra loro e che le modifiche ai dati sono coerenti solo quando la replica viene eseguita nell'ordine in cui si sono verificate le operazioni di I/O in tutti i nove VHD.
>
> Oltre alla possibilità concreta che le immagini con replica geografica nello scenario non forniscano un'immagine coerente del database, l'archiviazione con ridondanza geografica comporta anche una riduzione delle prestazioni che può influire notevolmente sulle prestazioni generali. In breve, non è consigliabile usare questo tipo di ridondanza di archiviazione per carichi di lavoro di tipo DBMS.
 
#### Mapping di dischi rigidi virtuali in account di archiviazione del servizio Macchina virtuale di Azure
Un account di archiviazione di Azure non è soltanto un costrutto amministrativo, ma è anche soggetto a limitazioni. Le limitazioni variano a seconda che si parli di account di archiviazione Standard o Premium di Azure. Le funzionalità e le limitazioni sono elencate nel dettaglio [qui][storage-scalability-targets]
 
Per l'archiviazione Standard di Azure è quindi importante tenere presente che è previsto un limite di IOPS per account di archiviazione. Vedere in proposito la riga contenente la frequenza di richiesta totale in [questo articolo][storage-scalability-targets]. Esiste poi un limite iniziale di 100 account di archiviazione per ogni sottoscrizione di Azure (a partire da luglio 2015). È quindi consigliabile bilanciare il numero di IOPS delle macchine virtuali tra più account di archiviazione quando si usa Archiviazione Standard di Azure. Teoricamente, una singola macchina virtuale dovrebbe usare un solo account di archiviazione, se possibile. Se si parla quindi di distribuzioni DBMS in cui ogni disco rigido virtuale ospitato in Archiviazione Standard di Azure potrebbe raggiungere il relativo limite di quota, è necessario distribuire solo 30-40 dischi rigidi virtuali per ogni account di archiviazione di Azure che usa Archiviazione Standard di Azure. D'altra parte, usare Archiviazione Premium di Azure per archiviare volumi di database di grandi dimensioni potrebbe essere possibile in termini di IOPS. Un account di archiviazione Premium di Azure, però, è molto più restrittivo in termini di volume di dati rispetto a un account di archiviazione Standard di Azure. Di conseguenza, è possibile distribuire solo un numero limitato di dischi rigidi virtuali all'interno di un account di archiviazione Premium di Azure prima di raggiungere il limite di volume di dati. Un account di archiviazione di Azure può essere paragonato a una "SAN virtuale" con funzionalità limitate in termini di IOPS e/o di capacità. In quest'ottica, come nelle distribuzioni locali, occorre poi definire il layout dei dischi rigidi virtuali dei diversi sistemi SAP nei vari "dispositivi SAN immaginari" o account di archiviazione di Azure.
 
Per Archiviazione Standard di Azure non è consigliabile presentare a una singola macchina virtuale risorse di archiviazione da diversi account di archiviazione, se possibile.

Usando invece VM di Azure della serie DS o GS è possibile montare dischi rigidi virtuali da account di archiviazione Standard e Premium di Azure. Risorse di archiviazione così eterogenee possono essere usate, ad esempio, per la scrittura di backup in dischi rigidi virtuali supportati da Archiviazione Standard con dati DBMS e file di log in Archiviazione Premium.

In base ai test e alle distribuzioni del cliente, è possibile effettuare il provisioning di circa 30-40 dischi rigidi virtuali contenenti file di dati di database e file di log in un singolo account di archiviazione Standard di Azure e ottenere prestazioni accettabili. Come accennato in precedenza, è più probabile che l'account di archiviazione Premium di Azure presenti limitazioni in termini di capacità anziché di IOPS.

Come per i dispositivi SAN locali, la condivisione deve essere monitorata allo scopo di rilevare i colli di bottiglia in un account di archiviazione di Azure. L'estensione di monitoraggio di Azure per SAP e il portale di Azure sono strumenti che permettono di rilevare account di archiviazione di Azure occupati che forniscono prestazioni di I/O non ottimali. Se viene rilevata questa situazione, è consigliabile spostare le macchine virtuali occupate in un altro account di archiviazione di Azure. Per informazioni dettagliate su come attivare le funzionalità di monitoraggio host SAP, vedere la [Guida alla distribuzione][deployment-guide].

Un altro articolo che offre un riepilogo delle procedure consigliate per l'archiviazione Standard di Azure e gli account di archiviazione Standard di Azure è disponibile qui: <https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx>
 
#### Spostamento di macchine virtuali DBMS distribuite da Archiviazione Standard in Archiviazione Premium di Azure
Quando un cliente vuole spostare una macchina virtuale distribuita da Archiviazione Standard in Archiviazione Premium di Azure, gli scenari si fanno interessanti. Non è possibile eseguire questa operazione senza spostare fisicamente i dati. A tale scopo, è possibile procedere in diversi modi:

* È possibile copiare semplicemente tutti i dischi rigidi virtuali, il VHD di base, nonché i VHD di dati in un nuovo account di archiviazione Premium di Azure. Molto spesso la scelta del numero di dischi rigidi virtuali in Archiviazione Standard di Azure non si basa sulla necessità effettiva del volume di dati, ma piuttosto sulle esigenze in termini di IOPS. Passando ad Archiviazione Premium di Azure è possibile usare un numero molto inferiore di dischi rigidi virtuali per ottenere la stessa velocità effettiva di IOPS. Dato che in Archiviazione di Azure Standard si paga la quantità di dati usati e non le dimensioni del disco nominale, il numero di dischi rigidi virtuali non è determinante in termini di costi. In Archiviazione Premium di Azure, invece, si pagano le dimensioni del disco nominale. Per questo motivo, la maggior parte dei clienti tende a limitare il numero di dischi rigidi virtuali di Azure in Archiviazione Premium al numero necessario per raggiungere la velocità effettiva di IOPS richiesta. La maggior parte dei clienti sceglie quindi una semplice copia 1:1.
* Se non è già stato fatto, è possibile montare un singolo disco rigido virtuale che può contenere un backup del database SAP. Dopo il backup, smontare tutti i dischi rigidi virtuali incluso quello contenente il backup e copiare quest'ultimo, insieme al VHD di base, in un account di archiviazione Premium di Azure. È quindi necessario distribuire la macchina virtuale basata sul VHD di base e montare il disco rigido virtuale con il backup. A questo punto è possibile creare dischi di Archiviazione Premium vuoti aggiuntivi per la macchina virtuale, che vengono usati per il ripristino del database. Questo presuppone che il sistema DBMS consenta di modificare i percorsi dei file di dati e di log come parte del processo di ripristino.
* Un'altra possibilità è una variante del processo precedente. È sufficiente copiare il disco rigido virtuale di backup in Archiviazione Premium di Azure e collegarlo a una macchina virtuale appena distribuita e installata.
* La quarta possibilità si applica al caso in cui è necessario modificare il numero di file di dati del database. In tal caso si esegue una copia di sistema omogeneo SAP usando le funzionalità di importazione ed esportazione. Inserire i file di esportazione in un disco rigido virtuale copiato in un account di archiviazione Premium di Azure e collegare il VHD a una macchina virtuale che consente di eseguire i processi di importazione. Questa possibilità viene usata dai clienti principalmente per ridurre il numero di file di dati.

### Distribuzione di macchine virtuali per SAP in Azure
Microsoft Azure offre diversi modi per distribuire le VM e i dischi associati. È quindi molto importante comprendere le differenze, perché le operazioni di preparazione delle VM possono variare in base al metodo di distribuzione. In generale vengono esaminati gli scenari descritti nei capitoli seguenti.

#### Distribuzione di una macchina virtuale da Azure Marketplace
Per distribuire la VM si sceglie di usare un'immagine presa da Azure Marketplace, fornita da Microsoft o da terze parti. Dopo aver distribuito la VM in Azure, si installa il software SAP all'interno della VM con le stesse linee guida e gli stessi strumenti usati in un ambiente locale. Per l'installazione del software SAP all'interno della macchina virtuale di Azure, SAP e Microsoft consigliano di caricare e archiviare il supporto di installazione SAP in dischi rigidi virtuali di Azure o di creare una VM di Azure che funge da "file server" contenente tutti i supporti di installazione SAP necessari.

#### Distribuzione di una macchina virtuale con un'immagine generalizzata specifica del cliente
Le immagini disponibili in Azure Marketplace potrebbero non soddisfare le esigenze a causa di specifici requisiti di patch riguardanti la versione del sistema operativo o DBMS corrente. Per questo motivo, potrebbe essere necessario creare una macchina virtuale usando una propria immagine "privata" di macchina virtuale del sistema operativo o DBMS che in seguito potrà essere distribuita diverse volte. Per preparare tale immagine "privata" per la duplicazione, è necessario generalizzare il sistema operativo nella macchina virtuale locale. Per informazioni su come generalizzare una VM, vedere la [Guida alla distribuzione][deployment-guide].

Se sono già stati installati contenuti SAP nella VM locale, in particolare per sistemi a 2 livelli, è possibile adattare le impostazioni di sistema SAP dopo la distribuzione della VM di Azure con la procedura di ridenominazione dell'istanza supportata da SAP Software Provisioning Manager (nota SAP [1619720]). In caso contrario, è possibile installare il software SAP in un secondo momento dopo la distribuzione della macchina virtuale di Azure.
 
Per quanto riguarda il contenuto di database usato dall'applicazione SAP, è possibile generare nuovo contenuto tramite un'installazione SAP oppure importarlo in Azure usando un disco rigido virtuale con un backup del database DBMS o sfruttando le funzionalità del sistema DBMS per eseguire il backup direttamente in Archiviazione di Microsoft Azure. In tal caso, è anche possibile preparare i dischi rigidi virtuali con i file di log e i dati DBMS in locale e quindi importarli come dischi in Azure. Tuttavia, il trasferimento di dati DBMS caricati da locale in Azure funziona solo su dischi rigidi virtuali preparati in locale.

#### Spostamento di una macchina virtuale da locale in Azure con un disco non generalizzato
Per spostare un sistema SAP specifico da locale in Azure, è possibile caricare in Azure il disco rigido virtuale contenente il sistema operativo, i file binari SAP e gli eventuali file binari DBMS, nonché i dischi rigidi virtuali con i file di dati e di log del sistema DBMS. A differenza dello scenario 2 illustrato in precedenza, il nome host, il SID di SAP e gli account utente SAP vengono mantenuti nella macchina virtuale di Azure perché sono stati configurati nell'ambiente locale. Di conseguenza, non è necessario generalizzare l'immagine. Questo caso si applica prevalentemente a scenari cross-premise in cui il panorama applicativo SAP viene eseguito in parte in locale e in parte in Azure.

## <a name="871dfc27-e509-4222-9370-ab1de77021c3"></a>Disponibilità elevata e ripristino di emergenza con VM di Azure
Di seguito sono illustrate le funzionalità di disponibilità elevata e ripristino di emergenza offerte da Azure valide per diversi componenti usati per le distribuzioni SAP e DBMS.

### Macchine virtuali distribuite in nodi di Azure
La piattaforma Azure non offre funzionalità quali Live Migration per le macchine virtuali distribuite. Ciò significa che se sono necessari interventi di manutenzione in un cluster di server in cui è distribuita una macchina virtuale, questa deve essere arrestata e riavviata. In Azure la manutenzione viene eseguita usando i domini di aggiornamento all'interno dei cluster di server. È possibile eseguire la manutenzione di un solo dominio di aggiornamento alla volta. Durante tale intervento, mentre la macchina virtuale viene arrestata, sottoposta a manutenzione e riavviata si verifica un'interruzione del servizio. La maggior parte dei fornitori di sistemi DBMS fornisce, tuttavia, funzionalità di disponibilità elevata e ripristino di emergenza che consentono di riavviare rapidamente i servizi DBMS in un altro nodo, se il nodo primario non è disponibile. La piattaforma Azure offre funzionalità per la distribuzione di macchine virtuali, il servizio Archiviazione e altri servizi di Azure nei domini di aggiornamento per limitare gli errori di infrastruttura o di manutenzione pianificati a un sottoinsieme minimo di macchine virtuali o servizi. Con un'attenta pianificazione è possibile ottenere livelli di disponibilità simili alle infrastrutture locali.

I set di disponibilità di Microsoft Azure sono un raggruppamento logico di VM o servizi che garantisce la distribuzione delle VM e degli altri servizi in diversi domini di aggiornamento e di errore all'interno di un cluster. In questo modo, si verifica l'arresto di un solo nodo in un determinato momento. Per altre informazioni, vedere [questo articolo][virtual-machines-manage-availability].

Questa funzione deve essere configurata appositamente durante il rollout delle macchine virtuali, come illustrato di seguito:

![Definizione di un set di disponibilità per le configurazioni a disponibilità DBMS][dbms-guide-figure-200]

Allo scopo di creare configurazioni a disponibilità elevata di distribuzioni DBMS, indipendentemente dalla singola funzionalità DBMS con disponibilità elevata usata, per le macchine virtuali DBMS è necessario quanto segue:

* Aggiungere le VM alla stessa rete virtuale di Azure (<https://azure.microsoft.com/documentation/services/virtual-network/>)
* Le macchine virtuali della configurazione a disponibilità elevata devono trovarsi anch'esse nella stessa subnet. La risoluzione dei nomi tra le varie subnet non è possibile nelle distribuzioni solo cloud. Funziona solo la risoluzione dell'IP. Se si usa la connettività da sito a sito o ExpressRoute per le distribuzioni cross-premise, è già presente una rete con almeno una subnet. La risoluzione dei nomi viene eseguita in base all'infrastruttura di rete e ai criteri di Active Directory locali. 
[comment]: <> (MSSedusch TODO Test if still true in ARM)

#### Indirizzi IP
È consigliabile impostare le macchine virtuali per le configurazioni a disponibilità elevata in modo resiliente. In Azure non è possibile fare affidamento sugli indirizzi IP per indirizzare i partner a disponibilità elevata all'interno della configurazione a disponibilità elevata, a meno che non vengano usati indirizzi IP statici. Azure prevede due tipi di "arresto":

* Arresto tramite il portale di Azure o il cmdlet Stop-AzureRmVM di Azure PowerShell: la macchina virtuale viene arrestata e deallocata. La macchina virtuale non viene più addebitata all'account Azure e gli unici costi saranno quelli relativi alle risorse di archiviazione usate. Tuttavia, se l'indirizzo IP privato dell'interfaccia di rete non è statico viene rilasciato e non è detto che, dopo il riavvio della macchina virtuale, all'interfaccia di rete venga assegnato nuovamente l'indirizzo IP precedente. L'arresto tramite il portale di Azure o la chiamata a Stop-AzureRmVM provoca automaticamente la deallocazione. Per evitare la deallocazione della macchina virtuale, usare Stop-AzureRmVM -StayProvisioned.
* Arresto a livello di sistema operativo: la macchina virtuale viene arrestata ma NON viene deallocata. In tal caso la macchina virtuale viene comunque addebitata all'account Azure, anche se è stata arrestata. L'assegnazione dell'indirizzo IP a una macchina virtuale arrestata in questo caso rimane invariata. L'arresto della macchina virtuale dall'interno non forza automaticamente la deallocazione.

Anche per gli scenari cross-premise, per impostazione predefinita l'arresto e la deallocazione annullano l'assegnazione degli indirizzi IP alla macchina virtuale, anche se i criteri locali nelle impostazioni DHCP non lo prevedono.

* Fa eccezione l'assegnazione di un indirizzo IP statico a un'interfaccia di rete, descritta [qui][virtual-networks-reserved-private-ip].
* In questo caso l'indirizzo IP rimane fisso, purché l'interfaccia di rete non venga eliminata.

> [AZURE.IMPORTANT] Per garantire la semplicità e la gestibilità dell'intera distribuzione, è consigliabile configurare in Azure le macchine virtuali che partecipano a una configurazione DBMS a disponibilità elevata o di ripristino di emergenza, facendo in modo che la risoluzione dei nomi tra le diverse macchine virtuali coinvolte funzioni.
 
## Distribuzione del monitoraggio host
Per un uso produttivo delle applicazioni SAP in macchine virtuali di Azure, SAP deve poter recuperare i dati relativi al monitoraggio host dagli host fisici che eseguono le macchine virtuali di Azure. È necessario un livello di patch SAP Host Agent specifico che abilita questa funzionalità in SAPOSCOL e SAP Host Agent. L'esatto livello di patch è documentato nella nota SAP [1409604].

Per informazioni dettagliate sulla distribuzione di componenti che forniscono dati host a SAPOSCOL e a SAP Host Agent e sulla gestione del ciclo di vita di tali componenti, vedere la [Guida alla distribuzione][deployment-guide]

## <a name="3264829e-075e-4d25-966e-a49dad878737"></a>Specifiche di Microsoft SQL Server

### SQL Server in IaaS
A partire da Microsoft Azure, è possibile eseguire facilmente la migrazione in macchine virtuali di Azure delle applicazioni SQL Server esistenti basate sulla piattaforma Windows Server. SQL Server in una macchina virtuale consente di ridurre il costo totale di proprietà dovuto a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni in Microsoft Azure. Con SQL Server in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

> [AZURE.IMPORTANT] Si noti che questo documento non illustra il database SQL di Microsoft Azure, una piattaforma distribuita come servizio offerta nell'ambito della piattaforma Microsoft Azure. Il documento illustra l'esecuzione del prodotto SQL Server, noto per le distribuzioni locali in macchine virtuali di Azure, tramite la funzionalità di infrastruttura distribuita come servizio di Azure. Le funzionalità di database di queste due offerte sono diverse e non devono essere confuse tra loro. Vedere anche: <https://azure.microsoft.com/services/sql-database/>
 
È consigliabile esaminare [questa][virtual-machines-sql-server-infrastructure-services] documentazione prima di proseguire.

Nelle sezioni seguenti vengono aggregate e citate parti della documentazione disponibile seguendo il collegamento indicato. Vengono anche riportate le specifiche relative a SAP e alcuni concetti vengono descritti in modo più dettagliato. Tuttavia, è consigliabile esaminare la documentazione indicata prima di leggere la documentazione specifica di SQL Server.

Di seguito sono indicate alcune informazioni specifiche su SQL Server in IaaS che è necessario conoscere per poter continuare:

* **Contratto di servizio per macchine virtuali**: il contratto di servizio per le macchine virtuali in esecuzione in Azure è disponibile qui: <https://azure.microsoft.com/support/legal/sla/>
* **Supporto della versione SQL**: per i clienti SAP, le macchine virtuali di Microsoft Azure supportano SQL Server 2008 R2 e versioni successive. Le versioni precedenti non sono supportate. Per altre informazioni, vedere questa [informativa sul supporto](https://support.microsoft.com/kb/956893) di carattere generale. Si noti che in genere SQL Server 2008 è supportato anche da Microsoft. Tuttavia, a causa di importanti funzionalità per SAP introdotte con SQL Server 2008 R2, quest'ultima è la versione minima richiesta per SAP. Tenere presente che SQL Server 2012 e SQL Server 2014 sono stati estesi con una maggiore integrazione nello scenario IaaS, ad esempio con il backup diretto in Archiviazione di Azure. Di conseguenza, questo documento è limitato a SQL Server 2012 e SQL Server 2014 con il relativo livello di patch più recente per Azure.
* **Supporto di funzionalità SQL**: le macchine virtuali di Microsoft Azure supportano la maggior parte delle funzionalità di SQL Server, con alcune eccezioni. **Il clustering di failover di SQL Server tramite dischi condivisi non è supportato**. Le tecnologie distribuite quali il mirroring del database, i gruppi di disponibilità AlwaysOn, la replica, il log shipping e Service Broker sono supportate in un'unica area di Azure. SQL Server AlwaysOn è supportato anche tra diverse aree di Azure, come illustrato qui: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>. Per altre informazioni, vedere l'[informativa sul supporto](https://support.microsoft.com/kb/956893). Per un esempio relativo alla distribuzione di una configurazione AlwaysOn, vedere [questo][virtual-machines-workload-template-sql-alwayson] articolo. Vedere anche le procedure consigliate illustrate [qui][virtual-machines-sql-server-infrastructure-services]
* **Prestazioni di SQL**: anche se con ogni probabilità le prestazioni delle macchine virtuali ospitate in Microsoft Azure saranno ottimali rispetto ad altre offerte per la virtualizzazione del cloud pubblico, i risultati ottenuti potrebbero essere diversi. Per informazioni, consultare [questo articolo][virtual-machines-sql-server-performance-best-practices].
* **Uso di immagini disponibili in Azure Marketplace**: il modo più rapido per distribuire una nuova VM di Microsoft Azure consiste nell'usare un'immagine disponibile in Azure Marketplace. In Azure Marketplace sono presenti immagini che contengono SQL Server. Non è però possibile usare subito per le applicazioni SAP NetWeaver le immagini in cui SQL Server è già installato. In tali immagini sono infatti installate le regole di confronto predefinite di SQL Server e non quelle richieste dai sistemi SAP NetWeaver. Per usare queste immagini, vedere la procedura documentata nel capitolo [Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace][dbms-guide-5.6].
* Per altre informazioni, consultare i [dettagli sui prezzi](https://azure.microsoft.com/pricing/). Un'altra risorsa importante è rappresentata dai documenti [Guida alle licenze di SQL Server 2012](https://download.microsoft.com/download/7/3/C/73CAD4E0-D0B5-4BE5-AB49-D5B886A5AE00/SQL_Server_2012_Licensing_Reference_Guide.pdf) e [Guida alle licenze di SQL Server 2014](https://download.microsoft.com/download/B/4/E/B4E604D9-9D38-4BBA-A927-56E4C872E41C/SQL_Server_2014_Licensing_Guide.pdf).
 
### Linee guida per la configurazione di SQL Server per le installazioni di SQL Server correlate a SAP nelle VM di Azure

#### Consigli sulla struttura di VM/dischi rigidi virtuali per distribuzioni di SQL Server correlate a SAP
In base alla descrizione generale, gli eseguibili di SQL Server devono essere salvati o installati nell'unità di sistema del disco rigido virtuale di base della VM (unità C:). In genere, la maggior parte dei database di sistema di SQL Server non viene pienamente sfruttata dal carico di lavoro di SAP NetWeaver. Di conseguenza è possibile lasciare sull'unità C:\\ anche i database di sistema di SQL Server (master, msdb e model). Può fare eccezione il database tempdb, che nel caso di alcuni carichi di lavoro SAP ERP e di tutti i carichi di lavoro BW può richiedere un volume dati o un volume di operazioni di I/O maggiore, le cui dimensioni sono troppo grandi per la VM originale. Per tali sistemi, è consigliabile eseguire la procedura seguente:

* Spostare i file di dati primari di tempdb nella stessa unità logica dei file di dati primari del database SAP.
* Aggiungere eventuali altri file di dati di tempdb a una delle altre unità logiche contenenti un file di dati del database utente SAP.
* Aggiungere il file di log di tempdb all'unità logica che contiene il file di log del database utente.
* **Esclusivamente per i tipi di VM che usano unità SSD locali** nel nodo di calcolo, i file di log e di dati di tempdb potrebbero essere disponibili nell'unità D:\\. Può tuttavia essere consigliabile usare più file di dati di tempdb. Tenere presente che i volumi delle unità D:\\ sono diversi in base al tipo di VM.
 
Con queste configurazioni il database tempdb può consumare più spazio di quello che l'unità di sistema è in grado di offrire. Per determinare le dimensioni corrette del database tempdb, è possibile controllare le dimensioni di tempdb in sistemi esistenti eseguiti in locale. Con una tale configurazione, inoltre, sarà possibile eseguire su tempdb un numero di operazioni di I/O al secondo che l'unità di sistema non è in grado di garantire. I sistemi in esecuzione in locale possono essere usati anche per il monitoraggio del carico di lavoro di I/O su tempdb, in modo da derivare il numero di operazioni di I/O al secondo previsto per il database tempdb.

Una configurazione di VM che esegue SQL Server con un database SAP e in cui i dati e il file di log di tempdb sono disponibili nell'unità D:\\ è simile a quella illustrata nella figura seguente:
 
![Configurazione di riferimento di una VM IaaS di Azure per SAP][dbms-guide-figure-300]

Tenere presente che le dimensioni dell'unità D:\\ variano in base al tipo di VM. A seconda del requisito previsto per le dimensioni del database tempdb, potrebbe essere necessario associare i file di log e di dati di tempdb con i file di log e di dati del database SAP nei casi in cui le dimensioni dell'unità D:\\ non siano sufficienti.

#### Formattazione dei dischi rigidi virtuali
Per SQL Server per i dischi rigidi virtuali che contengono file di dati e file di log di SQL Server è necessario un blocco NTFS di dimensioni pari a 64 KB. Non è necessario formattare l'unità D:\\ perché viene fornita preformattata.

Per evitare che in seguito al ripristino o la creazione di database i file di dati non vengano inizializzati e il loro contenuto venga azzerato, è necessario assicurarsi che per il contesto utente in cui viene eseguito il servizio SQL Server siano disponibili determinate autorizzazioni. Queste autorizzazioni sono in genere disponibili per gli utenti del gruppo Administrators di Windows. Se il servizio SQL Server viene eseguito nel contesto utente di un utente non appartenente al gruppo Administrators di Windows, è necessario assegnare a tale utente il diritto 'Esecuzione attività di manutenzione volume'. Per informazioni dettagliate, vedere l'articolo <https://support.microsoft.com/kb/2574695> della Microsoft Knowledge Base.
 
#### Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS, ad esempio Azure. Se quindi non è stato ancora fatto, sia SAP che Microsoft consigliano di applicare la compressione di pagina di SQL Server prima di caricare database SAP esistenti in Azure.
 
Sono tre i motivi per cui è consigliabile eseguire la compressione del database prima del caricamento in Azure:

* La quantità di dati da caricare è minore.
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale.
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione del database nelle macchine virtuali di Azure è analogo a quello in locale. Per maggiori dettagli su come comprimere un database esistente di SQL Server per SAP, vedere <https://blogs.msdn.com/b/saponsqlserver/archive/2010/10/08/compressing-an-sap-database-using-report-msscompress.aspx>
  
### SQL Server 2014: archiviazione dei file di database direttamente nell'archivio BLOB di Azure
A partire da SQL Server 2014 è possibile archiviare file di database direttamente nell'archivio BLOB di Azure senza salvarli in un disco rigido virtuale. Quando, in particolare, si usa l'archiviazione Standard di Azure o tipi di VM di dimensioni ridotte, questa funzionalità può essere applicata in determinati scenari per ovviare al numero limitato di operazioni di I/O al secondo supportate dai dischi rigidi virtuali che è possibile montare in alcuni tipi di VM di dimensioni ridotte. Si tratta però di una funzionalità valida per i database utente e non per i database di sistema di SQL Server, che funziona anche per i file di log e di dati di SQL Server. Se si preferisce distribuire un database di SQL Server per SAP in questo modo invece di salvarlo in dischi rigidi virtuali, tenere presente quanto segue:

* L'account di archiviazione usato deve trovarsi nella stessa area di Azure di quello usato per distribuire la VM in cui è in esecuzione SQL Server.
* Le considerazioni elencate in precedenza in merito alla distribuzione di dischi rigidi virtuali in diversi account di archiviazione di Azure sono valide anche per questo metodo di distribuzione. Questo significa che il numero di operazioni di I/O viene tenuto in considerazione ai fini dei limiti dell'account di archiviazione di Azure. 
[comment]: <> (MSSedusch TODO But this will use network bandwith and not storage bandwith, doesn't it?)

Per informazioni dettagliate su questo tipo di distribuzione, vedere <https://msdn.microsoft.com/library/dn385720.aspx>
 
Per archiviare i file di dati di SQL Server direttamente nell'archiviazione Premium di Azure, è necessario aver installato la versione patch minima di SQL Server 2014 documentata qui: <https://support.microsoft.com/kb/3063054>. Non è invece possibile archiviare file di dati di SQL Server nell'archiviazione Standard di Azure con la versione rilasciata di SQL Server 2014. Le stesse patch contengono tuttavia un'altra serie di correzioni per garantire una maggiore affidabilità quando si usa direttamente l'archivio BLOB di Azure per file di dati e backup di SQL Server. È quindi consigliabile usare queste patch in qualsiasi caso.

### Estensione del pool di buffer di SQL Server 2014
In SQL Server 2014 è stata introdotta una nuova funzionalità denominata estensione del pool di buffer. Questa funzionalità consente di estendere il pool di buffer di SQL Server mantenuto in memoria con una cache di secondo livello supportata da unità SSD di un server o da una VM, permettendo in tal modo di mantenere in memoria un working set di dati di dimensioni maggiori. Rispetto all'accesso all'archiviazione Standard di Azure, l'accesso nell'estensione del pool di buffer archiviato nelle unità SSD locali di una VM di Azure è di gran lunga più veloce. Se quindi si usa l'unità D:\\ locale dei tipi di VM che supportano un numero elevato di operazioni di I/O al secondo e una velocità effettiva elevata, è possibile implementare una soluzione molto conveniente per ridurre il carico delle operazioni di I/O al secondo in Archiviazione di Azure e migliorare notevolmente i tempi di risposta delle query. Questo vale soprattutto quando non si usa Archiviazione Premium. Se invece si usa Archiviazione Premium e la cache di lettura di Archiviazione Premium sul nodo di calcolo, come consigliato per i file di dati, non si riscontrano grandi differenze. Il motivo è che entrambe le cache (estensione del pool di buffer di SQL Server e la cache di lettura di Archiviazione Premium) usano i dischi locali dei nodi di calcolo. Per altri dettagli su questa funzionalità, vedere <https://msdn.microsoft.com/library/dn133176.aspx>

### Considerazioni sul backup/ripristino per SQL Server
Quando si distribuisce SQL Server in Azure, è necessario rivedere la metodologia di backup. Anche se il sistema non è usato in produzione, è necessario eseguire periodicamente il backup del database SAP ospitato da SQL Server. Dal momento che in Archiviazione di Azure vengono mantenute tre immagini, ora un backup è meno importante rispetto alla compensazione di un arresto anomalo della risorsa di archiviazione. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. È possibile, ad esempio, passare da una configurazione SAP di livello 2 a una configurazione di sistema di livello 3 dello stesso sistema ripristinando un backup.

È possibile eseguire il backup di SQL Server in Archiviazione di Azure in tre modi diversi:
 
1. SQL Server 2012 CU4 e versioni successive supportano il backup in modalità nativa di database a un URL, come spiegato in dettaglio nel post di blog [Nuova funzionalità di SQL Server 2014 - Parte 5 - Miglioramenti apportati a backup/ripristino](https://blogs.msdn.com/b/saponsqlserver/archive/2014/02/15/new-functionality-in-sql-server-2014-part-5-backup-restore-enhancements.aspx). Vedere il capitolo [SQL Server 2012 SP1 CU4 e versioni successive][dbms-guide-5.5.1].
1. Nelle versioni di SQL Server precedenti a SQL 2012 CU4 è possibile usare una funzionalità di reindirizzamento per eseguire il backup in un disco rigido virtuale e di fatto spostare il flusso di scrittura verso una posizione di archiviazione di Azure configurata. Vedere il capitolo [SQL Server 2012 SP1 CU3 e versioni precedenti][dbms-guide-5.5.2].
1. L'ultimo metodo prevede l'esecuzione di un comando convenzionale di backup su disco di SQL Server su un dispositivo disco VHD. Si tratta di una procedura identica al modello di distribuzione locale che non viene illustrata in dettaglio in questo documento.

#### <a name="0fef0e79-d3fe-4ae2-85af-73666a6f7268"></a>SQL Server 2012 SP1 CU4 e versioni successive
Questa funzionalità consente di eseguire il backup direttamente nell'archivio BLOB di Azure. Senza questo metodo è necessario eseguire il backup su altri dischi rigidi virtuali di Azure consumando quindi la capacità disponibile in termini di dischi rigidi virtuali e numero di operazioni di I/O al secondo. Il principio è illustrato nella figura seguente:
 
 ![Uso del backup di SQL Server 2012 nel BLOB di archiviazione di Microsoft Azure][dbms-guide-figure-400]

Il vantaggio in questo caso è che non è necessario consumare dischi rigidi virtuali per archiviarvi i backup di SQL Server. Il numero di dischi rigidi virtuali allocati è inferiore ed è possibile usare l'intera larghezza di banda delle operazioni di I/O al secondo dei dischi rigidi virtuali per i file di log e di dati. Notare che le dimensioni massime di un backup sono limitate a 1 TB, come documentato nella sezione 'Limitazioni' dell'articolo <https://msdn.microsoft.com/library/dn435916.aspx#limitations>. Se, nonostante l'uso della compressione del backup di SQL Server, le dimensioni del backup superano 1 TB, è necessario usare la funzionalità descritta nel capitolo [SQL Server 2012 SP1 CU3 e versioni precedenti][dbms-guide-5.5.2] di questo documento.

Nella [documentazione correlata](https://msdn.microsoft.com/library/dn449492.aspx) che descrive il ripristino di database da backup sull'archiviazione BLOB di Azure si consiglia di non eseguire il ripristino direttamente dall'archiviazione se le dimensioni dei backup superano i 25 GB. L'indicazione riportata in questo articolo si basa semplicemente su considerazioni relative alle prestazioni e non su limitazioni funzionali. Le condizioni applicabili possono quindi variare caso per caso.

La documentazione relativa alla configurazione e all'uso di questo tipo di backup è disponibile in [questa](https://msdn.microsoft.com/library/dn466438.aspx) esercitazione
 
Per un esempio della sequenza di passaggi, vedere [qui](https://msdn.microsoft.com/library/dn435916.aspx).

Quando si automatizzano i backup, è di fondamentale importanza garantire che ai BLOB di ogni backup venga assegnato un nome diverso. In caso contrario, questi vengono sovrascritti interrompendo la catena di ripristino.
 
Per evitare confusione tra i tre diversi tipi di backup, è consigliabile creare contenitori diversi nell'account di archiviazione usato per i backup. I contenitori possono essere organizzati solo in base alla VM oppure alla VM e al tipo di backup. Lo schema è simile al seguente:
 
 ![Uso del backup di SQL Server 2012 nel BLOB di archiviazione di Microsoft Azure: contenitori diversi in un account di archiviazione separato][dbms-guide-figure-500]

Nell'esempio precedente i backup non vengono eseguiti nello stesso account di archiviazione in cui sono distribuite le VM, ma in un nuovo account di archiviazione specifico per i backup. Negli account di archiviazione sono presenti contenitori diversi creati con una matrice del tipo di backup e del nome della VM. Tale segmentazione faciliterà l'amministrazione dei backup delle diverse VM.

I BLOB usati per scrivere direttamente i backup non vengono aggiunti al conteggio dei dischi rigidi virtuali di una VM. In questo modo è possibile sfruttare il numero massimo di dischi rigidi virtuali montati della SKU specifica della VM per i file di dati e di log delle transazioni ed eseguire comunque il backup in un contenitore di archiviazione.

#### <a name="f9071eff-9d72-4f47-9da4-1852d782087b"></a>SQL Server 2012 SP1 CU3 e versioni precedenti
Per eseguire un backup direttamente in Archiviazione di Azure, è innanzitutto necessario scaricare il file MSI collegato a [questo](https://www.microsoft.com/download/details.aspx?id=40740) articolo della Knowledge Base.
 
Scaricare il file di installazione per x64 e la documentazione. Il file installerà un programma denominato 'Strumento di backup di Microsoft SQL Server in Microsoft Azure'. Leggere attentamente la documentazione del prodotto. Lo strumento funziona nel modo seguente:

* Sul lato SQL Server viene definito un percorso sul disco per il backup di SQL Server (in questo caso non usare l'unità D:\\).
* Lo strumento consentirà di definire le regole da usare per indirizzare i diversi tipi di backup a contenitori di archiviazione di Azure diversi.
* Dopo aver definito le regole, lo strumento reindirizzerà il flusso di scrittura del backup destinato a uno dei dischi rigidi virtuali/dischi alla posizione di archiviazione di Azure definita in precedenza.
* Lo strumento lascerà quindi un piccolo file stub di pochi KB sul disco rigido virtuale/disco definito per il backup di SQL Server. **Tale file deve essere lasciato nella posizione di archiviazione perché è necessario per eseguire il ripristino da Archiviazione di Azure.**
	* Se il file stub è andato perso, ad esempio perché si è smarrito il supporto di archiviazione che lo conteneva, e si è scelto di eseguire il backup su un account di archiviazione di Microsoft Azure, è possibile ripristinare il file stub tramite Archiviazione di Microsoft Azure scaricandolo dal contenitore di archiviazione in cui è stato salvato. È quindi necessario inserire il file stub in una cartella del computer locale in cui è configurato lo strumento per rilevare e caricare nello stesso contenitore con la stessa password di crittografia se con la regola originale è stata usata la crittografia.

Questo significa che lo schema descritto in precedenza per le versioni più recenti di SQL Server può essere applicato anche a versioni di SQL Server che non consentono l'indirizzamento diretto a una posizione di archiviazione di Azure.
 
È consigliabile non usare questo metodo con versioni più recenti di SQL Server che supportano il backup in modalità nativa in Archiviazione di Azure. Fanno eccezione i casi in cui le limitazioni del backup nativo in Azure bloccano l'esecuzione di tale tipo di backup.

#### Altri modi per eseguire il backup di database di SQL Server
Per eseguire il backup dei database, è anche possibile collegare dischi rigidi virtuali aggiuntivi a una VM usata per l'archiviazione dei backup. In tal caso è necessario assicurarsi che i dischi rigidi virtuali usati non siano pieni. In caso contrario, sarà necessario smontare il disco rigido virtuale e sostituirlo con uno nuovo vuoto. Se si sceglie questo metodo, è opportuno mantenere questi dischi rigidi virtuali in account di archiviazione di Azure distinti rispetto a quelli che contengono i file di database.

È anche possibile usare una VM di grandi dimensioni a cui collegare più dischi rigidi virtuali, ad esempio D14 con 32 dischi rigidi virtuali. Usare Spazi di archiviazione per creare un ambiente flessibile in cui creare condivisioni utilizzabili come destinazioni di backup per i diversi server DBMS.
 
Per la documentazione su alcune procedure consigliate, vedere anche [qui](https://blogs.msdn.com/b/sqlcat/archive/2015/02/26/large-sql-server-database-backup-on-an-azure-vm-and-archiving.aspx).

#### Considerazioni sulle prestazioni per backup/ripristini
Come nelle distribuzioni bare metal, le prestazioni dei backup/ripristini dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. La quota di utilizzo della CPU usata dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di soli 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare i file di dati, più bassa è la velocità effettiva generale nella lettura.
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup.
* Minore è il numero di destinazioni (BLOB o dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva.
* Minori sono le dimensioni della VM, minore è la quota di velocità effettiva di archiviazione in lettura e scrittura da Archiviazione di Azure Storage. Questo avviene indipendentemente dal fatto che i backup vengano archiviati direttamente in BLOB di Azure o vengano archiviati in dischi rigidi virtuali a loro volta archiviati in BLOB di Azure.

Quando si usa un BLOB di archiviazione di Microsoft Azure come destinazione di backup nelle versioni più recenti, è consentito designare una sola destinazione di tipo URL per ogni backup specifico.
 
Quando invece si usa 'Strumento di backup di Microsoft SQL Server in Microsoft Azure' in versioni precedenti, è possibile definire più di una destinazione di tipo file. Con più di una destinazione è possibile ridimensionare il backup e aumentarne la velocità effettiva. In questo modo vengono anche creati più file nell'account di archiviazione di Azure. Nel corso dei test, usando più destinazioni di tipo file è possibile ottenere la velocità effettiva ottenibile con le estensioni di backup implementate a partire da SQL Server 2012 SP1 CU4. È anche possibile ovviare al limite di 1 TB previsto dal backup nativo in Azure.

Tenere comunque presente che la velocità effettiva dipende anche dalla località dell'account di archiviazione di Azure usato per il backup. Un'idea potrebbe essere quella di impostare per l'account di archiviazione una regione diversa da quella in cui vengono eseguite le VM e, ad esempio eseguire la configurazione delle VM in Europa occidentale ma impostare l'account di archiviazione usato per il backup in Europa settentrionale. Tale impostazione influirà certamente sulla velocità effettiva del backup, che potrebbe non raggiungere i 150 MB/sec, raggiunti invece nei casi in cui l'archivio di destinazione e le VM vengono eseguiti in un data center nella stessa area.

#### Gestione dei BLOB di backup
I backup devono essere gestiti direttamente dall'utente. Dal momento che si prevede che verranno creati numerosi BLOB in seguito all'esecuzione di backup frequenti dei log delle transazioni, l'amministrazione di tali BLOB può facilmente sovraccaricare il portale di Azure. È quindi consigliabile usare Azure Storage Explorer. Sono disponibili diversi ottimi strumenti per facilitare la gestione di un account di archiviazione di Azure:

* Microsoft Visual Studio con Azure SDK installato (<https://azure.microsoft.com/downloads/>)
* Microsoft Azure Storage Explorer (<https://azure.microsoft.com/downloads/>)
* Strumenti di terze parti

[comment]: <> (Not yet supported on ARM) 
[comment]: <> (#### Azure VM backup) 
[comment]: <> (VMs within the SAP system can be backed up using Azure Virtual Machine Backup functionality. Azure Virtual Machine Backup got introduced early in the year 2015 and meanwhile is a standard method to backup a complete VM in Azure. Azure Backup stores the backups in Azure and allows a restore of a VM again.) 
[comment]: <> (VMs that run databases can be backed up in a consistent manner as well if the DBMS systems supports the Windows VSS (Volume Shadow Copy Service - <https://msdn.microsoft.com/library/windows/desktop/bb968832.aspx>) as e.g. SQL Server does. So using Azure VM backup could be a way to get to a restorable backup of a SAP database. However, be aware that based on Azure VM backups point-in-time restores of databases is not possible. Therefore, the recommendation is to perform backups of databases with DBMS functionality instead of relying on Azure VM Backup.) 
[comment]: <> (To get familiar with Azure Virtual Machine Backup please start here <https://azure.microsoft.com/documentation/services/backup/>)

### <a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace
In Azure Marketplace sono disponibili VM che contengono già versioni di SQL Server. I clienti SAP che necessitano delle licenze per SQL Server e Windows possono approfittare di questa opportunità per ottenere le licenze necessarie scegliendo le VM con SQL Server già installato. Per usare tali immagini per SAP, è necessario considerare quanto segue:

* I costi delle versioni di SQL Server non di valutazione sono decisamente più elevati rispetto a una semplice VM solo Windows distribuita da Azure Marketplace. Per confrontare i prezzi, vedere gli articoli seguenti: <https://azure.microsoft.com/pricing/details/virtual-machines/> e <https://azure.microsoft.com/pricing/details/virtual-machines/#Sql>.
* È possibile usare solo versioni di SQL Server supportate da SAP, come SQL Server 2012.
* Le regole di confronto dell'istanza di SQL Server installata nelle VM offerte in Azure Marketplace non sono quelle che devono essere eseguite nell'istanza di SQL Server per SAP NetWeaver. È comunque possibile modificare le regole di confronto attenendosi alle istruzioni della sezione seguente.

#### Modifica delle regole di confronto SQL Server di una VM Microsoft Windows/SQL Server
Dal momento che le immagini di SQL Server in Azure Marketplace non sono configurate per l'uso delle regole di confronto richieste dalle applicazioni SAP NetWeaver, è necessario modificarle subito dopo la distribuzione. In SQL Server 2012 è possibile eseguire la procedura seguente non appena la VM è stata distribuita e un amministratore è in grado di accedervi:

* Aprire una finestra di comando di Windows come amministratore.
* Passare alla directory C:\\Programmi\\SQL Server\\110\\Setup Bootstrap\\SQLServer2012.
* Eseguire il comando: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL\_Latin1\_General\_Cp850\_BIN2
	* `<local_admin_account_name`> è l'account definito come account dell'amministratore quando si distribuisce la VM per la prima volta tramite la raccolta.

Il processo dovrebbe richiedere solo alcuni minuti. Per verificare la correttezza del risultato finale del passaggio, seguire questa procedura:

* Aprire SQL Server Management Studio.
* Aprire una finestra di query.
* Eseguire il comando sp\_helpsort nel database master di SQL Server.

Il risultato dovrebbe essere simile a quello della figura seguente:

	Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data

Se il risultato non corrisponde a quello nella figura, ARRESTARE la distribuzione di SAP e scoprire perché il comando di configurazione non ha funzionato nel modo previsto. La distribuzione di applicazioni SAP NetWeaver in un'istanza di SQL Server con tabelle codici di SQL Server diverse da quella indicata in precedenza **NON** è supportata.

### Disponibilità elevata di SQL Server per SAP in Azure
Come accennato in precedenza in questo articolo, non è in alcun modo possibile creare l'archivio condiviso necessario per l'utilizzo della funzionalità di disponibilità elevata offerta in versioni meno recenti di SQL Server. Questa funzionalità prevede l'installazione di due o più istanze di SQL Server in un cluster di failover di Windows Server (WSFC) usando un disco condiviso per i database utente e alla fine tempdb. Questo è il metodo di disponibilità elevata standard usato da più tempo, nonché supportato da SAP. Azure però non supporta l'archiviazione condivisa, di conseguenza non è possibile realizzare configurazioni a disponibilità elevata di SQL Server con cluster di tipo disco condiviso. Esistono tuttavia molti altri metodi di disponibilità elevata, descritti nelle sezioni seguenti.

[comment]: <> (Article is still refering to ASM) 
[comment]: <> (Before reading the different specific high availability technologies usable for SQL Server in Azure, there is a very good document which gives more details and pointers [here][virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions])

#### Log shipping SQL Server
Uno dei metodi di disponibilità elevata utilizzabili è il log shipping SQL Server. Se le VM incluse nella configurazione a disponibilità elevata hanno una risoluzione dei nomi funzionante, non ci sono problemi e la configurazione in Azure non sarà diversa da quelle eseguite in locale. Non è consigliabile affidarsi solo alla risoluzione IP. Per informazioni sulla configurazione del log shipping e sui principi su cui si basa, vedere la documentazione seguente:

<https://technet.microsoft.com/library/ms187103.aspx>
 
Per ottenere effettivamente una disponibilità elevata, è necessario distribuire le VM incluse in una configurazione di log shipping in modo che appartengano allo stesso set di disponibilità di Azure.

#### Mirroring del database
Il mirroring del database supportato da SAP (vedere la nota SAP [965908]) si basa sulla definizione di un partner di failover nella stringa di connessione SAP. Per i casi di cross-premise si presuppone che le due VM appartengano allo stesso dominio e che il contesto utente in cui vengono eseguite le due istanze di SQL Server sia anch'esso costituito da utenti di dominio e che questi dispongano di privilegi sufficienti nelle due istanze interessate. La configurazione del mirroring del database in Azure non è quindi diversa da una configurazione tipica locale.

A partire dalle distribuzioni solo cloud, il metodo più semplice prevede la configurazione di un altro dominio in Azure allo scopo di avere in un unico dominio le VM DBMS e idealmente le VM SAP dedicate.

Se non è possibile predisporre un unico dominio, è anche possibile usare i certificati per gli endpoint di mirroring del database, come descritto qui: <https://technet.microsoft.com/library/ms191477.aspx>

Per un'esercitazione relativa alla configurazione del mirroring del database in Azure, vedere <https://technet.microsoft.com/library/ms189852.aspx>

#### AlwaysOn
Dal momento che AlwaysOn è supportato per SAP in locale (vedere la nota SAP [1772688]), è possibile usarlo anche in combinazione con SAP in Azure. Il fatto che non sia possibile creare dischi condivisi in Azure non implica che non sia possibile creare una configurazione di cluster di failover Windows (WSFC) AlwaysOn tra VM diverse. In realtà indica che non è possibile usare un disco condiviso come quorum nella configurazione cluster. Di conseguenza, è possibile creare una configurazione WSFC AlwaysOn in Azure evitando semplicemente di selezionare il tipo di quorum che utilizza il disco condiviso. L'ambiente Azure in cui vengono distribuite le VM deve risolvere queste ultime in base al nome e le VM devono appartenere allo stesso dominio. Queste indicazioni sono valide solo per distribuzioni solo Azure e cross-premise. La distribuzione del listener del gruppo di disponibilità di SQL Server, da non confondere con il set di disponibilità di Azure presenta alcune particolarità da considerare, dal momento che al momento Azure non consente di creare semplicemente un oggetto AD/DNS, mentre questa operazione è consentita in locale. È quindi necessario eseguire alcuni passaggi di installazione diversi per ovviare al comportamento specifico di Azure.

Di seguito sono elencate alcune considerazioni relative all'uso di un listener del gruppo di disponibilità.

* L'uso di un listener del gruppo di disponibilità è possibile solo se il sistema operativo guest della VM è Windows Server 2012 o Windows Server 2012 R2. Per Windows Server 2012 è necessario assicurarsi che sia applicata la patch seguente: <https://support.microsoft.com/kb/2854082>
* Per Windows Server 2008 R2 questa patch non esiste ed è necessario usare AlwaysOn in modo analogo al mirroring del database, specificando un partner di failover nella stringa di connessione tramite il parametro dbs/mss/server del file SAP default.pfl. Vedere la nota SAP [965908].
* Quando si usa un listener del gruppo di disponibilità, le VM di database devono essere connesse a un Load Balancer dedicato. Per la risoluzione dei nomi in distribuzioni solo cloud è necessario che tutte le VM di un sistema SAP (server applicazioni, server DBMS e server (A)SCS) si trovino nella stessa rete virtuale oppure che la manutenzione del file etc\\host venga effettuata da un livello dell'applicazione SAP per ottenere i nomi risolti delle VM di SQL Server. Per evitare che Azure assegni nuovi indirizzi IP nei casi in cui entrambe le VM vengono arrestate incidentalmente, è opportuno assegnare indirizzi IP statici alle interfacce di rete di tali VM nella configurazione AlwaysOn. Per informazioni sulla definizione di un indirizzo IP statico, vedere [questo][virtual-networks-reserved-private-ip] articolo.
[comment]: <> (Old blogs)
[comment]: <> (<https://blogs.msdn.com/b/alwaysonpro/archive/2014/08/29/recommendations-and-best-practices-when-deploying-sql-server-alwayson-availability-groups-in-windows-azure-iaas.aspx>, <https://blogs.technet.com/b/rmilne/archive/2015/07/27/how-to-set-static-ip-on-azure-vm.aspx>) 
* La procedura di creazione della configurazione cluster WSFC prevede speciali passaggi quando è necessario assegnare al cluster un indirizzo IP particolare, dal momento che Azure con la funzionalità corrente assegna al nome del cluster lo stesso indirizzo IP del nodo in cui viene creato il cluster. È quindi necessario eseguire un passaggio manuale per assegnare al cluster un indirizzo IP diverso.
* Il listener del gruppo di disponibilità verrà creato in Azure con gli endpoint TCP/IP assegnati alle VM che eseguono le repliche primaria e secondaria del gruppo di disponibilità.
* Potrebbe essere necessario proteggere questi endpoint con ACL.

[comment]: <> (TODO old blog)
[comment]: <> (The detailed steps and necessities of installing an AlwaysOn configuration on Azure are best experienced when walking through the tutorial available [here][virtual-machines-windows-classic-ps-sql-alwayson-availability-groups])
[comment]: <> (Preconfigured AlwaysOn setup via the Azure gallery <https://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx>)
[comment]: <> (Creating an Availability Group Listener is best described in [this][virtual-machines-windows-classic-ps-sql-int-listener] tutorial)
[comment]: <> (Securing network endpoints with ACLs are explained best here:)
[comment]: <> (* <https://michaelwasham.com/windows-azure-powershell-reference-guide/network-access-control-list-capability-in-windows-azure-powershell/>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/08/31/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-1-of-2.aspx> )
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/01/weekend-scripter-creating-acls-for-windows-azure-endpoints-part-2-of-2.aspx>)
[comment]: <> (* <https://blogs.technet.com/b/heyscriptingguy/archive/2013/09/18/creating-acls-for-windows-azure-endpoints.aspx>)

È possibile distribuire un gruppo di disponibilità AlwaysOn di SQL Server anche in diverse aree di Azure. Questa funzionalità sfrutta la connettività da rete virtuale a rete virtuale di Azure ([maggiori dettagli][virtual-networks-configure-vnet-to-vnet-connection]).
[comment]: <> (TODO old blog)
[comment]: <> (The setup of SQL Server AlwaysOn Availability Groups in such a scenario is described here: <https://blogs.technet.com/b/dataplatforminsider/archive/2014/06/19/sql-server-alwayson-availability-groups-supported-between-microsoft-azure-regions.aspx>.)

#### Riepilogo sulla disponibilità elevata di SQL Server in Azure
In considerazione del fatto che Archiviazione di Azure garantisce la protezione del contenuto, diventa meno importante creare un'immagine hot standby. Lo scenario di disponibilità elevata deve quindi prevede la protezione nei casi seguenti:

* Indisponibilità completa della VM per interventi di manutenzione sul cluster di server in Azure o per altri motivi
* Problemi software nell'istanza di SQL Server
* Protezione da errori manuali nel caso in cui i dati vengono eliminati ed è necessario il ripristino temporizzato

Se si esaminano le tecnologie corrispondenti a questi casi, si può affermare che i primi due casi sono coperti dal mirroring del database o da AlwaysOn, mentre il terzo solo dal log shipping.

È quindi necessario bilanciare i vantaggi di AlwaysOn con la procedura di configurazione di questa tecnologia, che risulta più complessa se confrontata con quella del mirroring del database. I vantaggi includono:

*	Repliche secondarie leggibili.
*	Backup da repliche secondarie.
*	Migliore scalabilità.
*	Possibilità di creare più repliche secondarie.

### <a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>Riepilogo generale su SQL Server per SAP in Azure
Questa guida contiene numerosi consigli che è preferibile leggere più volte prima di pianificare la distribuzione di Azure. In linea generale, assicurarsi di seguire i dieci consigli generali specifici per la distribuzione di DBMS in Azure:

[comment]: <> (2.3 higher throughput than what? Than one VHD?)
1. Usare la versione più recente di DBMS, come SQL Server 2014, che offre maggiori vantaggi in Azure. Per SQL Server si tratta di SQL Server 2012 SP1 CU4 che include la funzionalità di backup in Archiviazione di Azure. Con SAP, tuttavia, è consigliabile almeno SQL Server 2014 SP1 CU1 o SQL Server 2012 SP2 con l'aggiornamento cumulativo più recente.
1. Pianificare attentamente l'infrastruttura di sistema SAP in Azure per bilanciare il layout dei file di dati e le restrizioni di Azure:
	* Usare un numero di dischi rigidi virtuali sufficiente a garantire il numero richiesto di operazioni di I/O al secondo.
	* Tenere presente che il numero di operazioni di I/O al secondo varia a seconda dell'account di archiviazione di Azure e che tali account di archiviazione sono limitati nelle singole sottoscrizioni di Azure ([maggiori dettagli][azure-subscription-service-limits]).
	* Se è necessaria una velocità effettiva maggiore, eseguire lo striping solo tra dischi rigidi virtuali.
1. Non installare mai o spostare file che richiedono la persistenza nell'unità D:\\ perché non è permanente e qualsiasi elemento in tale unità andrà perso al riavvio di Windows.
1. Non usare il caching dei dischi rigidi virtuali di Azure per l'archiviazione Standard di Azure.
1. Non usare account di archiviazione con replica geografica di Azure. Usare l'opzione Con ridondanza locale per i carichi di lavoro DBMS.
1. Usare la soluzione di disponibilità elevata/ripristino di emergenza del fornitore del sistema DBMS per replicare i dati del database.
1. Usare sempre la risoluzione dei nomi e non fare affidamento sugli indirizzi IP.
1. Usare la massima compressione possibile del database. Per SQL Server si tratta della compressione di pagina.
1. Prestare attenzione quando si usano immagini di SQL da Azure Marketplace. Se si usa quella di SQL Server, è necessario modificare le regole di confronto dell'istanza prima di installarvi un qualsiasi sistema SAP NetWeaver.
1. Installare e configurare la funzionalità di monitoraggio host SAP per Azure come descritto nella [Guida alla distribuzione][deployment-guide].

## Specifiche per SAP ASE in Windows
A partire da Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in macchine virtuali di Azure. SAP ASE in una macchina virtuale consente di ridurre il costo totale di proprietà dovuto a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni in Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

Per le macchine virtuali di Azure esiste un contratto di servizio disponibile qui: <https://azure.microsoft.com/support/legal/sla>

Anche se siamo fiduciosi che le prestazioni delle macchine virtuali ospitate in Microsoft Azure saranno ottimali rispetto ad altre soluzioni per la virtualizzazione del cloud pubblico, i risultati ottenuti potrebbero essere diversi. I numeri SAPS relativi al ridimensionamento SAP delle diverse SKU di VM con certificazione SAP verranno forniti nella nota SAP [1928533].

Le istruzioni e i suggerimenti relativi all'utilizzo di Archiviazione di Azure, alla distribuzione di VM SAP o al monitoraggio di SAP si applicano alle distribuzioni di SAP ASE insieme alle applicazioni SAP, come indicato nei primi quattro capitoli di questo documento.

### Supporto della versione di SAP ASE 
SAP supporta attualmente SAP ASE versione 16.0 per l'uso con i prodotti SAP Business Suite. Tutti gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC da usare con i prodotti SAP Business Suite vengono forniti esclusivamente in SAP Service Marketplace su: <https://support.sap.com/swdc>.

Come per le installazioni locali, non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente dai siti Web Sybase. Per informazioni dettagliate sulle patch supportate per l'uso con i prodotti SAP Business Suite in locale e nelle macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]
 
Le informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE sono disponibili in [SCN](https://scn.sap.com/community/ase)

### Linee guida per la configurazione di SAP ASE per le installazioni di SAP ASE correlate a SAP nelle VM di Azure

#### Struttura della distribuzione SAP ASE
In base alla descrizione generale, gli eseguibili di SAP ASE devono essere salvati o installati nell'unità di sistema del disco rigido virtuale di base della VM (unità C:). In genere, la maggior parte dei database di sistema e di strumenti di SAP ASE non viene pienamente sfruttata dal carico di lavoro di SAP NetWeaver. I database di sistema e di strumenti (master, model, saptools, sybmgmtdb, sybsystemdb) possono quindi rimanere anche nell'unità C:\\.

Può fare eccezione il database temporaneo contenente tutte le tabelle di lavoro e le tabelle temporanee create da SAP ASE, che nel caso di alcuni carichi di lavoro SAP ERP e di tutti i carichi di lavoro BW può richiedere un volume dati o un volume di operazioni di I/O maggiore, le cui dimensioni sono eccessive per il disco rigido virtuale di base (unità C:) della VM originale.
 
A seconda della versione di SAPInst/SWPM usata per installare il sistema, il database può contenere:

* Un solo tempdb SAP ASE creato durante l'installazione di SAP ASE
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un saptempdb aggiuntivo creato dalla routine di installazione SAP
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un tempdb aggiuntivo creato manualmente (ad esempio, seguendo la nota SAP [1752266]) per soddisfare i requisiti specifici di ERP/BW per tempdb

Nel caso di carichi di lavoro ERP specifici o di tutti carichi di lavoro BW è consigliabile, dal punto di vista delle prestazioni, tenere i dispositivi tempdb del tempdb aggiuntivo creato (con SWPM o manualmente) in un'unità diversa da C:. Se non esistono tempdb aggiuntivi, è consigliabile crearne uno (nota SAP [1752266]).

Per tali sistemi, eseguire questa procedura per il tempdb aggiuntivo creato:

* Spostare il primo dispositivo tempdb nel primo dispositivo del database SAP
* Aggiungere i dispositivi tempdb a ogni disco rigido virtuale contenente un dispositivo del database SAP

Questa configurazione consente a tempdb di utilizzare più spazio di quello fornito dall'unità di sistema. Come riferimento è possibile controllare le dimensioni del dispositivo tempdb nei sistemi esistenti eseguiti in locale. In caso contrario, con una tale configurazione sarà possibile eseguire su tempdb un numero di operazioni di I/O al secondo che l'unità di sistema non è in grado di garantire. I sistemi in esecuzione in locale possono anche essere usati per monitorare il carico di lavoro di I/O in tempdb.

Non spostare mai dispositivi SAP ASE nell'unità D:\\ della VM. Questo vale anche per il tempdb, anche se gli oggetti in esso presenti sono solo temporanei.

#### Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS, ad esempio Azure. È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

Sono diversi i motivi per cui è consigliabile eseguire la compressione del database prima del caricamento in Azure se non è già stata implementata:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per altri dettagli su come controllare se la compressione è già in uso in un database SAP ASE esistente, vedere la nota SAP [1750510].

#### Uso di DBACockpit per monitorare le istanze di database
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. La funzionalità completa per monitorare e gestire il database è tuttavia disponibile solo nell'implementazione Webdynpro di DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Per abilitare l'uso di webdynpro e generare quelli necessari, vedere la nota SAP [1245200]. Quando si seguono le istruzioni nelle note precedenti, si configurerà anche Internet Communication Manager (ICM) oltre alle porte da usare per le connessioni HTTP e HTTPS. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

I collegamenti generati nella transazione DBACockpit saranno simili ai seguenti:

> https://`<nomehostcompleto`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<nomehostcompleto`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

A seconda del fatto che la macchina virtuale di Azure che ospita il sistema SAP sia connessa o meno tramite una connessione da sito a sito, multisito o ExpressRoute (distribuzione cross-premise), è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta cercando di aprire DBACockpit. Vedere la nota SAP [773830] per comprendere come ICM determina il nome host completo in base ai parametri del profilo e impostare il parametro icm/host\_name\_full in modo esplicito, se necessario.

Se la VM è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra l'ambiente locale e Azure, è necessario definire un indirizzo IP pubblico e un'etichetta di dominio. Il formato del nome DNS pubblico della VM sarà quindi simile al seguente:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

Altri dettagli relativi al nome DNS sono disponibili [qui][virtual-machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host\_name\_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit

> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

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

#### Considerazioni sul backup/ripristino per SAP ASE
Quando si distribuisce SAP ASE in Azure, è necessario rivedere la metodologia di backup. Anche se il sistema non è usato in produzione, è necessario eseguire periodicamente il backup del database SAP ospitato da SAP ASE. Dal momento che in Archiviazione di Azure vengono mantenute tre immagini, ora un backup è meno importante rispetto alla compensazione di un arresto anomalo della risorsa di archiviazione. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. È possibile, ad esempio, passare da una configurazione SAP di livello 2 a una configurazione di sistema di livello 3 dello stesso sistema ripristinando un backup.

Il backup e il ripristino di un database in Azure funzionano esattamente come in locale. Vedere le note SAP:

* [1588316]
* [1585981]

per i dettagli sulla creazione di configurazioni di dump e sulla pianificazione dei backup. A seconda della strategia e delle esigenze, è possibile configurare i dump di database e di log su disco in uno dei dischi rigidi virtuali esistenti o aggiungere un altro disco rigido virtuale per il backup. Per limitare il rischio di perdita dei dati in caso di errore, è consigliabile usare un disco rigido virtuale senza spazio allocato per il database.

Oltre alla compressione dati e LOB, SAP ASE offre anche la compressione del backup. Per occupare meno spazio con i dump di database e di log, è consigliabile usare la compressione del backup. Per altre informazioni, vedere la nota SAP [1588316]. La compressione del backup è fondamentale anche per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenenti dump di backup dalla macchina virtuale di Azure in locale.

Non usare l'unità D:\\ come destinazione dei dump di database o di log.

#### Considerazioni sulle prestazioni per backup/ripristini
Come nelle distribuzioni bare metal, le prestazioni dei backup/ripristini dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. La quota di utilizzo della CPU usata dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di soli 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare gli spazi allocati per il database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Meno sono le destinazioni (directory Stripe, dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, sono disponibili due opzioni che possono essere usate/combinate a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi rigidi virtuali montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Creazione di una configurazione dump a livello di SAP ASE, che usa più di una directory di destinazione in cui scrivere il dump

Lo striping di un volume su più dischi rigidi virtuali montati è stato illustrato prima in questa guida. Per altre informazioni sull'uso di più directory nella configurazione dump SAP ASE, vedere la documentazione sulla stored procedure sp\_config\_dump che viene usata per creare la configurazione dump in [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### Ripristino di emergenza con VM di Azure

#### Replica dei dati con SAP Sybase Replication Server
Con SAP Sybase Replication Server (SRS), SAP ASE offre una nuova soluzione di standby per trasferire in modo asincrono le transazioni di database in una posizione distante.

L'installazione e il funzionamento di SRS sono altrettanto efficaci sia in una VM ospitata nei servizi Macchine virtuali di Azure che in locale.

ASE HADR tramite SAP Replication Server è previsto per una versione futura. Verrà testato e rilasciato per le piattaforme Microsoft Azure non appena sarà disponibile.

## Specifiche per SAP ASE in Linux

A partire da Microsoft Azure, è possibile eseguire facilmente la migrazione delle applicazioni SAP ASE in Macchine virtuali di Azure. SAP ASE in una macchina virtuale consente di ridurre il costo totale di proprietà dovuto a distribuzione, gestione e manutenzione delle applicazioni aziendali eseguendo facilmente la migrazione di queste applicazioni in Microsoft Azure. Con SAP ASE in una macchina virtuale di Azure, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale.

Per distribuire le VM di Azure, è importante conoscere i contratti di servizio ufficiali disponibili qui: <https://azure.microsoft.com/support/legal/sla>

Le informazioni sul ridimensionamento di SAP e un elenco di SKU di VM con certificazione SAP saranno disponibili nella nota SAP [1928533]. Altri documenti sul ridimensionamento di SAP per le macchine virtuali di Azure sono disponibili qui: <http://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> e <http://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

Le istruzioni e i suggerimenti relativi all'utilizzo di Archiviazione di Azure, alla distribuzione di VM SAP o al monitoraggio di SAP si applicano alle distribuzioni di SAP ASE insieme alle applicazioni SAP, come indicato nei primi quattro capitoli di questo documento.

Le due note SAP seguenti includono informazioni generali su ASE in Linux e su ASE nel cloud:

* [2134316]
* [1941500]

### Supporto della versione di SAP ASE 
SAP supporta attualmente SAP ASE versione 16.0 per l'uso con i prodotti SAP Business Suite. Tutti gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC da usare con i prodotti SAP Business Suite vengono forniti esclusivamente in SAP Service Marketplace su: <https://support.sap.com/swdc>.

Come per le installazioni locali, non scaricare gli aggiornamenti per il server SAP ASE o per i driver ODBC e JDBC direttamente dai siti Web Sybase. Per informazioni dettagliate sulle patch supportate per l'uso con i prodotti SAP Business Suite in locale e nelle macchine virtuali di Azure, vedere le note SAP seguenti:

* [1590719]
* [1973241]
 
Le informazioni generali sull'esecuzione di SAP Business Suite in SAP ASE sono disponibili in [SCN](https://scn.sap.com/community/ase)

### Linee guida per la configurazione di SAP ASE per le installazioni di SAP ASE correlate a SAP nelle VM di Azure

#### Struttura della distribuzione SAP ASE
In base alla descrizione generale, gli eseguibili di SAP ASE devono essere salvati o installati nel file system radice della VM (/sybase ). In genere, la maggior parte dei database di sistema e di strumenti di SAP ASE non viene pienamente sfruttata dal carico di lavoro di SAP NetWeaver. I database di sistema e di strumenti (master, modello, saptools, sybmgmtdb, sybsystemdb) possono quindi rimanere anche nel file system radice.

Può fare eccezione il database temporaneo contenente tutte le tabelle di lavoro e le tabelle temporanee create da SAP ASE, che nel caso di alcuni carichi di lavoro SAP ERP e di tutti i carichi di lavoro BW può richiedere un volume dati o un volume di operazioni di I/O maggiore, le cui dimensioni sono troppo grandi per il disco del sistema operativo della VM originale.
 
A seconda della versione di SAPInst/SWPM usata per installare il sistema, il database può contenere:

* Un solo tempdb SAP ASE creato durante l'installazione di SAP ASE
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un saptempdb aggiuntivo creato dalla routine di installazione SAP
* Un tempdb SAP ASE creato in seguito all'installazione di SAP ASE e un tempdb aggiuntivo creato manualmente (ad esempio, seguendo la nota SAP [1752266]) per soddisfare i requisiti specifici di ERP/BW per tempdb

Nel caso di carichi di lavoro ERP specifici o di tutti carichi di lavoro BW è consigliabile, dal punto di vista delle prestazioni, tenere i dispositivi tempdb della directory tempdb aggiuntiva creata (con SWPM o manualmente) in un file system separato che può essere un unico disco dati di Azure o un disco RAID Linux che si estende su più dischi dati di Azure. Se non esistono tempdb aggiuntivi, è consigliabile crearne uno (nota SAP [1752266]).

Per tali sistemi, eseguire questa procedura per il tempdb aggiuntivo creato:

* Spostare la prima directory tempdb nel primo file system del database SAP
* Aggiungere le directory tempdb a ogni disco rigido virtuale contenente un file system del database SAP

Questa configurazione consente a tempdb di utilizzare più spazio di quello fornito dall'unità di sistema. Come riferimento è possibile controllare le dimensioni della directory tempdb nei sistemi esistenti eseguiti in locale. In caso contrario, con una tale configurazione sarà possibile eseguire su tempdb un numero di operazioni di I/O al secondo che l'unità di sistema non è in grado di garantire. I sistemi in esecuzione in locale possono anche essere usati per monitorare il carico di lavoro di I/O in tempdb.

Non inserire mai le directory SAP ASE in /mnt o /mnt/resource nella VM. Questo si applica anche a tempdb, anche se gli oggetti conservati in tempdb sono solo temporanei perché /mnt o /mnt/resource è uno spazio temporaneo della VM di Azure VM predefinito non persistente. Per altri dettagli sullo spazio temporaneo della VM di Azure, vedere [questo articolo][virtual-machines-linux-how-to-attach-disk]

#### Impatto della compressione del database
Nelle configurazioni in cui la larghezza di banda di I/O può diventare un fattore limitante, ogni misura che riduce le operazioni di I/O al secondo consente di estendere il carico di lavoro eseguibile in uno scenario IaaS, ad esempio Azure. È quindi consigliabile verificare che venga usata la compressione SAP ASE prima di caricare un database SAP esistente in Azure.

Sono diversi i motivi per cui è consigliabile eseguire la compressione del database prima del caricamento in Azure se non è già stata implementata:

* La quantità di dati da caricare in Azure è minore
* La durata dell'esecuzione della compressione è inferiore, presupponendo che si possa usare hardware più avanzato con più CPU o una maggiore larghezza di banda di I/O oppure meno latenza di I/O in locale
* L'uso di database di dimensioni inferiori può contribuire a ridurre i costi per l'allocazione dei dischi

Il funzionamento della compressione dati e LOB in una VM ospitata in macchine virtuali di Azure è uguale a quello in locale. Per altri dettagli su come controllare se la compressione è già in uso in un database SAP ASE esistente, vedere la nota SAP [1750510]. Per altre informazioni sulla compressione del database, vedere anche la nota SAP [2121797].

#### Uso di DBACockpit per monitorare le istanze di database
Per i sistemi SAP che usano SAP ASE come piattaforma di database, DBACockpit è accessibile come finestra del browser incorporata nella transazione DBACockpit o come Webdynpro. La funzionalità completa per monitorare e gestire il database è tuttavia disponibile solo nell'implementazione Webdynpro di DBACockpit.

Come per i sistemi locali, sono necessari diversi passaggi per abilitare tutte le funzionalità di SAP NetWeaver usate dall'implementazione Webdynpro di DBACockpit. Per abilitare l'uso di webdynpro e generare quelli necessari, vedere la nota SAP [1245200]. Quando si seguono le istruzioni nelle note precedenti, si configurerà anche Internet Communication Manager (ICM) oltre alle porte da usare per le connessioni HTTP e HTTPS. L'impostazione predefinita per HTTP è simile alla seguente:

> icm/server\_port\_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
>
> icm/server\_port\_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600

I collegamenti generati nella transazione DBACockpit saranno simili ai seguenti:

> https://`<nomehostcompleto`>:44300/sap/bc/webdynpro/sap/dba\_cockpit
> 
> http://`<nomehostcompleto`>:8000/sap/bc/webdynpro/sap/dba\_cockpit

A seconda del fatto che la macchina virtuale di Azure che ospita il sistema SAP sia connessa o meno tramite una connessione da sito a sito, multisito o ExpressRoute (distribuzione cross-premise), è necessario verificare che ICM usi un nome host completo che può essere risolto nel computer da cui si sta cercando di aprire DBACockpit. Vedere la nota SAP [773830] per comprendere come ICM determina il nome host completo in base ai parametri del profilo e impostare il parametro icm/host\_name\_full in modo esplicito, se necessario.

Se la VM è stata distribuita in uno scenario solo cloud senza connettività cross-premise tra l'ambiente locale e Azure, è necessario definire un indirizzo IP pubblico e un'etichetta di dominio. Il formato del nome DNS pubblico della VM sarà quindi simile al seguente:

> `<custom domainlabel`>.`<azure region`>.cloudapp.azure.com

Altri dettagli relativi al nome DNS sono disponibili [qui][virtual-machines-azurerm-versus-azuresm].

Impostando il parametro del profilo SAP icm/host\_name\_full sul nome DNS della VM di Azure, il collegamento sarà simile a:

> https://mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http://mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

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

#### Considerazioni sul backup/ripristino per SAP ASE
Quando si distribuisce SAP ASE in Azure, è necessario rivedere la metodologia di backup. Anche se il sistema non è usato in produzione, è necessario eseguire periodicamente il backup del database SAP ospitato da SAP ASE. Dal momento che in Archiviazione di Azure vengono mantenute tre immagini, ora un backup è meno importante rispetto alla compensazione di un arresto anomalo della risorsa di archiviazione. È opportuno predisporre un piano di backup e ripristino appropriato principalmente perché consente di risolvere gli errori logici/manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. È possibile, ad esempio, passare da una configurazione SAP di livello 2 a una configurazione di sistema di livello 3 dello stesso sistema ripristinando un backup.

Il backup e il ripristino di un database in Azure funzionano esattamente come in locale. Vedere le note SAP:

* [1588316]
* [1585981]

per i dettagli sulla creazione di configurazioni di dump e sulla pianificazione dei backup. A seconda della strategia e delle esigenze, è possibile configurare i dump di database e di log su disco in uno dei dischi rigidi virtuali esistenti o aggiungere un altro disco rigido virtuale per il backup. Per limitare il rischio di perdita dei dati in caso di errore, è consigliabile usare un disco rigido virtuale senza directory/file per il database.

Oltre alla compressione dati e LOB, SAP ASE offre anche la compressione del backup. Per occupare meno spazio con i dump di database e di log, è consigliabile usare la compressione del backup. Per altre informazioni, vedere la nota SAP [1588316]. La compressione del backup è fondamentale anche per ridurre la quantità di dati da trasferire se si prevede di scaricare backup o dischi rigidi virtuali contenenti dump di backup dalla macchina virtuale di Azure in locale.

Non usare lo spazio temporaneo della VM di Azure /mnt o /mnt/resource come destinazione dei dump di database o di log.

#### Considerazioni sulle prestazioni per backup/ripristini
Come nelle distribuzioni bare metal, le prestazioni dei backup/ripristini dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. La quota di utilizzo della CPU usata dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di soli 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare gli spazi allocati per il database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Meno sono le destinazioni (RAID software Linux, dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, sono disponibili due opzioni che possono essere usate/combinate a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi rigidi virtuali montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Creazione di una configurazione dump a livello di SAP ASE, che usa più di una directory di destinazione in cui scrivere il dump

Lo striping di un volume su più dischi rigidi virtuali montati è stato illustrato prima in questa guida. Per altre informazioni sull'uso di più directory nella configurazione dump SAP ASE, vedere la documentazione sulla stored procedure sp\_config\_dump che viene usata per creare la configurazione dump in [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp).

### Ripristino di emergenza con VM di Azure

#### Replica dei dati con SAP Sybase Replication Server
Con SAP Sybase Replication Server (SRS), SAP ASE offre una nuova soluzione di standby per trasferire in modo asincrono le transazioni di database in una posizione distante.

L'installazione e il funzionamento di SRS sono altrettanto efficaci sia in una VM ospitata nei servizi Macchine virtuali di Azure che in locale.

ASE HADR tramite SAP Replication Server NON è attualmente supportato. È possibile che venga testato e rilasciato per le piattaforme Microsoft Azure in futuro.

## Specifiche per Oracle Database in Windows
Dalla metà del 2013, Oracle supporta l'esecuzione del software Oracle in Microsoft Windows Hyper-V e Azure. Per altri dettagli sul supporto generale fornito da Oracle per Windows Hyper-V e Azure, vedere questo articolo: <https://blogs.oracle.com/cloud/entry/oracle_and_microsoft_join_forces>

In base al supporto generale, è supportato anche lo scenario specifico delle applicazioni SAP che sfruttano i database Oracle. I dettagli sono disponibili in questa parte del documento.

### Supporto della versione di Oracle
Tutti i dettagli sulle versioni di Oracle e sulle corrispondenti versioni del sistema operativo supportate per l'esecuzione di SAP in Oracle nelle macchine virtuali di Azure sono disponibili nella nota SAP [2039619]

Informazioni generali sull'esecuzione di SAP Business Suite in Oracle sono disponibili in SCN: <https://scn.sap.com/community/oracle>

### Linee guida per la configurazione di Oracle per le installazioni di SAP nelle VM di Azure

#### Configurazione dell'archiviazione
È supportata una sola istanza di Oracle che usa dischi formattati NTFS. Tutti i file di database devono essere archiviati nel file system NTFS basato su dischi rigidi virtuali. Questi dischi rigidi virtuali vengono montati nella VM di Azure e si basano sull'archiviazione BLOB di pagine di Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Le unità di rete o le condivisioni remote, ad esempio i servizi file di Azure:
 
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
**NON** sono supportate per i file di database Oracle.

Usando i dischi rigidi virtuali di Azure basati sull'archiviazione BLOB di pagine di Azure, le istruzioni illustrate in questo documento nel capitolo [Caching per VM e dischi rigidi virtuali][dbms-guide-2.1] e [Archiviazione di Microsoft Azure][dbms-guide-2.3] si applicano anche alle distribuzioni con Oracle Database.

Come spiegato prima nella parte generale del documento, esistono quote relative alla velocità effettiva delle operazioni di I/O al secondo per i dischi rigidi virtuali di Azure. Le quote esatte dipendono dal tipo di VM usato. Un elenco di tipi di VM con le rispettive quote è disponibile [qui][virtual-machines-sizes]

Per identificare i tipi di VM di Azure supportati, vedere la nota SAP [1928533]

Purché la quota corrente di operazioni di I/O al secondo per ogni disco soddisfi i requisiti, è possibile archiviare tutti i file di DB in un solo disco rigido virtuale di Azure montato.

Se sono necessarie più operazioni di I/O al secondo, è consigliabile usare i pool di archiviazione di Windows (disponibili solo in Windows Server 2012 e versioni successive) o lo striping di Windows per Windows 2008 R2 per creare un solo dispositivo logico di grandi dimensioni su più dischi rigidi virtuali montati. Vedere anche il capitolo [RAID software][dbms-guide-2.2] di questo documento. Questo approccio riduce il sovraccarico di amministrazione per la gestione dello spazio su disco ed evita la fatica di distribuire manualmente i file in più dischi rigidi virtuali montati.

#### Backup/Ripristino
Per la funzionalità di backup/ripristino, SAP BR*Tools per Oracle è supportato esattamente come nei sistemi operativi Windows Server standard e in Hyper-V. Per i backup su disco e per il ripristino da disco, è supportato anche Oracle Recovery Manager (RMAN).

#### Disponibilità elevata
[comment]: <> (link refers to ASM) 
Oracle Data Guard è supportato per motivi di disponibilità elevata e ripristino di emergenza. I dettagli sono disponibili in [questa][virtual-machines-windows-classic-configure-oracle-data-guard] documentazione.

#### Altri
Tutti gli altri argomenti generali, ad esempio i set di disponibilità di Azure o il monitoraggio SAP, si applicano come descritto nei primi tre capitoli di questo documento anche per le distribuzioni di VM con Oracle Database.

## Specifiche per il database SAP MaxDB in Windows

### Supporto della versione di SAP MaxDB
SAP supporta attualmente SAP MaxDB versione 7.9 per l'uso con prodotti basati su SAP NetWeaver in Azure. Tutti gli aggiornamenti per il server SAP MaxDB o per i driver ODBC e JDBC da usare con i prodotti basati su SAP NetWeaver vengono forniti esclusivamente in SAP Service Marketplace su <https://support.sap.com/swdc>. Le informazioni generali sull'esecuzione di SAP NetWeaver in SAP MaxDB sono disponibili su <https://scn.sap.com/community/maxdb>.

### Versioni di Microsoft Windows e tipi di VM di Azure supportati per SAP MaxDB DBMS
Per trovare la versione supportata di Microsoft Windows per SAP MaxDB DBMS in Azure, vedere:

* [Product Availability Matrix (PAM) SAP][sap-pam]
* Nota SAP [1928533]

È consigliabile usare le versione più recente del sistema operativo Microsoft Windows, ovvero Microsoft Windows 2012 R2.

### Documentazione disponibile su SAP MaxDB
È possibile trovare l'elenco aggiornato della documentazione su SAP MaxDB nella nota SAP [767598]
	
### Linee guida per la configurazione di SAP MaxDB per le installazioni di SAP nelle VM di Azure

#### <a name="b48cfe3b-48e9-4f5b-a783-1d29155bd573"></a>Configurazione dell'archiviazione
Le procedure consigliate di Archiviazione di Azure per SAP MaxDB seguono i suggerimenti generali indicati nel capitolo [Struttura di una distribuzione RDBMS][dbms-guide-2].

> [AZURE.IMPORTANT] Come altri database, anche SAP MaxDB include file di dati e di log. Nella terminologia di SAP MaxDB, tuttavia, il termine corretto è "volume" (non "file"). Esistono, ad esempio, volumi di dati e volumi di log di SAP MaxDB. Non confonderli con i volumi dei dischi del sistema operativo.

In breve, è necessario:

* Impostare l'account di archiviazione di Azure che contiene i volumi (ovvero i file) di dati e di log di SAP MaxDB su **Archiviazione con ridondanza locale**, come specificato nel capitolo [Archiviazione di Microsoft Azure][dbms-guide-2.3].
* Separare il percorso di I/O per i volumi (ovvero i file) di dati di SAP MaxDB dal percorso di I/O per i volumi (ovvero i file) di log. I volumi (ovvero i file) di dati di SAP MaxDB dovranno quindi essere installati in un'unità logica e i volumi (ovvero i file) di log di SAP MaxDB dovranno essere installati in un'altra unità logica.
* Impostare il caching dei file appropriato per ogni BLOB di Azure, a seconda che lo si usi per i volumi (ovvero i file) di dati o di log di SAP MaxDB e che si usi l'archiviazione Standard di Azure o l'archiviazione Premium di Azure, come descritto nel capitolo [Caching per VM][dbms-guide-2.1].
* Purché la quota corrente di operazioni di I/O al secondo per ogni disco soddisfi i requisiti, è possibile archiviare tutti i volumi di dati in un solo disco rigido virtuale di Azure montato e anche archiviare tutti i volumi di log di database in un altro disco rigido virtuale di Azure montato singolo.
* Se sono necessarie più operazioni di I/O al secondo e/o più spazio, è consigliabile usare i pool di archiviazione di Microsoft Windows (disponibili solo in Microsoft Windows Server 2012 e versioni successive) o lo striping di Microsoft Windows per Microsoft Windows 2008 R2 per creare un solo dispositivo logico di grandi dimensioni su più dischi rigidi virtuali montati. Vedere anche il capitolo [RAID software][dbms-guide-2.2] di questo documento. Questo approccio riduce il sovraccarico di amministrazione per la gestione dello spazio su disco ed evita la fatica di distribuire manualmente i file in più dischi rigidi virtuali montati.
* Per requisiti di operazioni di I/O al secondo più elevati, è possibile usare l'Archiviazione Premium di Azure, disponibile nelle VM serie DS e serie GS.

![Configurazione di riferimento di una VM IaaS di Azure per SAP MaxDB DBMS][dbms-guide-figure-600]

#### <a name="23c78d3b-ca5a-4e72-8a24-645d141a3f5d"></a>Backup e ripristino
Quando si distribuisce SAP MaxDB in Azure, è necessario rivedere la metodologia di backup. Anche se il sistema non è produttivo, è necessario eseguire periodicamente il backup del database SAP ospitato da SAP MaxDB. Poiché Archiviazione di Azure conserva tre immagini, il backup è ora meno importante dal punto di vista della protezione del sistema da errori di archiviazione e da errori operativi o amministrativi più gravi. Il motivo principale per avere un piano di backup e ripristino appropriato è quindi la possibilità di risolvere gli errori logici o manuali fornendo funzionalità di ripristino temporizzato. L'obiettivo è quindi quello di usare i backup per ripristinare un determinato stato precedente del database o di usare i backup in Azure per effettuare il seeding di un altro sistema copiando il database esistente. È possibile, ad esempio, passare da una configurazione SAP di livello 2 a una configurazione di sistema di livello 3 dello stesso sistema ripristinando un backup.

Il backup e il ripristino di un database in Azure funzionano esattamente come nei sistemi locali, quindi è possibile usare gli strumenti di backup/ripristino di SAP MaxDB standard, descritti in uno dei documenti su SAP MaxDB elencati nella nota SAP [767598].

#### <a name="77cd2fbb-307e-4cbf-a65f-745553f72d2c"></a>Considerazioni sulle prestazioni per il backup e il ripristino
Come nelle distribuzioni bare metal, le prestazioni di backup e ripristino dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. L'utilizzo della CPU usato dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare gli spazi allocati per il database, più bassa è la velocità effettiva di lettura generale
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Meno sono le destinazioni (directory Stripe, dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, sono disponibili due opzioni che è possibile usare, anche in combinazione, a seconda delle proprie esigenze:

* Volumi separati dedicati al backup
* Striping del volume di destinazione di backup su più dischi rigidi virtuali montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume dei dischi con striping
* Dispositivi dischi logici separati dedicati per:
    * Volumi (ovvero file) di backup di SAP MaxDB
    * Volumi (ovvero file) di dati di SAP MaxDB
    * Volumi (ovvero file) di log di SAP MaxDB

Lo striping di un volume su più dischi rigidi virtuali montati è stato illustrato prima nel capitolo [RAID software][dbms-guide-2.2] di questo documento.

#### <a name="f77c1436-9ad8-44fb-a331-8671342de818"></a>Altro
Tutti gli altri argomenti generali, ad esempio i set di disponibilità di Azure o il monitoraggio SAP, si applicano come descritto nei primi tre capitoli di questo documento anche per le distribuzioni di VM con il database SAP MaxDB. Altre impostazioni specifiche di SAP MaxDB sono trasparenti per le VM di Azure e vengono descritte in documenti diversi elencati nella nota SAP [767598] e in queste note SAP:

* [826037]
* [1139904]
* [1173395]

## Specifiche per SAP liveCache in Windows

### Supporto della versione di SAP liveCache
La versione minima di SAP liveCache supportata nelle macchine virtuali di Azure è **SAP LC/LCAPPS 10.0 SP 25**, incluse **liveCache 7.9.08.31** e **LCA-Build 25**, rilasciate per **EhP 2 for SAP SCM 7.0** e versioni successive.

### Versioni di Microsoft Windows e tipi di VM di Azure supportati per SAP liveCache DBMS
Per trovare la versione supportata di Microsoft Windows per SAP liveCache in Azure, vedere:

* [Product Availability Matrix (PAM) SAP][sap-pam]
* Nota SAP [1928533]

È consigliabile usare le versione più recente del sistema operativo Microsoft Windows, ovvero Microsoft Windows 2012 R2.

### Linee guida per la configurazione di SAP liveCache per le installazioni di SAP nelle VM di Azure

#### Tipi di VM di Azure consigliati
Poiché SAP liveCache è un'applicazione che esegue calcoli molto complessi, la quantità e la velocità di RAM e CPU influiscono considerevolmente sulle prestazioni di SAP liveCache.

Per i tipi di VM di Azure supportati da SAP (nota SAP [1928533]), tutte le risorse della CPU virtuale allocate alla VM sono supportate da risorse della CPU fisica dedicate dell'hypervisor. Non si verifica nessun provisioning eccessivo (e quindi nessuna competizione per le risorse della CPU).

Analogamente, per tutti i tipi di istanza di VM di Azure supportati da SAP, viene eseguito al 100% il mapping della memoria della VM alla memoria fisica. Il provisioning eccessivo (memoria impegnata eccessiva), ad esempio, non viene usato.

Da questo punto di vista, è consigliabile usare il nuovo tipo di VM serie D o serie DS (in combinazione con l'Archiviazione Premium di Azure), perché i processori sono del 60% più veloci di quelli della serie A. Per carichi di RAM e CPU più elevati, è possibile usare VM serie G e serie GS (in combinazione con l'Archiviazione Premium di Azure) con la famiglia di processori E5 v3 di Intel® Xeon® più recente, con il doppio della memoria e archiviazione con unità SSD quattro volte superiore rispetto alla serie D/DS.

#### Configurazione dell'archiviazione
Poiché SAP liveCache si basa sulla tecnologia SAP MaxDB, tutti i suggerimenti sulle procedure consigliate per l'archiviazione di Azure indicati per SAP MaxDB nel capitolo [Configurazione dell'archiviazione][dbms-guide-8.4.1] sono validi anche per SAP liveCache.

#### VM di Azure dedicata per liveCache
Poiché SAP liveCache usa in modo intensivo la potenza di elaborazione, per l'utilizzo produttivo è consigliabile la distribuzione in una macchina virtuale di Azure dedicata.
 
![VM di Azure dedicata per liveCache per un caso d'uso produttivo][dbms-guide-figure-700]

#### Backup e ripristino
Il backup e il ripristino, incluse le considerazioni sulle prestazioni, sono già stati descritti nei capitoli pertinenti su SAP MaxDB, [Backup e ripristino][dbms-guide-8.4.2] e [Considerazioni sulle prestazioni per il backup e il ripristino][dbms-guide-8.4.3].

#### Altri
Tutti gli altri argomenti generali sono già stati descritti in [questo][dbms-guide-8.4.4] capitolo su SAP MaxDB pertinente.

## Specifiche per SAP Content Server in Windows
SAP Content Server è un componente separato basato su server per archiviare contenuto, ad esempio documenti elettronici in formati diversi. SAP Content Server è supportato dallo sviluppo della tecnologia e deve essere usato tra applicazioni per qualsiasi applicazione SAP. Viene installato in un sistema distinto. Il contenuto in genere è costituito da documentazione e materiale di formazione di Knowledge Warehouse o da disegni tecnici provenienti da mySAP PLM Document Management System.

### Supporto della versione di SAP Content Server
SAP attualmente supporta:

* **SAP Content Server** con la versione **6.50 (e successive)**
* **SAP MaxDB versione 7.9**
* **Microsoft IIS (Internet Information Server) versione 8.0 (e successive)**

È consigliabile usare la versione più recente di SAP Content Server, che al momento della stesura di questo documento è la **6.50 SP4**, e la versione più recente di **Microsoft IIS 8.5**.

Controllare le versioni supportate più recenti di SAP Content Server e Microsoft IIS in [Product Availability Matrix (PAM) SAP][sap-pam].

### Tipi di VM di Azure e di Microsoft Windows supportati per SAP Content Server
Per trovare le versioni di Windows supportate per SAP Content Server in Azure, vedere:

* [Product Availability Matrix (PAM) SAP][sap-pam]
* Nota SAP [1928533]

È consigliabile usare la versione più recente di Microsoft Windows, che al momento della stesura di questo documento è **Windows Server 2012 R2**.

### Linee guida per la configurazione di SAP Content Server per le installazioni di SAP nelle VM di Azure

#### Configurazione dell'archiviazione 
Se si configura SAP Content Server per archiviare i file nel database SAP MaxDB, tutti i suggerimenti sulle procedure consigliate per l'archiviazione di Azure indicati per SAP MaxDB nel capitolo [Configurazione dell'archiviazione][dbms-guide-8.4.1] sono validi anche per lo scenario di SAP Content Server.

Se si configura SAP Content Server per archiviare i file nel file system, è consigliabile usare un'unità logica dedicata. L'uso di spazi di archiviazione consente anche di aumentare le dimensioni del disco logico e la velocità effettiva delle operazioni di I/O al secondo, come descritto nel capitolo [RAID software][dbms-guide-2.2].

#### Posizione di SAP Content Server
SAP Content Server deve essere distribuito nella stessa area di Azure e nella stessa rete virtuale di Azure in cui è stato distribuito il sistema SAP. È possibile scegliere se distribuire i componenti di SAP Content Server in una VM di Azure dedicata o nella stessa VM in cui è in esecuzione il sistema SAP.
 
![VM di Azure dedicata per SAP Content Server][dbms-guide-figure-800]

#### Posizione di SAP Cache Server
SAP Cache Server è un componente aggiuntivo basato su server che fornisce accesso in locale ai documenti (memorizzati nella cache). SAP Cache Server memorizza nella cache i documenti di un'istanza di SAP Content Server per poter ottimizzare il traffico di rete se i documenti devono essere recuperati più di una volta da posizioni diverse. La regola generale prevede che SAP Cache Server deve essere fisicamente vicino al client che accede a SAP Cache Server.

Sono disponibili due opzioni:

1. **Il client è un sistema SAP back-end** Se un sistema SAP back-end viene configurato per accedere a SAP Content Server, tale sistema SAP è un client. Poiché sia il sistema SAP che SAP Content Server vengono distribuiti nella stessa area di Azure, ovvero nello stesso data center di Azure, sono fisicamente vicini. Non è quindi necessaria un'istanza di SAP Cache Server dedicata. I client dell'interfaccia utente SAP (GUI SAP o Web browser) accedono direttamente al sistema SAP e il sistema SAP recupera i documenti da SAP Content Server.
1. **Il client è un Web browser locale** SAP Content Server può essere configurato per essere accessibile direttamente dal Web browser. In questo caso, un Web browser in esecuzione in locale è un client di SAP Content Server. Il data center locale e il data center di Azure vengono posti in posizioni fisiche diverse, idealmente vicini l'uno all'altro. Il data center locale viene connesso ad Azure con una VPN da sito a sito di Azure o con ExpressRoute. Anche se entrambe le opzioni offrono una connessione di rete VPN sicura, la connessione di rete da sito a sito non offre un contratto di servizio per la latenza e la larghezza di banda di rete tra il data center locale e il data center di Azure. Per velocizzare l'accesso ai documenti, è possibile eseguire una di queste operazioni:
    1. Installare SAP Cache Server in locale, vicino al Web browser locale (opzione illustrata in [questa][dbms-guide-900-sap-cache-server-on-premises] figura)
    1. Configurare Azure ExpressRoute, che offre una connessione di rete dedicata a velocità elevata e a bassa latenza tra il data center locale e il data center di Azure.
 
![Possibilità di installare SAP Cache Server in locale][dbms-guide-figure-900] <a name="642f746c-e4d4-489d-bf63-73e80177a0a8"></a>

#### Backup/Ripristino
Se si configura SAP Content Server per archiviare i file nel database SAP MaxDB, la procedura di backup/ripristino e le considerazioni sulle prestazioni sono già state descritte nel capitolo su SAP MaxDB [Backup e ripristino][dbms-guide-8.4.2] e nel capitolo [Considerazioni sulle prestazioni per il backup e il ripristino][dbms-guide-8.4.3].

Se si configura SAP Content Server per archiviare i file nel file system, una possibilità consiste nell'eseguire il backup/ripristino manuale dell'intera struttura di file in cui si trovano i documenti. Come per il backup/ripristino di SAP MaxDB, è consigliabile avere un volume del disco dedicato per il backup.

#### Altri
Le altre impostazioni specifiche di SAP Content Server sono trasparenti per le VM di Azure e sono descritte in diversi documenti e note SAP:

* <https://service.sap.com/contentserver>
* Nota SAP [1619726]

## Specifiche per IBM DB2 per LUW in Windows
Con Microsoft Azure, è possibile eseguire facilmente la migrazione dell'applicazione SAP esistente in esecuzione in IBM DB2 per Linux, UNIX e Windows (LUW) alle macchine virtuali di Azure. Con SAP in IBM DB2 per LUW, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale. Informazioni generali sull'esecuzione di SAP Business Suite in IBM DB2 per LUW sono disponibili in SAP Community Network (SCN) su <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Per altre informazioni e aggiornamenti su SAP in DB2 per LUW in Azure, vedere la nota SAP [2233094].

### Supporto della versione di IBM DB2 per Linux, UNIX e Windows
SAP in IBM DB2 per LUW nei servizi Macchine virtuali di Microsoft Azure è supportato a partire da DB2 versione 10.5.

Per informazioni sui prodotti SAP e sui tipi di VM di Azure supportati, vedere la nota SAP [1928533].

### Linee guida per la configurazione di IBM DB2 per Linux, UNIX e Windows per le installazioni di SAP nelle VM di Azure

#### Configurazione dell'archiviazione
Tutti i file di database devono essere archiviati nel file system NTFS basato su dischi rigidi virtuali. Questi dischi rigidi virtuali vengono montati nella VM di Azure e si basano sull'archiviazione BLOB di pagine di Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Le unità di rete o le condivisioni remote di qualsiasi tipologia, ad esempio i servizi file di Azure seguenti, **NON** sono supportati per i file di database:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>
 
Se si usano i dischi rigidi virtuali di Azure basati sull'archiviazione BLOB di pagine di Azure, le istruzioni illustrate in questo documento nel capitolo [Struttura di una distribuzione RDBMS][dbms-guide-2] si applicano anche alle distribuzioni con il database IBM DB2 per LUW.

Come spiegato prima nella parte generale del documento, esistono quote relative alla velocità effettiva delle operazioni di I/O al secondo per i dischi rigidi virtuali di Azure. Le quote esatte dipendono dal tipo di VM usato. Un elenco di tipi di VM con le rispettive quote è disponibile [qui][virtual-machines-sizes].

Purché la quota corrente di operazioni di I/O al secondo per ogni disco sia sufficiente, è possibile archiviare tutti i file di database in un solo disco rigido virtuale di Azure montato.

Per le considerazioni sulle prestazioni, vedere anche il capitolo relativo alle considerazioni sulle prestazioni e la sicurezza dei dati per le directory di database nelle guide di installazione di SAP.

In alternativa, è possibile usare i pool di archiviazione di Windows (disponibili solo in Windows Server 2012 e versioni successive) o lo striping di Windows per Windows 2008 R2, come descritto nel capitolo [RAID software][dbms-guide-2.2] di questo documento, per creare un solo dispositivo logico di grandi dimensioni su più dischi rigidi virtuali montati. Per i dischi contenenti i percorsi di archiviazione DB2 per le directory sapdata e saptmp, è necessario specificare una dimensione del settore del disco pari a 512 KB. Quando si usano i pool di archiviazione di Windows, è necessario creare manualmente i pool di archiviazione con l'interfaccia della riga di comando usando il parametro „-LogicalSectorSizeDefault“. Per informazioni dettagliate, vedere <https://technet.microsoft.com/library/hh848689.aspx>.

#### Backup/Ripristino
La funzionalità di backup/ripristino per IBM DB2 per LUW è supportata esattamente come nei sistemi operativi Windows Server standard e in Hyper-V.

È necessario verificare di avere adottato una valida strategia di backup di database.

Come nelle distribuzioni bare metal, le prestazioni di backup/ripristino dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. La quota di utilizzo della CPU usata dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di soli 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare gli spazi allocati per il database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Meno sono le destinazioni (directory Stripe, dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, è possibile usare/combinare due opzioni a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi rigidi virtuali montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Uso di più di una directory di destinazione in cui scrivere il backup

#### Disponibilità elevata e ripristino di emergenza
Il server di cluster Microsoft non è supportato.

Il ripristino di emergenza a disponibilità DB2 è supportato. Se le macchine virtuali della configurazione a disponibilità elevata hanno una risoluzione dei nomi funzionante, la configurazione in Azure non sarà diversa da quelle eseguite in locale. Non è consigliabile affidarsi solo alla risoluzione IP.

Non usare la replica geografica di Azure Store. Per altre informazioni, vedere il capitolo [Archiviazione di Microsoft Azure][dbms-guide-2.3] e il capitolo [Disponibilità elevata e ripristino di emergenza con le VM di Azure][dbms-guide-3].

#### Altri
Tutti gli altri argomenti generali, ad esempio i set di disponibilità di Azure o il monitoraggio SAP, si applicano come descritto nei primi tre capitoli di questo documento anche per le distribuzioni di VM con IBM DB2 per LUW.

Vedere anche il capitolo [Riepilogo generale su SQL Server per SAP in Azure][dbms-guide-5.8].

## Specifiche per IBM DB2 per LUW in Linux
Con Microsoft Azure, è possibile eseguire facilmente la migrazione dell'applicazione SAP esistente in esecuzione in IBM DB2 per Linux, UNIX e Windows (LUW) alle macchine virtuali di Azure. Con SAP in IBM DB2 per LUW, gli amministratori e gli sviluppatori possono continuare a usare gli stessi strumenti di sviluppo e amministrazione disponibili in locale. Informazioni generali sull'esecuzione di SAP Business Suite in IBM DB2 per LUW sono disponibili in SAP Community Network (SCN) su <https://scn.sap.com/community/db2-for-linux-unix-windows>.

Per altre informazioni e aggiornamenti su SAP in DB2 per LUW in Azure, vedere la nota SAP [2233094].

### Supporto della versione di IBM DB2 per Linux, UNIX e Windows
SAP in IBM DB2 per LUW nei servizi Macchine virtuali di Microsoft Azure è supportato a partire da DB2 versione 10.5.

Per informazioni sui prodotti SAP e sui tipi di VM di Azure supportati, vedere la nota SAP [1928533].

### Linee guida per la configurazione di IBM DB2 per Linux, UNIX e Windows per le installazioni di SAP nelle VM di Azure

#### Configurazione dell'archiviazione
Tutti i file di database devono essere archiviati nel file system basato su dischi rigidi virtuali. Questi dischi rigidi virtuali vengono montati nella VM di Azure e si basano sull'archiviazione BLOB di pagine di Azure (<https://msdn.microsoft.com/library/azure/ee691964.aspx>). Le unità di rete o le condivisioni remote di qualsiasi tipologia, ad esempio i servizi file di Azure seguenti, **NON** sono supportati per i file di database:

* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx>
* <https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx>

Se si usano i dischi rigidi virtuali di Azure basati sull'archiviazione BLOB di pagine di Azure, le istruzioni illustrate in questo documento nel capitolo [Struttura di una distribuzione RDBMS][dbms-guide-2] si applicano anche alle distribuzioni con il database IBM DB2 per LUW.

Come spiegato prima nella parte generale del documento, esistono quote relative alla velocità effettiva delle operazioni di I/O al secondo per i dischi rigidi virtuali di Azure. Le quote esatte dipendono dal tipo di VM usato. Un elenco di tipi di VM con le rispettive quote è disponibile [qui][virtual-machines-sizes].

Purché la quota corrente di operazioni di I/O al secondo per ogni disco sia sufficiente, è possibile archiviare tutti i file di database in un solo disco rigido virtuale di Azure montato.

Per le considerazioni sulle prestazioni, vedere anche il capitolo relativo alle considerazioni sulle prestazioni e la sicurezza dei dati per le directory di database nelle guide di installazione di SAP.

In alternativa, è possibile usare la gestione dei volumi logici o MDADM come descritto nel capitolo [RAID software][dbms-guide-2.2] di questo documento per creare un grande dispositivo logico su più dischi rigidi virtuali montati. Per i dischi contenenti i percorsi di archiviazione DB2 per le directory sapdata e saptmp, è necessario specificare una dimensione del settore del disco pari a 512 KB.

#### Backup/Ripristino
La funzionalità di backup/ripristino per IBM DB2 per LUW è supportata esattamente come nell'installazione Linux locale standard.

È necessario verificare di avere adottato una valida strategia di backup di database.

Come nelle distribuzioni bare metal, le prestazioni di backup/ripristino dipendono dalla quantità di volumi leggibili in parallelo e dalla velocità effettiva di tali volumi. La quota di utilizzo della CPU usata dalla compressione del backup può anche avere un ruolo significativo per le VM con un massimo di soli 8 thread CPU. Si può quindi presumere quanto segue:

* Minore è il numero di dischi rigidi virtuali usati per archiviare gli spazi allocati per il database, più bassa è la velocità effettiva generale nella lettura
* Minore è il numero di thread CPU nella VM, più forte è l'impatto della compressione del backup
* Meno sono le destinazioni (directory Stripe, dischi rigidi virtuali) in cui scrivere il backup, più bassa è la velocità effettiva

Per aumentare il numero di destinazioni in cui scrivere, è possibile usare/combinare due opzioni a seconda delle proprie esigenze:

* Striping del volume di destinazione di backup su più dischi rigidi virtuali montati per migliorare la velocità effettiva delle operazioni di I/O al secondo in tale volume con striping
* Uso di più di una directory di destinazione in cui scrivere il backup

#### Disponibilità elevata e ripristino di emergenza
Il ripristino di emergenza a disponibilità DB2 è supportato. Se le macchine virtuali della configurazione a disponibilità elevata hanno una risoluzione dei nomi funzionante, la configurazione in Azure non sarà diversa da quelle eseguite in locale. Non è consigliabile affidarsi solo alla risoluzione IP.

Non usare la replica geografica di Azure Store. Per altre informazioni, vedere il capitolo [Archiviazione di Microsoft Azure][dbms-guide-2.3] e il capitolo [Disponibilità elevata e ripristino di emergenza con le VM di Azure][dbms-guide-3].

#### Altri
Tutti gli altri argomenti generali, ad esempio i set di disponibilità di Azure o il monitoraggio SAP, si applicano come descritto nei primi tre capitoli di questo documento anche per le distribuzioni di VM con IBM DB2 per LUW.

Vedere anche il capitolo [Riepilogo generale su SQL Server per SAP in Azure][dbms-guide-5.8].

<!---HONumber=AcomDC_0928_2016-->
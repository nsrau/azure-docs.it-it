<properties
   pageTitle="SAP NetWeaver in macchine virtuali (VM) Windows - Guida alle funzionalità di disponibilità elevata | Microsoft Azure"
   description="SAP NetWeaver in macchine virtuali (VM) Windows - Guida alle funzionalità di disponibilità elevata"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# SAP NetWeaver in macchine virtuali (VM) Windows - Guida alle funzionalità di disponibilità elevata

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

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla distribuzione DBMS"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "Memorizzazione nella cache per VM e dischi rigidi virtuali"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "RAID software"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Archiviazione di Microsoft Azure"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "Struttura di una distribuzione RDBMS"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Disponibilità elevata e ripristino di emergenza con macchine virtuali di Azure"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 e versioni successive"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 e versioni precedenti"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "Uso di immagini di SQL Server al di fuori di Microsoft Azure Marketplace"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "Riepilogo generale su SQL Server per SAP in Azure"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "Specifiche per RDBMS SQL Server"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "Configurazione dell'archiviazione"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "Backup e ripristino"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "Considerazioni sulle prestazioni per il backup e il ripristino"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "Altri"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla distribuzione"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "Risorse SAP"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "Distribuzione di una VM con un'immagine personalizzata"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "Scenario 1: Distribuzione di una VM da Azure Marketplace per SAP"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "Scenario 2: Distribuzione di una VM con un'immagine personalizzata per SAP"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "Scenario 3: Spostamento di una VM locale usando un disco rigido virtuale di Azure non generalizzato con SAP"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "Scenari di distribuzione di VM per SAP in Microsoft Azure"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "Distribuzione dei cmdlet di Azure PowerShell"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "Scaricare e importare i cmdlet di PowerShell pertinenti per SAP"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "Aggiungere la VM a un dominio locale (solo per Windows)"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "Scaricare, installare e abilitare l'agente di VM di Azure"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Interfaccia della riga di comando di Azure"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "Configurare l'estensione di monitoraggio avanzato di Azure per SAP"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Controllo dello stato di preparazione del monitoraggio avanzato di Azure per SAP"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Controllo dell'integrità della configurazione dell'infrastruttura di monitoraggio di Azure"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "Risoluzione di altri problemi dell'infrastruttura di monitoraggio di Azure per SAP"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "Configurare il monitoraggio"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "Configurare il proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "Controlli e risoluzione dei problemi per la configurazione del monitoraggio end-to-end per SAP in Azure"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla pianificazione e all'implementazione"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "Risorse"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Disponibilità elevata e ripristino di emergenza per SAP NetWeaver in esecuzione in macchine virtuali di Azure"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "Disponibilità elevata per i server applicazioni SAP"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "Uso dell'avvio automatico per le istanze di SAP"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "Solo cloud: distribuzioni di macchine virtuali in Azure senza dipendenze dalla rete locale dei clienti"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "Più sedi locali: distribuzione di una singola VM o di più VM SAP in Azure che devono essere completamente integrate con la rete locale"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Aree di Azure"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "Domini di errore"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "Domini di aggiornamento"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Set di disponibilità di Azure"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Concetto di macchina virtuale di Microsoft Azure"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Archiviazione Premium di Azure"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "Spostamento di una macchina virtuale da locale in Azure con un disco non generalizzato"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "Distribuzione di una VM con un'immagine specifica del cliente"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "Preparazione per lo spostamento di una VM da locale ad Azure con un disco non generalizzato"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "Preparazione per la distribuzione di una VM con un'immagine specifica del cliente per SAP"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "Preparazione di VM con SAP per Azure"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Differenza tra un disco di Azure e un'immagine di Azure"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "Caricamento di un disco rigido virtuale da locale ad Azure"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "Copia di dischi tra account di archiviazione di Azure"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "Struttura di VM/dischi rigidi virtuali per distribuzioni SAP"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "Impostazione del montaggio automatico per dischi collegati"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "Scenario di formazione/dimostrativo di una singola VM con SAP NetWeaver"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "Concetti della distribuzione solo cloud di istanze di SAP"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "Soluzione di monitoraggio di Azure per SAP"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Archiviazione Premium di Azure"

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
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Rete di Microsoft Azure"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "Archiviazione: Archiviazione di Microsoft Azure e dischi dati"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "SAP NetWeaver in macchine virtuali (VM) Windows - Guida alle funzionalità di disponibilità elevata"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "Prerequisiti"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "Risorse"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Differenze tra il modello di distribuzione di Azure Resource Manager e quello classico in SAP a disponibilità elevata"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "Gruppi di risorse"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Il clustering con Azure Resource Manager rispetto al modello di distribuzione classica"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "Windows Server Failover Clustering (WSFC)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "Modalità quorum"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "Cluster di failover Windows in locale"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "Archiviazione condivisa"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "Rete/risoluzione dei nomi"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "Cluster di failover Windows con Microsoft Azure"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "Disco condiviso in Microsoft Azure con SIOS DataKeeper"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Risoluzione dei nomi in Microsoft Azure"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Disponibilità elevata di SAP NetWeaver su IaaS di Azure"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "Disponibilità elevata per i server applicazioni SAP"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "Disponibilità elevata per l'istanza di SAP (A)SCS"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "Disponibilità elevata per l'istanza di SAP (A)SCS con il cluster di failover Windows in Azure"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "Disponibilità elevata per l'istanza DBMS"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "Possibili scenari di distribuzione end-to-end a disponibilità elevata"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "Preparazione dell'infrastruttura"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "Distribuzione di VM con connettività di rete aziendale (cross-premise) per l'uso in produzione"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "Distribuzione solo cloud delle istanze di SAP per test e demo"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Rete virtuale di Azure ("
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "Indirizzi IP DNS"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "Nomi host e indirizzi IP statici per l'istanza di SAP ASCS/SCS in cluster e l'istanza di DBMS in cluster"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "Configurazione di indirizzi IP statici per le VM SAP"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "Configurazione dell'indirizzo IP statico per il bilanciamento del carico interno (ILB)"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "Modifica delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "Aggiungere computer Windows al dominio"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster usati per l'istanza di SAP ASCS/SCS"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "Configurazione di Windows Server Failover Cluster per l'istanza di SAP ASCS/SCS"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "Raccogliere i nodi di cluster nella configurazione del cluster"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "Configurare un controllo di condivisione file del cluster"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "Creare una condivisione file"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "Configurare il quorum del controllo di condivisione file in Gestione cluster di failover"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "Aggiungere la funzionalità Microsoft .NET Framework 3.5"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "Installazione di SIOS DataKeeper"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "Installare SIOS DataKeeper"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "Installazione del sistema SAP NetWeaver"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "Installazione di SAP con istanza di ASCS/SCS a disponibilità elevata"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "Creare il nome host virtuale per SAP ASCS/SCS in cluster"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "Installare il primo nodo del cluster SAP"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "Modificare il profilo SAP dell'istanza ASCS/SCS"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "Aggiungere la porta probe"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "Installazione dell'istanza di database"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "Installazione del secondo nodo del cluster"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "Cambiare il tipo di avvio del servizio Windows dell'istanza di SAP ERS"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "Installazione di Primary Application Server (PAS) SAP"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "Installazione di Additional Application Server (AAS) SAP"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "Test del failover e della replica SIOS dell'istanza di SAP ASCS/SCS"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "Punto iniziale: istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "Processo di failover dal nodo A al nodo B"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "Punto finale: istanza di SAP ASCS/SCS in esecuzione nel nodo B del cluster"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


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
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "Distribuire e gestire le macchine virtuali usando modelli di Gestione risorse di Azure e l'interfaccia della riga di comando di Azure"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "Gestire le macchine virtuali usando Gestione risorse di Azure e PowerShell"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
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
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Microsoft Azure consente alle aziende di acquisire risorse di calcolo, di archiviazione e rete in poco tempo, senza lunghi cicli di approvvigionamento. Macchine virtuali di Azure consente alle aziende di distribuire applicazioni classiche, ad esempio applicazioni basate su SAP NetWeaver, come ABAP, Java e stack ABAP+Java, in Azure e di estenderne l'affidabilità e la disponibilità senza risorse aggiuntive disponibili in locale. Macchine virtuali di Azure supportano anche la connettività cross-premise, che consente alle aziende di integrare in modo attivo le Macchine virtuali di Azure nei propri domini locali, i propri cloud privati e nel panorama applicativo del sistema SAP.


Questo documento illustra tutti i passaggi necessari per distribuire sistemi SAP a disponibilità elevata in Azure usando il nuovo metodo con il nuovo modello di distribuzione Azure Resource Manager. La guida illustrerà i passaggi principali:


- Le note e le guide all'installazione di SAP appropriate sono elencate più avanti nella sezione [Risorse][sap-ha-guide-2].  
  Questo documento è complementare alla documentazione relativa all'installazione di SAP e alle note su SAP che rappresentano le risorse principali per le installazioni e le distribuzioni del software SAP nelle piattaforme specifiche.

- Informazioni sulla differenza tra l'attuale modello di distribuzione classica di Azure e il nuovo modello di distribuzione Azure Resource Manager.

- Informazioni sulle modalità di quorum di Windows Server Failover Cluster (WSFC) per selezionare il modello appropriato per una distribuzione specifica di Azure

- Informazioni sull'archiviazione condivisa di Windows Server Failover Cluster (WSFC) in Azure

- Informazioni su come proteggere i componenti con singolo punto di guasto SAP, come SAP ASCS/SCS e DBMS, e i componenti ridondanti, come i server applicazioni SAP in Azure

- Approccio dettagliato su come installare e configurare un sistema SAP a disponibilità elevata in un WSFC (Windows Failover Cluster) tramite Microsoft Azure come piattaforma e il nuovo Azure Resource Manager.

- Passaggi aggiuntivi necessari per WSFC in Azure che non sono necessari nelle distribuzioni locali


Per semplificare la distribuzione e la configurazione, stiamo usando i nuovi modelli di Azure Resource Manager a disponibilità elevata a 3 livelli per SAP, che consentono di automatizzare la distribuzione dell'infrastruttura completa necessaria per il sistema SAP a disponibilità elevata e che supportano il ridimensionamento SAPS desiderato del sistema SAP in uso.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Prerequisiti

Prima di iniziare, verificare che siano soddisfatti i prerequisiti descritti nei capitoli seguenti e che siano disponibili tutte le risorse elencate nel capitolo dedicato alle risorse.

Stiamo usando i modelli di Azure Resource Manager per SAP NetWeaver a 3 livelli: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

Di seguito viene riportata una panoramica dei modelli di Azure Resource Manager per SAP: [https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Risorse

Per informazioni sull'argomento relativo alle distribuzioni SAP in Azure sono disponibili le seguenti guide aggiuntive:

- [SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla pianificazione e all'implementazione][planning-guide]
- [SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla distribuzione][deployment-guide]
- [SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla distribuzione DBMS][dbms-guide]
- [SAP NetWeaver in macchine virtuali (VM) Windows - Guida alle funzionalità di disponibilità elevata (questa guida)][sap-ha-guide]


> [AZURE.NOTE] Dove possibile, viene usato un collegamento alla Guida all'installazione SAP di riferimento. Vedere [SAP Installation Guides][sap-installation-guides] \(Guide all'installazione di SAP). Per quanto riguarda i prerequisiti e il processo di installazione, è necessario leggere sempre con attenzione la Guida all'installazione di SAP NetWeaver, perché il presente documento illustra solo attività specifiche per i sistemi basati su SAP NetWeaver installati in Macchine virtuali di Microsoft Azure.

Le note seguenti su SAP sono correlate all'argomento relativo a SAP in Azure:


| Numero della nota | Titolo                                                    
| ------------- |----------------------------------------------------------
| [1928533] | Applicazioni SAP in Azure: dimensioni e prodotti supportati 
| [2015553] | SAP in Microsoft Azure: prerequisiti per il supporto         
| [1999351] | Monitoraggio avanzato di Azure per SAP                        
| [2178632] | Metriche chiave del monitoraggio per SAP in Microsoft Azure        
| [1999351] | Virtualizzazione in Windows: monitoraggio avanzato           


Le limitazioni predefinite generali e le limitazioni massime delle sottoscrizioni di Azure sono disponibili in [questo articolo][azure-subscription-service-limits-subscription].

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Differenze tra il modello di distribuzione di Azure Resource Manager e quello classico in SAP a disponibilità elevata 

> [AZURE.NOTE] Il modello di distribuzione classico è noto anche come modello di gestione del servizio Microsoft Azure (ASM).

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Gruppi di risorse
I gruppi di risorse sono un nuovo concetto e contengono tutte le risorse con lo stesso ciclo di vita, ad esempio che vengono create ed eliminate contemporaneamente. Per altre informazioni sui gruppi di risorse, leggere questo articolo.

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Confronto del clustering con Azure Resource Manager e con il modello di distribuzione classica 

Il nuovo modello Azure Resource Manager introduce le seguenti modifiche rispetto al modello di distribuzione classica a livello di disponibilità elevata:

- Non è necessario un servizio cloud per usare un servizio di bilanciamento del carico interno (ILB) di Azure

Se si desidera ancora usare il modello classico di Azure, è necessario seguire la procedura descritta nel documento [SAP NetWeaver in Azure - Clustering di istanze SAP ASCS/SCS tramite Windows Server Failover Cluster in Azure con SIOS DataKeeper](http://go.microsoft.com/fwlink/?LinkId=613056).

> [AZURE.NOTE] È fortemente consigliato usare il nuovo modello di distribuzione Azure Resource Manager per le installazioni di SAP, perché offre molti vantaggi rispetto al modello di distribuzione classica. Altre informazioni sono disponibili in [questo articolo][virtual-machines-azure-resource-manager-architecture-benefits-arm].


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server Failover Clustering (WSFC) 

Microsoft WSFC è la base tecnica per l'installazione di SAP ASCS/SCS e DBMS a disponibilità elevata in Windows.

Un cluster di failover è un gruppo di 1 + n server (nodi) indipendenti che funzionano insieme per aumentare la disponibilità di applicazioni e servizi. Nel caso in cui si verifichino uno o più errori nel nodo, WSFC deve determinare il numero di errori che possono verificarsi pur mantenendo un cluster integro per poter essere in grado di fornire le applicazioni e/o i servizi definiti. Per ottenere questo risultato, sono disponibili modalità quorum diverse.
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Modalità quorum

Con WSFC sono disponibili quattro modalità diverse:

- **Maggioranza dei nodi:** ogni nodo può vuotare. Il cluster funziona solo con la maggioranza dei voti, vale a dire più della metà. Questa opzione è consigliata in caso di un numero dispari di nodi. Ad esempio: anche se 3 nodi di un cluster a 7 nodi presentano un errore, il cluster è comunque in grado di ottenere la maggioranza e continuare l'esecuzione.

- **Maggioranza dei nodi e dei dischi:** ogni nodo più un disco designato nell'archiviazione del cluster, il "disco di controllo", può votare ogni volta che è disponibile e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire più della metà. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi. A condizione che metà dei nodi e il disco siano online, il cluster rimane in uno stato di integrità.

- **Maggioranza dei nodi e delle condivisioni file:** ogni nodo, più una condivisione file designata creata dall'amministratore (condivisione file di controllo) può votare se è disponibile e in comunicazione. Il cluster funziona solo con la maggioranza dei voti, vale a dire più della metà. Questa modalità ha senso in un ambiente cluster con un numero pari di nodi ed è simile alla modalità maggioranza dei nodi e dei dischi ma usa una condivisione file di controllo anziché un disco di controllo. È facile da implementare, ma la condivisione file potrebbe diventare un singolo punto di guasto, se non è a disponibilità elevata.

- **Non di maggioranza - solo disco:** il cluster dispone del quorum se un nodo è disponibile e in comunicazione con un disco specifico nell'archiviazione del cluster. Solo i nodi che sono anche in comunicazione con questo disco possano aggiungersi al cluster. È consigliabile non usare questa modalità.  

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Cluster di failover Windows in locale

In questo esempio esaminiamo un cluster costituito da due nodi. Se si verifica un errore della connessione di rete tra i nodi mentre entrambi i nodi sono attivi, è necessario chiarire quale nodo deve continuare a fornire le applicazioni e i servizi del cluster. Questa funzione viene svolta da una condivisione file o un disco quorum. Il nodo che dispone dell'accesso alla condivisione file o al disco quorum è quello che garantisce l'accessibilità dei servizi.

In questo esempio stiamo usando un cluster a due nodi. Per questo motivo abbiamo scelto la modalità quorum del nodo e della condivisione file. Anche l'opzione Maggioranza dei nodi e dei dischi è valida. In un ambiente di produzione è consigliabile usare invece un disco quorum e la tecnologia del sistema di archiviazione e rete per assicurare una disponibilità elevata.

![Figura 1: Configurazione di Windows Server Failover Cluster proposta per SAP ASCS/SCS in Azure][sap-ha-guide-figure-1000]

_**Figura 1:** Configurazione di Windows Server Failover Cluster proposta per SAP ASCS/SCS in Azure_

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Archiviazione condivisa

La figura precedente mostra un cluster di archiviazione condivisa con due nodi. In un cluster di archiviazione condivisa in locale è presente un'archiviazione condivisa visibile per tutti i nodi all'interno del cluster. Un meccanismo di blocco consente di proteggere i dati da danneggiamenti. Inoltre, tutti i nodi possono rilevare se si verifica un errore in un altro nodo. Se si verifica un errore in un nodo, quello rimanente assume la proprietà delle risorse di archiviazione e assicura la disponibilità dei servizi.

> [AZURE.NOTE] Per ottenere una disponibilità elevata con un sistema DBMS, ad esempio SQL Server, i dischi condivisi non sono necessari. La funzionalità AlwaysOn di SQL Server esegue la replica dei file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. Di conseguenza, la configurazione del cluster di Windows non richiede un disco condiviso.

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Rete/risoluzione dei nomi

Il cluster è raggiungibile tramite un indirizzo IP virtuale e un nome host virtuale fornito dal server DNS. I nodi in locale e il server DNS sono in grado di gestire più indirizzi IP.

In un'installazione tipica, vengono usate due o più connessioni di rete:

- Una connessione dedicata all'archiviazione; e
- Una connessione di rete interna al cluster per l'heartbeat; e
- Una rete pubblica usata dai client per la connessione al cluster.



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Cluster di failover Windows con Microsoft Azure

Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un WSFC in Macchine virtuali di Microsoft Azure. Per creare un disco cluster condiviso, sono necessari diversi indirizzi IP e nomi host virtuali per l'istanza di SAP ASCS/SCS.

Di seguito illustreremo altri concetti e passaggi necessari quando si crea un cluster Central Services a disponibilità elevata SAP in Microsoft Azure. La procedura mostra come installare lo strumento SIOS DataKeeper di terze parti e come configurare il servizio di bilanciamento del carico interno di Azure. Questi strumenti consentono di creare un cluster di failover Windows con una condivisione file di controllo in Microsoft Azure.


![Figura 2: Schema di una configurazione di Windows Server Failover Cluster in Azure senza disco condiviso][sap-ha-guide-figure-1001]

_**Figura 2:** Schema di una configurazione di Windows Server Failover Cluster in Azure senza disco condiviso_


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Disco condiviso in Microsoft Azure con SIOS DataKeeper

A partire da settembre 2016, Microsoft Azure non fornisce l'archiviazione condivisa per creare un cluster di archiviazione condivisa. Un'istanza di SAP ASCS/SCS a disponibilità elevata richiede tuttavia l'archiviazione condivisa del cluster.

Il software SIOS DataKeeper Cluster Edition di terze parti consente di creare una risorsa di archiviazione con mirroring che simula l'archiviazione condivisa del cluster. La soluzione SIOS fornisce la replica di dati sincrona in tempo reale. La procedura di creazione di una risorsa di disco condiviso per un cluster è la seguente:

- Un disco rigido virtuale di Azure aggiuntivo collegato a ogni VM presente in una configurazione di cluster Windows.
- SIOS DataKeeper Cluster Edition in esecuzione in entrambi i nodi delle VM.
- SIOS DataKeeper Cluster Edition configurato per eseguire il mirroring del contenuto del volume collegato del disco rigido virtuale aggiuntivo dalle VM di origine al volume collegato del disco rigido virtuale aggiuntivo della VM di destinazione. SIOS DataKeeper astrae i volumi locali di origine e di destinazione e li presenta al cluster di failover Windows come un disco condiviso.

Per altri dettagli sul prodotto SIOS DataKeeper, controllare qui: [http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)

 ![Figura 2: Schema di una configurazione di Windows Server Failover Cluster in Azure tramite SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 3:** Schema di una configurazione di Windows Server Failover Cluster in Azure tramite SIOS DataKeeper_

> [AZURE.NOTE] Per ottenere una disponibilità elevata con un sistema DBMS, ad esempio SQL Server, i dischi condivisi non sono necessari. La funzionalità AlwaysOn di SQL Server sta eseguendo la replica dei file di dati e di log del sistema DBMS dal disco locale di un nodo del cluster nel disco locale di un altro nodo del cluster. Di conseguenza, la configurazione del cluster di Windows non richiede un disco condiviso.

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Risoluzione dei nomi in Microsoft Azure

La piattaforma cloud di Microsoft Azure non consente di configurare gli indirizzi IP virtuali, ad esempio gli indirizzi IP a virgola mobile. Per questo motivo, è necessaria una soluzione alternativa per configurare un indirizzo IP virtuale in modo da raggiungere la risorsa cluster nel cloud. Azure offre il servizio di bilanciamento del carico interno (ILB). Con il servizio ILB il cluster può essere raggiunto tramite l'indirizzo IP virtuale del cluster. Di conseguenza è necessario distribuire l'ILB nel gruppo di risorse che contiene i nodi del cluster. È quindi necessario configurare tutte le necessarie regole di port forwarding con le porte probe dell'ILB. I client possono connettersi tramite il nome host virtuale. Il server DNS risolve l'indirizzo IP del cluster e il servizio ILB gestisce l'inoltro al nodo attivo del cluster.

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Disponibilità elevata di SAP NetWeaver su IaaS di Azure

Come già descritto nel capitolo sulla disponibilità elevata [SAP NetWeaver in macchine virtuali di Azure di SAP NetWeaver in macchine virtuali (VM) Windows - Guida alla pianificazione e all'implementazione][planning-guide-11] per ottenere la disponibilità elevata delle applicazioni SAP, ad esempio, quella dei componenti software SAP, è necessario proteggere i componenti seguenti:

- Server applicazioni SAP
- Istanza di SAP ASCS/SCS
- Server DBMS

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Disponibilità elevata per i server applicazioni SAP

Per le istanze dei server/delle finestre di dialogo delle applicazioni SAP non è richiesta una specifica soluzione a disponibilità elevata. La disponibilità elevata si ottiene tramite la ridondanza e pertanto con più istanze delle finestre di dialogo configurate in macchine virtuali diverse di Azure. È necessario disporre di almeno due istanze dell'applicazione SAP installate in due VM di Azure.

![Figura 4: Server applicazioni SAP a disponibilità elevata][sap-ha-guide-figure-2000]

_**Figura 4:** Server applicazioni SAP a disponibilità elevata_


Tutte le macchine virtuali che ospitano i server applicazioni SAP devono trovarsi nello stesso **set di disponibilità di Azure**. Il set di disponibilità di Azure garantisce che:

- Tutte le VM fanno parte degli stessi domini di aggiornamento, ad esempio si eviterà la possibilitò che le VM vengano aggiornate nello stesso momento durante il tempo di inattività della manutenzione pianificata.
- Tutte le VM faranno parte degli stessi domini di errore, ad esempio le VM verranno distribuite in modo da evitare che un singolo punto di guasto possa influire sulla disponibilità di tutte le VM.

Per altre informazioni, vedere l'articolo [Gestione della disponibilità delle macchine virtuali][virtual-machines-manage-availability].

Poiché l'account di archiviazione di Azure può essere un potenziale singolo punto di guasto, è importante disporre di almeno due account di archiviazione di Azure in cui verranno distribuite almeno due VM. In una condizione ideale, ogni VM che esegue istanze della finestra di dialogo di SAP deve essere distribuita in un account di archiviazione diverso.

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Disponibilità elevata per le istanze di SAP (A)SCS 

![Figura 5: Istanza di SAP ASCS/SCS a disponibilità elevata][sap-ha-guide-figure-2001]

_**Figura 5:** Istanza di SAP ASCS/SCS a disponibilità elevata_


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Disponibilità elevata per l'istanza di SAP (A)SCS con il cluster di failover Windows in Azure

Rispetto alle distribuzioni bare metal o cloud privato, sono necessari passaggi aggiuntivi per configurare un WSFC in Macchine virtuali di Microsoft Azure. Per creare un cluster di failover Windows, sono necessari un disco cluster condiviso, più indirizzi IP e nomi host virtuali e un servizio di bilanciamento del carico interno (ILB) di Azure per il clustering dell'istanza di SAP ASCS/SCS.

Questo argomento verrà discusso più in dettaglio in una sezione successiva del documento.

![Figura 6: Schema di una configurazione di Windows Server Failover Cluster per la configurazione di SAP ASCS/SCS in Azure tramite SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Figura 6:** Schema di una configurazione di Windows Server Failover Cluster per la configurazione di SAP ASCS/SCS in Azure tramite SIOS DataKeeper_


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> Disponibilità elevata per l'istanza di DBMS

Anche il sistema DBMS è un singolo punto di guasto (SPOF) di un sistema SAP e deve essere protetto tramite una soluzione a disponibilità elevata. Di seguito è riportato un esempio di una soluzione AlwaysOn a disponibilità elevata di SQL Server in Azure tramite Windows Server Failover Cluster e un servizio di bilanciamento del carico interno di Azure. La funzionalità AlwaysOn di SQL Server replica i file di dati e di log DBMS tramite la replica propria di DBMS. Non sono quindi necessari dischi condivisi del cluster che semplificano l'impostazione completa.


![Figura 7: Server DBMS SAP a disponibilità elevata: esempio di impostazione AlwaysOn a disponibilità elevata di SQL Server][sap-ha-guide-figure-2003]

_**Figura 7:** Server DBMS SAP a disponibilità elevata: esempio di impostazione AlwaysOn a disponibilità elevata di SQL Server_


Questo documento non tratta il clustering del sistema DBMS.

Altre informazioni sul clustering di SQL Server in Azure tramite il modello di distribuzione Azure Resource Manager sono disponibili negli articoli seguenti:

- [Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [Configurare un servizio di bilanciamento del carico interno per un gruppo di disponibilità AlwaysOn in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> Possibili scenari di distribuzione end-to-end a disponibilità elevata

L'esempio seguente mostra un'architettura completa a disponibilità elevata NetWeaver in Azure, in cui un cluster è dedicato all'istanza di SAP ASCS/SCS e un cluster è dedicato al sistema DBMS.

![Figura 8: Modello architetturale 1 a disponibilità elevata per SAP, con cluster dedicato per ASCS/SCS e cluster dedicato per l'istanza di DBMS][sap-ha-guide-figure-2004]

_**Figura 8:** Modello architetturale 1 a disponibilità elevata per SAP, con cluster dedicato per ASCS/SCS e cluster dedicato per l'istanza di DBMS_

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Preparazione dell'infrastruttura

Per semplificare la distribuzione delle risorse necessarie per SAP, abbiamo sviluppato modelli di Azure Resource Manager per SAP.

Questi modelli a 3 livelli supportano anche scenari a disponibilità elevata, come:

- **Modello architetturale 1**: con due cluster, ogni cluster per i singoli punti di guasto SAP di SAP ASCS/SCS e DBMS

I modelli di Azure Resource Manager per lo scenario 1 sono disponibili qui:

- Immagine di Azure Marketplace: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- Immagine personalizzata: [https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

Quando si fa clic sull'immagine di Marketplace a 3 livelli per SAP, verrà visualizzata l'interfaccia utente seguente nel portale di Azure:

![Figura 9: Specifica dei parametri di Azure Resource Manager di disponibilità elevata per SAP][sap-ha-guide-figure-3000]

_**Figura 9:** Specifica dei parametri di Azure Resource Manager di disponibilità elevata per SAP_


Assicurarsi di scegliere **HA** (Disponibilità elevata) per l'opzione SYSTEMAVAILABILITY.

Verranno creati i modelli:

- Tutte le **VM** necessarie per:
    - VM dei server applicazioni SAP: `<SAPSystemSID>-di-<Number>`
    - VM del cluster ASCS/SCS: `<SAPSystemSID>-ascs-<Number>`
    - Cluster DBMS: `<SAPSystemSID>-db-<Number>`
- **Schede di rete per tutte le VM con gli indirizzi IP associati**:
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Account di archiviazione di Azure**
- **Gruppi di disponibilità** per:
    - VM dei server applicazioni SAP: `<SAPSystemSID>-avset-di`
    - VM del cluster SAP ASCS/SCS: `<SAPSystemSID>-avset-ascs`
    - VM del cluster DBMS: `<SAPSystemSID>-avset-db`
- **Il servizio di bilanciamento del carico interno (ILB) di Azure** con tutte le porte per l'istanza di ASCS/SCS e l'indirizzo IP `<SAPSystemSID>-lb-ascs`
-	**Il servizio di bilanciamento del carico interno (ILB) di Azure** con tutte le porte per SQL Server DBMS e l'indirizzo IP `<SAPSystemSID>-lb-db`
- **Gruppo di sicurezza di rete**: `<SAPSystemSID>-nsg-ascs-0` Con la porta RDP esterna aperta alla VM `<SAPSystemSID>-ascs-0`


> [AZURE.NOTE]  TUTTI gli indirizzi IP delle schede di rete e gli ILB di Azure vengono creati inizialmente come **dinamici**. È necessario modificarli in indirizzi IP **statici**, come descritto più avanti nel documento.


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Distribuzione di VM SAP con connettività di rete aziendale (cross-premise) per l'uso in produzione

Per i sistemi SAP di produzione, si distribuiranno le VM Azure con la [connettività di rete aziendale (cross-premise)][planning-guide-2.2], usando la rete VPN da sito a sito di Azure o Azure ExpressRoute.

> [AZURE.NOTE]  In questo caso, la rete virtuale di Azure e la subnet sono già state create e preparate.


Nel campo **NEWOREXISTINGSUBNET** scegliere existing (esistente).

Nel campo di testo **SUBNETID** è necessario aggiungere la stringa completa di SubnetID della rete Azure preparata, in cui si prevede di distribuire le VM di Azure.

È possibile ottenere un elenco di tutte le subnet di rete di Azure tramite questo comando PowerShell:

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


Il valore **SUBNETID** viene visualizzato nel campo ID.

Un elenco di tutti i **SUBNETID** può essere recuperato tramite il comando PowerShell seguente:

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

Il valore **SUBNETID** è simile al seguente:

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Distribuzione solo cloud delle istanze di SAP a fini di test e demo

È anche possibile distribuire il sistema SAP a disponibilità elevata nel modello di distribuzione denominato solo cloud.

Questa distribuzione è opportuna soprattutto nel caso di uso per demo, ma non nei casi di uso in produzione.

Nel campo NEWOREXISTINGSUBNET scegliere _**new**_ (nuovo). Lasciare **vuoto** il campo SUBNETID.

La rete virtuale di Azure e la subnet verranno create automaticamente dal modello di Azure Resource Manager per SAP.

> [AZURE.NOTE] Inoltre è necessario distribuire almeno una VM dedicata per AD/DNS nella stessa rete virtuale. Queste VM non vengono create dal modello.


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Rete virtuale di Azure

In questo esempio, lo spazio degli indirizzi della rete virtuale di Azure è 10.0.0.0/16. Esiste una subnet denominata _**Subnet**_, con un intervallo di indirizzi di 10.0.0.0/24. Tutte le VM e gli ILB vengono distribuiti nella rete virtuale.
  
> [AZURE.NOTE] Non apportare modifiche alle impostazioni di rete nella macchina guest, ad esempio l'indirizzo IP, i server DNS, la subnet e così via. Tutte le impostazioni di rete vengono eseguite tramite Azure e propagate tramite il servizio DHCP.

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> Indirizzi IP DNS

Assicurarsi che l'opzione **Server DNS** della rete virtuale sia impostata su **DNS personalizzato**. In caso di:

- **[Connettività di rete aziendale (cross-premise)][planning-guide-2.2]**: aggiungere gli indirizzi IP dei server DNS locali. I server DNS locali possono essere estesi alle VM in esecuzione in Azure. In questo caso, è possibile aggiungere gli indirizzi IP di queste VM di Azure che sono configurati per l'esecuzione del servizio DNS.

-	**[Distribuzione solo cloud][planning-guide-2.1]**: distribuire una VM aggiuntiva nella stessa rete virtuale, che fungerà da server DNS. Aggiungere gli indirizzi IP di queste VM di Azure che sono configurati per l'esecuzione del servizio DNS.


![Figura 10: Configurazione dei server DNS per la rete virtuale di Azure][sap-ha-guide-figure-3001]

_**Figura 10:** Configurazione dei server DNS per la rete virtuale di Azure_

> [AZURE.NOTE] Se si modificano gli indirizzi IP dei server DNS, è necessario riavviare le VM di Azure per applicare la modifica e propagare i nuovi server DNS. Nel nostro esempio, il servizio DNS viene installato e configurato nelle VM Windows seguenti



| Ruolo VM | Nome host VM | Nome scheda di rete | Indirizzo IP statico  
| ---------------|-------------|--------------------|-------------------
| 1° server DNS | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 2° server DNS | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Nomi host e indirizzi IP statici per l'istanza in cluster di SAP ASCS/SCS e l'istanza in cluster di DBMS

Condizione simile come in locale, sono necessari i seguenti nomi host riservati/indirizzi IP:

| Ruolo nome host virtuale | Nome host virtuale | Indirizzo IP statico virtuale 
| ----------------------------------------------------------------------------|------------------|---------------------------
| 1° nome host virtuale del cluster SAP ASCS/SCS (usato per la gestione del cluster) | pr1-ascs-vir | 10\.0.0.42                 
| Nome host virtuale dell'**ISTANZA** di SAP ASCS/SCS | pr1-ascs-sap | `10.0.0.43`             
| 2° nome host virtuale del cluster DBMS SAP (usato per la gestione del cluster) | pr1-dbms-vir | 10\.0.0.32                 
 

I nomi host virtuali _**pr1-ascs-vir**_ e _**pr1-dbms-vir**_ e gli indirizzi IP associati, usati per gestire il cluster stesso, vengono creati in fase di creazione del cluster, come descritto nel capitolo [Raccogliere i nodi del cluster nella configurazione del cluster][sap-ha-guide-8.12.1].

È possibile creare manualmente sul server DNS gli altri due nomi host virtuali _**pr1-sap ascs**_ e _**pr1-dbms-sap**_ e gli indirizzi IP associati usati dall'istanza di SAP ASCS/SCS in cluster e dall'istanza di DBMS in cluster, come descritto nel capitolo [Creare il nome host virtuale per SAP ASCS/SCS in cluster][sap-ha-guide-9.1.1].

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Configurare indirizzi IP statici per le VM SAP

Dopo avere distribuito le macchine virtuali per il clustering è necessario configurare gli indirizzi IP statici per tutte le VM. Questa operazione non può essere eseguita all'interno del sistema operativo guest, ma deve essere impostata nella configurazione della rete virtuale di Azure.

Un modo per eseguire questa operazione è usare il portale di Azure. Nel portale di Azure passare a:

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

Modificare il campo Assegnazione da **dinamico** a **statico** e immettere il valore desiderato per l'**indirizzo IP**.

> [AZURE.NOTE] Se si modifica l'indirizzo IP della scheda di rete, è necessario riavviare le VM di Azure per applicare la modifica.


![Figura 11: Configurazione dell'indirizzo IP statico per la scheda di rete di ogni VM][sap-ha-guide-figure-3002]

_**Figura 11:** Configurazione dell'indirizzo IP statico per la scheda di rete di ogni VM_

Ripetere questo passaggio per tutte le interfacce di rete ad esempio per tutte le VM, tra cui le VM che si desidera usare per il servizio AD/DNS.

In questo esempio esaminiamo le seguenti VM e gli indirizzi IP statici:

| Ruolo VM | Nome host VM | Nome scheda di rete | Indirizzo IP statico  
| ----------------------------------------|--------------|--------------------|-------------------
| 1° server applicazioni SAP | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 2° server applicazioni SAP | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| Ultimo server applicazioni SAP | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| 1° nodo del cluster per l'istanza di ASCS/SCS | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| 2° nodo del cluster per l'istanza di ASCS/SCS | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| 1° nodo del cluster per l'istanza di DBMS | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| 2° nodo del cluster per l'istanza di DBMS | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Configurare l'indirizzo IP statico per il bilanciamento del carico interno (ILB)

Il modello di Azure Resource Manager per SAP crea un servizio di bilanciamento del carico interno (ILB) di Azure usato per il cluster dell'istanza di SAP ASCS/SCS e per il cluster DBMS.

La distribuzione iniziale imposta l'indirizzo IP dell'ILB **dinamico**. È importante modificare l'indirizzo IP in **statico**.

In questo esempio abbiamo due ILB di Azure con gli indirizzi IP statici seguenti:

| Ruolo ILB di Azure | Nome ILB di Azure | Indirizzo IP statico 
| ---------------------------|----------------|-------------------
| ILB dell'istanza di SAP ASCS/SCS | pr1-lb-ascs | `10.0.0.43`         
| ILB del sistema DBMS SAP | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**Indirizzo IP del nome host virtuale di SAP ASCS/SCS = indirizzo IP di Azure Load Balancer di SAP ASCS/SCS pr1-lb-ascs** **Indirizzo IP del nome virtuale del sistema DBMS = indirizzo IP di Azure Load Balancer di DBMS pr1-lb-dbms**

Nell'esempio impostare l'indirizzo IP del bilanciamento del carico interno _pr1-lb-ascs_ sull'indirizzo IP del nome host virtuale dell'istanza di SAP ASCS/SCS (`10.0.0.43`)

![Figura 12: Configurazione dell'indirizzo IP statico per il bilanciamento del carico interno (ILB) per l'istanza di SAP ASCS/SCS][sap-ha-guide-figure-3003]

_**Figura 12:** Configurazione dell'indirizzo IP statico per il bilanciamento del carico interno (ILB) per l'istanza di SAP ASCS/SCS_

Allo stesso modo impostare l'indirizzo IP del bilanciamento del carico _pr1-lb-dbms_ sull'indirizzo IP del nome host virtuale dell'istanza di DBMS (nell'esempio 10.0.0.33).

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure

Per impostazione predefinita, il modello di Azure Resource Manager per SAP crea tutte le porte necessarie per:

- L'istanza di ABAP ASCS con numero di istanza predefinito **00**
- L'istanza di Java SCS con numero di istanza predefinito **01**

Durante l'installazione dell'istanza di SAP ASCS/SCS è necessario usare questi numeri di istanza predefinita di 00 e 01 per l'istanza di ABAP ASCS e/o di Java SCS.

I seguenti endpoint ILB Azure sono necessari e creati per le porte ASCS di SAP NetWeaver ABAP:


| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza ASCS con l'istanza numero 00) (ERS con 10)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Server di accodamento/_lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| Server messaggi ABAP/_lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| Messaggio ABAP interno/_lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| HTTP server messaggi/_Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| SAP Start Service ASCS HTTP/_Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| SAP Start Service ASCS HTTPS/_Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| Replica di accodamento/_Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| SAP Start Service ERS HTTP _Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| SAP Start Service ERS HTTP _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| Condivisione file _Lbrule445_ | | 445                                                                      

_**Tabella 1:** Numeri di porta delle istanze di ASCS di SAP NetWeaver ABAP_


I seguenti endpoint ILB Azure sono necessari e devono essere creati per le porte SCS di SAP NetWeaver Java:

| Servizio/nome regola di bilanciamento del carico | Numeri porte predefinite | Porte concrete per (istanza SCS con numero di istanza 01) (ERS con 11)  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| Server di accodamento/_lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| Server gateway/_lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Server messaggi Java/_lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| HTTP server messaggi/_Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| SAP Start Service SCS HTTP/_Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| SAP Start Service SCS HTTPS/_Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| Replica di accodamento/_Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| SAP Start Service ERS HTTP _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| SAP Start Service ERS HTTP _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| Condivisione file _Lbrule445_ | | 445                                                                      

_**Tabella 2:** Numeri di porta delle istanze SCS di SAP NetWeaver Java_


![Figura 13: Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure][sap-ha-guide-figure-3004]

_**Figura 13:** Regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure_

Allo stesso modo impostare l'indirizzo IP del bilanciamento del carico _**pr1-lb-dbms**_ sull'indirizzo IP del nome host virtuale dell'istanza di DBMS (nell'esempio _**10.0.0.33**_).

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Modifica delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure

Se si desidera usare altri numeri di istanza per l'istanza di SAP ASCS o SCS, è necessario aggiornare i nomi e i valori di queste porte.

Un modo per eseguire l'aggiornamento è usare il portale di Azure.

Passare a `<SID>-lb-ascs load balancer -> Load Balancing Rules`.

Per tutte le regole di bilanciamento del carico appartenenti all'istanza di SAP ASCS o SCS, modificare:

- Nome
- Port
- Porta back-end

Ad esempio, se si desidera modificare il numero di istanza predefinito ASCS da 00 a 31, è necessario eseguire le modifiche per tutte le porte elencate nella _**Tabella 1:** Numeri di porta delle istanze ASCS di SAP NetWeaver ABAP ASCS_.

Di seguito è riportato un esempio di un aggiornamento per la porta _lbrule3200_.

![Figura 14: Modifica delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure][sap-ha-guide-figure-3005]

_**Figura 14:** Modifica delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure_


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Aggiungere computer Windows al dominio

Dopo avere assegnato indirizzi IP statici alle VM, aggiungere le VM al dominio.

![Figura 15: Aggiunta di una VM a un dominio][sap-ha-guide-figure-3006]

_**Figura 15:** Aggiunta di una VM a un dominio_


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Aggiungere le voci del Registro di sistema in entrambi i nodi di cluster usati per l'istanza di SAP ASCS/SCS

Gli Azure Load Balancer, incluso il servizio di bilanciamento del carico interno di Azure, stanno chiudendo le connessioni quando queste connessioni sono inattive per un determinato periodo di tempo (timeout di inattività). D'altra parte, i processi di lavoro SAP nelle istanze delle finestre di dialogo aprono le connessioni al processo di accodamento SAP non appena deve essere inviata la prima richiesta di accodamento/rimozione dalla coda. Queste connessioni restano in genere stabilite fino al riavvio del processo di lavoro o del processo di accodamento. Se tuttavia la connessione rimane inattiva per un certo tempo, verrà chiusa da ILB di Azure. Non è un problema poiché il processo di lavoro di SAP consente di ristabilire la connessione al processo di accodamento, se non esiste più. Queste attività verranno tuttavia documentate nelle tracce per gli sviluppatori dei processi SAP creando una notevole quantità di contenuti in queste tracce senza un motivo valido. È consigliabile quindi modificare i parametri `KeepAliveTime` e `KeepAliveInterval` TCP/IP in entrambi i nodi del cluster. Le modifiche ai parametri TCP/IP devono essere combinate con i parametri del profilo SAP descritti più avanti in questo documento.

Aggiungere le seguenti voci del Registro di sistema Windows in entrambi i nodi del cluster Windows per SAP ASCS/SCS:

| Path | HKLM\\System\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nome variabile | `KeepAliveTime`                                              
| Tipo di variabile | REG\_DWORD (decimale)                                        
| Valore | 120000                                                     
| Collegamento alla documentazione | [https://technet.microsoft.com/it-IT/library/cc957549.aspx](https://technet.microsoft.com/it-IT/library/cc957549.aspx) 


_**Tabella 3:** Primo parametro TCP/IP da modificare_


| Path | HKLM\\System\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| Nome variabile | `KeepAliveInterval`                                          
| Tipo di variabile | REG\_DWORD (decimale)                                        
| Valore | 120000                                                     
| Collegamento alla documentazione | [https://technet.microsoft.com/it-IT/library/cc957548.aspx](https://technet.microsoft.com/it-IT/library/cc957548.aspx)


_**Tabella 4:** Secondo parametro TCP/IP da modificare_

**Riavviare entrambi i nodi cluster** per applicare le modifiche.

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Configurazione di Windows Server Failover Cluster per l'istanza di SAP ASCS/SCS

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Raccogliere i nodi del cluster nella configurazione del cluster

Il primo passaggio consiste nell'aggiungere la funzionalità di clustering del failover a entrambi i nodi del cluster. Questa operazione viene eseguita con la procedura guidata "**Aggiunta guidata ruoli e funzionalità**" e non dovrebbe richiedere eventuali ulteriori descrizioni.

Il secondo passaggio consiste nel configurare il cluster di failover tramite Windows Failover Cluster Manager.

In MMC di Gestione cluster di failover fare clic sull'opzione per creare il cluster e aggiungere solo il nome del primo nodo A del cluster, ad esempio _**pr1-ascs-0**_. Non aggiungere ancora il secondo nodo. Il nodo sarà aggiunto in un passaggio successivo.

![Figura 16: Primo passaggio per aggiungere una configurazione del cluster di failover: aggiungere il nome del server o della VM del primo nodo che deve essere nodo del cluster][sap-ha-guide-figure-3007]

_**Figura 16:** Primo passaggio per aggiungere una configurazione del cluster di failover: aggiungere il nome del server o della VM del primo nodo che deve essere nodo del cluster_

Nei passaggi successivi verrà chiesto il nome di rete (nome host virtuale) del cluster.

![Figura 17: Secondo passaggio per aggiungere una configurazione del cluster di failover: definire il nome del cluster][sap-ha-guide-figure-3008]

_**Figura 17:** Secondo passaggio per aggiungere una configurazione del cluster di failover: definire il nome del cluster_


Dopo avere creato il cluster viene eseguito un test di convalida del cluster

![Figura 18: Ultimo passaggio per aggiungere una configurazione del cluster di failover: eseguire il controllo della convalida del cluster][sap-ha-guide-figure-3009]

_**Figura 18:** Ultimo passaggio per aggiungere una configurazione del cluster di failover: eseguire il controllo della convalida del cluster_


![Figura 19: Ultimo passaggio per aggiungere una configurazione del cluster di failover: il controllo della convalida del cluster visualizza avvisi relativi al disco di quorum non trovato][sap-ha-guide-figure-3010]

_**Figura 19:** Ultimo passaggio per aggiungere una configurazione del cluster di failover: il controllo della convalida del cluster visualizza avvisi relativi al disco di quorum non trovato_

In questa fase è possibile ignorare eventuali avvisi sui dischi, una condivisione file di controllo verrà aggiunta successivamente insieme ai dischi condivisi SIOS. In questa fase non siamo interessati a un quorum.

![Figura 20: Vengono definite le risorse principali del cluster con l'indirizzo IP; è tuttavia necessario un nuovo indirizzo IP][sap-ha-guide-figure-3011]

_**Figura 20:** Vengono definite le risorse principali del cluster con l'indirizzo IP; è tuttavia necessario un nuovo indirizzo IP_


Poiché l'indirizzo IP del server fa riferimento a uno dei nodi della VM, non è possibile avviare il cluster. È ora necessario modificare l'indirizzo IP del servizio cluster principale.

Ad esempio, è necessario assegnare un indirizzo IP (in questo esempio _**10.0.0.42**_) per il nome host virtuale del cluster _**pr1-ascs-vir**_. Questa operazione viene eseguita tramite la pagina delle proprietà della risorsa IP del servizio cluster principale, come illustrato di seguito

![Figura 21: Usare le Proprietà per impostare l'indirizzo IP corretto][sap-ha-guide-figure-3012]

_**Figura 21:** Usare le Proprietà per impostare l'indirizzo IP corretto_


![Figura 22: Assegnare l'indirizzo IP riservato per il cluster][sap-ha-guide-figure-3013]

_**Figura 22:** Assegnare l'indirizzo IP riservato per il cluster_

Dopo avere modificato l'indirizzo IP, portare online il nome host virtuale del cluster.

![Figura 23: Servizio principale del cluster in esecuzione con l'indirizzo IP corretto][sap-ha-guide-figure-3014]

_**Figura 23:** Servizio principale del cluster in esecuzione con l'indirizzo IP corretto_

Ora che il servizio principale del cluster è attivo e in esecuzione, è possibile aggiungere il secondo nodo del cluster

![Figura 24: Aggiungere il secondo nodo del cluster][sap-ha-guide-figure-3015]

_**Figura 24:** Aggiungere il secondo nodo del cluster_

![Figura 25: Aggiungere il nome host del secondo nodo del cluster, ad esempio pr1-ascs-1][sap-ha-guide-figure-3016]

_**Figura 25:** Aggiungere il nome host del secondo nodo del cluster, ad esempio pr1-ascs-1_

![Figura 26: NON selezionare la casella di controllo.][sap-ha-guide-figure-3017]

_**Figura 26:** NON selezionare la casella di controllo._

> [AZURE.NOTE]  
Assicurarsi che la casella di controllo "Aggiungi tutte le risorse di archiviazione idonee al cluster" **NON** sia selezionata.

![Figura 27: Ignorare di nuovo l'avviso sul quorum del disco][sap-ha-guide-figure-3018]

_**Figura 27:** Ignorare di nuovo l'avviso sul quorum del disco_

È possibile ignorare gli avvisi relativi al quorum e ai dischi. La configurazione del disco di condivisione e del quorum verrà eseguita in un secondo momento, come descritto nel capitolo **[Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS][sap-ha-guide-8.12.3]**.

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Configurare la condivisione file di controllo del cluster

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Creare una condivisione file

Scegliamo una condivisione file di controllo anziché un disco quorum. Questa opzione è supportata da SIOS DataKeeper.

Nella configurazione usata per le illustrazioni in questo articolo, la condivisione file di controllo è configurata sul server AD/DNS in esecuzione in Azure ed è chiamata _**domcontr 0**_. Poiché è stata configurata una connessione VPN ad Azure tramite l'opzione da sito a sito o ExpressRoute, il servizio AD/DNS si trova in locale e di conseguenza non è adatto per l'esecuzione di una condivisione file di controllo.

> [AZURE.NOTE] Nel caso in cui il servizio AD/DNS sia in esecuzione solo in locale, non configurare la condivisione file di controllo sul sistema operativo Windows di AD/DNS in esecuzione in locale perché la latenza di rete tra i nodi del cluster in esecuzione in Azure e il servizio AD/DNS locale potrebbe essere eccessiva e causare problemi di connettività. Assicurarsi di configurare il controllo della condivisione file in una VM Windows di Azure in esecuzione vicino al nodo del cluster.

L'unità del quorum richiede almeno 1.024 MB di spazio disponibile. Il valore consigliato è 2.048 MB

Il primo passaggio consiste nell'aggiungere l'oggetto nome cluster

![Figura 28: Assegnare le autorizzazioni sulla condivisione per l'oggetto nome cluster][sap-ha-guide-figure-3019]

_**Figura 28:** Assegnare le autorizzazioni sulla condivisione per l'oggetto nome cluster_

Assicurarsi che le autorizzazioni includano la possibilità di modificare i dati nella condivisione per l'oggetto nome cluster, in questo esempio _**pr1-ascs-vir$**_. Per aggiungere l'oggetto nome cluster nell'elenco illustrato nei passaggi precedenti è necessario premere "**Aggiungi**" e quindi modificare il filtro per consentire il controllo anche per gli oggetti computer, come illustrato di seguito.

![Figura 29: Cambiare il tipo di oggetto per includere anche oggetti computer][sap-ha-guide-figure-3020]

_**Figura 29:** Cambiare il tipo di oggetto per includere anche oggetti computer_

![Figura 30: Selezionare la casella per gli oggetti computer][sap-ha-guide-figure-3021]

_**Figura 30:** Selezionare la casella per gli oggetti computer_

A questo punto immettere l'oggetto nome del cluster, come illustrato nella figura 29. Poiché il record dovrebbe essere stato a questo punto, è possibile modificare le autorizzazioni, come illustrato nella figura 28.

Il passaggio successivo consiste nell'usare la scheda "Sicurezza" della condivisione e definire le autorizzazioni più granulari per l'oggetto nome cluster.

![Figura 31: Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file][sap-ha-guide-figure-3022]

_**Figura 31:** Impostare gli attributi di sicurezza per l'oggetto nome cluster sul quorum della condivisione file_

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Configurare il quorum del controllo di condivisione file in Gestione cluster di failover

Il passaggio successivo consiste nel modificare la configurazione del cluster in un controllo di condivisione file tramite Gestione cluster di failover.

![Figura 32: Usare la "configurazione guidata di impostazione del quorum del cluster", come illustrato di seguito][sap-ha-guide-figure-3023]

_**Figura 32:** Usare la "configurazione guidata di impostazione del quorum del cluster", come illustrato di seguito_

![Figura 33: Schermata di selezione delle diverse configurazioni del quorum][sap-ha-guide-figure-3024]

_**Figura 33:** Schermata di selezione delle diverse configurazioni del quorum_

In questa selezione è necessario scegliere "_**Seleziona il quorum di controllo**_".

![Figura 34: Schermata di selezione del controllo di condivisione file][sap-ha-guide-figure-3025]

_**Figura 34:** Schermata di selezione del controllo di condivisione file_

In questo caso è necessario scegliere "_**Configura condivisione file di controllo**_".

![Figura 35: Definire il percorso della condivisione file per la condivisione di controllo][sap-ha-guide-figure-3026]

_**Figura 35:** Definire il percorso della condivisione file per la condivisione di controllo_


Immettere il percorso UNC nella condivisione di file, in questo esempio `\\domcontr-0\FSW`.

Fare clic su "Avanti" per creare un elenco di modifiche da eseguire. Selezionarle e premere di nuovo Avanti per modificare la configurazione del cluster.

![Figura 36: Schermata che mostra l'esito positivo della riconfigurazione del cluster][sap-ha-guide-figure-3027]

_**Figura 36:** Schermata che mostra l'esito positivo della riconfigurazione del cluster_

In questo ultimo passaggio, la riconfigurazione del cluster deve essere eseguita correttamente.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>Installazione di SIOS DataKeeper Cluster Edition per il disco di condivisione del cluster SAP ASCS/SCS

A questo punto disponiamo di una configurazione di lavoro di Windows Server Failover Cluster in Azure. Tuttavia, questa configurazione del cluster non include ancora una risorsa di disco condiviso. Per installare un'istanza di SAP ASCS/SCS, sono necessarie risorse di disco condiviso. Qui entra in gioco SIOS DataKeeper Cluster Edition. Azure non consente di creare risorse di disco condiviso con la funzionalità necessaria ed è quindi necessario fare affidamento, ad esempio, su SIOS DataKeeper per fornire questa funzionalità.

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Aggiungere la funzionalità Microsoft .NET Framework 3.5

L'abilitazione o l'installazione di Microsoft .NET Framework 3.5 non viene eseguita automaticamente nelle versioni più recenti di Windows Server. Tuttavia, SIOS DataKeeper richiede .NET Framework in tutti i nodi su cui verrà installato il prodotto. È quindi necessario installare .NET 3.5 su tutto il sistema operativo guest delle diverse VM.

È possibile aggiungere .NET 3.5 Framework in due modi. La prima possibilità consiste nell'usare "**Aggiungi ruoli e funzionalità**" in Windows, come illustrato di seguito:

![Figura 37: Installare .NET Framework 3.5 tramite la procedura guidata "Aggiunta guidata ruoli e funzionalità"][sap-ha-guide-figure-3028]

_**Figura 37:** Installare .NET Framework 3.5 tramite la procedura guidata "Aggiunta guidata ruoli e funzionalità"_

![Figura 38: Barra di avanzamento dell'installazione di .NET Framework 3.5 tramite "Aggiunta guidata ruoli e funzionalità"][sap-ha-guide-figure-3029]

_**Figura 38:** Barra di avanzamento dell'installazione di .NET Framework 3.5 tramite "Aggiunta guidata ruoli e funzionalità"_

La seconda possibilità per abilitare la funzionalità .NET Framework 3.5 è quella di usare lo strumento da riga di comando _**dism.exe**_. Per questo tipo di installazione è necessario che sia accessibile la directory "sxs" dei supporti di installazione di Windows. Il comando seguente deve essere eseguito in una finestra di riga di comando con privilegi elevati:

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installazione di SIOS DataKeeper

Esaminiamo l'installazione di SIOS DataKeeper Cluster Edition. L'installazione deve essere eseguita in ognuno dei due nodi del cluster. SIOS DataKeeper consente di creare un'archiviazione condivisa virtuale mediante la creazione di un mirror sincronizzato e la simulazione di una risorsa di archiviazione condivisa del cluster.

Prima di installare il software SIOS, è necessario creare un utente di dominio _**DataKeeperSvc**_.

> [AZURE.NOTE] Aggiungere questo utente _**DataKeeperSvc**_ al gruppo di **amministratori locale** su entrambi i nodi del cluster.
  
Installare il software SIOS su entrambi i nodi del cluster

![Programma di installazione SIOS][sap-ha-guide-figure-3030]

![Figura 39: Prima schermata dell'installazione di SIOS DataKeeper][sap-ha-guide-figure-3031]

_**Figura 39:** Prima schermata dell'installazione di SIOS DataKeeper_

![Figura 40: DataKeeper segnala un servizio che deve essere disabilitato][sap-ha-guide-figure-3032]

_**Figura 40:** DataKeeper segnala un servizio che deve essere disabilitato_

Quando viene visualizzata la finestra a comparsa nella figura 40, scegliere "_**Yes**_" (Sì).

![Figura 41: Selezione dell'utente per SIOS DataKeeper][sap-ha-guide-figure-3033]

_**Figura 41:** Selezione dell'utente per SIOS DataKeeper_


Nella schermata precedente è consigliabile scegliere l'_**account di dominio o di server**_.

![Figura 42: Fornire l'utente di dominio e la password per l'installazione di SIOS DataKeeper][sap-ha-guide-figure-3034]

_**Figura 42:** Fornire l'utente di dominio e la password per l'installazione di SIOS DataKeeper_

Specificare l'account di dominio creato per SIOS DataKeeper e le password dell'account.

![Figura 43: Fornire la licenza di SIOS DataKeeper][sap-ha-guide-figure-3035]

_**Figura 43:** Fornire la licenza di SIOS DataKeeper_

Installare la chiave di licenza per SIOS DataKeeper, come illustrato nella figura 43. Al termine dell'installazione verrà chiesto di **riavviare la VM**.

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Installare SIOS DataKeeper

Dopo l'installazione di SIOS DataKeeper su entrambi i nodi è necessario avviare la configurazione. L'obiettivo della configurazione è eseguire la replica di dati sincrona tra i dischi rigidi virtuali aggiuntivi collegati a ogni VM. I passaggi seguenti mostrano la configurazione su entrambi i nodi.

![Figura 44: Strumento di configurazione e gestione di DataKeeper][sap-ha-guide-figure-3036]

_**Figura 44:** Strumento di configurazione e gestione di DataKeeper_


Avviare lo strumento di configurazione e gestione di DataKeeper e selezionare il collegamento "_**Connect Server**_"(Connetti server), evidenziato in rosso nella figura riportata sopra

![Figura 45:Inserire il nome o l'indirizzo TCP/IP del primo nodo e in un secondo passaggio del secondo nodo; lo strumento di gestione dovrebbe eseguire la connessione][sap-ha-guide-figure-3037]

_**Figura 45:** Inserire il nome o l'indirizzo TCP/IP del primo nodo e in un secondo passaggio del secondo nodo; lo strumento di gestione dovrebbe eseguire la connessione_

Il passaggio successivo consiste nel creare il processo di replica tra i due nodi

![Figura 46: Creare il processo di replica][sap-ha-guide-figure-3038]

_**Figura 46:** Creare il processo di replica_

Una procedura guidata consente di eseguire il processo passo passo

![Figura 47: Definire il nome del processo di replica][sap-ha-guide-figure-3039]

_**Figura 47:** Definire il nome del processo di replica_

![Figura 48: Definire i dati di base per il nodo che deve essere il nodo di origine corrente][sap-ha-guide-figure-3040]

_**Figura 48:** Definire i dati di base per il nodo che deve essere il nodo di origine corrente_

In un primo passaggio è necessario definire il nome, l'indirizzo TCP/IP e il volume del disco del nodo di origine. Il secondo passaggio consiste nel definire il nodo di destinazione. È necessario definire il nome, l'indirizzo TCP/IP e il volume del disco del nodo di destinazione.

![Figura 49: Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente][sap-ha-guide-figure-3041]

_**Figura 49:** Definire i dati di base per il nodo che deve essere il nodo di destinazione corrente_

Il passaggio successivo consiste nel definire gli algoritmi di compressione che devono essere applicati. Per i nostri scopi è consigliabile comprimere il flusso di replica. Soprattutto in caso di risincronizzazione, la compressione del flusso di replica riduce notevolmente il tempo necessario per l'operazione. Tenere presente che la compressione usa le risorse di CPU e RAM di una VM. Maggiore è la velocità di compressione, più elevato è l'uso della CPU. È possibile regolare e cambiare questa impostazione in un secondo momento.

Un'altra impostazione da verificare è se la replica viene eseguita in modalità sincrona o asincrona. **Per proteggere le configurazioni di SAP ASCS/SCS, è necessaria impostare la replica sincrona**.

![Figura 50: Definire i dettagli della replica][sap-ha-guide-figure-3042]

_**Figura 50:** Definire i dettagli della replica_

L'ultimo passaggio consiste nel definire se il volume replicato dal processo di replica deve essere rappresentato in una configurazione del cluster WSFC come disco condiviso. Per la configurazione di SAP ASCS/SCS dobbiamo scegliere "YES" (SÌ) in modo che il cluster di Windows rappresenti il volume replicato come disco condiviso che può essere usato come volume del cluster.

![Figura 51: Premere "Yes" (Sì) per abilitare il volume replicato come volume del cluster][sap-ha-guide-figure-3043]

_**Figura 51:** Premere "Yes" (Sì) per abilitare il volume replicato come volume del cluster_

Al termine della creazione, lo strumento di gestione di DataKeeper elenca il processo di replica come attivo.

![Figura 52: Il mirroring sincrono di DataKeeper per il disco di condivisione di SAP ASCS/SCS è attivo][sap-ha-guide-figure-3044]

_**Figura 52:** Il mirroring sincrono di DataKeeper per il disco di condivisione di SAP ASCS/SCS è attivo_

Di conseguenza, il disco è ora visibile in Windows Failover Cluster Manager come disco DataKeeper, come illustrato di seguito.

![Figura 53: Il disco replicato da DataKeeper viene visualizzato in Gestione cluster di failover][sap-ha-guide-figure-3045]

_**Figura 53:** Il disco replicato da DataKeeper viene visualizzato in Gestione cluster di failover_


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installazione del sistema SAP NetWeaver

Non descriveremo l'installazione del sistema DBMS poiché le impostazioni variano in base al sistema DBMS usato. Tuttavia, presupporremo che i problemi di disponibilità elevata del sistema DBMS vengano risolti con le funzionalità supportate dai diversi fornitori di sistemi DBMS per Azure. ad esempio AlwaysOn o mirroring del database per SQL Server e Oracle Data Guard per Oracle. Nello scenario di esempio usato per questa documentazione, non abbiamo fornito una protezione aggiuntiva del sistema DBMS.

Non esistono anche particolari considerazioni relative a un sistema DBMS diverso per interagire con questa configurazione di SAP ASCS/SCS in cluster in Azure.

> [AZURE.NOTE]  
La procedura di installazione dei sistemi SAP NetWeaver ABAP, dei sistemi Java e dei sistemi ABAP + Java è praticamente identica. La differenza principale è che un sistema SAP ABAP ha un'istanza di ASCS. Il sistema SAP Java ha un'istanza SCS e il sistema SAP ABAP + Java ha un'istanza ASCS e un'istanza SCS in esecuzione nello stesso gruppo di cluster di failover Microsoft. Eventuali differenze di installazione per ogni stack di installazione di SAP NetWeaver verranno indicate in modo esplicito. Si presume che tutte le altre parti siano uguali.

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installazione di SAP con un'istanza di ASCS/SCS a disponibilità elevata

> [AZURE.NOTE]  
NON inserire il file di paging sui volumi con mirroring DataKeeper perché non è supportato da DataKeeper. È possibile lasciare il file di paging nell'unità D:\\ temporanea di una VM di Azure in cui si trova già quando si distribuisce una VM in Azure. Se non è questo il caso, correggere questa situazione e inserire il file di paging di Windows nell'unità D:\\ della VM di Azure.

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Creare il nome host virtuale per SAP ASCS/SCS in cluster

Un primo passaggio consiste nel creare la voce DNS necessaria per il nome host virtuale dell'istanza di ASCS/SCS. Lo strumento usato per eseguire questa operazione è il Gestore DNS di Windows. Oltre al nome host virtuale, è necessario definire l'indirizzo IP assegnato al nome host virtuale.

> [AZURE.NOTE]  
**Tenere presente che l'indirizzo IP che assegniamo al nome host virtuale dell'istanza di ASCS/SCS deve essere lo stesso indirizzo IP che abbiamo assegnato ad Azure Load Balancer (`<sid>-lb-ascs`) Indirizzo IP del nome host virtuale di SAP ASCS/SCS `(pr1-ascs-sap)` = indirizzo IP di Azure Load Balancer `(pr1-lb-ascs)`**

Questo significa anche che solo un ruolo di cluster di failover di SAP, ad esempio per il sistema ABAP un'istanza di ASCS, per il sistema Java un'istanza di SCS e per ABAP + Java un'istanza di ASCS e un'istanza di SCS, può essere eseguito in un Windows Server Failover Cluster in Azure.

> [AZURE.NOTE] Il clustering con più SID descritto nella Guida all'installazione di SAP (vedere [Guide all'installazione di SAP][sap-installation-guides]) attualmente non funziona in Azure.

![Figura 54: Definizione della voce DNS per il nome virtuale e dell'indirizzo IP/TCP del cluster SAP ASCS/SCS][sap-ha-guide-figure-3046]

_**Figura 54:** Definizione della voce DNS per il nome virtuale e dell'indirizzo IP/TCP del cluster SAP ASCS/SCS_

La voce viene mostrata in Gestore DNS all'interno del dominio, come illustrato nella figura seguente.

![Figura 55: Nuovo nome virtuale e indirizzo TCP/IP elencati per la configurazione del cluster di SAP ASCS/SCS][sap-ha-guide-figure-3047]

_**Figura 55:** Nuovo nome virtuale e indirizzo TCP/IP elencati per la configurazione del cluster di SAP ASCS/SCS_

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installare il primo nodo del cluster SAP

L'installazione del primo nodo del cluster ASCS/SCS è identica alla procedura illustrata nella documentazione sull'installazione di SAP:
- Eseguire l'opzione del primo nodo del cluster nel nodo A, ad esempio sull'host _**pr1-ascs-0**_.

Se si desidera mantenere le porte predefinite per il servizio di bilanciamento del carico interno di Azure, scegliere:

- Per il **sistema ABAP**: numero di istanza **ASCS** **00**
- Per il **sistema Java**: **SCS** numero di istanza **01**
- Per il **sistema ABAP + JAVA**: numero di istanza **ASCS** **00** e numero di istanza **SCS** **01**

Se si desidera usare altri numeri di istanza rispetto a 00 per l'istanza di ABAP ASCS e rispetto a 01 per l'istanza di Java SCS, è necessario prima modificare le regole di bilanciamento del carico predefinito (ILB) di Azure, come descritto in: **[Modifica delle regole di bilanciamento del carico predefinite di ASCS/SCS per il servizio di bilanciamento del carico interno (ILB) di Azure][sap-ha-guide-8.9]**.

Al termine di questo passaggio, è necessario eseguire alcuni passaggi che non sono descritti nella documentazione di installazione di SAP comune.

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Modificare il profilo SAP dell'istanza di ASCS/SCS

È necessario aggiungere un nuovo parametro del profilo. Questo parametro impedisce la chiusura delle connessioni tra i processi di lavoro SAP e il server di accodamento quando sono inattivi per un tempo eccessivo. Abbiamo esaminato lo scenario dei problemi nel capitolo **[Aggiungere le voci del Registro di sistema in entrambi i nodi del cluster usati per l'istanza di SAP ASCS/SCS][sap-ha-guide-8.11]** di questo documento. In quella sezione abbiamo introdotto anche due modifiche ad alcuni parametri della connessione TCP/IP di base. Nel secondo passaggio è necessario configurare il server di accodamento in modo da inviare un segnale **keep\_alive** per impedire alle connessioni di raggiungere la soglia di inattività dell'ILB di Azure. A questo scopo, aggiungere questo parametro:

```
enque/encni/set_so_keepalive = true
```

al profilo dell'istanza di SAP ASCS/SCS. Nell'esempio il percorso è:

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

ad esempio, al profilo dell'istanza di SAP SCS e il percorso corrispondente

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Aggiungere la porta probe

Per un corretto funzionamento della configurazione completa del cluster con un Azure Load Balancer, è necessario usare la funzionalità probe del servizio di bilanciamento del carico interno. In genere, un servizio di bilanciamento del carico interno di Azure bilancia e distribuisce il carico di lavoro in ingresso in modo uniforme tra le macchine virtuali coinvolte. Tuttavia, questo meccanismo non funziona in una configurazione di cluster poiché solo una delle istanze è attiva e l'altra è passiva e non può accettare il carico di lavoro. Per abilitare le configurazioni in cui il servizio di bilanciamento del carico interno di Azure assegna il lavoro solo all'istanza o alle istanze attive, è stata definita una funzionalità probe. Tramite questa funzionalità, il bilanciamento del carico interno ha la possibilità di controllare le istanze che sono attive e successivamente usare come destinazione solo questa istanza con il carico di lavoro. Prima controlliamo l'impostazione _**ProbePort**_ corrente con questo comando PowerShell eseguito all'interno di una delle VM coinvolte nella configurazione del cluster:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 56: La configurazione della porta probe è 0 per impostazione predefinita][sap-ha-guide-figure-3048]

_**Figura 56:** La configurazione della porta probe è 0 per impostazione predefinita_

Per impostazione predefinita, il numero della porta probe è impostato su 0. Per un corretto funzionamento della configurazione, è necessario definire una porta. In questo caso è necessario usare la porta probe _**62300**_ perché questo numero di porta è definito nei modelli di modello di Azure Resource Manager per SAP. Questo numero di porta può essere assegnato con i due comandi seguenti:

Ottenere prima la risorsa cluster con nome host virtuale SAP _**SAP WAC IP**_

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

Impostare quindi la porta probe su 62300

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

È necessario arrestare e avviare il ruolo di cluster _**SAP PR1**_ per attivare le modifiche.

Dopo avere portato online il ruolo di cluster _**SAP PR1**_, verificare che _**ProbePort**_ sia impostata sul nuovo valore:

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![Figura 57: Porta probe del cluster dopo la modifica][sap-ha-guide-figure-3049]

_**Figura 57:** Porta probe del cluster dopo la modifica_

Il valore _**ProbePort**_ è ora impostato su _**62300**_. Ora è possibile accedere alla condivisione file _**\\\ascsha-clsap\\sapmnt**_ da altri host come _**ascsha DBA**_.

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installazione dell'istanza di database

L'installazione dell'istanza di database è identica alla procedura descritta nella documentazione sull'installazione di SAP. Per questo motivo non è stata documentata di seguito.

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installazione del secondo nodo del cluster

Anche in questo caso, l'installazione del secondo nodo del secondo non differisce dalla procedura illustrata nella documentazione sull'installazione di SAP. Seguire quindi i passaggi descritti nella Guida all'installazione per installare il secondo nodo del cluster.

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Cambiare il tipo di avvio del servizio Windows dell'istanza di SAP ERS

Cambiare il tipo di avvio dei servizi Windows SAP ERS per _**(avvio ritardato) automatico**_ in entrambi i nodi del cluster.

![Figura 58: Cambiare il tipo di servizio per l'istanza di SAP ERS su ritardo automatico][sap-ha-guide-figure-3050]

_**Figura 58:** Cambiare il tipo di servizio per l'istanza di SAP ERS su ritardo automatico_

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installazione di Primary Application Server (PAS) SAP

Eseguire l'istallazione `<sid>-di-0` dell'istanza di Primary Application Server nella VM designata per l'hosting di PAS. Non sono presenti dipendenze a specifiche di Azure o DataKeeper.

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installazione di Additional Application Server (AAS) SAP

Eseguire l'installazione di un SAP Application Server aggiuntivo in tutte le VM designate per l'hosting di un SAP Application Server, ad esempio su `<sid>-di-1` fino a `<sid>-di-<n>`.

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Test del failover e della replica SIOS dell'istanza di SAP ASCS/SCS

È possibile testare e monitorare con facilità un failover dell'istanza di SAP ASCS/SCS e una replica del disco SIOS usando _**Gestione cluster di failover**_ e l'interfaccia utente di SIOS DataKeeper.

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Punto iniziale: istanza di SAP ASCS/SCS in esecuzione nel nodo A del cluster

Il gruppo di cluster _**SAP WAC**_ è in esecuzione sul nodo A del cluster, ad esempio su _**ascsha-clna**_. Il disco condiviso S:, appartenente al gruppo di cluster _**SAP WAC**_ e usato dall'istanza di ASCS/SCS, viene assegnato al nodo A del cluster.

![Figura 59: Gestione cluster di failover: il gruppo di cluster <SID> SAP è in esecuzione nel nodo A del cluster][sap-ha-guide-figure-5000]

_**Figura 59:** Gestione cluster di failover: il gruppo di cluster <SID> SAP è in esecuzione nel nodo A del cluster_

Tramite l'interfaccia utente di SIOS DataKeeper è possibile vedere che i dati dei dischi condivisi vengano replicati in modo sincrono dal volume S: di origine del nodo A del cluster, ad esempio _**ascsha-clna [10.0.0.41]**_, nel volume _**S:**_ di destinazione del nodo B del cluster, ad esempio _**ascsha-clnb [10.0.0.42]**_

![Figura 60: SIOS DataKeeper: replica del volume locale dal nodo A al nodo B del cluster][sap-ha-guide-figure-5001]

_**Figura 60:** SIOS DataKeeper: replica del volume locale dal nodo A al nodo B del cluster_


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Processo di failover dal nodo A al nodo B

È possibile avviare un failover del gruppo di cluster <SID> SAP dal nodo A al nodo B del cluster:

- tramite Gestione cluster di failover

- tramite PowerShell per Clustering di failover

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- riavviando il nodo A del cluster nel sistema operativo guest di Windows. Verrà quindi avviato un failover automatico del gruppo di cluster <SID> SAP dal nodo A al nodo B.

- riavviando il nodo A del cluster dal portale di Azure. Verrà quindi avviato un failover automatico del gruppo di cluster <SID> SAP dal nodo A al nodo B.

- riavviando il nodo A del cluster tramite Azure PowerShell. Verrà avviato un failover automatico del gruppo di cluster <SID> SAP dal nodo A al nodo B.

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> Punto finale: istanza di SAP ASCS/SCS in esecuzione nel nodo B del cluster

Dopo il failover il gruppo di cluster `SAP <SID>` è in esecuzione sul nodo di B del cluster, ad esempio _**ascsha clnb**_.

![Figura 61: Gestione cluster di failover: il gruppo di cluster <SID> SAP è in esecuzione nel nodo B][sap-ha-guide-figure-5002]

_**Figura 61:** Gestione cluster di failover: il gruppo di cluster <SID> SAP è in esecuzione nel nodo B_

Il disco condiviso è ora montato sul nodo B del cluster. SIOS DataKeeper sta replicando i dati dal volume S: di origine del nodo B del cluster (ad esempio _**ascsha-clnb [10.0.0.42]**_) nel volume S: di destinazione del nodo A del cluster (ad esempio _**ascsha-clna [10.0.0.41]**_).

![Figura 62: SIOS DataKeeper: replica del volume locale dal nodo B al nodo A del cluster][sap-ha-guide-figure-5003]

_**Figura 62:** SIOS DataKeeper: replica del volume locale dal nodo B al nodo A del cluster_

<!---HONumber=AcomDC_0907_2016-->

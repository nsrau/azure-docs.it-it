---
title: Servizi disponibili in Azure per enti pubblici | Documentazione Microsoft
description: Presenta una panoramica dei servizi disponibili in Azure per enti pubblici
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: ffe3991f68bbd255ff6f4ffedbd6638f32897d1a
ms.openlocfilehash: 914870f59b488716bb283162078535849f732018
ms.lasthandoff: 02/21/2017


---
# <a name="available-services-on-azure-government"></a>Servizi disponibili in Azure per enti pubblici
Azure per enti pubblici amplia continuamente i servizi disponibili.  Questi servizi vengono distribuiti tramite lo stesso codice usato in Azure pubblico.  Questa sezione illustra i servizi attualmente disponibili in Azure per enti pubblici, inclusi due tipi principali di informazioni:

* **Variazioni:** variazioni dovute a funzionalità non ancora distribuite o a proprietà (come gli URL) univoche per l'ambiente per enti pubblici.  
* **Considerazioni:** dettagli dell'implementazione specifici di Azure per enti pubblici per garantire che i dati siano sempre conformi.

Qualsiasi informazione necessaria su questi servizi è inclusa nella documentazione generale dei servizi.

Per l'elenco più aggiornato dei servizi, vedere il [Prodotti in base all'area](https://azure.microsoft.com/regions/services/). 

Nelle tabelle seguenti i servizi specificati come abilitati per Resource Manager dispongono di provider di risorse e possono essere gestiti tramite PowerShell. Per altre informazioni sui provider, le versioni API e gli schemi di Resource Manager, vedere [qui](../azure-resource-manager/resource-manager-supported-services.md). I servizi descritti come disponibili nel portale possono essere gestiti nel [portale di Azure per enti pubblici](https://portal.azure.us/). 


## <a name="computedocumentation-government-computemd"></a>[Calcolo](documentation-government-compute.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [Macchine virtuali](documentation-government-compute.md#virtual-machines) | Sì | Sì |
| Batch | Sì | Sì |
| Servizi cloud | Sì | Sì |
| Service Fabric | Sì | Sì |
| Set di scalabilità delle VM | Sì | Sì |


## <a name="networkingdocumentation-government-networkingmd"></a>[Rete](documentation-government-networking.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | Sì | Sì |
| Rete virtuale | Sì | Sì |
| [Bilanciamento del carico](documentation-government-networking.md#support-for-load-balancer) | Sì | Sì |
| [Gestione traffico](documentation-government-networking.md#support-for-traffic-manger) | Sì | Sì |
| [Gateway VPN](documentation-government-networking.md#support-for-vpn-gateway) | Sì | Sì |
| gateway applicazione | Sì | Sì |
| ExpressRoute | Sì | Sì |



## <a name="storagedocumentation-government-services-storagemd"></a>[Archiviazione](documentation-government-services-storage.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [Archiviazione - BLOB](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Archiviazione - Tabelle](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Archiviazione - Code](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Archiviazione - File](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Archiviazione - Dischi](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [StorSimple](documentation-government-services-storage.md) | Sì | Sì |
| [Backup](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Site Recovery](documentation-government-services-storage.md#azure-storage) | Sì | Sì |
| [Importazione/Esportazione](documentation-government-services-storage.md#azure-storage) | Sì | No |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web e dispositivi mobili](documentation-government-services-webandmobile.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [Servizio app - App Web](documentation-government-services-webandmobile.md#app-services) | Sì | Sì |
| [Servizio app - App per le API](documentation-government-services-webandmobile.md#app-services) | Sì | Sì |
| [Servizio app - App per dispositivi mobili](documentation-government-services-webandmobile.md#app-services) | Sì | Sì |
| Servizi multimediali | Sì | Sì |


## <a name="databasesdocumentation-government-services-databasemd"></a>[Database](documentation-government-services-database.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [Database SQL](documentation-government-services-database.md#sql-database) | Sì | Sì |
| SQL Data Warehouse | Sì | Sì |
| Estensione database di SQL Server | Sì | Sì |
| [Cache Redis](documentation-government-services-database.md#azure-redis-cache) | Sì | Sì |




## <a name="internet-of-things-iot"></a>Internet delle cose

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| Hub eventi | Sì | Sì |
| Hub di notifica | No | No (accedere al [portale legacy](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>Integrazione aziendale

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| Bus di servizio | Sì | Sì |
| [StorSimple](documentation-government-services-storage.md) | Sì | Sì |
| Estensione database di SQL Server | Sì | Sì |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[Sicurezza e identità](documentation-government-services-securityandidentity.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| Azure Active Directory | Sì | Sì |
| [Insieme di credenziali delle chiavi](documentation-government-services-securityandidentity.md#key-vault) | Sì | No (disponibile a breve) |
| Multi-Factor Authentication | Sì | Sì |
| InTune | Sì | No |


## <a name="intelligence--analytics"></a>Intelligence e analisi

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| Power BI | Sì | No |
| HDInsight | Sì | Sì |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[Monitoraggio e gestione](documentation-government-services-monitoringandmanagement.md)

| Service | Gestione risorse abilitato | di Microsoft Azure |
| --- | --- | --- |
| [Automazione](documentation-government-services-monitoringandmanagement.md#automation) | Sì | Sì |
| [Backup](documentation-government-services-backup.md) | Sì | Sì |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | Sì | Sì |
| [Site Recovery](documentation-government-services-monitoringandmanagement.md#site-recovery) | Sì | Sì |
| Utilità di pianificazione | Sì | No |
| Monitoraggio e diagnostica | Sì | Sì |




## <a name="next-steps"></a>Passaggi successivi
Per altri aggiornamenti e informazioni, iscriversi al [blog di Microsoft Azure per enti pubblici](https://blogs.msdn.microsoft.com/azuregov/).



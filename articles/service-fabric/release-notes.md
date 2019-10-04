---
title: Versioni di Azure Service Fabric
description: Note sulla versione per le funzionalità e i miglioramenti più recenti in Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 984e7ed82ba81d8c40688efd0a0a06e095be7c06
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018977"
---
# <a name="service-fabric-releases"></a>Versioni Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guida</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">alla risoluzione dei problemi</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">delleOpzioni</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">supporto</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank"> per la gestione delle versioni supportate esempi di codice</a>

Questo articolo fornisce altre informazioni sulle versioni più recenti e sugli aggiornamenti per il runtime di Service Fabric e gli SDK.

## <a name="whats-new-in-service-fabric"></a>Novità di Service Fabric

### <a name="service-fabric-65"></a>Service Fabric 6,5

La versione più recente Service Fabric include miglioramenti a supporto, affidabilità e prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita delle applicazioni e dei cluster.

> [!IMPORTANT]
> Service Fabric 6,5 è la versione finale con supporto degli strumenti Service Fabric in Visual Studio 2015. Si consiglia ai clienti di passare a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Ecco le novità di Service Fabric 6,5:

- Service Fabric Explorer include un [visualizzatore archivio immagini](service-fabric-visualizing-your-cluster.md#image-store-viewer) per esaminare le applicazioni caricate nell'archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include numerosi miglioramenti di diagnostica automatica. I clienti di POA sono consigliati per passare a questa versione.

- Il [servizio EventStore è abilitato per impostazione predefinita](service-fabric-visualizing-your-cluster.md#event-store) per Service Fabric cluster 6,5, a meno che non sia stato rifiutato.

- Sono stati aggiunti [eventi del ciclo](service-fabric-diagnostics-event-generation-operational.md#replica-events) di vita della replica per i servizi con stato.

- [Migliore visibilità dello stato del nodo di inizializzazione](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusi gli avvisi a livello di cluster se un nodo di inizializzazione non è integro (inattivo, *rimosso* o *sconosciuto*).

- [Service Fabric strumento di ripristino di emergenza dell'applicazione](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente Service Fabric servizi con stato di eseguire rapidamente il ripristino quando il cluster primario rileva un'emergenza. I dati del cluster primario vengono continuamente sincronizzati nell'applicazione di standby secondaria mediante backup e ripristino periodici.

- Supporto di Visual Studio per la [pubblicazione di app .NET Core in cluster basati su Linux](service-fabric-how-to-publish-linux-app-vs.md).

- L'interfaccia della riga di comando di [Azure Service Fabric (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) verrà installata automaticamente per Service Fabric 6,5 (e versioni successive) quando si aggiorna o si crea un nuovo cluster Linux in Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) viene installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per ulteriori informazioni, vedere le [Note sulla versione di Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

### <a name="service-fabric-65-releases"></a>Versioni di Service Fabric 6,5

| Data di rilascio | Release | Altre informazioni |
|---|---|---|
| 11 giugno 2019 | [Azure Service Fabric 6,5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 23 agosto 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |

## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-64-releases"></a>Versioni di Service Fabric 6,4

| Data di rilascio | Release | Altre informazioni |
|---|---|---|
| 30 novembre, 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Versione di aggiornamento di Azure Service Fabric 6,4 per i cluster Windows](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 febbraio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marzo 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

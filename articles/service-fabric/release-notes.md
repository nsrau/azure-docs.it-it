---
title: Versioni di Service Fabric Azure
description: Note sulla versione per le ultime funzionalità e miglioramenti in Service Fabric.
author: athinanthny
manager: chackdan
ms.author: atsenthi
ms.date: 6/10/2019
ms.topic: conceptual
ms.service: service-fabric
hide_comments: true
hideEdit: true
ms.openlocfilehash: 78fb96cae3d3d128da608183f37771b2ecf66dcf
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165350"
---
# <a name="service-fabric-releases"></a>Versioni di Service Fabric

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Risoluzione dei problemi di Guide</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">gestione dei problemi</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">opzioni di supporto</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">versioni supportate</a>  
|  <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Esempi di codice</a>

Questo articolo fornisce altre informazioni sulle versioni più recenti e aggiornamenti per gli SDK e runtime di Service Fabric.

## <a name="whats-new-in-service-fabric"></a>**Novità in Service Fabric**

### <a name="service-fabric-65"></a>Service Fabric 6.5

La versione più recente di Service Fabric include la supportabilità, l'affidabilità e miglioramenti delle prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita del cluster e dell'applicazione.

> [!IMPORTANT]
> Service Fabric 6.5 è la versione finale di Service Fabric supportano strumenti in Visual Studio 2015. I clienti sono invitati a cui spostarsi [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Ecco che cosa sono le novità di Service Fabric 6.5:

- Service Fabric Explorer include un' [Image Store Viewer](service-fabric-visualizing-your-cluster.md#image-store-viewer) per il controllo delle applicazioni sono stati caricati su archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include numerosi miglioramenti di diagnostica automatico. I clienti di POA si consiglia di passare a questa versione.

- [EventStore Service è abilitato per impostazione predefinita](service-fabric-visualizing-your-cluster.md#event-store) per i cluster di Service Fabric 6.5 a meno che non si sono esclusi.

- Aggiunti [eventi del ciclo di vita della replica](service-fabric-diagnostics-event-generation-operational.md#replica-events) per i servizi con stato.

- [Migliore visibilità dello stato nodo di valore di inizializzazione](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusi gli avvisi a livello di cluster se un nodo di valore di inizializzazione non è integro (*verso il basso*, *rimosso* oppure *sconosciuto*).

- [Lo strumento Service Fabric Application Disaster Recovery](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente ai servizi con stati di Service Fabric per un rapido ripristino quando il cluster primario rileva una situazione di emergenza. I dati dal cluster primario viene sincronizzati in modo continuo nell'applicazione di standby secondario utilizzando backup periodico e il ripristino.

- Supporto di Visual Studio per [pubblicazione di App .NET Core per cluster basati su Linux](service-fabric-how-to-publish-linux-app-vs.md).

- [Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) verrà installato automaticamente per Service Fabric 6.5 e versioni successive, quando si aggiorna o si crea un nuovo cluster di Linux in Azure.

- [SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) viene installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per altre informazioni, vedere la [note sulla versione di Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-64-releases"></a>Versioni di Service Fabric 6.4

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Cluster di Service Fabric 6.4 Aggiorna versione per Windows Azure](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 febbraio 2019 | [Azure Service Fabric 6.4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marzo 2019 | [Azure Service Fabric 6.4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Azure Service Fabric 6.4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maggio 2019 | [Azure Service Fabric 6.4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maggio 2019 | [Azure Service Fabric 6.4 aggiornamento versione](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)

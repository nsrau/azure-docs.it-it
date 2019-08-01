---
title: Aggiornare un cluster autonomo di Azure Service Fabric | Microsoft Docs
description: Informazioni sull'aggiornamento della versione o della configurazione di un cluster autonomo di Service Fabric.  T
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: atsenthi
ms.openlocfilehash: bf99d5d59354745508d8ca88abfc4b42fe608025
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599796"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Aggiornamento di un cluster autonomo di Service Fabric

Per i sistemi attuali la progettazione a livello di aggiornamento è fondamentale per il successo a lungo termine di un prodotto. Un cluster autonomo di Azure Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive ciò che può essere aggiornato.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllo della versione di Fabric eseguita nel cluster
Verificare che il cluster esegua sempre una [versione di Service Fabric supportata](service-fabric-versions.md). Quando Microsoft annuncia il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni dalla data dell'annuncio. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). A questo punto è possibile scegliere la nuova versione.

È possibile impostare il cluster in modo che riceva gli aggiornamenti automatici di Fabric man mano che vengono rilasciati da Microsoft. In alternativa, è possibile selezionare manualmente una versione di Fabric supportata da eseguire nel cluster. Per altre informazioni, vedere [Aggiornare la versione di Service Fabric eseguita nel cluster](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Personalizzare le impostazioni di configurazione

Nel file *ClusterConfig.json* è possibile impostare molte [ impostazioni di configurazione](service-fabric-cluster-manifest.md) diverse, ad esempio il livello di affidabilità del cluster e le proprietà dei nodi.  Per altre informazioni, vedere [Aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md).  È possibile personalizzare anche molte altre impostazioni più avanzate.  Per altre informazioni, vedere [Impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definire le proprietà dei nodi
È talvolta necessario assicurarsi che carichi di lavoro specifici vengano eseguiti solo in tipi di nodo specifici nel cluster. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Per ogni tipo di nodo in un cluster è possibile aggiungere proprietà personalizzate ai nodi corrispondenti. I vincoli di posizionamento sono le istruzioni collegate ai singoli servizi che selezionano una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti.

Per informazioni dettagliate sull'uso e sulla definizione dei vincoli di posizionamento e delle proprietà dei nodi, vedere [Proprietà dei nodi e vincoli di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Aggiungere metriche di capacità
Per ogni tipo di nodo è possibile aggiungere metriche di capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche di capacità per la segnalazione del carico, vedere i documenti di Gestione risorse del cluster di Service Fabric relativi a [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carico](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Patch del sistema operativo nei nodi del cluster
Patch Orchestration Application (POA) è un'applicazione Service Fabric che automatizza l'applicazione di patch nei sistemi operativi in un cluster di Service Fabric senza tempi di inattività. È possibile eseguire la distribuzione di [Patch Orchestration Application per Windows](service-fabric-patch-orchestration-application.md) nel cluster per installare patch in modo orchestrato, mantenendo i servizi sempre disponibili. 


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come personalizzare alcune [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md)
* Informazioni su come eseguire [aggiornamenti dell'applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG

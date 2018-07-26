---
title: Opzioni di archiviazione dello stato su Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sull'archiviazione affidabile dello stato nelle applicazioni mesh Service Fabric eseguite su Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 6aa268cf56bfb8be9c27a9e0d9e5c9f4464b0c9d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075938"
---
# <a name="state-management-with-service-fabric"></a>Gestione dello stato con Service Fabric
Service Fabric supporta molte opzioni diverse per l'archiviazione dello stato. Per una panoramica concettuale dei modelli di gestione dello stato e Service Fabric, vedere [concetti relativi a Service Fabric: Stato del servizio](/azure/service-fabric/service-fabric-concepts-state). Tutti questi stessi concetti si applicano se i servizi vengono eseguiti all'interno o all'esterno di mesh Service Fabric. 

## <a name="state-storage-options-in-azure-service-fabric-mesh"></a>Opzioni di archiviazione dello stato su Azure Service Fabric Mesh
Con mesh Service Fabric puoi distribuire facilmente una nuova applicazione e connetterla a un archivio dati esistente ospitato in Azure. Oltre a usare qualsiasi database remoto, sono disponibili diverse opzioni per l'archiviazione dei dati, a seconda che il servizio richieda l'archiviazione locale o remota. 

* Dati replicati archiviati a livello locale
  * Reliable Collections (non disponibile in anteprima)
    * Una libreria che implementa le strutture di dati quali le code e coppie chiave-valore da usare nel servizio
    * Fornisce il modo più semplice e veloce per interagire con i dati, offrendo allo stesso tempo un facile instradamento delle partizioni in combinazione con l'Intelligent Routing in mesh Service Fabric
  * Driver di volume di Service Fabric (non disponibile in anteprima)
    * Un driver di volume Docker per montare un volume locale su un contenitore
    * Offre la massima flessibilità nell'archiviazione dei dati in locale, tramite qualsiasi API, che supporta l'archiviazione dei file.

* Archiviazione remota
  * Driver di volume di File di Azure
    * Un driver di volume Docker per montare una condivisione di File di Azure su un contenitore
    * Offre un'opzione di dati meno performante, ma anche più economica, flessibile e affidabile, tramite qualsiasi API, che supporta l'archiviazione dei file.
    * [Guida pratica: distribuire un'app con il volume dei File di Azure](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)
    
## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul modello dell'applicazione, consultare [risorse di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

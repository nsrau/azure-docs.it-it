---
title: Opzioni di archiviazione dello stato su Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sull'archiviazione affidabile dello stato nelle applicazioni mesh Service Fabric eseguite su Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810521"
---
# <a name="state-management-with-service-fabric"></a>Gestione dello stato con Service Fabric

Service Fabric supporta molte opzioni diverse per l'archiviazione dello stato. Per una panoramica concettuale dei modelli di gestione dello stato e Service Fabric, vedere [concetti relativi a Service Fabric: Stato del servizio](/azure/service-fabric/service-fabric-concepts-state). Tutti questi stessi concetti si applicano se i servizi vengono eseguiti all'interno o all'esterno di mesh Service Fabric. 

Con mesh Service Fabric puoi distribuire facilmente una nuova applicazione e connetterla a un archivio dati esistente ospitato in Azure. Oltre a usare qualsiasi database remoto, sono disponibili diverse opzioni per l'archiviazione dei dati, a seconda che il servizio richieda l'archiviazione locale o remota. 

## <a name="volumes"></a>Volumi

I contenitori spesso usano dischi temporanei. Si tratta di supporti temporanei, quindi si riceve un nuovo disco temporaneo e si perdono i dati in caso di arresto anomalo di un contenitore. È anche difficile condividere le informazioni sui dischi temporanei con altri contenitori. I volumi sono le directory che vengono montate all'interno di istanze del contenitore che è possibile usare per mantenere la persistenza dello stato. I volumi offrono una risorsa di archiviazione file per utilizzo generico e consentono di leggere e scrivere i file usando le API di file di I/O del disco. La risorsa Volume spiega come montare una directory e quale risorsa di archiviazione di backup usare. È possibile scegliere Archiviazione file di Azure o un disco volume di Service Fabric per archiviare i dati.

![Volumi][image3]

### <a name="service-fabric-reliable-volume"></a>Reliable Volume di Service Fabric

Reliable Volume di Service Fabric è un driver di volume Docker usato per montare un volume locale in un contenitore. Lettura e scrittura sono operazioni locali e veloci. I dati vengono replicati in nodi secondari e sono quindi estremamente disponibili. Anche il failover è veloce. Quando un contenitore si arresta in modo anomalo, esegue il failover in un nodo che contiene già una copia dei dati. Per un esempio, vedere [How to deploy an app with Service Fabric Reliable Volume](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md) (Come distribuire un'app con Reliable Volume di Service Fabric).

### <a name="azure-files-volume"></a>Volume di File di Azure

Il volume di File di Azure è un driver di volume Docker usato per montare una condivisione di File di Azure in un contenitore. La risorsa di archiviazione di File di Azure usa l'archiviazione di rete, quindi le operazioni di lettura e scrittura vengono eseguite in rete. Rispetto a Reliable Volume di Service Fabric, l'archiviazione di File di Azure è meno efficiente, ma offre un'opzione più economica e completamente affidabile per i dati. Per un esempio, vedere [Archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh tramite il montaggio di un volume basato su File di Azure all'interno del contenitore](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul modello dell'applicazione, consultare [risorse di Service Fabric](service-fabric-mesh-service-fabric-resources.md)

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png

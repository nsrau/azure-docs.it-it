---
title: INTERFACCIA della riga di comando di Azure Service Fabric-sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi e sottogruppi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fc317345155a6807a20d342e2cefd0701b20f180
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257054"
---
# <a name="sfctl"></a>sfctl
Comandi per la gestione di cluster ed entità di Service Fabric. Questa versione è compatibile con il runtime di Service Fabric 7,0.

I comandi seguono il modello sostantivo-verbo. Per altre informazioni, vedere Sottogruppi.

## <a name="subgroups"></a>Sottogruppi
|Sottogruppo|Descrizione|
| --- | --- |
| [applicazione](service-fabric-sfctl-application.md) | Consente di creare, eliminare e gestire le applicazioni e i tipi di applicazioni. |
| [chaos](service-fabric-sfctl-chaos.md) | Consente di avviare, arrestare e creare report nel servizio di test chaos. |
| [cluster](service-fabric-sfctl-cluster.md) | Selezionare, gestire e usare dei cluster di Service Fabric. |
| [compose](service-fabric-sfctl-compose.md) | Creare, eliminare e gestire applicazioni Docker Compose. |
| [container](service-fabric-sfctl-container.md) | Esegue i comandi relativi al contenitore in un nodo del cluster. |
| [eventi](service-fabric-sfctl-events.md) | Recuperare gli eventi dall'archivio eventi, se il servizio EventStore è già installato. |
| [is](service-fabric-sfctl-is.md) | Consente di eseguire query e inviare comandi al servizio di infrastruttura. |
| [mesh](service-fabric-sfctl-mesh.md) | Consente di eliminare e gestire le applicazioni mesh Service Fabric. |
| [nodo](service-fabric-sfctl-node.md) | Consente di gestire i nodi che formano un cluster. |
| [partizione](service-fabric-sfctl-partition.md) | Consente di eseguire una query e gestire partizioni per qualsiasi servizio. |
| [property](service-fabric-sfctl-property.md) | Consente di archiviare proprietà ed eseguire query su di esse con i nomi di Service Fabric. |
| [replica](service-fabric-sfctl-replica.md) | Consente di gestire le repliche che appartengono alle partizioni del servizio. |
| [RPM](service-fabric-sfctl-rpm.md) | Consente di eseguire query e inviare comandi al servizio di gestione di ripristino. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Gestisce i cluster di Service Fabric autonomi. |
| [servizio](service-fabric-sfctl-service.md) | Creare, eliminare e gestire servizi, tipi di servizio e pacchetti di servizi. |
| [impostazioni](service-fabric-sfctl-settings.md) | Consente di configurare le impostazioni locali per l'istanza di sfctl. |
| [store](service-fabric-sfctl-store.md) | Esegue operazioni base a livello di file nell'archivio immagini del cluster. |

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](./scripts/sfctl-upgrade-application.md).

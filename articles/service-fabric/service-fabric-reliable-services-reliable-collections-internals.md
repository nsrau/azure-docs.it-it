---
title: Elementi interni delle raccolte Reliable Collections e di Reliable State Manager in Azure Service Fabric | Microsoft Docs
description: Approfondimento dei concetti e della progettazione delle raccolte Reliable Collections in Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.contentlocale: it-it
ms.lasthandoff: 05/04/2017

---

# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Elementi interni delle raccolte Reliable Collections e di Reliable State Manager in Azure Service Fabric
Questo articolo tratta in modo approfondito Reliable State Manager e le raccolte Reliable Collections per spiegare il funzionamento in background dei componenti di base.

> [!NOTE]
> Il documento è in continua evoluzione ed è possibile aggiungere commenti all'articolo per indicare gli argomenti per cui si richiedono maggiori informazioni.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Modello di persistenza locale: log e checkpoint
Reliable State Manager e le raccolte Reliable Collections seguono un modello di persistenza basato su log e checkpoint.
In questo modello ogni cambiamento di stato viene registrato per prima cosa sul disco e quindi applicato in memoria.
Lo stesso stato completo viene reso persistente solo occasionalmente (noto anche come Checkpoint).
Il vantaggio è che per migliorare le prestazioni, le differenze vengono trasformate in operazioni di scrittura sequenziali di solo accodamento sul disco.

Per comprendere meglio il modello basato su log e checkpoint, considerare prima di tutto lo scenario con disco infinito.
Reliable State Manager registra ogni operazione prima che venga replicata.
La registrazione consente alla raccolta Reliable Collections di applicare l'operazione solo in memoria.
Dal momento che i log sono persistenti, anche se la replica ha esito negativo e deve essere riavviata, Reliable State Manager dispone di informazioni sufficienti per riprodurre tutte le operazioni perse dalla replica.
Poiché il disco è infinito, non è mai necessario rimuovere i record del log e Reliable Collections deve gestire solo lo stato in memoria.

Si prenda ora in considerazione lo scenario con disco finito.
Mano a mano che si accumulano i record di log, Reliable State Manager non avrà più spazio su disco a disposizione.
Prima che questo accada, Reliable State Manager deve troncare il proprio log per fare spazio ai record più recenti.
Reliable State Manager richiede quindi alle raccolte Reliable Collections di inserire un checkpoint del proprio stato in memoria sul disco.
A questo punto, le raccolte Reliable Collections rendono persistente il proprio stato in memoria.
Dopo che le raccolte Reliable Collections hanno completato i propri checkpoint, Reliable State Manager può troncare il log per liberare spazio su disco.
Quando è necessario riavviare la replica, le raccolte Reliable Collections ripristinano il proprio stato in corrispondenza del checkpoint e Reliable State Manager ripristina e riproduce tutte le modifiche apportate allo stato dall'ultimo checkpoint.

L'aggiunta di un altro valore di checkpoint migliora i tempi di ripristino negli scenari comuni. Il log contiene tutte le operazioni che si sono verificate dall'ultimo checkpoint.
Pertanto, può includere più versioni di un elemento come più valori per una determinata riga in Reliable Dictionary.
Al contrario, una raccolta Reliable Collection inserisce un checkpoint solo della versione più recente di ogni valore per una chiave.

## <a name="next-steps"></a>Passaggi successivi
* [Transazioni e blocchi](service-fabric-reliable-services-reliable-collections-transactions-locks.md)



---
title: Monitoraggio e diagnostica nelle applicazioni di contenitori di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sul monitoraggio e sulla diagnostica dell'applicazione in mesh Service Fabric su Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 6166808c85bbee9465a8fa12332afe2163027982
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810625"
---
# <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica
Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Il monitoraggio e la diagnostica di mesh Service Fabric sono suddivisi in tre tipi principali di dati di diagnostica:

- Registri applicazioni: sono definiti come i registri delle applicazioni in contenitori, in base al modo in cui è stata instrumentata l'applicazione (ad esempio, log di Docker)
- Eventi piattaforma: eventi dalla piattaforma Mesh relativi all'operazione del contenitore, che attualmente includono l'attivazione, la disattivazione e la terminazione del contenitore.
- Metriche contenitore: uso delle risorse e metriche delle prestazioni per i contenitori (statistiche Docker)

Questo articolo illustra le opzioni di monitoraggio e diagnostica per la versione di anteprima più recente disponibile.

## <a name="application-logs"></a>Log applicazioni

È possibile visualizzare i log di Docker dai contenitori distribuiti, in base al contenitore. Nel modello di applicazione di mesh Service Fabric, ogni contenitore è un pacchetto di codice nell'applicazione. Per visualizzare i log associati a un pacchetto di codice usare il comando seguente:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> È possibile usare il comando "az mesh service-replica" per ottenere il nome della replica. I nomi della replica sono numeri incrementali a partire da 0.*

Ecco come appare per visualizzare i log dal contenitore VotingWeb.Code dall'applicazione di voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su mesh Service Fabric leggere la panoramica:
- [Panoramica di mesh Service Fabric](service-fabric-mesh-overview.md)

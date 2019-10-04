---
title: Procedure consigliate per applicazioni e cluster di Azure Service Fabric | Microsoft Docs
description: Procedure consigliate per la gestione di cluster e applicazioni di Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 5fdbd3f15b11e4c3975ca29627d5984382bcf049
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206793"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Procedure consigliate per applicazioni e cluster di Azure Service Fabric

Questo articolo vengono forniti collegamenti alle procedure consigliate per la gestione dei cluster e applicazioni di Azure Service Fabric. È consigliabile implementare queste procedure consigliate per ottimizzare l'affidabilità dell'ambiente di produzione. Usare uno dei [modelli di cluster di Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) per iniziare a progettare la soluzione di produzione o per aggiornare il modello esistente in modo da incorporare queste procedure consigliate.

## <a name="security"></a>Security

* [Procedure consigliate per la sicurezza](service-fabric-best-practices-security.md)

## <a name="networking"></a>Rete

* [Procedure consigliate per la rete](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Pianificazione e scalabilità dell'ambiente di calcolo

* [Procedure consigliate per il ridimensionamento del calcolo](service-fabric-best-practices-capacity-scaling.md)
* [Pianificazione della capacità di calcolo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure as code

* [Procedure consigliate per l'implementazione di Infrastructure as Code (IaC)](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

* [Procedure consigliate per monitoraggio e diagnostica del cluster](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Progettazione di applicazioni

* [Le procedure consigliate per la progettazione di applicazioni](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Elenco di controllo

Dopo aver implementato le procedure consigliate nelle sezioni precedenti, verificare che tutte le procedure consigliate nell'elenco di controllo dello stato di preparazione dell'ambiente di produzione è stata integrata:
* [Azure Service Fabric produzione readiness elenco di controllo](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risolvere i problemi di Service Fabric: [Risoluzione dei problemi di guide](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
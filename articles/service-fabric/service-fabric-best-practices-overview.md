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
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231378"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Procedure consigliate per applicazioni e cluster di Azure Service Fabric

Per gestire correttamente i cluster e applicazioni di Azure Service Fabric, sono presenti operazioni che è altamente consigliabile che eseguire per ottimizzare l'affidabilità dell'ambiente di produzione; . eseguire le operazioni definite in questo documento e selezionare uno dei nostri [modelli di Cluster di Service Fabric di Azure esempi](https://github.com/Azure-Samples/service-fabric-cluster-templates) per iniziare a progettare la soluzione di produzione o modificare il modello esistente in modo da incorporare queste procedure consigliate.

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

Dopo aver completato tutte le sezioni precedenti, verificare di aver integrato tutte le procedure consigliate nell'elenco di controllo per l'idoneità per la produzione:
* [Elenco di controllo per l'idoneità per la produzione di Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle macchine virtuali o nei computer che eseguono Windows Server: [Creazione del cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle macchine virtuali o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risoluzione dei problemi: [Guida alla risoluzione dei problemi di Service Fabric](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
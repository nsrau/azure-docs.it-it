---
title: Procedure consigliate per applicazioni e cluster di Azure Service Fabric
description: Procedure consigliate e considerazioni di progettazione per la gestione di cluster, app e servizi con Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 86a02fd489ca0eec61b798db7136f963277f6c82
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261090"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Procedure consigliate per applicazioni e cluster di Azure Service Fabric

Questo articolo fornisce i collegamenti alle procedure consigliate per la gestione di applicazioni e cluster di Azure Service Fabric. Si consiglia vivamente di implementare queste procedure per ottimizzare l'affidabilità dell'ambiente di produzione. Usare uno dei [modelli di cluster Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) per iniziare a progettare la soluzione di produzione oppure aggiornare il modello esistente per incorporare queste procedure.

## <a name="security"></a>Sicurezza

* [Procedure consigliate per la sicurezza](service-fabric-best-practices-security.md)

## <a name="networking"></a>Rete

* [Procedure consigliate per la rete](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Pianificazione e scalabilità dell'ambiente di calcolo

* [Procedure consigliate per il ridimensionamento del calcolo](service-fabric-best-practices-capacity-scaling.md)
* [Pianificazione della capacità di calcolo](./service-fabric-cluster-capacity.md)

## <a name="infrastructure-as-code"></a>Infrastruttura come codice

* [Procedure consigliate per l'implementazione di Infrastructure as Code (IaC)](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Monitoraggio e diagnostica

* [Procedure consigliate per monitoraggio e diagnostica del cluster](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Progettazione di applicazioni

* [Procedure consigliate per la progettazione di applicazioni](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Elenco di controllo

Dopo aver implementato le procedure consigliate nelle sezioni precedenti, assicurarsi di aver integrato tutte le procedure consigliate nell'elenco di controllo della conformità per la produzione:
* [Elenco di controllo della conformità per la produzione di Azure Service Fabric](./service-fabric-production-readiness-checklist.md)

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risoluzione dei problemi Service Fabric: [guide alla risoluzione dei problemi](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)

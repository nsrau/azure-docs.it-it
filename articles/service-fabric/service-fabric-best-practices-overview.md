---
title: Procedure consigliate per applicazioni e cluster di Azure Service Fabric
description: Procedure consigliate e considerazioni di progettazione per la gestione di cluster, app e servizi con Azure Service Fabric.Best practices and design considerations for managing clusters, apps, and services using Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: pepogors
ms.openlocfilehash: 46601fd91fccb5bd0866bd999cc7643c37c6d582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551778"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Procedure consigliate per applicazioni e cluster di Azure Service Fabric

Questo articolo fornisce collegamenti alle procedure consigliate per la gestione di cluster e applicazioni di Azure Service Fabric.This article provides links to best practices for managing Azure Service Fabric applications and clusters. È consigliabile implementare queste procedure per ottimizzare l'affidabilità dell'ambiente di produzione. Usare uno dei modelli di cluster di [Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates) per iniziare a progettare la soluzione di produzione o aggiornare il modello esistente per incorporare queste procedure.

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

* [Procedure consigliate per la progettazione di applicazioni](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Elenco di controllo

Dopo aver implementato le procedure suggerite nelle sezioni precedenti, assicurarsi di aver integrato tutte le procedure consigliate nell'elenco di controllo per la preparazione alla produzione:After you implement the practices suggested in the previous sections, ensure that you've integrated all the best practices in the production readiness checklist:
* [Elenco di controllo per la preparazione alla produzione di Azure Service FabricAzure Service Fabric production readiness checklist](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risolvere i problemi relativi a Service Fabric: [guide alla risoluzione dei problemiTroubleshoot](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) Service Fabric: Troubleshooting guides
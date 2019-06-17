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
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939831"
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
> È possibile usare il comando "az mesh service-replica" per ottenere il nome della replica. I nomi di replica sono l'incrementazione di numeri interi da 0.

Ecco come appare per visualizzare i log dal contenitore VotingWeb.Code dall'applicazione di voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriche del contenitore 

L'ambiente di rete Mesh espone un numero limitato di metriche che indicano come eseguono i contenitori. Le metriche seguenti sono disponibili tramite Azure portale e Azure monitor della riga di comando:

| Metrica | Descrizione | Unità|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu sotto forma di percentuale | % |
| MemoryUtilization | ActualMem/AllocatedMem sotto forma di percentuale | % |
| AllocatedCpu | CPU allocata in base ai modelli Azure Resource Manager | Millicore |
| AllocatedMemory | Memoria allocata in base ai modelli Azure Resource Manager | MB |
| ActualCpu | Utilizzo CPU | Millicore |
| ActualMemory | Utilizzo memoria | MB |
| ContainerStatus | 0 - valido: Lo stato del contenitore è sconosciuto <br> 1 - in sospeso: Il contenitore è pianificato per iniziare <br> 2 - inizio: Il contenitore è in corso di avvio <br> 3 - started: Il contenitore è stato avviato correttamente <br> 4 - arresto: Il contenitore è in corso l'arresto <br> 5 - arrestato: Il contenitore è stato arrestato correttamente | N/D |
| ApplicationStatus | 0 - sconosciuto: Lo stato non è recuperabile <br> 1 - inizio: L'applicazione viene eseguita correttamente <br> 2 - aggiornamento: È un aggiornamento in corso <br> 3 - creazione: Viene creata l'applicazione <br> 4 - eliminazione: L'applicazione è in corso di eliminazione <br> 5 - non è riuscita: Impossibile distribuire l'applicazione | N/D |
| ServiceStatus | 0 - valido: Il servizio non ha attualmente uno stato di integrità <br> 1 - Ok: Il servizio è integro  <br> 2 - avviso: Potrebbe esserci un problema che richiede di indagine <br> 3 - Errore: Ci sono problemi che analisi necessaria <br> 4 - sconosciuto: Lo stato non è recuperabile | N/D |
| ServiceReplicaStatus | 0 - valido: La replica non presenta attualmente uno stato di integrità <br> 1 - Ok: Il servizio è integro  <br> 2 - avviso: Potrebbe esserci un problema che richiede di indagine <br> 3 - Errore: Ci sono problemi che analisi necessaria <br> 4 - sconosciuto: Lo stato non è recuperabile | N/D | 
| RestartCount | Numero di riavvio del contenitore | N/D |

> [!NOTE]
> I valori ServiceStatus e ServiceReplicaStatus sono lo stesso come il [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Ogni metrica è disponibile in dimensioni diverse, quindi è possibile visualizzare le aggregazioni a livelli diversi. L'elenco corrente delle dimensioni sono i seguenti:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> La quota di code non è disponibile per applicazioni Linux. 

Ogni dimensione corrisponde a diversi componenti del [modello applicazione di Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Monitoraggio di Azure CLI

È disponibile in un elenco completo dei comandi di [documentazione di monitoraggio di Azure CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) ma sono stati inclusi alcuni esempi utili riportato di seguito 

In ogni esempio, l'ID risorsa segue questo modello

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilizzo della CPU dei contenitori in un'applicazione

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilizzo della memoria per ogni Replica del servizio
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Riavvii per ogni contenitore in una finestra di 1 ora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilizzo medio della CPU in tutti i servizi denominati "VotingWeb" in una finestra di 1 ora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Esplora metriche

Esplora metriche è un pannello nel portale in cui è possibile visualizzare tutte le metriche per l'applicazione Mesh. Questo pannello è accessibile nella pagina dell'applicazione nel portale e il pannello monitoraggio di Azure, quest'ultimo dei quali è possibile usare per visualizzare le metriche per tutte le risorse di Azure che supportano il monitoraggio di Azure. 

![Esplora metriche](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
* Per altre informazioni sui comandi di metriche di monitoraggio di Azure, consultare il [documentazione di monitoraggio di Azure CLI](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).

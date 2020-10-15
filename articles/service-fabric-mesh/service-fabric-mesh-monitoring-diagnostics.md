---
title: Monitoraggio e diagnostica nelle app di Azure Service Fabric mesh
description: Informazioni sul monitoraggio e sulla diagnostica dell'applicazione in mesh Service Fabric su Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: e940f0cf0d1547b317cd9e7bd15ac5486d5e70b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248408"
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
> È possibile usare il comando "az mesh service-replica" per ottenere il nome della replica. I nomi delle repliche incrementano i numeri interi da 0.

Ecco come appare per visualizzare i log dal contenitore VotingWeb.Code dall'applicazione di voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriche del contenitore 

L'ambiente mesh espone alcune metriche che indicano il modo in cui i contenitori vengono eseguiti. Le metriche seguenti sono disponibili tramite l'interfaccia della riga di comando portale di Azure e monitoraggio di Azure:

| Metrica | Descrizione | Unità|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu come percentuale | % |
| MemoryUtilization | ActualMem/AllocatedMem come percentuale | % |
| AllocatedCpu | CPU allocata in base al modello di Azure Resource Manager | Millicore |
| AllocatedMemory | Memoria allocata in base al modello di Azure Resource Manager | MB |
| ActualCpu | Utilizzo della CPU | Millicore |
| ActualMemory | Utilizzo della memoria | MB |
| ContainerStatus | 0-non valido: lo stato del contenitore è sconosciuto <br> 1-in sospeso: il contenitore ha pianificato l'avvio <br> 2-avvio: il contenitore è in fase di avvio <br> 3-avviato: il contenitore è stato avviato correttamente <br> 4-arresto: il contenitore viene arrestato <br> 5-arrestato: il contenitore è stato arrestato correttamente | N/D |
| ApplicationStatus | 0-sconosciuto: lo stato non è recuperabile <br> 1-pronto: l'applicazione viene eseguita correttamente <br> 2-aggiornamento: è in corso un aggiornamento <br> 3-creazione: è in corso la creazione dell'applicazione <br> 4-Eliminazione: è in corso l'eliminazione dell'applicazione <br> 5-non riuscito: non è stato possibile distribuire l'applicazione | N/D |
| ServiceStatus | 0-non valido: il servizio attualmente non dispone di uno stato di integrità <br> 1-OK: il servizio è integro  <br> 2-avviso: è possibile che si verifichi un errore durante l'analisi <br> 3-errore: si è verificato un problema che richiede un'analisi <br> 4-sconosciuto: lo stato non è recuperabile | N/D |
| ServiceReplicaStatus | 0-non valido: la replica non dispone attualmente di uno stato di integrità <br> 1-OK: il servizio è integro  <br> 2-avviso: è possibile che si verifichi un errore durante l'analisi <br> 3-errore: si è verificato un problema che richiede un'analisi <br> 4-sconosciuto: lo stato non è recuperabile | N/D | 
| RestartCount | Numero di riavvii del contenitore | N/D |

> [!NOTE]
> I valori ServiceStatus e ServiceReplicaStatus sono uguali a quelli di [HealthState](/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Ogni metrica è disponibile su dimensioni diverse, in modo da poter visualizzare le aggregazioni a livelli diversi. L'elenco corrente delle dimensioni è il seguente:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> La dimensione codepackagename non è disponibile per le applicazioni Linux. 

Ogni dimensione corrisponde a componenti diversi del [modello di applicazione Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>INTERFACCIA della riga di comando monitoraggio di Azure

Un elenco completo dei comandi è disponibile nella documentazione dell'interfaccia della riga di comando di [monitoraggio di Azure](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , ma sono stati inclusi alcuni esempi utili di seguito 

In ogni esempio, l'ID risorsa segue questo modello

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilizzo della CPU dei contenitori in un'applicazione

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilizzo della memoria per ogni replica del servizio
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Riavvia per ogni contenitore in una finestra di 1 ora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilizzo medio della CPU tra servizi denominati "VotingWeb" in una finestra di 1 ora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Esplora metriche

Esplora metriche è un pannello nel portale in cui è possibile visualizzare tutte le metriche per l'applicazione mesh. Questo pannello è accessibile nella pagina dell'applicazione nel portale e nel Pannello monitoraggio di Azure, il secondo che è possibile usare per visualizzare le metriche per tutte le risorse di Azure che supportano monitoraggio di Azure. 

![Esplora metriche](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
* Per altre informazioni sui comandi di metrica di monitoraggio di Azure, vedere la [documentazione dell'interfaccia](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)della riga di comando di monitoraggio di Azure.

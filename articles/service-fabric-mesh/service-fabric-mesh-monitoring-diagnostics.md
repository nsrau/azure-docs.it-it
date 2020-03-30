---
title: Monitoraggio e diagnostica nelle app Mesh di Azure Service FabricMonitoring and diagnostics in Azure Service Fabric Mesh apps
description: Informazioni sul monitoraggio e sulla diagnostica dell'applicazione in mesh Service Fabric su Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498152"
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
> È possibile usare il comando "az mesh service-replica" per ottenere il nome della replica. I nomi delle repliche incrementano i numeri interi da 0.Replica names are incrementing integers from 0.

Ecco come appare per visualizzare i log dal contenitore VotingWeb.Code dall'applicazione di voto:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metriche dei contenitori 

L'ambiente Mesh espone una manciata di metriche che indicano le prestazioni dei contenitori. The following metrics are available via the Azure portal and Azure monitor CLI:

| Metrica | Descrizione | Unità|
|----|----|----|
| Utilizzo Cpu | ActualCpu/AllocatedCpu come percentuale | % |
| Utilizzo della memoria | ActualMem/AllocatedMem in percentuale | % |
| AllocazioneCpu | Cpu allocata in base al modello di Azure Resource Manager | Millicore |
| AllocatedMemory | Memoria allocata in base al modello di Azure Resource ManagerMemory allocated as per Azure Resource Manager template | MB |
| Cpu effettiva | Utilizzo di CPU | Millicore |
| ActualMemory (Memoria effettiva) | Utilizzo della memoria | MB |
| Proprietà ContainerStatus | 0 - Non valido: lo stato del contenitore è sconosciuto <br> 1 - In sospeso: l'avvio pianificato del contenitore <br> 2 - Avvio: il contenitore è in fase di avvio <br> 3 - Avviato: il contenitore è stato avviato correttamente <br> 4 - Arresto: il contenitore viene arrestato <br> 5 - Arrestato: il contenitore è stato arrestato correttamente | N/D |
| Stato applicazione | 0 - Sconosciuto: lo stato non è recuperabile <br> 1 - Pronto: l'applicazione è stata eseguita correttamente <br> 2 - Aggiornamento: è in corso un aggiornamento <br> 3 - Creazione: l'applicazione viene creata <br> 4 - Eliminazione: l'applicazione è in fase di eliminazione <br> 5 - Non riuscito: la distribuzione dell'applicazione non è riuscita | N/D |
| ServiceStatus | 0 - Non valido: il servizio non dispone attualmente di uno stato di integrità <br> 1 - Ok: il servizio è sano  <br> 2 - Avvertenza: Potrebbe esserci qualcosa di sbagliato che richiede un'indagine <br> 3 - Errore: C'è qualcosa di sbagliato che ha bisogno di un'indagine <br> 4 - Sconosciuto: lo stato non è recuperabile | N/D |
| ServiceReplicaStatus (ReplicaServizio) | 0 - Non valido: la replica non dispone attualmente di uno stato di integrità <br> 1 - Ok: il servizio è sano  <br> 2 - Avvertenza: Potrebbe esserci qualcosa di sbagliato che richiede un'indagine <br> 3 - Errore: C'è qualcosa di sbagliato che ha bisogno di un'indagine <br> 4 - Sconosciuto: lo stato non è recuperabile | N/D | 
| Conteggio riavvio | Numero di riavvii del contenitore | N/D |

> [!NOTE]
> I valori ServiceStatus e ServiceReplicaStatus sono gli stessi di [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) in Service Fabric. 

Ogni metrica è disponibile in dimensioni diverse in modo da poter visualizzare le aggregazioni a livelli diversi. L'elenco corrente delle dimensioni è il seguente:

* ApplicationName
* ServiceName
* ServiceReplicaName (NomeReplicaservizio)
* Nome CoPackageCodePackageName

> [!NOTE]
> La dimensione CodePackageName non è disponibile per le applicazioni Linux.The CodePackageName dimension is not available for Linux applications. 

Ogni dimensione corrisponde a componenti diversi del modello di applicazione [Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Interfaccia della riga di comando di Monitoraggio di AzureAzure Monitor

Un elenco completo dei comandi è disponibile nella [documentazione dell'interfaccia della](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) riga di comando di Monitoraggio di Azure, ma sono stati inclusi alcuni esempi utili di seguito 

In ogni esempio, l'ID risorsa segue questo modello

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Utilizzo della CPU dei contenitori in un'applicazione

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Utilizzo della memoria per ogni replica del servizioMemory Utilization for each Service Replica
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Riavvii per ogni contenitore in una finestra di 1 ora 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Utilizzo medio della CPU tra i servizi denominati "VotingWeb" in un intervallo di 1 ora
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Esplora metriche

Esplora metriche è un pannello nel portale in cui è possibile visualizzare tutte le metriche per l'applicazione Mesh.Metrics explorer is a blade in the portal in which you can visualize all the metrics for your Mesh application. Questo pannello è accessibile nella pagina dell'applicazione nel portale e nel pannello Monitoraggio di Azure, quest'ultimo dei quali è possibile usare per visualizzare le metriche per tutte le risorse di Azure che supportano Monitoraggio di Azure.This blade is accessible in the application's page in the portal and the Azure monitor blade, the latter of which you can use to view metrics for all your Azure resources that support Azure Monitor. 

![Esplora metriche](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
* Per altre informazioni sui comandi delle metriche di Monitoraggio di Azure, vedere i documenti dell'interfaccia della riga di comando di Monitoraggio di Azure.To learn more about the Azure Monitor metrics commands, check out the [Azure Monitor CLI docs.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)

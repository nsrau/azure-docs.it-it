---
title: Azure Service Fabric - Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure | Microsoft Docs
description: Usare Diagnostica di Microsoft Azure per raccogliere i contatori delle prestazioni per i cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: e29c32c27e7f6c62eb2c6a9cbe2e4d3f1294f038
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155154"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure

Questo documento illustra la procedura necessaria per configurare la raccolta dei contatori delle prestazioni tramite l'estensione Diagnostica di Microsoft Azure per i cluster Windows. Per i cluster Linux, configurare l'[agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) per raccogliere i contatori delle prestazioni per i nodi. 

 > [!NOTE]
> Perché questa procedura funzioni, l'estensione Diagnostica di Microsoft Azure deve essere distribuita nel cluster. Se non è configurata, vedere [Aggregazione e raccolta di eventi con Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Raccogliere i contatori delle prestazioni tramite WadCfg

Per raccogliere i contatori delle prestazioni tramite Diagnostica di Microsoft Azure, è necessario modificare correttamente la configurazione nel modello di Resource Manager del cluster. Seguire questa procedura per aggiungere al modello un contatore delle prestazioni che si vuole raccogliere ed eseguire un aggiornamento delle risorse di Resource Manager.

1. Trovare la configurazione di Diagnostica di Microsoft Azure nel modello del cluster: `WadCfg`. I contatori delle prestazioni da raccogliere verranno aggiunti in `DiagnosticMonitorConfiguration`.

2. Impostare la configurazione per raccogliere i contatori delle prestazioni aggiungendo la sezione seguente a `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` definisce la frequenza con cui i valori dei contatori raccolti vengono trasferiti alla tabella di archiviazione di Azure e ai sink configurati. 

3. Aggiungere i contatori delle prestazioni che si vuole raccogliere all'elemento `PerformanceCounterConfiguration` dichiarato nel passaggio precedente. Ogni contatore che si vuole raccogliere viene definito con `counterSpecifier`, `sampleRate`, `unit`, `annotation` ed eventuali `sinks` pertinenti.

Di seguito è riportato un esempio di configurazione con il contatore *Tempo totale processore* (tempo in cui la CPU ha eseguito le operazioni di elaborazione) e le *chiamate al metodo di Service Fabric Actor al secondo*, uno dei contatori delle prestazioni personalizzati di Service Fabric. Vedere [Contatori delle prestazioni di Actor affidabili](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) e [Contatori delle prestazioni del Servizio affidabili](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) per un elenco completo dei contatori delle prestazioni affidabili di Service Fabric.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 La frequenza di campionamento per il contatore può essere modificata a seconda delle esigenze. Poiché il formato è `PT<time><unit>`, se si vuole raccogliere il contatore ogni secondo, è consigliabile impostare `"sampleRate": "PT15S"`.

 È anche possibile usare variabili nel modello ARM per raccogliere una matrice di contatori delle prestazioni, che può essere utile quando si raccolgono contatori delle prestazioni per processo. Nell'esempio seguente, vengono raccolti tempo di elaborazione e tempo di Garbage Collector per ogni processo e quindi 2 contatori delle prestazioni nei nodi stessi, che usano tutti variabili. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Dopo avere aggiunto i contatori delle prestazioni appropriati che devono essere raccolti, è necessario aggiornare la risorsa cluster in modo che queste modifiche vengano riflesse nel cluster in esecuzione. Salvare il file `template.json` modificato e aprire PowerShell. È possibile aggiornare il cluster usando `New-AzResourceGroupDeployment`. La chiamata richiede il nome del gruppo di risorse e il file del modello aggiornato e chiede a Resource Manager di apportare le modifiche appropriate alle risorse aggiornate. Dopo avere eseguito l'accesso all'account e alla sottoscrizione corretta, usare il comando seguente per eseguire l'aggiornamento:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Al termine della distribuzione dell'aggiornamento (da 15 a 45 minuti, a seconda che si tratti della prima distribuzione o meno e in base alle dimensioni del gruppo di risorse), Diagnostica di Microsoft Azure raccoglierà i contatori delle prestazioni e li invierà a una tabella denominata WADPerformanceCountersTable nell'account di archiviazione associato al cluster. Vedere i contatori delle prestazioni in Application Insights [aggiungendo il sink di Application Insights al modello di Resource Manager](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Passaggi successivi
* Raccogliere altri contatori delle prestazioni per il cluster. Vedere [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) per un elenco di contatori da raccogliere.
* [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) per apportare altre modifiche a `WadCfg`, inclusa la configurazione di account di archiviazione aggiuntivi a cui inviare i dati di diagnostica.
* Visitare il [Generatore di WadCfg](https://azure.github.io/azure-diagnostics-tools/config-builder/) per creare un modello da zero e verificare che la sintassi sia corretta. (https://azure.github.io/azure-diagnostics-tools/config-builder/) per compilare un modello da zero e assicurarsi che la sintassi sia corretta.

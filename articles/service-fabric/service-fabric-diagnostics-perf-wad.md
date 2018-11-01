---
title: Azure Service Fabric - Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure | Microsoft Docs
description: Usare Diagnostica di Microsoft Azure per raccogliere i contatori delle prestazioni per i cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: srrengar
ms.openlocfilehash: bc86ef5a32e08bc00b5a2fa53dccb8d6313f167b
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230986"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure

Questo documento illustra la procedura necessaria per configurare la raccolta dei contatori delle prestazioni tramite l'estensione Diagnostica di Microsoft Azure per i cluster Windows. Per i cluster Linux, configurare l'[agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) per raccogliere i contatori delle prestazioni per i nodi. 

 > [!NOTE]
> Perché questa procedura funzioni, l'estensione Diagnostica di Microsoft Azure deve essere distribuita nel cluster. Se non è configurata, vedere [Aggregazione e raccolta di eventi con Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md).  

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

 >[!NOTE]
 >Anche se è possibile usare `*` per specificare gruppi di contatori delle prestazioni con nomi simili, per inviare i contatori tramite un sink (ad Application Insights), è necessario dichiararli singolarmente. 

4. Dopo avere aggiunto i contatori delle prestazioni appropriati che devono essere raccolti, è necessario aggiornare la risorsa cluster in modo che queste modifiche vengano riflesse nel cluster in esecuzione. Salvare il file `template.json` modificato e aprire PowerShell. È possibile aggiornare il cluster usando `New-AzureRmResourceGroupDeployment`. La chiamata richiede il nome del gruppo di risorse e il file del modello aggiornato e chiede a Resource Manager di apportare le modifiche appropriate alle risorse aggiornate. Dopo avere eseguito l'accesso all'account e alla sottoscrizione corretta, usare il comando seguente per eseguire l'aggiornamento:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Al termine della distribuzione dell'aggiornamento (da 15 a 45 minuti), Diagnostica di Microsoft Azure raccoglierà i contatori delle prestazioni e li invierà a una tabella denominata WADPerformanceCountersTable nell'account di archiviazione associato al cluster. Vedere i contatori delle prestazioni in Application Insights [aggiungendo il sink di Application Insights al modello di Resource Manager](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Passaggi successivi
* Raccogliere altri contatori delle prestazioni per il cluster. Vedere [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) per un elenco di contatori da raccogliere.
* [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) per apportare altre modifiche a `WadCfg`, inclusa la configurazione di account di archiviazione aggiuntivi a cui inviare i dati di diagnostica.

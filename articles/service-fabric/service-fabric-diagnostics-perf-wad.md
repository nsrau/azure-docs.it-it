---
title: Azure Service Fabric - Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure | Microsoft Docs
description: Usare Diagnostica di Microsoft Azure per raccogliere i contatori delle prestazioni per i cluster di Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Monitoraggio delle prestazioni con l'estensione Diagnostica di Microsoft Azure

Questo documento illustra la procedura necessaria per configurare la raccolta dei contatori delle prestazioni tramite l'estensione Diagnostica di Microsoft Azure per i cluster Windows. Per i cluster Linux, configurare l'[agente di OMS](service-fabric-diagnostics-oms-agent.md) per raccogliere i contatori delle prestazioni per i nodi. 

 > [!NOTE]
> Perché questa procedura funzioni, l'estensione Diagnostica di Microsoft Azure deve essere distribuita nel cluster. Se non è configurata, passare a [Aggregazione e raccolta di eventi con Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) e seguire la procedura illustrata in *Distribuire l'estensione Diagnostica*.

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

3. Aggiungere i contatori delle prestazioni che si vuole raccogliere all'elemento `PerformanceCounterConfiguration` dichiarato nel passaggio precedente. Ogni contatore che si vuole raccogliere viene definito con `counterSpecifier`, `sampleRate`, `unit`, `annotation` ed eventuali `sinks` pertinenti. Ecco un esempio di contatore delle prestazioni per il *tempo del processore totale* (intervallo di tempo in cui la CPU è stata in uso per elaborare le operazioni):

    ```json
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
            }
        ]
    }
    ```

    La frequenza di campionamento per il contatore può essere modificata a seconda delle esigenze. Poiché il formato è `PT<time><unit>`, se si vuole raccogliere il contatore ogni secondo, è consigliabile impostare `"sampleRate": "PT15S"`.

    Analogamente, è possibile raccogliere diversi altri contatori delle prestazioni, aggiungendoli all'elenco in `PerformanceCounterConfiguration`. Anche se è possibile usare `*` per specificare gruppi di contatori delle prestazioni con nomi simili, per inviare i contatori tramite un sink (ad Application Insights), è necessario dichiararli singolarmente. 

4. Dopo avere aggiunto i contatori delle prestazioni appropriati che devono essere raccolti, è necessario aggiornare la risorsa cluster in modo che queste modifiche vengano applicate nel cluster in esecuzione. Salvare il file `template.json` modificato e aprire PowerShell. È possibile aggiornare il cluster usando `New-AzureRmResourceGroupDeployment`. La chiamata richiede il nome del gruppo di risorse e il file del modello aggiornato e chiede a Resource Manager di apportare le modifiche appropriate alle risorse aggiornate. Dopo avere eseguito l'accesso all'account e alla sottoscrizione corretta, usare il comando seguente per eseguire l'aggiornamento:

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Al termine dell'implementazione dell'aggiornamento (sono necessari 15-45 minuti), Diagnostica di Microsoft Azure raccoglierà i contatori delle prestazioni e li invierà a una tabella nell'account di archiviazione dichiarato in `WadCfg`.

## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare i contatori delle prestazioni in Application Insights, aggiungere il sink di Application Insights a `WadCfg`. Vedere la sezione *Aggiungere il sink AI al modello di Resource Manager* in [Analisi e visualizzazione degli eventi con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) per configurare il sink di Application Insights.
* Raccogliere altri contatori delle prestazioni per il cluster. Vedere [Metriche delle prestazioni](service-fabric-diagnostics-event-generation-perf.md) per un elenco di contatori da raccogliere.
* [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) per apportare altre modifiche a `WadCfg`, inclusa la configurazione di account di archiviazione aggiuntivi a cui inviare i dati di diagnostica.
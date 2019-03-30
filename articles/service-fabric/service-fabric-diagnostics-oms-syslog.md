---
title: Monitorare gli eventi cluster Linux in Azure Service Fabric | Microsoft Docs
description: Informazioni su come monitorare gli eventi cluster Linux da Syslog
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
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 402e3dfe018c94ef068caf918b38aaad00064a49
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670371"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Eventi cluster Linux di Service Fabric in Syslog

Service Fabric espone una serie di eventi della piattaforma per informare l'utente delle attività principali nel cluster. L'elenco completo degli eventi esposti è disponibile [qui](service-fabric-diagnostics-event-generation-operational.md). Esistono diversi modi attraverso cui questi eventi possono essere utilizzati. In questo articolo verrà illustrato come configurare Service Fabric per la scrittura di questi eventi in Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introduzione

Nella versione 6.4, il SyslogConsumer è stato introdotto per inviare gli eventi della piattaforma Service Fabric a Syslog per i cluster Linux. Una volta attivati, gli eventi verranno trasferiti automaticamente in Syslog, che possono essere raccolti e inviati dall'agente di Log Analytics.

Ogni evento Syslog dispone di 4 componenti
* Facility
* Identità
* Message
* Gravità

Il SyslogConsumer scrive tutti gli eventi della piattaforma tramite Facility `Local0`. È possibile effettuare l’aggiornamento a qualsiasi struttura valida modificando la configurazione. L'identità usata è `ServiceFabric`. Il campo Messaggio contiene l'intero evento serializzato in JSON, in modo che possa essere eseguita una query o essere usato da una serie di strumenti. 

## <a name="enable-syslogconsumer"></a>Abilitare SyslogConsumer

Per abilitare SyslogConsumer, è necessario eseguire un aggiornamento del cluster. La sezione `fabricSettings` deve essere aggiornata con il codice seguente. Tenere presente che questo codice include solo le sezioni relative a SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Qui ci sono le modifiche da richiamare
1. Nella sezione Comune, è disponibile un nuovo parametro denominato `LinuxStructuredTracesEnabled`. **Questo è necessario per avere eventi Linux strutturati e serializzati quando inviati a Syslog.**
2. Nella sezione Diagnostica, un nuovo ConsumerInstance: SyslogConsumer è stato aggiunto. Questo comunica alla piattaforma che c'è un altro consumer di eventi. 
3. La nuova sezione SyslogConsumer deve avere `IsEnabled` come `true`. La configurazione prevede l'uso automatico della struttura Local0. È possibile eseguire l'override di questo elemento mediante l'aggiunta di un altro parametro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Monitoraggio di Azure log integration
È possibile leggere questi eventi Syslog in uno strumento di monitoraggio, ad esempio i log di monitoraggio di Azure. È possibile creare un’area di lavoro di Log Analytics utilizzando Azure Marketplace tramite queste [istruzioni]. (../azure-monitor/learn/quick-create-workspace.md). È inoltre necessario aggiungere l'agente Log Analytics al cluster per raccogliere e inviare questi dati allo spazio di lavoro. Questo è lo stesso agente usato per raccogliere i contatori delle prestazioni. 

1. Passare al pannello `Advanced Settings`

    ![Impostazioni area di lavoro](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Fare clic su`Data`.
3. Fare clic su`Syslog`.
4. Configurare Local0 come la Facility di cui tenere traccia. Se è stato modificato in fabricSettings, è possibile aggiungere un'altra Facility

    ![Configurare Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Passare al query explorer facendo clic su `Logs` nel menu della risorsa dell’area di lavoro per iniziare l’esecuzione della query

    ![Log dell'area di lavoro](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. È possibile eseguire una query con la tabella `Syslog` cercando `ServiceFabric` come ProcessName. La query riportata di seguito è un esempio di come analizzare il codice JSON dell'evento e visualizzarne il contenuto

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Query di Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

L'esempio precedente è di un evento NodeDown. L'elenco completo degli eventi è disponibile [qui](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire l'agente di Log Analytics](service-fabric-diagnostics-oms-agent.md) sui nodi per raccogliere contatori delle prestazioni, statistiche Docker e registri per i contenitori
* Acquisire familiarità con le [ricerca log e l'esecuzione di query](../log-analytics/log-analytics-log-searches.md) funzionalità fornite come parte dei log di monitoraggio di Azure
* [Utilizzare Progettazione vista per creare visualizzazioni personalizzate nel log di monitoraggio di Azure](../log-analytics/log-analytics-view-designer.md)
* Informazioni di riferimento per saper [monitoraggio di Azure esegue l'integrazione con Syslog](../log-analytics/log-analytics-data-sources-syslog.md).

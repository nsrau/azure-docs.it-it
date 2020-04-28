---
title: Monitorare gli eventi del cluster Linux in Azure Service Fabric
description: Informazioni su come monitorare gli eventi di un cluster Service Fabric Linux scrivendo Service Fabric eventi della piattaforma in syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645753"
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

Il SyslogConsumer scrive tutti gli eventi della piattaforma tramite Facility `Local0`. È possibile eseguire l'aggiornamento a qualsiasi funzionalità valida modificando la configurazione config. L'identità utilizzata è `ServiceFabric`. Il campo Messaggio contiene l'intero evento serializzato in JSON, in modo che possa essere eseguita una query o essere usato da una serie di strumenti. 

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
2. Nella sezione diagnostica è stato aggiunto un nuovo ConsumerInstance: SyslogConsumer. Questo comunica alla piattaforma che c'è un altro consumer di eventi. 
3. La nuova sezione SyslogConsumer deve avere `IsEnabled` come `true`. La configurazione prevede l'uso automatico della struttura Local0. È possibile eseguire l'override di questo elemento mediante l'aggiunta di un altro parametro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integrazione dei log di monitoraggio di Azure
È possibile leggere questi eventi syslog in uno strumento di monitoraggio, ad esempio i log di monitoraggio di Azure. È possibile creare un’area di lavoro Log Analytics utilizzando Azure Marketplace tramite queste [istruzioni]. (../azure-monitor/learn/quick-create-workspace.md). È inoltre necessario aggiungere l'agente Log Analytics al cluster per raccogliere e inviare questi dati allo spazio di lavoro. Questo è lo stesso agente usato per raccogliere i contatori delle prestazioni. 

1. Passare al pannello `Advanced Settings`

    ![Impostazioni area di lavoro](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Fare clic su`Data`.
3. Fare clic su`Syslog`.
4. Configurare Local0 come funzionalità di cui tenere traccia. È possibile aggiungere un'altra funzionalità se è stata modificata in fabricSettings

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
* Acquisire familiarità con le funzionalità di [Ricerca log ed esecuzione di query](../log-analytics/log-analytics-log-searches.md) disponibili nell'ambito dei log di monitoraggio di Azure
* [Usare Progettazione viste per creare visualizzazioni personalizzate nei log di monitoraggio di Azure](../log-analytics/log-analytics-view-designer.md)
* Informazioni di riferimento sull' [integrazione dei log di monitoraggio di Azure con syslog](../log-analytics/log-analytics-data-sources-syslog.md).

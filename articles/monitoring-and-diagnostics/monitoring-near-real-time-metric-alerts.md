---
title: Avvisi delle metriche near real time in Monitoraggio di Azure | Microsoft Docs
description: Gli avvisi delle metriche near real time consentono di monitorare le metriche delle risorse di Azure con una frequenza di 1 minuto.
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: cd1002929ad749ac1742e914a9f2411f09ec91d5
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>Avvisi metriche near real time (anteprima)
Monitoraggio di Azure supporta ora un nuovo tipo di avvisi delle metriche denominati Avvisi metriche near real time (anteprima). Questa funzionalità è attualmente in anteprima pubblica.
Questi avvisi sono diversi dagli avvisi delle metriche regolari per alcuni aspetti

- **Migliore latenza** - gli avvisi delle metriche near real time possono monitorare le modifiche in valori di metrica di 1 minuto.
- **Maggiore controllo delle condizioni delle metriche** - gli avvisi delle metriche near real time consentono agli utenti di definire regole di avviso più dettagliate. Gli avvisi ora supportano il monitoraggio dei valori massimo, minimo, medio e totale delle metriche.
- **Monitoraggio combinato di più metriche** - gli avvisi delle metriche near real time possono monitorare più metriche (attualmente due) con una singola regola. L'avviso viene attivato se entrambe le metriche violano le rispettive soglie per il periodo di tempo specificato.
- **Sistema di notifica modulare** - gli avvisi delle metriche near real time usano i [gruppi di azioni](monitoring-action-groups.md). Questa funzionalità consente agli utenti di creare azioni in modo modulare e riusarle per molte regole di avviso.

> [!NOTE]
> Gli avvisi delle metriche near real time sono attualmente disponibili in anteprima pubblica. L'esperienza utente e la funzionalità sono soggette a modifiche.
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>Per quali risorse è possibile creare gli avvisi delle metriche near real time?
L'elenco completo dei tipi di risorse che sono supportati dagli avvisi delle metriche near real time:

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>Avvisi di metrica in tempo reale vicino alle metriche con dimensioni
Quasi in tempo reale metrica avvisi supporta gli avvisi sulle metriche delle dimensioni. Le dimensioni sono un modo per filtrare l'unità di misura per il corretto livello. Quasi in tempo reale metrica gli avvisi sulle metriche delle dimensioni sono supportati per i seguenti tipi di risorse

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (supportato solo per gli account di archiviazione nelle aree Stati Uniti)
* Microsoft.Storage/storageAccounts/services (supportato solo per gli account di archiviazione nelle aree Stati Uniti)


## <a name="create-a-near-real-time-metric-alert"></a>Creare un avviso delle metriche near real time
Attualmente gli avvisi delle metriche near real time possono essere creati solo tramite il portale di Azure. Il supporto per la configurazione degli avvisi delle metriche near real time tramite PowerShell, l'interfaccia della riga di comando (CLI) e API REST di Monitoraggio di Azure sarà presto disponibile.

L'esperienza di avviso create per l'avviso di metrica quasi in tempo reale è stato spostato nella nuova **Alerts(Preview)** esperienza. Anche se gli avvisi correnti pagina vengono visualizzati **avviso aggiungere quasi in tempo reale metrica**, si verrà reindirizzati alla nuova esperienza.

È possibile creare un avviso di metriche in tempo reale quasi eseguendo la procedura descritta [qui](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal).

## <a name="managing-near-real-time-metric-alerts"></a>Gestione degli avvisi delle metriche near real time
Dopo aver creato un **avviso quasi in tempo reale metrica**, può essere gestita mediante i passaggi descritti [qui](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sulla nuova esperienza di avvisi (anteprima)](monitoring-overview-unified-alerts.md)
* [Informazioni sugli avvisi di Log negli avvisi di Azure (anteprima)](monitor-alerts-unified-log.md)
* [Informazioni sugli avvisi di Azure](monitoring-overview-alerts.md)
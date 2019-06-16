---
title: Raccogliere e analizzare i log attività di Azure nell'area di lavoro di Log Analitica | Microsoft Docs
description: Raccogliere Log attività di Azure in log di monitoraggio di Azure e usare la soluzione di monitoraggio per analizzare e cercare il log attività di Azure in tutte le sottoscrizioni di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.openlocfilehash: 5839fd40a128097e400f13acbe4fb6ef90c656b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248130"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Raccogliere e analizzare i log attività di Azure nell'area di lavoro di Log Analitica in Monitoraggio di Azure
Il [Log attività di Azure](activity-logs-overview.md) fornisce approfondite sugli eventi a livello di sottoscrizione che si sono verificate nella sottoscrizione di Azure. Questo articolo descrive come usare l'Analitica di Log attività e come raccogliere Log attività in un'area di lavoro di Log Analitica [soluzione di monitoraggio](../insights/solutions.md), che fornisce query di log e visualizzazioni per l'analisi dei dati. 

La connessione del Log attività a un'area di lavoro di Log Analitica offre i vantaggi seguenti:

- Consolidare il Log attività di più sottoscrizioni di Azure in un'unica posizione per l'analisi.
- Store le voci di Log attività per più di 90 giorni.
- Correlare i dati di Log attività con altri dati di monitoraggio raccolti da monitoraggio di Azure.
- Uso [registrare query](../log-query/log-query-overview.md) per eseguire analisi complessa e ottenere informazioni approfondite sulle voci di Log attività.

## <a name="connect-to-log-analytics-workspace"></a>Connettersi all'area di lavoro di Log Analitica
Un Log di attività possono essere connesse a una sola area di lavoro, ma una singola area di lavoro può essere connessa al log attività per più sottoscrizioni nello stesso tenant di Azure. Per la raccolta in più tenant, vedere [raccogliere log attività di Azure in un'area di lavoro di Log Analitica per le sottoscrizioni in Azure Active Directory diversi tenant](activity-log-collect-tenants.md).

Usare la procedura seguente per connettere il Log attività nell'area di lavoro di Log Analitica:

1. Dal **aree di lavoro di Log Analitica** menu nel portale di Azure, selezionare l'area di lavoro per raccogliere il Log attività.
1. Nel **origini dati dell'area di lavoro** sezione del menu dell'area di lavoro, seleziona **log attività Azure**.
1. Scegliere la sottoscrizione desiderata per la connessione.

    ![Aree di lavoro](media/activity-log-export/workspaces.png)

1. Fare clic su **Connect** connettersi il log attività della sottoscrizione per area di lavoro selezionata. Se la sottoscrizione è già connesso a un'altra area di lavoro, fare clic su **Disconnect** prima di tutto per eseguire la disconnessione.

    ![Connettere le aree di lavoro](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analizzare nell'area di lavoro di Log Analitica
Quando ci si connette un Log attività a un'area di lavoro di Log Analitica, le voci verranno inserite nell'area di lavoro in una tabella denominata **AzureActivity** che è possibile recuperare con una [query log](../log-query/log-query-overview.md). La struttura della tabella varia a seconda del [categoria della voce di log](activity-logs-overview.md#categories-in-the-activity-log). Visualizzare [schema di eventi di Log attività di Azure](activity-log-schema.md) per una descrizione di ogni categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Soluzione di monitoraggio Analitica i log attività
La soluzione di monitoraggio di Azure Log Analitica include più query di log e visualizzazioni per l'analisi dei record del Log attività nell'area di lavoro di Log Analitica.

### <a name="install-the-solution"></a>Installare la soluzione
Usare la procedura descritta in [installare una soluzione di monitoraggio](../insights/solutions.md#install-a-monitoring-solution) per installare il **attività Log Analitica** soluzione. Non è richiesta alcuna configurazione aggiuntiva.

### <a name="use-the-solution"></a>Usare la soluzione
Soluzioni di monitoraggio sono accessibili dal **Monitor** menu nel portale di Azure. Selezionare **altre** nel **Insights** sezione per aprire il **Panoramica** pagina con i riquadri delle soluzioni. Il **log attività di Azure** riquadro Visualizza un conteggio del numero di **AzureActivity** i record nell'area di lavoro.

![Riquadro Log attività di Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Fare clic sui **log attività di Azure** riquadro per aprire il **i log attività Azure** visualizzazione. La visualizzazione include le parti della visualizzazione nella tabella seguente. Ogni parte elenca fino a 10 elementi corrispondenti ai criteri che di parti per l'intervallo di tempo specificato. È possibile eseguire una query di log che restituisce tutti i record corrispondenti facendo **Vedi tutto** nella parte inferiore della parte.

![Dashboard Log attività di Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte della visualizzazione | Descrizione |
| --- | --- |
| Voci del Log attività di Azure | Mostra un grafico a barre della voce del Log attività Azure superiore totali di record per l'intervallo di date selezionato e un elenco dei primi 10 chiamanti attività. Fare clic sul grafico a barre per eseguire una ricerca di log per `AzureActivity`. Fare clic su un elemento chiamante per eseguire una ricerca log che restituisce tutte le voci di Log attività per tale elemento. |
| Log attività per stato | Mostra un grafico ad anello per lo stato del Log attività di Azure per l'intervallo di date selezionato e un elenco dei primi dieci record di stato. Fare clic sul grafico per eseguire una query di log per `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`. Fare clic su un elemento di stato per eseguire una ricerca log che restituisce tutte le voci di Log attività per tale record di stato. |
| Log attività per risorsa | Mostra il numero totale di risorse con i log attività ed elenca le prime dieci risorse con record conta per ogni risorsa. Fare clic sull'area totale per eseguire una ricerca di log per `AzureActivity | summarize AggregatedValue = count() by Resource`, che mostra tutte le risorse di Azure disponibili per la soluzione. Fare clic su una risorsa per eseguire una query di log che restituisce tutti i record di attività per tale risorsa. |
| Log attività per provider di risorse | Mostra il numero totale di provider di risorse che producono log attività ed elenca le prime dieci. Fare clic sull'area totale per eseguire una query di log per `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`, che mostra tutti i provider di risorse di Azure. Fare clic su un provider di risorse per eseguire una query di log restituzione di tutti i record di attività per il provider. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Log attività](activity-logs-overview.md).
- Altre informazioni sul [piattaforma dati di monitoraggio di Azure](data-platform.md).
- Uso [registrare query](../log-query/log-query-overview.md) per visualizzare informazioni dettagliate dal Log attività.

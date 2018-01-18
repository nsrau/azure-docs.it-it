---
title: "Raccogliere e analizzare i log attività di Azure in Log Analytics | Microsoft Docs"
description: "È possibile usare la soluzione Log attività di Azure per analizzare e cercare i log attività di Azure in tutte le sottoscrizioni di Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: banders
ms.openlocfilehash: e4f112a221221c7f68cc31c80fb43417bb617632
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Raccogliere e analizzare i log attività di Azure in Log Analytics

![Simbolo di Log attività di Azure](./media/log-analytics-activity/activity-log-analytics.png)

La soluzione Analisi log attività consente di analizzare e cercare i [log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) in tutte le sottoscrizioni di Azure. Il log attività di Azure offre informazioni approfondite sulle operazioni eseguite sulle risorse nelle sottoscrizioni. Il log attività era noto in precedenza come *log di controllo* o *log operativo* perché segnala eventi per le sottoscrizioni.

L'uso del log attività permette di acquisire *informazioni* *dettagliate* *su qualsiasi* operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. È anche possibile comprendere lo stato delle operazioni e altre proprietà pertinenti. Il log attività non include le operazioni di lettura (GET) o quelle per le risorse che usano il modello di distribuzione classica.

Quando si connettono i log attività di Azure a Log Analytics, è possibile:

- Analizzare i log attività con le viste predefinite
- Analizzare e cercare i log attività di più sottoscrizioni di Azure
- Conservare i log attività per più di 90 giorni<sup>1</sup>
- Correlare i log attività con altri dati dell'applicazione e della piattaforma di Azure
- Vedere le attività operative aggregate in base allo stato
- Visualizzare le tendenze delle attività in corso su ognuno dei servizi di Azure
- Segnalare le modifiche alle autorizzazioni in tutte le proprie risorse di Azure
- Identificare i problemi di integrità del servizio o le interruzioni che inficiano il funzionamento delle risorse
- Usare Ricerca log per correlare le attività degli utenti, le operazioni di ridimensionamento automatico, le modifiche alle autorizzazioni e l'integrità del servizio ad altri log o metriche provenienti dall'ambiente in uso

<sup>1</sup>Per impostazione predefinita, Log Analytics conserva i log attività di Azure per 90 giorni, anche per gli utenti del livello gratuito. Oppure, se si dispone di un'impostazione di conservazione dell'area di lavoro inferiore a 90 giorni. Se l'area di lavoro dispone di un criterio di conservazione superiore a 90 giorni, i log attività vengono conservati per il periodo di conservazione previsto per l'area di lavoro.

Log Analytics raccoglie i log attività e li conserva in archivio per 90 giorni senza costi aggiuntivi. Se si conservano in archivio i log per più di 90 giorni, verrà addebitato il costo di conservazione dei dati archiviati supplementare.

Per gli utenti che rientrano nel piano tariffario gratuito, i log attività non si applicano al loro consumo di dati giornaliero.

## <a name="connected-sources"></a>Origini connesse

A differenza della maggior parte delle soluzioni di Log Analytics, i dati per i log attività non vengono raccolti dagli agenti. Tutti i dati usati dalla soluzione provengono direttamente da Azure.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| [Agenti di Windows](log-analytics-windows-agent.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Windows. |
| [Agenti Linux](log-analytics-linux-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Linux. |
| [Gruppo di gestione SCOM](log-analytics-om-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti in un gruppo di gestione SCOM connesso. |
| [Account di archiviazione di Azure](log-analytics-azure-storage.md) | No | La soluzione non raccoglie le informazioni dall'Archiviazione di Azure. |

## <a name="prerequisites"></a>Prerequisiti

- L'accesso alle informazioni del log attività di Azure è consentito solo a chi dispone di una sottoscrizione di Azure.

## <a name="configuration"></a>Configurazione

Eseguire i passaggi seguenti per configurare la soluzione Log Analytics attività per le aree di lavoro.

1. Abilitare la soluzione Log Analytics attività da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](log-analytics-add-solutions.md).
2. Configurare i log attività per andare all'area di lavoro Log Analytics.
    1. Nel portale di Azure selezionare l'area di lavoro e fare clic su **Log attività di Azure**.
    2. Per ogni sottoscrizione, fare clic sul relativo nome.  
        ![aggiungere sottoscrizione](./media/log-analytics-activity/add-subscription.png)
    3. Nel pannello *SubscriptionName*, fare clic su **Connetti**.  
        ![connettere sottoscrizione](./media/log-analytics-activity/subscription-connect.png)

Se si aggiunge la soluzione usando il portale di OMS, verrà visualizzato il riquadro seguente. Accedere al portale di Azure per connettere una sottoscrizione di Azure all'area di lavoro.  
![esecuzione della valutazione](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione Log Analytics attività all'area di lavoro, il riquadro **Log attività di Azure** viene aggiunta al dashboard Panoramica. Questo riquadro mostra un conteggio del numero di record di attività di Azure per le sottoscrizioni di Azure a cui la soluzione ha accesso.

![Riquadro Log attività di Azure](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Visualizzare i log attività di Azure

Fare clic sul riquadro **Log attività di Azure** per aprire il dashboard **Log attività di Azure**. Il dashboard include i pannelli nella tabella seguente. Ogni panello elenca fino a 10 elementi corrispondenti ai criteri del pannello per lo scope e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.

Il log attività viene visualizzato solo *dopo* aver configurato i log attività per passare alla soluzione, così da non visualizzare i dati prima di allora.

| Pannello | Descrizione |
| --- | --- |
| Voci del Log attività di Azure | Mostra un grafico a barre dei principali totali di record di voci del log attività di Azure per l'intervallo di date selezionato e un elenco dei primi 10 chiamanti attività. Fare clic sul grafico a barre per eseguire una ricerca di log per <code>Type=AzureActivity</code>. Fare clic su un elemento chiamante per eseguire una ricerca di log che restituisce tutte le voci di log attività per tale elemento. |
| Log attività per stato | Mostra un grafico ad anello relativo allo stato del log attività di Azure per l'intervallo di date selezionato. Mostra anche un elenco dei primi dieci record di stato. Fare clic sul grafico per eseguire una ricerca di log per <code>Type=AzureActivity &#124; measure count() by ActivityStatus</code>. Fare clic su un elemento di stato per eseguire una ricerca di log che restituisce tutte le voci di log attività per il record di stato. |
| Log attività per risorsa | Indica il numero totale di risorse con i log attività ed elenca le prime dieci risorse con conteggi dei record di ciascuna risorsa. Fare clic sull'area totale per eseguire una ricerca di log per <code>Type=AzureActivity &#124; measure count() by Resource</code>, che mostra tutte le risorse di Azure disponibili per la soluzione. Fare clic su una risorsa per eseguire una ricerca di log che restituisce tutti i record di attività per quella risorsa. |
| Log attività per provider di risorse | Indica il numero totale di provider di risorse che producono log attività e ne elenca i primi dieci. Fare clic sull'area totale per eseguire una ricerca di log per <code>Type=AzureActivity &#124; measure count() by ResourceProvider</code>, che mostra tutti i provider di risorse di Azure. Fare clic su un provider di risorse per eseguire una ricerca di log che restituisce tutti i record di attività per quel provider. |

![Dashboard Log attività di Azure](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>Passaggi successivi

- Creare un [avviso](log-analytics-alerts-creating.md) quando si verifica un'attività specifica.
- Usare [Ricerca Log](log-analytics-log-searches.md) per visualizzare le informazioni dettagliate dai log attività.

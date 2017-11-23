---
title: "Novità di Azure Log Analytics | Microsoft Docs"
description: "Questo articolo riporta le domande più frequenti riguardanti l'aggiornamento di Log Analytics al nuovo linguaggio di query."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: bwren
ms.openlocfilehash: 783223a37c2a13c9affbf382209ca2aa4f1ba4c7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2017
---
# <a name="whats-changed-in-azure-log-analytics"></a>Novità di Azure Log Analytics
Oltre al linguaggio di query stesso, sono stati introdotti vari miglioramenti e modifiche di cui è necessario essere a conoscenza quando l'area di lavoro di Log Analytics viene [aggiornata al nuovo linguaggio di query](log-analytics-log-search-new.md).  Questo articolo descrive brevemente i cambiamenti tra l'area di lavoro precedente e quella aggiornata, con collegamenti ai contenuti dettagliati per ciascuna. 

Per una descrizione degli eventuali problemi noti dell'aggiornamento e risposte alle domande frequenti, vedere [Nuova ricerca log in Log Analytics - domande frequenti e problemi noti](log-analytics-log-search-faq.md).  

## <a name="query-language"></a>Linguaggio di query
La modifica principale nell'aggiornamento di Log Analytics è il nuovo linguaggio di query, che presenta importanti miglioramenti rispetto al linguaggio precedente.  

Alla pagina [Transizione al nuovo linguaggio di query di Azure Log Analytics](log-analytics-log-search-transition.md) è possibile trovare un confronto diretto tra le operazioni comuni del linguaggio precedente e quelle del nuovo linguaggio.  La documentazione completa sul nuovo linguaggio è disponibile alla pagina [Azure Log Analytics Query Language](https://docs.loganalytics.io) (Linguaggio di query di Log Analytics).


## <a name="computer-groups"></a>Gruppi di computer
Il metodo per creare un gruppo di computer non è cambiato, anche se è ora necessario specificare un alias univoco per ogni gruppo.  I gruppi di computer basati sulle ricerche log devono usare la sintassi del nuovo linguaggio.

Esiste una nuova sintassi per usare i gruppi di computer in una ricerca log.  Invece di usare la funzione $ComputerGroups, ora i gruppi di computer vengono implementati come funzione con un alias univoco.  L'alias nella ricerca log viene usato come qualsiasi altra funzione.  

Informazioni dettagliate sono disponibili in [Gruppi di computer nelle ricerche log in Log Analytics](log-analytics-computer-groups.md).


## <a name="log-search-portals"></a>Portali per la ricerca log
Oltre al portale di ricerca log per creare ed eseguire ricerche log, ora è possibile usare il portale Advanced Analytics, che offre funzionalità di modifica notevolmente migliorate.

Una breve descrizione dei due portali è disponibile in [Portali per la creazione e la modifica di ricerche log in Azure Log Analytics](log-analytics-log-search-portals.md).  È possibile partecipare a un'esercitazione sul nuovo portale in [Getting Started with the Analytics Portal](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) (Introduzione al portale Analytics).

## <a name="alerts"></a>Avvisi
Gli avvisi funzionano allo stesso modo nelle aree di lavoro aggiornate, tuttavia la query che eseguono deve essere scritta nel nuovo linguaggio.  Le eventuali regole di avviso esistenti prima dell'aggiornamento verranno convertite automaticamente nel nuovo linguaggio.  Maggiori dettagli sono disponibili in [Informazioni sugli avvisi in Log Analytics](log-analytics-alerts.md).

Il formato del payload per runbook e webhook inviati dagli avvisi è cambiato.  Informazioni dettagliate sul nuovo formato del payload sono disponibili in [Aggiungere azioni alle regole di avviso in Log Analytics](log-analytics-alerts-actions.md).

## <a name="dashboards"></a>Dashboard
I [dashboard](log-analytics-dashboards.md) stanno per diventare deprecati.  È possibile continuare a usare tutti i riquadri aggiunti al dashboard prima dell'aggiornamento dell'area di lavoro, ma non sarà possibile modificarli o crearne di nuovi.  Usare Progettazione vista per creare visualizzazioni personalizzate con un set di funzionalità più ricco dei dashboard.

I dettagli su Progettazione vista sono disponibili in [Usare Progettazione vista per creare viste personalizzate in Log Analytics](log-analytics-view-designer.md).

## <a name="power-bi"></a>Power BI
Il processo di esportazione dei dati di Log Analytics in Power BI è stato modificato per le aree di lavoro aggiornate e le eventuali pianificazioni esistenti create prima dell'aggiornamento verranno disabilitate.  

I dettagli sono disponibili in [Esportare i dati di Log Analytics in Power BI](log-analytics-powerbi.md).

## <a name="powershell"></a>PowerShell
Get-AzureRmOperationalInsightsSearchResults per eseguire le ricerche log da PowerShell non funziona con un'area di lavoro aggiornata.  Usare Invoke-LogAnalyticsQuery per questa funzionalità con un'area di lavoro aggiornata.

I dettagli sono disponibili in [Azure Log Analytics REST API - PowerShell Cmdlets](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) (API REST di Azure Log Analytics - Cmdlet PowerShell).

## <a name="log-search-api"></a>API di ricerca nei log
L'API REST di ricerca log è cambiata e molte soluzioni che usano la versione precedente dovranno essere aggiornate per usare la nuova versione dell'API.   

I dettagli sulla nuova versione dell'API sono disponibili in [Azure Log Analytics REST API](https://dev.loganalytics.io/) (API REST di Azure Log Analytics).

## <a name="next-steps"></a>Passaggi successivi

- Per una descrizione degli eventuali problemi noti dell'aggiornamento e risposte alle domande frequenti, vedere [Nuova ricerca log in Log Analytics - domande frequenti e problemi noti](log-analytics-log-search-faq.md).
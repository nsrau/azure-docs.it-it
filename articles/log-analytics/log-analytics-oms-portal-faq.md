---
title: Domande frequenti sulla transizione dal portale di OMS al portale di Azure per gli utenti di Log Analytics | Microsoft Docs
description: Risposte alle domande più frequenti sulla transizione degli utenti di Log Analytics dal portale OMS al portale di Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 503d5913efe67bd0de738f68921b9631c63acfa8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116045"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Domande frequenti sulla transizione dal portale di OMS al portale di Azure per gli utenti di Log Analytics
In precedenza, Log Analytics usava il proprio portale, chiamato portale di OMS, per gestire la configurazione e l'analisi dei dati raccolti.  Tutte le funzionalità di questo portale sono state spostate al portale di Azure in cui continueranno a essere sviluppate.

In questo articolo vengono offerte risposte alle domande frequenti degli utenti interessati da questa transizione.  Gli utenti abituati a usare Log Analytics nel portale di OMS troveranno indicazioni utili su come eseguire le stesse attività nel portale di Azure.

## <a name="do-i-need-to-migrate-anything"></a>È necessario eseguire la migrazione di eventuali elementi?
No. Non sono presenti modifiche da apportare a Log Analytics, pertanto non è necessario eseguire la migrazione di alcun elemento. L'unico elemento modificato è l'interfaccia usata per l'accesso. È ora possibile, infatti, usare il portale di Azure per accedere alle stesse aree di lavoro, soluzioni, viste e ricerche log usate attualmente nel portale di OMS.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Come trovare Log Analytics in Azure?
Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).  Fare clic su **Tutti i servizi** e nell'elenco di risorse digitare **Log Analytics**. Selezionare **Log Analytics** e selezionare l'area di lavoro. Viene visualizzata la pagina di riepilogo dell'area di lavoro.

![Area di lavoro di Log Analytics](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Come si gestiscono le autorizzazioni?
Se non si ha accesso all'area di lavoro Log Analytics nel portale di Azure, è necessario configurare le autorizzazioni usando l'[accesso in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md). Per informazioni dettagliate sulla gestione delle autorizzazioni dell'area di lavoro, vedere [Gestire le aree di lavoro](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Per informazioni sulla gestione delle autorizzazioni per gli avvisi, vedere [Introduzione a ruoli, autorizzazioni e sicurezza con Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Come si crea una nuova area di lavoro? 
Dall'elenco delle aree di lavoro nel portale di Azure fare clic su **Aggiungi**.  Per informazioni più complete, vedere [Creare un'area di lavoro di Log Analytics nel portale di Azure](../log-analytics/log-analytics-quick-create-workspace.md).

![Pagina di panoramica](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Dove si trova la pagina della panoramica?
La schermata principale del portale di OMS contiene i riquadri per tutte le soluzioni di gestione installate nell'area di lavoro e le visualizzazioni personalizzate create. La stessa visualizzazione è disponibile nel portale di Azure. Nell'area di lavoro selezionare **Workspace Summary** (Riepilogo area di lavoro).

![Pagina di panoramica](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Come aprire la ricerca log e la progettazione vista?
Sia la **ricerca log** che la **progettazione vista** sono disponibili nella pagina principale e nel menu a sinistra dell'area di lavoro del portale di Azure, a destra accanto a **Informazioni generali**.

## <a name="where-do-i-find-settings"></a>Dove si trovano le impostazioni?
Molte delle impostazioni della sezione **Impostazioni** del portale di OMS sono disponibili nel menu **Impostazioni avanzate** del portale di Azure per l'area di lavoro.

![Impostazioni avanzate](media/log-analytics-new-portal/advanced-settings.png)

Le sezioni seguenti offrono un elenco completo delle modalità per accedere alle impostazioni che in precedenza erano disponibili nella sezione **Impostazioni** del portale di OMS.

### <a name="accounts"></a>Account 
Le impostazioni dell'account vengono gestite in diversi punti del portale di Azure come descritto nella tabella seguente.

| Impostazione nel portale di OMS | Equivalente nel portale di Azure |
|:---|:---|
| Account di automazione | Menu **Account di Automazione** per l'area di lavoro. |
| Sottoscrizioni di Azure e piano dati | Menu **Piano tariffario** per l'area di lavoro. |
| Gestire gli utenti | Usare l'accesso in base al ruolo di Azure per [gestire le autorizzazioni per l'area di lavoro](#how-do-i-manage-permissions). |
| Informazioni area di lavoro | Informazioni disponibili nel menu **area di lavoro OMS** per l'area di lavoro. |

### <a name="alerts"></a>Avvisi
Le regole di avviso basate sulle query di Log Analytics vengono ora gestite nella [visualizzazione unificata degli avvisi](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Gruppi di computer
Gestire i gruppi di computer nel menu **Impostazioni avanzate** per l'area di lavoro. 

### <a name="connected-sources"></a>Origini connesse
Gestire le impostazioni delle origini connesse nel menu **Impostazioni avanzate** per l'area di lavoro. Nella tabella seguente vengono specificati i dettagli per ogni sezione del menu.

| Impostazione nel portale di OMS | Equivalente nel portale di Azure |
|:---|:---|
| Server Windows   | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Server Linux   | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Archiviazione di Azure     | Menu **Impostazioni avanzate** per l'area di lavoro. |
| System Center     | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Office 365        | Per i dettagli di configurazione, vedere la [documentazione per la soluzione di gestione di Office 365](../operations-management-suite/oms-solution-office-365.md). |
| Telemetria di Windows | Non ancora disponibile nel portale di Azure. |
| Connettore di Gestione dei servizi IT    | Per le istruzioni sulla connessione al servizio Gestione dei servizi IT con Log Analytics, vedere [Connettere prodotti e servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT](../log-analytics/log-analytics-itsmc-connections.md). |

### <a name="data"></a>Dati
Gestire la maggior parte delle impostazioni dei dati nel menu **Impostazioni avanzate** per l'area di lavoro. Nella tabella seguente vengono specificati i dettagli per ogni sezione del menu.

| Impostazione nel portale di OMS | Equivalente nel portale di Azure |
|:---|:---|
| Registri eventi di Windows           | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Contatori delle prestazioni di Windows | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Contatori delle prestazioni di Linux   | Menu **Impostazioni avanzate** per l'area di lavoro. |
| log di IIS                     | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Campi personalizzati                | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Log personalizzati                  | Menu **Impostazioni avanzate** per l'area di lavoro. |
| syslog                       | Menu **Impostazioni avanzate** per l'area di lavoro. |
| Application Insights         | La soluzione è stata deprecata, dal momento che Log Analytics e Application Insights condividono lo stesso motore di dati.  |
| Rilevamento file di Windows        | Menu **Rilevamento modifiche** in Automazione di Azure. Per altri dettagli, vedere [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/automation-change-tracking.md). |
| Rilevamento del Registro di sistema di Windows        | Menu **Rilevamento modifiche** in Automazione di Azure. Per altri dettagli, vedere [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/automation-change-tracking.md). |
| Rilevamento file di Linux          | Menu **Rilevamento modifiche** in Automazione di Azure. Per altri dettagli, vedere [Rilevare le modifiche nell'ambiente in uso con la soluzione di rilevamento modifiche](../automation/automation-change-tracking.md). |

### <a name="solutions"></a>Soluzioni
Gestire le soluzioni nel menu **Soluzioni** per l'area di lavoro. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Come installare e rimuovere le soluzioni di gestione?
Nel portale di OMS le soluzioni di gestione vengono installate dalla Raccolta soluzioni ed eliminate da **Impostazioni**. Nel portale di Azure è possibile [installare le soluzione di gestione](../monitoring/monitoring-solutions.md#install-a-management-solution) da Microsoft Azure Marketplace. [Rimuovere le soluzioni](../monitoring/monitoring-solutions.md#remove-a-management-solution) dall'elenco delle soluzioni installate.

## <a name="how-do-i-create-and-manage-alerts"></a>Come creare e gestire gli avvisi?
Le regole di avviso basate sulle query di Log Analytics vengono ora gestite nella [visualizzazione unificata degli avvisi](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Per altri dettagli sulla configurazione e l'uso di avvisi nel portale di Azure, vedere [Come estendere gli avvisi di Log Analytics a Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md).

## <a name="how-do-i-access-my-dashboards"></a>Come si accede ai dashboard personali?
I [dashboard](../log-analytics/log-analytics-dashboards.md) in Log Analytics sono stati deprecati.  È possibile visualizzare i dati in Log Analytics usando la [progettazione vista](../log-analytics/log-analytics-view-designer.md) che ha altre funzionalità per aggiungere query e viste ai dashboard di Azure.

## <a name="how-do-i-check-my-usage"></a>Come è possibile controllare l'utilizzo?
Ora è possibile visualizzare e gestire facilmente l'utilizzo e il costo di Log Analytics selezionando **Utilizzo e costi stimati** nell'area di lavoro.

![Utilizzo e costi stimati](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>È possibile usare ancora il portale classico?
Per un periodo di tempo limitato è ancora possibile accedere al portale tramite l'URL seguente, con il nome della propria area di lavoro: https://\<nome dell'area di lavoro\>.portal.mms.microsoft.com. È consigliabile comunque usare il portale di Azure e offrire commenti e suggerimenti all'indirizzo LAUpgradeFeedback@microsoft.com in caso di problemi che causano un blocco.

## <a name="next-steps"></a>Passaggi successivi

- [Trovare e installare le soluzioni di gestione](../monitoring/monitoring-solutions.md) tramite il portale di Azure.
- Informazioni sulla [Ricerca log nel portale di Azure](log-analytics-log-search-portals.md).
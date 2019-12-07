---
title: Panoramica dei log della piattaforma Azure | Microsoft Docs
description: Panoramica dei log in monitoraggio di Azure che forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 480c028f11de9a7c44168b217ad3553d721d01e1
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894558"
---
# <a name="overview-of-azure-platform-logs"></a>Panoramica dei log della piattaforma Azure
I log della piattaforma forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Vengono generati automaticamente anche se è necessario configurare determinati log della piattaforma da inviare a una o più destinazioni da mantenere. Questo articolo fornisce una panoramica dei log della piattaforma, incluse le informazioni fornite e come è possibile configurarle per la raccolta e l'analisi.

## <a name="types-of-platform-logs"></a>Tipi di log della piattaforma
La tabella seguente elenca i log della piattaforma specifici disponibili in diversi livelli di Azure.

| Livello | Log | Description |
|:---|:---|:---|
| Risorse di Azure | [Log delle risorse](resource-logs-overview.md) | Fornire informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il *piano dati*), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa.<br>*I log delle risorse sono stati indicati in precedenza come log di diagnostica.*  |
| Sottoscrizione di Azure | [Log attività](activity-logs-overview.md) | Fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (*il piano di gestione*) oltre agli aggiornamenti sugli eventi di integrità del servizio. È disponibile un singolo log attività per ogni sottoscrizione di Azure.   |
| Tenant di Azure | [Log di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md)  | Contiene la cronologia dell'attività di accesso e audit trail delle modifiche apportate nel Azure Active Directory per un tenant specifico.   |


![Panoramica dei log della piattaforma](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>Visualizzazione dei log della piattaforma
È possibile visualizzare il [log attività](activity-log-view.md) e i [log Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) nel portale di Azure. Per visualizzarli, è necessario inviare i log delle risorse a una [destinazione](#destinations) .


## <a name="destinations"></a>Destinations
È possibile inviare i log della piattaforma a una o più destinazioni nella tabella seguente, a seconda dei requisiti di monitoraggio. 

| Destinazione | Scenario | Riferimenti |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Analizzare i log con altri dati di monitoraggio e sfruttare le funzionalità di monitoraggio di Azure, ad esempio query e avvisi del log. | [Log delle risorse](resource-logs-collect-storage.md)<br>[Log attività](activity-log-collect.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Archiviazione di Azure | Archiviare i log per il controllo, l'analisi statica o il backup. |[Log delle risorse](archive-diagnostic-logs.md)<br>[Log attività](activity-log-export.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub eventi | Trasmettere i log a sistemi di registrazione e telemetria di terze parti.  |[Log delle risorse](resource-logs-stream-event-hubs.md)<br>[Log attività](activity-log-export.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>Impostazioni di diagnostica e profili di log
Configurare le destinazioni per i log delle risorse e i log di Azure Active Directory [creando un'impostazione di diagnostica](diagnostic-settings.md). Configurare le destinazioni per il log attività [creando un profilo di log](activity-log-export.md) o [collegandolo a un'area di lavoro log Analytics](activity-log-collect.md).

L'impostazione di diagnostica e il profilo di log definiscono gli elementi seguenti:

- Una o più destinazioni per inviare i log e le metriche selezionati.
- Le categorie di log e le metriche dalla risorsa vengono inviate alle destinazioni.
- Se è stato selezionato un account di archiviazione come destinazione, per quanto tempo ogni categoria di log deve essere mantenuta.



## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](activity-logs-overview.md)
* [Altre informazioni sui log delle risorse](resource-logs-overview.md)
* [Visualizzare lo schema del log delle risorse per i diversi servizi di Azure](diagnostic-logs-schema.md)

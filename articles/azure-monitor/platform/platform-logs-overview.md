---
title: Panoramica dei log della piattaforma Azure | Microsoft Docs
description: Panoramica dei log in monitoraggio di Azure che forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 89de6b3737c8a1e91832aba8f749078806b64e90
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659321"
---
# <a name="overview-of-azure-platform-logs"></a>Panoramica dei log della piattaforma Azure
I log della piattaforma forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Vengono generati automaticamente anche se è necessario configurare determinati log della piattaforma da inviare a una o più destinazioni da mantenere. Questo articolo fornisce una panoramica dei log della piattaforma, incluse le informazioni fornite e come è possibile configurarle per la raccolta e l'analisi.

## <a name="types-of-platform-logs"></a>Tipi di log della piattaforma
La tabella seguente elenca i log della piattaforma specifici disponibili in diversi livelli di Azure.

| File di log | Livello | Descrizione |
|:---|:---|:---|
| Log risorse | Risorse di Azure | Fornire informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il *piano dati*), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa.<br><br>*I log delle risorse sono stati indicati in precedenza come log di diagnostica.*  |
| Log attività | Sottoscrizione di Azure | Fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (*il piano di gestione*) oltre agli aggiornamenti sugli eventi di integrità del servizio. Usare il log attività per _determinare l'elemento_, _chi_e _quando_ per qualsiasi operazione di scrittura (Put, post, Delete) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche.  È disponibile un singolo log attività per ogni sottoscrizione di Azure. |
| Log di Azure Active Directory | Tenant di Azure |  Contiene la cronologia dell'attività di accesso e audit trail delle modifiche apportate nel Azure Active Directory per un tenant specifico. Per una descrizione completa dei log di Azure Active Directory, vedere [informazioni sui report Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) .   |

> [!NOTE]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. È possibile esaminare il log delle operazioni in una sezione distinta del portale.

![Panoramica dei log della piattaforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visualizzazione dei log della piattaforma
Sono disponibili diverse opzioni per la visualizzazione e l'analisi dei diversi log della piattaforma Azure.

- Visualizzare il log attività nel portale di Azure e accedere agli eventi da PowerShell e dall'interfaccia della riga di comando. Per informazioni dettagliate, vedere [visualizzare e recuperare gli eventi del log attività di Azure](activity-log-view.md) . 
- Visualizzare Azure Active Directory report di sicurezza e attività nel portale di Azure. Vedere [che cosa sono i report Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  per informazioni dettagliate.
- I log delle risorse vengono generati automaticamente dalle risorse di Azure supportate, ma non sono disponibili per essere visualizzati a meno che non vengano inviati a una [destinazione](#destinations). 

## <a name="destinations"></a>Destinations
È possibile inviare i log della piattaforma a una o più destinazioni nella tabella seguente, a seconda dei requisiti di monitoraggio. Configurare le destinazioni per i log della piattaforma [creando un'impostazione di diagnostica](diagnostic-settings.md).

| Destination | Scenario | Riferimenti |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Analizzare i log con altri dati di monitoraggio e sfruttare le funzionalità di monitoraggio di Azure, ad esempio query e avvisi del log. | [Log attività e log delle risorse](resource-logs-collect-workspace.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Archiviazione di Azure | Archiviare i log per il controllo, l'analisi statica o il backup. |[Log attività e log delle risorse](archive-diagnostic-logs.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub eventi | Trasmettere i log a sistemi di registrazione e telemetria di terze parti.  |[Log attività e log delle risorse](resource-logs-stream-event-hubs.md)<br>[Log di Azure Activity directory](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo schema del log attività per diverse categorie](activity-log-schema.md)
* [Visualizzare lo schema del log delle risorse per i diversi servizi di Azure](diagnostic-logs-schema.md)

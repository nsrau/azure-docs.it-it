---
title: Panoramica dei log della piattaforma Azure | Microsoft Docs
description: Panoramica dei log in monitoraggio di Azure che forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ea960e93dba634573ec1ef594f1d2c49be57ca9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945308"
---
# <a name="overview-of-azure-platform-logs"></a>Panoramica dei log della piattaforma di Azure
I log della piattaforma forniscono informazioni dettagliate di diagnostica e controllo per le risorse di Azure e la piattaforma Azure da cui dipendono. Vengono generati automaticamente anche se è necessario configurare determinati log della piattaforma da inviare a una o più destinazioni da mantenere. Questo articolo fornisce una panoramica dei log della piattaforma, incluse le informazioni fornite e come è possibile configurarle per la raccolta e l'analisi.

## <a name="types-of-platform-logs"></a>Tipi di log della piattaforma
La tabella seguente elenca i log della piattaforma specifici disponibili in diversi livelli di Azure.

| File di log | Livello | Descrizione |
|:---|:---|:---|
| [Log risorse](resource-logs.md) | Risorse di Azure | Fornire informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il *piano dati*), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto dei log delle risorse varia in base al servizio di Azure e al tipo di risorsa.<br><br>*I log delle risorse sono stati indicati in precedenza come log di diagnostica.*  |
| [Log attività](activity-log.md) | Sottoscrizione di Azure | Fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (*il piano di gestione*) oltre agli aggiornamenti sugli eventi di integrità del servizio. Usare il log attività per _determinare l'elemento_, _chi_e _quando_ per qualsiasi operazione di scrittura (Put, post, Delete) eseguita sulle risorse nella sottoscrizione. È disponibile un singolo log attività per ogni sottoscrizione di Azure. |
| [Log di Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) | Tenant di Azure |  Contiene la cronologia dell'attività di accesso e audit trail delle modifiche apportate nel Azure Active Directory per un tenant specifico.   |

> [!NOTE]
> Il log attività di Azure è destinato principalmente alle attività che si verificano in Azure Resource Manager. Non tiene traccia delle risorse che usano il modello classico/RDFE. Alcuni tipi di risorse classiche dispongono di un provider di risorse proxy in Azure Resource Manager (ad esempio, Microsoft.ClassicCompute). Se un utente interagisce con un tipo di risorsa classica tramite Azure Resource Manager con questi provider di risorse di proxy, le operazioni verranno visualizzate nel log attività. Se un utente interagisce con un tipo di risorsa classica all'esterno dei proxy di Azure Resource Manager, le azioni dell'utente verranno registrate solo nel log delle operazioni. È possibile esaminare il log delle operazioni in una sezione distinta del portale.

![Panoramica dei log della piattaforma](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Visualizzazione dei log della piattaforma
Sono disponibili diverse opzioni per la visualizzazione e l'analisi dei diversi log della piattaforma Azure.

- Visualizzare il log attività nel portale di Azure e accedere agli eventi da PowerShell e dall'interfaccia della riga di comando. Per informazioni dettagliate, vedere [visualizzare il log attività](activity-log.md#view-the-activity-log) . 
- Visualizzare Azure Active Directory report di sicurezza e attività nel portale di Azure. Vedere [che cosa sono i report Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  per informazioni dettagliate.
- I log delle risorse vengono generati automaticamente dalle risorse di Azure supportate, ma non sono disponibili per essere visualizzati a meno che non vengano inviati a una [destinazione](#destinations). 

## <a name="destinations"></a>Destinations
È possibile inviare i log della piattaforma a una o più destinazioni nella tabella seguente, a seconda dei requisiti di monitoraggio. Configurare le destinazioni per i log della piattaforma [creando un'impostazione di diagnostica](diagnostic-settings.md).

| Destination | Descrizione |
|:---|:---|
| Area di lavoro Log Analytics | Analizzare i log di tutte le risorse di Azure insieme e sfruttare tutte le funzionalità disponibili per i [log di monitoraggio di Azure](data-platform-logs.md) , incluse le [query di log](../log-query/log-query-overview.md) e gli [avvisi del log](alerts-log.md). Aggiungere i risultati di una query di log a un dashboard di Azure o includerlo in una cartella di lavoro come parte di un report interattivo. |  |
| Hub eventi | Inviare i dati dei log della piattaforma all'esterno di Azure, ad esempio a una piattaforma SIEM o di telemetria personalizzata di terze parti.
| Archiviazione di Azure | Archiviare i log per il controllo o il backup. |

- Per informazioni dettagliate sulla creazione di un'impostazione di diagnostica per log attività o log delle risorse, vedere [creare impostazioni di diagnostica per inviare i log e le metriche della piattaforma a destinazioni diverse](diagnostic-settings.md). 
- Per informazioni dettagliate sulla creazione di un'impostazione di diagnostica per i log di Azure Active Directory, vedere gli articoli seguenti.
  - [Integrare log di Azure AD con i log di monitoraggio di Azure](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Esercitazione: trasmettere i log di Azure Active Directory a un hub eventi di Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Esercitazione: archiviare i log dei Azure AD in un account di archiviazione di Azure](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sul log attività](activity-log.md)
* [Altre informazioni sui log delle risorse](resource-logs.md)


---
title: Come configurare il monitoraggio in Gemelli digitali di Azure | Microsoft Docs
description: Come configurare il monitoraggio in Gemelli digitali di Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 08/09/2019
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: dd1481c74cdf519dcb4588e0b7cf09b7b6ab5f1d
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950114"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Come configurare il monitoraggio in Gemelli digitali di Azure

Gemelli digitali di Azure supporta potenti funzionalità di registrazione, monitoraggio e analisi. Gli sviluppatori di soluzioni possono usare i log di monitoraggio di Azure, i log di diagnostica, la registrazione delle attività e altri servizi per supportare le complesse esigenze di monitoraggio di un'app. Le opzioni di registrazione possono essere combinate per eseguire query o visualizzare i record in diversi servizi e per fornire una copertura di registrazione granulare per numerosi servizi.

Questo articolo fornisce un riepilogo delle opzioni di registrazione e monitoraggio e illustra come combinarle specificamente per Gemelli digitali di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Rivedere i log attività

I [log attività](../azure-monitor/platform/activity-logs-overview.md) di Azure forniscono informazioni rapide sulle cronologie di eventi e operazioni a livello di sottoscrizione per ogni istanza del servizio di Azure.

Gli eventi a livello di sottoscrizione includono:

* Creazione di risorse
* Rimozione di risorse
* Creazione di segreti per app
* Integrazione con altri servizi

La registrazione delle attività per Gemelli digitali di Azure è abilitata per impostazione predefinita e può essere trovata tramite il portale di Azure seguendo questa procedura:

1. Selezionare l'istanza di Gemelli digitali di Azure.
1. Scegliere **Log attività** per aprire il pannello di visualizzazione:

    [![Log attività](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Per la registrazione avanzata delle attività:

1. Selezionare l'opzione **Log** per visualizzare **Panoramica di Analisi log attività**:

    [![Selezione](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. **Panoramica di Analisi log attività** fornisce un riepilogo dei dati fondamentali dei log attività:

    [![Panoramica di analisi log attività]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Usare i **log attività** per visualizzare informazioni rapide sugli eventi a livello di sottoscrizione.

## <a name="enable-customer-diagnostic-logs"></a>Abilitare i log di diagnostica dei clienti

Per integrare la registrazione delle attività, è possibile configurare le [impostazioni di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md) di Azure per ogni istanza di Azure. Mentre i log attività si riferiscono agli eventi a livello di sottoscrizione, la registrazione diagnostica fornisce informazioni approfondite sulla cronologia operativa delle risorse.

Esempi di registrazione diagnostica includono:

* Il tempo di esecuzione di una funzione definita dall'utente
* Il codice di stato della risposta di una richiesta API con esito positivo
* Il recupero di una chiave dell'app da un insieme di credenziali

Per abilitare i log di diagnostica per un'istanza:

1. Aprire la risorsa nel portale di Azure.
1. Selezionare **le impostazioni di diagnostica**:

    [![Impostazioni di diagnostica 1](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Selezionare **attiva diagnostica** per raccogliere dati (se non è stata abilitata in precedenza).
1. Compilare i campi richiesti e scegliere come e dove salvare i dati:

    [![Impostazioni di diagnostica due](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    I log di diagnostica vengono spesso salvati con [archiviazione file di Azure](../storage/files/storage-files-deployment-guide.md) e condivisi con i [log di monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md). È possibile selezionare entrambe le opzioni.

>[!TIP]
>Usare i **log di diagnostica** per informazioni dettagliate sulle operazioni relative alle risorse.

## <a name="azure-monitor-and-log-analytics"></a>Monitoraggio di Azure e Log Analytics

Le applicazioni IoT collegano numerosi dispositivi, risorse, posizioni e dati in un'unica piattaforma. La registrazione con granularità fine fornisce informazioni dettagliate su ogni parte, servizio o componente specifico dell'architettura complessiva dell'applicazione, ma è spesso necessaria una panoramica unificata per le attività di manutenzione e debug.

Monitoraggio di Azure include il potente servizio log Analytics, che consente di visualizzare e analizzare le origini di registrazione in un'unica posizione. Monitoraggio di Azure è pertanto estremamente utile per analizzare i log all'interno di app IoT avanzate.

Alcuni esempi d'uso sono:

* L'esecuzione di query su più cronologie di log di diagnostica
* La visualizzazione di log per diverse funzioni definite dall'utente
* La visualizzazione di log per due o più servizi in un intervallo di tempo specifico

L'esecuzione di query di log complete viene eseguita tramite i [log di monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md). Per configurare queste funzionalità avanzate:

1. Cercare **Log Analytics** nel portale di Azure.
1. Vengono visualizzate le istanze dell' **area di lavoro log Analytics** disponibili. Sceglierne una e selezionare **Log** per eseguire query:

    [![Log Analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Se non si dispone già di un'istanza dell' **area di lavoro log Analytics** , è possibile creare un'area di lavoro selezionando il pulsante **Aggiungi** :

    [![Creare OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Una volta eseguito il provisioning dell'istanza dell' **area di lavoro log Analytics** , è possibile usare query potenti per trovare voci nei log multipli o eseguire ricerche usando criteri specifici usando la **gestione dei log**:

   [![Gestione dei log](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Per altre informazioni sulle operazioni avanzate relative alle query, vedere [Introduzione alle query](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> È possibile che si verifichi un ritardo di 5 minuti quando si inviano eventi a **log Analytics area di lavoro** per la prima volta.

I log di monitoraggio di Azure forniscono anche potenti servizi di notifica degli avvisi e degli errori, che possono essere visualizzati selezionando **diagnostica e Risolvi i problemi**:

   [![Notifiche di avviso e di errore](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Usare **log Analytics area di lavoro** per eseguire query sulle cronologie dei log per più funzionalità, sottoscrizioni o servizi dell'app.

## <a name="other-options"></a>Altre opzioni

Gemelli digitali di Azure supporta anche la registrazione e il controllo di sicurezza specifici per l'applicazione. Per una panoramica completa di tutte le opzioni di registrazione di Azure disponibili per l'istanza di Gemelli digitali di Azure, vedere l'articolo [Registrazione e controllo di Azure](../security/fundamentals/log-audit.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log attività](../azure-monitor/platform/activity-logs-overview.md) di Azure

- Approfondimento sulle impostazioni di diagnostica di Azure con la lettura di una [panoramica dei log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md)

- Scopri di più sui [log di monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md).

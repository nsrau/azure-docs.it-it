---
title: Come configurare il monitoraggio in Gemelli digitali di Azure | Microsoft Docs
description: Come configurare il monitoraggio in Gemelli digitali di Azure.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 3c35633c9b25eafdb738ed591c7c7022fbd8149a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967815"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Come configurare il monitoraggio in Gemelli digitali di Azure

Gemelli digitali di Azure supporta potenti funzionalità di registrazione, monitoraggio e analisi. Gli sviluppatori di soluzioni possono usare i log, i log di diagnostica, registrazione delle attività e altri servizi di monitoraggio di Azure per supportare le complesse esigenze di monitoraggio di un'app IoT. Le opzioni di registrazione possono essere combinate per eseguire query o visualizzare i record in diversi servizi e per fornire una copertura di registrazione granulare per numerosi servizi.

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

    ![Log attività][1]

Per la registrazione avanzata delle attività:

1. Selezionare l'opzione **Log** per visualizzare **Panoramica di Analisi log attività**:

    ![Selezione][2]

1. **Panoramica di Analisi log attività** fornisce un riepilogo dei dati fondamentali dei log attività:

    ![Panoramica di analisi dei log di attività][3]

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
1. Fare clic su **Impostazioni di diagnostica**:

    ![Impostazioni di diagnostica uno][4]

1. Fare clic su **Abilita diagnostica** per raccogliere i dati (se l'opzione non era stata abilitata in precedenza).
1. Compilare i campi richiesti e scegliere come e dove salvare i dati:

    ![Impostazioni di diagnostica due][5]

    I log di diagnostica vengono spesso salvati utilizzando [archiviazione File di Azure](../storage/files/storage-files-deployment-guide.md) e condivisi con [monitoraggio di Azure registra](../azure-monitor/log-query/get-started-portal.md). È possibile selezionare entrambe le opzioni.

>[!TIP]
>Usare i **log di diagnostica** per informazioni dettagliate sulle operazioni relative alle risorse.

## <a name="azure-monitor-and-log-analytics"></a>Monitoraggio di Azure e Log Analytics

Le applicazioni IoT collegano numerosi dispositivi, risorse, posizioni e dati in un'unica piattaforma. La registrazione con granularità fine fornisce informazioni dettagliate su ogni parte, servizio o componente specifico dell'architettura complessiva dell'applicazione, ma è spesso necessaria una panoramica unificata per le attività di manutenzione e debug.

Monitoraggio di Azure include il servizio di analitica log potente, che consente la registrazione delle origini per essere visualizzati e analizzati in un'unica posizione. Monitoraggio di Azure è pertanto estremamente utile per analizzare i log all'interno di app IoT avanzate.

Alcuni esempi d'uso sono:

* L'esecuzione di query su più cronologie di log di diagnostica
* La visualizzazione di log per diverse funzioni definite dall'utente
* La visualizzazione di log per due o più servizi in un intervallo di tempo specifico

L'esecuzione di query di log completo viene fornito tramite [monitoraggio di Azure registra](../azure-monitor/log-query/log-query-overview.md). Per configurare queste funzionalità avanzate:

1. Cercare **Log Analytics** nel portale di Azure.
1. Verrà visualizzato il disponibili **dell'area di lavoro di Log Analitica** istanze. Sceglierne una e selezionare **Log** per eseguire query:

    ![Log Analytics][6]

1. Se si ha già un **dell'area di lavoro di Log Analitica** istanza, è possibile creare un'area di lavoro facendo il **Add** pulsante:

    ![Creare OMS][7]

Una volta il **dell'area di lavoro di Log Analitica** istanza viene eseguito il provisioning, è possibile usare query avanzate per trovare le voci nei log multipli o eseguire la ricerca usando i criteri specifici usando **Logmanagement**:

   ![Gestione log][8]

Per altre informazioni sulle operazioni avanzate relative alle query, vedere [Introduzione alle query](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Che si verifichi un ritardo di 5 minuti quando si inviano eventi a **dell'area di lavoro di Log Analitica** per la prima volta.

Log di monitoraggio di Azure offre anche potenti errore e i servizi di notifica di avviso, che possono essere visualizzati facendo clic **diagnosticare e risolvere i problemi**:

   ![Notifiche di avvisi ed errori][9]

>[!TIP]
>Uso **dell'area di lavoro di Log Analitica** a cronologie dei log di query per più funzionalità di app, le sottoscrizioni o i servizi.

## <a name="other-options"></a>Altre opzioni

Gemelli digitali di Azure supporta anche la registrazione e il controllo di sicurezza specifici per l'applicazione. Per una panoramica completa di tutte le opzioni di registrazione di Azure disponibili per l'istanza di Gemelli digitali di Azure, vedere l'articolo [Registrazione e controllo di Azure](../security/azure-log-audit.md).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [log attività](../azure-monitor/platform/activity-logs-overview.md) di Azure

- Approfondimento sulle impostazioni di diagnostica di Azure con la lettura di una [panoramica dei log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md)

- Altre informazioni, vedere [monitoraggio di Azure registra](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png

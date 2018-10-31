---
title: Come configurare il monitoraggio in Gemelli digitali di Azure | Microsoft Docs
description: Come configurare il monitoraggio in Gemelli digitali di Azure
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: adgera
ms.openlocfilehash: 1c8f1931a29ae9769f7d8ad57a184e3240105a1a
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945820"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Come configurare il monitoraggio in Gemelli digitali di Azure

Gemelli digitali di Azure supporta potenti funzionalità di registrazione, monitoraggio e analisi. Gli sviluppatori di soluzioni possono usare Azure Log Analytics, i log di diagnostica, la registrazione delle attività e altri servizi per supportare le complesse esigenze di monitoraggio di un'app IoT. Le opzioni di registrazione possono essere combinate per eseguire query o visualizzare i record in diversi servizi e per fornire una copertura di registrazione granulare per numerosi servizi.

Questo articolo fornisce un riepilogo delle opzioni di registrazione e monitoraggio e illustra come combinarle specificamente per Gemelli digitali di Azure.

## <a name="review-activity-logs"></a>Rivedere i log attività

I [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) di Azure forniscono informazioni rapide sulle cronologie di eventi e operazioni a livello di sottoscrizione per ogni istanza del servizio di Azure.

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

    ![Panoramica di Analisi log attività][3]

>[!TIP]
>Usare i **log attività** per visualizzare informazioni rapide sugli eventi a livello di sottoscrizione.

## <a name="enable-customer-diagnostic-logs"></a>Abilitare i log di diagnostica dei clienti

Per integrare la registrazione delle attività, è possibile configurare le [impostazioni di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) di Azure per ogni istanza di Azure. Mentre i log attività si riferiscono agli eventi a livello di sottoscrizione, la registrazione diagnostica fornisce informazioni approfondite sulla cronologia operativa delle risorse.

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

    I log di diagnostica vengono spesso salvati tramite [Archiviazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide) e condivisi con [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal). È possibile selezionare entrambe le opzioni.

>[!TIP]
>Usare i **log di diagnostica** per informazioni dettagliate sulle operazioni relative alle risorse.

## <a name="azure-monitor-and-log-analytics"></a>Monitoraggio di Azure e Log Analytics

Le applicazioni IoT collegano numerosi dispositivi, risorse, posizioni e dati in un'unica piattaforma. La registrazione con granularità fine fornisce informazioni dettagliate su ogni parte, servizio o componente specifico dell'architettura complessiva dell'applicazione, ma è spesso necessaria una panoramica unificata per le attività di manutenzione e debug.

Monitoraggio di Azure include il potente servizio Log Analytics che consente la registrazione delle origini da visualizzare e analizzare in un'unica posizione. Monitoraggio di Azure è pertanto estremamente utile per analizzare i log all'interno di app IoT avanzate.

Alcuni esempi d'uso sono:

* L'esecuzione di query su più cronologie di log di diagnostica
* La visualizzazione di log per diverse funzioni definite dall'utente
* La visualizzazione di log per due o più servizi in un intervallo di tempo specifico

La funzionalità completa per l'esecuzione di query sui log è disponibile tramite [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Per configurare queste funzionalità avanzate:

1. Cercare **Log Analytics** nel portale di Azure.
1. Verranno visualizzate le istanze di **Log Analytics** disponibili. Sceglierne una e selezionare **Log** per eseguire query:

    ![Log Analytics][6]

1. Se non si ha ancora un'istanza di **Log Analytics**, è possibile creare un'area di lavoro facendo clic sul pulsante **Aggiungi**:

    ![Creare OMS][7]

Dopo aver effettuato il provisioning dell'istanza di **Log Analytics**, è possibile usare query avanzate per trovare voci in più log o eseguire ricerche con criteri specifici usando **Gestione log**:

   ![Gestione log][8]

Per altre informazioni sulle operazioni avanzate relative alle query, vedere [Introduzione alle query](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

> [!NOTE]
> Quando si inviano eventi a **Log Analytics** per la prima volta, può verificarsi un ritardo di 5 minuti.

Azure Log Analytics offre anche servizi avanzati di notifica di errori e avvisi, che possono essere visualizzati facendo clic su **Diagnostica e risoluzione dei problemi**:

   ![Notifiche di avvisi ed errori][9]

>[!TIP]
>Usare **Log Analytics** per eseguire query su cronologie di log per più funzionalità di app, sottoscrizioni o servizi.

## <a name="other-options"></a>Altre opzioni

Gemelli digitali di Azure supporta anche la registrazione e il controllo di sicurezza specifici per l'applicazione. Per una panoramica completa di tutte le opzioni di registrazione di Azure disponibili per l'istanza di Gemelli digitali di Azure, vedere l'articolo [Registrazione e controllo di Azure](https://docs.microsoft.com/azure/security/azure-log-audit).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) di Azure

Approfondimento sulle impostazioni di diagnostica di Azure con la lettura di una [panoramica dei log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)

Altre informazioni su [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)

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
---
title: Resilienza tramite il monitoraggio e l'analisi con Azure AD B2C | Microsoft Docs
description: Resilienza tramite il monitoraggio e l'analisi con Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb63931f883c6061ded996621a09aacbacd14af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919547"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Resilienza tramite monitoraggio e analisi

Il monitoraggio massimizza la disponibilità e le prestazioni delle applicazioni e dei servizi. Offre una soluzione completa per la raccolta, l'analisi e la gestione dei dati di telemetria dall'infrastruttura e dalle applicazioni. Gli avvisi inviano notifiche in modo proattivo quando si riscontrano problemi con il servizio o le applicazioni. Consentono di identificare e risolvere i problemi prima che gli utenti finali del servizio li notino. [Azure AD log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) consente di analizzare, cercare i log di controllo e i log di accesso e creare visualizzazioni personalizzate.

## <a name="monitor-and-get-notified-through-alerts"></a>Monitorare e ricevere notifiche tramite gli avvisi

Il monitoraggio del sistema e dell'infrastruttura è fondamentale per garantire l'integrità complessiva dei servizi. Inizia con la definizione delle metriche aziendali, ad esempio, il nuovo arrivo degli utenti, le frequenze di autenticazione dell'utente finale e la conversione. Configurare tali indicatori per il monitoraggio. Se si sta pianificando un aumento imminente a causa del traffico di promozione o festività, rivedere le stime in modo specifico per l'evento e il benchmark corrispondente per le metriche di business. Dopo l'evento, eseguire il fallback al benchmark precedente.

Analogamente, per rilevare gli errori o le interruzioni delle prestazioni, la configurazione di una base di riferimento efficace e la definizione degli avvisi è una procedura indispensabile per rispondere tempestivamente ai problemi emergenti.

![Immagine che mostra i componenti di monitoraggio e analisi](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>Come implementare il monitoraggio e gli avvisi

- **Monitoraggio**: usare [monitoraggio di Azure](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor) per monitorare costantemente l'integrità degli obiettivi del livello di servizio (SLO) chiave e ricevere notifiche ogni volta che si verifica una modifica critica. Per iniziare, identificare i criteri di Azure AD B2C o un'applicazione come componente cruciale dell'azienda di cui è necessario monitorare l'integrità per mantenere SLO. Identificare gli indicatori chiave che si allineano con SLOs.
Ad esempio, tenere traccia delle metriche seguenti, poiché un calo improvviso in uno dei due comporterà una perdita di attività.

  - **Totale richieste**: numero totale di richieste inviate ai criteri Azure ad B2C.

  - Percentuale di operazioni riuscite **(%)**: richieste riuscite/numero totale di richieste.

  Accedere agli [indicatori chiave](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs) in [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights) , in cui vengono archiviati Azure ad B2C log basati su criteri, [log di controllo](https://docs.microsoft.coms/azure/active-directory-b2c/analytics-with-application-insights)e log di accesso.  

   - **Visualizzazioni**: uso dei dashboard di compilazione di log Analytics per monitorare visivamente gli indicatori chiave.

   - **Periodo corrente**: creare grafici temporali per mostrare le modifiche delle richieste totali e della percentuale di operazioni riuscite (%) nel periodo corrente, ad esempio, settimana corrente.

   - **Periodo precedente**: creare grafici temporali per mostrare le modifiche delle richieste totali e della percentuale di operazioni riuscite (%) per un periodo precedente a scopo di riferimento, ad esempio, la settimana scorsa.

- **Avviso**: l'uso di log Analytics definisce gli [avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) che vengono attivati quando si verificano modifiche improvvise negli indicatori chiave. Queste modifiche possono influire negativamente sulle SLOs. Gli avvisi usano diverse forme di metodi di notifica, tra cui posta elettronica, SMS e webhook. Per iniziare, definire un criterio che funga da soglia rispetto alla quale verrà attivato l'avviso. Ad esempio:
  - Avviso contro il calo improvviso delle richieste totali: attiva un avviso quando il numero di richieste totali viene eliminato bruscamente. Se, ad esempio, si verifica un calo del 25% del numero totale di richieste rispetto al periodo precedente, generare un avviso.  
  - Avviso contro un calo significativo in caso di esito positivo (%): attiva un avviso quando la percentuale di successo dei criteri selezionati diminuisce significativamente.
  - Alla ricezione di un avviso, risolvere il problema utilizzando [log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views), [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20)e [vs code estensione](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) per Azure ad B2C. Dopo la risoluzione del problema e la distribuzione di un'applicazione o di un criterio aggiornato, continuerà a monitorare gli indicatori chiave fino a tornare all'intervallo normale.

- **Avvisi del servizio**: usare gli [avvisi del livello di servizio Azure ad B2C](https://docs.microsoft.com/azure/service-health/service-health-overview) per ricevere notifiche relative a problemi di servizio, manutenzione pianificata, avviso di integrità e avvisi di sicurezza.

- **Creazione** di report: con [log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics), è possibile creare report che consentono di ottenere informazioni dettagliate sugli utenti, le questioni tecniche e le opportunità di crescita.
  - **Dashboard di integrità**: creare [dashboard personalizzati usando](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) la funzionalità Dashboard di Azure, che supporta l'aggiunta di grafici con log Analytics query. Ad esempio, identificare il modello di accessi riusciti e non riusciti, i motivi dell'errore e la telemetria sui dispositivi usati per eseguire le richieste.
  - **Abbandonare i percorsi di Azure ad B2C**: usare la [cartella di lavoro](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) per tenere traccia dell'elenco dei percorsi di Azure ad B2C abbandonati in cui l'utente ha avviato il percorso di accesso o di iscrizione ma non è mai stato terminato. Fornisce informazioni dettagliate sull'ID criteri e la suddivisione dei passaggi eseguiti dall'utente prima di abbandonare il viaggio.
  - **Azure ad B2C le cartelle di lavoro di monitoraggio**: usare le [cartelle di lavoro di monitoraggio](https://github.com/azure-ad-b2c/siem), che includono il dashboard Azure ad B2C, le operazioni di multi-factor authentication, il report di accesso condizionale e i log di ricerca per CorrelationId, per ottenere informazioni più dettagliate sull'integrità dell'ambiente Azure ad B2C.
  
## <a name="next-steps"></a>Passaggi successivi

- [Risorse di resilienza per sviluppatori Azure AD B2C](resilience-b2c.md)
  - [Esperienza utente finale resiliente](resilient-end-user-experience.md)
  - [Interfacce resilienti con processi esterni](resilient-external-processes.md)
  - [Resilienza tramite le procedure consigliate per gli sviluppatori](resilience-b2c-developer-best-practices.md)
- [Resilienza di compilazione nell'infrastruttura di autenticazione](resilience-in-infrastructure.md)
- [Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md)

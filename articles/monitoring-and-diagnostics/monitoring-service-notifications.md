---
title: "Quali sono le notifiche di integrità servizio di Azure? | Microsoft Docs"
description: "Le notifiche di integrità del servizio consentono di visualizzare i messaggi di integrità servizio pubblicati da Microsoft Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 198aa74622c0f938aabe6540e2321e16aa9beb21
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualizza le notifiche di integrità del servizio tramite il portale di Azure

Le notifiche di integrità del servizio sono pubblicate da Azure e contengono informazioni sulle risorse nella sottoscrizione. Queste notifiche sono una sottoclasse dell'attività del log eventi e sono disponibili anche nel log attività. Le notifiche di integrità del servizio possono essere informativi o utilizzabili, a seconda della classe.

Esistono varie classi di notifiche del servizio integrità:  

- **Azione necessaria:** possibile riscontrare qualcosa di sospetto Azure vengano eseguiti nel proprio account e collaborare con l'utente per risolvere questo problema. Azure invia una notifica, ovvero che riporta in dettaglio le azioni da eseguire o su come contattare un tecnico Azure o il supporto.  
- **Recupero assistito:** si è verificato un evento e tecnici di avere verificato che si verificano ancora impatto. I tecnici di Azure hanno la necessità di collaborare con l'utente per ripristinare integralmente i servizi.  
- **Evento imprevisto:** un evento che influisce su servizio attualmente influire su uno o più delle risorse nella sottoscrizione.  
- **Manutenzione:** un'attività di manutenzione pianificata che potrebbe compromettere uno o più delle risorse nella sottoscrizione.  
- **Informazioni:** potenziali ottimizzazioni che potrebbero facilitare migliorare l'utilizzo delle risorse. 
- **Sicurezza:** urgenti sicurezza informazioni relative a soluzioni eseguite in Azure.

Ogni notifica sull'integrità del servizio contiene informazioni dettagliate sull'ambito e sull'effetto sulle risorse. I dettagli includono:

Nome proprietà | DESCRIZIONE
-------- | -----------
channels | Uno dei seguenti valori: **Admin** o **operazione**.
correlationId | In genere un GUID in formato stringa. Gli eventi appartenenti alla stessa azione, in genere condividono lo stesso correlationId.
eventDataId | Identificatore univoco di un evento.
eventName | Il titolo di un evento.
level | Il livello di un evento. Uno dei seguenti valori: **critico**, **errore**, **avviso**, **informativo**, o **Verbose** .
resourceProviderName | Il nome del provider di risorse per la risorsa interessata.
resourceType| Tipo della risorsa interessata.
subStatus | In genere il codice di stato HTTP della parte RESTANTE corrispondente chiamata, ma può includere anche altre stringhe che descrive uno stato secondario. Ad esempio: OK (codice di stato HTTP: 200), creato (codice di stato HTTP: 201), accettato (codice di stato HTTP: 202), non il contenuto (codice di stato HTTP: 204), richiesta non valida (codice di stato HTTP: 400), non trovata (codice di stato HTTP: 404), dei conflitti (codice di stato HTTP: 409), interno del Server Errore (codice di stato HTTP: 500), Service non disponibile (codice di stato HTTP: 503) e di Timeout del Gateway (codice di stato HTTP: 504).
eventTimestamp | Timestamp dell'evento è stato generato dal servizio di Azure l'elaborazione della richiesta corrispondente all'evento.
submissionTimestamp | Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query.
subscriptionId | Sottoscrizione di Azure in cui è stato registrato l'evento.
status | Stringa che descrive lo stato dell'operazione. Alcuni valori comuni sono: **Started**, **In corso**, **Succeeded**, **Failed**, **Active**, e **Risolto**.
operationName | Nome dell'operazione.
category | Questa proprietà è sempre **ServiceHealth**.
ResourceId | L'ID risorsa della risorsa interessata.
Properties.title | Titolo della comunicazione localizzato. L'inglese è la lingua predefinita.
Properties.communication | Dettagli localizzati della comunicazione con markup HTML. L'inglese è la lingua predefinita.
Properties.incidentType | Uno dei seguenti valori: **AssistedRecovery**, **ActionRequired**, **informazioni**, **incidente**,  **Manutenzione**, o **sicurezza**.
Properties.trackingId | L'evento imprevisto a cui è associato questo evento. Usare questa proprietà per correlare gli eventi relativi a un evento imprevisto.
Properties.impactedServices | Un escape blob JSON che descrive i servizi e le aree interessate dall'evento imprevisto. La proprietà include un elenco di servizi, ognuno dei quali ha un **ServiceName**e un elenco di aree interessati, ognuno dei quali ha un **RegionName**.
Properties.defaultLanguageTitle | La comunicazione in inglese.
Properties.defaultLanguageContent | La comunicazione in inglese come markup HTML o testo normale.
Properties.stage | I valori possibili per **AssistedRecovery**, **ActionRequired**, **informazioni**, **incidente**, e **sicurezza**  sono **Active** o **risolto**. Per **manutenzione**, sono: **Active**, **pianificato**, **InProgress**, **Canceled**, **Ripianificata**, **risolto**, o **completo**.
Properties.communicationId | La comunicazione a cui è associato questo evento.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualizzare le notifiche di integrità del servizio nel portale di Azure
1.  Nel [portale di Azure](https://portal.azure.com) selezionare **Monitoraggio**.

    ![Menu del portale di schermata di Azure, con monitoraggio selezionato](./media/monitoring-service-notifications/home-monitor.png)

    Monitoraggio di Azure riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

3.  Selezionare **Avvisi**.

    ![Log di schermata di monitorare l'attività, con gli avvisi selezionati](./media/monitoring-service-notifications/service-health-summary.png)
4. Selezionare **+ Aggiungi avviso di log attività**e impostare un avviso per garantire che si riceve una notifica per le notifiche future del servizio. Per ulteriori informazioni, vedere [creare attività avvisi di log per le notifiche di servizio](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="next-steps"></a>Passaggi successivi
Ricezione [ogni volta che una notifica di integrità del servizio di notifiche di avviso](monitoring-activity-log-alerts-on-service-notifications.md) viene registrata.  
Altre informazioni sugli [avvisi del log attività](monitoring-activity-log-alerts.md).

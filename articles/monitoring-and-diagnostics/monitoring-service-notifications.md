---
title: "Notifiche sull'integrità del servizio | Microsoft Docs"
description: "Le notifiche sull'integrità del servizio consentono di visualizzare i messaggi sull'integrità del servizio pubblicati da Microsoft Azure."
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
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="service-health-notifications"></a>Notifiche sull'integrità del servizio
## <a name="overview"></a>Panoramica

Questo articolo descrive come visualizzare le notifiche sull'integrità del servizio usando il portale di Azure.

Le notifiche sull'integrità del servizio consentono di visualizzare i messaggi sull'integrità del servizio pubblicati dal team di Azure che possono interessare le risorse della sottoscrizione. Queste notifiche sono una sottoclasse di eventi del log attività e sono disponibili anche nel pannello del log attività. Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Sono disponibili sei classi di notifiche sull'integrità del servizio:  

- **Azione richiesta:** è possibile che venga rilevato un evento insolito nell'account. Potrebbe essere necessario l'intervento dell'utente per risolvere il problema. Viene inviata una notifica con informazioni dettagliate sulle azioni da eseguire o con i dati necessari per contattare il supporto tecnico o l'assistenza di Azure.  
- **Recupero assistito:** si è verificato un evento e i tecnici confermano gli effetti dell'evento ancora presenti. I tecnici hanno la necessità di collaborare con l'utente per ripristinare i servizi.  
- **Evento imprevisto:** un evento del servizio ha effetto su una o più risorse della sottoscrizione.  
- **Manutenzione:** notifica che segnala all'utente un'atività di manutenzione pianificata che potrebbe avere effetto su una o più risorse della sottoscrizione.  
- **Informazioni:** saltuariamente vengono inviate notifiche che segnalano all'utente possibili ottimizzazioni che possono migliorare l'uso delle risorse.  
- **Sicurezza:** informazioni urgenti relative alla sicurezza delle soluzioni in esecuzione in Azure.

Ogni notifica sull'integrità del servizio contiene informazioni dettagliate sull'ambito e sull'effetto sulle risorse. Le informazioni includono:

Nome proprietà | Descrizione
-------- | -----------
channels | Uno dei valori seguenti: "Admin" o "Operation"
correlationId | In genere un GUID in formato stringa. Gli eventi che appartengono alla stessa azione in genere condividono lo stesso correlationId.
eventDataId | Identificatore univoco di un evento
eventName | Titolo dell'evento
necessario | Livello dell'evento. Uno dei valori seguenti: "Critical", "Error", "Warning", "Informational" e "Verbose"
resourceProviderName | Nome del provider di risorse della risorsa interessata.
resourceType| Tipo della risorsa interessata
subStatus | In genere si tratta del codice di stato HTTP della chiamata REST corrispondente, ma può includere altre stringhe che descrivono uno stato secondario, come i valori comuni seguenti: OK (codice di stato HTTP: 200), Created (codice di stato HTTP: 201), Accepted (codice di stato HTTP: 202), No Content (codice di stato HTTP: 204), Bad Request (codice di stato HTTP: 400), Not Found (codice di stato HTTP: 404), Conflict (codice di stato HTTP: 409), Internal Server Error (codice di stato HTTP: 500), Service Unavailable (codice di stato HTTP: 503), Gateway Timeout (codice di stato HTTP: 504).
eventTimestamp | Timestamp del momento in cui l'evento è stato generato dal servizio di Azure che ha elaborato la richiesta corrispondente all'evento.
submissionTimestamp |   Timestamp del momento in cui l'evento è diventato disponibile per l'esecuzione di query.
subscriptionId | La sottoscrizione di Azure in cui è stato registrato l'evento
status | Stringa che descrive lo stato dell'operazione. Alcuni dei valori comuni sono: Started, In Progress, Succeeded, Failed, Active, Resolved.
operationName | Nome dell'operazione.
category | "ServiceHealth"
resourceId | ID risorsa della risorsa interessata.
Properties.title | Titolo della comunicazione localizzato. L'inglese è la lingua predefinita.
Properties.communication | Dettagli localizzati della comunicazione con markup HTML. L'inglese è la lingua predefinita.
Properties.incidentType | Valori possibili: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica l'evento imprevisto a cui è associato l'evento. Usare questa proprietà per correlare gli eventi relativi a un evento imprevisto.
Properties.impactedServices | BLOB JSON preceduto da un carattere di escape che descrive i servizi e le aree su cui ha effetto l'evento imprevisto. Un elenco di servizi, ognuno dei quali ha un nome di servizio e un elenco delle aree interessate, ognuna con un nome di area.
Properties.defaultLanguageTitle | La comunicazione in inglese
Properties.defaultLanguageContent | La comunicazione in inglese con markup HTML o come testo normale
Properties.stage | I valori possibili di AssistedRecovery, ActionRequired, Information, Incident, Security sono Active e Resolved. Per la manutenzione i valori possibili sono: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | La comunicazione a cui è associato l'evento.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Visualizzazione delle notifiche sull'integrità del servizio nel portale di Azure
1.  Nel [portale](https://portal.azure.com) passare al servizio **Monitoraggio**

    ![Monitoraggio](./media/monitoring-service-notifications/home-monitor.png)
2.  Fare clic sull'opzione **Monitoraggio** per aprire il relativo pannello. che riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

3.  Fare clic sulla sezione **Notifiche del servizio**

    ![Monitoraggio](./media/monitoring-service-notifications/service-health-summary.png)
4.  Fare clic su una delle voci per visualizzare altri dettagli

5. Fare clic sull'operazione **+ Aggiungi AVVISO del log attività** per ricevere le notifiche per assicurarsi di ricevere le future notifiche del servizio di questo tipo. Per altre informazioni sulla configurazione degli avvisi nelle notifiche del servizio [fare clic qui](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="next-steps"></a>Passaggi successivi:
Ricevere [notifiche di avviso per ogni notifica sull'integrità del servizio](monitoring-activity-log-alerts-on-service-notifications.md)  
Altre informazioni sugli [avvisi del log attività](monitoring-activity-log-alerts.md)

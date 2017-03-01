---
title: Introduzione alla creazione di report in Azure Active Directory | Microsoft Azure
description: Elenca i vari report disponibili nei report di Azure Active Directory
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: eec9b73cbaccfa50eec6f237e4d1d810c6efa1d9
ms.openlocfilehash: e5b8ac91914203156bd395d7f462385e9f6dbcb4
ms.lasthandoff: 02/24/2017


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Introduzione ad Azure Active Directory Reporting
## <a name="what-it-is"></a>Che cos'è
Azure Active Directory (Azure AD) include la sicurezza, l’attività e i report di controllo per la directory. Di seguito è riportato un elenco dei report compresi:

### <a name="security-reports"></a>Report sulla sicurezza
* Accessi da origini sconosciute
* Accessi dopo più errori
* Accessi da più aree geografiche
* Accessi da indirizzi IP con attività sospette
* Attività di accesso irregolare
* Accessi da dispositivi potenzialmente infetti
* Utenti con anomalie dell'attività di accesso

### <a name="activity-reports"></a>Report sull’attività
* Utilizzo dell'applicazione: riepilogo
* Utilizzo dell'applicazione: dettagliato
* Dashboard dell'applicazione
* Errori di provisioning dell'account
* Dispositivi dell’utente singolo
* Attività dell’utente singolo
* Report attività dei gruppi
* Report attività di registrazione per la reimpostazione password
* Attività di reimpostazione password

### <a name="audit-reports"></a>Report di controllo
* Report di controllo della Directory

> [!TIP]
> Per ulteriori informazioni sul Report di AD Azure, consultare [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="how-it-works"></a>Funzionamento
### <a name="reporting-pipeline"></a>Report sulla pipeline
La pipeline di report è costituita da tre passaggi principali. Ogni volta che un utente accede o si effettua l'autenticazione, si verifica quanto segue:

* In primo luogo, l'utente viene autenticato (con esito positivo o negativo) e il risultato viene archiviato nei database del servizio Azure Active Directory.
* A intervalli regolari, tutti gli accessi recenti vengono elaborati. A questo punto, la sicurezza e gli algoritmi relativi ad attività anomale eseguono la ricerca di attività sospette in tutti gli accessi recenti.
* Dopo l'elaborazione, i report vengono scritti, memorizzati nella cache e gestiti nel portale di Azure classico.

### <a name="report-generation-times"></a>Tempi per la generazione di report
A causa della grande quantità di autenticazioni e accessi elaborati dalla piattaforma di Azure AD, gli accessi più recenti elaborati sono, in media, di un'ora prima. In rari casi, ci potrebbero volere fino a 8 ore per elaborare gli accessi più recenti.

È possibile trovare l’accesso più recente elaborato esaminando il testo della Guida nella parte superiore di ogni report.

![Testo della Guida nella parte superiore di ogni report](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Per ulteriori informazioni sul Report di AD Azure, consultare [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="getting-started"></a>Introduzione
### <a name="sign-into-the-azure-classic-portal"></a>Accedere al portale di Azure classico.
Innanzitutto, è necessario accedere al [Portale di Azure classico](https://manage.windowsazure.com) .come amministratore globale o di conformità. Si deve anche essere un amministratore o coamministratore del servizio di sottoscrizione di Azure oppure usare la sottoscrizione di Azure "Accesso ad Azure Active Directory".

### <a name="navigate-to-reports"></a>Passare al report
Per visualizzare i report, passare alla scheda report nella parte superiore della directory.

Se questa è la prima volta in cui vengono visualizzati i report, è necessario accettare una finestra di dialogo prima di poter visualizzare i report. Questo serve a verificare che agli amministratori dell'azienda sia consentito visualizzare i dati, che possono essere considerati informazioni riservate in alcuni paesi.

![Nella finestra di dialogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Esplorare ogni report
Passare a ogni report per visualizzare i dati che raccolti e gli accessi elaborati. È possibile trovare un [elenco di tutti i report qui](active-directory-reporting-guide.md).

![Tutti i report](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Scaricare i report in formato CSV
Ogni report può essere scaricato come file CSV (valori delimitati da virgole). È possibile utilizzare questi file in Excel, PowerBI o programmi di analisi di terze parti per un'ulteriore analisi dei dati

Per scaricare tutti i report in un formato CSV, passare al report e fare clic su "Download" nella parte inferiore.

![Pulsante di download](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Per ulteriori informazioni sul Report di AD Azure, consultare [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalizzare gli avvisi per attività di accesso anomala.
Passare alla scheda "Configurazione" della directory.

Scorrere fino alla sezione "Notifiche".

Abilitare o disabilitare la sezione "Notifiche tramite posta elettronica di Accessi anomali".

![La sezione delle notifiche](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrazione con l'API di creazione report di Azure AD
Vedere la pagina relativa all' [Introduzione all'API di creazione report](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Assegnare la Multi-Factor Authentication agli utenti.
Selezionare un utente in un report.

Fare clic sul pulsante "Abilita autenticazione a più fattori" nella parte inferiore della schermata.

![Pulsante Multi-Factor Authentication nella parte inferiore dello schermo](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Per ulteriori informazioni sul Report di AD Azure, consultare [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="learn-more"></a>Altre informazioni
### <a name="audit-events"></a>Eventi di controllo
Informazioni su quali eventi vengono controllati nella directory in [Eventi di controllo per il report di Azure Active Directory](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integrazione di API
Vedere [Introduzione all'API di creazione report](active-directory-reporting-api-getting-started.md) e la [documentazione di riferimento sulle API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Ottenere un contatto
Inviare un messaggio di posta elettronica all'indirizzo [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) per commenti, richieste di assistenza o eventuali domande.

> [!TIP]
> Per ulteriori informazioni sul Report di AD Azure, consultare [Visualizzare i report di accesso e utilizzo](active-directory-view-access-usage-reports.md).
> 
> 



---
title: Informazioni dettagliate sull'accesso condizionale e cartella di lavoro di report-Azure Active Directory
description: Uso delle informazioni dettagliate sull'accesso condizionale di Azure AD e della cartella di lavoro per la risoluzione dei problemi
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4113328a5de02c36b7285c837bd5314d11e526b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690436"
---
# <a name="conditional-access-insights-and-reporting"></a>Informazioni dettagliate sull'accesso condizionale e creazione di report

La cartella di lavoro Insights e report di accesso condizionale consente di comprendere l'effetto dei criteri di accesso condizionale nell'organizzazione nel tempo. Durante l'accesso, è possibile applicare uno o più criteri di accesso condizionale, concedendo l'accesso se alcuni controlli di concessione sono soddisfatti o negando l'accesso in caso contrario. Poiché è possibile valutare più criteri di accesso condizionale durante ogni accesso, la cartella di lavoro Insights e report consente di esaminare l'effetto di un singolo criterio o di un subset di tutti i criteri.  

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la cartella di lavoro di Insights e Reporting, il tenant deve avere un'area di lavoro Log Analytics per conservare i dati dei log di accesso. Per usare l'accesso condizionale, gli utenti devono avere Azure AD Premium licenze P1 o P2.

I ruoli seguenti possono accedere a informazioni dettagliate e creazione di report:  

- Amministratore di accesso condizionale 
- Ruolo con autorizzazioni di lettura per la sicurezza 
- Amministratore della protezione 
- Lettore globale 
- Amministratore globale 

È inoltre necessario che gli utenti dispongano di uno dei seguenti ruoli Log Analytics area di lavoro:  

- Lettore 
- Lettore di monitoraggio 
- Lettore di Log Analytics 
- Collaboratore  
- Proprietario 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Eseguire lo streaming dei log di accesso da Azure AD ai log di monitoraggio di Azure 

Se non sono stati integrati Azure AD log con i log di monitoraggio di Azure, sarà necessario eseguire la procedura seguente prima che la cartella di lavoro venga caricata:  

1. [Creare un'area di lavoro log Analytics in monitoraggio di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integrare i log di Azure ad con i log di monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Funzionamento 

Per accedere alla cartella di lavoro di Insights e Reporting:  

1. Accedere al **portale di Azure**.
1. Passare a **Azure Active Directory** > **informazioni dettagliate**sull' > **accesso condizionale**per la**sicurezza** > e la creazione di report.

### <a name="get-started-select-parameters"></a>Introduzione: selezionare i parametri 

Il dashboard Insights e Reporting consente di visualizzare l'effetto di uno o più criteri di accesso condizionale in un periodo di tempo specificato. Iniziare impostando ognuno dei parametri nella parte superiore della cartella di lavoro. 

![Informazioni dettagliate sull'accesso condizionale e dashboard di report nell'portale di Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Criteri di accesso condizionale**: selezionare uno o più criteri di accesso condizionale per visualizzare l'effetto combinato. I criteri sono suddivisi in due gruppi: abilitati e criteri di sola segnalazione. Per impostazione predefinita, vengono selezionati tutti i criteri abilitati. Questi criteri abilitati sono i criteri applicati al tenant.  

**Intervallo di tempo**: selezionare un intervallo di tempo compreso tra 4 ore e fino a 90 giorni. Se si seleziona un intervallo di tempo più indietro rispetto al momento in cui sono stati integrati i log Azure AD con monitoraggio di Azure, verranno visualizzati solo gli accessi dopo l'integrazione.  

**Utente**: per impostazione predefinita, il dashboard mostra l'effetto dei criteri selezionati per tutti gli utenti. Per filtrare in base a un singolo utente, digitare il nome dell'utente nel campo di testo. Per filtrare in base a tutti gli utenti, digitare "All Users" nel campo di testo o lasciare vuoto il parametro. 

**App**: per impostazione predefinita, il dashboard mostra l'effetto dei criteri selezionati per tutte le app. Per filtrare in base a una singola app, digitare il nome dell'app nel campo di testo. Per filtrare in base a tutte le app, digitare "tutte le app" nel campo di testo o lasciare vuoto il parametro. 

**Vista dati**: specificare se si vuole che il dashboard mostri i risultati in termini di numero di utenti o di accessi. Un singolo utente può avere centinaia di accessi a molte app con molti risultati diversi in un determinato intervallo di tempo. Se si seleziona la vista dati come utenti, un utente potrebbe essere incluso nei conteggi di esito positivo e negativo (ad esempio, se sono presenti 10 utenti, 8 di questi potrebbero avere avuto esito positivo negli ultimi 30 giorni e 9 di essi avrebbero potuto avere un risultato di errore negli ultimi 30 giorni).

## <a name="impact-summary"></a>Riepilogo dell'effetto 

Una volta impostati i parametri, il riepilogo dell'effetto viene caricato. Il riepilogo Mostra il numero di utenti o accessi durante l'intervallo di tempo in cui si è verificato il risultato "operazione riuscita", "errore", "azione utente richiesta" o "non applicato" durante la valutazione dei criteri selezionati.  

![Riepilogo dell'effetto nella cartella di lavoro di accesso condizionale](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Totale**: numero di utenti o accessi durante il periodo di tempo in cui è stato valutato almeno uno dei criteri selezionati.

**Success**: numero di utenti o accessi durante il periodo di tempo in cui il risultato combinato dei criteri selezionati è "success" o "report-only: Success".

**Errore**: numero di utenti o accessi durante il periodo di tempo in cui il risultato di almeno uno dei criteri selezionati è "errore" o "solo report: errore".

**Azione utente richiesta**: numero di utenti o accessi durante il periodo di tempo in cui il risultato combinato dei criteri selezionati è "solo report: azione utente obbligatoria". L'azione dell'utente è obbligatoria quando è richiesto un controllo di concessione interattiva, ad esempio l'autenticazione a più fattori, da un criterio di accesso condizionale solo report. Poiché i controlli di concessione interattiva non vengono applicati dai criteri di solo report, non è possibile determinare l'esito positivo o negativo.  

**Non applicato**: numero di utenti o accessi durante il periodo di tempo in cui non è stato applicato nessuno dei criteri selezionati.

### <a name="understanding-the-impact"></a>Informazioni sull'effetto 

![Suddivisione cartella di lavoro per condizione e stato](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Visualizzare la suddivisione degli utenti o degli accessi per ogni condizione. È possibile filtrare gli accessi di un determinato risultato (ad esempio, esito positivo o negativo) selezionando i riquadri di riepilogo nella parte superiore della cartella di lavoro. È possibile visualizzare la suddivisione degli accessi per ogni condizione di accesso condizionale: stato del dispositivo, piattaforma del dispositivo, app client, posizione, applicazione e rischio di accesso.  

## <a name="sign-in-details"></a>Dettagli di accesso 

![Dettagli di accesso alla cartella di lavoro](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

È anche possibile esaminare gli accessi di un utente specifico cercando gli accessi nella parte inferiore del dashboard. La query a sinistra consente di visualizzare gli utenti più frequenti. Se si seleziona un utente, la query viene filtrata a destra.  

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Perché la cartella di lavoro impiega molto tempo per il caricamento?  

A seconda dell'intervallo di tempo selezionato e delle dimensioni del tenant, è possibile che la cartella di lavoro stia valutando un numero eccezionalmente elevato di eventi di accesso. Per i tenant di grandi dimensioni, il volume degli accessi può superare la capacità di query di Log Analytics. Provare a ridurre l'intervallo di tempo a 4 ore per verificare se la cartella di lavoro viene caricata.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Dopo il caricamento per alcuni minuti, perché la cartella di lavoro restituisce zero risultati? 

Quando il volume degli accessi supera la capacità di query di Log Analytics, la cartella di lavoro restituirà zero risultati. Provare a ridurre l'intervallo di tempo a 4 ore per verificare se la cartella di lavoro viene caricata.  

### <a name="can-i-save-my-parameter-selections"></a>È possibile salvare le selezioni di parametri?  

È possibile salvare le selezioni dei parametri nella parte superiore della cartella di lavoro passando a **Azure Active Directory** > **cartelle di lavoro** > **informazioni dettagliate sull'accesso condizionale e creazione di report**. Qui sarà disponibile il modello di cartella di lavoro, in cui è possibile modificare la cartella di lavoro e salvare una copia nell'area di lavoro, incluse le selezioni dei parametri, nei report **personali** o nei **report condivisi**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>È possibile modificare e personalizzare la cartella di lavoro con query aggiuntive? 

È possibile modificare e personalizzare la cartella di lavoro passando a **Azure Active Directory** > **cartelle di lavoro** > **informazioni dettagliate sull'accesso condizionale e creazione di report**. Qui sarà disponibile il modello di cartella di lavoro, in cui è possibile modificare la cartella di lavoro e salvare una copia nell'area di lavoro, incluse le selezioni dei parametri, nei report **personali** o nei **report condivisi**. Per iniziare a modificare le query, fare clic su **modifica** nella parte superiore della cartella di lavoro.  
 
## <a name="next-steps"></a>Passaggi successivi

[Modalità solo report di accesso condizionale](concept-conditional-access-report-only.md)

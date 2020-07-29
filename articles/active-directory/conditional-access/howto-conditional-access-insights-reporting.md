---
title: Cartella di lavoro Informazioni dettagliate e report di Accesso condizionale - Azure Active Directory
description: Uso della cartella di lavoro Informazioni dettagliate e report di Accesso condizionale di Azure AD per risolvere i problemi relativi ai criteri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 678c32703501c4d0b66321cfc3518631ffa28c0c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253274"
---
# <a name="conditional-access-insights-and-reporting"></a>Informazioni dettagliate e report di Accesso condizionale

La cartella di lavoro Informazioni dettagliate e report di Accesso condizionale consente di conoscere l'impatto dei criteri di accesso condizionale dell'organizzazione nel tempo. Durante l'accesso, è possibile applicare uno o più criteri di accesso condizionale, concedendo l'accesso se vengono soddisfatti alcuni controlli di concessione o negando l'accesso in caso contrario. Dato che durante ogni accesso possono essere valutati più criteri di accesso condizionale, la cartella di lavoro Informazioni dettagliate e report consente di esaminare l'impatto dei singoli criteri o di un sottoinsieme di tutti i criteri.  

## <a name="prerequisites"></a>Prerequisiti

Per abilitare la cartella di lavoro Informazioni dettagliate e report, il tenant deve avere un'area di lavoro Log Analytics per conservare i dati dei log di accesso. Per usare l'accesso condizionale, gli utenti devono avere licenze di Azure AD Premium P1 o P2.

A Informazioni dettagliate e report possono accedere i ruoli seguenti:  

- Amministratore di accesso condizionale 
- Ruolo con autorizzazioni di lettura per la sicurezza 
- Amministratore della sicurezza 
- Ruolo con autorizzazioni di lettura globali 
- Amministratore globale 

Gli utenti devono anche avere uno dei ruoli seguenti nell'area di lavoro Log Analytics:  

- Collaboratore  
- Proprietario 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Eseguire lo streaming dei log di accesso da Azure AD ai log di Monitoraggio di Azure 

Se non si sono integrati i log di Azure AD con i log di Monitoraggio di Azure, prima del caricamento della cartella di lavoro sarà necessario seguire questa procedura:  

1. [Creare un'area di lavoro Log Analytics in Monitoraggio di Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integrare i log di Azure AD con i log di Monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Funzionamento 

Per accedere alla cartella di lavoro Informazioni dettagliate e report:  

1. Accedere al **portale di Azure**.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale** > **Informazioni dettagliate e report**.

### <a name="get-started-select-parameters"></a>Per iniziare: selezionare i parametri 

Il dashboard Informazioni dettagliate e report consente di visualizzare l'impatto di uno o più criteri di accesso condizionale in un periodo di tempo specificato. Per iniziare, impostare ognuno dei parametri nella parte superiore della cartella di lavoro. 

![Dashboard Conditional Access Insights and Reporting (Informazioni dettagliate e report di Accesso condizionale) nel portale di Azure](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Criteri di accesso condizionale**: selezionare uno o più criteri di accesso condizionale per visualizzarne l'impatto combinato. I criteri sono suddivisi in due gruppi: criteri abilitati e in modalità solo report. Per impostazione predefinita, sono selezionati tutti i criteri abilitati, che sono i criteri attualmente applicati nel tenant.  

**Intervallo di tempo**: selezionare un intervallo di tempo da 4 ore a 90 giorni prima. Se l'intervallo di tempo selezionato risale a prima dell'integrazione dei log di Azure AD con Monitoraggio di Azure, verranno visualizzati solo gli accessi successivi all'integrazione.  

**Utente**: per impostazione predefinita, il dashboard mostra l'impatto dei criteri selezionati per tutti gli utenti. Per filtrare un singolo utente, digitarne il nome nel campo di testo. Per filtrare tutti gli utenti, digitare "Tutti gli utenti" nel campo di testo o lasciare vuoto il parametro. 

**App**: per impostazione predefinita, il dashboard mostra l'impatto dei criteri selezionati per tutte le app. Per filtrare una singola app, digitarne il nome nel campo di testo. Per filtrare tutte le app, digitare "Tutte le app" nel campo di testo o lasciare vuoto il parametro. 

**Data view** (Visualizzazione dati): selezionare se si vuole che i risultati vengano visualizzati nel dashboard in termini di numero di utenti o numero di accessi. In un determinato intervallo di tempo, un singolo utente può avere centinaia di accessi a numerose app con molti risultati diversi. Se si seleziona la visualizzazione dati in base agli utenti, un utente potrebbe essere incluso sia nel conteggio delle operazioni riuscite che in quello degli errori. Se sono presenti 10 utenti, ad esempio, negli ultimi 30 giorni 8 di questi potrebbero avere avuto un risultato positivo e 9 potrebbero aver avuto un errore.

## <a name="impact-summary"></a>Riepilogo dell'impatto 

Dopo l'impostazione dei parametri, viene caricato il riepilogo dell'impatto. Il riepilogo mostra il numero di utenti o di accessi per i quali, nell'intervallo di tempo, il risultato con la valutazione dei criteri selezionati è stato "Operazione riuscita", "Errore", "È richiesto l'intervento dell'utente" o "Non applicato".  

![Riepilogo dell'impatto nella cartella di lavoro di Accesso condizionale](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: numero di utenti o di accessi durante il periodo di tempo in cui è stato valutato almeno uno dei criteri selezionati.

**Operazione riuscita**: numero di utenti o di accessi durante il periodo di tempo in cui il risultato combinato dei criteri selezionati è stato "Operazione riuscita" o "Solo report: Operazione riuscita".

**Operazione non riuscita**: numero di utenti o di accessi durante il periodo di tempo in cui il risultato di almeno uno dei criteri selezionati è stato "Errore" o "Solo report: Errore".

**È richiesto l'intervento dell'utente**: numero di utenti o di accessi durante il periodo di tempo in cui il risultato combinato dei criteri selezionati è stato "Solo report: Intervento utente necessario". L'intervento dell'utente è necessario quando i criteri di accesso condizionale in modalità solo report richiedono un controllo di concessione interattivo, ad esempio l'autenticazione a più fattori. Dato che i controlli di concessione interattivi non vengono applicati dai criteri in modalità solo report, non è possibile determinare l'esito positivo o negativo.  

**Non applicato**: numero di utenti o di accessi durante il periodo di tempo in cui nessuno dei criteri selezionati è stato applicato.

### <a name="understanding-the-impact"></a>Informazioni sull'impatto 

![Dettaglio della cartella di lavoro per condizione e stato](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Visualizzare il dettaglio degli utenti o degli accessi per ognuna delle condizioni. È possibile filtrare gli accessi di un determinato risultato (ad esempio, Operazione riuscita o Errore) selezionando i riquadri di riepilogo nella parte superiore della cartella di lavoro. È possibile visualizzare il dettaglio degli accessi per ognuna delle condizioni di accesso condizionale: stato del dispositivo, piattaforma del dispositivo, app client, località, applicazione e rischio di accesso.  

## <a name="sign-in-details"></a>Dettagli di accesso 

![Dettagli di accesso nella cartella di lavoro](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

È anche possibile esaminare gli accessi di un utente specifico cercando gli accessi nella parte inferiore del dashboard. La query a sinistra visualizza gli utenti più frequenti. Selezionando un utente verrà filtrata la query a destra.  

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Perché le query hanno esito negativo a causa di un errore di autorizzazione?

Per accedere alla cartella di lavoro, è necessario disporre delle autorizzazioni Azure AD appropriate, nonché delle autorizzazioni Log Analytics area di lavoro. Per verificare se sono disponibili le autorizzazioni appropriate per l'area di lavoro eseguendo una query di log Analytics di esempio:

1. Accedere al **portale di Azure**.
1. Passare a **Azure Active Directory**  >  **log**Azure Active Directory.
1. Digitare `SigninLogs` nella casella query e selezionare **Esegui**.
1. Se la query non restituisce alcun risultato, è possibile che l'area di lavoro non sia stata configurata correttamente. 

![Risolvere i problemi delle query in errore](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Per altre informazioni su come eseguire lo streaming dei log di accesso Azure AD in un'area di lavoro Log Analytics, vedere l'articolo [integrare log di Azure ad con i log di monitoraggio di Azure](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Perché il parametro dei criteri di accesso condizionale è vuoto?

L'elenco dei criteri viene generato esaminando i criteri valutati per l'evento di accesso più recente. Se non sono presenti accessi recenti nel tenant, potrebbe essere necessario attendere alcuni minuti prima che la cartella di lavoro carichi l'elenco dei criteri di accesso condizionale. Questo problema può verificarsi immediatamente dopo la configurazione di Log Analytics o potrebbe richiedere più tempo se un tenant non ha l'attività di accesso recente.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Perché il caricamento della cartella di lavoro richiede molto tempo?  

A seconda dell'intervallo di tempo selezionato e delle dimensioni del tenant, la cartella di lavoro potrebbe valutare un numero estremamente elevato di eventi di accesso. Per i tenant di grandi dimensioni, il volume degli accessi potrebbe superare la capacità di query di Log Analytics. Provare a ridurre l'intervallo di tempo a 4 ore per verificare se la cartella di lavoro viene caricata.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Perché, dopo un caricamento durato alcuni minuti, la cartella di lavoro restituisce zero risultati? 

Quando il volume degli accessi supera la capacità di query di Log Analytics, la cartella di lavoro restituirà zero risultati. Provare a ridurre l'intervallo di tempo a 4 ore per verificare se la cartella di lavoro viene caricata.  

### <a name="can-i-save-my-parameter-selections"></a>È possibile salvare le selezioni dei parametri?  

È possibile salvare le selezioni dei parametri nella parte superiore della cartella di lavoro, passando ad **Azure Active Directory** > **Cartelle di lavoro** > **Conditional Access Insights and reporting** (Informazioni dettagliate e report di Accesso condizionale). Verrà così visualizzato il modello della cartella di lavoro in cui è possibile modificare la cartella di lavoro e salvarne una copia nell'area di lavoro, con le selezioni dei parametri, in **Report personali** o **Report condivisi**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>È possibile modificare e personalizzare la cartella di lavoro con query aggiuntive? 

È possibile modificare e personalizzare la cartella di lavoro passando ad **Azure Active Directory** > **Cartelle di lavoro** > **Conditional Access Insights and reporting** (Informazioni dettagliate e report di Accesso condizionale). Verrà così visualizzato il modello della cartella di lavoro in cui è possibile modificare la cartella di lavoro e salvarne una copia nell'area di lavoro, con le selezioni dei parametri, in **Report personali** o **Report condivisi**. Per iniziare a modificare le query, fare clic su **Modifica** nella parte superiore della cartella di lavoro.  
 
## <a name="next-steps"></a>Passaggi successivi

[Modalità solo report dell'accesso condizionale](concept-conditional-access-report-only.md)

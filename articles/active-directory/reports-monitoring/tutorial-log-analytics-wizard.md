---
title: Configurare la procedura guidata di Log Analytics in Azure AD | Microsoft Docs
description: Informazioni su come configurare Log Analytics.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 643010ef1f6e941a57673a711e1871aafd1e341d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91361909"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Esercitazione: Configurare la procedura guidata di Log Analytics


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare un'area di lavoro Log Analytics per i log di controllo e di accesso
> * Eseguire query con il linguaggio di query Kusto (KQL)
> * Creare una regola di avviso che invia avvisi quando viene usato un account specifico
> * Creare una cartella di lavoro personalizzata con il modello di avvio rapido
> * Aggiungere una query a un modello di cartella di lavoro esistente

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure con almeno un amministratore con licenza P1. Se non si ha una sottoscrizione di Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).

- Un tenant di Azure AD.

- Un utente con il ruolo di amministratore globale o amministratore della sicurezza per il tenant di Azure AD.


Acquisire familiarità con questi articoli:

- [Esercitazione: Raccogliere e analizzare i log delle risorse da una risorsa di Azure](../../azure-monitor/learn/tutorial-resource-logs.md)

- [Integrare i log attività con Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Gestire gli account di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)

- [Riferimento rapido sul linguaggio KQL](/azure/data-explorer/kql-quick-reference)

- [Cartelle di lavoro di Monitoraggio di Azure](../../azure-monitor/platform/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Configurare un'area di lavoro 

Questa procedura descrive come configurare un'area di lavoro Log Analytics per i log di controllo e di accesso.
La configurazione di un'area di lavoro Log Analytics è costituita da due passaggi principali:
 
1. Creazione di un'area di lavoro Log Analytics
2. Configurazione delle impostazioni di diagnostica

**Per configurare un'area di lavoro:** 


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Cercare **Aree di lavoro Log Analytics**.

    ![Cercare risorse, servizi e documenti](./media/tutorial-log-analytics-wizard/search-services.png)

3. Nella pagina Aree di lavoro Log Analytics fare clic su **Aggiungi**.

    ![Screenshot che mostra il pulsante Aggiungi nella pagina Aree di lavoro Log Analytics.](./media/tutorial-log-analytics-wizard/add.png)

4.  Nella pagina **Crea area di lavoro Log Analytics** seguire questa procedura:

    ![Creare un'area di lavoro Log Analytics](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Selezionare la propria sottoscrizione.

    2. Selezionare un gruppo di risorse.
 
    3. Nella casella di testo **Nome** digitare un nome, ad esempio MytestWorkspace1.

    4. Selezionare un'area.

5. Fare clic su **Rivedi e crea**.

    ![Rivedi e crea](./media/tutorial-log-analytics-wizard/review-create.png)

6. Fare clic su **Crea** e attendere il completamento della distribuzione. Potrebbe essere necessario aggiornare la pagina per visualizzare la nuova area di lavoro.

    ![Crea](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Cercare **Azure Active Directory**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. Nella sezione **Monitoraggio** fare clic su **Impostazioni di diagnostica**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Nella pagina **Impostazioni di diagnostica** fare clic su **Aggiungi impostazione di diagnostica**.

    ![Aggiungi impostazione di diagnostica](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Nella pagina **Impostazioni di diagnostica** seguire questa procedura:

    ![Selezionare le impostazioni di diagnostica](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. In **Dettagli categoria** selezionare **AuditLogs** e **SigninLogs**.

    2. In **Dettagli destinazione** selezionare **Invia a Log Analytics** e quindi selezionare la nuova area di lavoro Log Analytics. 
   
    3. Fare clic su **Salva**. 

## <a name="run-queries"></a>Eseguire le query  

Questa procedura mostra come eseguire le query con il **linguaggio di query Kusto (KQL)** .


**Per eseguire una query:**


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Cercare **Azure Active Directory**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Nella sezione **Monitoraggio** fare clic su **Log**.

4. Nella pagina **Log** fare clic su **Attività iniziali**.

5. Nella casella di testo di **ricerca* digitare la query.

6. Fare clic su **Esegui**.  


### <a name="kql-query-examples"></a>Esempi di query KQL

Prendere 10 voci casuali dai dati di input:

`SigninLogs | take 10`

Esaminare gli accessi in cui l'accesso condizionale ha avuto esito positivo

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Contare il numero di esecuzioni riuscite

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Aggregare il numero di accessi riusciti per utente al giorno:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Visualizzare il numero di volte in cui un utente esegue una determinata operazione in un periodo di tempo specifico:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Trasformare i risultati nel nome operazione tramite pivot

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Unire i log di controllo e di accesso con un inner join:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Visualizzare il numero di accessi per tipo di app client:

`SigninLogs | summarize count() by ClientAppUsed`

Contare gli accessi per giorno:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Prendere 5 voci casuali e proiettare le colonne che si vuole visualizzare nei risultati:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Prendere le prime 5 voci in ordine decrescente e proiettare le colonne che si vuole visualizzare

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Creare una nuova colonna combinando i valori con altre due colonne:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Creare una regola di avviso  

Questa procedura illustra come inviare avvisi quando viene usato l'account BreakGlass.

**Per creare una regola di avviso:**


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Cercare **Azure Active Directory**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Nella sezione **Monitoraggio** fare clic su **Log**.

4. Nella pagina **Log** fare clic su **Attività iniziali**.

5. Nella casella di testo di **ricerca** digitare: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Fare clic su **Esegui**.  

7. Nella barra degli strumenti fare clic su **Nuova regola di avviso**.

    ![Nuova regola di avviso](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Nella pagina **Crea regola di avviso** verificare che l'ambito sia corretto.

9. In **Condizione** fare clic su: **Ogni volta che la ricerca di log personalizzati media supera <logic undefined> risultati**

    ![Condizione predefinita](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Nella sezione **Logica avvisi** della pagina **Configura logica dei segnali** seguire questa procedura:

    ![Logica avvisi](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. In **In base a** selezionare **Numero di risultati**.

    2. In **Operatore** selezionare **Maggiore di**.

    3. In **Valore soglia** selezionare **0**. 

11. Nella sezione **Valutata in base a** della pagina **Configura logica dei segnali** seguire questa procedura:

    ![Valutata in base a](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. In **Periodo (in minuti)** selezionare **5**.

    2. In **Frequenza (in minuti)** selezionare **5**.

    3. Fare clic su **Fine**. 

12. In **Gruppo di azioni** fare clic su **Seleziona gruppo di azioni**. 

    ![Gruppo di azioni](./media/tutorial-log-analytics-wizard/action-group.png)

13. In **Selezionare un gruppo di azioni da collegare a questa regola di avviso** fare clic su **Crea gruppo di azioni**. 

    ![Creare un gruppo di azioni](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Nella pagina **Crea gruppo di azioni** seguire la procedura seguente:

    ![Dettagli dell'istanza](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Nella casella di testo **Nome gruppo di azioni** digitare **My action group**.

    2. Nella casella di testo **Nome visualizzato** digitare **My action**.

    3. Fare clic su **Rivedi e crea**. 

    4. Fare clic su **Crea**.


15. In **Personalizza azioni** seguire questa procedura:

    ![Personalizza azioni](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Selezionare **Oggetto del messaggio di posta elettronica**.

    2. Nella casella di testo **Riga dell'oggetto** digitare: `Breakglass account has been used`

16. In **Dettagli regola di avviso** seguire questa procedura:

    ![Dettagli regola di avviso](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Nella casella di testo **Nome regola di avviso** digitare: `Breakglass account`

    2. Nella casella di testo **Descrizione** digitare: `Your emergency access account has been used`

17. Fare clic su **Crea regola di avviso**.   


## <a name="create-a-custom-workbook"></a>Creare una cartella di lavoro personalizzata

Questa procedura illustra come creare una nuova cartella di lavoro con il modello di avvio rapido.




1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Cercare **Azure Active Directory**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Nella sezione **Monitoraggio** fare clic su **Cartelle di lavoro**.

    ![Screenshot che mostra la sezione Monitoraggio nel menu del portale di Azure con l'opzione Cartelle di lavoro selezionata.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Nella sezione **Avvio rapido** fare clic su **Vuoto**.

    ![Avvio rapido](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Scegliere **Aggiungi**.

    ![Aggiungere una cartella di lavoro](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Fare clic su **Aggiungi testo**.

    ![Aggiungere il testo](./media/tutorial-log-analytics-wizard/add-text.png)


7. Nella casella di testo digitare: `# Client apps used in the past week` e quindi fare clic su **Modifica completata**.

    ![Testo della cartella di lavoro](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Nella nuova cartella di lavoro fare clic su **Aggiungi** e quindi fare clic su **Aggiungi query**.

    ![Aggiungi query](./media/tutorial-log-analytics-wizard/add-query.png)

9. Nella casella di testo della query digitare: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Fare clic su **Esegui query**.

    ![Esegui query](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Nella barra degli strumenti, in **Visualizzazione**, fare clic su **Grafico a torta**.

    ![Grafico a torta](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Fare clic su **Modifica completata**.

    ![Modifica completata](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Aggiungere una query a un modello di cartella di lavoro  

Questa procedura illustra come aggiungere una query a un modello di cartella di lavoro esistente. L'esempio è basato su una query che mostra la distribuzione dell'esito positivo degli accessi condizionali rispetto agli errori.


1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.

2. Cercare **Azure Active Directory**.

    ![Azure Active Directory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Nella sezione **Monitoraggio** fare clic su **Cartelle di lavoro**.

    ![Screenshot che mostra la sezione Monitoraggio nel menu con l'opzione Cartelle di lavoro selezionata.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Nella sezione **Accesso condizionale** fare clic su **Informazioni dettagliate e report sull'accesso condizionale**.

    ![Modello di accesso condizionale](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Nella barra degli strumenti fare clic su **Modifica**.

    ![Modello di accesso condizionale](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Nella barra degli strumenti fare clic sui tre puntini, quindi fare clic su **Aggiungi** e infine selezionare **Aggiungi query**.

    ![Aggiungere una query della cartella di lavoro](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Nella casella di testo della query digitare: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Fare clic su **Esegui query**.

    ![Esegui query](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Fare clic su **Intervallo di tempo** e quindi selezionare **Imposta nella query**.

10. Fare clic su **Visualizzazione** e quindi selezionare **Grafico a barre**. 

11. Fare clic su **Impostazioni avanzate** e digitare `Conditional Access status over the last 20 days` come titolo del grafico, quindi fare clic su **Modifica completata**. 

    ![Impostare il titolo del grafico](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come gestire le identità dei dispositivi usando il portale di Azure.
> [!div class="nextstepaction"]
> [Monitoring](overview-monitoring.md)
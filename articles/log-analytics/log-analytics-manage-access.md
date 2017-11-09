---
title: Gestire aree di lavoro in Azure Log Analytics | Microsoft Docs
description: "È possibile gestire le aree di lavoro in Log Analytics di Azure usando svariate attività amministrative per utenti, account, aree di lavoro e account di Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: d9f86ac19044fd13e77d35d6c3dd9964c3852001
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-workspaces"></a>Gestire le aree di lavoro

Per gestire l'accesso a Log Analytics, vengono eseguite diverse attività amministrative relative alle aree di lavoro. Questo articolo fornisce le procedure consigliate per gestire le aree di lavoro. Un'area di lavoro è sostanzialmente un contenitore che include informazioni sull'account e semplici informazioni di configurazione per l'account stesso. Nell'organizzazione è possibile usare più aree di lavoro per gestire diversi set di dati raccolti dall'intera infrastruttura IT o da una parte di essa.

Per creare un'area di lavoro, è necessario:

1. Disporre di una sottoscrizione di Azure
2. Scegliere un nome per l'area di lavoro
3. Associare l'area di lavoro alla sottoscrizione
4. Scegliere una località geografica

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinare il numero di aree di lavoro necessarie
Un'area di lavoro è una risorsa di Azure e rappresenta un contenitore in cui i dati vengono, aggregati, analizzati e presentati nel portale di Azure.

È possibile avere più aree di lavoro per ogni sottoscrizione di Azure e avere accesso a più di un'area di lavoro. Ridurre al minimo il numero di aree di lavoro consente di effettuare una query sulla maggior parte dei dati e di correlarli, perché non è possibile effettuare una query su più aree di lavoro. Questa sezione descrive quando può essere utile creare più aree di lavoro.

Oggi, un'area di lavoro fornisce:

* Una posizione geografica per archiviare i dati
* Granularità per la fatturazione
* Isolamento dei dati
* Ambito per la configurazione

In base alle caratteristiche precedenti, si possono creare più aree di lavoro se:

* Si opera in un'azienda globale ed è necessario che i dati siano archiviati in aree specifiche per motivi di sovranità o conformità.
* Si usa Azure e si intendono evitare costi di trasferimento dei dati in uscita tramite un'area di lavoro nella stessa area delle risorse di Azure da essa gestite.
* Si vogliono allocare le spese a reparti o gruppi aziendali diversi in base all'utilizzo. Quando si crea un'area di lavoro per ogni reparto o gruppo aziendale, i rendiconti sulla fatturazione e l’utilizzo di Azure presentano gli addebiti separatamente per ogni area di lavoro.
* Si opera come provider di servizi gestiti e per ogni cliente gestito è necessario mantenere i dati di Log Analytics isolati da altri dati del cliente.
* Si gestiscono più clienti e si desidera che ogni cliente, reparto o gruppo aziendale visualizzi i propri dati, ma non i dati di altri.

Quando si usano agenti per la raccolta dei dati, è possibile [configurare ogni agente in modo che faccia riferimento a una o più aree di lavoro](log-analytics-windows-agents.md).

Se si usa System Center Operations Manager, ogni gruppo di gestione di Operations Manager può essere connesso con una sola area di lavoro. Microsoft Monitoring Agent può tuttavia essere configurato sul computer per l'invio di report sia a Operations Manager che a una diversa area di lavoro di Log Analytics.  

### <a name="workspace-information"></a>Informazioni sull'area di lavoro

È possibile visualizzare i dettagli sull'area di lavoro nel portale di Azure. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Visualizzare le informazioni sull'area di lavoro nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi** nell'angolo in basso a sinistra del portale di Azure.  Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Fare clic su **Log Analytics**.  
    ![Hub di Azure](./media/log-analytics-manage-access/hub.png)  
3. Nel pannello di sottoscrizioni di Log Analytics, selezionare un'area di lavoro.
4. Nel pannello dell'area di lavoro vengono visualizzati i dettagli sull'area di lavoro e i collegamenti che forniscono informazioni aggiuntive.  
    ![Dettagli dell'area di lavoro](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gestire utenti e account
A ogni area di lavoro possono essere associati più account, ognuno dei quali (account Microsoft o account aziendale) può avere accesso a diverse aree di lavoro.

Per impostazione predefinita, l'account Microsoft o l'account aziendale che crea l'area di lavoro diventa l'amministratore di quest'ultima.

Esistono due modelli di autorizzazione che controllano l'accesso a un'area di lavoro di Log Analytics:

1. Ruoli utente di Log Analytics legacy
2. [Accessi in base al ruolo di Azure](../active-directory/role-based-access-control-configure.md)

La tabella seguente riepiloga l'accesso che si può impostare con ogni modello di autorizzazione:

|                          | Portale di Log Analytics | Portale di Azure | API (incluso PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Ruoli utente di Log Analytics | Sì                  | No           | No                         |
| Accessi in base al ruolo di Azure  | Sì                  | Sì          | Sì                        |

> [!NOTE]
> È in corso il passaggio di Log Analytics all'uso dell'accesso in base ai ruoli di Azure come modello di autorizzazione, invece dei ruoli utente di Log Analytics.
>
>

I ruoli utente di Log Analytics legacy controllano solo l'accesso alle attività eseguite nel [portale di Log Analytics](https://mms.microsoft.com).

Le attività seguenti richiedono anche le autorizzazioni di Azure:

| Azione                                                          | Autorizzazioni di Azure necessarie | Note |
|-----------------------------------------------------------------|--------------------------|-------|
| Aggiunta e rimozione di soluzioni di gestione                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Modifica del piano tariffario                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Visualizzazione dei dati nei riquadri delle soluzioni *Backup* e *Site Recovery* | Amministratore/Coamministratore | Risorse di accesso distribuite usando il modello di distribuzione classica |
| Creare un'area di lavoro nel portale di Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Gestione dell'accesso a Log Analytics con le autorizzazioni di Azure
Per concedere l'accesso all'area di lavoro di Log Analytics usando le autorizzazioni di Azure, seguire i passaggi indicati in [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../active-directory/role-based-access-control-configure.md).

Azure offre due ruoli utente predefiniti per Log Analytics:
- Lettore di Log Analytics
- Collaboratore di Log Analytics

I membri del ruolo *Lettore di Log Analytics* possono eseguire queste operazioni:
- Visualizzare e cercare tutti i dati di monitoraggio 
- Visualizzare le impostazioni di monitoraggio, inclusa la configurazione della diagnostica di Azure in tutte le risorse di Azure.

| Tipo    | Autorizzazione | Descrizione |
| ------- | ---------- | ----------- |
| Azione | `*/read`   | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
| Azione | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Consente di eseguire query di ricerca log versione 2 |
| Azione | `Microsoft.OperationalInsights/workspaces/search/action` | Consente di eseguire query di ricerca log versione 1 |
| Azione | `Microsoft.Support/*` | Consente di aprire casi di supporto |
|Non azione | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Impedisce la lettura della chiave dell'area di lavoro, necessaria per l'uso dell'API di raccolta dati e per l'installazione degli agenti |


I membri del ruolo *Collaboratore di Log Analytics* possono eseguire queste operazioni:
- Leggere tutti i dati di monitoraggio 
- Creare e configurare account di automazione
- Aggiungere e rimuovere soluzioni di gestione
- Leggere le chiavi degli account di archiviazione 
- Configurare la raccolta di log da Archiviazione di Azure
- Modificare le impostazioni di monitoraggio per le risorse di Azure, tra cui
  - Aggiunta dell'estensione macchina virtuale alle VM
  - Configurazione della diagnostica di Azure in tutte le risorse di Azure

> [!NOTE] 
> È possibile usare la possibilità di aggiungere un'estensione macchina virtuale a una VM per ottenere il controllo completo su di essa.

| Autorizzazione | Descrizione |
| ---------- | ----------- |
| `*/read`     | Consente di visualizzare tutte le risorse e le configurazioni delle risorse. Include la visualizzazione di: <br> Stato dell'estensione macchina virtuale <br> Configurazione della diagnostica di Azure nelle risorse <br> Tutte le proprietà e le impostazioni di tutte le risorse |
| `Microsoft.Automation/automationAccounts/*` | Consente di creare e configurare account di automazione di Azure, inclusa l'aggiunta e modifica di runbook |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Consente di aggiungere, aggiornare e rimuovere estensioni macchina virtuale, inclusa l'estensione Microsoft Monitoring Agent e l'estensione agente OMS per Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Consente di visualizzare la chiave dell'account di archiviazione, è necessaria per configurare Log Analytics per la lettura dei log dagli account di archiviazione di Azure |
| `Microsoft.Insights/alertRules/*` | Consente di aggiungere, aggiornare e rimuovere regole di avviso |
| `Microsoft.Insights/diagnosticSettings/*` | Consente di aggiungere, aggiornare e rimuovere impostazioni di diagnostica dalle risorse di Azure |
| `Microsoft.OperationalInsights/*` | Consente di aggiungere, aggiornare e rimuovere configurazioni per le aree di lavoro di Log Analytics |
| `Microsoft.OperationsManagement/*` | Consente di aggiungere e rimuovere soluzioni di gestione |
| `Microsoft.Resources/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Consente di creare ed eliminare distribuzioni, è necessaria per aggiungere e rimuovere soluzioni, aree di lavoro e account di automazione |

Per aggiungere e rimuovere utenti da un ruolo utente, è necessario avere le autorizzazioni `Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write`.

Usare questi ruoli per concedere agli utenti l'accesso ad ambiti diversi:
- Sottoscrizione: accesso a tutte le aree di lavoro nella sottoscrizione
- Gruppo di risorse: accesso a tutte le aree di lavoro nel gruppo di risorse
- Risorsa: accesso alla sola area di lavoro specificata

Usare i [ruoli personalizzati](../active-directory/role-based-access-control-custom-roles.md) per creare ruoli con le autorizzazioni specifiche necessarie.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Ruoli utente di Azure e ruoli utente del portale di Log Analytics
Avendo almeno l'autorizzazione di lettura di Azure per l'area di lavoro di Log Analytics, è possibile aprire il portale di OMS facendo clic sull'attività **Portale di OMS** quando si visualizza l'area di lavoro di Log Analytics.

Quando si apre il portale di OMS, si passa all'uso dei ruoli utente di Log Analytics legacy. Se non si ha un'assegnazione di ruolo nel portale di Log Analytics, il servizio [controlla le autorizzazioni di Azure disponibili per l'area di lavoro](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
L'assegnazione di ruolo nel portale di OMS viene determinata come segue:

| Condizioni                                                   | Ruolo utente di Log Analytics assegnato | Note |
|--------------------------------------------------------------|----------------------------------|-------|
| L'account appartiene a un ruolo utente di Log Analytics legacy     | Ruolo utente di Log Analytics specificato | |
| L'account non appartiene a un ruolo utente di Log Analytics legacy <br> Autorizzazioni di Azure complete per l'area di lavoro (autorizzazione `*` <sup>1</sup>) | Amministratore ||
| L'account non appartiene a un ruolo utente di Log Analytics legacy <br> Autorizzazioni di Azure complete per l'area di lavoro (autorizzazione `*` <sup>1</sup>) <br> *non azioni*`Microsoft.Authorization/*/Delete` e `Microsoft.Authorization/*/Write` | Collaboratore ||
| L'account non appartiene a un ruolo utente di Log Analytics legacy <br> Autorizzazione di lettura di Azure | Sola lettura ||
| L'account non appartiene a un ruolo utente di Log Analytics legacy <br> Le autorizzazioni di Azure non vengono riconosciute | Sola lettura ||
| Per le sottoscrizioni gestite da un Cloud Solution Provider (CSP) <br> L'account con cui è stato eseguito l'accesso si trova nell'istanza di Azure Active Directory collegata all'area di lavoro | Amministratore | In genere il cliente di un CSP |
| Per le sottoscrizioni gestite da un Cloud Solution Provider (CSP) <br> L'account con cui è stato eseguito l'accesso non si trova nell'istanza di Azure Active Directory collegata all'area di lavoro | Collaboratore | In genere il CSP |

<sup>1</sup> Per altre informazioni sulle definizioni del ruolo, vedere [Autorizzazioni di Azure](../active-directory/role-based-access-control-custom-roles.md). Quando si valutano i ruoli, un'azione `*` non equivale a `Microsoft.OperationalInsights/workspaces/*`.

Ecco alcuni elementi relativi al portale di Azure da tenere presenti:

* Quando si accede al portale di OMS usando l'URL http://mms.microsoft.com, viene visualizzato l'elenco **Selezionare un'area di lavoro**. Questo elenco contiene solo le aree di lavoro in cui si ha un ruolo utente di Log Analytics. Per visualizzare le aree di lavoro a cui si può accedere con le sottoscrizioni di Azure, è necessario specificare un tenant come parte dell'URL. Ad esempio: `mms.microsoft.com/?tenant=contoso.com`. L'identificatore del tenant corrisponde spesso all'ultima parte dell'indirizzo di posta elettronica usato per eseguire l'accesso.
* Se si vuole passare direttamente a un portale a cui si ha accesso tramite le autorizzazioni di Azure, è necessario specificare la risorsa come parte dell'URL. È possibile ottenere questo URL usando PowerShell.

  ad esempio `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  L'URL ha l'aspetto seguente: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Gestione degli utenti nel portale di OMS
È possibile gestire utenti e gruppi nella scheda **Gestisci utenti** in **Account** nella pagina Impostazioni.   

![gestione utenti](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Aggiungere un utente a un'area di lavoro esistente
Per aggiungere un utente o un gruppo a un'area di lavoro, seguire questa procedura.

1. Nel portale di OMS fare clic sul riquadro **Impostazioni**.
2. Fare clic sulla scheda **Account** e quindi sulla scheda **Gestisci utenti**.
3. Nella sezione **Gestisci utenti** scegliere il tipo di account da aggiungere: **Account aziendale**, **Account Microsoft**, **Supporto Microsoft**.

   * Se si sceglie l'Account Microsoft, digitare l'indirizzo di posta elettronica dell'utente associato all'Account Microsoft.
   * Se si sceglie l'account dell'organizzazione, immettere parte del nome utente/gruppo o l'alias di posta elettronica e verrà visualizzato un elenco di utenti e gruppi in una casella a discesa. Selezionare un utente o gruppo.
   * Usare Supporto Microsoft per concedere a un tecnico del supporto tecnico Microsoft o a un altro dipendente Microsoft l'accesso temporaneo all'area di lavoro per la risoluzione dei problemi.

     > [!NOTE]
     > Per prestazioni ottimali, limitare a tre il numero di gruppi di Active Directory associati a un singolo account OMS, ovvero uno per gli amministratori, uno per i collaboratori e uno per gli utenti di sola lettura. L'uso di più gruppi potrebbe compromettere le prestazioni di Log Analytics.
     >
     >
4. Scegliere il tipo di utente o gruppo da aggiungere: **Amministratore**, **Collaboratore** o **Utente di sola lettura**.  
5. Fare clic su **Aggiungi**.

   Se si aggiunge un account Microsoft, all'indirizzo di posta elettronica fornito viene inviato un invito ad aggiungere l'account. Dopo aver seguito le istruzioni riportate nell'invito a iscriversi a OMS, l'utente può accedere alla sua area di lavoro.
   Se si aggiunge un account aziendale, l'utente può accedere immediatamente a Log Analytics.  

#### <a name="edit-an-existing-user-type"></a>Modificare un tipo utente esistente
È possibile modificare il ruolo account per un utente associato all'account OMS. Per il ruolo sono disponibili le opzioni seguenti:

* *Administrator*: può gestire gli utenti, visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server
* *Collaboratore*: può visualizzare ed eseguire operazioni su tutti gli avvisi e aggiungere e rimuovere i server
* *Utente di sola lettura*: gli utenti contrassegnati come di sola lettura non possono:

  1. Aggiungere o rimuovere soluzioni. La raccolta di soluzioni è nascosta.
  2. Aggiungere, modificare o rimuovere riquadri nel **Dashboard**.
  3. Visualizzare le pagine **Impostazioni**. Queste pagine sono nascoste.
  4. Nella visualizzazione Ricerca le attività di configurazione di Power BI, ricerche salvate e avvisi sono nascoste.

#### <a name="to-edit-an-account"></a>Per modificare un account
1. Nel portale di OMS fare clic sul riquadro **Impostazioni**.
2. Fare clic sulla scheda **Account** e quindi sulla scheda **Gestisci utenti**.
3. Selezionare il ruolo per l'utente da modificare.
4. Nella finestra di dialogo di conferma fare clic su **Sì**.

### <a name="remove-a-user-from-a-workspace"></a>Rimuovere un utente da un'area di lavoro
Per rimuovere un utente da un'area di lavoro, seguire questa procedura. La rimozione dell'utente non chiude l'area di lavoro, ma rimuove l'associazione tra l'utente e l'area di lavoro stessa. Se è associato a più aree di lavoro, un utente può comunque accedere a OMS e visualizzare le altre aree di lavoro.

1. Nel portale di OMS fare clic sul riquadro **Impostazioni**.
2. Fare clic sulla scheda **Account** e quindi sulla scheda **Gestisci utenti**.
3. Fare clic su **Rimuovi** accanto al nome utente da rimuovere.
4. Nella finestra di dialogo di conferma fare clic su **Sì**.

### <a name="add-a-group-to-an-existing-workspace"></a>Aggiungere un gruppo a un'area di lavoro esistente
1. Nella sezione precedente "Per aggiungere un utente a un'area di lavoro esistente" seguire i passaggi da 1 a 4.
2. In **Scegliere l'utente/gruppo** selezionare **Gruppo**.  
   ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3. Immettere il nome visualizzato o l'indirizzo di posta elettronica per il gruppo che si vuole aggiungere.
4. Selezionare il gruppo nei risultati dell'elenco e quindi fare clic su **Aggiungi**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Collegare un'area di lavoro esistente a una sottoscrizione di Azure
Tutte le aree di lavoro create dopo il 26 settembre 2016 devono essere collegate a una sottoscrizione di Azure al momento della creazione. All'accesso, le aree di lavoro create prima di tale data devono essere collegate a un'area di lavoro. Quando si crea l'area di lavoro dal portale di Azure o si collega l'area di lavoro a una sottoscrizione di Azure, Azure Active Directory viene collegato come account aziendale.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Per collegare un'area di lavoro a una sottoscrizione di Azure nel portale di OMS

- Quando si accede al portale di OMS, viene richiesto di selezionare una sottoscrizione di Azure. Selezionare la sottoscrizione che si vuole collegare all'area di lavoro e quindi fare clic su **Collega**.  
    ![Collegare la sottoscrizione di Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Per collegare un'area di lavoro, è necessario che l'account di Azure abbia già accesso a tale area.  In altri termini, è necessario che l'account usato per accedere al portale di Azure sia **lo stesso** account usato per accedere all'area di lavoro. In caso contrario, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Per collegare un'area di lavoro a una sottoscrizione di Azure nel portale di Azure
1. Accedere al [portale di Azure](http://portal.azure.com).
2. Cercare e selezionare **Log Analytics**.
3. Viene visualizzato un elenco delle aree di lavoro esistenti. Fare clic su **Aggiungi**.  
   ![Elenco di aree di lavoro](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. In **Area di lavoro di OMS** fare clic su **Collega esistente**.  
   ![Collegare un'area di lavoro esistente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Fare clic su **Configura le impostazioni necessarie**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Viene visualizzato l'elenco delle aree di lavoro non ancora collegate all'account Azure. Selezionare un'area di lavoro.  
   ![Selezionare le aree di lavoro](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Se necessario, è possibile modificare i valori per gli elementi seguenti:
   * Sottoscrizione
   * Gruppo di risorse
   * Percorso
   * Piano tariffario   
     ![Modificare i valori](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Fare clic su **OK**. L'area di lavoro ora è collegata all'account Azure.

> [!NOTE]
> Se non viene visualizzata l'area di lavoro a cui ci si vuole collegare, la sottoscrizione di Azure non ha accesso all'area di lavoro creata usando il portale di OMS.  Per concedere l'accesso a questo account dal portale di OMS, vedere [Aggiungere un utente a un'area di lavoro esistente](#add-a-user-to-an-existing-workspace).
>
>

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Modificare un'organizzazione di Azure Active Directory in un'area di lavoro

È possibile modificare un'organizzazione di Azure Active Directory in un'area di lavoro. La modifica dell'organizzazione di Azure Active Directory consente di aggiungere all'area di lavoro gli utenti e i gruppi dalla directory.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Per modificare l'organizzazione di Azure Active Directory in un'area di lavoro

1. Nella pagina Impostazioni del portale di OMS fare clic su **Account** e quindi su **Gestisci utenti**.  
2. Rivedere le informazioni sugli account aziendali e quindi fare clic su **Modifica organizzazione**.  
    ![Modificare l'organizzazione](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Immettere le informazioni sull'identità per l'amministratore del dominio di Azure Active Directory. Successivamente, viene visualizzato un acknowledgement che informa che l'area di lavoro è collegata al dominio di Azure Active Directory.  
    ![Acknowledgment dell'area di lavoro collegata](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Informazioni sull'utilizzo dei dati](log-analytics-usage.md) per informazioni su come analizzare il volume dei dati raccolti dalle soluzioni e inviati dai computer.
* [Aggiungere soluzioni di gestione di Log Analytics da Azure Marketplace](log-analytics-add-solutions.md) per aggiungere funzionalità e raccogliere i dati.
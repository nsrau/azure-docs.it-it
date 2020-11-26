---
title: Monitorare Azure AD B2C con monitoraggio di Azure
titleSuffix: Azure AD B2C
description: Informazioni su come registrare eventi di Azure AD B2C con monitoraggio di Azure usando la gestione delle risorse delegata.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 11/12/2020
ms.openlocfilehash: 6d40eab12c9726459543d0b69e27b73178eba99f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170617"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorare Azure AD B2C con monitoraggio di Azure

Usare monitoraggio di Azure per indirizzare l'accesso Azure Active Directory B2C (Azure AD B2C) e i log di [controllo](view-audit-logs.md) a diverse soluzioni di monitoraggio. È possibile conservare i log per l'uso a lungo termine o l'integrazione con strumenti di gestione di informazioni ed eventi di sicurezza di terze parti per ottenere informazioni approfondite sull'ambiente.

È possibile instradare gli eventi del log a:

* Un [account di archiviazione](../storage/blobs/storage-blobs-introduction.md)di Azure.
* Un' [area di lavoro log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (per analizzare i dati, creare dashboard e avvisi per eventi specifici).
* Un [Hub eventi](../event-hubs/event-hubs-about.md) di Azure (e si integrano con le istanze della logica Splunk e Sumo).

![Monitoraggio di Azure](./media/azure-monitor/azure-monitor-flow.png)

Questo articolo illustra come trasferire i log in un'area di lavoro di Azure Log Analytics. È quindi possibile creare un dashboard o creare avvisi basati sulle attività Azure AD B2C degli utenti.

## <a name="deployment-overview"></a>Panoramica della distribuzione

Azure AD B2C sfrutta [Azure Active Directory il monitoraggio](../active-directory/reports-monitoring/overview-monitoring.md). Per abilitare *le impostazioni di diagnostica* in Azure Active Directory all'interno del tenant di Azure ad B2C, usare [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) per [delegare una risorsa](../lighthouse/concepts/azure-delegated-resource-management.md), che consente al Azure ad B2C ( **provider di servizi**) di gestire una risorsa Azure ad ( **cliente**). Dopo aver completato i passaggi descritti in questo articolo, sarà possibile accedere al gruppo di risorse *Azure-ad-B2C-monitor* che contiene l' [area di lavoro log Analytics](../azure-monitor/learn/quick-create-workspace.md) nel portale di **Azure ad B2C** . Sarà anche possibile trasferire i log da Azure AD B2C nell'area di lavoro Log Analytics.

Durante questa distribuzione, è necessario autorizzare un utente o un gruppo nella directory Azure AD B2C per configurare l'istanza dell'area di lavoro Log Analytics all'interno del tenant che contiene la sottoscrizione di Azure. Per creare l'autorizzazione, si distribuisce un modello di [Azure Resource Manager](../azure-resource-manager/index.yml) nel tenant di Azure ad che contiene la sottoscrizione.

Il diagramma seguente illustra i componenti che verranno configurati nella Azure AD e Azure AD B2C i tenant.

![Proiezione del gruppo di risorse](./media/azure-monitor/resource-group-projection.png)

Durante questa distribuzione, si configureranno sia il tenant di Azure AD B2C che Azure AD tenant in cui verrà ospitata l'area di lavoro Log Analytics. All'account utilizzato per eseguire la distribuzione deve essere assegnato il ruolo di [amministratore globale](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) in entrambi i tenant. È anche importante verificare di aver eseguito l'accesso alla directory corretta quando si completa ogni passaggio, come descritto.

## <a name="1-create-or-choose-resource-group"></a>1. creare o scegliere un gruppo di risorse

Per prima cosa, creare o scegliere un gruppo di risorse contenente l'area di lavoro Log Analytics di destinazione che riceverà i dati da Azure AD B2C. Quando si distribuisce il modello di Azure Resource Manager, è necessario specificare il nome del gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il **tenant Azure ad**.
1. [Creare un gruppo di risorse](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) o sceglierne uno esistente. Questo esempio usa un gruppo di risorse denominato *Azure-ad-B2C-monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. creare un'area di lavoro Log Analytics

Un' **area di lavoro log Analytics** è un ambiente univoco per i dati di log di monitoraggio di Azure. Usare questa area di lavoro Log Analytics per raccogliere dati da Azure AD B2C [log di controllo](view-audit-logs.md)e quindi visualizzarli con query e cartelle di lavoro o creare avvisi.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il **tenant Azure ad**.
1. [Creare un'area di lavoro log Analytics](../azure-monitor/learn/quick-create-workspace.md). Questo esempio usa un'area di lavoro Log Analytics denominata *AzureAdB2C*, in un gruppo di risorse denominato *Azure-ad-B2C-monitor*.

## <a name="3-delegate-resource-management"></a>3. delegare la gestione delle risorse

In questo passaggio si sceglie il tenant Azure AD B2C come provider di **Servizi**. Si definiscono anche le autorizzazioni necessarie per assegnare i ruoli predefiniti di Azure appropriati ai gruppi nel tenant del Azure AD.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 ottenere l'ID tenant Azure AD B2C

Per prima cosa, ottenere l' **ID tenant** della directory di Azure ad B2C (anche noto come ID directory).

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant **Azure ad B2C** .
1. Selezionare **Azure Active Directory**, quindi **Panoramica**.
1. Registrare l' **ID tenant**.

### <a name="32-select-a-security-group"></a>3,2 Selezionare un gruppo di sicurezza

A questo punto, selezionare un gruppo Azure AD B2C o un utente a cui si vuole concedere l'autorizzazione per il gruppo di risorse creato in precedenza nella directory che contiene la sottoscrizione.  

Per semplificare la gestione, è consigliabile utilizzare Azure AD *gruppi* di utenti per ogni ruolo, consentendo di aggiungere o rimuovere singoli utenti al gruppo anziché assegnare le autorizzazioni direttamente a tale utente. In questa procedura dettagliata verrà aggiunto un gruppo di sicurezza.

> [!IMPORTANT]
> Per aggiungere autorizzazioni per un gruppo di Azure AD, il **tipo di gruppo** deve essere impostato su **sicurezza**. Questa opzione è selezionata quando viene creato il gruppo. Per altre informazioni, vedere [Creare un gruppo di base e aggiungere membri con Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Con **Azure Active Directory** ancora selezionato nella directory **Azure ad B2C** , selezionare **gruppi** e quindi selezionare un gruppo. Se non si dispone di un gruppo esistente, creare un gruppo di **sicurezza** , quindi aggiungere i membri. Per ulteriori informazioni, attenersi alla procedura [creare un gruppo di base e aggiungere membri utilizzando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Selezionare **Panoramica** e registrare l' **ID oggetto** del gruppo.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 creare un modello di Azure Resource Manager

Successivamente, verrà creato un modello di Azure Resource Manager che concede Azure AD B2C accesso al gruppo di risorse Azure AD creato in precedenza, ad esempio *Azure-ad-B2C-monitor*. Distribuire il modello dall'esempio GitHub usando il pulsante **Distribuisci in Azure** , che apre il portale di Azure e consente di configurare e distribuire il modello direttamente nel portale. Per questa procedura, assicurarsi di aver eseguito l'accesso al tenant di Azure AD (non al tenant di Azure AD B2C).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant **Azure ad** .
3. Usare il pulsante **Distribuisci in Azure** per aprire il portale di Azure e distribuire il modello direttamente nel portale. Per altre informazioni, vedere [creare un modello di Azure Resource Manager](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Distribuzione in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Nella pagina **distribuzione personalizzata** immettere le informazioni seguenti:

   | Campo   | Definizione |
   |---------|------------|
   | Subscription |  Selezionare la directory che contiene la sottoscrizione di Azure in cui è stato creato il gruppo di risorse *Azure-ad-B2C-monitor* . |
   | Region| Selezionare l'area in cui verrà distribuita la risorsa.  | 
   | Nome dell'offerta msp| Nome che descrive questa definizione. Ad esempio, *Azure ad B2C monitoraggio*.  |
   | Descrizione dell'offerta msp| Breve descrizione dell'offerta. Ad esempio, *Abilita monitoraggio di Azure in Azure ad B2C*.|
   | Gestito dall'ID tenant| **ID tenant** del tenant di Azure ad B2C (noto anche come ID directory). |
   |Authorizations|Specificare una matrice JSON di oggetti che includono la Azure AD `principalId` , `principalIdDisplayName` e Azure `roleDefinitionId` . `principalId`È l' **ID oggetto** del gruppo B2C o dell'utente che avrà accesso alle risorse in questa sottoscrizione di Azure. Per questa procedura dettagliata, specificare l'ID oggetto del gruppo registrato in precedenza. Per `roleDefinitionId` , utilizzare il valore del [ruolo predefinito](../role-based-access-control/built-in-roles.md) per il *ruolo Collaboratore*, `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Nome gruppo di risorse | Il nome del gruppo di risorse creato in precedenza nel tenant del Azure AD. Ad esempio, *Azure-ad-B2C-monitor*. |

   Nell'esempio seguente viene illustrata una matrice di autorizzazioni con un gruppo di sicurezza.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Dopo aver distribuito il modello, il completamento della proiezione delle risorse può richiedere alcuni minuti (in genere non più di cinque). È possibile verificare la distribuzione nel tenant di Azure AD e ottenere i dettagli della proiezione di risorse. Per altre informazioni, vedere [visualizzare e gestire i provider di servizi](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Selezionare la sottoscrizione

Dopo aver distribuito il modello e aver atteso alcuni minuti per il completamento della proiezione di risorse, attenersi alla seguente procedura per associare la sottoscrizione alla directory Azure AD B2C.

1. Disconnettersi dal portale di Azure se è stato eseguito l'accesso (in questo modo è possibile aggiornare le credenziali della sessione nel passaggio successivo).
2. Accedere al [portale di Azure](https://portal.azure.com) con l'account amministrativo **Azure ad B2C** . Questo account deve essere un membro del gruppo di sicurezza specificato nel passaggio [delega della gestione delle risorse](#3-delegate-resource-management) .
3. Nella barra degli strumenti del portale selezionare l'icona **Directory e sottoscrizione**.
4. Selezionare la directory Azure AD che contiene la sottoscrizione di Azure e il gruppo di risorse *Azure-ad-B2C-monitor* creato.

    ![Cambia directory](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Verificare di aver selezionato la directory e la sottoscrizione corrette. In questo esempio vengono selezionate tutte le directory e tutte le sottoscrizioni.

    ![Tutte le directory selezionate nel filtro directory & sottoscrizione](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. configurare le impostazioni di diagnostica

Le impostazioni di diagnostica definiscono dove devono essere inviati i log e le metriche per una risorsa. Le possibili destinazioni sono:

- [Account di archiviazione di Azure](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)
- Soluzioni di [Hub eventi](../azure-monitor/platform/resource-logs.md#send-to-azure-event-hubs)
- [area di lavoro Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)

In questo esempio viene usata l'area di lavoro Log Analytics per creare un dashboard.

### <a name="51-create-diagnostic-settings"></a>5,1 creare le impostazioni di diagnostica

È ora possibile [creare le impostazioni di diagnostica](../active-directory/reports-monitoring/overview-monitoring.md) nel portale di Azure.

Per configurare le impostazioni di monitoraggio per i log attività Azure AD B2C:

1. Accedere al [portale di Azure](https://portal.azure.com/) con l'account amministrativo Azure ad B2C. Questo account deve essere membro del gruppo di sicurezza specificato nel passaggio [selezionare un gruppo di sicurezza](#32-select-a-security-group) .
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Selezionare **Azure Active Directory**
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
1. Se sono presenti impostazioni per la risorsa, verrà visualizzato un elenco di impostazioni già configurate. Selezionare **Aggiungi impostazioni di diagnostica** per aggiungere una nuova impostazione o selezionare **modifica** per modificare un'impostazione esistente. Ogni impostazione può avere non più di uno dei tipi di destinazione.

    ![Riquadro impostazioni di diagnostica in portale di Azure](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Assegnare un nome all'impostazione se non ne è già presente uno.
1. Selezionare la casella per ogni destinazione per inviare i log. Selezionare **Configura** per specificare le impostazioni **come descritto nella tabella seguente**.
1. Selezionare **Invia a log Analytics**, quindi selezionare il **nome dell'area di lavoro** creata in precedenza ( `AzureAdB2C` ).
1. Selezionare **AuditLogs** e **SignInLogs**.
1. Selezionare **Salva**.

> [!NOTE]
> Possono essere necessari fino a 15 minuti dopo la creazione di un evento [in un'area di lavoro log Analytics](../azure-monitor/platform/data-ingestion-time.md). Sono inoltre disponibili altre informazioni sulle [latenze per la creazione di report Active Directory](../active-directory/reports-monitoring/reference-reports-latencies.md), che possono influisca sull'obsolescenza dei dati e svolgono un ruolo importante nella creazione di report.

Se viene visualizzato il messaggio di errore "per configurare le impostazioni di diagnostica per usare monitoraggio di Azure per la directory Azure AD B2C, è necessario configurare la gestione delle risorse delegata", assicurarsi di accedere con un utente membro del gruppo di [sicurezza](#32-select-a-security-group) e [selezionare la sottoscrizione](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. visualizzare i dati

A questo punto è possibile configurare l'area di lavoro di Log Analytics per visualizzare i dati e configurare gli avvisi. Queste configurazioni possono essere eseguite sia nel tenant del Azure AD che nel tenant del Azure AD B2C.

### <a name="61-create-a-query"></a>6,1 creare una query

Le query su log consentono di sfruttare appieno il valore dei dati raccolti nei log di Monitoraggio di Azure. Un linguaggio di query avanzato consente di unire dati da più tabelle, aggregare set di dati di grandi dimensioni ed eseguire operazioni complesse con codice minimo. È possibile rispondere a qualsiasi domanda ed eseguire l'analisi fino a quando i dati di supporto sono stati raccolti e si comprende come costruire la query corretta. Per altre informazioni, vedere [Introduzione alle query di log in monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md).

1. Dall' **area di lavoro log Analytics** selezionare **log**
1. Nell'editor di query incollare la query del [linguaggio di query kusto](/azure/data-explorer/kusto/query/) seguente. Questa query Mostra l'utilizzo dei criteri per operazione negli ultimi x giorni. La durata predefinita è impostata su 90 giorni (90D). Si noti che la query si concentra solo sull'operazione in cui un token/codice viene emesso dal criterio.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Selezionare **Run** (Esegui). I risultati della query vengono visualizzati nella parte inferiore della schermata.
1. Per salvare la query per un uso successivo, selezionare **Salva**.

   ![Editor di log Log Analytics](./media/azure-monitor/query-policy-usage.png)

1. Immettere i dettagli seguenti:

    - **Nome** : immettere il nome della query.
    - **Salva con nome** `query` .
    - **Category** : selezionare `Log` .

1. Selezionare **Salva**.

È anche possibile modificare la query per visualizzare i dati usando l'operatore [Render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) .

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Torta dell'editor di log Log Analytics](./media/azure-monitor/query-policy-usage-pie.png)

Per altri esempi, vedere il [repository GitHub Azure ad B2C Siem](https://aka.ms/b2csiem).

### <a name="62-create-a-workbook"></a>6,2 creare una cartella di lavoro

Le cartelle di lavoro offrono un canvas flessibile per l'analisi dei dati e la creazione di report visivi avanzati nel portale di Azure. Consentono di accedere a più origini dati da Azure e combinarle in esperienze interattive unificate. Per altre informazioni, vedere [cartelle di lavoro di monitoraggio di Azure](../azure-monitor/platform/workbooks-overview.md).

Seguire le istruzioni riportate di seguito per creare una nuova cartella di lavoro usando un modello di raccolta JSON. Questa cartella di lavoro fornisce un dashboard **User Insights** e **Authentication** per Azure ad B2C tenant.

1. Dall' **area di lavoro log Analytics** selezionare **cartelle di lavoro**.
1. Dalla barra degli strumenti selezionare **+ nuova** opzione per creare una nuova cartella di lavoro.
1. Nella pagina **nuova cartella di lavoro** selezionare la **Editor avanzato** utilizzando l' **</>** opzione sulla barra degli strumenti.

     ![Modello di raccolta](./media/azure-monitor/wrkb-adv-editor.png)

1. Selezionare il **modello raccolta**.
1. Sostituire il codice JSON nel **modello di raccolta**  con il contenuto della [cartella di lavoro di Azure ad B2C Basic](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Applicare il modello usando il pulsante **applica** .
1. Selezionare il pulsante **modifica completato** dalla barra degli strumenti per completare la modifica della cartella di lavoro.
1. Infine, salvare la cartella di lavoro utilizzando il pulsante **Salva** sulla barra degli strumenti.
1. Fornire un **titolo**, ad esempio *Azure ad B2C dashboard*.
1. Selezionare **Salva**.

    ![Salvare la cartella di lavoro](./media/azure-monitor/wrkb-title.png)

Nella cartella di lavoro vengono visualizzati i report sotto forma di dashboard.

![Primo dashboard cartella di lavoro](./media/azure-monitor/wkrb-dashboard-1.png)

![Secondo dashboard cartella di lavoro](./media/azure-monitor/wrkb-dashboard-2.png)

![Terzo dashboard cartella di lavoro](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Creare avvisi

Gli avvisi vengono creati tramite le regole di avviso in Monitoraggio di Azure e possono eseguire automaticamente query salvate o ricerche personalizzate nei log a intervalli regolari. È possibile creare avvisi in base a metriche di prestazioni specifiche o quando vengono creati determinati eventi, in assenza di un evento o per un numero di eventi creati all'interno di un intervallo di tempo specifico. Ad esempio, è possibile usare gli avvisi per ricevere una notifica quando il numero medio di accesso supera una determinata soglia. Per altre informazioni, vedere [Creare avvisi](../azure-monitor/learn/tutorial-response.md).


Usare le istruzioni seguenti per creare un nuovo avviso di Azure, che invierà una [notifica tramite posta elettronica](../azure-monitor/platform/action-groups.md#configure-notifications) ogni volta che si verifica un calo del 25% nel **totale delle richieste** rispetto al periodo precedente. L'avviso viene eseguito ogni 5 minuti e cerca il calo nelle ultime 24 ore di Windows. Gli avvisi vengono creati utilizzando il linguaggio di query kusto.


1. Dall' **area di lavoro log Analytics** selezionare **log**. 
1. Creare una nuova **query kusto** usando la query seguente.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Selezionare **Esegui** per testare la query. Verranno visualizzati i risultati se il numero totale di richieste nelle ultime 24 ore è pari a un calo del 25%.
1. Per creare una regola di avviso basata sulla query precedente, usare l'opzione **+ nuova regola di avviso** disponibile sulla barra degli strumenti.
1. Nella pagina **Crea una regola di avviso** selezionare **nome condizione** 
1. Nella pagina **Configura logica** per i segnali impostare i valori seguenti e quindi usare il pulsante **fine** per salvare le modifiche.
    * Logica di avviso: impostare il **numero di risultati** **maggiore di** **0**.
    * Valutazione basata su: selezionare **1440** per periodo (in minuti) e **5** per frequenza (in minuti) 

    ![Creare una condizione della regola di avviso](./media/azure-monitor/alert-create-rule-condition.png)

Dopo la creazione dell'avviso, passare all' **area di lavoro log Analytics** e selezionare **avvisi**. In questa pagina vengono visualizzati tutti gli avvisi attivati nell'opzione durata impostata per **intervallo di tempo** .  

### <a name="configure-action-groups"></a>Configurare i gruppi di azioni

Gli avvisi di Monitoraggio di Azure e di integrità dei servizi usano gruppi di azioni per notificare agli utenti l'attivazione di un avviso. È possibile includere l'invio di una chiamata vocale, un SMS, un messaggio di posta elettronica; o attivazione di diversi tipi di azioni automatiche. Seguire le linee guida [per creare e gestire gruppi di azioni nella portale di Azure](../azure-monitor/platform/action-groups.md)

Di seguito è riportato un esempio di messaggio di posta elettronica di notifica di avviso. 

   ![Notifica tramite posta elettronica](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Più tenant

Per caricare più log del tenant di Azure AD B2C nella stessa area di lavoro Log Analytics (o nell'account di archiviazione di Azure o nell'hub eventi), è necessario disporre di distribuzioni separate con valori di **nome dell'offerta msp** diversi. Assicurarsi che l'area di lavoro di Log Analytics si trovi nello stesso gruppo di risorse in cui è stato configurato in [creare o scegliere un gruppo di risorse](#1-create-or-choose-resource-group).

Quando si utilizzano più aree di lavoro Log Analytics, utilizzare la [query tra aree](../azure-monitor/log-query/cross-workspace-query.md) di lavoro per creare query che funzionano in più aree di lavoro. Ad esempio, la query seguente esegue un join di due log di controllo da tenant diversi in base alla stessa categoria (ad esempio, l'autenticazione):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

I log di monitoraggio di Azure sono progettati per scalare e supportare la raccolta, l'indicizzazione e l'archiviazione di grandi quantità di dati al giorno da qualsiasi origine nell'azienda o distribuita in Azure. Per impostazione predefinita, i log vengono conservati per 30 giorni, ma la durata della conservazione può essere aumentata fino a due anni. Informazioni su come [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](../azure-monitor/platform/manage-cost-storage.md). Dopo aver selezionato il piano tariffario, è possibile [modificare il periodo di conservazione dei dati](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Passaggi successivi

* Altri esempi sono disponibili nella [raccolta Azure ad B2C Siem](https://aka.ms/b2csiem). 

* Per altre informazioni sull'aggiunta e la configurazione delle impostazioni di diagnostica in monitoraggio di Azure, vedere [esercitazione: raccogliere e analizzare i log delle risorse da una risorsa di Azure](../azure-monitor/insights/monitor-azure-resource.md).

* Per informazioni sul flusso di log Azure AD a un hub eventi, vedere [esercitazione: trasmettere i log di Azure Active Directory a un hub eventi di Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
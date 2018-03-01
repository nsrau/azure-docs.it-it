---
title: Registrazione diagnostica per Azure Analysis Services | Microsoft Docs
description: Informazioni sulla configurazione della registrazione diagnostica per Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: cadd47d2e5f490f82846ea562803fcd60f5405a7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="setup-diagnostic-logging"></a>Configurare la registrazione diagnostica

Un aspetto importante di qualsiasi soluzione di Analysis Services è costituito dal monitoraggio delle prestazioni dei server. I [log di diagnostica delle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) consentono di monitorare e inviare log ad [Archiviazione di Microsoft Azure](https://azure.microsoft.com/services/storage/), trasmetterli agli [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) ed esportarli in [Log Analytics](https://azure.microsoft.com/services/log-analytics/), che è una funzionalità di [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Registrazione diagnostica in Archiviazione, hub eventi oppure Operations Management Suite mediante Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Informazioni registrate

È possibile selezionare le categorie **Engine** (Motore), **Service** (Servizio) e **Metrics** (Metriche).

### <a name="engine"></a>Engine (Motore)

Se si seleziona la categoria **Engine** (Motore) vengono registrati nel log tutti gli [eventi estesi](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) (XEvent). Non è possibile selezionare eventi singoli. 

|Categorie di eventi estesi |Nome evento  |
|---------|---------|
|Controllo di sicurezza    |   Connessione di controllo      |
|Controllo di sicurezza    |   Disconnessione di controllo      |
|Controllo di sicurezza    |   Inizi e arresti server di controllo      |
|Report di stato     |   Inizio report di stato      |
|Report di stato     |   Fine report di stato      |
|Report di stato     |   Report di stato corrente      |
|Query     |  Inizio query       |
|Query     |   Fine query      |
|Comandi:     |  Inizio comando       |
|Comandi:     |  Fine comando       |
|Errori e avvisi     |   Tipi di errore      |
|Scoprire     |   Fine individuazione      |
|Notifica     |    Notifica     |
|sessione     |  Inizializzazione sessione       |
|Blocchi    |  Deadlock       |
|Elaborazione di query     |   Inizio query SE VertiPaq      |
|Elaborazione di query     |   Fine query SE VertiPaq      |
|Elaborazione di query     |   Corrispondenza cache query SE VertiPaq      |
|Elaborazione di query     |   Inizio query diretta      |
|Elaborazione di query     |  Fine query diretta       |

### <a name="service"></a>Service

|Nome operazione  |Ambito  |
|---------|---------|
|CreateGateway     |   Configurazione di un gateway nel server      |
|ResumeServer     |    Ripresa di un server     |
|SuspendServer    |   Sospensione di un server      |
|DeleteServer     |    Eliminazione di un server     |
|RestartServer    |     Riavvio di un server tramite SQL Server Management Studio o PowerShell    |
|GetServerLogFiles    |    Esportazione di un log del server tramite PowerShell     |
|ExportModel     |   Esportazione di un modello nel portale tramite Apri in Visual Studio     |

### <a name="all-metrics"></a>Tutte le metriche

Se si seleziona la categoria Metriche, vengono registrate nel log le stesse [metriche del server](analysis-services-monitor.md#server-metrics) visualizzate in Metrics (Metriche).

## <a name="setup-diagnostics-logging"></a>Configurare la registrazione diagnostica

### <a name="azure-portal"></a>Portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) > server fare clic su **Log di diagnostica** nel riquadro di sinistra e quindi fare clic su **Abilita diagnostica**.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. In **Impostazioni di diagnostica** specificare le opzioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archivia in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md). Seguire le istruzioni per creare un account di Resource Manager di uso generale, quindi selezionare l'account di archiviazione ritornando in questa pagina del portale. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Streaming in un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi esistente e un hub eventi a cui connettersi. Per altre informazioni, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Tornare a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome dei criteri.
    * **Invia a Log Analytics**. Per usare questa opzione, usare un'area di lavoro esistente o creare una nuova area di lavoro di Log Analytics seguendo la procedura per [creare una nuova area di lavoro](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) nel portale. Per altre informazioni sulla visualizzazione dei log in Log Analytics, vedere [Visualizzare i log in Log Analytics](#view-in-loganalytics).

    * **Engine** (Motore). Selezionare questa opzione per registrare gli eventi estesi. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Servizio**. Selezionare questa opzione per registrare gli eventi a livello di servizio. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Metrics** (Metriche). Selezionare questa opzione per archiviare informazioni dettagliate in [Metrics](analysis-services-monitor.md#server-metrics) (Metriche). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.

3. Fare clic su **Save**.

    Se si riceve il messaggio di errore "Non è stato possibile aggiornare la diagnostica per \<nome area di lavoro>. La sottoscrizione \<id sottoscrizione> non è registrata per l'uso di microsoft.insights." seguire le istruzioni in [Risolvere i problemi relativi a Diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se in seguito si vuole modificare la modalità di salvataggio dei log di diagnostica, è possibile tornare in questa pagina e modificare le impostazioni.

### <a name="powershell"></a>PowerShell

Ecco i comandi di base per iniziare. Se si desiderano istruzioni dettagliate su come configurare la registrazione per un account di archiviazione usando PowerShell, vedere l'esercitazione più avanti in questo articolo.

Per abilitare le metriche e la registrazione diagnostica con PowerShell, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modello di Resource Manager

Informazioni su come [abilitare le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Gestire i log

I log sono in genere disponibili entro due ore dall'impostazione della registrazione. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Assicurarsi di impostare un periodo di conservazione dopo il quale i log obsoleti vengono eliminati dall'account di archiviazione.

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Le metriche e gli eventi del server sono integrati con gli eventi estesi in Log Analytics per consentirne l'analisi affiancata. Log Analytics può anche essere configurato in modo da ricevere gli eventi da altri servizi di Azure e offrire una vista olistica dei dati della registrazione diagnostica in tutta l'architettura.

Per visualizzare i dati di diagnostica in Log Analytics, aprire la pagina Ricerca log dal menu a sinistra o nell'area Gestione, come illustrato nella figura seguente.

![Opzioni di Ricerca log nel portale di Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Dopo aver abilitato la raccolta dei dati, in **Ricerca log** fare clic su **Tutti i dati raccolti**.

In **Tipo** fare clic su **AzureDiagnostics** e quindi su **Applica**. AzureDiagnostics include eventi del motore e del servizio. Notare che viene creata una query di Log Analytics nell'immediato. Il campo EventClass\_s contiene nomi di eventi estesi, che possono apparire familiari a chi ha usato gli eventi estesi per la registrazione locale.

Fare clic su **EventClass\_s** o su uno dei nomi di evento per consentire a Log Analytics di proseguire con la creazione della query. Assicurarsi di salvare le query per un successivo riutilizzo.

Vedere Operations Management Suite, che fornisce al sito Web funzionalità avanzate di query, dashboard e avvisi sui dati di Log Analytics.

### <a name="queries"></a>Query

Sono disponibili centinaia di query. Di seguito ne sono riportate alcune per iniziare.
Per altre informazioni sull'uso del nuovo linguaggio di query di Ricerca log, vedere [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

* Query che restituiscono query inviate a Azure Analysis Services che hanno richiesto più di cinque minuti (300.000 millisecondi) per il completamento.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identificare le repliche di scale-out.

    ```
    search * | summarize count() by ServerName_s
    ```
    Quando si usa la funzionalità di scale-out, è possibile identificare le repliche di sola lettura perché ai valori del campo ServerName\_s viene accodato il numero di istanza della replica. Il campo della risorsa contiene il nome della risorsa di Azure, che corrisponde al nome del server visibile agli utenti. Il campo IsQueryScaleoutReadonlyInstance_s è impostato su true per le repliche.



> [!TIP]
> Se si dispone di una query di Log Analytics interessante da condividere, è possibile aggiungerla a questo articolo, purché si disponga di un account GitHub. È sufficiente fare clic su **Modifica** in alto a destra in questa pagina.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Esercitazione: Attivare la registrazione mediante PowerShell
In questa esercitazione rapida vengono creati un account di archiviazione nella stessa sottoscrizione e un gruppo di risorse come server di Analysis Services. Viene poi usato il cmdlet Set-AzureRmDiagnosticSetting per attivare la registrazione diagnostica e inviare i risultati al nuovo account di archiviazione.

### <a name="prerequisites"></a>prerequisiti
Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Un server di Azure Analysis Services esistente. Per istruzioni sulla creazione di una risorsa del server, vedere [Creare un server nel portale di Azure](analysis-services-create-server.md) o [Creare un server Azure Analysis Services tramite PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Connettersi alle sottoscrizioni

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```powershell
Login-AzureRmAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzureRmSubscription
```

Per specificare quindi la sottoscrizione associata all'account di Azure Analysis Services per la registrazione, digitare:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se più sottoscrizioni sono associate all'account, è importante specificarne una.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Creare un nuovo account di archiviazione per i log

È possibile usare un account di archiviazione esistente per i log, purché sia nella stessa sottoscrizione del server. Per questa esercitazione si crea un nuovo account di archiviazione dedicato ai log di Analysis Services. Per praticità, i dettagli dell'account di archiviazione vengono memorizzati in una variabile denominata **sa**.

Viene usato anche lo stesso gruppo di risorse che contiene il server Analysis Services. Sostituire i valori di `awsales_resgroup`, `awsaleslogs` e `West Central US` con valori di propria scelta:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificare l'account del server per i log

Impostare il nome dell'account su una variabile denominata **account**, dove ResourceName è il nome dell'account.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione, usare il cmdlet Set-AzureRmDiagnosticSetting con le variabili create per il nuovo account di archiviazione, l'account del server e la categoria. Eseguire questo comando, impostando il flag **-Enabled** su **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

L'output dovrebbe essere simile a quanto segue:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Ciò conferma che la registrazione è abilitata per il server. Le informazioni vengono salvate nell'account di archiviazione.

È possibile anche impostare criteri di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. È, ad esempio, possibile impostare i criteri di conservazione usando il flag **-RetentionEnabled** impostato su **$true** e impostare il parametro **-RetentionInDays** su **90**. I log antecedenti a 90 giorni vengono eliminati automaticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [Registrazione diagnostica delle risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Vedere [Set-AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) nella Guida di PowerShell.
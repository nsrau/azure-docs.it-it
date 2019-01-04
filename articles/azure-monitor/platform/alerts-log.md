---
title: Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure
description: Usare Monitoraggio di Azure per creare, visualizzare e gestire le regole degli avvisi relativi ai log in Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: be86e961d04b600f112a173c041b60cbe50ea00d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725058"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure  

## <a name="overview"></a>Panoramica
Questo articolo illustra come configurare gli avvisi del log tramite l'interfaccia degli avvisi nel portale di Azure. La definizione di una regola di avviso è costituita da tre parti:
- Destinazione: la risorsa specifica di Azure da monitorare
- Criteri: condizione specifica o logica che, se rilevata nel segnale, deve attivare l'azione
- Azione: chiamata specifica inviata a un ricevitore di una notifica di posta elettronica, SMS, webhook e così via.

Il termine **Avvisi del log** viene usato per descrivere gli avvisi in cui il segnale è basato su una query personalizzata in [Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) o [Application Insights](../../azure-monitor/app/analytics.md). Altre informazioni su funzionalità, terminologia e tipi su [Avvisi del log - Panoramica](../../azure-monitor/platform/alerts-unified-log.md).

> [!NOTE]
> I dati di log più comuni di [Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) sono ora disponibili anche nella piattaforma di metriche in Monitoraggio di Azure. Per i dettagli, vedere [Metric Alert for Logs](../../azure-monitor/platform/alerts-metric-logs.md) (Avvisi di metrica per i log).

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gestione degli avvisi del log dal portale di Azure

Di seguito è riportata una Guida dettagliata all'uso degli avvisi del log tramite l'interfaccia del portale di Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Creare una regola per gli avvisi relativi ai log con il portale di Azure
1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.  
    ![Monitoraggio](media/alerts-log/AlertsPreviewMenu.png)

1. Selezionare il pulsante **Nuova regola di avviso** per creare un nuovo avviso in Azure.
    ![Aggiungi avviso](media/alerts-log/AlertsPreviewOption.png)

1. La sezione Crea avviso viene visualizzata con le tre parti costituite da: *Definire la condizione dell'avviso*, *Definire i dettagli dell'avviso* e *Definire il gruppo di azioni*.

    ![Creare una regola](media/alerts-log/AlertsPreviewAdd.png)

1.  Definire la condizione dell'avviso tramite il collegamento **Seleziona risorsa** e specificare la destinazione selezionando una risorsa. Filtrare scegliendo la _Sottoscrizione_, il _Tipo di risorsa_ e infine la _Risorsa_ necessaria. 

    >[!NOTE]

    > Prima di procedere, per creare un avviso relativo ai log verificare che il segnale di **log** sia disponibile per la risorsa selezionata.
    ![Selezionare una risorsa](media/alerts-log/Alert-SelectResourceLog.png)

 
1. *Avvisi relativi ai log*: verificare che **Tipo di risorsa** sia un'origine dati di analisi come *Log Analytics* o *Application Insights* e un tipo di segnale **Log** quindi, dopo aver selezionato la **risorsa** appropriata, fare clic su *Operazione completata*. Usare quindi il pulsante **Aggiungi criteri** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa e nell'elenco dei segnali selezionare l'opzione di **ricerca log personalizzata** per il servizio di monitoraggio del log scelto, ad esempio *Log Analytics* o *Application Insights*.

   ![Selezionare una risorsa - ricerca log personalizzata](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Avvisi può importare query di Analytics come tipo di segnale **Log (query salvata)**, come illustrato nella figura precedente. Gli utenti possono quindi perfezionare la query in Analytics e salvarla per uso futuri in Avvisi. Altri dettagli sull'uso del salvataggio di query sono disponibili in [Usare la ricerca log in Log Analytics](../../azure-monitor/log-query/log-query-overview.md) o in [Query condivisa in Analytics in Application Insights](../../azure-monitor/log-query/log-query-overview.md). 

1.  *Avvisi relativi ai log*: una volta selezionata, la query per gli avvisi può essere immessa nel campo **Query di ricerca**. Se la sintassi della query non è corretta, nel campo viene visualizzato un errore in ROSSO. Se la sintassi della query è corretta, i dati cronologici della query specificata vengono visualizzati sotto forma di grafico come riferimento con un'opzione per modificare l'intervallo di tempo dalle ultime sei ore alla settimana precedente.

 ![Configurare una regola di avviso](media/alerts-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > La visualizzazione dei dati cronologici è possibile solo se i risultati della query includono dettagli temporali. Se la query restituisce dati di riepilogo o specifici valori di colonna, questi dati vengono visualizzati come tracciato singolo.

    >  Per il tipo Unità di misura della metrica degli avvisi del log con Application insights, è possibile specificare la variabile specifica per raggruppare i dati con l'opzione **Aggrega in base a**, come illustrato di seguito:

    ![opzione aggrega in base a](media/alerts-log/aggregate-on.png)

1.  *Avvisi relativi ai log*: Con la visualizzazione impostata, è possibile selezionare **Logica avvisi** dalle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Specificare l'ora in cui valutare la condizione specificata nella logica tramite l'opzione **Periodo**. Specificare la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**.
Gli **avvisi del log** possono basarsi su:
   - *Numero di record*: viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
   - *Misura della metrica*: viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* in base al valore scelto. Il numero di violazioni per un avviso è il numero di volte in cui viene superata la soglia nel periodo di tempo scelto. È possibile specificare le violazioni totali per qualsiasi combinazione di violazioni nei set di risultati o le violazioni consecutive necessarie che devono verificarsi in campioni consecutivi. Per altre informazioni, vedere [Avvisi del log e relativi tipi](../../azure-monitor/platform/alerts-unified-log.md).


1. Come secondo passaggio, definire un nome per l'avviso nel campo **Nome regola di avviso** insieme a una **Descrizione** con le specifiche per l'avviso e il valore di **Gravità** nelle opzioni disponibili. Questi dettagli vengono riutilizzati in tutti i messaggi di avviso, le notifiche o notifiche push eseguite da Monitoraggio di Azure. Inoltre, l'utente può scegliere di attivare immediatamente la regola di avviso durante la creazione attivando in modo appropriato l'opzione **Abilita regola alla creazione**.

    Solo per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive nei dettagli degli avvisi:

    - **Elimina avvisi**: Quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, crea i record di avviso. L'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate.

        ![Elimina avvisi per gli avvisi del log](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Specificare un valore di eliminazione dell'avviso maggiore della frequenza di avviso per garantire che le notifiche vengano arrestate senza sovrapposizione

1. Come terzo e ultimo passaggio, specificare se un **gruppo di azioni** deve essere attivato per la regola di avviso quando viene soddisfatta la condizione dell'avviso. È possibile scegliere qualsiasi gruppo di azioni esistente con un avviso o creare un nuovo gruppo di azioni. In base al gruppo di azioni selezionato, quando viene attivato l'avviso, Azure eseguirà queste operazioni: invio di messaggi di posta elettronica, invio di SMS, chiamata di webhook, correzione dell'uso di runbook di Azure, invio di notifiche push allo strumento Gestione dei servizi IT e così via. Altre informazioni sui [gruppi di azioni](action-groups.md).

    > [!NOTE]
    > Vedere i [vincoli dei servizi di sottoscrizione di Azure](../../azure-subscription-service-limits.md) per i vincoli sui payload di Runbook attivati per gli avvisi del log tramite i gruppi di azioni di Azure

    Per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive per eseguire l'override di quelle predefinite:

    - **Notifica tramite posta elettronica**: esegue l'override dell'*oggetto di posta elettronica* nel messaggio di posta elettronica inviato tramite il gruppo di azioni, se esistono una o più azioni di posta elettronica nel gruppo di azioni. Non è possibile modificare il corpo del messaggio e questo campo **non** è destinato agli indirizzi di posta elettronica.
    - **Includi payload JSON personalizzato**: esegue l'override del webhook JSON usato dai gruppi di azioni, se nel gruppo di azioni sono presenti una o più azioni webhook. Un utente può specificare il formato JSON da usare per tutti i webhook configurati nel gruppo di azioni associato. Per altre informazioni sui formati webhook, vedere [Azioni webhook per le regole di avviso relative ai log](../../azure-monitor/platform/alerts-log-webhook.md). L'opzione di visualizzazione Webhook consente di verificare il formato tramite i dati JSON di esempio.

        ![Override dell'azione per gli avvisi del log](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se tutti i campi sono validi e hanno un segno di spunta verde, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso in Monitoraggio di Azure - Avvisi. Tutti gli avvisi possono essere visualizzati nella dashboard di Avvisi.

    ![Creazione di regole](media/alerts-log/AlertsPreviewCreate.png)

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Gli utenti possono anche finalizzare le query di analisi nella [pagina Log Analytics nel portale di Azure](../../azure-monitor/log-query/portals.md#log-analytics-page
) e quindi eseguirne il push per creare un avviso tramite il pulsante "Imposta avviso", quindi seguendo le istruzioni dal passaggio 6 nell'esercitazione precedente.

 ![Log Analytics - Imposta avviso](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visualizzare e gestire gli avvisi del log nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.  

1. Viene visualizzato il **Dashboard degli avvisi**, in cui tutti gli avvisi di Azure (inclusi gli avvisi relativi ai log) vengono riportati in una sola scheda; includendo ogni istanza relativa al momento in cui è stata attivata la regola di avviso del log. Per altre informazioni, consultare [Gestione avvisi](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Le regole di avviso del log comprendono la logica personalizzata basata su query fornita dagli utenti e quindi senza stato risolto. Per questo motivo viene generata ogni volta che vengono soddisfatte le condizioni specificate nella regola di avviso del log. 


1. Selezionare il pulsante **Gestisci regole** nella barra superiore per passare alla sezione di gestione delle regole in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
    ![gestione delle regole di avviso](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gestione degli avvisi del log tramite il modello di risorsa di Azure
Attualmente gli avvisi del log possono essere creati usando due diversi modelli di risorse, secondo la piattaforma di analisi su cui si deve basare l'avviso (ovvero) Log Analytics o Application Insights.

Pertanto, la sezione seguente fornisce dettagli sull'uso del modello di risorsa per gli avvisi del log per ogni piattaforma di analisi.

### <a name="azure-resource-template-for-log-analytics"></a>Modello di risorsa di Azure per Log Analytics
Gli avvisi del log di Log Analytics vengono creati da regole di avviso che eseguono una ricerca salvata a intervalli regolari. Se i risultati della query corrispondono ai criteri specificati, viene creato un record di avviso e vengono eseguite una o più azioni. 

Il modello di risorsa per la ricerca salvata di Log Analytics e gli avvisi di Log Analytics sono disponibili nella sezione Log Analytics della documentazione. Per altre informazioni, vedere [Aggiunta di avvisi e di ricerche salvate di Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md) che include esempi illustrativi e dettagli sullo schema.

### <a name="azure-resource-template-for-application-insights"></a>Modello di risorse di Azure per Application Insights
L'avviso del log per le risorse di Application Insights ha il tipo `Microsoft.Insights/scheduledQueryRules/`. Per altre informazioni su questo tipo di risorsa, vedere il [riferimento all'API per regole di query pianificate in Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

La struttura riportata di seguito per la [creazione di regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) si basa su modello di risorse e un set di dati di esempio come variabili.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Il campo di tag con il collegamento nascosto alla risorsa di destinazione è obbligatorio se si usa la chiamata API per le [regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) o il modello di risorse. 

Per usare il JSON di esempio in questa procedura dettagliata, è possibile salvarlo con il nome sampleScheduledQueryRule.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Gestione degli avvisi del log tramite PowerShell, CLI o API
Attualmente gli avvisi del log possono essere creati usando due diverse API conformi a Resource Manager, secondo la piattaforma di analisi su cui si deve basare l'avviso (ovvero) Log Analytics o Application Insights.

Pertanto, la sezione seguente fornisce dettagli sull'uso dell'API tramite Powershell o CLI per gli avvisi del log per ogni piattaforma di analisi.

### <a name="powershell-cli-or-api-for-log-analytics"></a>PowerShell, CLI o API per Log Analytics
L'API REST degli avvisi di Log Analytics è RESTful ed è accessibile tramite l'API REST di Azure Resource Manager. L'API è quindi accessibile da una riga di comando di PowerShell. Offre i risultati della ricerca in formato JSON per poter essere usati in molti modi diversi a livello di codice.

In [Creare e gestire regole di avviso in Log Analytics con l'API REST](../../azure-monitor/platform/api-alerts.md) sono disponibili esempi per accedere all'API da Powershell.

### <a name="powershell-cli-or-api-for-application-insights"></a>PowerShell, CLI o API per Application Insights
L'[API delle regole di query pianificate in Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) è un'API REST completamente compatibile con l'API REST di Azure Resource Manager. Può pertanto essere usata con il cmdlet di Resource Manager tramite Powershell e l'interfaccia della riga di comando di Azure.

Di seguito viene illustrato l'utilizzo tramite il cmdlet di Azure Resource Manager in PowerShell per il modello di risorsa di esempio riportato in precedenza (sampleScheduledQueryRule.json) nella [sezione Modello di risorsa](#azure-resource-template-for-application-insights):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Di seguito viene illustrato l'utilizzo tramite il comando di Azure Resource Manager nell'interfaccia della riga di comando di Azure per il modello di risorsa di esempio riportato in precedenza (sampleScheduledQueryRule.json) nella [sezione Modello di risorsa](#azure-resource-template-for-application-insights):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Se l'operazione è stata eseguita correttamente, sarà restituito il valore 201 a indicare la creazione della nuova regola di avviso. Se invece è stata modificata una regola di avviso esistente, sarà restituito il valore 200.


  
## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [avvisi del log in Avvisi di Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Comprendere le [azioni webhook per gli avvisi del log](../../azure-monitor/platform/alerts-log-webhook.md)
* Altre informazioni su [Application Insights](../../azure-monitor/app/analytics.md)
* Altre informazioni su [Log Analytics](../../azure-monitor/log-query/log-query-overview.md). 


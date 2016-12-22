---
title: "Creare test di disponibilità, avviso e risorse di Application Insights in PowerShell | Microsoft Docs"
description: Automatizzare la gestione delle risorse di Application Insights usando un modello di Azure Resource Manager.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: fc2d3c3fee5abbab0eab16c106c88c8753e703cc
ms.openlocfilehash: f6302d7d11691635c286164f11be74ff3a1ad8dd


---
# <a name="create-application-insights-resources-using-powershell"></a>Creazione di risorse Application Insights con PowerShell
In questo articolo viene illustrato come creare automaticamente una risorsa [Application Insights](app-insights-overview.md) in Azure. Questo procedimento potrebbe, ad esempio, essere utilizzato come parte di un processo di compilazione. Insieme alla risorsa di base Application Insights, è possibile creare [test web di disponibilità](app-insights-monitor-web-app-availability.md), [impostare avvisi](app-insights-alerts.md), e creare altre risorse di Azure.

La chiave per la creazione di queste risorse è rappresentata dai modelli JSON per [Gestione risorse di Azure](../azure-resource-manager/powershell-azure-resource-manager.md). In breve, la procedura consiste in: scaricare le definizioni JSON delle risorse esistenti; impostare i parametri per determinati valori, come ad esempio nomi; ed eseguire il modello ogni volta che si desidera creare una nuova risorsa. È possibile raggruppare diverse risorse per crearle tutte in un’unica volta - ad esempio, un monitoraggio app con test di disponibilità, avvisi e risorsa di archiviazione per l'esportazione continua. Esistono alcune sottigliezze di alcuni parametri, che verranno illustrate di seguito.

## <a name="one-time-setup"></a>Installazione singola
Se non si è utilizzato prima PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure Powershell nel computer in cui si desidera eseguire gli script:

1. Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Usarla per installare Microsoft Azure Powershell.

## <a name="copy-the-json-for-existing-resources"></a>Copiare i JSON per le risorse esistenti
1. Impostare [Application Insights](app-insights-overview.md) per progetti simili a quelli che si desidera generare automaticamente. Se si desidera, aggiungere i test web e gli avvisi.
2. Creare un nuovo file con estensione .json - definirlo `template1.json` in questo esempio. Copiare questo contenuto al suo interno:

    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
            "testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }

            // Any other resources go here
          ]
        }

    ```

    Questo modello configura un test di disponibilità oltre alla risorsa principale.


1. Aprire [Gestione risorse di Azure](https://resources.azure.com/). Scorrere verso il basso `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` fino alla risorsa dell'applicazione. 
   
    ![Navigare in Esplora risorse di Azure](./media/app-insights-powershell/01.png)
   
    *Componenti* sono le risorse base di Application Insights per la visualizzazione di applicazioni. Sono disponibili risorse separate per le regole di avviso associate e i test web di disponibilità.
2. Copiare i JSON del componente nella posizione appropriata in `template1.json`.
3. Eliminare queste proprietà:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Aprire le sezioni webtests e alertrules e copiare i JSON per i singoli elementi nel modello. (Non copiare dai nodi webtests e alertrules: passare agli elementi sotto ad essi.)
   
    Ciascun test web dispone di una regola di avviso associata, perciò è necessario copiarli entrambi.
   
    Ogni test Web dispone di una regola di avviso corrispondente. Il test Web ha la precedenza.
   
    È possibile includere anche avvisi nelle metriche. [Nomi delle metriche](app-insights-powershell-alerts.md#metric-names).
5. Inserire questa riga in ciascuna risorsa:
   
    `"apiVersion": "2015-05-01",`

## <a name="parameterize-the-template"></a>Impostazione dei parametri per il modello
È necessario sostituire i nomi specifici con i parametri. Per [impostare i parametri di un modello](../azure-resource-manager/resource-group-authoring-templates.md), si scrivono espressioni mediante un [set di funzioni di supporto](../azure-resource-manager/resource-group-template-functions.md). 

È Impossibile impostare i parametri per una sola parte di una stringa, quindi utilizzare `concat()` per compilare stringhe.

Di seguito sono riportati esempi delle sostituzioni che si possono apportare. Sono presenti più occorrenze di ogni sostituzione. Potrebbero esserne necessarie altre nel modello. Questi esempi utilizzano i parametri e le variabili definite nella parte superiore del modello.

| find | sostituire con |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minuscolo) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Eliminare GUID e ID. |

## <a name="set-dependencies-between-the-resources"></a>Impostazione di dipendenze tra le risorse
Azure deve configurare le risorse in ordine fisso. Per assicurarsi che un programma di installazione venga completato prima che inizi il successivo, aggiungere le righe delle dipendenze:

* Nella risorsa del test di disponibilità:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Nella risorsa di avviso:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

Si noti che un test di disponibilità è in realtà composto da due parti: il test stesso e una regola di avviso che viene attivata a seconda dei risultati dei test.

## <a name="create-application-insights-resources"></a>Creare risorse di Application Insights
1. In PowerShell, accedere ad Azure
   
    `Login-AzureRmAccount`
2. Eseguire un comando simile al seguente:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -url http://myapp.com `
               -text "Welcome!"
   
    ``` 
   
   * -ResourceGroupName è il gruppo in cui si desidera creare le nuove risorse.
   * -templateFile deve precedere i parametri personalizzati.
   * -appName è il nome della risorsa da creare.
   * -webTestName è il nome del test web da creare.
   * -Url è l'url dell'app web.
   * -text è una stringa che viene visualizzata nella pagina web.

## <a name="to-get-the-instrumentation-key"></a>Per impostare la chiave di strumentazione
Dopo la creazione di una risorsa dell'applicazione, è necessaria la chiave di strumentazione: 

```PS

    $resource = Get-AzureRmResource -ResourceId "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<YOUR RESOURCE GROUP>/providers/Microsoft.Insights/components/<YOUR APP NAME>"

    $resource.Properties.InstrumentationKey
```


## <a name="an-example"></a>un esempio
Questo è il modello completo che è stato creato. Ha il componente dell'applicazione, il test di disponibilità, l'avviso del test di disponibilità e un avviso sulla metrica del tempo di risposta.

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type": "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]",
    "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //
      // App resource
      //
      "name": "[parameters('appName')]",
      "type": "Microsoft.Insights/components",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "East US", // Set to preferred location 
      "properties": {
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { "applicationType": "web" }
    },
    {
      //
      // Availability test
      //
      "name": "[variables('testName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "East US", // Set to preferred location
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[parameters('webTestName')]",
        "Description": "n",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', parameters('webTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('text'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //
      // Alert rule for the availability test
      //
      "name": "[variables('alertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }

    },
    {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "East US", // Must be East US at present
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": 3, //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
  ]
}


```


## <a name="next-steps"></a>Passaggi successivi
Altri articoli di automazione:

* [Creare una risorsa di Application Insights](app-insights-powershell-script-create-resource.md) : metodo rapido che esclude l'uso di un modello.
* [Configurare avvisi](app-insights-powershell-alerts.md)
* [Creare test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Deploy to Azure from Github](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx) (Distribuire ad Azure da Github)
* [Creare annotazioni di rilascio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)




<!--HONumber=Nov16_HO4-->



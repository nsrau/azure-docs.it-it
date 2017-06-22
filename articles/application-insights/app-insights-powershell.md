---
title: Automatizzare Azure Application Insights con PowerShell | Microsoft Docs
description: "Automatizzare la creazione di risorse, avvisi e test di disponibilità in PowerShell usando un modello di Azure Resource Manager."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e1ceaf7baef021f97f70a6b1a5203e178db613db
ms.contentlocale: it-it
ms.lasthandoff: 04/03/2017


---
#  <a name="create-application-insights-resources-using-powershell"></a>Creazione di risorse Application Insights con PowerShell
L'articolo descrive come automatizzare la creazione e l'aggiornamento di risorse di [Application Insights](app-insights-overview.md) usando Azure Resource Manager. Questo procedimento potrebbe, ad esempio, essere utilizzato come parte di un processo di compilazione. Insieme alla risorsa di base di Application Insights, è possibile creare [test Web di disponibilità](app-insights-monitor-web-app-availability.md), configurare [avvisi](app-insights-alerts.md), impostare lo [schema dei prezzi](app-insights-pricing.md) e creare altre risorse di Azure.

La chiave per la creazione di queste risorse è rappresentata dai modelli JSON per [Gestione risorse di Azure](../azure-resource-manager/powershell-azure-resource-manager.md). In breve, la procedura consiste in: scaricare le definizioni JSON delle risorse esistenti; impostare i parametri per determinati valori, come ad esempio nomi; ed eseguire il modello ogni volta che si desidera creare una nuova risorsa. È possibile raggruppare diverse risorse per crearle tutte in un’unica volta - ad esempio, un monitoraggio app con test di disponibilità, avvisi e risorsa di archiviazione per l'esportazione continua. Esistono alcune sottigliezze di alcuni parametri, che verranno illustrate di seguito.

## <a name="one-time-setup"></a>Installazione singola
Se non si è utilizzato prima PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure Powershell nel computer in cui si desidera eseguire gli script:

1. Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Usarla per installare Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Creare un modello di Azure Resource Manager
Creare un nuovo file con estensione .json - definirlo `template1.json` in questo esempio. Copiare questo contenuto al suo interno:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "HockeyAppBridge",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Creare risorse di Application Insights
1. In PowerShell accedere ad Azure:
   
    `Login-AzureRmAccount`
2. Eseguire un comando simile al seguente:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` è il gruppo in cui si vogliono creare le nuove risorse.
   * `-TemplateFile` deve precedere i parametri personalizzati.
   * `-appName` è il nome della risorsa da creare.

È possibile aggiungere altri parametri le cui descrizioni sono disponibili nella sezione del modello dedicata ai parametri.

## <a name="to-get-the-instrumentation-key"></a>Per impostare la chiave di strumentazione
Dopo la creazione di una risorsa applicazione, è necessaria la chiave di strumentazione: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Impostare il piano tariffario

È possibile impostare il [piano tariffario](app-insights-pricing.md).

Per creare una risorsa app con il piano tariffario Enterprise, usando il modello precedente:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|piano|
|---|---|
|1|Basic|
|2|Enterprise|

* Se si intende usare solo il piano tariffario Basic predefinito, è possibile omettere la risorsa CurrentBillingFeatures dal modello.
* Se si vuole modificare il piano tariffario dopo la creazione della risorsa dei componenti, è possibile usare un modello non contenente la risorsa "microsoft.insights/components". Omettere anche il nodo `dependsOn` nella risorsa della fatturazione. 

Per verificare il piano tariffario aggiornato, esaminare il pannello "Funzionalità + prezzi" nel browser. **Aggiornare la visualizzazione nel browser** per assicurarsi che venga visualizzato lo stato più recente.



## <a name="add-a-metric-alert"></a>Aggiungere un avviso per la metrica

Per configurare un avviso per la metrica contemporaneamente alla risorsa app, unire il codice seguente nel file modello:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
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
          "threshold": "[parameters('responseTime')]", //seconds
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
}
```

Quando si richiama il modello, se si vuole, è possibile aggiungere questo parametro:

    `-responseTime 2`

È possibile impostare parametri anche per altri campi. 

Per trovare i nomi dei tipi e i dettagli di configurazione di altre regole di avviso, creare una regola manualmente e quindi esaminarla in [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Aggiungere un test di disponibilità

Questo esempio è relativo a un test di ping (per testare una singola pagina).  

**Sono due le parti** di un test di disponibilità: il test stesso e l'avviso che notifica gli errori.

Unire il codice seguente nel file modello che crea l'app.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
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
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
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
    }
}
```

Per trovare i codici per altre posizioni di test o per automatizzare la creazione di test Web più complessi, creare un esempio manualmente e quindi impostare i parametri per il codice da [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Aggiungere altre risorse

Per automatizzare la creazione di altre risorse di qualsiasi tipo, creare un esempio manualmente e quindi copiare il codice e impostarne i parametri da [Azure Resource Manager](https://resources.azure.com/). 

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
   
    È possibile includere anche avvisi nelle metriche. [Nomi delle metriche](app-insights-powershell-alerts.md#metric-names).
5. Inserire questa riga in ciascuna risorsa:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Impostazione dei parametri per il modello
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

### <a name="set-dependencies-between-the-resources"></a>Impostazione di dipendenze tra le risorse
Azure deve configurare le risorse in ordine fisso. Per assicurarsi che un programma di installazione venga completato prima che inizi il successivo, aggiungere le righe delle dipendenze:

* Nella risorsa del test di disponibilità:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Nella risorsa avviso per un test di disponibilità:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Passaggi successivi
Altri articoli di automazione:

* [Creare una risorsa di Application Insights](app-insights-powershell-script-create-resource.md) : metodo rapido che esclude l'uso di un modello.
* [Configurare avvisi](app-insights-powershell-alerts.md)
* [Creare test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Deploy to Azure from GitHub](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx) (Distribuire in Azure da GitHub)
* [Creare annotazioni di rilascio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)



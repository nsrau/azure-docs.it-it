---
title: Creazione di risorse Application Insights con PowerShell
description: Creare risorse Application Insights a livello di programmazione come parte della compilazione.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/02/2016
ms.author: awills

---
# Creazione di risorse Application Insights con PowerShell
In questo articolo viene illustrato come creare automaticamente una risorsa [Application Insights](app-insights-overview.md) in Azure. Questo procedimento potrebbe, ad esempio, essere utilizzato come parte di un processo di compilazione. Insieme alla risorsa di base Application Insights, è possibile creare [test web di disponibilità](app-insights-monitor-web-app-availability.md), [impostare avvisi](app-insights-alerts.md), e creare altre risorse di Azure.

La chiave per la creazione di queste risorse è rappresentata dai modelli JSON per [Gestione risorse di Azure](../powershell-azure-resource-manager.md). In breve, la procedura consiste in: scaricare le definizioni JSON delle risorse esistenti; impostare i parametri per determinati valori, come ad esempio nomi; ed eseguire il modello ogni volta che si desidera creare una nuova risorsa. È possibile raggruppare diverse risorse per crearle tutte in un’unica volta - ad esempio, un monitoraggio app con test di disponibilità, avvisi e risorsa di archiviazione per l'esportazione continua. Esistono alcune sottigliezze di alcuni parametri, che verranno illustrate di seguito.

## Installazione singola
Se non si è utilizzato prima PowerShell con la sottoscrizione di Azure:

Installare il modulo Azure Powershell nel computer in cui si desidera eseguire gli script:

1. Installare [Installazione guidata piattaforma Web Microsoft (v5 o versione successiva)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Usarla per installare Microsoft Azure Powershell.

## Copiare i JSON per le risorse esistenti
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


1. Aprire [Gestione risorse di Azure](https://resources.azure.com/). Scorrere verso il basso sottoscrizioni, resourceGroups, componenti, fino alla risorsa dell’app.
   
    ![](./media/app-insights-powershell/01.png)
   
    *Componenti* sono le risorse base di Application Insights per la visualizzazione di applicazioni. Sono disponibili risorse separate per le regole di avviso associate e i test web di disponibilità.
2. Copiare i JSON del componente nella posizione appropriata in `template1.json`.
3. Eliminare queste proprietà:
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
4. Aprire le sezioni webtests e alertrules e copiare i JSON per i singoli elementi nel modello. (Non copiare dai nodi webtests e alertrules: passare agli elementi sotto ad essi.)
   
    Ciascun test web dispone di una regola di avviso associata, perciò è necessario copiarli entrambi.
   
    Il test web deve andare prima della regola di avviso.
5. Per soddisfare lo schema, inserire questa riga in ciascuna risorsa:
   
    `"apiVersion": "2014-04-01",`
   
    (Lo schema indica inoltre l'utilizzo delle lettere maiuscole sui nomi di tipo risorsa `Microsoft.Insights/*` - ma *non* le modifica.)

## Impostazione dei parametri per il modello
È necessario sostituire i nomi specifici con i parametri. Per [impostare i parametri di un modello](../resource-group-authoring-templates.md), si scrivono espressioni mediante un [set di funzioni di supporto](../resource-group-template-functions.md).

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
| `"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"` |`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" ` |

## Se l'app è un'app Web di Azure
Aggiungere la risorsa. Se è già presente una risorsa `siteextensions`, impostarne i parametri come indicato di seguito:

```json
    {
      "apiVersion": "2014-06-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

Questa risorsa distribuisce Application Insights SDK nell'app Web di Azure.

## Impostazione di dipendenze tra le risorse
Azure deve configurare le risorse in ordine fisso. Per assicurarsi che un programma di installazione venga completato prima che inizi il successivo, aggiungere le righe delle dipendenze:

* Nella risorsa di test web:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Nella risorsa di avviso:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Creare risorse di Application Insights
1. In PowerShell, accedere ad Azure
   
    `Login-AzureRmAccount`
2. Eseguire un comando simile al seguente:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"
   
    ``` 
   
   * -ResourceGroupName è il gruppo in cui si desidera creare le nuove risorse.
   * -templateFile deve precedere i parametri personalizzati.
   * -appName è il nome della risorsa da creare.
   * -webTestName è il nome del test web da creare.
   * -Url è l'url dell'app web.
   * -text è una stringa che viene visualizzata nella pagina web.
   * -siteName viene usato se si tratta di un sito Web di Azure.

## Definire avvisi delle metriche
Esiste un [metodo PowerShell per l’impostazione degli avvisi](app-insights-alerts.md#set-alerts-by-using-powershell).

## un esempio
Quello che segue è il componente completo con test web e modello di avviso del test web creato:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
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
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## Vedere anche
Altri articoli di automazione:

* [Creare una risorsa di Application Insights](app-insights-powershell-script-create-resource.md): metodo rapido che esclude l'uso di un modello.
* [Configurare avvisi](app-insights-powershell-alerts.md)
* [Creare test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Inviare i dati del servizio Diagnostica di Azure ad Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Creare annotazioni di rilascio](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

<!---HONumber=AcomDC_0727_2016-->
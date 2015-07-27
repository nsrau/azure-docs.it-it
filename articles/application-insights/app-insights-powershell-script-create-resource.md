<properties 
	pageTitle="Script di PowerShell per creare una risorsa di Application Insights" 
	description="Consente di automatizzare la creazione di risorse di Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/12/2015" 
	ms.author="awills"/>

#  Script di PowerShell per creare una risorsa di Application Insights

*Application Insights è disponibile in anteprima.*

Se si vuole monitorare una nuova applicazione oppure una nuova versione di un'applicazione con [Application Insights per Visual Studio](https://azure.microsoft.com/services/application-insights/), è possibile configurare una nuova risorsa in Microsoft Azure. Questa risorsa verrà usata per analizzare e visualizzare i dati di telemetria provenienti dall'app.

Per automatizzare la creazione di una nuova risorsa con PowerShell.

Se, ad esempio, si intende sviluppare un'app per dispositivi mobili, è probabile che, in un dato momento, i clienti usino diverse versioni pubblicate dell'app. Se però si vuole evitare di combinare i risultati di telemetria delle diverse versioni, è possibile fare in modo che il processo di compilazione crei una nuova risorsa per ogni compilazione.

## Script per creare una risorsa di Application Insights

*Output*

* App Insights Name = erimattestapp
* IKey = 00000000-0000-0000-0000-000000000000

*Script di PowerShell*

```PowerShell

cls


##################################################################
# Set Values
##################################################################

#If running manually, comment this out before the first execution to login to the Azure Portal
#Add-AzureAccount

#Set the name of the Application Insights Resource
$appInsightsName = "TestApp"

#Set the application name used for the value of the Tag "AppInsightsApp" - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

#Set the name of the Resource Group to use.  By default will use the application name as a starter
$resourceGroupName = "MyAppResourceGroup"

##################################################################
# Create the Resource and Output the name and iKey
##################################################################
#Set the script to Resource Manager - http://azure.microsoft.com/documentation/articles/powershell-azure-resource-manager/
Switch-AzureMode AzureResourceManager

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

#Create the App Insights Resource
$resource = New-AzureResource -Name $appInsightsName -ResourceGroupName $resourceGroupName -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} -ResourceType "Microsoft.Insights/Components" -Location "Central US" -ApiVersion "2014-08-01"

#Give team owner access - http://azure.microsoft.com/documentation/articles/role-based-access-control-powershell/
New-AzureRoleAssignment -Mail "myTeam@fabrikam.com" -RoleDefinitionName Owner -Scope $resource.ResourceId | Out-Null

#Display iKey
Write-Host "App Insights Name = " $resource.Properties["Name"]
Write-Host "IKey = " $resource.Properties["InstrumentationKey"]

```

## Cosa fare con l'iKey

Per identificare le singole risorse, viene usata una chiave di strumentazione (iKey), ovvero l'output dello script di creazione della risorsa. Lo script di compilazione deve fornire l'iKey all'istanza di Application Insights SDK incorporata nell'app.

È possibile fornire l'iKey all'SDK in due modi diversi:
  
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Oppure nel [codice di inizializzazione](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`





 

<!---HONumber=July15_HO3-->
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
	ms.date="02/19/2016" 
	ms.author="awills"/>

#  Script di PowerShell per creare una risorsa di Application Insights

*Application Insights è disponibile in anteprima.*

Se si vuole monitorare una nuova applicazione oppure una nuova versione di un'applicazione con [Application Insights per Visual Studio](https://azure.microsoft.com/services/application-insights/), è possibile configurare una nuova risorsa in Microsoft Azure. Questa risorsa verrà usata per analizzare e visualizzare i dati di telemetria provenienti dall'app.

Per automatizzare la creazione di una nuova risorsa con PowerShell.

Se, ad esempio, si intende sviluppare un'app per dispositivi mobili, è probabile che, in un dato momento, i clienti usino diverse versioni pubblicate dell'app. Se però si vuole evitare di combinare i risultati di telemetria delle diverse versioni, è possibile fare in modo che il processo di compilazione crei una nuova risorsa per ogni compilazione.

## Script per creare una risorsa di Application Insights

Vedere le specifiche dei cmdlet:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)


*Script di PowerShell*

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Add-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 
# - http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-tags/
$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

#Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource

$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ Name = "AppInsightsApp"; Value = $applicationTagName} `
  -ResourceType "Microsoft.Insights/Components" `
  -Location "Central US" `
  -PropertyObject @{"Type"="ASP.NET"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


#Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## Cosa fare con l'iKey

Per identificare le singole risorse, viene usata una chiave di strumentazione (iKey), ovvero l'output dello script di creazione della risorsa. Lo script di compilazione deve fornire l'iKey all'istanza di Application Insights SDK incorporata nell'app.

È possibile fornire l'iKey all'SDK in due modi diversi:
  
* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
 * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Oppure nel [codice di inizializzazione](app-insights-api-custom-events-metrics.md): 
 * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`



## Vedere anche

* [Creare risorse Application Insights e test web da modelli](app-insights-powershell.md)
* [Impostare il monitoraggio di diagnostica Azure con PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Impostare avvisi tramite PowerShell](app-insights-powershell-alerts.md)

 

<!---HONumber=AcomDC_0224_2016-->
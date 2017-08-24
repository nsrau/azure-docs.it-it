---
title: Script di PowerShell per creare una risorsa di Application Insights | Documentazione Microsoft
description: Consente di automatizzare la creazione di risorse di Application Insights.
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: d84ab993b1d9489ca9d2edaa1cb9672d9bced899
ms.openlocfilehash: 4a7a4b719176a1d10bee2fc4f6b65204cc77bee8
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script di PowerShell per creare una risorsa di Application Insights


Se si vuole monitorare una nuova applicazione oppure una nuova versione di un'applicazione con [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), è possibile configurare una nuova risorsa in Microsoft Azure. Questa risorsa verrà usata per analizzare e visualizzare i dati di telemetria provenienti dall'app. 

Per automatizzare la creazione di una nuova risorsa con PowerShell.

Se, ad esempio, si intende sviluppare un'app per dispositivi mobili, è probabile che, in un dato momento, i clienti usino diverse versioni pubblicate dell'app. Se però si vuole evitare di combinare i risultati di telemetria delle diverse versioni, è possibile fare in modo che il processo di compilazione crei una nuova risorsa per ogni compilazione.

> [!NOTE]
> Se si desidera creare un set di risorse simultaneamente, è consigliabile [creare le risorse tramite un modello di Azure](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script per creare una risorsa di Application Insights
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

# Add-AzureRmAccount / Login-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Cosa fare con l'iKey
Per identificare le singole risorse, viene usata una chiave di strumentazione (iKey), ovvero l'output dello script di creazione della risorsa. Lo script di compilazione deve fornire l'iKey all'istanza di Application Insights SDK incorporata nell'app.

È possibile fornire l'iKey all'SDK in due modi diversi:

* In [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Oppure nel [codice di inizializzazione](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Vedere anche
* [Creare risorse Application Insights e test web da modelli](app-insights-powershell.md)
* [Impostare il monitoraggio di diagnostica Azure con PowerShell](app-insights-powershell-azure-diagnostics.md) 
* [Impostare avvisi tramite PowerShell](app-insights-powershell-alerts.md)



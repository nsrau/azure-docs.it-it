---
title: "Hosting ad alta densità nel servizio app di Azure | Microsoft Docs"
description: "Hosting ad alta densità nel servizio app di Azure"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 22/01/2018
ms.author: byvinyal
ms.openlocfilehash: 2ffffd3cc9f5c59f74f71d6d7d31c5ea615d11f4
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="high-density-hosting-on-azure-app-service"></a>Hosting ad alta densità nel servizio app di Azure
Quando si usa il servizio app, l'applicazione viene distinta dalla capacità ad essa allocata in base a due concetti:

* **Applicazione:** rappresenta l'app e la relativa configurazione di runtime. Include ad esempio la versione di .NET che dovrà essere caricata dal runtime e le impostazioni dell'app.
* **Piano di servizio app:** definisce le caratteristiche in termini di capacità, set di funzionalità disponibile e località dell'applicazione. Le caratteristiche possono ad esempio corrispondere a un computer di grandi dimensioni (quattro core), quattro istanze e funzionalità Premium negli Stati Uniti orientali.

Un'app è sempre collegata a un piano di servizio app, ma un piano di servizio app può fornire capacità a una o più app.

La piattaforma garantisce quindi la possibilità di isolare una singola app o consentire a più app di condividere le risorse condividendo un piano di servizio app.

Se più app condividono un piano di servizio app, tuttavia, un'istanza dell'app viene eseguita in ogni istanza del piano di servizio app.

## <a name="per-app-scaling"></a>Scalabilità per app
*Scalabilità per app* è una funzionalità che può essere abilitata a livello di piano di servizio app ed essere quindi usata per ogni applicazione.

La scalabilità per app consente di ridimensionare un'app indipendentemente dal piano di servizio app in cui è ospitata. È così possibile configurare un piano di servizio app per offrire 10 istanze e impostare un'app in modo che usi solo cinque istanze.

   >[!NOTE]
   >La scalabilità per app è disponibile solo per i piani di servizio app per SKU **Standard**, **Premium**, **Premium V2** e **Isolated**
   >

### <a name="per-app-scaling-using-powershell"></a>Scalabilità per app tramite PowerShell

Creare un piano configurato come piano di *scalabilità per app* passando l'attributo ```-perSiteScaling $true``` al cmdlet ```New-AzureRmAppServicePlan```

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Se si desidera aggiornare un piano di servizio app esistente per usare questa funzionalità: 

- ottenere il piano di destinazione ```Get-AzureRmAppServicePlan```
- modificare la proprietà localmente ```$newASP.PerSiteScaling = $true```
- pubblicare le modifiche su Azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

A livello di app, configurare il numero di istanze che l'app può usare nel piano di servizio app.

Nell'esempio seguente l'app è limitata a due istanze indipendentemente dall'aggiunta del numero di istanze al piano di servizio app sottostante.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp.SiteConfig.NumberOfWorkers è diverso da $newapp.MaxNumberOfWorkers. La scalabilità per app usa $newapp.SiteConfig.NumberOfWorkers per determinare le caratteristiche di scalabilità dell'app.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Scalabilità per app tramite Azure Resource Manager

Il *modello di Azure Resource Manager* seguente crea:

- un piano di servizio app a cui sono aggiunte 10 istanze
- un'app che è configurata per l'aggiunta di un massimo di cinque istanze.

Il piano di servizio app imposta la proprietà **PerSiteScaling** su true ```"perSiteScaling": true```. L'app imposta il **numero di ruoli di lavoro** da usare su 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configurazione consigliata per l'hosting ad alta densità
La scalabilità per app è una funzionalità abilitata sia nelle aree di Azure globali che negli ambienti del servizio app. È tuttavia consigliabile usare gli ambienti del servizio app per sfruttarne le funzionalità avanzate e i pool di capacità di maggiori dimensioni.  

Per configurare l'hosting ad alta densità per le app, seguire questa procedura:

1. Configurare l'ambiente del servizio app e scegliere un pool di lavoro da dedicare allo scenario di hosting ad alta densità.
1. Creare un singolo piano di servizio app e ridimensionarlo in modo da usare tutta la capacità disponibile del pool di lavoro.
1. Impostare il flag PerSiteScaling su true nel piano di servizio app.
1. Vengono create nuove app e assegnate al piano di servizio app con la proprietà **numberOfWorkers** impostata su **1**. L'uso di questa configurazione consente di ottenere la massima densità possibile nel pool di lavoro.
1. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni app, per concedere risorse aggiuntive in base alle esigenze. Ad esempio: 
    - Per un'app a utilizzo elevato è possibile impostare **numberOfWorkers** su **3** per avere una maggiore capacità di elaborazione per l'app. 
    - Per le app a basso utilizzo impostare **numberOfWorkers** su **1**.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica approfondita dei piani di servizio app di Azure](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Introduzione all'ambiente del servizio app](app-service-app-service-environment-intro.md)

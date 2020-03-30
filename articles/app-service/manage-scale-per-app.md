---
title: Scalabilità per app per l'hosting ad alta densità
description: Ridimensiona le app in modo indipendente dai piani del servizio app e ottimizza le istanze con scalabilità orizzontale nel piano.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672342"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting ad alta densità nel servizio app di Azure usando il ridimensionamento per appHigh-density hosting on Azure App Service using per-app scaling

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Quando si usa il servizio app, è possibile ridimensionare le app ridimensionando il piano di [servizio app](overview-hosting-plans.md) in cui vengono eseguite. Se vengono eseguite più app nello stesso piano di servizio app,ogni istanza scale-out esegue tutte le app nel piano.

Il *ridimensionamento per app* può essere abilitato a livello di piano di servizio app per consentire la scalabilità indipendente di un'app dal piano di servizio app che la ospita. È così possibile configurare un piano di servizio app per offrire 10 istanze e impostare un'app in modo che usi solo cinque istanze.

> [!NOTE]
> La scalabilità per app è disponibile solo per i piani tariffari **Standard**, **Premium**, **Premium V2** e **Isolated**.
>

Le app vengono allocate al piano di servizio app disponibile usando un approccio di lavoro ottimale per una distribuzione uniforme tra istanze. Anche se una distribuzione uniforme non è garantita, la piattaforma farà in modo che due istanze della stessa app non vengano ospitate nella stessa istanza del piano del servizio app.

La piattaforma non si basa sulle metriche per decidere sull'allocazione dei lavoratori. Le applicazioni vengono ribilanciate solo quando le istanze vengono aggiunte o rimosse dal piano di servizio app.

## <a name="per-app-scaling-using-powershell"></a>Scalabilità per app tramite PowerShell

Creare un piano con scalabilità per applicazione, passando il parametro ```-PerSiteScaling $true``` al cmdlet ```New-AzAppServicePlan```.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Abilitare la scalabilità con un piano di Servizio app di Azure, passando il parametro `-PerSiteScaling $true` al cmdlet ```Set-AzAppServicePlan```.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

A livello di app, configurare il numero di istanze che l'app può usare nel piano di servizio app.

Nell'esempio seguente l'app è limitata a due istanze indipendentemente dall'aggiunta del numero di istanze al piano di servizio app sottostante.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` è diverso da `$newapp.MaxNumberOfWorkers`. La scalabilità per app usa `$newapp.SiteConfig.NumberOfWorkers` per determinare le caratteristiche di scalabilità dell'app.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Scalabilità per app tramite Azure Resource Manager

Il modello di Azure Resource Manager seguente crea:

- un piano di servizio app a cui sono aggiunte 10 istanze
- un'app che è configurata per l'aggiunta di un massimo di cinque istanze.

Il piano di servizio app imposta la proprietà **PerSiteScaling** su true `"perSiteScaling": true`. L'app imposta il **numero di ruoli di lavoro** da usare su 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

La scalabilità per app è una funzionalità abilitata sia nelle aree di Azure globali che negli [ambienti del servizio app](environment/app-service-app-service-environment-intro.md). Tuttavia, la strategia consigliata consiste nell'usare gli ambienti del servizio app per sfruttare le funzionalità avanzate e la capacità del piano di servizio app più grande.  

Per configurare l'hosting ad alta densità per le app, seguire questa procedura:

1. Designare un piano di servizio app come piano ad alta densità e scalarlo orizzontalmente alla capacità desiderata.
1. Impostare il flag `PerSiteScaling` su true nel piano di servizio app.
1. Vengono create nuove app e assegnate al piano di servizio app con la proprietà **numberOfWorkers** impostata su **1**.
   - L'utilizzo di questa configurazione consente di ottenere la densità più elevata possibile.
1. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni app, per concedere risorse aggiuntive in base alle esigenze. Ad esempio:
   - Per un'app a utilizzo elevato è possibile impostare **numberOfWorkers** su **3** per avere una maggiore capacità di elaborazione per l'app.
   - Per le app a basso utilizzo impostare **numberOfWorkers** su **1**.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica approfondita dei piani di servizio app di Azure](overview-hosting-plans.md)
- [Introduzione all'ambiente del servizio app](environment/app-service-app-service-environment-intro.md)

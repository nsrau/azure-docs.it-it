---
title: "Hosting ad alta densità nel servizio app di Azure | Documentazione Microsoft"
description: "Hosting ad alta densità nel servizio app di Azure"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/11/2017
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 0c2677b388f7a88ff88715a05212633565393cc2
ms.openlocfilehash: 2d5d1d5123ca718b2e7dcdf426b77f91969dc9dc


---
# <a name="high-density-hosting-on-azure-app-service"></a>Hosting ad alta densità nel servizio app di Azure
Quando si usa il servizio app, l'applicazione viene distinta dalla capacità ad essa allocata in base a due concetti:

* **Applicazione:** rappresenta l'app e la relativa configurazione di runtime. Include ad esempio la versione di .NET che dovrà essere caricata dal runtime, le impostazioni dell'app e così via.
* **Piano di servizio app:** definisce le caratteristiche in termini di capacità, set di funzionalità disponibile e località dell'applicazione. Le caratteristiche possono ad esempio corrispondere a un computer di grandi dimensioni (quattro core), quattro istanze e funzionalità Premium negli Stati Uniti orientali.

Un'app è sempre collegata a un piano di servizio app, ma un piano di servizio app può fornire capacità a una o più app.

La piattaforma garantisce quindi la possibilità di isolare una singola app o consentire a più app di condividere le risorse condividendo un piano di servizio app.

Se più app condividono un piano di servizio app, tuttavia, un'istanza dell'app viene eseguita in ogni istanza del piano di servizio app.

## <a name="per-app-scaling"></a>Scalabilità per app
*Scalabilità per app* è una funzionalità che può essere abilitata a livello di piano di servizio app ed essere quindi usata per ogni applicazione.

La scalabilità per app consente di ridimensionare un'app indipendentemente dal piano di servizio app in cui è ospitata. È così possibile configurare un piano di servizio app per offrire 10 istanze impostando però un'app in modo che venga ridimensionata a solo 5 di tali istanze.

   >[!NOTE]
   >La scalabilità per app è disponibile solo per i piani di servizio app con SKU **Premium**
   >

### <a name="per-app-scaling-using-powershell"></a>Scalabilità per app tramite PowerShell

Si può configurare un nuovo piano come piano di *scalabilità per app* passando l'attributo ```-perSiteScaling $true``` al cmdlet ```New-AzureRmAppServicePlan```

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

Dopo aver configurato il piano, è possibile impostare il numero massimo di istanze per ognuna delle app.

Nell'esempio seguente l'app è limitata a due sole istanze indipendentemente dal numero di istanze a cui viene scalato orizzontalmente il piano di servizio app sottostante.

```
    # Get the app we want to configure to use "PerSiteScaling"
    $newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
    # Modify the NumberOfWorkers setting to the desired value.
    $newapp.SiteConfig.NumberOfWorkers = 2
    
    # Post updated app back to azure
    Set-AzureRmWebApp $newapp
```

### <a name="per-app-scaling-using-azure-resource-manager"></a>Scalabilità per app tramite Azure Resource Manager

Il seguente *modello di Azure Resource Manager* crea:

- un piano di servizio app che viene scalato orizzontalmente a 10 istanze
- un'app che è configurata per la scalabilità orizzontale fino a un massimo di cinque istanze.

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
La funzionalità di scalabilità per app è abilitata sia nelle aree di Azure pubbliche che negli ambienti del servizio app. È tuttavia consigliabile usare gli ambienti del servizio app per sfruttarne le funzionalità avanzate e i pool di capacità di maggiori dimensioni.  

Per configurare l'hosting ad alta densità per le app, seguire questa procedura:

1. Configurare l'ambiente del servizio app e scegliere un pool di lavoro da dedicare allo scenario di hosting ad alta densità.
2. Creare un singolo piano di servizio app e ridimensionarlo in modo da usare tutta la capacità disponibile del pool di lavoro.
3. Impostare il flag PerSiteScaling su true nel piano di servizio app.
4. Vengono create nuove app e assegnate al piano di servizio app con la proprietà **numberOfWorkers** impostata su **1**. L'uso di questa configurazione consente di ottenere la massima densità possibile nel pool di lavoro.
5. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni app, per concedere risorse aggiuntive in base alle esigenze. Ad esempio, per un'app a uso elevato è possibile impostare **numberOfWorkers** su **3** per avere maggiore capacità di elaborazione per l'app, mentre per le app a uso ridotto è possibile impostare **numberOfWorkers** su **1**.




<!--HONumber=Jan17_HO2-->



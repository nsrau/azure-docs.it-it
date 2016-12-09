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
ms.date: 10/24/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a4105feb1621891e1777078c67e080c44e7be51


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

Il modello di Azure Resource Manager seguente crea un piano di servizio app con aumento del numero di istanze a 10 e un'app configurata per usare la scalabilità per app con ridimensionamento a 5 istanze soltanto.

Il piano di servizio app imposta la proprietà di **scalabilità per ogni sito** su true ( `"perSiteScaling": true`). L'app imposta il **numero di ruoli di lavoro** da usare su 5 (`"properties": { "numberOfWorkers": "5" }`).

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
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
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


## <a name="recommended-configuration-for-high-density-hosting"></a>Configurazione consigliata per l'hosting ad alta densità
La funzionalità di scalabilità per app è abilitata sia nelle aree di Azure pubbliche che negli ambienti del servizio app. È tuttavia consigliabile usare gli ambienti del servizio app per sfruttarne le funzionalità avanzate e i pool di capacità di maggiori dimensioni.  

Per configurare l'hosting ad alta densità per le app, seguire questa procedura:

1. Configurare l'ambiente del servizio app e scegliere un pool di lavoro da dedicare allo scenario di hosting ad alta densità.
2. Creare un singolo piano di servizio app e ridimensionarlo in modo da usare tutta la capacità disponibile del pool di lavoro.
3. Impostare il flag della scalabilità per sito su true nel piano di servizio app.
4. Vengono creati nuovi siti, che vengono assegnati al piano di servizio app con la proprietà **numberOfWorkers** impostata su **1**. L'uso di questa configurazione consente di ottenere la massima densità possibile nel pool di lavoro.
5. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni sito, per concedere risorse aggiuntive in base alle esigenze. Ad esempio, per un sito a uso elevato è possibile impostare **numberOfWorkers** su **3** per avere maggiore capacità di elaborazione per l'app corrispondente, mentre per siti di uso inferiore è possibile impostare **numberOfWorkers** su **1**.




<!--HONumber=Nov16_HO3-->



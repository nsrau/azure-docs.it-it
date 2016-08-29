<properties
	pageTitle="Hosting ad alta densità nel servizio app di Azure | Microsoft Azure"
	description="Hosting ad alta densità nel servizio app di Azure"
	authors="btardif"
	manager="wpickett"
	editor=""
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/07/2016"
	ms.author="byvinyal"/>  

# Hosting ad alta densità nel servizio app di Azure#

Quando si usa il servizio app di Azure, l'applicazione è distinta dalla capacità allocata in base a due concetti.

- **Applicazione:** rappresenta l'app e la relativa configurazione di runtime. Include ad esempio la versione di .NET che dovrà essere caricata dal runtime, le impostazioni dell'app e così via.

- **Piano di servizio app:** definisce le caratteristiche in termini di capacità, set di funzionalità disponibile e località dell'applicazione. Le caratteristiche possono ad esempio corrispondere a un computer di grandi dimensioni (quattro core), quattro istanze e funzionalità Premium negli Stati Uniti orientali.

Un'app è sempre collegata a un piano di servizio app, ma un piano di servizio app può fornire capacità a una o più app.

In questo modo, la piattaforma garantisce la possibilità di isolare una singola app o consentire a più app di condividere le risorse condividendo un piano di servizio app.

Se più app condividono un piano di servizio app, tuttavia, un'istanza dell'app viene eseguita in ogni istanza del piano di servizio app.

## Scalabilità per app##
La *scalabilità per app* è una funzionalità che può essere abilitata a livello di piano di servizio app ed essere quindi usata per ogni applicazione.

La scalabilità per app consente di ridimensionare un'app indipendentemente dal piano di servizio app in cui è ospitata. È così possibile configurare un piano di servizio app per offrire 10 istanze impostando però un'app in modo che venga ridimensionata a solo 5 di tali istanze.

Il modello di Azure Resource Manager seguente creerà un piano di servizio app con aumento del numero di istanze a 10 e un'app configurata per usare la scalabilità per app con ridimensionamento a 5 istanze soltanto.

A tale scopo, il piano di servizio app imposta la proprietà della **scalabilità per sito** su true (`"perSiteScaling": true`) e l'app imposta il **numero di ruoli di lavoro** da usare su 1 (`"properties": { "numberOfWorkers": "1" }`).

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
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


## Configurazione consigliata per l'hosting ad alta densità

La funzionalità di scalabilità per app è abilitata sia nelle aree di Azure pubbliche che negli ambienti del servizio app. È tuttavia consigliabile usare gli ambienti del servizio app per sfruttarne le funzionalità avanzate e i pool di capacità di maggiori dimensioni.

Per configurare l'hosting ad alta densità per le app, seguire questa procedura:

1. Configurare l'ambiente del servizio app e scegliere un pool di lavoro da dedicare allo scenario di hosting ad alta densità.

1. Creare un singolo piano di servizio app e ridimensionarlo in modo da usare tutta la capacità disponibile del pool di lavoro.

1. Impostare il flag della scalabilità per sito su true nel piano di servizio app.

1. Verranno creati nuovi siti, che verranno assegnati al piano di servizio app con la proprietà **numberOfWorkers** impostata su **1**. In questo modo si otterrà la massima densità possibile nel pool di lavoro.

1. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni sito, per concedere risorse aggiuntive in base alle esigenze. Ad esempio, per un sito a uso elevato è possibile impostare **numberOfWorkers** su **3** per avere maggiore capacità di elaborazione per l'app corrispondente, mentre per siti di uso inferiore è possibile impostare **numberOfWorkers** su **1**.

<!---HONumber=AcomDC_0817_2016-->
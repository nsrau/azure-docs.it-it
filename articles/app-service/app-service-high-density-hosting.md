<properties 
	pageTitle="Hosting ad alta densità nel servizio app di Azure" 
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
	ms.date="05/17/2016" 
	ms.author="byvinyal"/>

#Hosting ad alta densità nel servizio app di Azure#

##Informazioni sul ridimensionamento delle app##

Quando si usa il servizio app, l'applicazione viene distinta dalla capacità ad essa allocata in base a due concetti:
 
>**Applicazione:** rappresenta l'applicazione e la relativa configurazione di runtime, ad esempio la versione di .NET che il runtime deve caricare, le impostazioni dell'app e così via.

>**Piano del servizio app:** definisce le caratteristiche della capacità, il set di funzionalità disponibili e la località dell'applicazione, ad esempio, computer di grandi dimensioni (a quattro memorie centrali), quattro istanze, funzionalità Premium negli Stati Uniti orientali

Un'app è sempre collegata a un **piano del servizio app** ma un **piano del servizio app** può fornire capacità a una o più app.

Ciò significa che la piattaforma è abbastanza flessibile da mantenere isolata un'app singola o da consentire a più app di condividere risorse tramite la condivisione di un unico **piano del servizio app**.

Se più app condividono un unico **piano del servizio app**, tuttavia, in ogni istanza di tale **piano del servizio app** sarà in esecuzione un'istanza dell'app.

##Ridimensionamento per app##
Il **ridimensionamento per app** è una funzionalità che è possibile abilitare a livello di **piano del servizio app** e quindi sfruttare per ogni applicazione.

Il **ridimensionamento per app** consente di ridimensionare un'app indipendentemente dal **piano del servizio app** usato per ospitarla. In questo modo, un **piano del servizio app** può essere configurato per fornire 10 istanze, ma un'app può essere impostata per il ridimensionamento solo a 5 di esse.

Il modello di Azure Resource Manager riportato di seguito crea un **piano del servizio app** con un numero di istanze aumentato fino a 10 e un'app configurata per l'uso del **ridimensionamento per app** e con ridimensionamento solo fino a cinque istanze.

A tale scopo, il piano del servizio app imposta la proprietà di ridimensionamento per sito scalabilità su true ( `"perSiteScaling": true`) e l'app imposta il numero di ruoli di lavoro da usare su 1 `"properties": { "numberOfWorkers": "1" }`

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


##Configurazione consigliata per l'hosting ad alta densità##

Il **ridimensionamento per app** è una funzionalità abilitata sia nelle aree di Azure pubbliche che negli ambienti del servizio app. La strategia consigliata, tuttavia, consiste nell'usare gli ambienti del servizio app per sfruttare le funzionalità avanzate e i pool di capacità di dimensioni maggiori.

Seguire i passaggi elencati di seguito come linee guida su come configurare l'**hosting ad alta densità** per le proprie applicazioni.

1. Configurare l'**ambiente del servizio app** e scegliere un **pool di ruoli di lavoro** da riservare allo scenario di *hosting ad alta densità*.

1. Creare un singolo **piano del servizio app** e ridimensionarlo in modo che usi tutta la capacità disponibile del **pool di ruoli di lavoro**.

1. Impostare il flag del ridimensionamento per sito su true per il **piano del servizio app**.

1. Verranno creati nuovi siti, che verranno assegnati al **piano del servizio app** con la proprietà *numberOfWorkers* impostata su *1*. Ciò consente di ottenere la più alta densità possibile per questo **pool di ruoli di lavoro**.

1. Il numero di ruoli di lavoro può essere configurato in modo indipendente per ogni sito, per concedere risorse aggiuntive in base alle esigenze. Ad esempio, per un sito a uso elevato è possibile impostare *numberOfWorkers* su *3* per avere maggiore capacità di elaborazione per l'app corrispondente, mentre per siti di uso inferiore è possibile impostare *numberOfWorkers* su *1*.

<!---HONumber=AcomDC_0518_2016-->
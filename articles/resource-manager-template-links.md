<properties
   pageTitle="Modello di Gestione risorse per il collegamento delle risorse | Microsoft Azure"
   description="Mostra lo schema di Gestione risorse per la distribuzione di collegamenti tra risorse correlate tramite un modello."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Collegamenti alle risorse - Schema del modello

Crea un collegamento tra due risorse. Il collegamento viene applicato a una risorsa nota come risorsa di origine. La seconda risorsa nel collegamento è nota come risorsa di destinazione.

## Formato dello schema

Per creare un collegamento, aggiungere lo schema seguente alla sezione delle risorse del modello.
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Valore |
| ---- | ---- |
| type | Enum<br />Obbligatorio<br />**{spazio dei nomi}/{tipo}/providers/links**<br /><br />Tipo di risorsa da creare. I valori {spazio dei nomi} e {tipo} fanno riferimento allo spazio dei nomi del provider e al tipo di risorsa della risorsa di origine. |
| apiVersion | Enum<br />Obbligatorio<br />**2015-01-01**<br /><br />Versione dell'API da usare per la creazione della risorsa. |  
| name | String<br />Obbligatorio<br />**{risorsa}/Microsoft.Resources/{nome collegamento}**<br /> fino a 64 caratteri e non può contenere i caratteri <, > %, &, ? né caratteri di controllo.<br /><br />Valore che specifica sia il nome della risorsa di origine che il nome del collegamento. | | dependsOn | Array<br />Facoltativo<br />Elenco con valori delimitati da virgole di nomi o di identificatori univoci di risorse.<br /><br />Raccolta di risorse da cui il collegamento dipende. Se le risorse che si stanno collegando vengono distribuite nello stesso modello, includerne i nomi in questo elemento per assicurarsi che vengano distribuite per prime. | | properties | Oggetto<br />Obbligatorio<br />[oggetto properties](#properties)<br /><br />Oggetto che identifica la risorsa a cui collegarsi e note sul collegamento. | 

<a id="properties" />
### oggetto delle proprietà

| Nome | Valore |
| ------- | ---- |
| targetId | String<br />Obbligatorio<br />**{ID risorsa}**<br /><br />Identificatore della risorsa di destinazione a cui collegarsi. | | notes | String<br />Facoltativo<br />fino a 512 caratteri<br /><br />Descrizione del blocco. |


## Come usare la risorsa di collegamento

Applicare un collegamento tra due risorse quando le risorse presentano una dipendenza che continua dopo la distribuzione. Ad esempio, un'applicazione può connettersi a un database in un gruppo di risorse diverso. È possibile definire tale dipendenza creando un collegamento dall'applicazione al database. I collegamenti consentono di descrivere la relazione tra due risorse. Successivamente, l'utente o un altro utente all'interno dell'organizzazione può eseguire query in una risorsa per richiedere i collegamenti e scoprire come funziona la risorsa con altre risorse.

Tutte le risorse collegate devono appartenere alla stessa sottoscrizione. Ogni risorsa può essere collegata a 50 altre risorse. Se una qualsiasi delle risorse collegate viene eliminata o spostata, il proprietario del collegamento deve eliminare il corrispondente collegamento.

Per lavorare con i collegamenti tramite REST, vedere le [risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Usare il comando seguente di Azure PowerShell per vedere tutti i collegamenti nella sottoscrizione. È possibile usare altri parametri per limitare i risultati.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## esempi

Nell'esempio seguente viene applicato un blocco di sola lettura a un'app web.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
        ],
        "outputs": {}
    }

## Modelli di Guida introduttiva

I modelli di Guida introduttiva seguenti consentono di distribuire risorse con un collegamento.

- [Avviso alla coda con un'app per la logica](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [Avviso a Slack con un'app per la logica](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [Eseguire il provisioning di un'app per le API con un gateway esistente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [Eseguire il provisioning di un'app per le API con un nuovo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [Creare un'app per la logica e un'app per le API usando un modello](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [App per la logica che invia un messaggio di testo quando viene generato un avviso](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## Passaggi successivi

- Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

<!----HONumber=AcomDC_0406_2016-->
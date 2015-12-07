<properties
   pageTitle="Modello di Gestione risorse per il collegamento delle risorse | Microsoft Azure"
   description="Illustra lo schema del modello di Gestione risorse per la creazione di collegamenti tra risorse correlate."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="tomfitz"/>

# Collegamenti alle risorse - schema del modello

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

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sì | **{spazio dei nomi}/{tipo}/provider/collegamenti** | Il tipo di risorsa da creare. I valori {spazio dei nomi} e {tipo} fanno riferimento allo spazio dei nomi del provider e al tipo di risorsa della risorsa di origine. |
| apiVersion | enum | Sì | **01/01/2015** | La versione dell'API da utilizzare per la creazione della risorsa. |  
| name | string | Sì | **{risorsa}/Microsoft.Resources/{nome collegamento}**<br /><br />fino a 64 caratteri<br />Non può contenere <, >, %, &, ? o eventuali caratteri di controllo. | Valore che specifica sia il nome della risorsa di origine sia un nome per il collegamento. |
| dependsOn | array | No | Un elenco delimitato da virgole di nomi di risorse o di identificatori univoci di risorse. | La raccolta di risorse da cui dipende questo collegamento. Se le risorse che vengono collegate sono distribuite nello stesso modello, includere i nomi delle risorse in questo elemento per assicurarsi che vengano distribuiti per primi. | 
| properties | object | Sì | (come illustrato di seguito) | Oggetto che identifica la risorsa a cui collegarsi e note relative al collegamento. |  

### oggetto delle proprietà

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ------- | ---- | ---------------- | -------- | ----------- |
| targetId | stringa | Sì | | Identificatore della risorsa di destinazione a cui collegarsi. |
| di HDInsight | string | No | 512 caratteri | Descrizione del blocco. |


## Come usare la risorsa di collegamento

Applicare un collegamento tra due risorse quando le risorse presentano una dipendenza che continua dopo la distribuzione. Ad esempio, un'applicazione può connettersi a un database in un gruppo di risorse diverso. È possibile definire tale dipendenza creando un collegamento dall'applicazione al database. I collegamenti consentono di descrivere la relazione tra due risorse. Successivamente, l'utente o un altro utente all'interno dell'organizzazione può eseguire query in una risorsa per richiedere i collegamenti e scoprire come funziona la risorsa con altre risorse.

Tutte le risorse collegate devono appartenere alla stessa sottoscrizione. Ogni risorsa può essere collegata a 50 altre risorse. Se una qualsiasi delle risorse collegate viene eliminata o spostata, il proprietario del collegamento deve eliminare il corrispondente collegamento.

Per lavorare con i collegamenti tramite REST, vedere le [risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Usare il comando seguente di Azure PowerShell per vedere tutti i collegamenti nella sottoscrizione. È possibile usare altri parametri per limitare i risultati.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -OutputObjectFormat New

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


## Passaggi successivi

- Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).

<!---HONumber=AcomDC_1125_2015-->
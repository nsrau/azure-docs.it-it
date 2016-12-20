---
title: Modello di Resource Manager per il collegamento delle risorse | Microsoft Docs
description: Mostra lo schema di Gestione risorse per la distribuzione di collegamenti tra risorse correlate tramite un modello.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 48a13b1a-3208-4f91-ba85-bda2a0e22605
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: dcac45042b2511ae2b82ecf8278e9873c6f8c9b9


---
# <a name="resource-links-template-schema"></a>Collegamenti alle risorse - Schema del modello
Crea un collegamento tra due risorse. Il collegamento viene applicato a una risorsa nota come risorsa di origine. La seconda risorsa nel collegamento è nota come risorsa di destinazione.

## <a name="schema-format"></a>Formato dello schema
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



## <a name="values"></a>Valori
Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Valore |
| --- | --- |
| type |Enum<br />Obbligatorio<br />**{spazio dei nomi}/{tipo}/providers/links**<br /><br />Tipo di risorsa da creare. I valori {spazio dei nomi} e {tipo} fanno riferimento allo spazio dei nomi del provider e al tipo di risorsa della risorsa di origine. |
| apiVersion |Enum<br />Obbligatorio<br />**2015-01-01**<br /><br />Versione dell'API da usare per creare la risorsa. |
| Nome |String<br />Obbligatorio<br />**{risorsa}/Microsoft.Resources/{nome collegamento}**<br /> massimo 64 caratteri, non può contenere <, > %, &, ? o qualsiasi altro carattere di controllo.<br /><br />Valore che specifica sia il nome della risorsa di origine sia un nome per il collegamento. |
| dependsOn |Array<br />Facoltativo<br />Elenco di valori separati da virgole di nomi di risorsa o di identificatori univoci di risorse.<br /><br />Raccolta di risorse da cui dipende questo collegamento. Se le risorse che vengono collegate sono distribuite nello stesso modello, includere i nomi delle risorse in questo elemento per assicurarsi che vengano distribuiti per primi. |
| properties |Oggetto<br />Obbligatorio<br />[oggetto delle proprietà](#properties)<br /><br />Oggetto che identifica la risorsa a cui collegarsi e note relative al collegamento. |

<a id="properties" />

### <a name="properties-object"></a>oggetto delle proprietà
| Nome | Valore |
| --- | --- |
| targetId |String<br />Obbligatorio<br />**{ID risorsa}**<br /><br />Identificatore della risorsa di destinazione a cui collegarsi. |
| di HDInsight |String<br />Facoltativo<br />massimo 512 caratteri<br /><br />Descrizione del blocco. |

## <a name="how-to-use-the-link-resource"></a>Come usare la risorsa di collegamento
Applicare un collegamento tra due risorse quando le risorse presentano una dipendenza che continua dopo la distribuzione. Ad esempio, un'applicazione può connettersi a un database in un gruppo di risorse diverso. È possibile definire tale dipendenza creando un collegamento dall'applicazione al database. I collegamenti consentono di descrivere la relazione tra due risorse. Successivamente, l'utente o un altro utente all'interno dell'organizzazione può eseguire query in una risorsa per richiedere i collegamenti e scoprire come funziona la risorsa con altre risorse.

Tutte le risorse collegate devono appartenere alla stessa sottoscrizione. Ogni risorsa può essere collegata a 50 altre risorse. Se una qualsiasi delle risorse collegate viene eliminata o spostata, il proprietario del collegamento deve eliminare il corrispondente collegamento.

Per lavorare con i collegamenti tramite REST, vedere le [risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

Usare il comando seguente di Azure PowerShell per vedere tutti i collegamenti nella sottoscrizione. È possibile usare altri parametri per limitare i risultati.

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## <a name="examples"></a>esempi
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

## <a name="quickstart-templates"></a>Modelli di Guida introduttiva
I modelli di Guida introduttiva seguenti consentono di distribuire risorse con un collegamento.

* [Avviso alla coda con un'app per la logica](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
* [Avviso a Slack con un'app per la logica](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
* [Eseguire il provisioning di un'app per le API con un gateway esistente](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
* [Eseguire il provisioning di un'app per le API con un nuovo gateway](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
* [Creare un'app per la logica e un'app per le API usando un modello](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
* [App per la logica che invia un messaggio di testo quando viene generato un avviso](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulla struttura del modello, vedere [Creazione di modelli di Gestione risorse di Azure](resource-group-authoring-templates.md).




<!--HONumber=Nov16_HO3-->



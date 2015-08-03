<properties 
	pageTitle="Collegamento di risorse in Gestione risorse di Azure" 
	description="Creare un collegamento tra risorse in gruppi di risorse diversi in Gestione risorse di Azure." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Collegamento di risorse in Gestione risorse di Azure

In un ambiente di post-distribuzione è possibile eseguire query su relazioni o collegamenti tra risorse. Le dipendenze forniscono informazioni alla distribuzione, ma tale ciclo di vita termina al momento della distribuzione. Una volta completata la distribuzione, non esistono relazioni identificate tra le risorse dipendenti.

Gestione risorse di Azure fornisce invece una nuova funzionalità denominata collegamento di risorse per stabilire relazioni tra le risorse ed eseguire query su di esse. È possibile determinare le risorse collegate a o da una risorsa.

L'ambito per un collegamento di risorse può essere una sottoscrizione, un gruppo di risorse o una risorsa specifica. Ciò significa che i collegamenti di risorse possono documentare le relazioni che interessano più gruppi di risorse. Quando si inizia a scomporre la soluzione in più modelli e più gruppi di risorse, la possibilità di usare un meccanismo per identificare i collegamenti delle risorse risulta particolarmente utile. Ad esempio, è uno scenario comune che un database con un proprio ciclo di vita si trovi in un gruppo di risorse, mentre un'applicazione con un ciclo di vita diverso si trovi in un gruppo di risorse diverso. L'applicazione si connette al database e di conseguenza sussiste un collegamento tra risorse in gruppi di risorse diversi.

Tutte le risorse collegate devono appartenere alla stessa sottoscrizione. Ogni risorsa può essere collegata a 50 altre risorse. Se una qualsiasi delle risorse collegate viene eliminata o spostata, il proprietario del collegamento deve eliminare il corrispondente collegamento.

## Collegamento nei modelli

Nell'esempio seguente viene illustrato un modello che crea una risorsa di tipo "Microsoft.AppService/apiapps" con un set di relazioni unidirezionali per un sito Web, un hub di notifica e database SQL.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Collegamento con l'API REST

Per definire un collegamento tra risorse distribuite, eseguire:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Sostituire {subscription-id} con l'identificatore della sottoscrizione. Sostituire {resource-group}, {provider-namespace}, {resource-type} e {resource-name} con i valori che identificano la prima risorsa nel collegamento. Sostituire {link-name} con il nome del collegamento da creare. Usare 2015-01-01 per {api-version}.

Nella richiesta includere un oggetto che definisce la seconda risorsa nel collegamento:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

L'elemento properties contiene l'identificatore della seconda risorsa.

Per altri esempi, tra cui come recuperare le informazioni sui collegamenti, vedere [Risorse collegate](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Passaggi successivi

- È inoltre possibile organizzare le risorse con i tag. Per altre informazioni sull'applicazione di tag alle risorse, vedere [Uso dei tag per organizzare le risorse](resource-group-using-tags.md).
- Per una descrizione su come creare modelli e definire le risorse da distribuire, vedere [Creazione di modelli](resource-group-authoring-templates.md).

<!---HONumber=July15_HO4-->
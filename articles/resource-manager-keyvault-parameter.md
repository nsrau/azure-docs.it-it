<properties
   pageTitle="Utilizzare la chiave privata nell’insieme di credenziali chiave con il modello di Gestione risorse | Microsoft Azure"
   description="Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione."
   services="azure-resource-manager,key-vault"
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
   ms.date="12/15/2015"
   ms.author="tomfitz"/>

# Passare valori protetti durante la distribuzione

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile archiviare tale valore come chiave privata in un [insieme di credenziali chiave di Azure](./key-vault/key-vault-whatis.md) e fare riferimento al valore in altri modelli di Gestione risorse. Includere solo un riferimento alla chiave privata nel modello, in modo che la chiave privata non sia mai esposta, e non è necessario immettere manualmente il valore per la chiave privata ogni volta che si distribuiscono le risorse. Specificare quali utenti o entità servizio possono accedere alla chiave privata.

> [AZURE.NOTE]Attualmente, solo l’interfaccia della riga di comando di Azure supporta la possibilità di fare riferimento a una chiave privata nell’insieme di credenziali chiave. Azure PowerShell aggiungerà presto questa possibilità.

## Distribuire un insieme di credenziali chiave e una chiave privata

Per creare l'insieme di credenziali chiave da utilizzare come riferimento da altri modelli di Gestione risorse, è necessario impostare la proprietà **enabledForTemplateDeployment** su **true** e occorre concedere l'accesso all'utente o entità servizio che eseguirà la distribuzione con riferimento alla chiave privata.

Per ulteriori informazioni sulla distribuzione di un insieme di credenziali chiave e di una chiave privata, vedere lo [Schema dell'insieme di credenziali chiave](resource-manager-template-keyvault.md) e lo [Schema chiave privata nell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md).

## Fare riferimento a una chiave privata

Si fa riferimento alla chiave privata all'interno di un file dei parametri che passa i valori al modello. Si fa riferimento alla chiave privata passando l'identificatore della risorsa dell'insieme di credenziali chiave e il nome della chiave privata.

    "parameters": {
        "adminPassword": {
            "reference": {
                "keyVault": {
                    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                }, 
                "secretName": "sqlAdminPassword" 
            } 
        }
    }

Un file di parametri completo potrebbe essere simile a:

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "value": ""
            },
            "sqlsvrAdminLoginPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                    },
                    "secretName": "adminPassword"
                }
            }
        }
    }

Il parametro che accetta la chiave privata deve essere di tipo **securestring**. Nell'esempio seguente sono illustrate le sezioni pertinenti di un modello che consente di distribuire un server SQL che richiede una password di amministratore.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## Passaggi successivi

- Per informazioni generali sugli insiemi di credenziali chiave, vedere [Introduzione all'insieme di credenziali chiave Azure](./key-vault/key-vault-get-started.md).
- Per ulteriori informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).
- Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_1217_2015-->
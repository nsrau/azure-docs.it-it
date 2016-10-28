<properties
   pageTitle="Chiave privata nell’insieme di credenziali chiave con il modello di Resource Manager | Microsoft Azure"
   description="Viene illustrato come passare una chiave privata da un insieme di credenziali chiave come parametro durante la distribuzione."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Passare valori protetti durante la distribuzione

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile archiviare tale valore come chiave privata in un [insieme di credenziali chiave di Azure](./key-vault/key-vault-whatis.md) e fare riferimento al valore in altri modelli di Gestione risorse. Includere solo un riferimento alla chiave privata nel modello, in modo che la chiave privata non sia mai esposta, e non è necessario immettere manualmente il valore per la chiave privata ogni volta che si distribuiscono le risorse. Specificare quali utenti o entità servizio possono accedere alla chiave privata.

## Distribuire un insieme di credenziali chiave e una chiave privata

Per creare l'insieme di credenziali chiave da utilizzare come riferimento da altri modelli di Gestione risorse, è necessario impostare la proprietà **enabledForTemplateDeployment** su **true** e occorre concedere l'accesso all'utente o entità servizio che eseguirà la distribuzione con riferimento alla chiave privata.

Per ulteriori informazioni sulla distribuzione di un insieme di credenziali chiave e di una chiave privata, vedere lo [Schema dell'insieme di credenziali chiave](resource-manager-template-keyvault.md) e lo [Schema chiave privata nell'insieme di credenziali chiave](resource-manager-template-keyvault-secret.md).

## Fare riferimento a un segreto con un ID statico

Si fa riferimento alla chiave privata all'interno di un file dei parametri che passa i valori al modello. Si fa riferimento alla chiave privata passando l'identificatore della risorsa dell'insieme di credenziali chiave e il nome della chiave privata. In questo esempio il segreto dell'insieme di credenziali delle chiavi deve esistere già e si deve usare un valore statico per l'ID risorsa.

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

## Fare riferimento a un segreto con un ID dinamico

La sezione precedente ha illustrato come passare un ID risorsa statico per il segreto dell'insieme di credenziali delle chiavi. In alcuni scenari, tuttavia, è necessario fare riferimento a un segreto dell'insieme di credenziali delle chiavi che varia a seconda della distribuzione corrente. In questo caso non è possibile impostare come hardcoded l'ID risorsa nel file dei parametri. Non è sfortunatamente possibile generare in modo dinamico l'ID risorsa nel file dei parametri, perché le espressioni del modello non sono consentite nel file dei parametri.

Per generare in modo dinamico l'ID risorsa per un segreto dell'insieme di credenziali delle chiavi, è necessario spostare la risorsa che necessita del segreto in un modello annidato. Nel modello principale aggiungere il modello annidato e passare un parametro che include l'ID risorsa generato in modo dinamico.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "vaultName": {
          "type": "string"
        },
        "secretName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "nestedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
              "contentVersion": "1.0.0.0"
            },
            "parameters": {
              "adminPassword": {
                "reference": {
                  "keyVault": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
                  },
                  "secretName": "[parameters('secretName')]"
                }
              }
            }
          }
        }
      ],
      "outputs": {}
    }


## Passaggi successivi

- Per informazioni generali sugli insiemi di credenziali chiave, vedere [Introduzione all'insieme di credenziali chiave Azure](./key-vault/key-vault-get-started.md).
- Per informazioni sull'uso di un insieme di credenziali delle chiavi con una macchina virtuale, vedere [Considerazioni sulla sicurezza per Azure Resource Manager](best-practices-resource-manager-security.md).
- Per esempi completi di segreti di riferimento alle chiavi private, vedere [Esempi di insiemi di credenziali chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0629_2016-->
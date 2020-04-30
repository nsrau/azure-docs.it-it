---
title: Usare Key Vault quando si distribuisce l'app gestita
description: Illustra come usare i segreti di accesso in Azure Key Vault durante la distribuzione delle applicazioni gestite
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458284"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Segreto di accesso di Key Vault quando si distribuiscono le Applicazioni gestite di Azure

Quando è necessario passare un valore protetto (ad esempio una password) come parametro durante la distribuzione, è possibile recuperare il valore da [Azure Key Vault](../../key-vault/general/overview.md). Per accedere a Key Vault durante la distribuzione delle applicazione gestite è necessario concedere l'accesso all'entità servizio **Provider di risorse di Appliance**. Il servizio Applicazioni gestite usa questa identità per eseguire le operazioni. Per recuperare correttamente un valore da un insieme di credenziali delle chiavi durante la distribuzione, l'entità servizio deve essere in grado di accedere all'insieme di credenziali delle chiavi.

Questo articolo descrive come configurare Key Vault per lavorare con le applicazioni gestite.

## <a name="enable-template-deployment"></a>Abilitare la distribuzione di modelli

1. Nel portale selezionare Key Vault.

1. Selezionare **criteri di accesso**.   

   ![Selezionare i criteri di accesso](./media/key-vault-access/select-access-policies.png)

1. Selezionare **Fare clic per visualizzare i criteri di accesso avanzati**.

   ![Mostrare i criteri di accesso avanzati](./media/key-vault-access/advanced.png)

1. Selezionare **Abilita l'accesso ad Azure Resource Manager per la distribuzione dei modelli**. Selezionare quindi **Salva**.

   ![Abilitare la distribuzione di modelli](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Aggiungere il servizio come collaboratore

1. Selezionare **controllo di accesso (IAM)**.

   ![Selezionare il controllo di accesso](./media/key-vault-access/access-control.png)

1. Selezionare **Aggiungi assegnazione ruolo**.

   ![Selezionare Aggiungi](./media/key-vault-access/add-access-control.png)

1. Selezionare **Collaboratore** per il ruolo. Cercare **Provider di risorse di Appliance** e selezionarlo dalle opzioni disponibili.

   ![Cercare il provider](./media/key-vault-access/search-provider.png)

1. Selezionare **Salva**.

## <a name="reference-key-vault-secret"></a>Fare riferimento al segreto di Key Vault

Per passare un segreto da un Key Vault a un modello nell'applicazione gestita, è necessario usare un [modello collegato o annidato](../templates/linked-templates.md) e fare riferimento al Key Vault nei parametri per il modello collegato o annidato. Fornire l'ID risorsa di Key Vault e il nome del segreto.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Key Vault è stato configurato per essere accessibile durante la distribuzione di un'applicazione gestita.

* Per informazioni su come passare un valore da un Key Vault come parametro del modello, vedere [Usare Azure Key Vault per passare valori di parametro protetti durante la distribuzione](../templates/key-vault-parameter.md).
* Per esempi di applicazioni gestite, vedere [Progetti di esempio per applicazioni gestite di Azure](sample-projects.md).
* Per informazioni sulla creazione di un file di definizione dell'interfaccia utente per un'applicazione gestita, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).

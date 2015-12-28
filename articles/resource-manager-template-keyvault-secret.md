<properties
   pageTitle="Modello di Gestione risorse per una chiave privata nell’insieme di credenziali chiave | Microsoft Azure"
   description="Mostra lo schema di gestione delle risorse per le chiavi private negli insiemi di credenziali chiave."
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

# Schema del modello di chiave privata nell’insieme di credenziali chiave

Crea una chiave privata archiviata in un insieme di credenziali chiave. Questo tipo di risorsa viene spesso distribuito come una risorsa figlio di [insieme di credenziali chiave](resource-manager-template-keyvault.md).

## Formato dello schema

Per creare una chiave privata nell’insieme di credenziali chiave, aggiungere lo schema seguente al modello. La chiave privata può essere definita come una risorsa figlio di un insieme di credenziali chiave o come risorsa di livello superiore. È possibile definirla come risorsa figlio quando l'insieme di credenziali chiave è distribuito nello stesso modello. È necessario definire la chiave privata come una risorsa di livello superiore quando l'insieme di credenziali chiave non è distribuito nello stesso modello o quando è necessario creare più chiavi private tramite ciclo sul tipo di risorsa.

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sì | Come risorsa figlio dell'insieme di credenziali chiave:<br />**secrets**<br /><br />Come risorsa di livello superiore:<br />**Microsoft.KeyVault/vaults/secrets** | Il tipo di risorsa da creare. |
| apiVersion | enum | Sì | **2015-06-01** <br /> **2014-12-19-preview** | La versione dell'API da utilizzare per la creazione della risorsa. | 
| name | string | Sì | | Nome della chiave privata da creare. Se si distribuisce la chiave privata come una risorsa figlio di un insieme di credenziali chiave, è sufficiente fornire un nome per la chiave privata. Se si distribuisce la chiave privata come una risorsa di livello superiore, i nomi devono essere nel formato **{key-vault-name}/{secret-name}**. |
| properties | object | Sì | (come illustrato di seguito) | Oggetto che specifica il valore della chiave privata da creare. |
| dependsOn | array | No | Un elenco delimitato da virgole di nomi di risorse o di identificatori univoci di risorse. | La raccolta di risorse da cui dipende questo collegamento. Se l'insieme di credenziali chiave per la chiave privata viene distribuito nello stesso modello, includere il nome dell'insieme di credenziali chiave in questo elemento per assicurarsi che venga distribuito prima. |

### oggetto delle proprietà

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| value | string | Sì | | Il valore della chiave privata da memorizzare nell'insieme di credenziali chiave. Quando si passa un valore di questa proprietà, utilizzare un parametro di tipo **securestring**. |

	
## esempi

Nel primo esempio viene distribuita una chiave privata come risorsa figlio di un insieme di credenziali chiave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "tags": { "displayName": "secret" },
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

Nel secondo esempio viene distribuita una chiave privata come risorsa di livello superiore archiviata in un insieme di credenziali chiave esistente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## Passaggi successivi

- Per informazioni generali sugli insiemi di credenziali chiave, vedere [Introduzione all'insieme di credenziali chiave Azure](./key-vault/key-vault-get-started.md).
- Per un esempio di riferimento a una chiave privata nell’insieme di credenziali chiave durante la distribuzione di modelli, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_1217_2015-->
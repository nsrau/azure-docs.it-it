<properties
   pageTitle="Modello di Gestione risorse per l’insieme di credenziali chiave | Microsoft Azure"
   description="Mostra lo schema di gestione delle risorse per gli insiemi di credenziali chiave."
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

# Schema del modello di insieme di credenziali chiave

Crea un insieme di credenziali chiave.

## Formato dello schema

Per creare un insieme di credenziali chiave, aggiungere lo schema seguente alla sezione delle risorse del modello.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## Valori

Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sì | **Microsoft.KeyVault/vaults** | Il tipo di risorsa da creare. |
| apiVersion | enum | Sì | **2015-06-01** <br /> **2014-12-19-preview** | La versione dell'API da utilizzare per la creazione della risorsa. | 
| name | string | Sì | | Il nome dell'insieme di credenziali chiave da creare. Il nome deve essere univoco in Azure. È consigliabile utilizzare la funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione, come illustrato nell'esempio riportato di seguito. |
| location | string | Sì | Per determinare le aree valide, vedere [aree supportate](resource-manager-supported-services.md#supported-regions). | L'area in cui ospitare l'insieme di credenziali chiave. |
| properties | object | Sì | ([come illustrato di seguito](#properties)) | Oggetto che specifica il tipo di insieme di credenziali chiave da creare. |
| resources | array | No | [Chiavi private nell’insieme di credenziali chiave](resource-manager-template-keyvault-secret.md) | Risorse figlio per l'insieme di credenziali chiave. |

<a id="properties" />
### oggetto delle proprietà

| Name | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | boolean | No | **true** o **false** | Specifica se l'insieme di credenziali è abilitato per la distribuzione delle macchine virtuali o di Service Fabric. |
| enabledForTemplateDeployment | boolean | No | **true** o **false** | Specifica se l'insieme di credenziali è abilitato per l'utilizzo in distribuzioni di modello di Gestione risorse. Per ulteriori informazioni, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | boolean | No | **true** o **false** | Specifica se l'insieme di credenziali è abilitato per la crittografia del volume. |
| TenantId | string | Sì | Identificatore univoco globale | L'identificatore del tenant per la sottoscrizione. È possibile recuperarlo con il cmdlet di PowerShell **Get-AzureRMSubscription**. |
| accessPolicies | array | Sì | ([come illustrato di seguito](#accesspolicies)) | Matrice di massimo 16 che specifica le autorizzazioni per l'utente o l’entità servizio. |
| sku | object | Sì | ([come illustrato di seguito](#sku)) | La SKU per l'insieme di credenziali chiave. |

<a id="accesspolicies" />
### properties.accessPolicies object

| Nome | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| TenantId | string | Sì | Identificatore univoco globale | L'identificatore del tenant Azure Active Directory che contiene **objectId** nei criteri di accesso |
| objectId | string | Sì | Identificatore univoco globale | L'identificatore di oggetto dell'utente AAD o entità servizio che avrà accesso all'insieme di credenziali. È possibile recuperare il valore dai cmdlet **Get-AzureRMADUser** o **Get-AzureRMADServicePrincipal**. |
| autorizzazioni | object | Sì | ([come illustrato di seguito](#permissions)) | Le autorizzazioni concesse per l'insieme di credenziali all'oggetto Active Directory. |

<a id="permissions" />
### properties.accessPolicies.permissions object

| Nome | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| chiavi | array | Sì | Un elenco delimitato da virgole dei valori seguenti:<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | Le autorizzazioni concesse per le chiavi nell'insieme di credenziali all'oggetto Active Directory. Questo valore deve essere specificato come matrice di valori consentiti. |
| chiavi private | array | Sì | Un elenco delimitato da virgole dei valori seguenti:<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | Le autorizzazioni concesse per le chiavi private nell'insieme di credenziali all'oggetto Active Directory. Questo valore deve essere specificato come matrice di valori consentiti. |

<a id="sku" />
### oggetto properties.SKU

| Nome | Tipo | Obbligatorio | Valori consentiti | Descrizione |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | Sì | **standard**<br />* * premium * * | Il livello di servizio dell’insieme di credenziali chiave da utilizzare. Standard supporta chiavi private e chiavi protette tramite software. Premium aggiunge il supporto per le chiavi protette tramite HMS. |
| famiglia | enum | Sì | **A** | La famiglia di sku da utilizzare. 
 
	
## esempi

Nell'esempio seguente vengono distribuiti un insieme di credenziali chiave e una chiave privata.

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


## Passaggi successivi

- Per informazioni generali sugli insiemi di credenziali chiave, vedere [Introduzione all'insieme di credenziali chiave Azure](./key-vault/key-vault-get-started.md).
- Per un esempio di riferimento a una chiave privata nell’insieme di credenziali chiave durante la distribuzione di modelli, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_1217_2015-->
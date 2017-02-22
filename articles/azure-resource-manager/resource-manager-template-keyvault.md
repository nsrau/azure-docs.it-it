---
title: Modello di Resource Manager per l&quot;insieme di credenziali delle chiavi | Microsoft Docs
description: Mostra lo schema di Gestione risorse per la distribuzione di insiemi di credenziali delle chiavi tramite un modello.
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: f72ae06c2e31de5d8a1121a9e265c23f016fffe9


---
# <a name="key-vault-template-schema"></a>Schema del modello di insieme di credenziali chiave
Crea un insieme di credenziali chiave.

## <a name="schema-format"></a>Formato dello schema
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

## <a name="values"></a>Valori
Nelle tabelle seguenti vengono descritti i valori che è necessario impostare nello schema.

| Nome | Valore |
| --- | --- |
| type |Enum<br />Obbligatorio<br />**Microsoft.KeyVault/vaults**<br /><br />Tipo di risorsa da creare. |
| apiVersion |Enum<br />Obbligatorio<br />**2015-06-01** o **2014-12-19-preview**<br /><br />Versione dell'API da usare per creare la risorsa. |
| Nome |String<br />Obbligatorio<br />Un nome univoco in Azure.<br /><br />Nome dell'insieme di credenziali delle chiavi da creare. Per creare un nome univoco, si prenda in considerazione l'uso della funzione [uniqueString](resource-group-template-functions.md#uniquestring) con la convenzione di denominazione in uso, come illustrato nell'esempio riportato di seguito. |
| location |string<br />Obbligatorio<br />Area valida per gli insiemi di credenziali delle chiavi. Per determinare le aree valide, vedere [Supported regions](resource-manager-supported-services.md#supported-regions) (Aree supportate).<br /><br />Area che deve ospitare l'insieme di credenziali delle chiavi. |
| properties |Oggetto<br />Obbligatorio<br />[oggetto delle proprietà](#properties)<br /><br />Oggetto che specifica il tipo dell'insieme di credenziali delle chiavi da creare. |
| resources |Array<br />Facoltativo<br />Valori consentiti: [risorse segrete dell'insieme di credenziali delle chiavi](resource-manager-template-keyvault-secret.md)<br /><br />Risorse figlio per l'insieme di credenziali delle chiavi. |

<a id="properties" />

### <a name="properties-object"></a>oggetto delle proprietà
| Nome | Valore |
| --- | --- |
| enabledForDeployment |Boolean<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'insieme di credenziali delle chiavi è abilitato per la distribuzione di macchine virtuali o di Service Fabric. |
| enabledForTemplateDeployment |Boolean<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'insieme di credenziali delle chiavi è abilitato per l'uso nelle distribuzioni di modelli di Resource Manager. Per ulteriori informazioni, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption |Boolean<br />Facoltativo<br />**true** o **false**<br /><br />Specifica se l'insieme di credenziali delle chiavi è abilitato per la crittografia di volumi. |
| TenantId |String<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore del tenant per la sottoscrizione. È possibile recuperarlo con il cmdlet [Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) di PowerShell o il comando **azure account show** dell'interfaccia della riga di comando di Azure. |
| accessPolicies |Array<br />Obbligatorio<br />[oggetto accessPolicies](#accesspolicies)<br /><br />Matrice che include al massimo 16 oggetti che specifica le autorizzazioni per l'utente o l'entità servizio. |
| sku |Oggetto<br />Obbligatorio<br />[oggetto sku](#sku)<br /><br />SKU dell'insieme di credenziali delle chiavi. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies object
| Nome | Valore |
| --- | --- |
| TenantId |String<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore del tenant di Azure Active Directory contenente l' **objectId** di questi criteri di accesso. |
| objectId |String<br />Obbligatorio<br />**Identificatore univoco globale**<br /><br />Identificatore dell'oggetto dell'utente o dell'entità servizio di Azure Active Directory che ha accesso all'insieme di credenziali. È possibile recuperare il valore con il cmdlet [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) o [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) di PowerShell oppure con i comandi **azure ad user** o **azure ad sp** dell'interfaccia della riga di comando di Azure. |
| autorizzazioni |Oggetto<br />Obbligatorio<br />[oggetto permissions](#permissions)<br /><br />Autorizzazioni concesse per l'insieme di credenziali all'oggetto Active Directory. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions object
| Nome | Valore |
| --- | --- |
| chiavi |Array<br />Obbligatorio<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />Autorizzazioni concesse per le chiavi dell'insieme di credenziali all'oggetto Active Directory. Questo valore deve essere specificato come matrice di uno o più valori consentiti. |
| chiavi private |Array<br />Obbligatorio<br />**all**, **delete**, **get**, **list**, **set**<br /><br />Autorizzazioni concesse per i segreti nell'insieme di credenziali all'oggetto Active Directory. Questo valore deve essere specificato come matrice di uno o più valori consentiti. |

<a id="sku" />

### <a name="propertiessku-object"></a>oggetto properties.SKU
| Nome | Valore |
| --- | --- |
| Nome |Enum<br />Obbligatorio<br />**standard** o **premium** <br /><br />Livello di servizio del KeyVault da usare.  Standard supporta chiavi private e chiavi protette tramite software.  Premium aggiunge il supporto per le chiavi protette tramite HMS. |
| famiglia |Enum<br />Obbligatorio<br />**A** <br /><br />Famiglia di SKU da usare. |

## <a name="examples"></a>esempi
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
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
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
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## <a name="quickstart-templates"></a>Modelli di Guida introduttiva
Il modello di Guida introduttiva seguente distribuisce un insieme di credenziali delle chiavi.

* [Creare un insieme di credenziali delle chiavi](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni generali sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).
* Per un esempio di riferimento a una chiave privata nell’insieme di credenziali chiave durante la distribuzione di modelli, vedere [Passare valori protetti durante la distribuzione](resource-manager-keyvault-parameter.md).




<!--HONumber=Nov16_HO3-->



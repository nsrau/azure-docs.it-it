---
title: Esempi di modelli di Resource Manager per le impostazioni di diagnostica
description: Esempi di modelli di Azure Resource Manager per applicare le impostazioni di diagnostica di Monitoraggio di Azure a una risorsa di Azure.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/11/2020
ms.openlocfilehash: 4b2727cfdc31dca509a4c8416010bbb40d2e46bb
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055419"
---
# <a name="resource-manager-template-samples-for-diagnostic-settings-in-azure-monitor"></a>Esempi di modelli di Resource Manager per le impostazioni di diagnostica in Monitoraggio di Azure
Questo articolo include esempi di [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) per creare impostazioni di diagnostica per una risorsa di Azure. Ogni esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

Per creare un'impostazione di diagnostica per una risorsa di Azure, aggiungere una risorsa di tipo `<resource namespace>/providers/diagnosticSettings` al modello. Questo articolo fornisce esempi per alcuni tipi di risorse, ma è possibile applicare lo stesso modello ad altri tipi. La raccolta di metriche e log consentiti può variare per ogni tipo di risorsa.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="diagnostic-setting-for-activity-log"></a>Impostazione di diagnostica per il log attività
L'esempio seguente crea un'impostazione di diagnostica per un log attività aggiungendo una risorsa di tipo `Microsoft.Insights/diagnosticSettings` al modello.

> [!IMPORTANT]
> Le impostazioni di diagnostica per i log attività vengono create per una sottoscrizione, non per un gruppo di risorse come nel caso delle impostazioni per le risorse di Azure. Per distribuire il modello di gestione delle risorse, usare `New-AzSubscriptionDeployment` per PowerShell o `az deployment sub create` per l'interfaccia della riga di comando di Azure.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
          "type": "String"
        },
        "workspaceId": {
          "type": "String"
        },
        "storageAccountId": {
          "type": "String"
        },
        "eventHubAuthorizationRuleId": {
          "type": "String"
        },
        "eventHubName": {
          "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
        "value": "Send to all locations"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```


## <a name="diagnostic-setting-for-azure-key-vault"></a>Impostazione di diagnostica per Azure Key Vault 
L'esempio seguente crea un'impostazione di diagnostica per Azure Key Vault aggiungendo una risorsa di tipo `Microsoft.KeyVault/vaults/providers/diagnosticSettings` al modello.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },
        "vaultName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "Microsoft.KeyVault/vaults/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('vaultName'), '/Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "AuditEvent",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "AllMetrics",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "vaultName": {
        "value": "MyVault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-sql-database"></a>Impostazione di diagnostica per database SQL di Azure
L'esempio seguente crea un'impostazione di diagnostica per un database SQL di Azure aggiungendo una risorsa di tipo `microsoft.sql/servers/databases/providers/diagnosticSettings` al modello.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "String"
        },        
        "serverName": {
            "type": "String"
        },
        "dbName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }

    },
    "resources": [
        {
          "type": "microsoft.sql/servers/databases/providers/diagnosticSettings",
          "apiVersion": "2017-05-01-preview",
          "name": "[concat(parameters('serverName'),'/',parameters('dbName'),'/microsoft.insights', parameters('settingName'))]",
          "dependsOn": [],
          "properties": {
            "workspaceId": "[parameters('workspaceId')]",
            "storageAccountId": "[parameters('storageAccountId')]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "logs": [
              {
                "category": "SQLInsights",
                "enabled": true
              },
              {
                "category": "AutomaticTuning",
                "enabled": true
              },
              {
                "category": "QueryStoreRuntimeStatistics",
                "enabled": true
              },
              {
                "category": "QueryStoreWaitStatistics",
                "enabled": true
              },
              {
                "category": "Errors",
                "enabled": true
              },
              {
                "category": "DatabaseWaitStatistics",
                "enabled": true
              },
              {
                "category": "Timeouts",
                "enabled": true
              },
              {
                "category": "Blocks",
                "enabled": true
              },
              {
                "category": "Deadlocks",
                "enabled": true
              }
            ],
            "metrics": [
              {
                "category": "Basic",
                "enabled": true
              },
              {
                "category": "InstanceAndAppAdvanced",
                "enabled": true
              },
              {
                "category": "WorkloadManagement",
                "enabled": true
              }
            ]
          }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "serverName": {
        "value": "MySqlServer"
      },
      "dbName": {
        "value": "MySqlDb"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-recovery-services-vault"></a>Impostazione di diagnostica per l'insieme di credenziali di Servizi di ripristino
L'esempio seguente crea un'impostazione di diagnostica per un insieme di credenziali di Servizi di ripristino aggiungendo una risorsa di tipo `microsoft.recoveryservices/vaults/providers/diagnosticSettings` al modello. Questo esempio specifica la modalità di raccolta descritta in [Log delle risorse di Azure](../platform/resource-logs.md#send-to-log-analytics-workspace). Specificare `Dedicated` o `AzureDiagnostics` per la proprietà `logAnalyticsDestinationType`.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "recoveryServicesName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "microsoft.recoveryservices/vaults/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('recoveryServicesName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "AzureBackupReport",
                        "enabled": false
                    },
                    {
                        "category": "CoreAzureBackup",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupJobs",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupAlerts",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupPolicy",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupStorage",
                        "enabled": true
                    },
                    {
                        "category": "AddonAzureBackupProtectedInstance",
                        "enabled": true
                    },
                    {
                        "category": "AzureSiteRecoveryJobs",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryEvents",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicatedItems",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationStats",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryRecoveryPoints",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryReplicationDataUploadRate",
                        "enabled": false
                    },
                    {
                        "category": "AzureSiteRecoveryProtectedDiskDataChurn",
                        "enabled": false
                    }
                ],
                "logAnalyticsDestinationType": "Dedicated"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "recoveryServicesName": {
        "value": "my-vault"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-log-analytics-workspace"></a>Impostazione di diagnostica per l'area di lavoro Log Analytics
L'esempio seguente crea un'impostazione di diagnostica per un insieme di credenziali dell'area di lavoro Log Analytics aggiungendo una risorsa di tipo `Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings` al modello. In questo esempio, i dati di controllo sulle query eseguite nell'area di lavoro vengono inviati alla stessa area di lavoro.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String"
        },
        "settingName": {
            "type": "String"
        },
        "workspaceId": {
            "type": "String"
        },
        "storageAccountId": {
            "type": "String"
        },
        "eventHubAuthorizationRuleId": {
            "type": "String"
        },
        "eventHubName": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces/providers/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[concat(parameters('workspaceName'), '/Microsoft.Insights/', parameters('settingName'))]",
            "dependsOn": [],
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "metrics": [],
                "logs": [
                    {
                        "category": "LAQueryLogs",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "settingName": {
          "value": "Send to all locations"
      },
      "workspaceName": {
        "value": "MyWorkspace"
      },
      "workspaceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      },
      "storageAccountId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
      },
      "eventHubAuthorizationRuleId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNameSpace/authorizationrules/RootManageSharedAccessKey"
      },
      "eventHubName": {
        "value": "my-eventhub"
      }
  }
}
```

## <a name="diagnostic-setting-for-azure-storage"></a>Impostazione di diagnostica per Archiviazione di Azure
L'esempio seguente crea un'impostazione di diagnostica per ogni endpoint del servizio di archiviazione disponibile nell'account di archiviazione. Un'impostazione viene applicata a ogni singolo servizio di archiviazione disponibile nell'account. I servizi di archiviazione disponibili dipendono dal tipo di account di archiviazione. Questo modello crea un'impostazione di diagnostica per un servizio di archiviazione nell'account solo se esiste per l'account. Per ogni servizio disponibile, l'impostazione di diagnostica abilita le metriche delle transazioni e la raccolta di log delle risorse per le operazioni di lettura, scrittura ed eliminazione.

### <a name="template-file"></a>File modello

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        },
        "settingName": {
            "type": "string"
        },
        "storageSyncName": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-10-01",
            "name": "nested",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "parameters": {
                    "endpoints": {
                        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01', 'Full').properties.primaryEndpoints]"
                    },
                    "settingName": {
                        "value": "[parameters('settingName')]"
                    },
                    "storageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    },
                    "storageSyncName": {
                        "value": "[parameters('storageSyncName')]"
                    },
                    "workspaceId": {
                        "value": "[parameters('workspaceId')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "endpoints": {
                            "type": "object"
                        },
                        "settingName": {
                            "type": "String"
                        },
                        "storageAccountName": {
                            "type": "String"
                        },
                        "storageSyncName": {
                            "type": "String"
                        },
                        "workspaceId": {
                            "type": "String"
                        }
                    },
                    "variables": {
                        "hasblob": "[contains(parameters('endpoints'),'blob')]",
                        "hastable": "[contains(parameters('endpoints'),'table')]",
                        "hasfile": "[contains(parameters('endpoints'),'file')]",
                        "hasqueue": "[contains(parameters('endpoints'),'queue')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasblob')]",
                            "type": "Microsoft.Storage/storageAccounts/blobServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hastable')]",
                            "type": "Microsoft.Storage/storageAccounts/tableServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",

                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasfile')]",
                            "type": "Microsoft.Storage/storageAccounts/fileServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        },
                        {
                            "condition": "[variables('hasqueue')]",
                            "type": "Microsoft.Storage/storageAccounts/queueServices/providers/diagnosticsettings",
                            "apiVersion": "2017-05-01-preview",
                            "name": "[concat(parameters('storageAccountName'),'/default/Microsoft.Insights/', parameters('settingName'))]",
                            "properties": {
                                "workspaceId": "[parameters('workspaceId')]",
                                "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageSyncName'))]",
                                "logs": [
                                    {
                                        "category": "StorageRead",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageWrite",
                                        "enabled": true
                                    },
                                    {
                                        "category": "StorageDelete",
                                        "enabled": true
                                    }
                                ],
                                "metrics": [
                                    {
                                        "category": "Transaction",
                                        "enabled": true
                                    }
                                ]
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountName": {
          "value": "mymonitoredstorageaccount"
      },
      "settingName": {
          "value": "Send to all locations"
      },
      "storageSyncName": {
          "value": "mystorageaccount"
      },
      "workspaceId": {
          "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/MyResourceGroup/providers/microsoft.operationalinsights/workspaces/MyWorkspace"
      }
    }
  }
```

## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](resource-manager-samples.md).
* [Altre informazioni sulle impostazioni di diagnostica](../platform/diagnostic-settings.md).

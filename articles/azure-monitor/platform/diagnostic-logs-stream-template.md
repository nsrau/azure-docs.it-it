---
title: Abilitare automaticamente le impostazioni di diagnostica con un modello di Resource Manager
description: Informazioni su come usare un modello di Resource Manager per creare impostazioni di diagnostica che permettono di trasmettere i log di diagnostica a Hub eventi o di memorizzarli in un account di archiviazione.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 3/26/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 7edce5175a1dda66abf3316cb8f0eb33e9f64ef7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371469"
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Abilitare automaticamente le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager
Questo articolo illustra come usare un [modello di Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) per configurare le impostazioni di diagnostica in una risorsa durante la sua creazione. In questo modo è possibile avviare automaticamente i log di diagnostica e le metriche a hub eventi di streaming, memorizzarli in un Account di archiviazione o di inviarli a un'area di lavoro di Log Analitica quando viene creata una risorsa.

> [!WARNING]
> Il formato dei dati di log nell'account di archiviazione verrà modificato in JSON Lines dal 1° novembre 2018. [Vedere questo articolo per una descrizione dell'impatto e per informazioni su come aggiornare gli strumenti per gestire il nuovo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Il metodo da usare per abilitare i log di diagnostica tramite un modello di Resource Manager dipende dal tipo di risorsa.

* **risorse non di calcolo** , ad esempio Gruppi di sicurezza di rete, App per la logica e Automazione, usare le [impostazioni di diagnostica descritte in questo articolo](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings).
* **risorse di calcolo** , basate su WAD/LAD, usare il [file di configurazione WAD/LAD descritto in questo articolo](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

Questo articolo illustra come configurare la diagnostica con entrambi i metodi.

I passaggi di base sono i seguenti:

1. Creare un modello come file JSON che descrive come creare la risorsa e abilitare la diagnostica.
2. [Distribuire il modello con un metodo di distribuzione qualsiasi](../../azure-resource-manager/resource-group-template-deploy.md).

Di seguito viene fornito un esempio del file JSON modello da generare per le risorse di calcolo e non di calcolo.

## <a name="non-compute-resource-template"></a>Modello per risorse non di calcolo
Per le risorse non di calcolo, è necessario eseguire due operazioni:

1. Aggiungere parametri al blob dei parametri per il nome account di archiviazione, ID di regole di autorizzazione dell'hub eventi e/o ID dell'area di lavoro di Log Analitica (abilitando la memorizzazione dei log di diagnostica in un account di archiviazione, lo streaming dei log a hub eventi e/o inviare i log a monitoraggio di Azure).
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Nella matrice di risorse della risorsa per cui si vuole abilitare i log di diagnostica, aggiungere una risorsa di tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2017-05-01-preview",
        "properties": {
          "name": "[parameters('settingName')]",
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
          "eventHubName": "[parameters('eventHubName')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

Il BLOB delle proprietà per l'impostazione di diagnostica è nel [formato descritto in questo articolo](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate). L'aggiunta della proprietà `metrics` consentirà anche di inviare le metriche delle risorse a questi stessi output, purché [la risorsa supporti le metriche di Monitoraggio di Azure](../../azure-monitor/platform/metrics-supported.md).

Di seguito è riportato un esempio completo che crea un'app per la logica e abilita la trasmissione agli hub eventi e la memorizzazione in un account di archiviazione.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "https://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "[concat('Microsoft.Insights/', parameters('settingName'))]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modello per risorse di calcolo
Per abilitare la diagnostica su una risorsa di calcolo, ad esempio una macchina virtuale o un cluster di Service Fabric, seguire questa procedura:

1. Aggiungere l'estensione Diagnostica di Azure alla definizione della risorsa macchina virtuale.
2. Specificare un account di archiviazione e/o un hub eventi come parametro.
3. Aggiungere il contenuto del file XML WADCfg nella proprietà XMLCfg, usando le sequenze di escape corrette per i caratteri XML.

> [!WARNING]
> Quest'ultimo passaggio può risultare difficile. [vedere questo articolo](../../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) .
> 
> 

L'intero processo, esempi compresi, viene descritto in [questo documento](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui log di diagnostica di Azure](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Trasmettere log di diagnostica di Azure a Hub eventi](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)



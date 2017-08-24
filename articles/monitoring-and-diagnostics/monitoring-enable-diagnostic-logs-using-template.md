---
title: Abilitare automaticamente le impostazioni di diagnostica con un modello di Resource Manager | Microsoft Docs
description: Informazioni su come usare un modello di Resource Manager per creare impostazioni di diagnostica che permettono di trasmettere i log di diagnostica a Hub eventi o di memorizzarli in un account di archiviazione.
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: dde2435e976bbd14ca35cccc714ea21dcc5817b7
ms.contentlocale: it-it
ms.lasthandoff: 08/07/2017

---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Abilitare automaticamente le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager
Questo articolo illustra come usare un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per configurare le impostazioni di diagnostica in una risorsa durante la sua creazione. Ciò consente di iniziare automaticamente a trasmettere le metriche e i log di diagnostica a Hub eventi, di memorizzarli in un account di archiviazione o di inviarli a Log Analytics quando viene creata una risorsa.

Il metodo da usare per abilitare i log di diagnostica tramite un modello di Resource Manager dipende dal tipo di risorsa.

* **risorse non di calcolo** , ad esempio Gruppi di sicurezza di rete, App per la logica e Automazione, usare le [impostazioni di diagnostica descritte in questo articolo](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings).
* **risorse di calcolo** , basate su WAD/LAD, usare il [file di configurazione WAD/LAD descritto in questo articolo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Questo articolo illustra come configurare la diagnostica con entrambi i metodi.

I passaggi di base sono i seguenti:

1. Creare un modello come file JSON che descrive come creare la risorsa e abilitare la diagnostica.
2. [Distribuire il modello con un metodo di distribuzione qualsiasi](../azure-resource-manager/resource-group-template-deploy.md).

Di seguito viene fornito un esempio del file JSON modello da generare per le risorse di calcolo e non di calcolo.

## <a name="non-compute-resource-template"></a>Modello per risorse non di calcolo
Per le risorse non di calcolo, è necessario eseguire due operazioni:

1. Aggiungere parametri al BLOB dei parametri per il nome dell'account di archiviazione, l'ID dell'area regola del bus di servizio e/o l'ID dell'area di lavoro di Log Analytics OMS, abilitando la memorizzazione dei log di diagnostica in un account di archiviazione, la trasmissione dei log a Hub eventi e/o l'invio dei log a Log Analytics.
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Nella matrice di risorse della risorsa per cui si vuole abilitare i log di diagnostica, aggiungere una risorsa di tipo `[resource namespace]/providers/diagnosticSettings`.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
    ]
    ```

Il BLOB delle proprietà per l'impostazione di diagnostica è nel [formato descritto in questo articolo](https://msdn.microsoft.com/library/azure/dn931931.aspx). L'aggiunta della proprietà `metrics` consentirà anche di inviare le metriche delle risorse a questi stessi output, purché [la risorsa supporti le metriche di Monitoraggio di Azure](monitoring-supported-metrics.md).

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
      "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
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
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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
> Quest'ultimo passaggio può risultare difficile. [vedere questo articolo](../virtual-machines/windows/extensions-diagnostics-template.md#diagnostics-configuration-variables) .
> 
> 

L'intero processo, esempi compresi, viene descritto in [questo documento](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)
* [Trasmettere log di diagnostica di Azure a Hub eventi](monitoring-stream-diagnostic-logs-to-event-hubs.md)



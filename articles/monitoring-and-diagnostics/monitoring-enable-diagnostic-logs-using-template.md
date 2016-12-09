---
title: Abilitare automaticamente le impostazioni di diagnostica con un modello di Resource Manager | Microsoft Docs
description: Informazioni su come usare un modello di Resource Manager per creare impostazioni di diagnostica che permettono di trasmettere i log di diagnostica a Hub eventi o di memorizzarli in un account di archiviazione.
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 30b023429cfdc671ac68175f94ffb48379c58dda


---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Abilitare automaticamente le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager
Questo articolo illustra come usare un [modello di Azure Resource Manager](../resource-group-authoring-templates.md) per configurare le impostazioni di diagnostica in una risorsa durante la sua creazione. Ciò consente di iniziare automaticamente a trasmettere le metriche e i log di diagnostica a Hub eventi, di memorizzarli in un account di archiviazione o di inviarli a Log Analytics quando viene creata una risorsa.

Il metodo da usare per abilitare i log di diagnostica tramite un modello di Resource Manager dipende dal tipo di risorsa.

* **risorse non di calcolo** , ad esempio Gruppi di sicurezza di rete, App per la logica e Automazione, usare le [impostazioni di diagnostica descritte in questo articolo](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
* **risorse di calcolo** , basate su WAD/LAD, usare il [file di configurazione WAD/LAD descritto in questo articolo](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Questo articolo illustra come configurare la diagnostica con entrambi i metodi.

I passaggi di base sono i seguenti:

1. Creare un modello come file JSON che descrive come creare la risorsa e abilitare la diagnostica.
2. [Distribuire il modello con un metodo di distribuzione qualsiasi](../resource-group-template-deploy.md).

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
          ]
        }
      }
    ]
    ```

Il BLOB delle proprietà per l'impostazione di diagnostica è nel [formato descritto in questo articolo](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Di seguito è riportato un esempio completo che crea un gruppo di sicurezza di rete e abilita la trasmissione a Hub eventi e la memorizzazione in un account di archiviazione.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
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
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
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
> Quest'ultimo passaggio può risultare difficile. [vedere questo articolo](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) .
> 
> 

L'intero processo, esempi compresi, viene descritto in [questo documento](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi
* [Altre informazioni sui log di diagnostica di Azure](monitoring-overview-of-diagnostic-logs.md)
* [Trasmettere log di diagnostica di Azure a Hub eventi](monitoring-stream-diagnostic-logs-to-event-hubs.md)




<!--HONumber=Nov16_HO3-->



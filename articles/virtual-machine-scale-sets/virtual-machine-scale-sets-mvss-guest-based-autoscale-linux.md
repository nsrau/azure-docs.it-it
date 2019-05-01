---
title: Usare la scalabilità automatica di Azure con metriche guest in un modello di set di scalabilità Linux | Microsoft Docs
description: Informazioni su come eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità di macchine virtuali Linux
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868982"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità Linux

Esistono due tipi di metriche in Azure che vengono raccolti dalle macchine virtuali e set di scalabilità: Metriche host e Guest. A livello generale, se si desidera utilizzare standard con CPU, disco e metriche di rete, le metriche host sono una scelta ideale. Se, tuttavia, è necessario una selezione più ampia di metriche, le metriche guest devono essere esaminate in:

Le metriche host non richiedono configurazione aggiuntiva perché vengono raccolte dall'host di macchina virtuale, mentre le metriche guest richiedono di installare il [estensione diagnostica di Azure](../virtual-machines/windows/extensions-diagnostics-template.md) o il [estensione diagnostica di Azure per Linux ](../virtual-machines/linux/diagnostic-extension.md) nella macchina virtuale guest. È preferibile usare metriche guest anziché metriche host perché le metriche guest offrono una selezione più ampia di metriche. Le metriche relative al consumo di memoria, disponibili solo tramite metriche guest, ne sono un esempio. Le metriche host supportate sono elencate [qui](../azure-monitor/platform/metrics-supported.md), mentre le metriche guest usate comunemente sono elencate [qui](../azure-monitor/platform/autoscale-common-metrics.md). Questo articolo illustra come modificare la [modello di set di scalabilità a validità base](virtual-machine-scale-sets-mvss-start.md) usare le regole di scalabilità automatica basate sulle metriche guest per i set di scalabilità Linux.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

In un [articolo precedente](virtual-machine-scale-sets-mvss-start.md) è stato creato un modello di set di scalabilità di base. È ora verrà usare tale modello precedente e modificarlo per creare un modello che distribuisce un set di scalabilità Linux con scalabilità automatica in base alle metriche di guest.

Per prima cosa, aggiungere i parametri per `storageAccountName` e `storageAccountSasToken`. L'agente di diagnostica archivia i dati di metrica in una [tabella](../cosmos-db/table-storage-how-to-use-dotnet.md) in questo account di archiviazione. A partire dalla versione 3.0 dell'agente di diagnostica Linux, non è più supportato l'uso di una chiave di accesso di archiviazione. Usare invece un [token di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Successivamente, modificare il set di scalabilità `extensionProfile` in modo da includere l'estensione di diagnostica. In questa configurazione specificare l'ID della risorsa del set di scalabilità da cui vengono raccolte le metriche, oltre all'account di archiviazione e al token di firma di accesso condiviso da usare per archiviare le metriche. Specificare la frequenza con cui vengono aggregate le metriche, in questo caso, ogni minuto, e le metriche di cui tenere traccia, in questo caso, la percentuale di memoria usata. Per informazioni più dettagliate su questa configurazione e sulle metriche diverse dalla percentuale di memoria usata, vedere [questa documentazione](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Aggiungere infine una risorsa `autoscaleSettings` per configurare la scalabilità automatica in base a queste metriche. Questa risorsa include una clausola `dependsOn` che fa riferimento al set di scalabilità per verificarne l'esistenza prima di tentare l'applicazione della scalabilità automatica. Se si sceglie una metrica diversa su cui basare la scalabilità automatica, si dovrebbe usare `counterSpecifier` della configurazione per l'estensione di diagnostica come `metricName` nella configurazione di scalabilità automatica. Per altre informazioni sulla configurazione della scalabilità automatica, vedere [Procedure consigliate per la scalabilità automatica](../azure-monitor/platform/autoscale-best-practices.md) e la [documentazione di riferimento sulle API REST di monitoraggio di Azure](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

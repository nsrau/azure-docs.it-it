---
title: Usare la scalabilità automatica di Azure con le metriche Guest in un modello di set di scalabilità Linux
description: Informazioni su come eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità di macchine virtuali Linux
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: aa004cc3ad6c02937ae3c3c8bdb1d5ebd225f434
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124806"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità Linux

In Azure sono disponibili due tipi generali di metriche che vengono raccolte da VM e set di scalabilità: metriche host e metriche Guest. A un livello elevato, se si vuole usare la metrica standard della CPU, del disco e della rete, le metriche host sono adatte. Se, tuttavia, è necessaria una selezione più ampia di metriche, le metriche Guest devono essere esaminate.

Le metriche host non richiedono una configurazione aggiuntiva perché vengono raccolte dalla macchina virtuale host, mentre le metriche Guest richiedono l'installazione dell' [estensione diagnostica di Azure Windows](../virtual-machines/windows/extensions-diagnostics-template.md) o dell' [estensione Linux diagnostica di Azure](../virtual-machines/linux/diagnostic-extension.md) nella VM guest. È preferibile usare metriche guest anziché metriche host perché le metriche guest offrono una selezione più ampia di metriche. Le metriche relative al consumo di memoria, disponibili solo tramite metriche guest, ne sono un esempio. Le metriche host supportate sono elencate [qui](../azure-monitor/platform/metrics-supported.md), mentre le metriche guest usate comunemente sono elencate [qui](../azure-monitor/platform/autoscale-common-metrics.md). Questo articolo illustra come modificare il [modello di set di scalabilità di base valido](virtual-machine-scale-sets-mvss-start.md) per usare le regole di scalabilità automatica in base alle metriche Guest per i set di scalabilità Linux.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

In un [articolo precedente](virtual-machine-scale-sets-mvss-start.md) è stato creato un modello di set di scalabilità di base. A questo punto si userà il modello precedente e lo si modificherà per creare un modello che distribuisce un set di scalabilità Linux con scalabilità automatica basata sulle metriche Guest.

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

---
title: Usare la scalabilità automatica di Azure con le metriche guest in un modello di set di scalabilità LinuxUse Azure autoscale with guest metrics in a Linux scale set template
description: Informazioni su come eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità di macchine virtuali Linux
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271959"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Eseguire la scalabilità automatica usando metriche guest in un modello di set di scalabilità Linux

Esistono due tipi generali di metriche in Azure raccolte dalle macchine virtuali e dai set di scalabilità: metriche host e metriche Guest.There are two broad types of metrics in Azure that are gathered from VMs and scale sets: Host metrics and Guest metrics. A livello generale, se si desidera utilizzare le metriche standard di CPU, disco e rete, le metriche host sono adatte. Se, tuttavia, è necessaria una selezione più ampia di metriche, è necessario esaminare le metriche guest.

Le metriche host non richiedono un'installazione aggiuntiva perché vengono raccolte dalla macchina virtuale host, mentre le metriche guest richiedono l'installazione [dell'estensione Diagnostica](../virtual-machines/windows/extensions-diagnostics-template.md) di Windows Azure o [dell'estensione Diagnostica](../virtual-machines/linux/diagnostic-extension.md) di Azure Linux nella macchina virtuale guest. È preferibile usare metriche guest anziché metriche host perché le metriche guest offrono una selezione più ampia di metriche. Le metriche relative al consumo di memoria, disponibili solo tramite metriche guest, ne sono un esempio. Le metriche host supportate sono elencate [qui](../azure-monitor/platform/metrics-supported.md), mentre le metriche guest usate comunemente sono elencate [qui](../azure-monitor/platform/autoscale-common-metrics.md). Questo articolo illustra come modificare il modello di set di [scalabilità praticabile di base](virtual-machine-scale-sets-mvss-start.md) per usare le regole di scalabilità automatica in base alle metriche guest per i set di scalabilità Linux.This article shows how to modify the basic able scale set template to use autoscale rules based on guest metrics for Linux scale sets.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

In un [articolo precedente](virtual-machine-scale-sets-mvss-start.md) avevamo creato un modello di set di scalabilità di base. Ora useremo quel modello precedente e lo modificheremo per creare un modello che distribuisce un set di scalabilità Linux con scalabilità automatica basata sulla metrica guest.

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

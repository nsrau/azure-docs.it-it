---
title: Fare riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure
description: Informazioni su come aggiungere una rete virtuale in un modello di set di scalabilità di macchine virtuali di Azure esistente
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275539"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Aggiungere un riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure

Questo articolo illustra come modificare il [modello di set di scalabilità di base](virtual-machine-scale-sets-mvss-start.md) per distribuire in una rete virtuale esistente anziché crearne uno nuovo.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

In un [articolo precedente](virtual-machine-scale-sets-mvss-start.md) è stato creato un modello di set di scalabilità di base. A questo punto si userà il modello precedente e lo si modificherà per creare un modello che distribuisce un set di scalabilità in una rete virtuale esistente. 

Prima di tutto aggiungere un parametro `subnetId`. Questa stringa viene passata nella configurazione del set di scalabilità e permette al set di scalabilità di identificare la subnet creata in precedenza in cui distribuire le macchine virtuali. Il formato della stringa deve essere: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Ad esempio, per distribuire il set di scalabilità in una rete virtuale esistente denominata `myvnet`, subnet `mysubnet`, gruppo di risorse `myrg` e sottoscrizione`00000000-0000-0000-0000-000000000000`, l'ID subnet sarebbe il seguente: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Dal momento che si usa una rete virtuale esistente e non è necessario distribuirne una nuova, eliminare successivamente la risorsa rete virtuale dalla matrice `resources`.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

La rete virtuale esiste già prima della distribuzione del modello, non è quindi necessario specificare una clausola dependsOn dal set di scalabilità alla rete virtuale. Individuare le righe seguenti:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Infine, passare il parametro `subnetId` impostato dall'utente (invece di usare `resourceId` per ottenere l'ID di un VNET nella stessa distribuzione, ovvero il modello di set di scalabilità di base valido).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

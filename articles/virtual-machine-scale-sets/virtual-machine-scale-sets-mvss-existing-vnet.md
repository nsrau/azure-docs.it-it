---
title: "Fare riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure | Microsoft Docs"
description: "Informazioni su come aggiungere una rete virtuale in un modello di set di scalabilità di macchine virtuali di Azure esistente"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: negat
ms.openlocfilehash: 28117d467b491704aed8d45e5eba42530579dfa2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Aggiungere un riferimento a una rete virtuale esistente in un modello di set di scalabilità di Azure

Questo articolo illustra come modificare il [modello di set di scalabilità minimo valido](./virtual-machine-scale-sets-mvss-start.md) per eseguire la distribuzione in una rete virtuale esistente anziché crearne uno nuovo.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

Il modello di set di scalabilità minimo valido è disponibile [qui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), mentre il modello per la distribuzione del set di scalabilità in una rete virtuale esistente è disponibile [qui](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Viene ora esaminato il diff usato per creare questo modello, `git diff minimum-viable-scale-set existing-vnet`, passo per passo:

Prima di tutto si aggiunge un parametro `subnetId`. Questa stringa viene passata nella configurazione del set di scalabilità e permette di identificare la subnet, creata in precedenza, in cui distribuire le macchine virtuali. La stringa deve essere nel formato seguente: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`. Ad esempio, per distribuire il set di scalabilità in una rete virtuale esistente denominata `myvnet`, subnet `mysubnet`, gruppo di risorse `myrg` e sottoscrizione`00000000-0000-0000-0000-000000000000`, l'ID subnet sarebbe il seguente: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

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

Dal momento che si usa una rete virtuale esistente e non è necessario distribuirne una nuova, è possibile eliminare la risorsa rete virtuale dalla matrice `resources`.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2016-12-01",
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

La rete virtuale esiste già prima della distribuzione del modello, non è quindi necessario specificare una clausola dependsOn dal set di scalabilità alla rete virtuale. Vengono quindi eliminate le righe seguenti:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Infine, si passa il parametro `subnetId` impostato dall'utente, anziché usare `resourceId` per ottenere l'ID di una rete virtuale nella stessa distribuzione, perché questa operazione viene eseguita dal modello di set di scalabilità minimo valido.

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

---
title: "Set di scalabilità di macchine virtuali di Azure: modello di rete virtuale esistente | Microsoft Docs"
description: "Informazioni su come creare un modello di set di scalabilità con una rete virtuale esistente"
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
ms.date: 3/06/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ddb3e1789e49d138e744c2238679236134b69324
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-article"></a>Informazioni sull'articolo

Questo articolo illustra come modificare il [modello di set di scalabilità minimo valido](./virtual-machine-scale-sets-mvss-start.md) per eseguire la distribuzione in una rete virtuale esistente anziché crearne uno nuovo.

## <a name="modifying-the-minimum-viable-scale-set-to-deploy-into-an-existing-virtual-network"></a>Modifica del modello di set di scalabilità minimo valido per eseguire la distribuzione in una rete virtuale esistente

Il modello di set di scalabilità minimo valido è disponibile [qui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), mentre il modello per la distribuzione del set di scalabilità in una rete virtuale esistente è disponibile [qui](https://raw.githubusercontent.com/gatneil/mvss/existing-vnet/azuredeploy.json). Viene ora esaminato il diff usato per creare questo modello, `git diff master minimum-viable-scale-set`, passo per passo:

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


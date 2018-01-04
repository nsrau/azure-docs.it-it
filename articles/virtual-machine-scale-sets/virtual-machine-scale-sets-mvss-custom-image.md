---
title: "Fare riferimento a un'immagine personalizzata in un modello di set di scalabilità di Azure | Microsoft Docs"
description: "Informazioni su come aggiungere un'immagine personalizzata in un modello di set di scalabilità di macchine virtuali di Azure esistente"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2017
ms.author: negat
ms.openlocfilehash: 28d2c080048a7f82e83ad9c1794c9757b330a8c7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Aggiungere un'immagine personalizzata in un modello di set di scalabilità di Azure

Questo articolo illustra come modificare il [modello di set di scalabilità a validità minima](./virtual-machine-scale-sets-mvss-start.md) per la distribuzione da un'immagine personalizzata.

## <a name="change-the-template-definition"></a>Modificare la definizione del modello

Può essere visualizzato il modello di set di scala valida minima [qui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json), e il modello per la distribuzione di scala impostato da un'immagine personalizzata può essere visto [qui](https://raw.githubusercontent.com/gatneil/mvss/custom-image/azuredeploy.json). Viene ora esaminato il diff usato per creare questo modello, `git diff minimum-viable-scale-set custom-image`, passo per passo:

### <a name="creating-a-managed-disk-image"></a>Creazione dell'immagine di un disco gestito

Se si dispone già di un'immagine di disco gestito personalizzata (una risorsa di tipo `Microsoft.Compute/images`), è possibile ignorare questa sezione.

Aggiungere innanzitutto un `sourceImageVhdUri` parametro, che è l'URI del BLOB in archiviazione di Azure che contiene l'immagine personalizzata per la distribuzione da generalizzato.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Successivamente, aggiungere una risorsa di tipo `Microsoft.Compute/images`, ovvero l'immagine del disco gestito in base all'URI blob generalizzato `sourceImageVhdUri`. Questa immagine deve trovarsi nella stessa area del set di scalabilità da cui viene usata. Nelle proprietà dell'immagine, specificare il tipo del sistema operativo, la posizione del blob (dal `sourceImageVhdUri` parametro) e il tipo di account di archiviazione:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2016-04-30-preview",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

Nella scala dei set di risorse, aggiungere un `dependsOn` clausola che fa riferimento all'immagine personalizzata per verificare che l'immagine viene creata prima tenta di distribuire da quell'immagine di impostare la scala:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2016-04-30-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Modifica delle proprietà del set di scalabilità per usare l'immagine del disco gestito

Nel `imageReference` della scala impostare `storageProfile`, anziché specificare il server di pubblicazione, offerta, sku e versione di un'immagine della piattaforma, specificare il `id` del `Microsoft.Compute/images` risorse:

```diff
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
-              "publisher": "Canonical",
-              "offer": "UbuntuServer",
-              "sku": "16.04-LTS",
-              "version": "latest"
+              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

In questo esempio, utilizzare il `resourceId` funzione per ottenere l'ID risorsa dell'immagine creato nello stesso modello. Se è stato creato l'immagine del disco gestito in anticipo, è necessario fornire l'ID di tale immagine invece. L'ID deve essere nel formato: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Fasi successive

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

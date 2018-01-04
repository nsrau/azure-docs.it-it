---
title: "Convertire un modello di set di scalabilità di Azure Resource Manager per usare i dischi gestiti | Documentazione Microsoft"
description: "Convertire un modello di set di scalabilità in un modello di set di scalabilità per i dischi gestiti."
keywords: "set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/18/2017
ms.author: negat
ms.openlocfilehash: 760e30f5c6f4ecaff299bae1725548a6a7c5184c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2017
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Convertire un modello di set di scalabilità in un modello di set di scalabilità per i dischi gestiti

I clienti con un modello di Resource Manager per la creazione di un set di scalabilità che non usa i dischi gestiti potrebbero volerlo modificare per usare i dischi gestiti. In questo articolo viene illustrato come utilizzare dischi gestiti, utilizzando ad esempio una richiesta di pull dal [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates), un repository basato sulla community per i modelli di gestione risorse di esempio. La richiesta pull completa è disponibile all'indirizzo [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998). Le parti pertinenti del diff sono sotto, con le spiegazioni:

## <a name="making-the-os-disks-managed"></a>Impostazione dei dischi del sistema operativo come gestiti

Nelle differenze seguenti, vengono rimossi diverse variabili correlate alle proprietà di account e il disco di archiviazione. Tipo di account di archiviazione non è più necessario (Standard_LRS è il valore predefinito), ma è possibile specificare se si desidera. Con il disco gestito sono supportati solo Standard_LRS e Premium_LRS. Nel modello precedente, per generare i nomi degli account di archiviazione, vengono usati un nuovo suffisso dell'account di archiviazione, una matrice di stringhe univoca e il conteggio degli account di archiviazione. Queste variabili non sono più necessarie nel nuovo modello perché il disco gestito crea automaticamente gli account di archiviazione per conto del cliente. Analogamente, il nome di contenitore vhd e il nome del disco del sistema operativo non sono più necessarie poiché disco gestito automaticamente nomi di contenitori di archiviazione blob e i dischi sottostanti.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Le differenze seguenti, per calcolare che API viene aggiornata alla versione 2016-04-30-preview, ovvero la versione richiesta meno recente per il supporto di dischi gestiti con set di scalabilità. Se si desidera, è possibile utilizzare i dischi non gestiti nella nuova versione dell'API con la sintassi precedente. Se si solo aggiornare la versione API di calcolo e non modificare nemmeno altri elementi, il modello deve continuare a funzionare come prima.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Nelle differenze seguenti, la risorsa di account di archiviazione viene rimosso dalla matrice di risorse completamente. La risorsa non è più necessario come disco gestito vengono create automaticamente.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Nelle differenze seguenti, è possibile osservare che viene rimossa la dipende dalla clausola che fa riferimento dalla scala impostata per il ciclo che la creazione di account di archiviazione. Nel modello precedente ciò assicura che gli account di archiviazione vengano creati prima che il set di scalabilità inizi la creazione, ma questa clausola non è più necessaria con i dischi gestiti. La proprietà di contenitori di disco rigido virtuale viene rimossa anche, insieme alla proprietà di nome disco del sistema operativo come queste proprietà vengono gestite automaticamente dietro le quinte dal disco gestito. È possibile aggiungere `"managedDisk": { "storageAccountType": "Premium_LRS" }` nella configurazione "osDisk" Se si desidera che i dischi del sistema operativo premium. Solo le VM con una "s" maiuscola o minuscola nell'unità SKU possono usare dischi Premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Non esistono proprietà esplicite nella configurazione del set di scalabilità che indichino se usare dischi gestiti o non gestiti. Il set di scalabilità determina quale usare in base alle proprietà presenti nel profilo di archiviazione. È quindi importante, quando si modifica il modello, assicurarsi che nel profilo di archiviazione del set di scalabilità siano presenti le proprietà corrette.


## <a name="data-disks"></a>Dischi dati

Con le modifiche precedenti, il set di scalabilità usa dischi gestiti per il disco del sistema operativo. Non si è però parlato dei dischi dati. Per aggiungere dischi dati, aggiungere la proprietà "dataDisks" in "storageProfile" allo stesso livello di "osDisk". Il valore della proprietà è un elenco JSON di oggetti, ognuno dei quali ha proprietà "lun" (che devono essere univoche per ogni disco dati in una VM), "createOption" ("empty" è attualmente la sola opzione supportata) e "diskSizeGB" (le dimensioni del disco in gigabyte, che devono essere maggiori di 0 e minori di 1024), come nell'esempio seguente: 

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se si specificano `n` dischi in questa matrice, ogni VM nel set di scalabilità ottiene `n` dischi dati. Si noti tuttavia che questi dischi dati sono dispositivi RAW. Non sono formattati. Spetta al cliente di collegare, partizionare e formattare i dischi prima di usarli. Facoltativamente, è anche possibile specificare `"managedDisk": { "storageAccountType": "Premium_LRS" }` in ogni oggetto disco dati per specificare che deve essere un disco dati premium. Solo le VM con una "s" maiuscola o minuscola nell'unità SKU possono usare dischi Premium.

Per altre informazioni sull'uso dei dischi dati con i set di scalabilità, vedere [questo articolo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passaggi successivi
Ad esempio, per i modelli di Resource Manager con set di scalabilità, cercare "vmss" nel [repository di GitHub dei modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates).

Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).


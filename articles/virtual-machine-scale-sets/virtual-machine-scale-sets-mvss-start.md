---
title: "Set di scalabilità di macchine virtuali di Azure: set di scalabilità a validità minima | Microsoft Docs"
description: "Informazioni su come creare un modello di un set di scalabilità a validità minima"
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
ms.date: 2/14/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: eee63d76941f327304348262f00afafe8413cb6b
ms.lasthandoff: 03/09/2017


---

# <a name="about-this-tutorial"></a>Informazioni sull'esercitazione

I [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) sono un ottimo modo di distribuire gruppi di risorse correlate. Questa serie di esercitazioni illustra come creare un modello di set di scalabilità a validità minima e come modificarlo per adattarsi a vari scenari. Tutti gli esempi provengono da questo [archivio GitHub](https://github.com/gatneil/mvss). Ciascun diff mostrato in questa procedura dettagliata è il risultato di un'operazione `git diff` tra rami dell'archivio. Questi modelli e diff sono pensati per essere esempi semplici, non completi. Per esempi più completi di modelli di set di scalabilità, vedere l'[archivio GitHub Modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates) e cercare le cartelle contenenti la stringa `vmss`.

## <a name="a-minimum-viable-scale-set"></a>Un set di scalabilità a validità minima

Il modello di un set di scalabilità a validità minima può essere visualizzato [qui](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json). Se si ha già familiarità con i modelli, è possibile passare direttamente alla sezione "Passaggi successivi" per istruzioni su come modificare il modello per altri scenari. In caso contrario, questa descrizione passo per passo potrebbe risultare molto utile. Per iniziare, si esaminerà il diff per creare questo modello (`git diff master minimum-viable-scale-set`) passo per passo:

In primo luogo, si definiscono `$schema` e `contentVersion` del modello. `$schema` definisce la versione del linguaggio del modello e viene usato per l'evidenziazione della sintassi di Visual Studio e per funzionalità di convalida simili. `contentVersion`, in realtà, non viene usato da Azure, ma ha il compito di aiutare l'utente a tenere traccia della versione del modello.

```diff
+{
+  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
+  "contentVersion": "1.0.0.0",
```

Successivamente, si definiscono due parametri, `adminUsername` e `adminPassword`. I parametri sono valori specificati dall'utente al momento della distribuzione. `adminUsername` è semplicemente un `string`, tuttavia, poiché `adminPassword` è un segreto, gli viene assegnato il tipo `securestring`. In seguito si vedrà come questi parametri vengano passati nella configurazione del set di scalabilità.

```diff
+  "parameters": {
+    "adminUsername": {
+      "type": "string"
+    },
+    "adminPassword": {
+      "type": "securestring"
+    }
+  },
```

I modelli di Resource Manager consentono inoltre di definire variabili da usare in un secondo momento nel modello. In questo esempio non vengono usate variabili, quindi l'oggetto JSON rimane vuoto.

```diff
+  "variables": {},
```

Successivamente, si definiscono gli elementi da distribuire: le risorse del modello. A differenza di `parameters` e `variables` (oggetti JSON), `resources` è un elenco JSON di oggetti JSON.

```diff
+  "resources": [
```

Tutte le risorse richiedono un `type`, `name`, `apiVersion` e `location`. La prima risorsa è di tipo `Microsft.Network/virtualNetwork` con nome `myVnet` e apiVersion `2016-03-30`. Per determinare la versione più recente dell'API di un tipo di risorsa, fare riferimento alla [Documentazione dell'API del servizio REST](https://docs.microsoft.com/rest/api/).

```diff
+    {
+      "type": "Microsoft.Network/virtualNetworks",
+      "name": "myVnet",
+      "apiVersion": "2016-12-01",
```

Per specificare il percorso per la rete virtuale, si usa una [funzione di modello di Resource Manager](../azure-resource-manager/resource-group-template-functions.md), da racchiudere tra virgolette e parentesi quadre come nell'esempio seguente: `"[<template-function>]"`. In questo caso viene usata la funzione resourceGroup, che non riceve argomenti e restituisce un oggetto JSON con metadati sul gruppo di risorse in cui viene eseguita la distribuzione. Il gruppo di risorse viene impostato dall'utente al momento della distribuzione. L'oggetto JSON viene quindi indicizzato con `.location` per ottenere il percorso dall'oggetto JSON.

```diff
+      "location": "[resourceGroup().location]",
```


Ogni risorsa di Resource Manager dispone di una propria sezione `properties` per le configurazioni a essa specifiche. In questo caso, si specifica che la rete virtuale deve disporre di una subnet usando l'intervallo di indirizzi IP privati `10.0.0.0/16`. Un set di scalabilità è sempre contenuto in una subnet. Non può estendersi a più subnet.

```diff
+      "properties": {
+        "addressSpace": {
+          "addressPrefixes": [
+            "10.0.0.0/16"
+          ]
+        },
+        "subnets": [
+          {
+            "name": "mySubnet",
+            "properties": {
+              "addressPrefix": "10.0.0.0/16"
+            }
+          }
+        ]
+      }
+    },
```

Oltre alle proprietà obbligatorie `type`, `name`, `apiVersion` e `location`, ogni risorsa può disporre facoltativamente di un elenco `dependsOn` di stringhe per specificare quali altre risorse di questa distribuzione debbano finire prima di distribuire la risorsa in questione. In questo caso è presente un solo elemento nell'elenco: la rete virtuale menzionata in precedenza. Si specifica la dipendenza poiché il set di scalabilità deve disporre di una rete esistente prima di creare macchine virtuali. In questo modo, il set di scalabilità può indicare indirizzi IP privati alle macchine virtuali, prendendoli dall'intervallo specificato in precedenza nelle proprietà di rete. Il formato di ogni stringa nell'elenco dependsOn è `<type>/<name>` (i medisimi `type` e `name` usati in precedenza per la definizione della risorsa di rete virtuale).

```diff
+    {
+      "type": "Microsoft.Compute/virtualMachineScaleSets",
+      "name": "myScaleSet",
+      "apiVersion": "2016-04-30-preview",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Network/virtualNetworks/myVnet"
+      ],
```

Il set di scalabilità deve conoscere le dimensioni (il "nome sku") e il numero (la "capacità sku") delle macchine virtuali da creare. Per visualizzare le dimensioni di macchina virtuale disponibili, vedere la [documentazione sulle dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes).

```diff
+      "sku": {
+        "name": "Standard_A1",
+        "capacity": 2
+      },
```

Il set di scalabilità deve inoltre sapere come gestire gli aggiornamenti: al momento sono disponibili due opzioni, `Manual` e `Automatic`. Per ulteriori informazioni sulle differenze tra le due opzioni, vedere la documentazione su [come aggiornare un set di scalabilità](./virtual-machine-scale-sets-upgrade-scale-set.md).

```diff
+      "properties": {
+        "upgradePolicy": {
+          "mode": "Manual"
+        },
```

Il set di scalabilità deve inoltre sapere quale sistema operativo installare nelle macchine virtuali. In questo esempio le macchine virtuali vengono create con un'immagine di Ubuntu 16.04-LTS con patch complete.

```diff
+        "virtualMachineProfile": {
+          "storageProfile": {
+            "imageReference": {
+              "publisher": "Canonical",
+              "offer": "UbuntuServer",
+              "sku": "16.04-LTS",
+              "version": "latest"
+            }
+          },
```

Poiché il set di scalabilità distribuisce più macchine virtuali, invece di specificare il nome di ogni macchina si specifica un `computerNamePrefix`. Il set di scalabilità aggiunge un indice a questo prefisso per ogni macchina virtuale. Pertanto, i nomi delle macchine sono in formato `<computerNamePrefix>_<auto-generated-index>`. In questo frammento di codice vengono inoltre usati i parametri precedenti per configurare il nome utente e la password amministratore per tutte le macchine virtuali nel set di scalabilità. Questa operazione viene eseguita con la funzione del modello `parameters`, che riceve una stringa che specifica il parametro a cui fare riferimento e restituisce il valore di tale parametro.

```diff
+          "osProfile": {
+            "computerNamePrefix": "vm",
+            "adminUsername": "[parameters('adminUsername')]",
+            "adminPassword": "[parameters('adminPassword')]"
+          },
```

Infine, è necessario specificare la configurazione di rete per le macchine virtuali nel set di scalabilità. In questo caso, è sufficiente specificare l'ID della subnet creata in precedenza in cui il set di scalabilità inserirà le interfacce di rete. È possibile ottenere l'ID della rete virtuale che contiene la subnet usando la funzione del modello `resourceId`. Questa funzione riceve il tipo e il nome di una risorsa e restituisce il relativo identificatore completo (questo ID è nel formato: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`). Tuttavia, l'identificatore della rete virtuale non è sufficiente. È necessario indicare anche la subnet specifica in cui devono trovarsi le macchine virtuali del set di scalabilità, in modo da concatenare `/subnets/mySubnet` all'ID della rete virtuale. Il risultato è l'ID completo della subnet. La concatenazione viene eseguita con la funzione `concat`, che riceve una serie di stringhe e restituisce la relativa concatenazione.

```diff
+          "networkProfile": {
+            "networkInterfaceConfigurations": [
+              {
+                "name": "myNic",
+                "properties": {
+                  "primary": "true",
+                  "ipConfigurations": [
+                    {
+                      "name": "myIpConfig",
+                      "properties": {
+                        "subnet": {
+                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                        }
+                      }
+                    }
+                  ]
+                }
+              }
+            ]
+          }
+        }
+      }
+    }
+  ]
+}

```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

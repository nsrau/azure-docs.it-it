---
title: "Informazioni sui modelli di set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Informazioni su come creare un modello di set di scalabilità a validità minima per set di scalabilità di macchine virtuali di Azure"
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
ms.date: 06/01/2017
ms.author: negat
ms.openlocfilehash: 65f02c4675eb752dcc82e9a1d1c7f6c2c193fc32
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Informazioni sui modelli di set di scalabilità di macchine virtuali
I [modelli di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment) sono un ottimo modo di distribuire gruppi di risorse correlate. Questa serie di esercitazioni illustra come creare un modello di set di scalabilità a validità minima e come modificarlo per adattarsi a vari scenari. Tutti gli esempi provengono da questo [archivio GitHub](https://github.com/gatneil/mvss). 

Questo modello è progettato per essere semplice. Per esempi più completi di modelli di set di scalabilità, vedere [Azure Quickstart Templates GitHub repository](https://github.com/Azure/azure-quickstart-templates) (Archivio GitHub Modelli di avvio rapido di Azure) e cercare le cartelle contenenti la stringa `vmss`.

Se si ha già familiarità con i modelli, è possibile passare direttamente alla sezione "Passaggi successivi" per informazioni su come modificare questo modello.

## <a name="review-the-template"></a>Rivedere il modello

Usare GitHub per rivedere il modello di set di scalabilità a validità minima che è possibile usare, ovvero [azuredeploy.json](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json).

In questa esercitazione verranno esaminati in dettaglio i diff (`git diff master minimum-viable-scale-set`) per creare il modello di set di scalabilità più semplice che è possibile usare.

## <a name="define-schema-and-contentversion"></a>Definire $schema e contentVersion
Si definiscono prima gli elementi `$schema` e `contentVersion` del modello. L'elemento `$schema` definisce la versione del linguaggio del modello e viene usato per l'evidenziazione della sintassi di Visual Studio e per funzionalità di convalida simili. L'elemento `contentVersion` non viene usato da Azure. Però consente di tenere traccia della versione del modello.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```
## <a name="define-parameters"></a>Definire i parametri
Successivamente, si definiscono due parametri, `adminUsername` e `adminPassword`. I parametri sono valori specificati al momento della distribuzione. Il parametro `adminUsername` è semplicemente un tipo `string`, ma dato che `adminPassword` è un segreto, gli viene assegnato il tipo `securestring`. Successivamente questi parametri vengono passati nella configurazione del set di scalabilità.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Definire le variabili
I modelli di Resource Manager consentono anche di definire variabili da usare successivamente nel modello. In questo esempio non vengono usate variabili, quindi l'oggetto JSON è vuoto.

```json
  "variables": {},
```

## <a name="define-resources"></a>Definire le risorse
La sezione successiva del modello riguarda le risorse. In questa sezione si definiscono gli elementi da distribuire. A differenza di `parameters` e `variables` (che sono oggetti JSON), `resources` è un elenco JSON di oggetti JSON.

```json
   "resources": [
```

Tutte le risorse richiedono le proprietà `type`, `name`, `apiVersion` e `location`. La prima risorsa di questo esempio è di tipo `Microsft.Network/virtualNetwork`, nome `myVnet` e apiVersion `2016-03-30`. Per determinare la versione più recente dell'API di un tipo di risorsa, vedere [Azure REST API documentation](https://docs.microsoft.com/rest/api/) (Documentazione dell'API REST di Azure).

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2016-12-01",
```

## <a name="specify-location"></a>Specificare il percorso
Per specificare il percorso della rete virtuale, viene usata una [funzione di modello di Resource Manager](../azure-resource-manager/resource-group-template-functions.md). Questa funzione deve essere racchiusa tra virgolette e parentesi quadre, nel modo seguente: `"[<template-function>]"`. In questo caso, si usa la funzione `resourceGroup`. La funzione non accetta argomenti e restituisce un oggetto JSON con metadati sul gruppo di risorse a cui viene eseguita la distribuzione. Il gruppo di risorse viene impostato dall'utente al momento della distribuzione. L'oggetto JSON viene quindi indicizzato con `.location` per ottenere il percorso dall'oggetto JSON.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Specificare le proprietà di rete virtuale
Ogni risorsa di Resource Manager dispone di una propria sezione `properties` per le configurazioni a essa specifiche. In questo caso, si specifica che la rete virtuale deve avere una subnet che usa l'intervallo `10.0.0.0/16` di indirizzi IP privati. Un set di scalabilità è sempre contenuto in una subnet. Non può estendersi a più subnet.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Aggiungere un elenco dependsOn
In aggiunta alle proprietà `type`, `name`, `apiVersion` e `location` necessarie, ogni risorsa può avere un elenco `dependsOn` di stringhe facoltativo. Questo elenco specifica le altre risorse di questa distribuzione che devono essere completate prima di distribuire la risorsa.

In questo caso c'è solo un elemento nell'elenco, ovvero la rete virtuale dell'esempio precedente. Si specifica la dipendenza poiché il set di scalabilità deve disporre di una rete esistente prima di creare macchine virtuali. In questo modo, il set di scalabilità può indicare gli indirizzi IP privati delle macchine virtuali dall'intervallo degli indirizzi IP specificato precedentemente nelle proprietà della rete. Il formato di ogni stringa nell'elenco dependsOn è `<type>/<name>`. Usare gli stessi `type` e `name` inclusi precedentemente nella definizione delle risorse della rete virtuale.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2016-04-30-preview",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Specificare le proprietà del set di scalabilità
I set di scalabilità hanno molte proprietà per personalizzare le macchine virtuali nel set di scalabilità. Per un elenco completo di queste proprietà, vedere [Scale set REST API documentation](https://docs.microsoft.com/en-us/rest/api/virtualmachinescalesets/create-or-update-a-set) (Documentazione dell'API REST del set di scalabilità). Per questa esercitazione, verranno impostate solo alcune proprietà usate di frequente.
### <a name="supply-vm-size-and-capacity"></a>Specificare capacità e dimensioni della macchina virtuale
Il set di scalabilità deve conoscere le dimensioni della macchina virtuale da creare ("nome SKU") e quante macchine virtuali di questo tipo deve creare ("capacità SKU"). Per visualizzare le dimensioni disponibili di macchine virtuali, vedere [VM Sizes documentation](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes) (Documentazione delle dimensioni di macchine virtuali).

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Scegliere un tipo di aggiornamenti
Il set di scalabilità deve inoltre sapere come gestire gli aggiornamenti: Attualmente sono disponibili due opzioni, `Manual` e `Automatic`. Per ulteriori informazioni sulle differenze tra le due opzioni, vedere la documentazione su [come aggiornare un set di scalabilità](./virtual-machine-scale-sets-upgrade-scale-set.md).

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>Scegliere il sistema operativo delle macchine virtuali
Il set di scalabilità deve conoscere quale sistema operativo installare nelle macchine virtuali. In questo esempio le macchine virtuali vengono create con un'immagine di Ubuntu 16.04-LTS con patch complete.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>Specificare computerNamePrefix
Il set di scalabilità distribuisce più macchine virtuali. Anziché specificare il nome di ogni macchina virtuale, si specifica `computerNamePrefix`. Il set di scalabilità aggiunge un indice al prefisso di ogni macchina virtuale. Pertanto, i nomi delle macchine virtuali hanno il formato `<computerNamePrefix>_<auto-generated-index>`.

In questo frammento di codice vengono anche usati i parametri precedenti per configurare il nome utente e la password dell'amministratore di tutte le macchine virtuali nel set di scalabilità. Questa operazione viene eseguita con la funzione di modello `parameters`. Questa funzione accetta una stringa che specifica il parametro a cui fare riferimento a e restituisce il valore del parametro.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Specificare la configurazione di rete delle macchine virtuali
Infine, è necessario specificare la configurazione di rete per le macchine virtuali nel set di scalabilità. In questo caso, è sufficiente specificare l'ID della subnet creata in precedenza. Questa specifica indica al set di scalabilità di inserire le interfacce di rete in questa subnet.

È possibile ottenere l'ID della rete virtuale che contiene la subnet usando la funzione di modello `resourceId`. Questa funzione accetta il tipo e il nome di una risorsa e restituisce il relativo identificatore completo della risorsa. L'ID ha il formato seguente: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

Tuttavia, l'identificatore della rete virtuale non è sufficiente. È necessario indicare la subnet specifica in cui devono trovarsi le macchine virtuali del set di scalabilità. A tale scopo, concatenare `/subnets/mySubnet` all'ID della rete virtuale. Il risultato è l'ID completo della subnet. Eseguire la concatenazione con la funzione `concat`, che accetta una serie di stringhe e restituisce la relativa concatenazione.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]

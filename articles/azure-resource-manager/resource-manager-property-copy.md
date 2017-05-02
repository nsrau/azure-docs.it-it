---
title: "Più copie di una proprietà di risorsa di Azure | Documentazione Microsoft"
description: "Nel modello di Azure Resource Manager creare più istanze di una proprietà in un tipo di risorsa. Per questo scenario è necessario usare la funzione take anziché il ciclo di copia."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: da79ff58e892bde5a35456992105de9bdbe36364
ms.lasthandoff: 04/18/2017


---
# <a name="create-multiple-instances-of-property-on-resource-type"></a>Creare più istanze della proprietà sul tipo di risorsa
È possibile usare [copy](resource-group-create-multiple.md) solo su tipi di risorse e non sulle proprietà al loro interno. Questo requisito può creare problemi per l'utente quando si desidera creare più istanze di un elemento che fa parte di una risorsa. Uno scenario frequente consiste nel creare più dischi dati per una macchina virtuale. Non è possibile usare copy con i dischi dati perché dataDisks è una proprietà nella macchina virtuale e non il relativo tipo di risorsa. È invece possibile creare una matrice con tutti i dischi dati necessari e passare il numero effettivo di dischi dati da creare. Nella definizione di macchina virtuale usare la funzione `take` per ottenere solo il numero di elementi effettivamente desiderato dalla matrice.

Un esempio completo di questo schema è illustrato nel modello per la [creazione di una macchina virtuale con una selezione dinamica di dischi dati](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Questo articolo riporta le sezioni pertinenti del modello di distribuzione. Alcune parti del modello sono state rimosse per evidenziare le sezioni coinvolte nella creazione dinamica di alcuni dischi dati. 

## <a name="define-template-with-variable-for-the-property"></a>Modello di definizione con una variabile per la proprietà

Per creare una macchina virtuale con più dischi dati, aggiungere una variabile di matrice che definisce più dischi dati di quanti ne siano necessari. Nel parametro `numDataDisks` passare il numero effettivo di dischi dati da creare. Usare `take` per specificare il numero esatto di elementi della matrice da assegnare alla macchina virtuale.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ... /* not all elements shown */
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        "storageProfile": {
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

## <a name="create-multiple-virtual-machines-with-multiple-data-disks"></a>Creare più macchine virtuali con più dischi dati

La funzione **take** e l'elemento **copy** possono essere usati insieme per creare più istanze di una risorsa con un numero variabile di elementi per una proprietà. Ad esempio, si supponga di dover creare più macchine virtuali, ma ognuna con un numero diverso di dischi dati. 

Per assegnare a ciasuno un nome che identifichi la macchina virtuale associata, inserire l'array di dischi dati in un modello distinto. Includere i parametri del nome della macchina virtuale e il numero di dischi dati da restituire. Nella sezione dell'output, inserire il numero di elementi specificati.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

Nel modello padre si includono i parametri relativi al numero di macchine virtuali e una matrice relativa al numero dei dischi dati per ogni macchina virtuale.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

Nella sezione delle risorse distribuire più istanze del modello che definisce i dischi dati. 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

Nella stessa sezione distribuire più istanze della macchina virtuale. Per i dischi dati, fare riferimento alla distribuzione annidata che contiene il numero e i nomi corretti dei dischi dati.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione di più istanze di una risorsa, vedere [Distribuire più istanze delle risorse nei modelli di Azure Resource Manager](resource-group-create-multiple.md).
* Per creare risorse iterate in sequenza, vedere [Sequential looping for Azure Resource Manager templates](resource-manager-sequential-loop.md) (Ciclo sequenziale per i modelli di Azure Resource Manager).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).



---
title: "Distribuire più istanze di risorse | Microsoft Docs"
description: "Usare l&quot;operazione di copia e le matrici in un modello di Gestione risorse di Azure per eseguire più iterazioni durante la distribuzione delle risorse."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: e37eb89227ce8927f1c7f53306168962dce9b8f1


---
# <a name="create-multiple-instances-of-resources-in-azure-resource-manager"></a>Creare più istanze di risorse in Gestione risorse di Azure
In questo argomento viene illustrato come eseguire un'iterazione del modello di Gestione risorse di Azure per creare più istanze di una risorsa.

## <a name="copy-copyindex-and-length"></a>copy, copyIndex e length
All'interno della risorsa da ricreare più volte è possibile definire un oggetto **copy** che specifica il numero di iterazioni da eseguire. L'oggetto copy avrà il formato seguente:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

Per accedere al valore di iterazione corrente, è possibile usare la funzione **copyIndex()**. Nell'esempio seguente viene usato copyIndex con la funzione concat per costruire un nome.

    [concat('examplecopy-', copyIndex())]

Quando si creano più risorse da una matrice di valori, è possibile usare la funzione **length** per specificare il numero. Fornire la matrice come parametro alla funzione length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

L'oggetto copia può essere applicato solo a una risorsa di livello principale, mentre non è possibile applicarlo a una proprietà su un tipo di risorsa o a una risorsa figlio. Tuttavia, in questo argomento viene illustrato come specificare più elementi per una proprietà e creare più istanze di una risorsa figlio. L'esempio di codice seguente mostra in che modo è possibile applicare la copia:

    "resources": [
      {
        "type": "{provider-namespace-and-type}",
        "name": "parentResource",
        "copy": {  
          /* yes, copy can be applied here */
        },
        "properties": {
          "exampleProperty": {
            /* no, copy cannot be applied here */
          }
        },
        "resources": [
          {
            "type": "{provider-type}",
            "name": "childResource",
            /* copy can be applied if resource is promoted to top level */ 
          }
        ]
      }
    ] 

Anche se non è possibile applicare la funzione **copy** a una proprietà, tale proprietà rimane parte delle iterazioni della risorsa che la contiene. Pertanto, è possibile usare **copyIndex()** all'interno della proprietà per specificare i valori.

Esistono diversi scenari in cui può essere utile eseguire l'iterazione di una proprietà in una risorsa. Ad esempio, è consigliabile specificare più dischi di dati per una macchina virtuale. Per informazioni su come eseguire l'iterazione di una proprietà, vedere [Create multiple instances when copy won't work](#create-multiple-instances-when-copy-wont-work) (Creare più istanze quando non è possibile eseguire la funzione copy). 

Per usare le risorse figlio, vedere [Create multiple instances of a child resource](#create-multiple-instances-of-a-child-resource) (Creare più istanze di una risorsa figlio).

## <a name="use-index-value-in-name"></a>Usare il valore di indice nel nome
È possibile utilizzare l'operazione di copia per creare più istanze di una risorsa denominata in modo univoco in base all'indice incrementale. Ad esempio, è possibile aggiungere un numero univoco alla fine del nome di ogni risorsa distribuita. Per distribuire tre siti Web denominati:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

Usare il modello seguente:

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          }
      } 
    ]

## <a name="offset-index-value"></a>Offset del valore di indice
Nell'esempio precedente, il valore di indice è compreso fra 0 e 2. Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione **copyIndex()**, ad esempio **copyIndex(1)**. Il numero di iterazioni da eseguire viene comunque specificato nell'elemento copia, ma il valore di copyIndex è compensato dal valore specificato. Quindi, utilizzando lo stesso modello dell'esempio precedente, ma specificando **copyIndex(1)** si distribuirebbero tre siti Web denominati:

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>Usare l'oggetto copy con matrice
L'operazione di copia è utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Per distribuire tre siti Web denominati:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

Usare il modello seguente:

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      }
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2015-08-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {
              "serverFarmId": "hostingPlanName"
          } 
      } 
    ]

Naturalmente, è possibile impostare il numero di copie su un valore diverso dalla lunghezza della matrice. Ad esempio, si potrebbe creare una matrice con molti valori, poi passare a un valore di parametro che specifichi il numero degli elementi della matrice da distribuire. In tal caso, impostare il numero di copie come illustrato nel primo esempio. 

## <a name="depend-on-resources-in-a-loop"></a>In base alle risorse in un ciclo
L'elemento **dependsOn** consente di specificare che una risorsa sia distribuita dopo un'altra. Per distribuire una risorsa che dipende dalla raccolta di risorse in un ciclo, usare il nome del ciclo di copia nell'elemento **dependsOn** . L'esempio seguente illustra come distribuire tre account di archiviazione prima di distribuire la macchina virtuale. La definizione completa di macchina virtuale non viene visualizzata. Si noti che la copia ha la proprietà **name** impostata su **storagecopy** e l'elemento **dependsOn** per le macchine virtuali impostato su **storagecopy**.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "resources": [
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "accountType": "Standard_LRS"
                 },
                "copy": { 
                     "name": "storagecopy", 
                     "count": 3 
                  }
            },
           {
               "apiVersion": "2015-06-15", 
               "type": "Microsoft.Compute/virtualMachines", 
               "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
               "dependsOn": ["storagecopy"],
               ...
           }
        ],
        "outputs": {}
    }

## <a name="create-multiple-instances-of-a-child-resource"></a>Creare più istanze di una risorsa figlio
Non è possibile usare un ciclo di copia per una risorsa figlio. Per creare più istanze di una risorsa cosiddetta "annidata" all'interno di un'altra risorsa, è invece necessario creare tale risorsa come una risorsa di livello superiore. La relazione con la risorsa padre si definisce con le proprietà **type** e **name**.

Si supponga, ad esempio, di definire in genere un set di dati come una risorsa figlio all'interno di una data factory.

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "exampleDataFactory",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "exampleDataSet",
            "dependsOn": [
                "exampleDataFactory"
            ],
            ...
        }
    }]

Per creare più istanze di un set di dati, spostarlo all'esterno della data factory. Il set di dati deve essere sullo stesso livello della data factory, di cui è comunque una risorsa figlio. La relazione fra set di dati e data factory viene mantenuta con le proprietà **type** e **name**. Poiché non è possibile dedurre il tipo dalla sua posizione nel modello, è necessario specificarne il nome completo nel formato seguente:

 **{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}** 

Per stabilire una relazione padre/figlio con un'istanza della data factory, specificare il nome del set di dati che include il nome della risorsa padre. Il nome deve essere nel formato seguente:

**{parent-resource-name}/{child-resource-name}**.  

Nell'esempio seguente viene descritta l'implementazione:

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "exampleDataFactory",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
        "dependsOn": [
            "exampleDataFactory"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "3" 
        } 
        ...
    }]

## <a name="create-multiple-instances-when-copy-wont-work"></a>Creare più istanze quando l'oggetto copy non funziona
È possibile usare **copy** solo su tipi di risorse e non sulle proprietà al loro interno. Questo requisito può creare problemi per l'utente quando si desidera creare più istanze di un elemento che fa parte di una risorsa. Uno scenario frequente consiste nel creare più dischi dati per una macchina virtuale. Non è possibile usare **copy** con i dischi dati perché **dataDisks** è una proprietà nella macchina virtuale e non il relativo tipo di risorsa. È invece possibile creare una matrice con tutti i dischi dati necessari e passare il numero effettivo di dischi dati da creare. Nella definizione di macchina virtuale usare la funzione **take** per ottenere solo il numero di elementi effettivamente desiderato dalla matrice.

Un esempio completo di questo schema è illustrato nel modello per la [creazione di una macchina virtuale con una selezione dinamica di dischi dati](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) .

Le sezioni pertinenti del modello di distribuzione sono riportate nell'esempio seguente. Gran parte del modello è stata rimossa per evidenziare le sezioni coinvolte nella creazione dinamica di alcuni dischi dati. Si noti il parametro **numDataDisks** che consente di passare il numero di dischi da creare. 

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
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
      ...
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
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

La funzione **take** e l'elemento **copy** possono essere usati insieme per creare più istanze di una risorsa con un numero variabile di elementi per una proprietà. Ad esempio, si supponga di dover creare più macchine virtuali, ma ognuna con un numero diverso di dischi dati. Per assegnare a ciasuno un nome che identifichi la macchina virtuale associata, inserire l'array di dischi dati in un modello distinto. Includere i parametri del nome della macchina virtuale e il numero di dischi dati da restituire. Nella sezione dell'output, inserire il numero di elementi specificati.

```
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

```
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

```
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

Nella stessa sezione distribuire più istanze della macchina virtuale. Per i dischi dati, fare riferimento alla distribuzione nidificata che contiene il numero e i nomi corretti dei dischi dati.

```
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

## <a name="return-values-from-a-loop"></a>Restituire i valori di un ciclo
Se da un lato è utile creare più istanze di uno stesso tipo di risorsa, dall'altro restituire i valori del ciclo può essere difficile. Un modo per conservare e restituire valori consiste nell'usare **copy** con un modello nidificato ed eseguire il round trip di una matrice che contiene tutti i valori da restituire. Si supponga, ad esempio, di voler creare più account di archiviazione e restituire per ognuno l'endpoint primario. 

Innanzitutto, creare il modello nidificato che crea l'account di archiviazione, il quale accetta un parametro di matrice per gli URI dei BLOB. Usare questo parametro per eseguire il round trip di tutti i valori delle distribuzioni precedenti. L'output del modello è una matrice che concatena i nuovi URI dei BLOB agli URI precedenti.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

A questo punto, creare un modello padre che dispone di un'istanza statica del modello nidificato ed esegue il ciclo delle istanze rimanenti del modello nidificato. Per ogni istanza della distribuzione a cicli, trasmettere una matrice che rappresenta l'output della distribuzione precedente.

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle sezioni di un modello, vedere [Authoring Azure Resource Manager Templates](resource-group-authoring-templates.md) (Creazione di modelli di Azure Resource Manager).
* Per conoscere tutte le funzioni che è possibile usare in un modello, vedere [Azure Resource Manager Template Functions](resource-group-template-functions.md) (Funzioni del modello di Gestione risorse di Azure).
* Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).




<!--HONumber=Nov16_HO3-->



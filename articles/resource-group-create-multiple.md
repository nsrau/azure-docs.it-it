<properties
   pageTitle="Distribuire più istanze di risorse | Microsoft Azure"
   description="Usare l'operazione di copia e le matrici in un modello di Gestione risorse di Azure per eseguire più iterazioni durante la distribuzione delle risorse."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/01/2015"
   ms.author="tomfitz"/>

# Creare più istanze di risorse in Gestione risorse di Azure

In questo argomento viene illustrato come eseguire un'iterazione del modello di Gestione risorse di Azure per creare più istanze di una risorsa.

## copy, copyIndex e length

All'interno della risorsa da ricreare più volte è possibile definire un oggetto **copy** che specifica il numero di iterazioni da eseguire. L'oggetto copy avrà il formato seguente:

    "copy": { 
        "name": "websitescopy", 
        "count": "[parameters('count')]" 
    } 

È possibile accedere al valore di iterazione corrente con la funzione **copyIndex()**, come illustrato di seguito all'interno della funzione concat.

    [concat('examplecopy-', copyIndex())]

Quando si creano più risorse da una matrice di valori, è possibile usare la funzione **length** per specificare il numero. Fornire la matrice come parametro alla funzione length.

    "copy": {
        "name": "websitescopy",
        "count": "[length(parameters('siteNames'))]"
    }

## Usare il valore di indice nel nome

È possibile utilizzare l'operazione di copia per creare più istanze di una risorsa denominata in modo univoco in base all'indice incrementale. Ad esempio, è possibile aggiungere un numero univoco alla fine del nome di ogni risorsa distribuita. Per distribuire tre siti Web denominati:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

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

## Offset del valore di indice

Si noterà nell'esempio precedente che il valore di indice va da 0 a 2. Per eseguire l'offset del valore di indice, è possibile passare un valore nella funzione **copyIndex()**, ad esempio **copyIndex(1)**. Il numero di iterazioni da eseguire viene comunque specificato nell'elemento copia, ma il valore di copyIndex è compensato dal valore specificato. Quindi, utilizzando lo stesso modello dell'esempio precedente, ma specificando **copyIndex(1)** si distribuirebbero tre siti Web denominati:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Uso con la matrice
   
L'operazione di copia è particolarmente utile quando si lavora con le matrici in quanto è possibile iterare ogni elemento della matrice. Per distribuire tre siti Web denominati:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

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

Naturalmente, si imposta il numero di copia a un valore diverso dalla lunghezza della matrice. Ad esempio, si potrebbe creare una matrice con molti valori, poi passare a un valore di parametro che specifichi il numero degli elementi della matrice da distribuire. In tal caso, impostare il numero di copie come illustrato nel primo esempio.

## In base alle risorse in un ciclo

È possibile specificare che una risorsa deve essere distribuita dopo un'altra risorsa tramite l’elemento **dependsOn**. Quando è necessario distribuire una risorsa che dipende dalla raccolta di risorse in un ciclo, è possibile utilizzare il nome del ciclo di copia nell’elemento **dependsOn**. L’esempio seguente illustra come distribuire 3 account di archiviazione prima di distribuire la macchina virtuale. La definizione completa di macchina virtuale non viene visualizzata. Si noti che l'elemento di copia ha il **nome** impostato su **storagecopy** e l’elemento **dependsOn** per le macchine virtuali anch’esso impostato su **storagecopy**.

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

## Ciclo su una risorsa annidata

Non è possibile utilizzare un ciclo di copia per una risorsa annidata. Se è necessario creare più istanze di una risorsa che è in genere definita come annidata all'interno di un'altra risorsa, si deve invece creare la risorsa come una risorsa di livello principale e definire la relazione con la risorsa padre mediante le proprietà **tipo** e **nome**.

Si supponga, ad esempio, di definire in genere un set di dati come una risorsa annidata all'interno di una Data Factory.

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[variables('dataFactoryName')]",
        ...
        "resources": [
        {
            "type": "datasets",
            "name": "[variables('dataSetName')]",
            "dependsOn": [
                "[variables('dataFactoryName')]"
            ],
            ...
        }
    }]
    
Per creare più istanze di set di dati, è necessario modificare il modello come illustrato di seguito. Si noti che il tipo qualificato come completo e il nome includono il nome della data factory.

    "resources": [
    {
        "type": "Microsoft.DataFactory/datafactories",
        "name": "[variables('dataFactoryName')]",
        ...
    },
    {
        "type": "Microsoft.DataFactory/datafactories/datasets",
        "name": "[concat(variables('dataFactoryName'), '/', variables('dataSetName'), copyIndex())]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "copy": { 
            "name": "datasetcopy", 
            "count": "[parameters('count')]" 
        } 
        ...
    }]

## Passaggi successivi
- Per altre informazioni sulle sezioni di un modello, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).
- Per tutte le funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md).
- Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di Gestione risorse di Azure](resource-group-template-deploy.md).

<!---HONumber=AcomDC_1203_2015-->
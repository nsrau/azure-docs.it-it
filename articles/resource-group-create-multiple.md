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
   ms.date="08/27/2015"
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
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
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
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[length(parameters('org'))]" 
          }, 
          "properties": {} 
      } 
    ]

Naturalmente, si imposta il numero di copia a un valore diverso dalla lunghezza della matrice. Ad esempio, si potrebbe creare una matrice con molti valori, poi passare a un valore di parametro che specifichi il numero degli elementi della matrice da distribuire. In tal caso, impostare il numero di copie come illustrato nel primo esempio.

## Passaggi successivi
- Per altre informazioni sulle sezioni di un modello, vedere [Creazione di modelli di Gestione risorse di Azure](./resource-group-authoring-templates.md).
- Per tutte le funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Gestione risorse di Azure](./resource-group-template-functions.md).
- Per altre informazioni sulla distribuzione di modelli, vedere [Distribuire un'applicazione con il modello di gestione risorse di Azure](azure-portal/resource-group-template-deploy.md).

<!---HONumber=Oct15_HO3-->
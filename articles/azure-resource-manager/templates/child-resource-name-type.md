---
title: Risorse figlio nei modelli
description: Descrive come impostare il nome e il tipo per le risorse figlio in un modello di Azure Resource Manager.Describes how to set the name and type for child resources in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 6de2b476fe19a057a62e4a54963dd8fde0d11579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77207894"
---
# <a name="set-name-and-type-for-child-resources"></a>Impostare il nome e il tipo per le risorse figlioSet name and type for child resources

Le risorse figlio sono risorse che esistono solo nel contesto di un'altra risorsa. Ad esempio, [un'estensione](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) di macchina virtuale non può esistere senza una [macchina virtuale](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). La risorsa di estensione è un elemento figlio della macchina virtuale.

In un modello gestione risorse è possibile specificare la risorsa figlio all'interno della risorsa padre o all'esterno della risorsa padre. Nell'esempio seguente viene illustrata la risorsa figlio inclusa nella proprietà resources della risorsa padre.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

L'esempio seguente mostra la risorsa figlio all'esterno della risorsa padre. È possibile usare questo approccio se la risorsa padre non è distribuita nello stesso modello o se si vuole usare [la copia](copy-resources.md) per creare più di una risorsa figlio.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

I valori specificati per il nome e il tipo di risorsa variano a seconda che la risorsa figlio sia definita all'interno o all'esterno della risorsa padre.

## <a name="within-parent-resource"></a>All'interno della risorsa padre

Quando vengono definiti all'interno del tipo di risorsa padre, è possibile formattare i valori di tipo e nome come una singola parola senza barre.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Nell'esempio seguente viene illustrata una rete virtuale e con una subnet. Si noti che la subnet è inclusa all'interno della matrice di risorse per la rete virtuale. Il nome è impostato su **Subnet1** e il tipo è impostato su **subnets**. La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Il tipo di risorsa completa è ancora **Microsoft.Network/virtualNetworks/subnets**. Non si fornisce **Microsoft.Network/virtualNetworks/** perché viene considerato dal tipo di risorsa padre.

Il nome della risorsa figlio è impostato su **Subnet1,** ma il nome completo include il nome padre. Non si fornisce **VNet1** perché viene considerato dalla risorsa padre.

## <a name="outside-parent-resource"></a>Risorsa padre esterna

Quando viene definito all'esterno della risorsa padre, si formatta il tipo e con le barre per includere il tipo e il nome padre.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Nell'esempio seguente vengono illustrate una rete virtuale e una subnet entrambe definite a livello di radice. Si noti che la subnet non è inclusa nell'array di risorse per la rete virtuale. Il nome è impostato su **VNet1/Subnet1** e il tipo è impostato su **Microsoft.Network/virtualNetworks/subnets**. La risorsa figlio è contrassegnata come dipendente dalla risorsa padre perché la risorsa padre deve esistere prima che la risorsa figlio possa essere distribuita.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla creazione di modelli di Gestione risorse di Azure, vedere [Creazione di modelli](template-syntax.md).

* Per informazioni sul formato del nome della risorsa quando si fa riferimento alla risorsa, vedere la funzione di [riferimento](template-functions-resource.md#reference).

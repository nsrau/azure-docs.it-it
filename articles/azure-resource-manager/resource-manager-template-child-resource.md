---
title: Definire una risorsa figlio in un modello di Azure | Microsoft Docs
description: Illustra come impostare il nome e il tipo di una risorsa figlio in un modello di Azure Resource Manager
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 5b6ce5526f354008eb4a697deec737876f22391f
ms.contentlocale: it-it
ms.lasthandoff: 06/01/2017

---
# <a name="set-name-and-type-for-child-resource-in-resource-manager-template"></a>Impostare il nome e il tipo di una risorsa figlio in un modello di Resource Manager
Quando si crea un modello, è spesso necessario includere una risorsa figlio correlata a una risorsa padre. Il modello, ad esempio, potrebbe includere un'istanza di SQL Server e un database. L'istanza di SQL Server è la risorsa padre e il database è la risorsa figlio. 

Il formato del tipo della risorsa figlio è: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Il formato del nome della risorsa figlio è: `{parent-resource-name}/{child-resource-name}`

Il tipo e il nome vengono tuttavia specificati in modo diverso in un modello a seconda che la risorsa sia annidata nella risorsa padre oppure di primo livello. Questo argomento illustra come gestire entrambi gli approcci.

Quando si crea un riferimento completo a una risorsa, l'ordine di combinazione dei segmenti dal tipo e dal nome non è semplicemente una concatenazione dei due elementi.  Dopo lo spazio dei nomi, usare invece una sequenza di coppie *tipo/nome* dal meno specifico al più specifico:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

ad esempio:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` è corretto `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` non è corretto

## <a name="nested-child-resource"></a>Risorsa figlio annidata
Il modo più semplice per definire una risorsa figlio consiste nell'annidarla nella risorsa padre. L'esempio seguente illustra un database SQL annidato in un'istanza di SQL Server.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

Per la risorsa figlio, il tipo è impostato su `databases`, ma il tipo di risorsa completo è `Microsoft.Sql/servers/databases`. Non si specifica `Microsoft.Sql/servers/` perché viene ottenuto dal tipo della risorsa padre. Il nome della risorsa figlio è impostato su `exampledatabase`, ma il nome completo include il nome della risorsa padre. Non si specifica `exampleserver` perché viene ottenuto dalla risorsa padre.

## <a name="top-level-child-resource"></a>Risorsa figlio di primo livello
È possibile definire la risorsa figlio al primo livello. Questo approccio potrebbe essere usato se la risorsa padre non viene distribuita nello stesso modello o se si vuole usare `copy` per creare più risorse figlio. Con questo approccio, è necessario specificare il tipo di risorsa completo e includere il nome della risorsa padre nel nome della risorsa figlio.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

Il database è una risorsa figlio del server anche se sono definiti allo stesso livello nel modello.

## <a name="next-steps"></a>Passaggi successivi
* Per altri suggerimenti su come creare i modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](resource-manager-template-best-practices.md).
* Per un esempio della creazione di più risorse figlio, vedere [Distribuire più istanze delle risorse nei modelli di Azure Resource Manager](resource-group-create-multiple.md).


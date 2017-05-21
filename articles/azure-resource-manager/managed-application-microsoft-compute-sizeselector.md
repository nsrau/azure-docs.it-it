---
title: Elemento SizeSelector dell&quot;interfaccia utente dell&quot;applicazione gestita di Azure | Microsoft Docs
description: Illustra l&quot;elemento Microsoft.Compute.SizeSelector dell&quot;interfaccia utente per le applicazioni gestite di Azure
services: azure-resource-manager
documentationcenter: na
author: tabrezm
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: tabrezm;tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 076210146550510549d388eed981c439941f4fc9
ms.contentlocale: it-it
ms.lasthandoff: 05/11/2017


---
# <a name="microsoftcomputesizeselector-ui-element"></a>Elemento Microsoft.Compute.SizeSelector dell'interfaccia utente
Controllo per la selezione di una dimensione per una o più istanze di macchina virtuale.

## <a name="ui-sample"></a>Esempio di interfaccia utente
![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft.compute.sizeselector.png)

## <a name="schema"></a>Schema
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": []
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="remarks"></a>Osservazioni
- `recommendedSizes` deve contenere almeno una dimensione. La prima dimensione consigliata viene usata come impostazione predefinita.
- Se una dimensione consigliata non è disponibile nella località selezionata, viene ignorata automaticamente e viene usata la dimensione consigliata successiva.
- Le dimensioni non specificate in `constraints.allowedSizes` vengono nascoste e quelle non specificate in `constraints.excludedSizes` vengono visualizzate.
`constraints.allowedSizes` e `constraints.excludedSizes` sono entrambi facoltativi, ma non possono essere usati contemporaneamente. Per determinare l'elenco delle dimensioni disponibili è possibile chiamare l'[elenco di dimensioni di macchina virtuale disponibili per una sottoscrizione](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region).
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**. Viene usato per determinare i costi hardware delle macchine virtuali.
- `imageReference` viene omesso per le immagini proprietarie e viene specificato per le immagini di terze parti. Viene usato per determinare i costi software delle macchine virtuali.
- `count` viene usato per impostare il moltiplicatore appropriato per l'elemento. Supporta un valore statico, ad esempio **2**, o un valore dinamico da un altro elemento, ad esempio `[steps('step1').vmCount]`. Il valore predefinito è **1**.

## <a name="sample-output"></a>Output di esempio
```json
"Standard_D1"
```

## <a name="next-steps"></a>Passaggi successivi
* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](managed-application-createuidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](managed-application-createuidefinition-elements.md).


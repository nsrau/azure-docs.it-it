---
title: Elemento EditableGrid dell'interfaccia utente
description: Descrive l'elemento Microsoft. Common. EditableGrid dell'interfaccia utente per portale di Azure. Consente agli utenti di raccogliere input tabulari.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88893772"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>Elemento Microsoft. Common. EditableGrid dell'interfaccia utente

Controllo per la raccolta di input tabulari. Tutti i campi all'interno della griglia sono modificabili e il numero di righe può variare.

## <a name="ui-sample"></a>Esempio di interfaccia utente

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="Microsoft. Common. EditableGrid":::

## <a name="schema"></a>SCHEMA

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>Output di esempio

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>Commenti

- Gli unici controlli validi all'interno della matrice Columns sono la [casella di testo](microsoft-common-textbox.md), [OptionsGroup](microsoft-common-optionsgroup.md)e l'elenco a [discesa](microsoft-common-dropdown.md).
- La `$rowIndex` variabile è valida solo nelle espressioni contenute all'interno di elementi figlio delle colonne della griglia. Si tratta di un numero intero che rappresenta l'indice di riga relativo dell'elemento e il conteggio inizia da uno e viene incrementato di uno. Come illustrato nella sezione dello schema `"columns":` , `$rowIndex` viene usato per la convalida.
- Quando le convalide vengono eseguite utilizzando la `$rowIndex` variabile, è possibile ottenere il valore della riga corrente combinando i `last()` comandi e `take()` .

  Ad esempio:

  `last(take(<reference_to_grid>, $rowIndex))`

- La `label` proprietà non viene visualizzata come parte del controllo ma viene visualizzata nel riepilogo della scheda finale.
- La `ariaLabel` proprietà è l'etichetta di accessibilità per la griglia. Specificare un testo utile per gli utenti che utilizzano le utilità per la lettura dello schermo.
- La `constraints.width` proprietà viene utilizzata per impostare la larghezza complessiva della griglia. Le opzioni sono _full_, _medium_, _small_. Il valore predefinito è _full_.
- La `width` proprietà per gli elementi figlio delle colonne determina la larghezza della colonna. Le larghezze vengono specificate usando unità frazionarie, ad esempio _3fr_, con spazio totale assegnato alle colonne proporzionali alle rispettive unità. Se non viene specificata una larghezza di colonna, il valore predefinito è _1FR_.

## <a name="next-steps"></a>Passaggi successivi

- Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
- Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).

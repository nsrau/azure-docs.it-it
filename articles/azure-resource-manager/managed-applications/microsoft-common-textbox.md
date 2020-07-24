---
title: Elemento TextBox dell'interfaccia utente
description: Illustra l'elemento Microsoft.Common.TextBox dell'interfaccia utente per il portale di Azure. Usare per aggiungere testo non formattato.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 547b3ed84c8e4406b65ee8cf51c0db10b6878793
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063838"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento Microsoft.Common.TextBox dell'interfaccia utente

Controllo che è possibile usare per modificare il testo non formattato.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft-common-textbox.png)

## <a name="schema"></a>SCHEMA

```json
{
    "name": "nameInstance",
    "type": "Microsoft.Common.TextBox",
    "label": "Name",
    "defaultValue": "contoso123",
    "toolTip": "Use only allowed characters",
    "constraints": {
        "required": true,
        "validations": [
            {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
            },
            {
                "isValid": "[startsWith(steps('resourceConfig').nameInstance, 'contoso')]",
                "message": "Must start with 'contoso'."
            }
        ]
    },
    "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"contoso123"
```

## <a name="remarks"></a>Osservazioni

- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo la casella di testo deve avere un valore. Il valore predefinito è **false**.
- La `validations` proprietà è una matrice in cui si aggiungono le condizioni per verificare il valore fornito nella casella di testo.
- La `regex` proprietà è un modello di espressione regolare JavaScript. Se specificato, perché la convalida venga abbia esito positivo il valore della casella di testo deve corrispondere al modello. Il valore predefinito è **null**.
- La `isValid` proprietà contiene un'espressione che restituisce true o false. All'interno dell'espressione si definisce la condizione che determina se la casella di testo è valida.
- La `message` proprietà è una stringa da visualizzare quando il valore della casella di testo non riesce a eseguire la convalida.
- È possibile specificare un valore per `regex` quando `required` è impostato su **false**. In questo scenario non è richiesto un valore perché la convalida della casella di testo abbia esito positivo. Se viene specificato, deve corrispondere al modello di espressione regolare.

## <a name="example"></a>Esempio

Nell'esempio seguente viene usata una casella di testo con il controllo [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md) per verificare la disponibilità di un nome di risorsa.

```json
"basics": [
    {
        "name": "nameApi",
        "type": "Microsoft.Solutions.ArmApiControl",
        "request": {
            "method": "POST",
            "path": "[concat(subscription().id, '/providers/Microsoft.Storage/checkNameAvailability?api-version=2019-06-01')]",
            "body": "[parse(concat('{\"name\": \"', basics('txtStorageName'), '\", \"type\": \"Microsoft.Storage/storageAccounts\"}'))]"
        }
    },
    {
        "name": "txtStorageName",
        "type": "Microsoft.Common.TextBox",
        "label": "Storage account name",
        "constraints": {
            "validations": [
                {
                    "isValid": "[not(equals(basics('nameApi').nameAvailable, false))]",
                    "message": "[concat('Name unavailable: ', basics('txtStorageName'))]"
                }
            ]
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).

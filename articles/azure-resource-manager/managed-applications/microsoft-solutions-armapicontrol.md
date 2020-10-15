---
title: Elemento ArmApiControl dell'interfaccia utente
description: Descrive l'elemento Microsoft. Solutions. ArmApiControl dell'interfaccia utente per portale di Azure. Usato per chiamare le operazioni dell'API.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: bbe36e072d10b81c421331b2212d8b161afd2693
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87098157"
---
# <a name="microsoftcommonarmapicontrol-ui-element"></a>Elemento Microsoft. Common. ArmApiControl dell'interfaccia utente

ArmApiControl consente di ottenere risultati da un'operazione API Azure Resource Manager. Usare i risultati per popolare il contenuto dinamico in altri controlli.

## <a name="ui-sample"></a>Esempio di interfaccia utente

Non è disponibile alcuna interfaccia utente per questo controllo.

## <a name="schema"></a>SCHEMA

Nell'esempio seguente viene illustrato lo schema per il controllo:

```json
{
    "name": "testApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "{HTTP-method}",
        "path": "{path-for-the-URL}",
        "body": {
            "key1": "val1",
            "key2": "val2"
        }
    }
}
```

## <a name="sample-output"></a>Output di esempio

L'output del controllo non viene visualizzato all'utente. Il risultato dell'operazione viene invece utilizzato in altri controlli.

## <a name="remarks"></a>Commenti

- La `request.method` proprietà specifica il metodo HTTP. `GET` `POST` Sono consentiti solo o.
- La `request.path` proprietà specifica il percorso relativo dell'URL. Può trattarsi di un percorso statico oppure può essere costruito in modo dinamico facendo riferimento ai valori di output degli altri controlli.
- La proprietà `request.body` è facoltativa. Usarlo per specificare un corpo JSON che viene inviato con la richiesta. Il corpo può essere contenuto statico o costruito in modo dinamico facendo riferimento ai valori di output di altri controlli.

## <a name="example"></a>Esempio

Nell'esempio seguente l' `providersApi` elemento chiama un'API per ottenere una matrice di oggetti provider.

La `allowedValues` proprietà dell' `providersDropDown` elemento è configurata in modo da ottenere i nomi dei provider. Vengono visualizzati nell'elenco a discesa.

```json
{
    "name": "providersApi",
    "type": "Microsoft.Solutions.ArmApiControl",
    "request": {
        "method": "GET",
        "path": "[concat(subscription().id, '/providers/Microsoft.Network/expressRouteServiceProviders?api-version=2019-02-01')]"
    }
},
{
    "name": "providerDropDown",
    "type": "Microsoft.Common.DropDown",
    "label": "Provider",
    "toolTip": "The provider that offers the express route connection.",
    "constraints": {
        "allowedValues": "[map(steps('settings').providersApi.value, (item) => parse(concat('{\"label\":\"', item.name, '\",\"value\":\"', item.name, '\"}')))]",
        "required": true
    },
    "visible": true
}
```

Per un esempio dell'uso di ArmApiControl per verificare la disponibilità di un nome di risorsa, vedere [Microsoft. Common. TextBox](microsoft-common-textbox.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).

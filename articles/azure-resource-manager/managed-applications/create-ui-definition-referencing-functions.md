---
title: Creare funzioni di riferimento per la definizione dell'interfaccia utente
description: Descrive le funzioni da usare quando si creano definizioni dell'interfaccia utente per portale di Azure che fanno riferimento ad altri oggetti.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098177"
---
# <a name="createuidefinition-referencing-functions"></a>Funzioni di riferimento CreateUiDefinition

Funzioni da usare quando si fa riferimento a output dalle proprietà o dal contesto di un file CreateUiDefinition.

## <a name="basics"></a>basics

Restituisce i valori di output di un elemento definito nel passaggio di [base](create-uidefinition-overview.md#basics) . Passare il nome dell'elemento come parametro a questa funzione.

Per ottenere i valori di output degli elementi in altri passaggi, usare la funzione [Steps ()](#steps) .

L'esempio seguente restituisce l'output dell'elemento denominato `clusterName` nel passaggio basics:

```json
"[basics('clusterName')]"
```

I valori restituiti variano in base al tipo di elemento recuperato.

## <a name="location"></a>posizione

Restituisce il percorso selezionato nel passaggio basics o nel contesto corrente.

Nell'esempio seguente viene restituito un valore come `"westus"` :

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

Restituisce i dettagli relativi al resourceGroup selezionato nel passaggio di base o nel contesto corrente.

L'esempio seguente:

```json
"[resourceGroup()]"
```

Restituisce le proprietà seguenti:

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

È possibile ottenere qualsiasi valore specifico con la notazione del punto.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>steps

Restituisce gli elementi in un passaggio specificato. Passare il nome del passaggio come parametro a questa funzione. Dagli elementi restituiti è possibile ottenere valori di proprietà specifici.

Per ottenere i valori di output degli elementi nel passaggio di base, usare la funzione [nozioni di base ()](#basics) .

Nell'esempio seguente viene restituito il passaggio denominato `vmParameters` . In questo passaggio è un elemento denominato `adminUsername` .

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>sottoscrizione

Restituisce le proprietà per la sottoscrizione selezionata nel passaggio di base o nel contesto corrente.

L'esempio seguente:

```json
"[subscription()]"
```

Restituisce le proprietà seguenti:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione allo sviluppo dell'interfaccia del portale, vedere [CreateUiDefinition.json per l'esperienza di creazione di un'applicazione gestita di Azure](create-uidefinition-overview.md).

---
title: Output del modello di Azure Resource Manager | Microsoft Docs
description: Descrive come definire gli output dei modelli di Azure Resource Manager con la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2019
ms.author: tomfitz
ms.openlocfilehash: aadc92c232d32d827644caa52b3c362d9c8d4c9b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55691032"
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Sezione outputs nei modelli di Azure Resource Manager

Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. Ad esempio, è possibile restituire l'URI per accedere a una risorsa distribuita.

## <a name="define-and-use-output-values"></a>Definire e usare i valori di output

L'esempio seguente illustra come restituire l'ID risorsa per un indirizzo IP pubblico:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Dopo la distribuzione, è possibile recuperare il valore con uno script. Per PowerShell, usare:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

È possibile recuperare il valore di output da un modello collegato usando la funzione [reference](resource-group-template-functions-resource.md#reference). Per ottenere un valore di output da un modello collegato, recuperare il valore della proprietà con una sintassi analoga a: `"[reference('deploymentName').outputs.propertyName.value]"`.

Quando si ottiene una proprietà di output da un modello collegato, il nome della proprietà non può includere un trattino.

Ad esempio, si può impostare l'indirizzo IP in un dispositivo di bilanciamento del carico recuperando il valore da un modello collegato.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Non è possibile usare la funzione `reference` nella sezione outputs di un [modello annidato](resource-group-linked-templates.md#link-or-nest-a-template). Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

## <a name="available-properties"></a>Proprietà disponibili

L'esempio seguente illustra la struttura di una definizione di output:

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| Nome dell'elemento | Obbligatoria | DESCRIZIONE |
|:--- |:--- |:--- |
| outputName |Sì |Nome del valore di output. Deve essere un identificatore JavaScript valido. |
| type |Sì |Tipo del valore di output. I valori di output supportano gli stessi tipi dei parametri di input del modello. |
| value |Sì |Espressione del linguaggio di modello valutata e restituita come valore di output. |

Per informazioni sull'aggiunta di commenti, vedere [Commenti nei modelli](resource-group-authoring-templates.md#comments).

## <a name="example-templates"></a>Modelli di esempio

|Modello  |DESCRIZIONE  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variabili complesse e restituisce i valori. Non distribuisce alcuna risorsa. |
|[Indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea un indirizzo IP pubblico e restituisce l'ID risorsa. |
|[Bilanciamento del carico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | È collegato al modello precedente. Usa l'ID risorsa nell'output durante la creazione del dispositivo di bilanciamento del carico. |


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per altri suggerimenti sulla creazione di modelli, vedere [Procedure consigliate per la creazione di modelli di Azure Resource Manager](template-best-practices.md).

---
title: Output nei modelli
description: Viene descritto come definire i valori di output in un modello di Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460025"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Output nel modello di Azure Resource Manager

Questo articolo descrive come definire i valori di output nel modello di Azure Resource Manager. Gli output vengono usati quando è necessario restituire valori dalle risorse distribuite.

## <a name="define-output-values"></a>Definire i valori di output

L'esempio seguente illustra come restituire l'ID risorsa per un indirizzo IP pubblico:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Output condizionale

Nella sezione output è possibile restituire un valore in modo condizionale. In genere, la condizione viene usata negli output quando si [distribuisce](conditional-resource-deployment.md) una risorsa in modo condizionale. Nell'esempio seguente viene illustrato come restituire in modo condizionale l'ID risorsa per un indirizzo IP pubblico a seconda che sia stato distribuito un nuovo:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Per un esempio semplice di output condizionale, vedere [modello di output condizionale](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Numero dinamico di output

In alcuni scenari non si conosce il numero di istanze di un valore che è necessario restituire quando si crea il modello. È possibile restituire un numero variabile di valori usando l'elemento **Copy** .

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Per ulteriori informazioni, vedere l' [iterazione degli output nei modelli Azure Resource Manager](copy-outputs.md).

## <a name="linked-templates"></a>Modelli collegati

Per recuperare il valore di output da un modello collegato, usare la funzione [Reference](template-functions-resource.md#reference) nel modello padre. La sintassi nel modello padre è la seguente:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Quando si ottiene una proprietà di output da un modello collegato, il nome della proprietà non può includere un trattino.

Nell'esempio seguente viene illustrato come impostare l'indirizzo IP su un servizio di bilanciamento del carico recuperando un valore da un modello collegato.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Non è possibile usare la funzione `reference` nella sezione outputs di un [modello annidato](linked-templates.md#nested-template). Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

## <a name="get-output-values"></a>Ottenere i valori di output

Quando la distribuzione ha esito positivo, i valori di output vengono restituiti automaticamente nei risultati della distribuzione.

Per ottenere i valori di output dalla cronologia di distribuzione, è possibile usare lo script.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Modelli di esempio

Gli esempi seguenti illustrano gli scenari per l'uso degli output.

|Modello  |Descrizione  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variabili complesse e restituisce i valori. Non distribuisce alcuna risorsa. |
|[Indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea un indirizzo IP pubblico e restituisce l'ID risorsa. |
|[Bilanciamento del carico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | È collegato al modello precedente. Usa l'ID risorsa nell'output durante la creazione del dispositivo di bilanciamento del carico. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per gli output, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).

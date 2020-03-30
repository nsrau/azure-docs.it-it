---
title: Output nei modelli
description: Viene descritto come definire i valori di output in un modello di Azure Resource Manager.Describes how to define output values in an Azure Resource Manager template.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460025"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Output nel modello di Azure Resource ManagerOutputs in Azure Resource Manager template

Questo articolo descrive come definire i valori di output nel modello di Azure Resource Manager.This article describes how to define output values in your Azure Resource Manager template. Gli output vengono utilizzati quando è necessario restituire valori dalle risorse distribuite.

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

Nella sezione output è possibile restituire in modo condizionale un valore. In genere, si usa la condizione negli output quando è stata distribuita in [modo condizionale](conditional-resource-deployment.md) una risorsa. L'esempio seguente mostra come restituire in modo condizionale l'ID di risorsa per un indirizzo IP pubblico a seconda che ne sia stato distribuito uno nuovo:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Per un semplice esempio di output condizionale, vedere modello di [output condizionale](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Numero dinamico di uscite

In alcuni scenari, non si conosce il numero di istanze di un valore che è necessario restituire durante la creazione del modello. È possibile restituire un numero variabile di valori utilizzando l'elemento **copy.**

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

Per altre informazioni, vedere [Output dell'iterazione nei modelli](copy-outputs.md)di Azure Resource Manager.For more information, see Outputs iteration in Azure Resource Manager templates.

## <a name="linked-templates"></a>Modelli collegati

Per recuperare il valore di output da un modello collegato, utilizzare la funzione di [riferimento](template-functions-resource.md#reference) nel modello padre. La sintassi nel modello padre è:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Quando si ottiene una proprietà di output da un modello collegato, il nome della proprietà non può includere un trattino.

Nell'esempio seguente viene illustrato come impostare l'indirizzo IP in un servizio di bilanciamento del carico recuperando un valore da un modello collegato.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Non è possibile usare la funzione `reference` nella sezione outputs di un [modello annidato](linked-templates.md#nested-template). Per restituire i valori per una risorsa distribuita in un modello annidato, convertire il modello annidato in un modello collegato.

## <a name="get-output-values"></a>Ottenere i valori di outputGet output values

Quando la distribuzione ha esito positivo, i valori di output vengono restituiti automaticamente nei risultati della distribuzione.

Per ottenere i valori di output dalla cronologia di distribuzione, è possibile usare lo script.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Modelli di esempio

Negli esempi seguenti vengono illustrati scenari per l'utilizzo degli output.

|Modello  |Descrizione  |
|---------|---------|
|[Copia variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variabili complesse e restituisce i valori. Non distribuisce alcuna risorsa. |
|[Indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea un indirizzo IP pubblico e restituisce l'ID risorsa. |
|[Servizio di bilanciamento del carico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | È collegato al modello precedente. Usa l'ID risorsa nell'output durante la creazione del dispositivo di bilanciamento del carico. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle proprietà disponibili per gli output, vedere Informazioni sulla struttura e la [sintassi dei modelli di Azure Resource Manager.](template-syntax.md)

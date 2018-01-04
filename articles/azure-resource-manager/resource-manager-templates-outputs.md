---
title: Genera modello di gestione risorse di Azure | Documenti Microsoft
description: Viene descritto come definire gli output di un modelli di gestione risorse di Azure utilizzando la sintassi dichiarativa JSON.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2017
ms.author: tomfitz
ms.openlocfilehash: 485a3eb5c5d04d1540482245d088c48645704465
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="outputs-section-in-azure-resource-manager-templates"></a>Sezione di output nei modelli di gestione risorse di Azure
Nella sezione dell'output è possibile specificare i valori restituiti dalla distribuzione. Ad esempio, è possibile restituire l'URI per accedere a una risorsa distribuita.

## <a name="define-and-use-output-values"></a>Definire e utilizzare i valori di output

Nell'esempio seguente viene illustrato come restituire l'ID di risorsa per un indirizzo IP pubblico:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Dopo la distribuzione, è possibile recuperare il valore con script. Per PowerShell, usare:

```powershell
(Get-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Per l'interfaccia della riga di comando di Azure usare:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

È possibile recuperare il valore di output da un modello collegato tramite il [riferimento](resource-group-template-functions-resource.md#reference) (funzione). Per ottenere un valore di output da un modello collegato, recuperare il valore della proprietà con una sintassi analoga a: `"[reference('<name-of-deployment>').outputs.<property-name>.value]"`.

Ad esempio, è possibile impostare l'indirizzo IP in un bilanciamento del carico per il recupero di un valore da un modello collegato.

```json
"publicIPAddress": {
    "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

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

## <a name="recommendations"></a>Raccomandazioni

Se si utilizza un modello per creare gli indirizzi IP pubblici, includere una sezione di output che restituisce i dettagli dell'indirizzo IP e il nome di dominio completo (FQDN). Questi valori di output consentiranno di recuperare facilmente i dettagli sugli indirizzi IP pubblici e sugli FQDN dopo la distribuzione.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="example-templates"></a>Modelli di esempio


|Modello  |DESCRIZIONE  |
|---------|---------|
|[Copiare le variabili](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea le variabili complesse e restituisce i valori. Non distribuire le risorse. |
|[Indirizzo IP pubblico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea un indirizzo IP pubblico e restituisce l'ID di risorsa. |
|[Bilanciamento del carico](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Collegamenti al modello precedente. Utilizza l'ID di risorsa nell'output di quando si crea il bilanciamento del carico. |


## <a name="next-steps"></a>Passaggi successivi
* Per visualizzare modelli completi per molti tipi diversi di soluzioni, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/).
* Per informazioni dettagliate sulle funzioni che è possibile usare in un modello, vedere [Funzioni del modello di Azure Resource Manager](resource-group-template-functions.md).
* Per unire più modelli durante la distribuzione, vedere [Uso di modelli collegati con Azure Resource Manager](resource-group-linked-templates.md).
* Potrebbe essere necessario usare le risorse esistenti all'interno di un gruppo di risorse diverso. Questo scenario è comune quando si usano account di archiviazione o reti virtuali condivisi tra più gruppi di risorse. Per altre informazioni, vedere la [funzione resourceId](resource-group-template-functions-resource.md#resourceid).
---
title: Applicare tag alle risorse Azure per l&quot;organizzazione logica | Documentazione Microsoft
description: Mostra come applicare i tag per organizzare le risorse Azure per la fatturazione e la gestione.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ec80273fd388a435bc0aee9cb2dd49df12535923
ms.openlocfilehash: c5270c56ab6d0a4f200b0707554705d3b7855d32


---
# <a name="use-tags-to-organize-your-azure-resources"></a>Usare tag per organizzare le risorse di Azure
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> È possibile applicare dei tag solo alle risorse che supportano le operazioni di gestione delle risorse. Se è stata creata una macchina virtuale, una rete virtuale o un'archiviazione tramite il modello di distribuzione classica, ad esempio tramite il portale di Azure classico, non è possibile applicare un tag a tale risorsa. Per supportare l'assegnazione di tag, distribuire nuovamente tali risorse tramite Gestione risorse. Tutte le altre risorse supportano l'assegnazione di tag.
> 
> 

## <a name="templates"></a>Modelli

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>di Microsoft Azure
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20-preview"></a>Interfaccia della riga di comando di Azure 2.0 (anteprima)

Con l'interfaccia della riga di comando di Azure 2.0 (anteprima), è possibile aggiungere tag alle risorse e ai gruppo di risorse ed eseguire query sulle risorse in base ai valori dei tag.

Ogni volta che si applicano tag a una risorsa o a un gruppo di risorse, si sovrascrivono i tag esistenti in tale risorsa o gruppo di risorse. È quindi necessario usare un approccio diverso se nella risorsa o nel gruppo di risorse esistono tag che si vuole mantenere o meno. Per aggiungere a:

* un gruppo di risorse senza tag esistenti.

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* una risorsa senza tag esistenti.

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

Per aggiungere tag a una risorsa che già dispone di tag, per prima cosa recuperare i tag esistenti: 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

Viene restituito il formato seguente:

```
Dept        : Finance
Environment : Test
```

Riapplicare i tag esistenti alla risorsa e aggiungere i nuovi tag.

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

Per ottenere i gruppi di risorse con un tag specifico, usare `az group list`.

```azurecli
az group list --tag Dept=IT
```

Per ottenere tutte le risorse con un tag e un valore specifici, usare `az resource list`.

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>API REST
Sia il portale che PowerShell usano l' [API REST di Gestione risorse](https://docs.microsoft.com/rest/api/resources/) dietro le quinte. Se è necessario integrare l'assegnazione di tag in un altro ambiente, è possibile ottenere i tag con un'operazione GET sull'ID di risorsa e aggiornare il set di tag con una chiamata PATCH.

## <a name="tags-and-billing"></a>Tag e fatturazione
I tag consentono di raggruppare i dati di fatturazione. Ad esempio, se si eseguono più macchine virtuali per organizzazioni diverse, usare i tag per raggruppare l'utilizzo in base al centro di costo. È inoltre possibile utilizzare i tag per classificare i costi in base all'ambiente di runtime; ad esempio, l'utilizzo di fatturazione per le macchine virtuali in esecuzione nell'ambiente di produzione.


Le informazioni sui tag possono essere recuperate tramite l' [API di utilizzo della risorsa di Azure e della Rate Card](../billing/billing-usage-rate-card-overview.md) o il file di utilizzo con valori delimitati da virgole (CSV). Il file di utilizzo può essere scaricato dal [portale degli account di Azure](https://account.windowsazure.com/) o dal [portale EA](https://ea.azure.com). Per altre informazioni sull'accesso a livello di codice alle informazioni sulla fatturazione, vedere [Ottenere informazioni dettagliate sul consumo delle risorse di Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Per le operazioni API REST, vedere [Riferimento API REST alla fatturazione di Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).


Quando si scarica il CSV di utilizzo per i servizi che supportano i tag di fatturazione, i tag vengono visualizzati nella colonna **Tag** . Per altre informazioni, vedere [Comprendere la fattura per Microsoft Azure](../billing/billing-understand-your-bill.md).

![Vedere i tag della fatturazione](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Passaggi successivi
* È possibile applicare restrizioni e convenzioni all’interno della sottoscrizione con criteri personalizzati. Il criterio definito potrebbe richiedere che per tutte le risorse sia impostato un determinato tag. Per altre informazioni, vedere [Usare i criteri per gestire le risorse e controllare l'accesso](resource-manager-policy.md).
* Per un'introduzione all'uso di Azure PowerShell durante la distribuzione delle risorse, vedere [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).
* Per un'introduzione all'uso dell'interfaccia della riga di comando di Azure durante la distribuzione delle risorse, vedere [Uso dell'interfaccia della riga di comando di Azure per Mac, Linux e Windows con Gestione risorse di Azure](xplat-cli-azure-resource-manager.md).
* Per un'introduzione all'uso del portale, vedere [Uso del portale di Azure per gestire le risorse di Azure](resource-group-portal.md)  
* Per indicazioni su come le aziende possono usare Resource Manager per gestire efficacemente le sottoscrizioni, vedere [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Scaffolding aziendale Azure - Governance prescrittiva per le sottoscrizioni).




<!--HONumber=Feb17_HO2-->



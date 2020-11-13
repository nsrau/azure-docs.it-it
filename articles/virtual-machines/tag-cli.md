---
title: Come assegnare un tag a una macchina virtuale di Azure tramite l'interfaccia della riga di comando
description: Informazioni sull'assegnazione di tag a una macchina virtuale tramite l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595026"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Come contrassegnare una macchina virtuale usando l'interfaccia della riga di comando

Questo articolo descrive come assegnare un tag a una macchina virtuale usando l'interfaccia della riga di comando di Azure. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 50 tag per risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. È anche possibile contrassegnare una macchina virtuale con Azure [PowerShell](tag-powershell.md).


È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, inclusi i tag, usando `az vm show` .

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Per aggiungere un nuovo tag della macchina virtuale tramite l'interfaccia della riga di comando di Azure, è possibile usare il `azure vm update` comando insieme al parametro tag `--set` :

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Per rimuovere i tag, è possibile usare il `--remove` parametro nel `azure vm update` comando.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Dopo aver applicato i tag alle risorse tramite l'interfaccia della riga di comando di Azure e il portale, esaminare i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.


**Passaggi successivi**

- Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md) e [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).
- Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).

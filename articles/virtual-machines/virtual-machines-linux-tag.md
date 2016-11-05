---
title: Come assegnare un tag a una macchina virtuale Linux | Microsoft Docs
description: Informazioni sull’assegnazione di tag a una macchina virtuale Linux creata in Azure con il modello di distribuzione di Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror

---
# Come contrassegnare una macchina virtuale Linux in Azure
Questo articolo descrive diversi modi per contrassegnare una macchina virtuale Linux in Azure tramite il modello di distribuzione Resource Manager. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Attualmente, Azure supporta fino a 15 tag per ogni risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite il modello di distribuzione Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Assegnazione di tag con Azure CLI
Per iniziare, [installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-azure-resource-manager.md) e assicurarsi di trovarsi in modalità Resource Manager (`azure config mode arm`).

È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, compresi i tag, utilizzando questo comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Per aggiungere un nuovo tag della macchina virtuale tramite la CLI di Azure, è possibile utilizzare il comando`azure vm set` insieme al parametro tag**-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Per rimuovere tutti i tag, è possibile usare il parametro **–T** nel comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Dopo aver applicato i tag alle risorse tramite l'interfaccia della riga di comando di Azure e il portale, esaminare i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.

[!INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Passaggi successivi
* Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager][Panoramica di Azure Resource Manager] e [Uso dei tag per organizzare le risorse di Azure][Uso dei tag per organizzare le risorse di Azure].
* Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure][Comprendere la fattura per Microsoft Azure] e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure].

[ambiente CLI di Azure]: ./xplat-cli-azure-resource-manager.md
[Panoramica di Azure Resource Manager]: ../resource-group-overview.md
[Uso dei tag per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Comprendere la fattura per Microsoft Azure]: ../billing-understand-your-bill.md
[Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->
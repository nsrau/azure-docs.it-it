<properties
   pageTitle="Come assegnare un tag a una macchina virtuale Linux | Microsoft Azure"
   description="Informazioni sull’assegnazione di tag a una macchina virtuale Linux creata in Azure con il modello di distribuzione di Resource Manager."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="dkshir;memccror"/>

# Come contrassegnare una macchina virtuale Linux in Azure

In questo articolo vengono descritti diversi modi per contrassegnare una macchina virtuale Linux in Azure tramite Azure Resource Manager. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Attualmente, Azure supporta fino a 15 tag per ogni risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite la gestione risorse di Azure. Se si desidera assegnare un tag a una macchina virtuale Windows, vedere l'articolo relativo a [come assegnare un tag a una macchina virtuale Windows in Azure](virtual-machines-windows-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Assegnazione di tag con Azure CLI

L’assegnazione di tag è supportata per le risorse che sono già state create tramite la CLI di Azure. Per iniziare, impostare l’[ambiente CLI di Azure][]. Accedere alla sottoscrizione tramite la CLI di Azure e passare alla modalità ARM. È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, compresi i tag, utilizzando questo comando:

        azure vm show -g MyResourceGroup -n MyVM

A differenza di PowerShell, se si sta aggiungendo tag a una risorsa che già contiene tag, non occorre specificare tutti i tag (vecchi e nuovi) prima di usare il comando `azure vm set`. Questo comando consente invece di aggiungere un tag per la risorsa. Per aggiungere un nuovo tag della macchina virtuale tramite la CLI di Azure, è possibile utilizzare il comando`azure vm set` insieme al parametro tag**-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Per rimuovere tutti i tag, è possibile usare il parametro **–T** nel comando `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Ora che sono stati applicati tag alle risorse tramite PowerShell, la CLI di Azure e il portale, esaminiamo i dettagli di utilizzo per visualizzare i tag nel portale di fatturazione.




## Passaggi successivi

* Per ulteriori informazioni sull'uso dei tag delle risorse di Azure, vedere [Panoramica su Azure Resource Manager][] e [Uso dei tag per organizzare le risorse di Azure][].
* Per informazioni su come i tag contribuiscono alla gestione delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure][] e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][].





[ambiente CLI di Azure]: ./xplat-cli-azure-resource-manager.md
[Panoramica su Azure Resource Manager]: ../resource-group-overview.md
[Uso dei tag per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Comprendere la fattura per Microsoft Azure]: ../billing-understand-your-bill.md
[Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0330_2016-->
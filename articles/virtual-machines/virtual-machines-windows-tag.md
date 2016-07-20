<properties
   pageTitle="Come assegnare i tag a una VM | Microsoft Azure"
   description="Informazioni sull’assegnazione di tag a una macchina virtuale Windows creata in Azure con il modello di distribuzione di Resource Manager"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# Come assegnare un tag a una macchina virtuale Windows in Azure


Questo articolo descrive diversi modi per contrassegnare una macchina virtuale Windows in Azure tramite il modello di distribuzione Resource Manager. I tag sono coppie chiave/valore definite dall'utente che possono essere inserite direttamente in una risorsa o un gruppo di risorse. Attualmente, Azure supporta fino a 15 tag per ogni risorsa e gruppo di risorse. I tag possono essere posizionati su una risorsa al momento della creazione o aggiunti a una risorsa esistente. Si noti che i tag sono supportati solo per le risorse create tramite il modello di distribuzione Resource Manager. Se si desidera assegnare un tag a una macchina virtuale Linux, vedere l'articolo relativo a [come assegnare un tag a una macchina virtuale Linux in Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Assegnazione di tag tramite PowerShell

Per creare, aggiungere ed eliminare i tag tramite PowerShell, è prima necessario configurare l’[ambiente PowerShell con Gestione risorse di Azure][]. Dopo aver completato l'installazione, è possibile inserire tag su risorse di calcolo, rete e archiviazione al momento della creazione o dopo la creazione della risorsa tramite PowerShell. Questo articolo si concentrerà su come visualizzare o modificare tag inseriti nelle macchine virtuali.

Per prima cosa, spostarsi su una macchina virtuale tramite il`Get-AzureRmVM`cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se la macchina virtuale contiene già dei tag, verranno visualizzati tutti i tag per la risorsa:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se si desidera aggiungere i tag tramite PowerShell, è possibile utilizzare il comando `Set-AzureRmResource`. Nota: Quando si aggiornano i tag tramite PowerShell, i tag vengono aggiornati nel loro complesso. Se si aggiunge un tag a una risorsa che già dispone di tag, sarà pertanto necessario includere tutti i tag che si desidera inserire nella risorsa. Di seguito è riportato un esempio di come aggiungere ulteriori tag a una risorsa tramite Cmdlets di PowerShell.

Questo primo cmdlet imposta tutti i tag inseriti in *MyTestVM* per la variabile *tag*, usando la funzione `Get-AzureRmResource` e `Tags`.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Il secondo comando consente di visualizzare i tag per la variabile specificata.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

Il terzo comando aggiunge un altro tag alla variabile *tags*. Si noti l'uso di **+=** per aggiungere la nuova coppia chiave/valore all'elenco *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

Il quarto comando imposta tutti i tag definiti nella variabile *tags* sulla risorsa specificata. In questo caso, è MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Il quinto comando visualizza tutti i tag sulla risorsa. Come si può vedere, *Percorso* è ora definito come un tag con *Ilmiopercorso* come valore.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Per altre informazioni sull'assegnazione di tag tramite PowerShell, consultare i [cmdlet di Gestione risorse di Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Passaggi successivi

* Per altre informazioni sull'uso dei tag nelle risorse di Azure, vedere [Panoramica di Azure Resource Manager][] e [Uso dei tag per organizzare le risorse di Azure][].
* Per informazioni sull'utilità dei tag nella gestione dell'uso delle risorse di Azure, vedere [Comprendere la fattura per Microsoft Azure][] e [Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure][].

[ambiente PowerShell con Gestione risorse di Azure]: ../powershell-azure-resource-manager.md
[cmdlet di Gestione risorse di Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Panoramica di Azure Resource Manager]: ../resource-group-overview.md
[Uso dei tag per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Comprendere la fattura per Microsoft Azure]: ../billing-understand-your-bill.md
[Ottenere informazioni dettagliate sul consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->
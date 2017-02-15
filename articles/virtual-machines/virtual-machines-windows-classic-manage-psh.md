---
title: Gestire le macchine virtuali tramite Azure PowerShell | Microsoft Docs
description: "Informazioni su comandi che è possibile utilizzare per automatizzare le attività di gestione delle macchine virtuali."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 5b178da3f36bee8dbd48c988af452575328447fe


---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gestire le macchine virtuali con Azure PowerShell
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per i comandi comuni di PowerShell con il modello di Resource Manager, vedere [qui](virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Molte attività che è possibile eseguire ogni giorno per gestire le macchine virtuali possono essere automatizzate utilizzando i cmdlet di Azure PowerShell. In questo articolo offre esempi di comandi per le attività più semplici e collegamenti ad articoli in cui visualizzare i comandi per attività più complesse.

> [!NOTE]
> Se non è ancora stato installato e configurato Azure PowerShell, è possibile ottenere le istruzioni nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Come utilizzare i comandi di esempio
È necessario sostituire una parte del testo nei comandi con il testo appropriato per l'ambiente. I simboli < e > indicano il testo da sostituire. Quando si sostituisce il testo, rimuovere i simboli ma mantenere le virgolette.

## <a name="get-a-vm"></a>Ottenere una macchina virtuale
Si tratta di un'attività di base che si utilizzerà spesso. È possibile utilizzarla per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o recuperare l'output da archiviare in una variabile.

Per ottenere informazioni sulla macchina virtuale, eseguire questo comando sostituendo tutto ciò che è racchiuso tra virgolette, inclusi i caratteri < e >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Per archiviare l'output in una variabile $vm, eseguire:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Accedere a una macchina virtuale basata su Windows
Eseguire i comandi seguenti.

> [!NOTE]
> È possibile ottenere il nome del servizio delle macchine virtuali e cloud dalla visualizzazione della **Get-AzureVM** comando.
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<posizione dell'unità e della cartella in cui archiviare il file con estensione RDP scaricato, ad esempio: c:\temp >" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>Arrestare una macchina virtuale
Eseguire questo comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Utilizzare questo parametro per mantenere l'IP virtuale (VIP) del servizio cloud, qualora fosse l'ultima macchina virtuale inclusa nel servizio cloud specifico. <br><br> Se si utilizza il parametro StayProvisioned, sarà ancora configurato per la macchina virtuale.
> 
> 

## <a name="start-a-vm"></a>Avviare una macchina virtuale
Eseguire questo comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Collegamento di un disco dati
Questa operazione richiede alcuni passaggi. Utilizzare innanzitutto la cmdlet****Aggiungi-DiscoDatiAzure**** per aggiungere il disco per l'oggetto $vm. Quindi è possibile utilizzare il cmdlet **Aggiorna-MacchinaVirtualeAzure** per aggiornare la configurazione della macchina virtuale.

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Per collegare un disco dati esistente, eseguire questo comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Per collegare dischi dati da un file con estensione vhd esistente nell'archiviazione blob, è necessario eseguire questo comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Creare una macchina virtuale basata su Windows
Per creare una nuova macchina virtuale basata su Windows in Azure, usare le istruzioni nell'argomento relativo all'[uso di Azure PowerShell per creare e preconfigurare le macchine virtuali basate su Windows](virtual-machines-windows-classic-create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Questo argomento illustra la creazione di un set di comandi di PowerShell che consente di creare una macchina virtuale di Windows che può essere preconfigurata con:

* Con l’appartenenza al dominio di Active Directory
* Con Dischi aggiuntivi
* Come membro di un set esistente con carico bilanciato
* Con un indirizzo IP statico




<!--HONumber=Dec16_HO2-->



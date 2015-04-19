<properties
 pageTitle="manage-vms-azure-powershell"
 description="Gestire le macchine virtuali con Azure PowerShell"
 services="virtual-machines"
 documentationCenter="windows"
 authors="singhkay"
 manager="timlt"
 editor=""/>

 <tags
 ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="02/20/2015"
 ms.author="kasing"/>

# Gestire le macchine virtuali con Azure PowerShell

Prima di iniziare è necessario assicurarsi che Azure PowerShell sia installato. A tale scopo, vedere [Come installare e configurare Azure PowerShell](install-configure-powershell.md)

## Ottenere un'immagine

Prima di creare una macchina virtuale, è necessario decidere **quale immagine utilizzare**. È possibile ottenere un elenco di immagini mediante il seguente cmdlet

 Get-AzureVMImage

Questo cmdlet restituirà l'elenco di tutte le immagini disponibili in Azure. Si tratta di un elenco molto lungo e può essere difficile trovare l'immagine esatta che si desidera utilizzare. Nell'esempio seguente si utilizzano altri cmdlet di PowerShell per limitare l'elenco delle immagini restituite solo a quelle con contenuti basati su **Windows Server 2012 R2 Datacenter**. Inoltre, si sceglie di ottenere solo l'immagine più recente specificando [-1] per la matrice di immagini restituita

 $img = (Get-AzureVMImage | Select -Property ImageName, Label | where {$_.Label -like '*Windows Server 2012 R2 Datacenter*'})[-1]

## Creare una macchina virtuale

La creazione di una macchina virtuale inizia con il cmdlet **New-AzureVMConfig**. È possibile specificare il **nome** della macchina virtuale, le **dimensioni** della macchina virtuale e l'**immagine** da utilizzare per la macchina virtuale. Questo cmdlet crea un oggetto macchina virtuale locale **$myVM** che viene modificato in un secondo momento mediante altri cmdlet PowerShell di Azure presenti in questa Guida.

 $myVM = New-AzureVMConfig -Name "testvm" -InstanceSize "Small" -ImageName $img.ImageName

Successivamente sarà necessario scegliere il **nome utente** e la **password** per la macchina virtuale. È possibile eseguire questa operazione utilizzando il cmdlet **Add-AzureProvisioningConfig**. Qui si indica ad Azure il sistema operativo della macchina virtuale. Si noti che si stanno ancora apportando modifiche all'oggetto **$myVM** locale.

 $user = "azureuser"
 $pass = "&Azure1^Pass@"
 $myVM = Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass

A questo punto è tutto pronto per utilizzare la macchina virtuale in Azure. A tale scopo è necessario utilizzare il cmdlet **New-AzureVM**

> [AZURE.NOTE] È necessario configurare il servizio cloud prima di creare la macchina virtuale. Per eseguire questa operazione è possibile procedere in due modi:
* Creare il servizio cloud utilizzando il cmdlet New-AzureService. Se si sceglie questo metodo, è necessario assicurarsi che il percorso specificato nel seguente cmdlet New-AzureVM corrisponda al percorso del servizio cloud, in caso contrario l'esecuzione del cmdlet New-AzureVM avrà esito negativo.
* Permettere al cmdlet New-AzureVM di eseguire automaticamente questa operazione. È necessario assicurarsi che il nome del servizio sia univoco, in caso contrario l'esecuzione del cmdlet New-AzureVM avrà esito negativo.

 New-AzureVM -ServiceName "mytestserv" -VMs $myVM -Location "West US"

**FACOLTATIVO**

È possibile utilizzare altri cmdlet, ad esempio **Add-AzureDataDisk** e **Add-AzureEndpoint**, per configurare opzioni aggiuntive per la macchina virtuale

## Ottenere una macchina virtuale
Ora che è stata creata una macchina virtuale in Azure, è certamente opportuno esaminare come si procede. A tale scopo, utilizzare il cmdlet **Get-AzureVM** come mostrato di seguito

 Get-AzureVM -ServiceName "mytestserv" -Name "testvm"


## Passaggi successivi
[Connettersi a una macchina virtuale di Azure con RDP o SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)<br>
[Domande frequenti sulle macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/dn683781.aspx)

<!--HONumber=47-->

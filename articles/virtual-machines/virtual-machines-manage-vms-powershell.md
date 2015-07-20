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
   ms.date="06/24/2015"
   ms.author="kasing"/>

# Gestire le macchine virtuali con Azure PowerShell

Molte attività che è possibile eseguire ogni giorno per gestire le macchine virtuali possono essere automatizzate utilizzando i cmdlet di Azure PowerShell. In questo articolo offre esempi di comandi per le attività più semplici e collegamenti ad articoli in cui visualizzare i comandi per attività più complesse.

>[AZURE.NOTE]Se non ancora stato installato e configurato Azure PowerShell, è possibile ottenere istruzioni [qui](../install-configure-powershell.md).

## Come utilizzare i comandi di esempio
È necessario sostituire una parte del testo nei comandi con il testo appropriato per l'ambiente. Il < and > simboli indicano è necessario sostituire il testo. Quando si sostituisce il testo, rimuovere i simboli ma mantenere le virgolette.

## Ottenere una macchina virtuale
Si tratta di un'attività di base che si utilizzerà spesso. È possibile utilizzarla per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o recuperare l'output da archiviare in una variabile.

Per ottenere informazioni sulla macchina virtuale, eseguire questo comando, sostituendo tutto ciò che è racchiuso tra virgolette, inclusi i caratteri < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Per archiviare l'output in una variabile $vm, eseguire:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Accedere a una macchina virtuale Windows

Eseguire i comandi seguenti.

>[AZURE.NOTE]È possibile ottenere il nome del servizio delle macchine virtuali e cloud dalla visualizzazione della **Get-AzureVM** comando.
>
	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## Arrestare una macchina virtuale

Eseguire questo comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilizzare questo parametro per mantenere l'IP virtuale (VIP) del servizio cloud, qualora fosse l'ultima macchina virtuale inclusa nel servizio cloud specifico. <br><br> Se si utilizza il parametro StayProvisioned, sarà ancora configurato per la macchina virtuale.

## Avviare una macchina virtuale

Eseguire questo comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Collegamento di un disco dati
Questa operazione richiede alcuni passaggi. Utilizzare innanzitutto la * * * Add-AzureDataDisk * * * cmdlet per aggiungere il disco per l'oggetto $vm, quindi si utilizza il cmdlet Update-AzureVM per aggiornare la configurazione della macchina virtuale.

È inoltre necessario decidere se collegare un nuovo disco o uno che contiene già dati. Per un nuovo disco, il comando permette di creare il file con estensione VHD e contemporaneamente di collegarlo.

Per collegare un nuovo disco, eseguire questo comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> `
              | Update-AzureVM

Per collegare un disco dati esistente, eseguire questo comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> `
              | Update-AzureVM

Per collegare dischi dati da un file con estensione vhd esistente nell'archiviazione blob, è necessario eseguire questo comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> `
              | Update-AzureVM

## Creare un'app Windows

Per creare una nuova macchina virtuale basata su Windows in Azure, utilizzare le istruzioni in [utilizzare Azure PowerShell per creare e preconfigurare le macchine virtuali basate su Windows](virtual-machines-ps-create-preconfigure-windows-vms.md). Passaggi in questo argomento è illustrata la creazione di un set di comandi di PowerShell consente di creare una macchina virtuale di Windows che può essere preconfigurata con:

- Appartenenza al dominio di Active Directory
- Dischi aggiuntivi
- Imposta un membro di un oggetto esistente con carico bilanciato
- Un indirizzo IP statico

<!---HONumber=July15_HO2-->
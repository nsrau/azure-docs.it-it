---
title: Gestire VM con Resource Manager e PowerShell | Microsoft Docs
description: Gestire macchine virtuali con Azure Resource Manager e PowerShell.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: 63e822de6ae50be33590048140e06e89526282ee


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Gestire macchine virtuali di Azure con Resource Manager di Azure e PowerShell
## <a name="install-azure-powershell"></a>Installare Azure PowerShell
Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="set-variables"></a>Impostare variabili
Tutti i comandi nell'articolo richiedono il nome del gruppo di risorse in cui si trova la macchina virtuale e il nome della macchina virtuale da gestire. Sostituire il valore di **$rgName** con il nome del gruppo di risorse che contiene la macchina virtuale. Sostituire il valore di **$vmName** con il nome della VM. Creare le variabili.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>Visualizzare informazioni relative a una macchina virtuale
Ottenere le informazioni relative alla macchina virtuale.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Verrà visualizzata una schermata simile a all'esempio seguente:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>Arrestare una macchina virtuale
Arrestare la macchina virtuale in esecuzione.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Verrà richiesta una conferma:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Immettere **Y** per arrestare la macchina virtuale.

Dopo qualche minuto viene visualizzata una schermata simile alla seguente:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>Avviare una macchina virtuale
Avviare la macchina virtuale, se arrestata.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Dopo qualche minuto viene visualizzata una schermata simile alla seguente:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

Se si desidera riavviare una macchina virtuale che è già in esecuzione, usare **Restart-AzureRmVM** come descritto di seguito.

## <a name="restart-a-virtual-machine"></a>Riavviare una macchina virtuale
Riavviare la macchina virtuale in esecuzione.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Verrà visualizzata una schermata simile a all'esempio seguente:

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>Eliminare una macchina virtuale
Eliminare la macchina virtuale.  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> È possibile utilizzare il parametro **-Force** per ignorare la richiesta di conferma.
> 
> 

Se non si usa il parametro -Force, verrà richiesta una conferma:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Verrà visualizzata una schermata simile a all'esempio seguente:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>Aggiornare una macchina virtuale
Questo esempio illustra come aggiornare le dimensioni della macchina virtuale.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Verrà visualizzata una schermata simile a all'esempio seguente:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Per un elenco di dimensioni disponibili per una macchina virtuale, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Aggiungere un disco dati a una macchina virtuale
Questo esempio illustra come aggiungere un disco dati a una macchina virtuale esistente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Il disco aggiunto non viene inizializzato. Per inizializzare il disco, è possibile accedere al disco e usare Gestione disco. Se è stata eseguita l'installazione di WinRM e di un certificato durante la creazione del disco, è possibile usare PowerShell remoto per inizializzare il disco. È anche possibile usare un'estensione di script personalizzata: 

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

Il file di script può contenere codice analogo al seguente per inizializzare i dischi:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi con la distribuzione, è consigliabile vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)




<!--HONumber=Dec16_HO2-->



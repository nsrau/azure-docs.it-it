---
title: L'avvio della VM si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer". in Azure | Microsoft Docs
description: Presentazione della procedura per risolvere il problema di blocco dell'avvio della macchina virtuale quando viene restituito un messaggio analogo a "Preparazione di Windows. Non spegnere il computer."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2bcdb2b458327a5e87dc36e4a5f50f0ac46bf96a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621035"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>L'avvio della VM si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer". in Azure

Questo articolo consente di risolvere il problema di blocco della macchina virtuale quando viene restituito un messaggio analogo a "Preparazione di Windows. Non spegnere il computer". in fase di avvio.

## <a name="symptoms"></a>Sintomi

Quando si usa **Diagnostica di avvio** per ottenere lo screenshot di una macchina virtuale, si riscontra che il sistema operativo non viene avviato completamente. Inoltre, la macchina virtuale visualizza un messaggio analogo a **"Preparazione di Windows. Non spegnere il computer."** criteri.).

![Esempio di messaggio](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Esempio di messaggio](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Questo problema si verifica in genere quando il server esegue il riavvio finale dopo che è stata modificata la configurazione. La modifica della configurazione può essere stata inizializzata da aggiornamenti di Windows o da modifiche apportate ai ruoli o alle funzionalità del server. Per Windows Update, se l'entità degli aggiornamenti è stata notevole, il sistema operativo avrà bisogno di più tempo per riconfigurare le modifiche.

## <a name="back-up-the-os-disk"></a>Eseguire il backup del disco del sistema operativo

Prima di provare a risolvere il problema, eseguire il backup del disco del sistema operativo:

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Per le macchine virtuali con un disco crittografato, sbloccare prima i dischi

Verificare se la macchina virtuale è crittografata. A questo scopo, seguire questa procedura:

1. Nel portale aprire la macchina virtuale e quindi selezionare i dischi.

2. Verrà visualizzata una colonna denominata "Crittografia" che indicherà se la crittografia è abilitata.

Se il disco del sistema operativo è crittografato, sbloccare il disco crittografato. A questo scopo, seguire questa procedura:

1. Creare una macchina virtuale di ripristino che si trova nello stesso gruppo di risorse, lo stesso account di archiviazione e la stessa posizione della macchina virtuale interessata.

2. Nel portale di Azure eliminare la macchina virtuale interessata e mantenere il disco.

3. Eseguire PowerShell come amministratore.

4. Eseguire il cmdlet seguente per ottenere il nome del segreto.

    ```Powershell
    Login-AzureRmAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Dopo aver ottenuto il nome del segreto, eseguire i comandi seguenti in PowerShell:

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Convertire in byte il valore con codifica Base64 e scrivere l'output in un file. 

    > [!Note]
    > Se si usa l'opzione di sblocco USB, il nome del file BEK deve corrispondere al GUID BEK originale. È inoltre necessario creare nell'unità C una cartella denominata BEK prima di eseguire i passaggi seguenti.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Dopo aver creato il file con estensione bek nel PC, copiare il file nella macchina virtuale di ripristino a cui è collegato il disco del sistema operativo bloccato. Eseguire il comando seguente usando il percorso del file con estensione bek:

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Facoltativo**: in alcuni scenari può anche essere necessario decrittografare il disco con questo comando.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Nel codice si presuppone che il disco da crittografare si trovi nell'unità F.

8. Se è necessario raccogliere i log, è possibile passare al percorso **LETTERA UNITÀ:\Windows\System32\winevt\Logs**.

9. Scollegare l'unità dalla macchina di ripristino.

### <a name="create-a-snapshot"></a>Creare uno snapshot

Per creare uno snapshot, seguire i passaggi descritti in [Creare uno snapshot di un disco](..\windows\snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Raccogliere un dump di memoria del sistema operativo

Seguire la procedura descritta nella sezione [Raccogliere un dump del sistema operativo](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) per raccogliere un dump del sistema operativo quando la macchina virtuale è bloccata in fase di configurazione.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Dopo aver raccolto il file di dump, contattare il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per analizzare la causa radice.


## <a name="rebuild-the-vm-using-powershell"></a>Ricreare la macchina virtuale usando PowerShell

Dopo aver raccolto il file di dump della memoria, seguire la procedura seguente per ricreare la macchina virtuale.

**Per i dischi non gestiti**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID “SubscriptionID” | Select-AzureRmSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzureRmVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzureRmNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzureRmVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzureRmVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Per i dischi gestiti**

```PowerShell
# To login to Azure Resource Manager
Login-AzureRmAccount

# To view all subscriptions for your account
Get-AzureRmSubscription

# To select a default subscription for your current session
Get-AzureRmSubscription –SubscriptionID "SubscriptionID" | Select-AzureRmSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure Portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzureRmVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzureRmVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```

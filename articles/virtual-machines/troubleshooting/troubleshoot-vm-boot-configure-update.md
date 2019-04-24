---
title: L'avvio della macchina virtuale si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer" in Azure | Microsoft Docs
description: Presentazione della procedura per risolvere il problema di blocco dell'avvio della macchina virtuale quando viene restituito un messaggio analogo a "Preparazione di Windows. Non spegnere il computer".
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
ms.openlocfilehash: c3592529d20680c6920e569887effee4ffe38344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443842"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>L'avvio della macchina virtuale si blocca su un messaggio analogo a "Preparazione di Windows. Non spegnere il computer" in Azure

Questo articolo consente di risolvere il problema di blocco della macchina virtuale quando viene restituito un messaggio analogo a "Preparazione di Windows. Non spegnere il computer" in fase di avvio.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Sintomi

Quando si usa **Diagnostica di avvio** per ottenere lo screenshot di una macchina virtuale, si riscontra che il sistema operativo non viene avviato completamente. Sulla macchina virtuale viene visualizzato il messaggio "Preparazione di Windows. Non spegnere il computer".

![Esempio di messaggio per Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Esempio di messaggio](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Questo problema si verifica in genere quando il server esegue il riavvio finale dopo che è stata modificata la configurazione. È possibile che la modifica della configurazione sia stata inizializzata da aggiornamenti di Windows o da modifiche apportate ai ruoli o alle funzionalità del server. Per Windows Update, se l'entità degli aggiornamenti è stata notevole, il sistema operativo ha bisogno di più tempo per riconfigurare le modifiche.

## <a name="back-up-the-os-disk"></a>Eseguire il backup del disco del sistema operativo

Prima di provare a risolvere il problema, eseguire il backup del disco del sistema operativo.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Per le macchine virtuali con un disco crittografato, sbloccare prima i dischi

Seguire questa procedura per determinare se la macchina virtuale è crittografata.

1. Nel portale di Azure aprire la macchina virtuale e quindi selezionare i dischi.

2. Verificare che la crittografia sia abilitata nella colonna **Crittografia**.

Se il disco del sistema operativo è crittografato, sbloccare il disco crittografato. Per sbloccare il disco, seguire questa procedura.

1. Creare una macchina virtuale di ripristino che si trova nello stesso gruppo di risorse, lo stesso account di archiviazione e la stessa posizione della macchina virtuale interessata.

2. Nel portale di Azure eliminare la macchina virtuale interessata e mantenere il disco.

3. Eseguire PowerShell come amministratore.

4. Eseguire il cmdlet seguente per ottenere il nome del segreto.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Dopo aver ottenuto il nome del segreto, eseguire i comandi seguenti in PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Convertire in byte il valore con codifica Base64 e scrivere l'output in un file. 

    > [!Note]
    > Se si usa l'opzione di sblocco USB, il nome del file BEK deve corrispondere al GUID BEK originale. Creare nell'unità C una cartella denominata BEK prima di eseguire la procedura seguente.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Dopo aver creato il file con estensione bek nel PC, copiare il file nella macchina virtuale di ripristino a cui è collegato il disco del sistema operativo bloccato. Eseguire i comandi seguenti usando il percorso del file BEK.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Facoltativo**: in alcuni scenari potrebbe essere necessario decrittografare il disco con questo comando.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > Il comando precedente presuppone che il disco da crittografare si trovi nell'unità F.

8. Se è necessario raccogliere i log, passare al percorso **LETTERA UNITÀ:\Windows\System32\winevt\Logs**.

9. Scollegare l'unità dalla macchina di ripristino.

### <a name="create-a-snapshot"></a>Creare uno snapshot

Per creare uno snapshot, seguire i passaggi descritti in [Creare uno snapshot di un disco](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Raccogliere un dump di memoria del sistema operativo

Seguire la procedura descritta nella sezione [Raccogliere un dump del sistema operativo](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) per raccogliere un dump del sistema operativo quando la macchina virtuale è bloccata in fase di configurazione.

## <a name="contact-microsoft-support"></a>Contattare il supporto Microsoft

Dopo aver raccolto il file di dump, contattare il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per analizzare la causa radice.


## <a name="rebuild-the-vm-by-using-powershell"></a>Ricreare la macchina virtuale usando PowerShell

Dopo aver raccolto il file di dump della memoria, seguire la procedura seguente per ricreare la macchina virtuale.

**Per i dischi non gestiti**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Per i dischi gestiti**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

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

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```

---
title: Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 3f028431fcd4b338d2e610ce1828a02b753c4d32
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483705"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure

When you troubleshoot issues on an Azure virtual machine (VM), you can connect to the VM by using the remote tools that are discussed in this article instead of using the Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Console seriale

Use a [serial console for Azure Virtual Machines](serial-console-windows.md) to run commands on the remote Azure VM.

## <a name="remote-cmd"></a>CMD remoto

Scaricare [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Connettersi alla macchina virtuale eseguendo il comando seguente:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* The command must be run on a computer that's in the same virtual network.
>* DIP or HostName can be used to replace \<computer>.
>* Il parametro -s assicura che il comando venga richiamato mediante l'account di sistema (autorizzazioni di amministratore).
>* PsExec usa le porte TCP 135 e 445. As a result, the two ports have to be open on the firewall.

## <a name="run-command"></a>Eseguire un comando

For more information about how to use the run command feature to run scripts on the VM, see [Run PowerShell scripts in your Windows VM with run command](../windows/run-command.md).

## <a name="custom-script-extension"></a>Estensione di script personalizzati

È possibile usare l'estensione script personalizzata per eseguire uno script personalizzato sulla macchina virtuale di destinazione. Per usare questa funzionalità devono essere soddisfatte le condizioni seguenti:

* La macchina virtuale deve disporre di connettività.
* Azure Virtual Machine Agent is installed and is working as expected on the VM.
* The extension wasn't previously installed on the VM.
 
  The extension injects the script only the first time that it's used. If you use this feature later, the extension recognizes that it was already used and doesn't upload the new script.

Upload your script to a storage account, and generate its own container. Eseguire quindi lo script seguente in Azure PowerShell su un computer che disponga di connettività alla macchina virtuale.

### <a name="for-classic-deployment-model-vms"></a>For classic deployment model VMs

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>For Azure Resource Manager VMs

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!NOTE]
>La porta TCP 5986 (HTTPS) deve essere aperta per poter usare questa opzione.
>
>For Azure Resource Manager VMs, you must open port 5986 on the network security group (NSG). Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con una porta privata (5986) e una porta pubblica. Then, you also have to open that public-facing port on the NSG.

### <a name="set-up-the-client-computer"></a>Configurare il computer client

Per usare PowerShell per connettersi in remoto alla macchina virtuale, occorre prima configurare il computer client in modo che consenta la connessione. A questo scopo, aggiungere la macchina virtuale all'elenco degli host attendibili di PowerShell eseguendo il comando seguente nel modo appropriato.

To add one VM to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

To add multiple VMs to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Per aggiungere tutti i computer all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Abilitare RemotePS sulla macchina virtuale

For VMs created using the classic deployment model, use the Custom Script Extension to run the following script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

For Azure Resource Manager VMs, use run commands from the portal to run the EnableRemotePS script:

![Eseguire un comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Run the following command based on the client computer location:

* Outside the virtual network or deployment

  * For a VM created using the classic deployment model, run the following command:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * For an Azure Resource Manager VM, first add a DNS name to the public IP address. Per i passaggi dettagliati, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../windows/portal-create-fqdn.md). Quindi, eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Inside the virtual network or deployment, run the following command:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Impostando il flag SkipCaCheck è possibile ignorare il requisito di importazione di un certificato nella macchina virtuale all'avvio della sessione.

You can also use the Invoke-Command cmdlet to run a script on the VM remotely.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro di sistema remoto

>[!NOTE]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open the registry editor (regedit.exe).

2. Selezionare **File** > **Connetti a Registro di sistema in rete**.

   ![Registry editor](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Locate the target VM by **host name** or **dynamic IP** (preferable) by entering it in the **Enter the object name to select** box.

   ![Enter the object name to select box](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Immettere le credenziali della macchina virtuale di destinazione.

5. Apportare le modifiche al Registro di sistema, se necessario.

## <a name="remote-services-console"></a>Remote services console

>[!NOTE]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>For Azure Resource Manager VMs, you have to open port 5986 on the NSG. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. You also have to open that public-facing port on the NSG.

1. From another VM on the same virtual network, open an instance of **Services.msc**.

2. Fare clic con il pulsante destro del mouse su **Servizi (computer locale)** .

3. Selezionare **Connetti a un altro computer**.

   ![Servizio remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Enter the dynamic IP of the target VM.

   ![Input dynamic IP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Apportare le modifiche necessarie ai servizi.

## <a name="next-steps"></a>Passaggi successivi

- For more information about the Enter-PSSession cmdlet, see [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- For more information about the Custom Script Extension for Windows using the classic deployment model, see [Custom Script Extension for Windows](../extensions/custom-script-classic.md).
- PsExec fa parte della [suite PSTools](https://download.sysinternals.com/files/PSTools.zip).
- For more information about the PSTools Suite, see [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).



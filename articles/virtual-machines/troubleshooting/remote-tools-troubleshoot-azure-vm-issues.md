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
ms.openlocfilehash: b86b1a2d8a49554cc3df99e0a32a2c0ccaacb560
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920009"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure

Quando si risolvono i problemi in una macchina virtuale di Azure, è possibile connettersi alla macchina virtuale usando gli strumenti remoti descritti in questo articolo anziché usare il protocollo RdP (Remote Desktop Protocol).

## <a name="serial-console"></a>Console seriale

Usare una [console seriale per macchine virtuali](serial-console-windows.md) di Azure per eseguire comandi nella macchina virtuale di Azure remota.

## <a name="remote-cmd"></a>CMD remoto

Scaricare [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Connettersi alla macchina virtuale eseguendo il comando seguente:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Il comando deve essere eseguito in un computer che si trovare nella stessa rete virtuale.
>* DIP o HostName può \<essere utilizzato per sostituire i> del computer.
>* Il parametro -s assicura che il comando venga richiamato mediante l'account di sistema (autorizzazioni di amministratore).
>* PsExec usa le porte TCP 135 e 445. Di conseguenza, le due porte devono essere aperte sul firewall.

## <a name="run-command"></a>Eseguire un comando

Per altre informazioni su come usare la funzionalità di comando di esecuzione per eseguire script nella macchina virtuale, vedere [Eseguire script di PowerShell nella macchina virtuale Windows con](../windows/run-command.md)il comando run .

## <a name="custom-script-extension"></a>Estensione di script personalizzati

È possibile usare l'estensione script personalizzata per eseguire uno script personalizzato sulla macchina virtuale di destinazione. Per usare questa funzionalità devono essere soddisfatte le condizioni seguenti:

* La macchina virtuale deve disporre di connettività.
* Azure Virtual Machine Agent è installato e funziona come previsto nella macchina virtuale.
* L'estensione non è stata installata in precedenza nella macchina virtuale.
 
  L'estensione inserisce lo script solo la prima volta che viene utilizzato. Se si utilizza questa funzionalità in un secondo momento, l'estensione riconosce che è già stata utilizzata e non carica il nuovo script.

Caricare lo script in un account di archiviazione e generare il proprio contenitore. Eseguire quindi lo script seguente in Azure PowerShell su un computer che disponga di connettività alla macchina virtuale.

### <a name="for-classic-deployment-model-vms"></a>Per le macchine virtuali del modello di distribuzione classicaFor classic deployment model VMs

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


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

### <a name="for-azure-resource-manager-vms"></a>Per le macchine virtuali di Azure Resource ManagerFor Azure Resource Manager VMs

 

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
>Per le macchine virtuali di Azure Resource Manager, è necessario aprire la porta 5986 nel gruppo di sicurezza di rete .NSG. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con una porta privata (5986) e una porta pubblica. Quindi, è anche necessario aprire tale porta pubblica nel gruppo di sicurezza di rete.

### <a name="set-up-the-client-computer"></a>Configurare il computer client

Per usare PowerShell per connettersi in remoto alla macchina virtuale, occorre prima configurare il computer client in modo che consenta la connessione. A questo scopo, aggiungere la macchina virtuale all'elenco degli host attendibili di PowerShell eseguendo il comando seguente nel modo appropriato.

Per aggiungere una macchina virtuale all'elenco degli host attendibili:To add one VM to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Per aggiungere più macchine virtuali all'elenco degli host attendibili:To add multiple VMs to the trusted hosts list:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Per aggiungere tutti i computer all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Abilitare RemotePS sulla macchina virtuale

Per le macchine virtuali create usando il modello di distribuzione classica, usare l'estensione script personalizzato per eseguire lo script seguente:For VMs created using the classic deployment model, use the Custom Script Extension to run the following script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Per le macchine virtuali di Azure Resource Manager, usare eseguire comandi dal portale per eseguire lo script EnableRemotePS:For Azure Resource Manager VMs, use run commands from the portal to run the EnableRemotePS script:

![Eseguire un comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Eseguire il comando seguente in base al percorso del computer client:

* All'esterno della rete virtuale o della distribuzione

  * Per una macchina virtuale creata usando il modello di distribuzione classica, eseguire il comando seguente:For a VM created using the classic deployment model, run the following command:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Per una macchina virtuale di Azure Resource Manager, aggiungere innanzitutto un nome DNS all'indirizzo IP pubblico. Per i passaggi dettagliati, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../windows/portal-create-fqdn.md). Quindi, eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* All'interno della rete virtuale o della distribuzione, eseguire il comando seguente:Inside the virtual network or deployment, run the following command:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>Impostando il flag SkipCaCheck è possibile ignorare il requisito di importazione di un certificato nella macchina virtuale all'avvio della sessione.

È anche possibile usare il cmdlet Invoke-Command per eseguire uno script nella macchina virtuale in modalità remota.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro di sistema remoto

>[!NOTE]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>Per le macchine virtuali di Azure Resource Manager, è necessario aprire la porta 5986 nel gruppo di sicurezza di rete. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. È inoltre necessario aprire tale porta pubblica nel gruppo di sicurezza di rete.

1. Da un'altra macchina virtuale nella stessa rete virtuale, aprire l'editor del Registro di sistema (regedit.exe).

2. Selezionare **Registro** > di sistema**di rete per la connessione**a i file .

   ![Editor del Registro di sistema](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Individuare la macchina virtuale di destinazione in base al **nome host** o all'IP **dinamico** (preferibile) immettendola nella casella **Immettere il nome dell'oggetto da selezionare.**

   ![Immettere il nome dell'oggetto da selezionare](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Immettere le credenziali della macchina virtuale di destinazione.

5. Apportare le modifiche al Registro di sistema, se necessario.

## <a name="remote-services-console"></a>Console Servizi remoti

>[!NOTE]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>Per le macchine virtuali di Azure Resource Manager, è necessario aprire la porta 5986 nel gruppo di sicurezza di rete. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. È inoltre necessario aprire tale porta pubblica nel gruppo di sicurezza di rete.

1. Da un'altra macchina virtuale nella stessa rete virtuale aprire un'istanza di **Services.msc**.

2. Fare clic con il pulsante destro del mouse su **Servizi (computer locale)**.

3. Selezionare **Connetti a un altro computer**.

   ![Servizio remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Immettere l'IP dinamico della macchina virtuale di destinazione.

   ![Input IP dinamico](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Apportare le modifiche necessarie ai servizi.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sul cmdlet Enter-PSSession, vedere [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Per ulteriori informazioni sull'estensione script personalizzato per Windows utilizzando il modello di distribuzione classica, vedere [Estensione script personalizzato per Windows](../extensions/custom-script-classic.md).
- PsExec fa parte della [suite PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Per ulteriori informazioni su PSTools Suite, consultate [PSTools.](https://docs.microsoft.com/sysinternals/downloads/pstools)



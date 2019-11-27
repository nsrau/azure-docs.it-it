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

Quando si risolvono i problemi in una macchina virtuale (VM) di Azure, è possibile connettersi alla VM usando gli strumenti remoti descritti in questo articolo anziché usare il Remote Desktop Protocol (RDP).

## <a name="serial-console"></a>Console seriale

Usare una [console seriale per le macchine virtuali di Azure](serial-console-windows.md) per eseguire i comandi nella VM di Azure remota.

## <a name="remote-cmd"></a>CMD remoto

Scaricare [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Connettersi alla macchina virtuale eseguendo il comando seguente:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* Il comando deve essere eseguito in un computer che si trova nella stessa rete virtuale.
>* È possibile utilizzare DIP o HostName per sostituire \<> del computer.
>* Il parametro -s assicura che il comando venga richiamato mediante l'account di sistema (autorizzazioni di amministratore).
>* PsExec usa le porte TCP 135 e 445. Di conseguenza, le due porte devono essere aperte sul firewall.

## <a name="run-command"></a>Eseguire un comando

Per altre informazioni su come usare la funzionalità Esegui comando per eseguire gli script nella macchina virtuale, vedere [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](../windows/run-command.md).

## <a name="custom-script-extension"></a>Estensione di script personalizzati

È possibile usare l'estensione script personalizzata per eseguire uno script personalizzato sulla macchina virtuale di destinazione. Per usare questa funzionalità devono essere soddisfatte le condizioni seguenti:

* La macchina virtuale deve disporre di connettività.
* L'agente di macchine virtuali di Azure è installato e funziona come previsto nella VM.
* L'estensione non è stata installata in precedenza nella macchina virtuale.
 
  L'estensione inserisce lo script solo la prima volta che viene usato. Se si usa questa funzionalità in un secondo momento, l'estensione riconosce che è già in uso e non carica il nuovo script.

Caricare lo script in un account di archiviazione e generare il proprio contenitore. Eseguire quindi lo script seguente in Azure PowerShell su un computer che disponga di connettività alla macchina virtuale.

### <a name="for-classic-deployment-model-vms"></a>Per le macchine virtuali con modello di distribuzione classica

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

### <a name="for-azure-resource-manager-vms"></a>Per macchine virtuali Azure Resource Manager

 

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
>Per Azure Resource Manager macchine virtuali, è necessario aprire la porta 5986 nel gruppo di sicurezza di rete (NSG). Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con una porta privata (5986) e una porta pubblica. Quindi, è necessario aprire anche la porta pubblica sul NSG.

### <a name="set-up-the-client-computer"></a>Configurare il computer client

Per usare PowerShell per connettersi in remoto alla macchina virtuale, occorre prima configurare il computer client in modo che consenta la connessione. A questo scopo, aggiungere la macchina virtuale all'elenco degli host attendibili di PowerShell eseguendo il comando seguente nel modo appropriato.

Per aggiungere una macchina virtuale all'elenco di host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Per aggiungere più macchine virtuali all'elenco di host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Per aggiungere tutti i computer all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Abilitare RemotePS sulla macchina virtuale

Per le macchine virtuali create con il modello di distribuzione classica, usare l'estensione dello script personalizzata per eseguire lo script seguente:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Per Azure Resource Manager macchine virtuali, usare i comandi Run dal portale per eseguire lo script EnableRemotePS:

![Eseguire un comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Eseguire il comando seguente in base alla posizione del computer client:

* All'esterno della rete virtuale o della distribuzione

  * Per una macchina virtuale creata con il modello di distribuzione classica, eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Per una macchina virtuale Azure Resource Manager, aggiungere prima di tutto un nome DNS all'indirizzo IP pubblico. Per i passaggi dettagliati, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../windows/portal-create-fqdn.md). Quindi, eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* All'interno della rete virtuale o della distribuzione, eseguire il comando seguente:
  
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
>Per Azure Resource Manager macchine virtuali, è necessario aprire la porta 5986 nella NSG. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. È anche necessario aprire la porta pubblica sul NSG.

1. Da un'altra VM nella stessa rete virtuale, aprire l'editor del registro di sistema (Regedit. exe).

2. Selezionare **File** > **Connetti a Registro di sistema in rete**.

   ![Editor del registro di sistema](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Individuare la macchina virtuale di destinazione in base al **nome host** o all' **indirizzo IP dinamico** (preferibile) inserendola nella casella **immettere il nome dell'oggetto da selezionare** .

   ![Immettere il nome dell'oggetto da selezionare box](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Immettere le credenziali della macchina virtuale di destinazione.

5. Apportare le modifiche al Registro di sistema, se necessario.

## <a name="remote-services-console"></a>Console dei servizi remoti

>[!NOTE]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>Per Azure Resource Manager macchine virtuali, è necessario aprire la porta 5986 nella NSG. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. È anche necessario aprire la porta pubblica sul NSG.

1. Da un'altra VM nella stessa rete virtuale, aprire un'istanza di **Services. msc**.

2. Fare clic con il pulsante destro del mouse su **Servizi (computer locale)** .

3. Selezionare **Connetti a un altro computer**.

   ![Servizio remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Immettere l'indirizzo IP dinamico della macchina virtuale di destinazione.

   ![IP dinamico di input](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Apportare le modifiche necessarie ai servizi.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul cmdlet Enter-PSSession, vedere [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Per altre informazioni sull'estensione script personalizzata per Windows con il modello di distribuzione classica, vedere [estensione script personalizzato per Windows](../extensions/custom-script-classic.md).
- PsExec fa parte della [suite PSTools](https://download.sysinternals.com/files/PSTools.zip).
- Per ulteriori informazioni su PSTools Suite, vedere [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools).



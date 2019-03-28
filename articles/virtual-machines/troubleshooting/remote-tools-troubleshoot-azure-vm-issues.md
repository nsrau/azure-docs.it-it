---
title: Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: dc27ea0552c6135d01256586b1746219caac17f1
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539871"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Usare gli strumenti remoti per risolvere i problemi delle macchine virtuali di Azure

Per eseguire una procedura di risoluzione dei problemi in una macchina virtuale di Azure, è possibile connettersi alla macchina virtuale usando gli strumenti remoti illustrati in questo articolo, invece di usare RDP (Remote Desktop Protocol).

## <a name="serial-console"></a>Console seriale

Usare la [console seriale per macchine virtuali](serial-console-windows.md) per eseguire comandi sulla macchina virtuale di Azure remota.

## <a name="remote-cmd"></a>CMD remoto

Scaricare [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Connettersi alla macchina virtuale eseguendo il comando seguente:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* Il comando deve essere eseguito in un computer che si trova nella stessa rete virtuale.
>* Nome host o un DIP può essere usato per sostituire \<computer >.
>* Il parametro -s assicura che il comando venga richiamato mediante l'account di sistema (autorizzazioni di amministratore).
>* PsExec usa le porte TCP 135 e 445. Le due porte devono quindi essere aperte sul firewall.

## <a name="run-commands"></a>Comandi di esecuzione

Per altre informazioni su come usare la funzionalità Comandi di esecuzione per eseguire script sulla macchina virtuale, vedere [Eseguire gli script di PowerShell nella macchina virtuale Linux con Esegui comando](../windows/run-command.md).

## <a name="customer-script-extension"></a>Estensione script personalizzata

È possibile usare l'estensione script personalizzata per eseguire uno script personalizzato sulla macchina virtuale di destinazione. Per usare questa funzionalità devono essere soddisfatte le condizioni seguenti:

* La macchina virtuale deve disporre di connettività.

* L'agente di Azure deve essere installato e funzionare nel modo previsto nella macchina virtuale.

* L'estensione non deve essere stata precedentemente installata nella macchina virtuale.
 
  L'estensione inserirà lo script solo al primo utilizzo. Se si usa questa funzionalità in un secondo momento, l'estensione riconoscerà che è già stata usata e non caricherà il nuovo script.

È necessario caricare lo script in un account di archiviazione e generare il relativo contenitore. Eseguire quindi lo script seguente in Azure PowerShell su un computer che disponga di connettività alla macchina virtuale.

### <a name="for-v1-vms"></a>Per le macchine virtuali V1

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Per le macchine virtuali V2

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!Note]
>La porta TCP 5986 (HTTPS) deve essere aperta per poter usare questa opzione.
>
>Per le macchine virtuali ARM è necessario aprire la porta 5986 nel gruppo di sicurezza di rete (NSG). Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con una porta privata (5986) e una porta pubblica. Occorre quindi aprire anche la porta pubblica nel gruppo di sicurezza di rete.

### <a name="set-up-the-client-computer"></a>Configurare il computer client

Per usare PowerShell per connettersi in remoto alla macchina virtuale, occorre prima configurare il computer client in modo che consenta la connessione. A questo scopo, aggiungere la macchina virtuale all'elenco degli host attendibili di PowerShell eseguendo il comando seguente nel modo appropriato.

Per aggiungere una sola macchina virtuale all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Per aggiungere più macchine virtuali all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Per aggiungere tutti i computer all'elenco degli host attendibili:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Abilitare RemotePS sulla macchina virtuale

Per le macchine virtuali classiche, usare l'estensione script personalizzata per eseguire lo script seguente:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Per le macchine virtuali ARM, usare la funzionalità Comandi di esecuzione sul portale per eseguire lo script EnableRemotePS:

![Eseguire un comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Connettersi alla VM

Eseguire il comando seguente, in bade alla posizione del computer client:

* All'esterno della rete virtuale o della distribuzione

  * Per una macchina virtuale classica eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Per una macchina virtuale ARM aggiungere prima un nome DNS all'indirizzo IP pubblico. Per i passaggi dettagliati, vedere [Creare un nome di dominio completo nel portale di Azure per una macchina virtuale Windows](../windows/portal-create-fqdn.md). Quindi, eseguire il comando seguente:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* All'interno della rete virtuale o della distribuzione, eseguire il comando seguente:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Impostando il flag SkipCaCheck è possibile ignorare il requisito di importazione di un certificato nella macchina virtuale all'avvio della sessione.

È anche possibile usare il cmdlet Invoke-Command per eseguire uno script sulla macchina virtuale in remoto:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro di sistema remoto

>[!Note]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>Per le macchine virtuali ARM è necessario aprire la porta 5986 nel gruppo di sicurezza di rete. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. Occorre aprire anche la porta pubblica nel gruppo di sicurezza di rete.

1. In un'altra macchina virtuale sulla stessa rete virtuale aprire l'Editor del Registro di sistema (regedit.exe).

2. Selezionare **File** >**Connetti a Registro di sistema in rete**.

   ![Opzione remota](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Individuare la macchina virtuale di destinazione in base al **nome host** o all'**IP dinamico** (preferibile) immettendolo nella casella "Immettere il nome dell'oggetto da selezionare".

   ![Opzione remota](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Immettere le credenziali della macchina virtuale di destinazione.

5. Apportare le modifiche al Registro di sistema, se necessario.

## <a name="remote-services-console"></a>Console di servizi remoti

>[!Note]
>La porta TCP 135 o 445 deve essere aperta per poter usare questa opzione.
>
>Per le macchine virtuali ARM è necessario aprire la porta 5986 nel gruppo di sicurezza di rete. Per altre informazioni, vedere Gruppi di sicurezza. 
>
>Per le macchine virtuali RDFE è necessario un endpoint con la porta privata 5986 e una porta pubblica. Occorre quindi aprire anche la porta pubblica nel gruppo di sicurezza di rete.

1. In un'altra macchina virtuale sulla stessa rete virtuale aprire un'istanza di **Services.msc**.

2. Fare clic con il pulsante destro del mouse su **Servizi (computer locale)**.

3. Selezionare **Connetti a un altro computer**.

   ![Servizio remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Immettere l'indirizzo IP dinamico della macchina virtuale di destinazione.

   ![Immettere l'IP dinamico](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Apportare le modifiche necessarie ai servizi.

## <a name="next-steps"></a>Fasi successive

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Estensione script personalizzata per Windows usando il modello di distribuzione classico](../extensions/custom-script-classic.md)

PsExec fa parte della [suite PSTools](https://download.sysinternals.com/files/PSTools.zip).

Per altre informazioni sulla suite PSTools, vedere [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).



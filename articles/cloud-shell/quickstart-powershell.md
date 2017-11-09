---
title: Guida introduttiva a PowerShell in Azure Cloud Shell (anteprima) | Documentazione Microsoft
description: Guida introduttiva a PowerShell in Cloud Shell
services: Azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: fd1d340bc0408eaeb0b7b18235df109224eae5f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Guida introduttiva a PowerShell in Azure Cloud Shell

Questo documento illustra dettagliatamente come usare PowerShell in Cloud Shell nel [portale di Azure](https://aka.ms/PSCloudPreview).

> [!NOTE]
> È disponibile anche una guida introduttiva a [Bash in Azure Cloud Shell](quickstart.md).

## <a name="start-cloud-shell"></a>Avviare Cloud Shell

1. Fare clic sul pulsante **Cloud Shell** dalla barra di navigazione in alto nel portale di Azure

  ![](media/quickstart-powershell/shell-icon.png)

2. Selezionare l'ambiente PowerShell dall'elenco a discesa per accedere all’unità Azure `(Azure:)`

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>Esecuzione dei comandi di PowerShell

Eseguire i comandi regolari di PowerShell in Cloud Shell, tra cui:

```Powershell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Esplora risorse di Azure

 1. Elenco delle sottoscrizioni

    ``` Powershell
    PS Azure:\> dir
    ```

 2. `cd` alla sottoscrizione preferita

    ``` Powershell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. Consente di visualizzare tutte le risorse di Azure nella sottoscrizione corrente
 
    Digitare `dir` per elencare più viste delle risorse Azure.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>Visualizzazione AllResources 
Digitare `dir` nella directory `AllResources` per visualizzare le risorse in Azure.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>Esplora gruppi di risorse

 È possibile accedere alla directory `ResourceGroups` e all'interno di un gruppo di risorse specifico è possibile trovare le macchine virtuali.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> È possibile notare che quando si digita `dir` per la seconda volta, Cloud Shell è in grado di mostrare gli elementi in modo molto più veloce.
> Questo avviene perché gli elementi figlio vengono spostati nella cache della memoria per migliorare l'esperienza dell’utente.
Tuttavia, è sempre possibile usare `dir -Force` per ottenere dati aggiornati.

### <a name="navigate-storage-resources"></a>Esplorare le risorse di archiviazione
    
Accedendo alla cartella `StorageAccounts` è possibile esplorare facilmente le risorse di archiviazione
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

Con la stringa di connessione, è possibile utilizzare il comando seguente per montare la condivisione file di Azure.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

Per maggiori dettagli, vedere [Montare una condivisione file di Azure e accedere alla condivisione in Windows][azmount].

È anche possibile esplorare le directory nella condivisione file di Azure, come indicato di seguito:

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>Interazione con macchine virtuali

È possibile trovare tutte le macchine virtuali della sottoscrizione corrente tramite la directory `VirtualMachines`.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>Richiamare uno script PowerShell tra le macchine virtuali remote

 > [!WARNING]
 > Consultare [Risoluzione dei problemi di gestione remota delle macchine virtuali di Azure](troubleshooting.md#powershell-resolutions).

  Se si dispone di una macchina virtuale, MyVM1, usare `Invoke-AzureRmVMCommand` per richiamare un blocco di script di PowerShell nel computer remoto.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  È anche possibile prima passare alla directory virtualMachines e poi eseguire `Invoke-AzureRmVMCommand` come indicato di seguito.

  ``` Powershell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  L'output sarà simile al seguente:

  ``` Powershell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>Accedere in modo interattivo a una macchina virtuale remota

È possibile utilizzare `Enter-AzureRmVM` per accedere in modo interattivo a una macchina virtuale in esecuzione in Azure.

  ``` Powershell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

È anche possibile prima passare alla directory `virtualMachines` e poi eseguire `Enter-AzureRmVM` come indicato di seguito.

  ``` Powershell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>Scoprire WebApp

Accedendo alla cartella `WebApps` è possibile esplorare facilmente le risorse di archiviazione

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps for example,
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -force
PS Azure:\MySubscriptionName\WebApps> dir -force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="list-available-commands"></a>Elenco dei comandi disponibili

Nell’unità `Azure`, digitare `Get-AzureRmCommand` per ottenere i comandi di Azure specifici per il contesto.

In alternativa, è sempre possibile usare `Get-Command *azurerm* -Module AzureRM.*` per scoprire i comandi di Azure disponibili.

## <a name="install-custom-modules"></a>Installazione di moduli personalizzati

È possibile eseguire `Install-Module` per installare moduli dalla [PowerShell Gallery][gallery].

## <a name="get-help"></a>Get-Help

Digitare `Get-Help` per ottenere informazioni su PowerShell in Azure Cloud Shell.

``` Powershell
PS Azure:\> Get-Help
```

Per un comando specifico, è comunque possibile eseguire Get-Help seguito da un cmdlet, ad esempio,

``` Powershell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-file-storage-to-store-your-data"></a>Usare l'archiviazione file di Azure per archiviare i dati

È possibile creare uno script, ad esempio `helloworld.ps1`, e salvarlo nel proprio clouddrive per usarlo in più sessioni di shell.

``` Powershell
cd C:\users\ContainerAdministrator\CloudDrive
PS C:\users\ContainerAdministrator\CloudDrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\CloudDrive> .\helloworld.ps1
Hello World!
```

Quando si usa PowerShell in Cloud Shell la volta successiva, il file `helloworld.ps1` sarà presente nella cartella `CloudDrive` che monta la condivisione file di Azure.

## <a name="use-custom-profile"></a>Usare un profilo personalizzato

È possibile personalizzare l'ambiente PowerShell creando un profilo o più profili PowerShell, `profile.ps1` o `Microsoft.PowerShell_profile.ps1`. Salvarlo in `CloudDrive` in modo che possa essere caricato in ogni sessione di PowerShell quando si avvia Cloud Shell.

Per informazioni su come creare un profilo, fare riferimento a [Informazioni sui profili][profile].

## <a name="use-git"></a>Usare Git

Per clonare un repository git in CloudShell, è necessario creare un [token di accesso personale][githubtoken] e utilizzarlo come nome utente. Dopo avere ottenuto il token, clonare il repository come indicato di seguito:

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
Poiché le sessioni in CloudShell non vengono mantenute quando si esegue la disconnessione o la sessione scade, il file di configurazione Git non sarà esistente al momento dell'accesso successivo. Per rendere persistente il file di configurazione Git, è necessario salvare il .gitconfig sul proprio `CloudDrive` e copiarlo o creare un collegamento simbolico quando `CloudShell` viene avviato. Utilizzare il seguente frammento di codice nel proprio profile.ps1 per creare un collegamento simbolico a `CloudDrive`.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $Home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>Chiudere la shell

Digitare `exit` per terminare la sessione.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
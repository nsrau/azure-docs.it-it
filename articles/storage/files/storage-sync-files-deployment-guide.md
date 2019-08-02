---
title: Distribuire Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni complete su come distribuire Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7b9c9a7639ed7a9938052197758e5796fb9fc879
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699431"
---
# <a name="deploy-azure-file-sync"></a>Distribuire Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

È consigliabile leggere [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md) e [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) prima di completare i passaggi descritti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
* Una condivisione file di Azure nella stessa area che si vuole distribuire Sincronizzazione file di Azure. Per altre informazioni, vedere:
    - [Aree di disponibilità](storage-sync-files-planning.md#region-availability) per Sincronizzazione file di Azure.
    - [Creare una condivisione file](storage-how-to-create-file-share.md) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
* Almeno un'istanza supportata di Windows Server o di cluster Windows Server da sincronizzare con Sincronizzazione file di Azure. Per altre informazioni sulle versioni supportate di Windows Server, vedere [Interoperabilità di Sincronizzazione file di Azure](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Il modulo AZ PowerShell può essere usato con PowerShell 5,1 o PowerShell 6 +. È possibile usare il modulo AZ PowerShell per Sincronizzazione file di Azure in qualsiasi sistema supportato, inclusi i sistemi non Windows, ma il cmdlet di registrazione del server deve essere sempre eseguito nell'istanza di Windows Server che si sta registrando. questa operazione può essere eseguita direttamente o tramite PowerShell Servizi remoti). In Windows Server 2012 R2 è possibile verificare che sia in esecuzione almeno PowerShell 5,1. esaminando il valore della proprietà **psversion** dell'oggetto $psversiontable: \*

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se il valore di PSVersion è minore di 5.1.\*, come nella maggior parte delle installazioni recenti di Windows Server 2012 R2, è possibile eseguire facilmente l'aggiornamento scaricando e installando [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Il pacchetto appropriato da scaricare e installare per Windows Server 2012 R2 è **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6 + può essere usato con qualsiasi sistema supportato e può essere scaricato tramite la [pagina GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se si prevede di usare l'interfaccia utente di registrazione del server, anziché eseguire la registrazione direttamente da PowerShell, è necessario usare PowerShell 5,1.

* Se si è scelto di usare PowerShell 5,1, verificare che sia installato almeno .NET 4.7.2. Altre informazioni su [.NET Framework versioni e dipendenze](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) nel sistema.

    > [!Important]  
    > Se si sta installando .NET 4.7.2 + in Windows Server Core, è necessario installare con `quiet` i `norestart` flag e. in caso contrario, l'installazione avrà esito negativo. Ad esempio, se si installa .NET 4,8, il comando avrà un aspetto simile al seguente:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Il modulo AZ PowerShell, che può essere installato seguendo le istruzioni riportate qui: [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Il modulo AZ. StorageSync viene ora installato automaticamente quando si installa il modulo AZ PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparare Windows Server per l'uso con Sincronizzazione file di Azure
Per ogni server da usare con Sincronizzazione file di Azure, incluso ogni nodo server in un cluster di failover, disabilitare **Sicurezza avanzata di Internet Explorer**. Questa operazione è necessaria solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
> [!Note]  
> È possibile ignorare questo passaggio se si sta distribuendo Sincronizzazione file di Azure in Windows Server Core.

1. Aprire Server Manager.
2. Fare clic su **Server locale**:  
    !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Ne riquadro secondario **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  
    ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Nella finestra di dialogo **Configurazione sicurezza avanzata IE** selezionare **Disattivato** per **Amministratori** e **Utenti**:  
    ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Per disabilitare Sicurezza avanzata di Internet Explorer, eseguire quanto segue da una sessione di PowerShell con privilegi elevati:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Distribuire il servizio di sincronizzazione archiviazione 
La distribuzione di Sincronizzazione file di Azure inizia con l'inserimento di una risorsa **servizio di sincronizzazione archiviazione** in un gruppo di risorse della sottoscrizione selezionata. È consigliabile effettuare il provisioning di alcune di queste risorse in base alle esigenze. Verrà creata una relazione di trust tra i server e la risorsa e un server può essere registrato solo in un servizio di sincronizzazione archiviazione. Di conseguenza, è consigliabile distribuire tutti i servizi di sincronizzazione archiviazione necessari per separare i gruppi di server. Tenere presente che non è possibile sincronizzare tra di loro i server di servizi di sincronizzazione archiviazione diversi.

> [!Note]
> Il servizio di sincronizzazione archiviazione eredita le autorizzazioni di accesso dalla sottoscrizione e dal gruppo di risorse in cui viene distribuito. È consigliabile controllare attentamente chi dispone di accesso. Le entità con accesso in scrittura possono avviare la sincronizzazione di nuovi set di file dai server registrati in questo servizio di sincronizzazione archiviazione, causando il flusso dei dati in un archivio di Azure a loro accessibile.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Per distribuire un servizio di sincronizzazione archiviazione, passare alla [portale di Azure](https://portal.azure.com/), fare clic su *Crea una risorsa* e quindi cercare sincronizzazione file di Azure. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure** e quindi selezionare **Crea** per aprire la scheda **Distribuisci sincronizzazione archiviazione**.

Nel pannello che viene visualizzato immettere le informazioni seguenti:

- **Nome**: Un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.
- **Sottoscrizione** la sottoscrizione in cui creare il servizio di sincronizzazione archiviazione. A seconda della strategia di configurazione dell'organizzazione, è possibile accedere a una o più sottoscrizioni. Una sottoscrizione di Azure è il contenitore di base per la fatturazione di ogni servizio cloud, ad esempio File di Azure.
- **Gruppo di risorse**: un gruppo di risorse è un gruppo logico di risorse di Azure, ad esempio un account di archiviazione o un servizio di sincronizzazione archiviazione. Per Sincronizzazione file di Azure è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. È consigliabile usare i gruppi di risorse come contenitori per isolare le risorse in modo logico per l'organizzazione, ad esempio si possono raggruppare le risorse delle Risorse Umane o per un progetto specifico.
- **Località**: l'area in cui si vuole distribuire Sincronizzazione file di Azure. In questo elenco sono disponibili solo le aree supportate.

Al termine, selezionare **Crea** per distribuire il servizio di sincronizzazione di archiviazione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Sostituire **< Az_Region >** , **< RG_Name >** e **< my_storage_sync_service >** con i propri valori, quindi usare il cmds seguente per creare e distribuire un servizio di sincronizzazione archiviazione:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Installare l'agente Sincronizzazione file di Azure
L'agente Sincronizzazione file di Azure è un pacchetto scaricabile che consente di sincronizzare Windows Server con una condivisione file di Azure. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
È possibile scaricare l'agente dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Al termine del download, fare doppio clic sul pacchetto MSI per avviare l'installazione dell'agente Sincronizzazione file di Azure.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster. Ogni nodo del cluster deve registrato per l'utilizzo con Sincronizzazione file di Azure.

È consigliabile eseguire queste operazioni:
- Lasciare il percorso di installazione predefinito (c:\Programmi\Microsoft Files\Azure\StorageSyncAgent), per semplificare la risoluzione dei problemi e la manutenzione del server.
- Abilitare Microsoft Update per mantenere sempre aggiornato Sincronizzazione file di Azure. Tutti gli aggiornamenti per l'agente Sincronizzazione file di Azure, inclusi gli aggiornamenti delle funzionalità e gli hotfix, vengono eseguiti tramite Microsoft Update. È consigliabile installare l'aggiornamento più recente di Sincronizzazione file di Azure. Per altre informazioni, vedere [Criteri di aggiornamento dell'agente Sincronizzazione file di Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Al termine dell'installazione dell'agente Sincronizzazione file di Azure, viene avviata automaticamente l'interfaccia utente di Registrazione server. È necessario disporre di un servizio di sincronizzazione archiviazione prima di eseguire la registrazione. Per creare un servizio di sincronizzazione archiviazione, vedere la sezione successiva.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Eseguire il codice di PowerShell seguente per scaricare la versione appropriata dell'agente di Sincronizzazione file di Azure per il sistema operativo in uso e installarla nel sistema.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster. Ogni nodo del cluster deve registrato per l'utilizzo con Sincronizzazione file di Azure.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrare Windows Server con il servizio di sincronizzazione archiviazione
La registrazione di Windows Server con un servizio di sincronizzazione archiviazione consente di stabilire una relazione di trust tra il server, o il cluster, in uso e il servizio di sincronizzazione archiviazione. Un server può essere registrato solo in un servizio di sincronizzazione archiviazione e può eseguire la sincronizzazione con altri server e condivisioni file di Azure associati allo stesso servizio di sincronizzazione archiviazione.

> [!Note]
> La registrazione server usa le credenziali di Azure per creare una relazione di trust tra il servizio di sincronizzazione archiviazione e Windows Server. Tuttavia, il server crea e usa successivamente la propria identità, che rimane valida fino a quando il server risulta registrato e il token di firma di accesso condiviso (SAS di archiviazione) corrente è valido. Non è possibile rilasciare un nuovo token di firma di accesso condiviso per il server dopo l'annullamento della registrazione del server, quindi il server non può più accedere alle condivisioni file di Azure con il conseguente arresto di ogni attività di sincronizzazione.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
L'interfaccia utente di Registrazione server viene visualizzata automaticamente al termine dell'installazione dell'agente Sincronizzazione file di Azure. In caso contrario, è possibile aprirla manualmente dal percorso file: C:\Programmi\Azure\StorageSyncAgent\ServerRegistration.exe. Dopo avere aperto l'interfaccia utente di Registrazione server fare clic su **Accesso** per iniziare.

Eseguito l'accesso, viene richiesto di specificare le informazioni seguenti:

![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Sottoscrizione di Azure**: la sottoscrizione che contiene il servizio di sincronizzazione archiviazione. Vedere [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service). 
- **Gruppo di risorse**: il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.
- **Servizio di sincronizzazione archiviazione**: il nome del servizio di sincronizzazione archiviazione con cui si effettua la registrazione.

Dopo avere selezionato le informazioni appropriate, fare clic su **Registra** per completare la registrazione del server. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Creare un gruppo di sincronizzazione e un endpoint cloud
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere un endpoint cloud, che rappresenta una condivisione file di Azure, e uno o più endpoint server. Un endpoint server rappresenta un percorso in un server registrato. Un server può avere endpoint server in più gruppi di sincronizzazione. È possibile creare tutti i gruppi di sincronizzazione necessari per descrivere in modo appropriato la topologia di sincronizzazione desiderata.

Un endpoint cloud è un puntatore a una condivisione file di Azure. Tutti gli endpoint server vengono sincronizzati con un endpoint cloud, che diventa quindi l'hub. L'account di archiviazione per la condivisione file di Azure deve trovarsi nella stessa area del servizio di sincronizzazione archiviazione. Verrà sincronizzata l'intera condivisione file di Azure, con un'eccezione: viene effettuato il provisioning di una cartella speciale, paragonabile alla cartella di informazioni sul volume di sistema nascosta, in un volume NTFS. Tale directory è denominata ".SystemShareInformation". Contiene metadati di sincronizzazione importanti che non vengono sincronizzati negli altri endpoint. Non usarla né eliminarla.

> [!Important]  
> È possibile apportare modifiche a qualsiasi endpoint cloud o endpoint server nel gruppo di sincronizzazione e fare in modo che i file vengano sincronizzati con gli altri endpoint del gruppo di sincronizzazione. Se si apporta direttamente una modifica all'endpoint cloud (condivisione file di Azure), le modifiche apportate devono essere prima di tutto individuate da un processo di rilevamento delle modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Per altre informazioni, vedere [Domande frequenti su File di Azure](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Per creare un gruppo di sincronizzazione, accedere al [portale di Azure](https://portal.azure.com/), passare al servizio di sincronizzazione archiviazione e quindi selezionare **+ Gruppo di sincronizzazione**:

![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Nel riquadro che viene visualizzato immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

- **Nome gruppo di sincronizzazione**: il nome del gruppo di sincronizzazione da creare. Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente.
- **Sottoscrizione** la sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione in [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service).
- **Account di archiviazione**: se si seleziona **Selezionare l'account di archiviazione**, viene visualizzato un altro riquadro in cui è possibile selezionare l'account di archiviazione che contiene la condivisione file di Azure con cui si vuole eseguire la sincronizzazione.
- **Condivisione file di Azure**: il nome della condivisione file di Azure con cui si vuole eseguire la sincronizzazione.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Per creare il gruppo di sincronizzazione, eseguire il comando di PowerShell seguente. Ricordarsi di sostituire `<my-sync-group>` con il nome desiderato per il gruppo di sincronizzazione.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Dopo aver creato correttamente il gruppo di sincronizzazione, è possibile creare l'endpoint cloud. Assicurarsi di sostituire `<my-storage-account>` e `<my-file-share>` con i valori previsti.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Creare un endpoint server
Un endpoint server rappresenta una posizione specifica in un server registrato, ad esempio una cartella in un volume del server. Un endpoint server deve essere un percorso in un server registrato (diverso da una condivisione montata) e per usare la suddivisione in livelli cloud il percorso deve trovarsi in un volume non di sistema. L'archiviazione NAS (Network Attached Storage) non è supportata.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)
Per aggiungere un endpoint server, passare al gruppo di sincronizzazione appena creato e fare clic su **Aggiungi endpoint server**.

![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Nel riquadro **Aggiungi endpoint server** immettere le informazioni seguenti per creare un endpoint server:

- **Server registrato**: il nome del server o del cluster in cui viene creato l'endpoint server.
- **Percorso**: il percorso in Windows Server da sincronizzare come parte del gruppo di sincronizzazione.
- **Suddivisione in livelli nel cloud**: l'opzione che abilita o disabilita la suddivisione in livelli cloud, che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente.
- **Spazio disponibile nel volume**: la quantità di spazio disponibile da riservare nel volume in cui si trova l'endpoint server. Se ad esempio lo spazio disponibile nel volume è impostato su 50% per un volume con un singolo endpoint server, circa la metà dei dati viene archiviata a livelli in File di Azure. A prescindere dall'abilitazione o meno della suddivisione in livelli nel cloud, per la condivisione file di Azure è sempre disponibile una copia completa dei dati nel gruppo di sincronizzazione.

Per aggiungere l'endpoint server, selezionare **Crea**. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Eseguire i comandi di PowerShell seguenti per creare l'endpoint server e assicurarsi di sostituire `<your-server-endpoint-path>` e `<your-volume-free-space>` con i valori desiderati.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Configurare le impostazioni del firewall e della rete virtuale

### <a name="portal"></a>Portale
Se si vuole configurare sincronizzazione file di Azure per l'uso con le impostazioni del firewall e della rete virtuale, eseguire le operazioni seguenti:

1. Dalla portale di Azure passare all'account di archiviazione che si vuole proteggere.
1. Selezionare il pulsante **firewall e reti virtuali** nel menu a sinistra.
1. Selezionare le **reti selezionate** in **Consenti accesso da**.
1. Verificare che l'indirizzo IP o la rete virtuale dei server sia elencato nella sezione appropriata.
1. Verificare che **l'opzione Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** sia selezionata.
1. Per salvare le impostazioni, fare clic su **Save** (Salva).

![Configurazione delle impostazioni del firewall e della rete virtuale per l'uso con sincronizzazione file di Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding con Sincronizzazione file di Azure
Le procedure consigliate per l'esecuzione dell'onboarding in Sincronizzazione file di Azure per la prima volta senza alcun tempo di inattività e mantenendo intatta la fedeltà dei file e dell'elenco di controllo di accesso (ACL) sono le seguenti:
 
1. Distribuire un servizio di sincronizzazione archiviazione.
2. Creare un gruppo di sincronizzazione.
3. Installare l'agente di Sincronizzazione file di Azure nel server con il set di dati completo.
4. Registrare il server e creare un endpoint del server nella condivisione. 
5. Consentire alla sincronizzazione di eseguire il caricamento completo in condivisione file di Azure (endpoint cloud).  
6. Al termine del caricamento iniziale, installare l'agente di Sincronizzazione file di Azure in ognuno dei server rimanenti.
7. Creare nuove condivisioni file in ognuno dei server rimanenti.
8. Creare endpoint server nelle nuove condivisioni file con i criteri di suddivisione in livelli nel cloud, se lo si desidera. Per questo passaggio sono necessarie altre risorse di archiviazione disponibili per l'installazione iniziale.
9. Consentire all'agente di Sincronizzazione file di Azure di eseguire un rapido ripristino dello spazio dei nomi completo senza il trasferimento effettivo dei dati. Dopo la sincronizzazione completa dello spazio dei nomi, il motore di sincronizzazione riempirà lo spazio su disco locale in base ai criteri suddivisione in livelli nel cloud per l'endpoint server. 
10. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze. 
11. Reindirizzare gli utenti e le applicazioni a questa nuova condivisione.
12. Facoltativamente, è possibile eliminare le condivisioni duplicate nei server.
 
Se non si dispone di risorse di archiviazione extra per l'onboarding iniziale e si desidera collegarsi alle condivisioni esistenti, è possibile effettuare il pre-seeding dei dati nelle condivisioni file di Azure. Questo approccio è consigliato solo se è possibile accettare il tempo di inattività e garantire che non avvenga alcuna modifica nelle condivisioni dei server durante il processo di onboarding iniziale. 
 
1. Assicurarsi che i dati in uno qualsiasi dei server non possano essere modificati durante il processo di onboarding.
2. Pre-inizializzare le condivisioni file di Azure con i dati del server usando uno strumento di trasferimento dei dati su SMB, ad esempio Robocopy, copia SMB diretta. Poiché l'utilità AzCopy non carica i dati su SMB, non può essere usata per l'esecuzione del pre-seeding.
3. Creare una topologia di Sincronizzazione file di Azure con gli endpoint del server desiderati che puntano alle condivisioni esistenti.
4. Consentire alla sincronizzazione di completare il processo di riconciliazione in tutti gli endpoint. 
5. Una volta completata la riconciliazione, è possibile aprire le condivisioni per le modifiche.
 
Attualmente, l'approccio di pre-seeding presenta alcune limitazioni: 
- La piena fedeltà dei file non viene mantenuta. I file perdono ad esempio tutti i timestamp e gli elenchi di controllo di accesso.
- Le modifiche ai dati nel server prima che la topologia di sincronizzazione sia completamente operativa possono causare conflitti negli endpoint server.  
- Dopo aver creato l'endpoint cloud, Sincronizzazione file di Azure esegue un processo per rilevare i file nel cloud prima di avviare la sincronizzazione iniziale. Il tempo impiegato per completare il processo varia a seconda dei vari fattori quali la velocità di rete, la larghezza di banda disponibile e il numero di file e cartelle. Per la stima approssimativa nella versione di anteprima, il processo di rilevamento viene eseguito a una velocità di circa 10 file/sec. Di conseguenza, anche se il pre-seeding viene eseguito velocemente, il tempo complessivo per ottenere un sistema completamente operativo può essere notevolmente più lungo quando viene effettuato il pre-seeding dei dati nel cloud.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Eseguire la migrazione di una distribuzione di Replica DFS (DFS-R) in Sincronizzazione file di Azure
Per eseguire la migrazione di una distribuzione di DFS-R in Sincronizzazione file di Azure:

1. Creare un gruppo di sincronizzazione per rappresentare la topologia di DFS-R che si sta sostituendo.
2. Avviare il server contenente il set completo di dati in una topologia DFS-R di cui eseguire la migrazione. Installare Sincronizzazione file di Azure su tale server.
3. Registrare il server e creare un endpoint server per il primo server di cui eseguire la migrazione. Non abilitare la funzionalità Suddivisione in livelli cloud.
4. Consentire la sincronizzazione di tutti i dati in Condivisione file di Azure (endpoint cloud).
5. Installare e registrare l'agente Sincronizzazione file di Azure in ogni server DFS-R rimanente.
6. Disabilitare DFS-R. 
7. Creare un endpoint server in ogni server DFS-R. Non abilitare la funzionalità Suddivisione in livelli cloud.
8. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze.
9. Disattivare DFS-R.
10. A questo punto, è possibile abilitare la funzionalità Suddivisione in livelli cloud in qualsiasi endpoint server in base alle esigenze.

Per altre informazioni, vedere [Interoperabilità di Sincronizzazione file di Azure con il file system distribuito](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere e rimuovere un endpoint server di Sincronizzazione file di Azure](storage-sync-files-server-endpoint.md)
- [Registrare e annullare la registrazione di un server con Sincronizzazione file di Azure](storage-sync-files-server-registration.md)
- [Monitorare Sincronizzazione file di Azure](storage-sync-files-monitoring.md)

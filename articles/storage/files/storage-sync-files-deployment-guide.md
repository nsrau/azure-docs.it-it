---
title: Distribuire Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni complete su come distribuire Sincronizzazione file di Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268067"
---
# <a name="deploy-azure-file-sync"></a>Distribuire Sincronizzazione file di Azure
Usare Sincronizzazione file di Azure per centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Il servizio Sincronizzazione file di Azure trasforma Windows Server in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS (Network File System) e FTPS (File Transfer Protocol Service). Si può usare qualsiasi numero di cache necessario in tutto il mondo.

È consigliabile leggere [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md) e [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) prima di completare i passaggi descritti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
* Una condivisione file di Azure nella stessa area in cui si vuole distribuire Sincronizzazione file di Azure.An Azure file share in the same region that you want to deploy Azure File Sync. Per ulteriori informazioni, vedere:
    - [Aree di disponibilità](storage-sync-files-planning.md#azure-file-sync-region-availability) per Sincronizzazione file di Azure.
    - [Creare una condivisione file](storage-how-to-create-file-share.md) per una descrizione dettagliata della procedura per la creazione di una condivisione file.
* Almeno un'istanza supportata di Windows Server o cluster di Windows Server da sincronizzare con Sincronizzazione file di Azure.At least one supported instance of Windows Server or Windows Server cluster to sync with Azure File Sync. Per ulteriori informazioni sulle versioni supportate di Windows Server, vedere [Interoperabilità con Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* Il modulo Az PowerShell può essere usato con PowerShell 5.1 o PowerShell 6. È possibile usare il modulo Az PowerShell per Sincronizzazione file di Azure in qualsiasi sistema supportato, inclusi i sistemi non Windows, tuttavia il cmdlet di registrazione del server deve sempre essere eseguito nell'istanza di Windows Server che si sta registrando (questa operazione può essere eseguita direttamente o tramite PowerShell servizi remoti). In Windows Server 2012 R2, è possibile verificare che si esegue almeno PowerShell 5.1. \* osservando il valore della proprietà **PSVersion** dell'oggetto **$PSVersionTable:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se il valore di PSVersion è minore di 5.1.\*, come nella maggior parte delle installazioni recenti di Windows Server 2012 R2, è possibile eseguire facilmente l'aggiornamento scaricando e installando [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Il pacchetto appropriato da scaricare e installare per Windows Server 2012 R2 è **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6 può essere utilizzato con qualsiasi sistema supportato e può essere scaricato tramite la [relativa pagina GitHub.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Se si prevede di usare l'interfaccia utente di registrazione del server, anziché registrarsi direttamente da PowerShell, è necessario usare PowerShell 5.1.If you plan to use the Server Registration UI, rather than registering directly from PowerShell, you must use PowerShell 5.1.

* Se si è scelto di usare PowerShell 5.1, verificare che sia installato almeno .NET 4.7.2.If you have opted to use PowerShell 5.1, ensure that at least .NET 4.7.2 is installed. Ulteriori informazioni sulle [versioni di .NET Framework e sulle dipendenze](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) nel sistema.

    > [!Important]  
    > Se si sta installando .NET 4.7.2 in Windows `quiet` Server `norestart` Core, è necessario eseguire l'installazione con i flag e altrimenti l'installazione non riuscirà. Ad esempio, se si installa .NET 4.8, il comando sarà simile al seguente:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Il modulo Az PowerShell, che può essere installato seguendo le istruzioni riportate di seguito: [Installare e configurare Azure PowerShell.](https://docs.microsoft.com/powershell/azure/install-Az-ps)
     
    > [!Note]  
    > Il modulo Az.StorageSync viene ora installato automaticamente quando si installa il modulo Az PowerShell.The Az.StorageSync module is now installed automatically when you install the Az PowerShell module.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparare Windows Server per l'uso con Sincronizzazione file di Azure
Per ogni server da usare con Sincronizzazione file di Azure, incluso ogni nodo server in un cluster di failover, disabilitare **Sicurezza avanzata di Internet Explorer**. Questa operazione è necessaria solo per la registrazione iniziale del server e l'opzione può essere riabilitata dopo che il server è stato registrato.

# <a name="portal"></a>[Portale](#tab/azure-portal)
> [!Note]  
> È possibile ignorare questo passaggio se si distribuisce Sincronizzazione file di Azure in Windows Server Core.You can skip this step if you're deploying Azure File Sync on Windows Server Core.

1. Aprire Server Manager.
2. Fare clic su **Server locale**:  
    !["Server locale" sul lato sinistro dell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Ne riquadro secondario **Proprietà** selezionare il collegamento per **Configurazione sicurezza avanzata IE**.  
    ![Riquadro "Configurazione sicurezza avanzata IE" nell'interfaccia utente di Server Manager](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Nella finestra di dialogo **Protezione avanzata di Internet Explorer** selezionare **Disattivato** per **Amministratori** e **utenti:**  
    ![Finestra popup Configurazione sicurezza avanzata IE con opzione "Disattivato" selezionata](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
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
> Il servizio di sincronizzazione archiviazione eredita le autorizzazioni di accesso dalla sottoscrizione e dal gruppo di risorse in cui è stato distribuito. È consigliabile controllare attentamente chi dispone di accesso. Le entità con accesso in scrittura possono avviare la sincronizzazione di nuovi set di file dai server registrati in questo servizio di sincronizzazione archiviazione, causando il flusso dei dati in un archivio di Azure a loro accessibile.

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per distribuire un servizio di sincronizzazione archiviazione, passare al portale di Azure , fare clic su Crea una risorsa e quindi cercare Sincronizzazione file di Azure.To deploy a Storage Sync Service, go to the [Azure portal](https://portal.azure.com/), click *Create a resource* and then search for Azure File Sync. Nei risultati della ricerca selezionare **Sincronizzazione file di Azure**e quindi selezionare **Crea** per aprire la scheda **Distribuisci sincronizzazione archiviazione.**

Nel pannello che viene visualizzato immettere le informazioni seguenti:

- **Nome**: un nome univoco (per ogni sottoscrizione) per il servizio di sincronizzazione archiviazione.
- **Sottoscrizione**: la sottoscrizione in cui creare il servizio di sincronizzazione archiviazione. A seconda della strategia di configurazione dell'organizzazione, è possibile accedere a una o più sottoscrizioni. Una sottoscrizione di Azure è il contenitore di base per la fatturazione di ogni servizio cloud, ad esempio File di Azure.
- **Gruppo**di risorse: un gruppo di risorse è un gruppo logico di risorse di Azure, ad esempio un account di archiviazione o un servizio di sincronizzazione archiviazione. È possibile creare un nuovo gruppo di risorse o usare un gruppo di risorse esistente per Sincronizzazione file di Azure.You can create a new resource group or use an existing resource group for Azure File Sync. È consigliabile usare i gruppi di risorse come contenitori per isolare le risorse in modo logico per l'organizzazione, ad esempio raggruppando risorse HR o risorse per un progetto specifico.
- **Percorso:** l'area in cui si vuole distribuire Sincronizzazione file di Azure.Location : The region in which you want to deploy Azure File Sync. In questo elenco sono disponibili solo le aree supportate.

Al termine, selezionare **Crea** per distribuire il servizio di sincronizzazione di archiviazione.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Sostituire `<Az_Region>` `<RG_Name>`, `<my_storage_sync_service>` e con valori personalizzati, quindi utilizzare i comandi seguenti per creare e distribuire un servizio di sincronizzazione archiviazione:

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

# <a name="portal"></a>[Portale](#tab/azure-portal)
È possibile scaricare l'agente dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Al termine del download, fare doppio clic sul pacchetto MSI per avviare l'installazione dell'agente Sincronizzazione file di Azure.

> [!Important]  
> Se si intende usare Sincronizzazione file di Azure con un cluster di failover, l'agente Sincronizzazione file di Azure deve essere installato in ogni nodo del cluster. Ogni nodo del cluster deve essere registrato per funzionare con Sincronizzazione file di Azure.Each node in the cluster must be registered to work with Azure File Sync.

È consigliabile eseguire queste operazioni:
- Lasciare il percorso di installazione predefinito (c:\Programmi\Microsoft Files\Azure\StorageSyncAgent), per semplificare la risoluzione dei problemi e la manutenzione del server.
- Abilitare Microsoft Update per mantenere sempre aggiornato Sincronizzazione file di Azure. Tutti gli aggiornamenti per l'agente Sincronizzazione file di Azure, inclusi gli aggiornamenti delle funzionalità e gli hotfix, vengono eseguiti tramite Microsoft Update. È consigliabile installare l'aggiornamento più recente di Sincronizzazione file di Azure.We recommend installing the latest update to Azure File Sync. Per altre informazioni, vedere Criteri di aggiornamento di [Sincronizzazione file](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)di Azure.For more information, see Azure File Sync update policy .

Al termine dell'installazione dell'agente Sincronizzazione file di Azure, viene avviata automaticamente l'interfaccia utente di Registrazione server. È necessario disporre di un servizio di sincronizzazione archiviazione prima di eseguire la registrazione. Per creare un servizio di sincronizzazione archiviazione, vedere la sezione successiva.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
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

# <a name="portal"></a>[Portale](#tab/azure-portal)
L'interfaccia utente di Registrazione server viene visualizzata automaticamente al termine dell'installazione dell'agente Sincronizzazione file di Azure. In caso contrario, è possibile aprirla manualmente dal percorso: c:\Programmi\Microsoft Files\Azure\StorageSyncAgent\ServerRegistration.exe. Dopo avere aperto l'interfaccia utente di Registrazione server fare clic su **Accesso** per iniziare.

Eseguito l'accesso, viene richiesto di specificare le informazioni seguenti:

![Schermata dell'interfaccia utente di Registrazione Server](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Sottoscrizione di Azure**: la sottoscrizione che contiene il servizio di sincronizzazione archiviazione (vedere [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service)). 
- **Gruppo di risorse**: il gruppo di risorse che contiene il servizio di sincronizzazione archiviazione.
- **Servizio di sincronizzazione archiviazione**: il nome del servizio di sincronizzazione archiviazione con cui si effettua la registrazione.

Dopo avere selezionato le informazioni appropriate, fare clic su **Registra** per completare la registrazione del server. Come parte del processo di registrazione, viene richiesto un accesso aggiuntivo.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Creare un gruppo di sincronizzazione e un endpoint cloud
Un gruppo di sincronizzazione definisce la topologia di sincronizzazione per un set di file. Gli endpoint all'interno di un gruppo di sincronizzazione vengono mantenuti sincronizzati tra loro. Un gruppo di sincronizzazione deve contenere un endpoint cloud, che rappresenta una condivisione file di Azure, e uno o più endpoint server. Un endpoint server rappresenta un percorso in un server registrato. Un server può avere endpoint server in più gruppi di sincronizzazione. È possibile creare tutti i gruppi di sincronizzazione necessari per descrivere in modo appropriato la topologia di sincronizzazione desiderata.

Un endpoint cloud è un puntatore a una condivisione file di Azure. Tutti gli endpoint server vengono sincronizzati con un endpoint cloud, che diventa quindi l'hub. L'account di archiviazione per la condivisione file di Azure deve trovarsi nella stessa area del servizio di sincronizzazione archiviazione. Viene sincronizzata l'intera condivisione file di Azure con un'unica eccezione: viene effettuato il provisioning di una cartella speciale, paragonabile alla cartella di informazioni sul volume di sistema nascosta, in un volume NTFS. Tale directory è denominata ".SystemShareInformation". Contiene metadati di sincronizzazione importanti che non vengono sincronizzati negli altri endpoint. Non usarla né eliminarla.

> [!Important]  
> È possibile apportare modifiche a qualsiasi endpoint cloud o endpoint server nel gruppo di sincronizzazione e fare in modo che i file vengano sincronizzati con gli altri endpoint del gruppo di sincronizzazione. Se si apporta direttamente una modifica all'endpoint cloud (condivisione file di Azure), le modifiche apportate devono essere prima di tutto individuate da un processo di rilevamento delle modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una sola volta ogni 24 ore. Per altre informazioni, vedere [Domande frequenti su File di Azure](storage-files-faq.md#afs-change-detection).

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per creare un gruppo di sincronizzazione, nel [portale di Azure](https://portal.azure.com/)passare al servizio di sincronizzazione archiviazione e quindi selezionare Gruppo di **sincronizzazione:**

![Creare un nuovo gruppo di sincronizzazione nel portale di Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Nel riquadro che viene visualizzato immettere le informazioni seguenti per creare un gruppo di sincronizzazione con un endpoint cloud:

- **Nome del gruppo**di sincronizzazione : il nome del gruppo di sincronizzazione da creare. Questo nome deve essere univoco all'interno del servizio di sincronizzazione archiviazione, ma può essere qualsiasi nome logico per l'utente.
- **Sottoscrizione**: la sottoscrizione in cui è stato distribuito il servizio di sincronizzazione archiviazione in [Distribuire il servizio di sincronizzazione archiviazione](#deploy-the-storage-sync-service).
- **Account di archiviazione**: se si seleziona **Selezionare l'account di archiviazione**, viene visualizzato un altro riquadro in cui è possibile selezionare l'account di archiviazione che contiene la condivisione file di Azure con cui si vuole eseguire la sincronizzazione.
- **Condivisione file di Azure**: il nome della condivisione file di Azure con cui si vuole eseguire la sincronizzazione.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
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

# <a name="portal"></a>[Portale](#tab/azure-portal)
Per aggiungere un endpoint server, passare al gruppo di sincronizzazione appena creato e quindi selezionare **Aggiungi endpoint server**.

![Aggiungere un nuovo endpoint server nel riquadro del gruppo di sincronizzazione](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Nel riquadro Aggiungi endpoint server immettere le informazioni seguenti per creare un endpoint server:In the **Add server endpoint** pane, enter the following information to create a server endpoint:

- **Server registrato**: il nome del server o del cluster in cui si desidera creare l'endpoint server.
- **Percorso**: il percorso di Windows Server da sincronizzare come parte del gruppo di sincronizzazione.
- **Suddivisione in livelli cloud**: l'opzione che abilita o disabilita la suddivisione in livelli cloud, che consente di archiviare a livelli in File di Azure i file che si usano o a cui si accede raramente.
- **Spazio libero volume**: quantità di spazio libero da riservare nel volume in cui si trova l'endpoint server. Se ad esempio lo spazio disponibile nel volume è impostato su 50% per un volume con un singolo endpoint server, circa la metà dei dati viene archiviata a livelli in File di Azure. A prescindere dall'abilitazione o meno della suddivisione in livelli nel cloud, per la condivisione file di Azure è sempre disponibile una copia completa dei dati nel gruppo di sincronizzazione.

Per aggiungere l'endpoint server, selezionare **Crea**. I file vengono ora mantenuti sincronizzati tra la condivisione file di Azure e Windows Server. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
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
Se si vuole configurare la sincronizzazione file di Azure in modo che funzioni con le impostazioni del firewall e della rete virtuale, eseguire le operazioni seguenti:If you're like to configure your Azure File sync to work with firewall and virtual network settings, do the following:

1. Dal portale di Azure passare all'account di archiviazione che si vuole proteggere.
1. Selezionare il pulsante **Firewall e reti virtuali** nel menu a sinistra.
1. Selezionare **Reti selezionate** in **Consenti accesso da**.
1. Assicurarsi che l'INDIRIZZO IP o la rete virtuale del server sia elencato nella sezione appropriata.
1. Verificare che l'opzione **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione** sia selezionata.
1. Selezionare **Salva** per salvare le impostazioni.

![Configurazione delle impostazioni del firewall e della rete virtuale per l'utilizzo con la sincronizzazione dei file di AzureConfiguring firewall and virtual network settings to work with Azure File sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

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
9. Consentire all'agente di Sincronizzazione file di Azure di eseguire un ripristino rapido dello spazio dei nomi completo senza il trasferimento di dati effettivo. Dopo la sincronizzazione completa dello spazio dei nomi, il motore di sincronizzazione riempirà lo spazio su disco locale in base ai criteri suddivisione in livelli nel cloud per l'endpoint server. 
10. Verificare che la sincronizzazione sia stata completata e testare la topologia in base alle esigenze. 
11. Reindirizzare gli utenti e le applicazioni a questa nuova condivisione.
12. Facoltativamente, è possibile eliminare le condivisioni duplicate nei server.
 
Se non si dispone di risorse di archiviazione extra per l'onboarding iniziale e si desidera collegarsi alle condivisioni esistenti, è possibile effettuare il pre-seeding dei dati nelle condivisioni file di Azure. Questo approccio è consigliato solo se è possibile accettare il tempo di inattività e garantire che non avvenga alcuna modifica nelle condivisioni dei server durante il processo di onboarding iniziale. 
 
1. Assicurarsi che i dati in uno dei server non possano cambiare durante il processo di onboarding.
2. Condivisioni file di Azure pre-seed con i dati del server usando qualsiasi strumento di trasferimento dati tramite SMB, ad esempio Robocopy, copia diretta SMB. Poiché l'utilità AzCopy non carica i dati su SMB, non può essere usata per l'esecuzione del pre-seeding.
3. Creare una topologia di Sincronizzazione file di Azure con gli endpoint del server desiderati che puntano alle condivisioni esistenti.
4. Consentire alla sincronizzazione di completare il processo di riconciliazione in tutti gli endpoint. 
5. Una volta completata la riconciliazione, è possibile aprire le condivisioni per le modifiche.
 
Attualmente, l'approccio pre-seeding presenta alcune limitazioni - 
- La piena fedeltà dei file non viene mantenuta. I file perdono ad esempio tutti i timestamp e gli elenchi di controllo di accesso.
- Le modifiche ai dati nel server prima che la topologia di sincronizzazione sia completamente operativa possono causare conflitti negli endpoint server.  
- Dopo aver creato l'endpoint cloud, Sincronizzazione file di Azure esegue un processo per rilevare i file nel cloud prima di avviare la sincronizzazione iniziale. Il tempo necessario per completare questo processo varia a seconda dei vari fattori come la velocità di rete, la larghezza di banda disponibile e il numero di file e cartelle. Per la stima approssimativa nella versione di anteprima, il processo di rilevamento viene eseguito a una velocità di circa 10 file/sec. Di conseguenza, anche se il pre-seeding viene eseguito velocemente, il tempo complessivo per ottenere un sistema completamente operativo può essere notevolmente più lungo quando viene effettuato il pre-seeding dei dati nel cloud.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Ripristino self-service tramite versioni precedenti e VSS (servizio Copia Shadow del volume)

> [!IMPORTANT]
> Le informazioni seguenti possono essere utilizzate solo con la versione 9 (o superiore) dell'agente di sincronizzazione archiviazione. Le versioni inferiori a 9 non dicono i cmdlet StorageSyncSelfService.

Versioni precedenti è una funzionalità di Windows che consente di utilizzare istantanee VSS lato server di un volume per presentare le versioni di supporto di un file a un client SMB.
Ciò consente uno scenario potente, comunemente definito ripristino self-service, direttamente per gli information worker anziché a seconda del ripristino da parte di un amministratore IT.

VSS snapshots and Previous Versions work independently of Azure File Sync. Tuttavia, la suddivisione in livelli cloud deve essere impostata su una modalità compatibile. Molti endpoint del server di Sincronizzazione file di Azure possono esistere nello stesso volume. È necessario effettuare la chiamata di PowerShell seguente per ogni volume che dispone anche di un endpoint server in cui si pianifica o si usa la suddivisione in livelli cloud.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Gli snapshot VSS vengono acquisiti di un intero volume. Per impostazione predefinita, possono esistere fino a 64 snapshot per un determinato volume, a c'è spazio sufficiente per archiviare gli snapshot. VSS gestisce automaticamente questo. La pianificazione snapshot predefinita accetta due snapshot al giorno, dal lunedì al venerdì. Tale pianificazione è configurabile tramite un'operazione pianificata di Windows.That schedule is configurable via a Windows Scheduled Task. Il cmdlet PowerShell precedente esegue due operazioni:The above PowerShell cmdlet does two things:
1. Configura la suddivisione in livelli cloud di Sincronizzazione file di Azure nel volume specificato in modo che sia compatibile con le versioni precedenti e garantisce che un file possa essere ripristinato da una versione precedente, anche se è stato suddiviso in livelli nel cloud nel server. 
2. Abilita la pianificazione VSS predefinita. È quindi possibile decidere di modificarlo in un secondo momento. 

> [!Note]  
> Esistono due aspetti importanti da notare:
>- Se si utilizza il parametro -Force e VSS è attualmente abilitato, sovrascriverà la pianificazione snapshot VSS corrente e la sostituirà con la pianificazione predefinita. Assicurarsi di salvare la configurazione personalizzata prima di eseguire il cmdlet.
> - Se si utilizza questo cmdlet in un nodo del cluster, è necessario eseguirlo anche in tutti gli altri nodi del cluster. 

Per verificare se la compatibilità del ripristino self-service è abilitata, è possibile eseguire il cmdlet seguente.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Elencherà tutti i volumi sul server e il numero di giorni compatibili con il tiering cloud per ciascuno. Questo numero viene calcolato automaticamente in base al numero massimo di snapshot possibili per volume e alla pianificazione predefinita dello snapshot. Pertanto, per impostazione predefinita, tutte le versioni precedenti presentate a un Information Worker possono essere utilizzate per il ripristino. Lo stesso vale se si modifica la pianificazione predefinita per creare più snapshot.
Tuttavia, se si modifica la pianificazione in modo da ottenere uno snapshot disponibile nel volume precedente al valore dei giorni compatibili, gli utenti non saranno in grado di utilizzare questo snapshot precedente (versione precedente) da cui eseguire il ripristino.

> [!Note]
> L'abilitazione del ripristino self-service può avere un impatto sull'utilizzo e sulla fattura dell'archiviazione di Azure.Enabling self-service restore can have an impact on your Azure storage consumption and bill. Questo impatto è limitato ai file attualmente suddivisi in livelli nel server. L'abilitazione di questa funzionalità garantisce la disponibilità di una versione di file nel cloud a cui è possibile fare riferimento tramite una voce delle versioni precedenti (snapshot VSS).
>
> Se si disabilita la funzionalità, l'utilizzo dell'archiviazione di Azure diminuirà lentamente fino a quando non sarà trascorso il intervallo di giorni compatibile. Non c'è modo di accelerare la questione. 

Il numero massimo predefinito di snapshot VSS per volume (64) nonché la pianificazione predefinita per accettarli, comportano un massimo di 45 giorni di versioni precedenti da cui un Information Worker può eseguire il ripristino, a seconda del numero di snapshot VSS che è possibile archiviare nel volume.

Se max. 64 Gli snapshot VSS per volume non sono l'impostazione corretta, è possibile [modificare tale valore tramite una chiave del Registro](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)di sistema .
Affinché il nuovo limite abbia effetto, è necessario eseguire nuovamente il cmdlet per abilitare la compatibilità delle versioni precedenti in ogni volume in cui è stato abilitato in precedenza, con il flag -Force per prendere in considerazione il nuovo numero massimo di snapshot VSS per volume. Ciò si tradurrà in un nuovo numero calcolato di giorni compatibili. Si noti che questa modifica avrà effetto solo sui nuovi file a livelli e sovrascriverà tutte le personalizzazioni nella pianificazione VSS che potrebbe essere stata apportata.

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

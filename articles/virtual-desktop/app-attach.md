---
title: Collegamento dell'app MSIX di Windows Virtual Desktop - Azure
description: Come configurare la connessione dell'app MSIX per Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128330"
---
# <a name="set-up-msix-app-attach"></a>Configurare la connessione all'app MSIX

> [!IMPORTANT]
> La connessione dell'app MSIX è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo argomento illustra come configurare il collegamento dell'app MSIX in un ambiente Windows Virtual Desktop.This topic will walk you through how to set up MSIX app attach in a Windows Virtual Desktop environment.

## <a name="requirements"></a>Requisiti

Prima di iniziare, ecco cosa è necessario configurare il collegamento dell'app MSIX:Before you get started, here's what you need to configure MSIX app attach:

- Accesso al portale Windows Insider per ottenere la versione di Windows 10 con il supporto per l'app MSIX collegare le API.
- Una distribuzione di Windows Virtual Desktop funzionante. Per informazioni, vedere [Creare un tenant in Desktop virtuale di Windows.](tenant-setup-azure-active-directory.md)
- Lo strumento di confezionamento MSIX
- Una condivisione di rete nella distribuzione di Windows Virtual Desktop in cui verrà archiviato il pacchetto MSIX

## <a name="get-the-os-image"></a>Scarica l'immagine del sistema operativo

In primo luogo, è necessario ottenere l'immagine del sistema operativo che verrà utilizzata per l'applicazione MSIX. Per ottenere l'immagine del sistema operativo:

1. Apri il [portale Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) e accedi.

     >[!NOTE]
     >Devi essere membro del programma Windows Insider per accedere al portale di Windows Insider. Per ulteriori informazioni sul programma Windows Insider, consulta la documentazione di [Windows Insider.](/windows-insider/at-home/)

2. Scorri verso il basso fino alla sezione **Seleziona edizione** e seleziona **Windows 10 Insider Preview Enterprise (FAST) - Build 19035** o versione successiva.

3. Selezionare **Conferma**, quindi selezionare la lingua che si desidera utilizzare e quindi selezionare di nuovo **Conferma.**
    
     >[!NOTE]
     >Al momento, l'inglese è l'unica lingua che è stata testata con la funzione. È possibile selezionare altre lingue, ma potrebbero non essere visualizzate come previsto.
    
4. Quando viene generato il collegamento di download, selezionare il download a **64 bit** e salvarlo sul disco rigido locale.

## <a name="prepare-the-vhd-image-for-azure"></a>Preparare l'immagine del disco rigido virtuale per AzurePrepare the VHD image for Azure 

Prima di iniziare, è necessario creare un'immagine VHD master. Se non è ancora stata creata l'immagine del disco rigido virtuale master, passare a [Preparare e personalizzare un'immagine VHD master](set-up-customize-master-image.md) e seguire le istruzioni. 

Dopo aver creato l'immagine VHD master, è necessario disabilitare gli aggiornamenti automatici per le applicazioni di collegamento delle applicazioni dell'app MSIX. Per disabilitare gli aggiornamenti automatici, è necessario eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Preparare quindi il disco rigido virtuale della macchina virtuale per Azure e caricare il disco VHD risultante in Azure.Next, prepare the VM VHD for Azure and upload the resulting VHD disk to Azure. Per altre informazioni, vedere [Preparare e personalizzare un'immagine VHD master.](set-up-customize-master-image.md)

Dopo aver caricato il disco rigido virtuale in Azure, creare un pool host basato su questa nuova immagine seguendo le istruzioni nell'esercitazione [Creare un pool host tramite Azure Marketplace.Once](create-host-pools-azure-marketplace.md) you've uploaded the VHD to Azure, create a host pool that's based on this new image by following the instructions in the Create a host pool by using the Azure Marketplace tutorial.

## <a name="prepare-the-application-for-msix-app-attach"></a>Preparare l'applicazione per il collegamento dell'app MSIXPrepare the application for MSIX app attach 

Se si dispone già di un pacchetto MSIX, passare a [Configurare l'infrastruttura desktop virtuale](#configure-windows-virtual-desktop-infrastructure)di Windows . Se si desidera testare le applicazioni legacy, seguire le istruzioni in [Creare un pacchetto MSIX da un programma di installazione desktop in una macchina virtuale](/windows/msix/packaging-tool/create-app-package-msi-vm/) per convertire l'applicazione legacy in un pacchetto MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generare un pacchetto VHD o VHDX per MSIXGenerate a VHD or VHDX package for MSIX

I pacchetti sono in formato VHD o VHDX per ottimizzare le prestazioni. MSIX richiede pacchetti VHD o VHDX per funzionare correttamente.

Per generare un pacchetto VHD o VHDX per MSIX:To generate a VHD or VHDX package for MSIX:

1. [Scaricare lo strumento msixmgr](https://aka.ms/msixmgr) e salvare la cartella .zip in una cartella all'interno di una macchina virtuale host di sessione.

2. Decomprimere la cartella .zip dello strumento msixmgr.

3. Inserire il pacchetto MSIX di origine nella stessa cartella in cui è stato decompresso lo strumento msixmgr.

4. Eseguire il cmdlet seguente in PowerShell per creare un disco rigido virtuale:Run the following cmdlet in PowerShell to create a VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Assicurarsi che le dimensioni del disco rigido virtuale siano sufficientemente grandi da contenere l'oggetto MSIX espanso.

5. Eseguire il cmdlet seguente per montare il disco rigido virtuale appena creato:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Eseguire questo cmdlet per inizializzare il disco rigido virtuale:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Eseguire questo cmdlet per creare una nuova partizione:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Eseguire questo cmdlet per formattare la partizione:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Creare una cartella padre nel disco rigido virtuale montato. Questo passaggio è obbligatorio perché il collegamento dell'app MSIX richiede una cartella padre. È possibile assegnare alla cartella padre il nome desiderato.

### <a name="expand-msix"></a>Espandere MSIX

Dopo di che, è necessario "espandere" l'immagine MSIX decomprimendola. Per decomprimere l'immagine MSIX:

1. Aprire un prompt dei comandi come amministratore e passare alla cartella in cui è stato scaricato e decompresso lo strumento msixmgr.

2. Eseguire il cmdlet seguente per decomprimere il server di creazione registrato nel disco rigido virtuale creato e montato nella sezione precedente.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Il seguente messaggio dovrebbe apparire una volta che la decompressione è fatto:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se si usano pacchetti da Microsoft Store per le aziende (o Education) all'interno della rete o su dispositivi non connessi a Internet, dovrai ottenere le licenze del pacchetto dallo Store e installarle per eseguire correttamente l'app. Consultate [Usare i pacchetti offline.](#use-packages-offline)

3. Passare al disco rigido virtuale montato e aprire la cartella dell'app e verificare che il contenuto del pacchetto sia presente.

4. Smontare il VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configurare l'infrastruttura di Desktop virtuale di Windows

Per impostazione generale, un singolo pacchetto espanso MSIX (il disco rigido virtuale creato nella sezione precedente) può essere condiviso tra più macchine virtuali host di sessione poiché i dischi rigidi virtuali sono collegati in modalità di sola lettura.

Prima di iniziare, assicurati che la condivisione di rete soddisfi i seguenti requisiti:

- La condivisione è compatibile con SMB.
- Le macchine virtuali che fanno parte del pool host di sessione dispongono delle autorizzazioni NTFS per la condivisione.

### <a name="set-up-an-msix-app-attach-share"></a>Configurare una condivisione di collegamento dell'app MSIX 

Nell'ambiente Desktop virtuale di Windows, creare una condivisione di rete e spostare il pacchetto.

>[!NOTE]
> La procedura consigliata per la creazione di condivisioni di rete MSIX consiste nell'impostare la condivisione di rete con autorizzazioni di sola lettura NTFS.

## <a name="install-certificates"></a>Installa i certificati

Se l'app usa un certificato non attendibile o autofirmato, ecco come installarlo:

1. Fare clic con il pulsante destro del mouse sul pacchetto e scegliere **Proprietà**.
2. Nella finestra visualizzata, selezionare la scheda **Firme digitali.** Nella scheda deve essere presente un solo elemento, come illustrato nell'immagine seguente. Selezionare l'elemento per evidenziarlo, quindi selezionare **Dettagli**.
3. Quando viene visualizzata la finestra dei dettagli della firma digitale, selezionare la scheda **Generale** , quindi selezionare **Installa certificato**.
4. Quando si apre il programma di installazione, selezionare **il computer locale** come percorso di archiviazione, quindi selezionare **Avanti**.
5. Se il programma di installazione chiede se si desidera consentire all'app di apportare modifiche al dispositivo, selezionare **Sì**.
6. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi scegliere **Sfoglia**.
7. Quando viene visualizzata la finestra di selezione dell'archivio certificati, selezionare **Persone attendibili**, quindi scegliere **OK**.
8. Fare clic su **Fine**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparare gli script di PowerShell per il collegamento dell'app MSIXPrepare PowerShell scripts for MSIX app attach

La connessione all'app MSIX ha quattro fasi distinte che devono essere eseguite nell'ordine seguente:MSIX app attach has four distinct phases that must be performed in the following order:

1. Fase
2. Register
3. Annullare la registrazione
4. Destage

Ogni fase crea uno script di PowerShell.Each phase creates a PowerShell script. Gli script di esempio per ogni fase sono disponibili [qui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Eseguire temporaneamente lo script di PowerShell

Prima di aggiornare gli script di PowerShell, assicurarsi di disporre del GUID del volume nel disco rigido virtuale. Per ottenere il GUID del volume:

1.  Aprire la condivisione di rete in cui si trova il disco rigido virtuale all'interno della macchina virtuale in cui verrà eseguito lo script.

2.  Fare clic con il pulsante destro del mouse sul disco rigido virtuale e scegliere **Monta**. Questo installerà il disco rigido virtuale a una lettera di unità.

3.  Dopo aver montato il disco rigido virtuale, si aprirà la finestra **Esplora file.** Acquisire la cartella padre e aggiornare la variabile **$parentFolder**

    >[!NOTE]
    >Se non viene visualizzata una cartella padre, significa che l'MSIX non è stato espanso correttamente. Ripetere la sezione precedente e riprovare.

4.  Aprire la cartella padre. Se espanso correttamente, verrà visualizzata una cartella con lo stesso nome del pacchetto. Aggiornare la variabile **$packageName** in modo che corrisponda al nome di questa cartella.

    Ad esempio: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Aprire un prompt dei comandi e immettere **mountvol**. Questo comando visualizza un elenco di volumi e dei relativi GUID. Copiare il GUID del volume in cui la lettera di unità corrisponde all'unità in cui è stato eseguito il disco rigido virtuale nel passaggio 2.

    Ad esempio, in questo output di esempio per il comando mountvol, se il disco rigido `C:\`virtuale è stato montato sull'unità C, è consigliabile copiare il valore precedente:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aggiornare la variabile **$volumeGuid** con il GUID del volume appena copiato.

7. Aprire un prompt di PowerShell di amministrazione e aggiornare lo script di PowerShell seguente con le variabili applicabili all'ambiente.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registrare lo script di PowerShellRegister PowerShell script

Per eseguire lo script di registrazione, eseguire i cmdlet di PowerShell seguenti con i valori segnaposto sostituiti con valori applicabili all'ambiente.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Annullare la registrazione dello script di PowerShellDeregister PowerShell script

Per questo script, sostituire il segnaposto per **$packageName** con il nome del pacchetto che si sta testando.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Script di PowerShell per la destageDestage PowerShell script

Per questo script, sostituire il segnaposto per **$packageName** con il nome del pacchetto che si sta testando.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurare gli script di simulazione per l'agente di collegamento dell'app MSIXSet up simulation scripts for the MSIX app attach agent

Dopo aver creato gli script, gli utenti possono eseguirli manualmente o configurarli per l'esecuzione automatica come script di avvio, accesso, disconnessione e arresto. Per ulteriori informazioni su questi tipi di script, vedere Utilizzo degli script di [avvio, arresto, accesso e disconnessione in Criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Ognuno di questi script automatici esegue una fase dell'app per allegare script:

- Lo script di avvio esegue lo script di fase.
- Lo script di accesso esegue lo script di registrazione.
- Lo script logoff esegue lo script di annullamento della registrazione.
- Lo script di arresto esegue lo script di destage.

## <a name="use-packages-offline"></a>Utilizzare i pacchetti offline

Se usi pacchetti di [Microsoft Store per](https://businessstore.microsoft.com/) le aziende o Microsoft Store per la [formazione](https://educationstore.microsoft.com/) all'interno della rete o in dispositivi non connessi a Internet, devi ottenere le licenze dei pacchetti da Microsoft Store e installarle nel dispositivo per eseguire correttamente l'app. Se il dispositivo è online e può connettersi a Microsoft Store per le aziende, le licenze necessarie devono essere scaricate automaticamente, ma se sei offline, dovrai configurare le licenze manualmente. 

Per installare i file di licenza, è necessario utilizzare uno script di PowerShell che chiama la classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 nel provider di Bridge WMI.  

Ecco come configurare le licenze per l'uso offline: 

1. Scarica il pacchetto dell'app, le licenze e i framework necessari da Microsoft Store per le aziende. Sono necessari sia i file di licenza codificati che quelli non codificati. Istruzioni dettagliate per il download sono disponibili [qui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aggiornare le seguenti variabili nello script per il passaggio 3:
      1. `$contentID`è il valore ContentID del file di licenza non codificato (.xml). È possibile aprire il file di licenza in un editor di testo di propria scelta.
      2. `$licenseBlob`è l'intera stringa per il BLOB di licenze nel file di licenza codificato (bin). È possibile aprire il file di licenza codificato in un editor di testo di propria scelta. 
3. Eseguire lo script seguente da un prompt di PowerShell di amministrazione. Una buona posizione per eseguire l'installazione della licenza è alla fine dello script di [gestione temporanea](#stage-the-powershell-script) che deve essere eseguito anche da un prompt di amministrazione.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Passaggi successivi

Questa funzionalità non è attualmente supportata, ma è possibile porre domande alla community in [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare commenti e suggerimenti per Windows Virtual Desktop nell'hub di commenti e suggerimenti di [Windows Virtual Desktop](https://aka.ms/MRSFeedbackHub)oppure lasciare commenti e suggerimenti per l'app MSIX e lo strumento di creazione pacchetti nell'hub di collegamento [dell'app MSIX](https://aka.ms/msixappattachfeedback) e dell'hub di feedback dello strumento di [creazione pacchetti MSIX.](https://aka.ms/msixtoolfeedback)

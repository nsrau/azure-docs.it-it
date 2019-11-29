---
title: Connessione app MSIX desktop virtuale Windows-Azure
description: Come configurare la connessione all'app MSIX per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: helohr
ms.openlocfilehash: 54f2c39260ecaf643b0f2add8758ceb866e71afe
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561125"
---
# <a name="set-up-msix-app-attach"></a>Configurare la connessione all'app MSIX

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo argomento descrive come configurare la connessione dell'app MSIX in un ambiente desktop virtuale Windows.

## <a name="requirements"></a>Requisiti

Prima di iniziare, è necessario configurare la connessione dell'app MSIX:

- Accesso al portale di Windows Insider per ottenere la versione di Windows 10 con supporto per le API di collegamento delle app MSIX.
- Una distribuzione desktop virtuale di Windows funzionante. Per informazioni, vedere [creare un tenant in un desktop virtuale di Windows](tenant-setup-azure-active-directory.md).
- Strumento per la creazione di pacchetti MSIX
- Una condivisione di rete nella distribuzione di desktop virtuale Windows in cui verrà archiviato il pacchetto MSIX

## <a name="get-the-os-image"></a>Ottenere l'immagine del sistema operativo

In primo luogo, è necessario ottenere l'immagine del sistema operativo da usare per l'app MSIX. Per ottenere l'immagine del sistema operativo:

1. Aprire il [portale di Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) ed accedere.

     >[!NOTE]
     >Per accedere al portale di Windows Insider, è necessario essere membri del programma Windows Insider. Per ulteriori informazioni sul programma Windows Insider, consultare la [documentazione di Windows Insider](https://docs.microsoft.com/windows-insider/at-home/).

2. Scorrere verso il basso fino alla sezione **Select Edition** e selezionare **Windows 10 Insider Preview Enterprise (Fast) – Build xxxxx**.

3. Selezionare **conferma**, quindi selezionare la lingua che si vuole usare e quindi fare di nuovo clic su **conferma** .
    
     >[!NOTE]
     >Al momento, l'inglese è l'unica lingua che è stata testata con la funzionalità. È possibile selezionare altre lingue, ma potrebbero non essere visualizzate come previsto.
    
4. Quando viene generato il collegamento per il download, selezionare il **download a 64 bit** e salvarlo sul disco rigido locale.

## <a name="prepare-the-vhd-image-for-azure"></a>Preparare l'immagine del disco rigido virtuale per Azure 

Prima di iniziare, è necessario creare un'immagine del disco rigido virtuale master. Se non è ancora stata creata l'immagine del disco rigido virtuale Master, passare a [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md) e seguire le istruzioni riportate. 

Dopo aver creato l'immagine del disco rigido virtuale Master, è necessario disabilitare gli aggiornamenti automatici per le applicazioni MSIX. Per disabilitare gli aggiornamenti automatici, è necessario eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati:

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

Preparare quindi il VHD della macchina virtuale per Azure e caricare il disco VHD risultante in Azure. Per altre informazioni, vedere [preparare e personalizzare un'immagine del disco rigido virtuale Master](set-up-customize-master-image.md).

Dopo aver caricato il disco rigido virtuale in Azure, creare un pool host basato su questa nuova immagine seguendo le istruzioni riportate nell'esercitazione [creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md) .

## <a name="prepare-the-application-for-msix-app-attach"></a>Preparare l'applicazione per la connessione all'app MSIX 

Se si dispone già di un pacchetto MSIX, andare avanti per [configurare l'infrastruttura di desktop virtuali Windows](#configure-windows-virtual-desktop-infrastructure). Se si vuole testare le applicazioni legacy, seguire le istruzioni riportate in [creare un pacchetto MSIX da un programma di installazione desktop in una macchina virtuale](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm) per convertire l'applicazione legacy in un pacchetto MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generare un pacchetto VHD o VHDX per MSIX

I pacchetti sono in formato VHD o VHDX per ottimizzare le prestazioni. Per il corretto funzionamento di MSIX sono necessari i pacchetti VHD o VHDX.

Per generare un pacchetto VHD o VHDX per MSIX:

1. [Scaricare lo strumento msixmgr](https://aka.ms/msixmgr) e salvare la cartella zip in una cartella all'interno di una macchina virtuale host sessione.

2. Decomprimere la cartella msixmgr Tool. zip.

3. Inserire il pacchetto MSIX di origine nella stessa cartella in cui è stato decompresso lo strumento msixmgr.

4. Eseguire il cmdlet seguente in PowerShell per creare un disco rigido virtuale:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Verificare che le dimensioni del disco rigido virtuale siano sufficienti per mantenere la MSIX espansa. *

5. Eseguire il cmdlet seguente per montare il VHD appena creato:

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

9. Creare una cartella padre nel disco rigido virtuale montato. Questo passaggio è obbligatorio perché la connessione dell'app MSIX richiede una cartella padre. È possibile denominare la cartella padre come si desidera.

### <a name="expand-msix"></a>Espandi MSIX

Successivamente, sarà necessario "espandere" l'immagine MSIX decomprimerla. Per decomprimere l'immagine di MSIX:

1. Aprire un prompt dei comandi come amministratore e passare alla cartella in cui è stato scaricato e decompresso lo strumento msixmgr.

2. Eseguire il cmdlet seguente per decomprimere il MSIX nel disco rigido virtuale creato e montato nella sezione precedente.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Il messaggio seguente dovrebbe comparire al termine dell'operazione di decompressione:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se si usano i pacchetti di Microsoft Store for business (o Education) nella rete o nei dispositivi non connessi a Internet, sarà necessario ottenere le licenze del pacchetto dallo Store e installarle per eseguire correttamente l'app. Vedere [usare i pacchetti offline](#use-packages-offline).

3. Passare al disco rigido virtuale montato e aprire la cartella dell'app e verificare che il contenuto del pacchetto sia presente.

4. Smontare il disco rigido virtuale.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configurare l'infrastruttura di desktop virtuali Windows

Per impostazione predefinita, un singolo pacchetto espanso MSIX (il VHD creato nella sezione precedente) può essere condiviso tra più macchine virtuali host sessione, perché i dischi rigidi virtuali sono collegati in modalità di sola lettura.

Prima di iniziare, assicurarsi che la condivisione di rete soddisfi i requisiti seguenti:

- La condivisione è compatibile con SMB.
- Le macchine virtuali che fanno parte del pool host sessione hanno autorizzazioni NTFS per la condivisione.

### <a name="set-up-an-msix-app-attach-share"></a>Configurare una condivisione di connessione dell'app MSIX 

Nell'ambiente desktop virtuale Windows creare una condivisione di rete e spostare il pacchetto in tale ambiente.

>[!NOTE]
> La procedura consigliata per la creazione di condivisioni di rete MSIX consiste nell'impostare la condivisione di rete con autorizzazioni di sola lettura NTFS.

## <a name="install-certificates"></a>Installare i certificati

Se l'app usa un certificato che non è pubblico o è autofirmato, di seguito viene illustrato come installarlo:

1. Fare clic con il pulsante destro del mouse sul pacchetto e scegliere **Proprietà**.
2. Nella finestra visualizzata selezionare la scheda **firme digitali** . Nella scheda deve essere presente un solo elemento nell'elenco, come illustrato nella figura seguente. Selezionare l'elemento per evidenziare l'elemento, quindi selezionare * * de
3. Quando viene visualizzata la finestra Dettagli segnale digitale, selezionare la scheda **generale** e quindi fare clic su **Installa certificato**.
4. Quando si apre il programma di installazione, selezionare **computer locale** come percorso di archiviazione, quindi fare clic su **Avanti**.
5. Se il programma di installazione chiede se si vuole consentire all'app di apportare modifiche al dispositivo, selezionare **Sì**.
6. Selezionare **colloca tutti i certificati nel seguente archivio**, quindi selezionare **Sfoglia**.
7. Quando viene visualizzata la finestra Seleziona archivio certificati, selezionare **persone attendibili**, quindi fare clic su **OK**.
8. Selezionare **Fine**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparare gli script di PowerShell per la connessione all'app MSIX

La connessione dell'app MSIX prevede quattro fasi distinte che devono essere eseguite nell'ordine seguente:

1. Stage
2. Iscriviti
3. Annullare
4. Destage

Ogni fase crea uno script di PowerShell. Gli script di esempio per ogni fase sono disponibili [qui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Preparare lo script di PowerShell

Prima di aggiornare gli script di PowerShell, assicurarsi di disporre del GUID del volume del volume nel disco rigido virtuale. Per ottenere il GUID del volume:

1.  Aprire la condivisione di rete in cui si trova il disco rigido virtuale all'interno della macchina virtuale in cui verrà eseguito lo script.

2.  Fare clic con il pulsante destro del mouse sul disco rigido virtuale e scegliere **monta**. Il disco rigido virtuale verrà montato in una lettera di unità.

3.  Dopo aver montato il disco rigido virtuale, viene visualizzata la finestra **Esplora file** . Acquisisci la cartella padre e aggiorna la variabile **\$ParentFolder**

    >[!NOTE]
    >Se non viene visualizzata una cartella padre, significa che MSIX non è stato espanso correttamente. Ripetere la sezione precedente e riprovare.

4.  Aprire la cartella padre. Se viene espansa correttamente, verrà visualizzata una cartella con lo stesso nome del pacchetto. Aggiornare la variabile **\$PackageName** in modo che corrisponda al nome di questa cartella.

    Ad esempio `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Aprire un prompt dei comandi e immettere **mountvol**. Questo comando visualizzerà un elenco di volumi e i relativi GUID. Copiare il GUID del volume in cui la lettera di unità corrisponde all'unità in cui è stato montato il disco rigido virtuale nel passaggio 2.

    Ad esempio, in questo output di esempio per il comando mountvol, se il disco rigido virtuale è stato montato sull'unità C, è necessario copiare il valore precedente `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Aggiornare la variabile **\$volumeGuid** con il GUID del volume appena copiato.

7. Aprire un prompt di PowerShell per l'amministratore e aggiornare lo script di PowerShell seguente con le variabili che si applicano all'ambiente.

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

    Mount-Diskimage -ImagePath \$vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + \$vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + \$vhdSrc + " has failed!") -BackgroundColor Red

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
    $_.ToString() -eq 'System.Threading.Tasks.Task\`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress\`2[TResult,TProgress])'})[0]

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

### <a name="register-powershell-script"></a>Registrare uno script di PowerShell

Per eseguire lo script di registrazione, eseguire i cmdlet di PowerShell riportati di seguito con i valori segnaposto sostituiti con valori validi per l'ambiente in uso.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path \$path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Annullare la registrazione dello script di PowerShell

Per questo script, sostituire il segnaposto per **\$PackageName** con il nome del pacchetto che si sta testando.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Destage script PowerShell

Per questo script, sostituire il segnaposto per **\$PackageName** con il nome del pacchetto che si sta testando.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurare gli script di simulazione per l'agente di associazione app MSIX

Dopo aver creato gli script, gli utenti possono eseguirli manualmente o configurarli per l'esecuzione automatica come script di avvio, di accesso, di disconnessione e di arresto. Per altre informazioni su questi tipi di script, vedere [uso degli script di avvio, arresto, accesso e disconnessione in criteri di gruppo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)).

Ognuno di questi script automatici esegue una fase degli script di connessione dell'app:

- Lo script di avvio esegue lo script della fase.
- Lo script di accesso esegue lo script di registrazione.
- Lo script di disconnessione esegue lo script di annullamento della registrazione.
- Lo script shutdown esegue lo script di disinstallazione.

## <a name="use-packages-offline"></a>USA pacchetti offline

Se si usano pacchetti di [Microsoft Store for business](https://businessstore.microsoft.com/) o l' [Microsoft Store per la formazione](https://educationstore.microsoft.com/) nella rete o nei dispositivi non connessi a Internet, è necessario ottenere le licenze del pacchetto dal Microsoft Store e installarle nel dispositivo per eseguire correttamente l'app. Se il dispositivo è online ed è in grado di connettersi al Microsoft Store for business, le licenze necessarie dovrebbero essere scaricate automaticamente, ma se si è offline sarà necessario configurare le licenze manualmente. 

Per installare i file di licenza, è necessario usare uno script di PowerShell che chiama la classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 nel provider del Bridge WMI.  

Di seguito viene illustrato come configurare le licenze per l'utilizzo offline: 

1. Scaricare il pacchetto dell'app, le licenze e i Framework necessari da Microsoft Store for business. Sono necessari sia il file di licenza codificato che quello non codificato. Le istruzioni dettagliate per il download sono disponibili [qui](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aggiornare le variabili seguenti nello script per il passaggio 3:
      1. `$contentID` è il valore ContentID del file di licenza non codificato (con estensione XML). È possibile aprire il file di licenza in un editor di testo di propria scelta.
      2. `$licenseBlob` è l'intera stringa per il BLOB di licenze nel file di licenza codificato (. bin). È possibile aprire il file di licenza codificato in un editor di testo di propria scelta. 
3. Eseguire lo script seguente da un prompt di PowerShell per l'amministratore. Un posto ideale per eseguire l'installazione delle licenze è alla fine dello [script di staging](#stage-the-powershell-script) che deve essere eseguito anche da un prompt di amministratore.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

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

Questa funzionalità non è attualmente supportata, ma è possibile porre domande alla community sul [desktop virtuale Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare commenti e suggerimenti per desktop virtuale di Windows nell' [Hub di feedback per desktop virtuali Windows](https://aka.ms/MRSFeedbackHub)o lasciare il feedback per l'app MSIX e lo strumento di creazione dei pacchetti nell'hub feedback dell' [app MSIX](https://aka.ms/msixappattachfeedback) e nell'hub di feedback dello strumento di creazione [pacchetti di MSIX](https://aka.ms/msixtoolfeedback).

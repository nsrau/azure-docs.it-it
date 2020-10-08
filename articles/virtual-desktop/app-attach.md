---
title: Connessione all'app MSIX per Desktop virtuale Windows - Azure
description: Procedura di configurazione della connessione all'app MSIX per Desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816319"
---
# <a name="set-up-msix-app-attach"></a>Configurare la connessione all'app MSIX

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo argomento descrive come configurare la connessione all'app MSIX in un ambiente Desktop virtuale Windows.

## <a name="requirements"></a>Requisiti

Prima di iniziare, è necessario configurare la connessione all'app MSIX:

- Accedere al portale di Windows Insider per ottenere la versione di Windows 10 con supporto per le API di connessione all'app MSIX.
- Una distribuzione di Desktop virtuale Windows funzionante. Per informazioni su come distribuire desktop virtuale di Windows (versione classica), vedere [creare un tenant in un desktop virtuale di Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Per informazioni su come distribuire desktop virtuale di Windows con l'integrazione di Azure Resource Manager, vedere [creare un pool host con l'portale di Azure](./create-host-pools-azure-marketplace.md).
- Strumento per la creazione di pacchetti MSIX.
- Una condivisione di rete nella distribuzione di desktop virtuale Windows in cui verrà archiviato il pacchetto MSIX.

## <a name="get-the-os-image"></a>Ottenere l'immagine del sistema operativo

Prima di tutto, è necessario ottenere l'immagine del sistema operativo. È possibile ottenere l'immagine del sistema operativo tramite la portale di Azure. Tuttavia, se si è un membro del programma Windows Insider, è possibile usare invece il portale di Windows Insider.

### <a name="get-the-os-image-from-the-azure-portal"></a>Ottenere l'immagine del sistema operativo dal portale di Azure

Per ottenere l'immagine del sistema operativo dal portale di Azure:

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere.

2. Passare a **creare una macchina virtuale**.

3. Nella scheda di **base** selezionare **Windows 10 Enterprise Multisession, versione 2004**.

4. Per completare la creazione della macchina virtuale, seguire le istruzioni rimanenti.

     >[!NOTE]
     >È possibile usare questa macchina virtuale per testare direttamente la connessione all'app MSIX. Per altre informazioni, andare avanti per [generare un pacchetto VHD o VHDX per MSIX](#generate-a-vhd-or-vhdx-package-for-msix). In caso contrario, continua a leggere questa sezione.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Ottenere l'immagine del sistema operativo dal portale di Windows Insider

Per ottenere l'immagine del sistema operativo dal portale di Windows Insider:

1. Aprire il [portale di Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) ed accedere.

     >[!NOTE]
     >Per accedere al portale di Windows Insider è necessario partecipare al Programma Windows Insider. Per altre informazioni sul programma Windows Insider, consultare la [documentazione di Windows Insider](/windows-insider/at-home/).

2. Scorrere verso il basso fino alla sezione **Seleziona un'edizione** e selezionare **Windows 10 Insider Preview Enterprise (FAST) – Build 19041** o versione successiva.

3. Selezionare **Conferma**, quindi selezionare la lingua da usare e selezionare  di nuovo **Conferma**.

     >[!NOTE]
     >Al momento, l'inglese è l'unica lingua ad essere stata testata con la funzionalità. Anche se è possibile selezionare altre lingue, queste potrebbero non essere visualizzate come previsto.

4. Quando viene generato il collegamento per il download, selezionare **Download a 64 bit** e salvarlo sul disco rigido locale.

## <a name="prepare-the-vhd-image-for-azure"></a>Preparare l'immagine del disco rigido virtuale per Azure

Successivamente, sarà necessario creare un'immagine del disco rigido virtuale master. Se non è ancora stata creata alcuna immagine del disco rigido virtuale master, passare a [Preparare e personalizzare un'immagine master di disco rigido virtuale](set-up-customize-master-image.md) e seguire le istruzioni riportate.

Dopo aver creato l'immagine del disco rigido virtuale master, è necessario disabilitare gli aggiornamenti automatici per le applicazioni di connessione all'app MSIX. Per disabilitare gli aggiornamenti automatici, è necessario eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati:

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

Dopo aver disabilitato gli aggiornamenti automatici, è necessario abilitare Hyper-V perché si userà il comando Mount-VHD per eseguire il staging e smontare il disco rigido virtuale per la fase di disinstallazione.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Questa modifica richiede il riavvio della macchina virtuale.

Preparare quindi il disco rigido virtuale della macchina virtuale per Azure e caricare il disco rigido virtuale risultante in Azure. Per altre informazioni, vedere [Preparare e personalizzare un'immagine master di disco rigido virtuale](set-up-customize-master-image.md).

Dopo aver caricato il disco rigido virtuale in Azure, creare un pool host basato su questa nuova immagine seguendo le istruzioni riportate nell'esercitazione [Creare un pool di host tramite Azure Marketplace](create-host-pools-azure-marketplace.md).

## <a name="prepare-the-application-for-msix-app-attach"></a>Per preparare l'applicazione per la connessione all'app MSIX

Se si dispone già di un pacchetto MSIX, passare a [Configurare l'infrastruttura di Desktop virtuale Windows](#configure-windows-virtual-desktop-infrastructure). Per testare le applicazioni legacy, seguire le istruzioni riportate in [Creare un pacchetto MSIX da un programma di installazione desktop in una macchina virtuale](/windows/msix/packaging-tool/create-app-package-msi-vm/) per convertire l'applicazione legacy in un pacchetto MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Generare un pacchetto VHD o VHDX per MSIX

I pacchetti sono in formato VHD o VHDX per ottimizzare le prestazioni. Per il corretto funzionamento di MSIX sono necessari pacchetti VHD o VHDX.

Per generare un pacchetto VHD o VHDX per MSIX:

1. [Scaricare lo strumento msixmgr](https://aka.ms/msixmgr) e salvare la cartella .zip in una cartella all'interno di una macchina virtuale dell'host di sessione.

2. Decomprimere la cartella .zip dello strumento msixmgr.

3. Inserire il pacchetto MSIX di origine nella stessa cartella in cui è stato decompresso lo strumento msixmgr.

4. Eseguire il cmdlet seguente in PowerShell per creare un disco rigido virtuale:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Verificare che le dimensioni del disco rigido virtuale siano sufficienti per mantenere MSIX espanso.*

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

9. Creare una cartella padre nel disco rigido virtuale montato. Questo passaggio è obbligatorio perché la connessione all'app MSIX richiede una cartella padre. È possibile assegnare alla cartella padre un nome qualsiasi.

### <a name="expand-msix"></a>Espandere MSIX

Successivamente, sarà necessario "espandere" l'immagine MSIX decomprimendola. Per decomprimere l'immagine MSIX:

1. Aprire un prompt dei comandi come amministratore e passare alla cartella in cui è stato scaricato e decompresso lo strumento msixmgr.

2. Eseguire il cmdlet seguente per decomprimere MSIX nel disco rigido virtuale creato e montato nella sezione precedente.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Al termine dell'operazione di decompressione dovrebbe comparire il messaggio seguente:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se si usano pacchetti di Microsoft Store for Business (o Education) all'interno della rete o in dispositivi non connessi a Internet, sarà necessario ottenere le licenze del pacchetto dallo Store e installarle per eseguire correttamente l'app. Vedere [Usare pacchetti offline](#use-packages-offline).

3. Passare al disco rigido virtuale montato e aprire la cartella dell'app per verificare che il contenuto del pacchetto sia presente.

4. Smontare il disco rigido virtuale.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configurare l'infrastruttura di Desktop virtuale Windows

Per impostazione predefinita, un singolo pacchetto MSIX espanso (il disco rigido virtuale creato nella sezione precedente) può essere condiviso tra più macchine virtuali di un host di sessione, perché i dischi rigidi virtuali sono collegati in modalità di sola lettura.

Prima di iniziare, assicurarsi che la condivisione di rete soddisfi i requisiti seguenti:

- La condivisione è compatibile con SMB.
- Le macchine virtuali che fanno parte del pool di host della sessione dispongono di autorizzazioni NTFS per la condivisione.

### <a name="set-up-an-msix-app-attach-share"></a>Configurare una condivisione di connessione all'app MSIX

Nell'ambiente Desktop virtuale Windows, creare una condivisione di rete e spostarvi il pacchetto.

>[!NOTE]
> La procedura consigliata per la creazione di condivisioni di rete MSIX consiste nel configurare la condivisione di rete con autorizzazioni NTFS di sola lettura.

## <a name="install-certificates"></a>Installare i certificati

Se l'app usa un certificato che non è pubblico o è autofirmato, di seguito viene illustrato come installarlo:

1. Fare clic con il pulsante destro del mouse sul pacchetto e scegliere **Proprietà**.
2. Nella finestra visualizzata, selezionare la scheda **Firme digitali**. Nella scheda deve essere presente un solo elemento nell'elenco, come illustrato nella figura seguente. Selezionare l'elemento per evidenziarlo, quindi selezionare **Dettagli**.
3. Quando viene visualizzata la finestra Dettagli firma digitale, selezionare la scheda **generale** , selezionare **Visualizza certificato**, quindi selezionare **Installa certificato**.
4. Quando si apre il programma di installazione, selezionare **Computer locale** come percorso di archiviazione, quindi selezionare **Avanti**.
5. Se il programma di installazione chiede se consentire all'app di apportare modifiche al dispositivo, selezionare **Sì**.
6. Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi selezionare **Sfoglia**.
7. Quando viene visualizzata la finestra Seleziona archivio certificati, selezionare **Persone attendibili**, quindi selezionare **OK**.
8. Selezionare **Avanti** e **fine**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Preparare uno script di PowerShell per la connessione all'app MSIX

La connessione all'app MSIX prevede quattro fasi distinte che devono essere eseguite nell'ordine seguente:

1. Fase
2. Register
3. Annullamento registrazione
4. Rimozione gestione temporanea

Ogni fase crea uno script di PowerShell. Gli script di esempio per ogni fase sono disponibili [qui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-powershell-script"></a>Script di PowerShell per stage

Prima di aggiornare gli script di PowerShell, assicurarsi di disporre del GUID del volume nel disco rigido virtuale. Per ottenere il GUID del volume:

1.  Aprire la condivisione di rete in cui si trova il disco rigido virtuale all'interno della macchina virtuale in cui verrà eseguito lo script.

2.  Fare clic con il pulsante destro del mouse sul disco rigido virtuale e scegliere **Monta**. Il disco rigido virtuale verrà montato in una lettera di unità.

3.  Dopo aver montato il disco rigido virtuale, verrà visualizzata la finestra di **Esplora file**. Acquisire la cartella padre e aggiornare la variabile **$parentFolder**

    >[!NOTE]
    >Se non viene visualizzata alcuna cartella padre, significa che MSIX non è stato espanso correttamente. Ripetere la sezione precedente e riprovare.

4.  Aprire la cartella padre. Se espansa correttamente, verrà visualizzata una cartella con lo stesso nome del pacchetto. Aggiornare la variabile **$packageName** in modo che corrisponda al nome di questa cartella.

    Ad esempio: `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Aprire un prompt dei comandi e immettere **mountvol**. Questo comando visualizzerà un elenco di volumi e i relativi GUID. Copiare il GUID del volume in cui la lettera di unità corrisponde all'unità in cui è stato montato il disco rigido virtuale nel passaggio 2.

    Ad esempio, in questo output di esempio per il comando mountvol, se il disco rigido virtuale è stato montato sull'unità C, sarà necessario copiare il precedente valore `C:\`:

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

7. Aprire un prompt di PowerShell come amministratore e aggiornare lo script di PowerShell seguente con le variabili applicabili all'ambiente.

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
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registrare lo script di PowerShell

Per eseguire lo script di registrazione, eseguire i cmdlet di PowerShell riportati di seguito, dopo avere sostituito i valori segnaposto con valori validi per l'ambiente in uso.

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

### <a name="deregister-powershell-script"></a>Annullare la registrazione di uno script di PowerShell

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

### <a name="destage-powershell-script"></a>Rimuovere la gestione temporanea di uno script di PowerShell

Per questo script, sostituire il segnaposto per **$packageName** con il nome del pacchetto che si sta testando. In una distribuzione di produzione è preferibile eseguire questa operazione alla chiusura.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurare script di simulazione per l'agente di connessione all'app MSIX

Dopo aver creato gli script, gli utenti possono eseguirli manualmente o configurarli per l'esecuzione automatica come script di avvio, di accesso, di disconnessione e di arresto. Per altre informazioni su questi tipi di script, vedere [Uso degli script di avvio, arresto, accesso e disconnessione in Criteri di gruppo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/).

Ognuno di questi script automatici esegue una fase degli script di associazione dell'app:

- Lo script di avvio esegue lo script di preparazione per il commit.
- Lo script di accesso esegue lo script di registrazione.
- Lo script di disconnessione esegue lo script di annullamento della registrazione.
- Lo script di arresto esegue lo script di rimozione della gestione temporanea.

## <a name="use-packages-offline"></a>Usare pacchetti offline

Se si usano pacchetti di [Microsoft Store for Business](https://businessstore.microsoft.com/) o [Microsoft Store for Education](https://educationstore.microsoft.com/) all'interno della rete o in dispositivi non connessi a Internet, sarà necessario ottenere le licenze del pacchetto da Microsoft Store e installarle sul dispositivo per eseguire correttamente l'app. Se il dispositivo è online ed è in grado di connettersi a Microsoft Store for Business, le licenze necessarie saranno scaricate automaticamente, mentre se si è offline sarà necessario configurarle manualmente.

Per installare i file di licenza, sarà necessario usare uno script di PowerShell che chiami la classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 nel provider del bridge WMI.

Di seguito viene illustrato come configurare le licenze per l'uso offline:

1. Scaricare il pacchetto dell'app, le licenze e i framework necessari da Microsoft Store for Business. Sono necessari sia il file di licenza codificato che quello non codificato. Le istruzioni dettagliate per il download sono disponibili [qui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Aggiornare le variabili seguenti nello script per il passaggio 3:
      1. `$contentID` è il valore ContentID del file di licenza non codificato (.xml). Il file di licenza può essere aperto in un editor di testo di propria scelta.
      2. `$licenseBlob` è la stringa intera per il BLOB di licenza nel file di licenza codificato (.bin). Il file di licenza codificato può essere aperto in un editor di testo di propria scelta.
3. Eseguire lo script seguente da un prompt di PowerShell come amministratore. Un percorso ideale per eseguire l'installazione delle licenze è alla fine dello [script di staging](#stage-powershell-script) che deve essere eseguito anche da un prompt di amministratore.

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

Questa funzionalità non è attualmente supportata, ma è possibile porre domande alla community sulla [TechCommunity di Desktop virtuale Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

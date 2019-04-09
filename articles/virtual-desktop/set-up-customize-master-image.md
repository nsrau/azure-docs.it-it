---
title: Preparare e personalizzare un'immagine di disco rigido virtuale master - Azure
description: Come preparare, personalizzare e caricare un'immagine master di anteprima di Desktop virtuale Windows in Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2019
ms.author: helohr
ms.openlocfilehash: d22fffcb792227b4d0805abd005d8c050cb97248
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006208"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparare e personalizzare un'immagine master di disco rigido virtuale

Questo articolo illustrerà come preparare un'immagine di disco rigido virtuale (VHD) master per il caricamento in Azure, incluse le procedure creare macchine virtuali (VM) e installare e configurare il software su di essi. Queste istruzioni sono valide per una configurazione specifici del Desktop virtuale Windows anteprima che può essere usata con i processi esistenti dell'organizzazione.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Multisessione per Windows 10 Enterprise è disponibile nella raccolta di immagini di Azure. Sono disponibili due opzioni per personalizzare l'immagine.

La prima opzione consiste nell'eseguire il provisioning di una macchina virtuale (VM) in Azure seguendo le istruzioni riportate in [creare una macchina virtuale da un'immagine gestita](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)e quindi proseguire con il passaggio [Software preparazione e l'installazione](set-up-customize-master-image.md#software-preparation-and-installation).

La seconda opzione consiste nel creare l'immagine in locale per il download dell'immagine, il provisioning di una macchina virtuale Hyper-V e personalizzarla in base alle esigenze, che verranno trattati nella sezione seguente.

### <a name="local-image-creation"></a>Creazione di un'immagine locale

Dopo aver scaricato l'immagine in un percorso locale, aprire **Hyper-V Manager** per creare una macchina virtuale con il disco rigido virtuale appena copiata. Di seguito è la versione semplice, ma è possibile trovare istruzioni più dettagliate nel [creare una macchina virtuale in Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v).

Per creare una macchina virtuale con il disco rigido virtuale copiato:

1. Aprire il **Creazione guidata macchina virtuale**.

2. Nella pagina specifica generazione, selezionare **generazione 1**.

    ![Screenshot della pagina specifica generazione. L'opzione "Generazione 1" è selezionata.](media/a41174fd41302a181e46385e1e701975.png)

3. In tipo di Checkpoint, disabilitare i checkpoint, deselezionare la casella di controllo.

    ![Screenshot della sezione della pagina i checkpoint del tipo di Checkpoint.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

È anche possibile eseguire il cmdlet seguente in PowerShell per disabilitare i checkpoint.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fisso

Se si crea una macchina virtuale da un VHD esistente, per impostazione predefinita viene creato un disco dinamico. Può essere modificata in un disco fisso selezionando **modifica disco...**  come illustrato nell'immagine seguente. Per istruzioni più dettagliate, vedere [preparare un disco rigido virtuale Windows o vhdx prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

![Screenshot dell'opzione di modifica disco.](media/35772414b5a0f81f06f54065561d1414.png)

È anche possibile eseguire il cmdlet di PowerShell seguente per modificare il disco in un disco fisso.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparazione di software e l'installazione

Questa sezione descrive come preparare e installare Office 365 ProPlus, OneDrive, FSLogix, Windows Defender e altre applicazioni comuni. Se gli utenti devono accedere a determinate applicazioni LOB, è consigliabile che installarli dopo aver completato le istruzioni della sezione.

In questa sezione si presuppone che si have accesso con privilegi elevati nella macchina virtuale, se il provisioning in Azure o Hyper-V Manager.

### <a name="install-office-in-shared-computer-activation-mode"></a>Installare Office nella modalità di attivazione di computer condivisi

Usare la [strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117) per installare Office. Multisessione per Windows 10 Enterprise supporta solo Office 365 ProPlus, non perpetuo di Office 2019.

Lo strumento di distribuzione di Office è necessario un file XML di configurazione. Per personalizzare l'esempio seguente, vedere la [opzioni di configurazione per lo strumento di distribuzione di Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Questo esempio di configurazione XML è stato fornito eseguirà le operazioni seguenti:

- Installare Office dal canale Insider e distribuire gli aggiornamenti dal canale Insider quando vengono eseguiti.
- Architettura di uso x64.
- Disabilitare gli aggiornamenti automatici.
- Installazione di Visio e Project.
- Rimuovere tutte le installazioni esistenti di Office e la migrazione delle impostazioni.
- Abilitare i computer condivisi di gestione delle licenze per l'operazione in un ambiente server terminal.

Ecco cosa non fare questo file XML di configurazione di esempio:

- Installazione di Skype for Business
- Installare OneDrive in modalità utente. Per altre informazioni, vedere [OneDrive installare in modalità per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Licenze per Computer condiviso può essere impostata tramite oggetti Criteri di gruppo (GPO) o le impostazioni del Registro di sistema. Oggetto Criteri di gruppo si trova in **configurazione Computer\\i criteri\\modelli amministrativi\\Microsoft Office 2016 (computer)\\le impostazioni di gestione delle licenze**

Lo strumento di distribuzione di Office contiene setup.exe. Per installare Office, eseguire il comando seguente in una riga di comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuration. XML di esempio

L'esempio XML seguente installerà la versione di Insider, noto anche come Fast Insider o principale di Insider.

```xml
<Configuration>
    <Add OfficeClientEdition="64" SourcePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f">
        <Product ID="O365ProPlusRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Groove" />
            <ExcludeApp ID="Lync" />
            <ExcludeApp ID="OneDrive" />
            <ExcludeApp ID="Teams" />
        </Product>
        <Product ID="VisioProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" /> 
        </Product>
        <Product ID="ProjectProRetail">
            <Language ID="en-US" />
            <Language ID="MatchOS" Fallback = "en-US"/>
            <Language ID="MatchPreviousMSI" />
            <ExcludeApp ID="Teams" />
        </Product>
    </Add>
    <RemoveMSI All="True" />
    <Updates Enabled="FALSE" UpdatePath="http://officecdn.microsoft.com/pr/5440fd1f-7ecb-4221-8110-145efaa6372f" />
    <Display Level="None" AcceptEULA="TRUE" />
    <Logging Level="Verbose" Path="%temp%\WVDOfficeInstall" />
    <Property Value="TRUE" Name="FORCEAPPSHUTDOWN"/>
    <Property Value="1" Name="SharedComputerLicensing"/>
    <Property Value="TRUE" Name="PinIconsToTaskbar"/>
</Configuration>
```

>[!NOTE]
>Il team di Office consiglia l'uso di installazione a 64 bit per il **OfficeClientEdition** parametro.

Dopo l'installazione di Office, è possibile aggiornare il comportamento di Office predefinito. Eseguire i comandi seguenti, singolarmente o in un file batch per aggiornare il comportamento.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

È possibile disabilitare manualmente gli aggiornamenti automatici.

Per disabilitare gli aggiornamenti automatici:

1. Installare Office 365 seguendo le istruzioni riportate in [preparazione di Software e l'installazione](set-up-customize-master-image.md#software-preparation-and-installation).
2. Installare applicazioni aggiuntive, seguendo le istruzioni in [configurare il contenitore di profilo utente (FSLogix)](set-up-customize-master-image.md#set-up-user-profile-container-fslogix), [configura Windows Defender](set-up-customize-master-image.md#configure-windows-defender), e [altre applicazioni e il Registro di sistema configurazione](set-up-customize-master-image.md#other-applications-and-registry-configuration).
3. Disabilitare il servizio di aggiornamento automatico di Windows nella macchina virtuale locale.
4. Aprire **Editor criteri di gruppo locali\\modelli amministrativi\\i componenti di Windows\\Windows Update**.
5. Fare doppio clic su **Configura Aggiornamenti automatici** e impostarlo su **disabilitato**.

È anche possibile eseguire il comando seguente al prompt dei comandi per disabilitare gli aggiornamenti automatici.

```batch
reg add HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

Eseguire questo comando per specificare un layout iniziale per i PC Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="install-onedrive-in-per-machine-mode"></a>Installazione di OneDrive in modalità per computer

OneDrive viene in genere installata per utente. In questo ambiente, deve essere installato sul computer.

Di seguito viene illustrato come installare OneDrive in modalità per computer:

1. Creare innanzitutto un percorso per organizzare il programma di installazione di OneDrive. Una cartella del disco locale o [\\\\unc] (file://unc) percorso è corretto.

2. Scaricare OneDriveSetup.exe il percorso di gestione temporaneo con questo collegamento: <https://aka.ms/OneDriveWVD-Installer>

3. Se è installato office con OneDrive omettendo  **\<ExcludeApp ID = "OneDrive" /\>**, disinstallare eventuali installazioni di ogni utente OneDrive esistente da un prompt dei comandi con privilegi elevati eseguendo le operazioni seguenti comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Eseguire questo comando al prompt dei comandi con privilegi elevati per impostare il **AllUsersInstall** valore del Registro di sistema:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Eseguire questo comando per installare OneDrive in modalità per computer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe /allusers"
    ```

6. Eseguire questo comando per configurare OneDrive iniziare in corrispondenza di accesso per tutti gli utenti:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Abilitare **invisibile all'utente configurare account utente** eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Reindirizzamento e spostare noto cartelle in OneDrive eseguendo il comando seguente di Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

### <a name="teams-and-skype"></a>Teams e Skype

Desktop virtuale di Windows non supporta ufficialmente Skype for Business e i team.

### <a name="set-up-user-profile-container-fslogix"></a>Configurare il contenitore di profilo utente (FSLogix)

Per includere il contenitore FSLogix come parte dell'immagine, seguire le istruzioni in [configurare una condivisione di profilo utente per un pool di host](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). È possibile testare la funzionalità del contenitore FSLogix [questa Guida introduttiva](https://docs.fslogix.com/display/20170529/Profile+Containers+-+Quick+Start).

### <a name="configure-windows-defender"></a>Configura Windows Defender

Se Windows Defender è configurato nella macchina virtuale, assicurarsi che è configurato per l'analisi non l'intero contenuto del file VHD e VHDX durante il collegamento dello stesso.

Solo questa configurazione rimuove la scansione dei file VHD e VHDX durante il collegamento, ma non influisce sull'analisi in tempo reale.

Per istruzioni su come configurare Windows Defender in Windows Server più dettagliate, vedere [esclusioni configura Windows Defender Antivirus in Windows Server](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus).

Per altre informazioni su come configurare Windows Defender per escludere determinati file dall'analisi, vedere [configurare e convalidare le esclusioni in base alla posizione di cartella ed estensione di file](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus).

### <a name="configure-session-timeout-policies"></a>Configurare i criteri di timeout sessione

I criteri di sessione remota possono essere applicati a livello di criteri di gruppo, poiché tutte le macchine virtuali in un pool di host fanno parte dello stesso gruppo di sicurezza.

Per configurare i criteri di sessione remota:

1. Passare a **modelli amministrativi** > **i componenti di Windows** > **Servizi Desktop remoto**  >  **Host sessione Desktop remoto** > **limiti di tempo della sessione**.
2. Nel pannello a destra, selezionare la **limite di tempo impostato per le sessioni di Servizi Desktop remoto attive ma in attesa** criteri.
3. Dopo che viene visualizzata la finestra modale, cambiare l'opzione dei criteri da **non configurato** al **abilitato** per attivare il criterio.
4. Nel menu di riepilogo a discesa sotto l'opzione del criterio, impostare la quantità di tempo **4 ore**.

È anche possibile configurare manualmente i criteri di sessione remota eseguendo i comandi seguenti:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fResetBroken /t REG_DWORD /d 1 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxConnectionTime /t REG_DWORD /d 10800000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v RemoteAppLogoffTimeLimit /t REG_DWORD /d 0 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxDisconnectionTime /t REG_DWORD /d 5000 /f
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v MaxIdleTime /t REG_DWORD /d 7200000 /f
```

### <a name="set-up-time-zone-redirection"></a>Impostare il reindirizzamento di fuso orario

Il reindirizzamento di fuso orario può essere applicato a livello di criteri di gruppo, poiché tutte le macchine virtuali in un pool di host fanno parte dello stesso gruppo di sicurezza.

Per reindirizzare i fusi orari:

1. Nel server di Active Directory, aprire il **Console Gestione criteri di gruppo**.
2. Espandere il dominio e oggetti Criteri di gruppo.
3. Fare doppio clic il **oggetto Criteri di gruppo** creata per le impostazioni dei criteri di gruppo e selezionare **modificare**.
4. Nel **Editor Gestione criteri di gruppo**, passare alla **configurazione del Computer** > **criteri** > **amministrazione I modelli** > **i componenti di Windows** > **orizzonte visualizzare i servizi host sessione Desktop remoto** > **sessione Desktop remoto Host** > **dispositivo e il reindirizzamento della risorsa**.
5. Abilitare la **consentire il reindirizzamento di fuso orario** impostazione.

È anche possibile eseguire questo comando nell'immagine master per reindirizzare i fusi orari:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Disabilitare Sense archiviazione

Per host sessione Desktop virtuale Windows che usano Windows 10 Enterprise o multisessione per Windows 10 Enterprise, si consiglia di disabilitare Sense di archiviazione. È possibile disabilitare Sense archiviazione nel menu Impostazioni sotto **archiviazione**, come illustrato nello screenshot seguente:

![Screenshot del menu di archiviazione in impostazioni. L'opzione "Sensore memoria" è stata disattivata.](media/storagesense.png)

È anche possibile modificare l'impostazione del Registro di sistema eseguendo il comando seguente:

```batch
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Includono il supporto di lingue aggiuntive

Questo articolo non illustra come configurare la lingua e supporto a livello di area. Per altre informazioni, vedere gli articoli seguenti:

- [Aggiungere lingue per le immagini di Windows](https://docs.microsoft.com/windows-hardware/manufacture/desktop/add-language-packs-to-windows)
- [Funzionalità su richiesta](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities)
- [Lingua e area geografica funzionalità su richiesta (DOM)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/features-on-demand-language-fod)

### <a name="other-applications-and-registry-configuration"></a>Altre applicazioni e configurazione del Registro di sistema

Questa sezione descrive l'applicazione e configurazione del sistema operativo. Tutte le configurazioni in questa sezione viene eseguita tramite le voci del Registro di sistema che possono essere eseguite da riga di comando e gli strumenti di regedit.

>[!NOTE]
>È possibile implementare le procedure consigliate nella configurazione con oggetti di criteri generale (GPO) o le importazioni del Registro di sistema. L'amministratore può scegliere delle opzioni in base ai requisiti della propria organizzazione.

Per la raccolta hub commenti e suggerimenti di dati di telemetria nella sessione a più di Windows 10 Enterprise, eseguire questo comando:

```batch
HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection "AllowTelemetry"=dword:00000003
reg add HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection /v AllowTelemetry /d 3
```

Eseguire il comando seguente per risolvere Watson arresti anomali del sistema:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Immettere i comandi seguenti nell'editor del Registro di sistema per correggere supporto per la risoluzione 5 k. È necessario eseguire i comandi prima di abilitare lo stack side-by-side.

```batch
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs]
"MaxMonitors"=dword:00000004
"MaxXResolution"=dword:00001400
"MaxYResolution"=dword:00000b40
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparare l'immagine per il caricamento in Azure

Dopo aver completato la configurazione e tutte le applicazioni installate, seguire le istruzioni in [preparare un disco rigido virtuale Windows o vhdx prima del caricamento in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) per preparare l'immagine.

Dopo aver preparato l'immagine per il caricamento, assicurarsi che la macchina virtuale rimane nello stato spento o deallocato.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Caricare l'immagine master in un account di archiviazione di Azure

In questa sezione si applica solo quando è stata creata l'immagine master in locale.

Le istruzioni seguenti indicano come caricare l'immagine master in un account di archiviazione di Azure. Se non si ha già un account di archiviazione di Azure, seguire le istruzioni in [questo articolo](https://code.visualstudio.com/tutorials/static-website/create-storage) per crearne uno.

1. Convertire l'immagine di macchina virtuale (VHD) a dimensione fissa se non è già presente. Se si non converte l'immagine a dimensione fissa, è possibile creare correttamente l'immagine.

2. Caricare il disco rigido virtuale in un contenitore blob nell'account di archiviazione. È possibile caricare rapidamente con il [lo strumento Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Per altre informazioni sullo strumento Esplora archivi, vedere [questo articolo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Screenshot della finestra di ricerca di Microsoft Azure Storage Explorer dello strumento. È selezionata la casella di controllo "Carica i file con estensione vhd o vhdx come BLOB di pagine (scelta consigliato)".](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Passare quindi al portale di Azure nel browser e cercare "Immagini". La ricerca per rivedendo i **Crea immagine** pagina, come illustrato nello screenshot seguente:

    ![Screenshot della pagina Crea immagine del portale di Azure, riempiti con valori di esempio per l'immagine.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Dopo aver creato l'immagine, si dovrebbe vedere una notifica simile a quello nello screenshot seguente:

    ![Screenshot della notifica "immagine è stato creato".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un'immagine, è possibile creare o aggiornare i pool di host. Per altre informazioni su come creare e aggiornare i pool di host, vedere gli articoli seguenti:

- [Creare un pool di host con un modello di Azure Resource Manager](create-host-pools-arm-template.md)
- [Esercitazione: Creare un pool di host con Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)
- [Configurare una condivisione di profilo utente per un pool di host](create-host-pools-user-profile.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)

---
title: Installa Office in un'immagine di disco rigido virtuale master - Azure
description: Come installare e personalizzare Office su un'immagine master di anteprima di Desktop virtuale Windows in Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/02/2019
ms.author: v-chjenk
ms.openlocfilehash: cb9edbb508ddd993dcefbf69eb06b4f0d4156485
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742557"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installare Office in un'immagine master di disco rigido virtuale

Questo articolo descrive come installare Office 365 ProPlus, OneDrive e altre applicazioni comuni su un'immagine di disco rigido virtuale (VHD) master per il caricamento in Azure. Se gli utenti devono accedere a determinate applicazioni line-of-business (LOB), si consiglia di installarli dopo aver completato le istruzioni riportate in questo articolo.

Questo articolo presuppone di che aver già creato una macchina virtuale (VM). In caso contrario, vedere [Prepare e personalizzare un'immagine di disco rigido virtuale principale](set-up-customize-master-image.md#create-a-vm)

Anche questo articolo presuppone che avere accesso con privilegi elevati nella macchina virtuale, se il provisioning in Azure o Hyper-V Manager. In caso contrario, vedere [elevare l'accesso per gestire tutti i gruppi di gestione e di sottoscrizione Azure](https://docs.microsoft.com/azure/role-based-access-control/elevate-access-global-admin).

>[!NOTE]
>Queste istruzioni sono valide per una configurazione specifici del Desktop virtuale Windows anteprima che può essere usata con i processi esistenti dell'organizzazione.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installare Office nella modalità di attivazione di computer condivisi

Attivazione di computer condivisi ti permette di distribuire Office 365 ProPlus in un computer dell'organizzazione a cui si accede da più utenti. Per altre informazioni sull'attivazione di computer condivisi, vedere [panoramica dell'attivazione di computer condivisi per Office 365 ProPlus](https://docs.microsoft.com/DeployOffice/overview-of-shared-computer-activation-for-office-365-proplus).

Usare la [strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117) per installare Office. Multisessione per Windows 10 Enterprise supporta solo le versioni seguenti di Office:
- Office 365 ProPlus
- Office 365 Business fornita con una sottoscrizione di Microsoft 365 Business

Lo strumento di distribuzione di Office è necessario un file XML di configurazione. Per personalizzare l'esempio seguente, vedere la [opzioni di configurazione per lo strumento di distribuzione di Office](https://docs.microsoft.com/deployoffice/configuration-options-for-the-office-2016-deployment-tool).

Questo esempio di configurazione XML è stato fornito eseguirà le operazioni seguenti:

- Installare Office dal canale Insider e distribuire gli aggiornamenti dal canale Insider quando vengono eseguiti.
- Architettura di uso x64.
- Disabilitare gli aggiornamenti automatici.
- Installazione di Visio e Project.
- Rimuovere tutte le installazioni esistenti di Office e la migrazione delle impostazioni.
- Abilitare l'attivazione di computer condivisi.

>[!NOTE]
>Funzionalità di ricerca di uno stencil di Visio non venga eseguita nel Desktop virtuale di Windows durante la configurazione di anteprima.

Ecco cosa non fare questo file XML di configurazione di esempio:

- Installazione di Skype for Business
- Installare OneDrive in modalità utente. Per altre informazioni, vedere [OneDrive installare in modalità per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Attivazione di Computer condivisi può essere impostata tramite oggetti Criteri di gruppo (GPO) o le impostazioni del Registro di sistema. Oggetto Criteri di gruppo si trova in **configurazione Computer\\i criteri\\modelli amministrativi\\Microsoft Office 2016 (computer)\\le impostazioni di gestione delle licenze**

Lo strumento di distribuzione di Office contiene setup.exe. Per installare Office, eseguire il comando seguente in una riga di comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Configuration. XML di esempio

L'esempio XML seguente installerà la versione Insider.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Installazione di OneDrive in modalità per computer

OneDrive viene in genere installata per utente. In questo ambiente, deve essere installato sul computer.

Di seguito viene illustrato come installare OneDrive in modalità per computer:

1. Creare innanzitutto un percorso per organizzare il programma di installazione di OneDrive. Una cartella del disco locale o [\\\\unc] (file://unc) percorso è corretto.

2. Scaricare OneDriveSetup.exe il percorso di gestione temporaneo con questo collegamento: <https://aka.ms/OneDriveWVD-Installer>

3. Se è installato office con OneDrive omettendo  **\<ExcludeApp ID = "OneDrive" /\>** , disinstallare eventuali installazioni di ogni utente OneDrive esistente da un prompt dei comandi con privilegi elevati eseguendo le operazioni seguenti comando:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Eseguire questo comando al prompt dei comandi con privilegi elevati per impostare il **AllUsersInstall** valore del Registro di sistema:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Eseguire questo comando per installare OneDrive in modalità per computer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Eseguire questo comando per configurare OneDrive iniziare in corrispondenza di accesso per tutti gli utenti:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Abilitare **invisibile all'utente configurare account utente** eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Reindirizzamento e spostare noto cartelle in OneDrive eseguendo il comando seguente di Windows.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams e Skype

Desktop virtuale di Windows non supporta Skype for Business e i team.

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato aggiunto Office all'immagine, è possibile continuare a personalizzare l'immagine di disco rigido virtuale master. Visualizzare [prepara e personalizzare un'immagine di disco rigido virtuale master](set-up-customize-master-image.md).

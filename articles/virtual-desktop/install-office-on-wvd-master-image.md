---
title: Installare Office in un'immagine del disco rigido virtuale master - AzureInstall Office on a master VHD image - Azure
description: Come installare e personalizzare Office in un'immagine master di Windows Virtual Desktop in Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127844"
---
# <a name="install-office-on-a-master-vhd-image"></a>Installare Office in un'immagine master di disco rigido virtuale

Questo articolo illustra come installare Office 365 ProPlus, OneDrive e altre applicazioni comuni in un'immagine del disco rigido virtuale master per il caricamento in Azure.This article tells you how to install Office 365 ProPlus, OneDrive, and other common applications on a master virtual hard disk (VHD) image for upload to Azure. Se gli utenti devono accedere a determinate applicazioni line-of-business (LOB), è consigliabile installarle dopo aver completato le istruzioni in questo articolo.

In questo articolo si presuppone che sia già stata creata una macchina virtuale (VM). In caso contrario, vedere [Preparare e personalizzare un'immagine del disco rigido virtuale master](set-up-customize-master-image.md#create-a-vm)

Questo articolo presuppone inoltre che l'accesso sia elevato nella macchina virtuale, indipendentemente dal fatto che ne venga eseguito il provisioning in Azure o nella gestione di Hyper-V.This article also assumes you have elevated access on the VM, whether it's provisioned in Azure or Hyper-V Manager. In caso contrario, vedere [Elevare l'accesso per gestire tutti i gruppi](../role-based-access-control/elevate-access-global-admin.md)di sottoscrizione e gestione di Azure.If not, see Elevate access to manage all Azure subscription and management groups .

>[!NOTE]
>Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows che può essere usata con processi esistenti dell'organizzazione.

## <a name="install-office-in-shared-computer-activation-mode"></a>Installare Office in modalità di attivazione di computer condivisi

L'attivazione di computer condivisi consente di distribuire Office 365 ProPlus in un computer dell'organizzazione a cui accedono più utenti. Per ulteriori informazioni sull'attivazione di computer condivisi, vedere [Overview of shared computer activation for Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/).

Utilizzare lo [Strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117) per installare Office. Windows 10 Enterprise multisessione supporta solo le seguenti versioni di Office:
- Office 365 ProPlus
- Office 365 Business fornito con un abbonamento a Microsoft 365 Business

Lo Strumento di distribuzione di Office richiede un file XML di configurazione. Per personalizzare l'esempio seguente, vedere [Opzioni di configurazione per lo Strumento di distribuzione di Office](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Questo codice XML di configurazione di esempio fornito eseguirà le operazioni seguenti:This sample configuration XML we've provided will do the following things:

- Installa Office dal canale mensile e distribuisci gli aggiornamenti dal canale mensile quando vengono eseguiti.
- Utilizzare l'architettura x64.
- Disattivare gli aggiornamenti automatici.
- Rimuovere tutte le installazioni esistenti di Office ed eseguire la migrazione delle relative impostazioni.
- Abilitare l'attivazione di computer condivisi.

>[!NOTE]
>La funzionalità di ricerca degli stencil di Visio potrebbe non funzionare come previsto in Windows Virtual Desktop.

Ecco cosa non farà questo codice XML di configurazione di esempio:Here's what this sample configuration XML won't do:

- Installare Skype for Business
- Installare OneDrive in modalità per utente. Per altre informazioni, vedere [Installare OneDrive in modalità per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>L'attivazione di computer condivisi può essere configurata tramite oggetti Criteri di gruppo (GPO) o impostazioni del Registro di sistema. L'oggetto Criteri di gruppo si trova in **Criteri\\\\di configurazione del computer Modelli\\amministrativi Impostazioni di licenza di Microsoft Office 2016 (computer)\\**

Lo Strumento di distribuzione di Office contiene setup.exe. Per installare Office, eseguire il comando seguente in una riga di comando:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Esempio di configuration.xml

L'esempio XML seguente consente di installare la versione mensile.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Il team di Office consiglia di utilizzare l'installazione a 64 bit per il parametro **OfficeClientEdition.**

Dopo aver installato Office, è possibile aggiornare il comportamento predefinito di Office. Eseguire i comandi seguenti singolarmente o in un file batch per aggiornare il comportamento.

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

## <a name="install-onedrive-in-per-machine-mode"></a>Installare OneDrive in modalità per computer

OneDrive viene in genere installato per utente. In questo ambiente, deve essere installato per computer.

Ecco come installare OneDrive in modalità per computer:

1. Creare innanzitutto un percorso per gestire temporaneamente il programma di installazione di OneDrive.First, create a location to stage the OneDrive installer. Una cartella del\\\\disco locale o [unc] (file://unc) va bene.

2. Scaricare OneDriveSetup.exe nella posizione in fasi con questo collegamento:Download OneDriveSetup.exe to your staged location with this link:<https://aka.ms/OneDriveWVD-Installer>

3. Se Office è stato installato con OneDrive omettendo ** \<ExcludeApp ID "OneDrive" /\>**, disinstallare eventuali installazioni di OneDrive per utente esistenti da un prompt dei comandi con privilegi elevati eseguendo il comando seguente:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Eseguire questo comando da un prompt dei comandi con privilegi elevati per impostare il valore del Registro di sistema **AllUsersInstall:**

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Eseguire questo comando per installare OneDrive in modalità per computer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Eseguire questo comando per configurare OneDrive in modo che l'avvio sia in fase di accesso per tutti gli utenti:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Abilitare **la configurazione invisibile all'utente dell'account utente** eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Reindirizzare e spostare le cartelle note di Windows in OneDrive eseguendo il comando seguente.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Team e Skype

Windows Virtual Desktop non supporta Skype for Business e Teams.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto Office all'immagine, è possibile continuare a personalizzare l'immagine del disco rigido virtuale master. Consultate [Preparare e personalizzare un'immagine vHD master.](set-up-customize-master-image.md)

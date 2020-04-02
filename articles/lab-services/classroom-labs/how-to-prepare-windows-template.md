---
title: Guida alla configurazione di una macchina modello di Windows Documenti Microsoft
description: Passaggi generici per preparare un computer modello di Windows in Lab Services.Generic steps to prepare a Windows template machine in Lab Services.  Questi passaggi includono l'impostazione della pianificazione di Windows Update, l'installazione di OneDrive e l'installazione di Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c1aaf588f61b329fa3b838b8a92f3e287897315b
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521179"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guida alla configurazione di un computer modello di Windows in Azure Lab Services

Se stai configurando un computer modello di Windows 10 per Azure Lab Services, ecco alcune procedure consigliate e suggerimenti da considerare. I passaggi di configurazione riportati di seguito sono tutti facoltativi.  Tuttavia, questi passaggi preparatori potrebbero aiutare a rendere gli studenti più produttivi, ridurre al minimo le interruzioni del tempo della classe e assicurarsi che utilizzino le tecnologie più recenti.

>[!IMPORTANT]
>Questo articolo contiene frammenti di PowerShell per semplificare il processo di modifica del modello di computer.  Per tutti gli script di PowerShell illustrati, è consigliabile eseguirli in Windows PowerShell con privilegi di amministratore. In Windows 10, un modo rapido per farlo è quello di fare clic con il pulsante destro del mouse sul menu Start e scegliere "Windows PowerShell (Admin)".

## <a name="install-and-configure-onedrive"></a>Installare e configurare OneDrive

Per proteggere i dati degli studenti dalla perdita in caso di reimpostazione di una macchina virtuale, è consigliabile eseguire il backup dei dati nel cloud.  Microsoft OneDrive può aiutare gli studenti a proteggere i propri dati.  

### <a name="install-onedrive"></a>Installare OneDrive

Per scaricare e installare manualmente OneDrive, vedere le pagine di download di [OneDrive](https://onedrive.live.com/about/download/) o [OneDrive for Business.](https://onedrive.live.com/about/business/)

È anche possibile usare lo script di PowerShell seguente.  Scaricherà e installerà automaticamente l'ultima versione di OneDrive.  Una volta installato il client OneDrive, eseguire il programma di installazione.  In questo esempio viene `/allUsers` usato l'opzione per installare OneDrive per tutti gli utenti del computer. Usiamo anche `/silent` l'interruttore per installare Silently OneDrive.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>Personalizzazioni di OneDrive

Ci sono molte [personalizzazioni che possono essere fatte a OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Esaminiamo alcune delle personalizzazioni più comuni.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Spostare in modo invisibile all'utente le cartelle note di Windows in OneDrive

Cartelle come Documenti, Download e Immagini vengono spesso utilizzate per archiviare i file degli studenti. Per assicurarsi che venga eseguito il backup di queste cartelle in OneDrive, è consigliabile spostare queste cartelle in OneDrive.

Se ci si trova in un computer che non utilizza Active Directory, gli utenti possono spostare manualmente tali cartelle in OneDrive dopo l'autenticazione in OneDrive.

1. Apri Esplora file
2. Fare clic con il pulsante destro del mouse sulla cartella Documenti, Download o Immagini.
3. Passare a Proprietà > posizione.  Spostare la cartella in una nuova cartella nella directory di OneDrive.

Se la macchina virtuale è connessa ad Active Directory, è possibile impostare la macchina modello in modo che gli studenti spostino automaticamente le cartelle note in OneDrive.  

È necessario recuperare prima l'ID tenant di Office.  Per ulteriori istruzioni, vedere [l'ID tenant di Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  È anche possibile ottenere l'ID tenant di Office 365 usando PowerShell seguente.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Dopo aver creato l'ID tenant di Office 365, impostare OneDrive in modo che venga richiesto di spostare le cartelle note in OneDrive usando PowerShell seguente.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Usare i file di OneDrive su richiesta

Gli studenti potrebbero avere molti file all'interno dei loro account OneDrive. Per risparmiare spazio sul computer e ridurre il tempo di download, è consigliabile fare in modo che tutti i file archiviati nell'account OneDrive dello studente siano su richiesta.  I file su richiesta vengono scaricati solo dopo l'accesso del file da parte di un utente.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Accedere in modo invisibile all'utente agli utenti a OneDrive

OneDrive può essere impostato per l'accesso automatico con le credenziali di Windows dell'utente connesso.  L'accesso automatico è utile per i corsi in cui lo studente accede con le credenziali dell'istituto di istruzione di Office 365.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Disabilitare l'esercitazione visualizzata alla fine dell'installazione di OneDrive

Questa impostazione consente di impedire l'avvio dell'esercitazione in un Web browser al termine dell'installazione di OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Impostare la dimensione massima di un file da scaricare automaticamente

Questa impostazione viene usata insieme agli utenti di accesso invisibile all'utente al client di sincronizzazione di OneDrive con le credenziali di Windows nei dispositivi in cui i file di OneDrive non sono abilitati per la richiesta. A qualsiasi utente con un OneDrive di dimensioni superiori alla soglia specificata (in MB) verrà richiesto di scegliere le cartelle da sincronizzare prima che il client di sincronizzazione di OneDrive (OneDrive.exe) scarichi i file.  In questo esempio, "1111-2222-3333-4444" è l'ID tenant di Office 365 e 0005000 imposta una soglia di 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Installare e configurare Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Installare Microsoft Office 365

Se il computer modello necessita di Office, è consigliabile eseguire l'installazione di Office tramite lo Strumento di distribuzione di [Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). Sarà necessario creare un file di configurazione riutilizzabile usando il servizio di configurazione client di [Office 365](https://config.office.com/) per scegliere l'architettura, le funzionalità necessarie da Office e la frequenza di aggiornamento.

1. Passare al servizio di configurazione client di [Office 365](https://config.office.com/) e scaricare il proprio file di configurazione.
2. Scaricare [lo Strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117).  Il file `setup.exe`scaricato sarà .
3. Consente `setup.exe /download configuration.xml` di scaricare i componenti di Office.
4. Eseguire `setup.exe /configure configuration.xml` questa opzione per installare i componenti di Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Modificare il canale di aggiornamento di Microsoft Office 365

Utilizzando lo Strumento di configurazione di Office, è possibile impostare la frequenza con cui Office riceve gli aggiornamenti. Tuttavia, se è necessario modificare la frequenza con cui Office riceve gli aggiornamenti dopo l'installazione, è possibile modificare l'URL del canale di aggiornamento. Gli indirizzi URL del canale di aggiornamento sono disponibili in Modificare il canale di aggiornamento di [Office 365 ProPlus per i dispositivi dell'organizzazione.](https://docs.microsoft.com/deployoffice/change-update-channels) L'esempio seguente mostra come impostare Office 365 per l'uso del canale di aggiornamento mensile.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Installare e configurare gli aggiornamenti

### <a name="install-the-latest-windows-updates"></a>Installare gli aggiornamenti di Windows più recenti

È consigliabile installare gli aggiornamenti Microsoft più recenti nel computer modello per motivi di sicurezza prima di pubblicare la macchina virtuale del modello.  Si evita anche potenzialmente gli studenti di essere interrotto nel loro lavoro quando gli aggiornamenti vengono eseguiti in momenti imprevisti.

1. Avvia **impostazioni** dal menu Start
2. Fare clic su **Aggiorna** & sicurezza
3. Fare clic su **Controlla aggiornamenti**
4. Gli aggiornamenti verranno scaricati e installati.

È anche possibile usare PowerShell per aggiornare il computer modello.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Alcuni aggiornamenti potrebbero richiedere il riavvio del computer.  Verrà richiesto se è necessario un riavvio.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Installare gli aggiornamenti più recenti per le app di Microsoft Store

Ti consigliamo di aggiornare tutte le app di Microsoft Store alle versioni più recenti.  Ecco le istruzioni per aggiornare manualmente le applicazioni da Microsoft Store.  

1. Avviare l'applicazione **Microsoft Store.**
2. Fare clic sui puntini di sospensione (...) accanto alla foto dell'utente nell'angolo superiore dell'applicazione.
3. Seleziona **Scarica** e aggiorna dal menu a discesa.
4. Fare clic sul pulsante **Ottieni aggiornamento.**

È inoltre possibile utilizzare PowerShell per aggiornare le applicazioni di Microsoft Store già installate.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Interrompere gli aggiornamenti automatici di Windows

Dopo aver aggiornato Windows alla versione più recente, potresti prendere in considerazione l'interruzione degli aggiornamenti di Windows.  Gli aggiornamenti automatici potrebbero interferire con l'orario pianificato.  Se il corso è più lungo, è consigliabile chiedere agli studenti di controllare manualmente la disponibilità di aggiornamenti o impostare gli aggiornamenti automatici per un orario al di fuori dell'orario di lezione pianificato.  Per ulteriori informazioni sulle opzioni di personalizzazione per Windows Update, vedere [Gestire ulteriori impostazioni](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)di Windows Update .

Aggiornamenti automatici di Windows potrebbero essere arrestati utilizzando il seguente script di PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Installare i Language Pack per le lingue straniere

Se nella macchina virtuale sono necessarie altre lingue installate, è possibile aggiungerle tramite Microsoft Store.

1. Avvia Microsoft Store
2. Cercare "Language Pack"
3. Scegliere la lingua da installare

Se si è già connessi alla macchina virtuale del modello, usare il [collegamento "Installa Language Pack"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) per passare direttamente alla pagina delle impostazioni appropriata.

## <a name="remove-unneeded-built-in-apps"></a>Rimuovere le app predefinite non necessarie

Windows 10 viene fornito con molte applicazioni integrate che potrebbero non essere necessarie per la tua classe specifica. Per semplificare l'immagine della macchina per gli studenti, è possibile disinstallare alcune applicazioni dalla macchina modello.  Per visualizzare un elenco delle applicazioni `Get-AppxPackage` installate, utilizzare il cmdlet PowerShell.  L'esempio seguente mostra tutte le applicazioni installate che possono essere rimosse.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Per rimuovere un'applicazione, utilizzare il cmdlet Remove-Appx.  L'esempio seguente mostra come rimuovere tutto ciò che riguarda XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Installare applicazioni comuni relative all'insegnamento

Installa altre app comunemente usate per insegnare tramite l'app di Windows Store. I suggerimenti includono applicazioni come [Microsoft Whiteboard app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)e Minecraft [Education Edition](https://education.minecraft.net/). Queste applicazioni devono essere installate manualmente tramite Windows Store o i rispettivi siti Web nella macchina virtuale modello.

## <a name="conclusion"></a>Conclusioni

Questo articolo illustra i passaggi facoltativi per preparare la macchina virtuale del modello di Windows per una classe efficace.  I passaggi includono l'installazione di OneDrive e l'installazione di Office 365, l'installazione degli aggiornamenti per Windows e l'installazione degli aggiornamenti per le app di Microsoft Store.  Abbiamo anche discusso come impostare gli aggiornamenti di un programma che funziona meglio per la tua classe.  

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo su come controllare il comportamento di arresto di Windows per facilitare la gestione dei costi: [Guida al controllo](how-to-windows-shutdown.md) del comportamento di arresto di Windows

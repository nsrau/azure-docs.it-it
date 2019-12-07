---
title: Guida alla configurazione di un computer modello Windows | Microsoft Docs
description: Procedura generica per preparare un computer modello Windows in Lab Services.  Questi passaggi includono l'impostazione Windows Update pianificazione, l'installazione di OneDrive e l'installazione di Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: e8c0e67567bd5ddbfdd45762edd52112c1fd4c70
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897273"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Guida alla configurazione di un computer modello Windows in Azure Lab Services

Se si sta configurando un computer modello Windows 10 per Azure Lab Services, di seguito sono riportate alcune procedure consigliate e suggerimenti da prendere in considerazione. I passaggi di configurazione riportati di seguito sono tutti facoltativi.  Tuttavia, questi passaggi preliminari possono aiutare a rendere gli studenti più produttivi, ridurre al minimo le interruzioni temporali delle classi e assicurarsi che usino le tecnologie più recenti.

>[!IMPORTANT]
>Questo articolo contiene i frammenti di codice di PowerShell per semplificare il processo di modifica dei modelli di computer.  Per tutti gli script di PowerShell mostrati, è necessario eseguirli in Windows PowerShell con privilegi di amministratore. In Windows 10, un modo rapido per farlo è fare clic con il pulsante destro del mouse sul menu Start e scegliere "Windows PowerShell (admin)".

## <a name="install-and-configure-onedrive"></a>Installare e configurare OneDrive

Per evitare che i dati degli studenti vadano persi se una macchina virtuale viene reimpostata, è consigliabile che gli studenti riportino i dati nel cloud.  Microsoft OneDrive può aiutare gli studenti a proteggere i dati.  

### <a name="install-onedrive"></a>Installare OneDrive

Per scaricare e installare manualmente OneDrive, vedere le pagine di download di [OneDrive](https://onedrive.live.com/about/download/) o [OneDrive for business](https://onedrive.live.com/about/business/) .

È anche possibile usare lo script di PowerShell seguente.  La versione più recente di OneDrive verrà scaricata e installata automaticamente.  Una volta installato il client di OneDrive, eseguire il programma di installazione.  In questo esempio viene usata l'opzione `/allUsers` per installare OneDrive per tutti gli utenti del computer. Si usa anche l'opzione `/silent` per installare OneDrive in modo invisibile all'utente.

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

Sono disponibili molte [personalizzazioni che è possibile eseguire per OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Ecco alcune delle personalizzazioni più comuni.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Sposta automaticamente le cartelle note di Windows in OneDrive

Cartelle quali documenti, download e immagini vengono spesso usate per archiviare i file degli studenti. Per assicurarsi che venga eseguito il backup di queste cartelle in OneDrive, è consigliabile spostare queste cartelle in OneDrive.

Se ci si trova in un computer che non usa Active Directory, gli utenti possono spostare manualmente le cartelle in OneDrive dopo l'autenticazione in OneDrive.

1. Apre Esplora file
2. Fare clic con il pulsante destro del mouse sulla cartella documenti, download o immagini.
3. Passare a proprietà > percorso.  Spostare la cartella in una nuova cartella nella directory OneDrive

Se la macchina virtuale è connessa a Active Directory, è possibile impostare il computer modello in modo da richiedere automaticamente agli studenti di spostare le cartelle note in OneDrive.  

Per prima cosa è necessario recuperare l'ID tenant di Office.  Per altre istruzioni, vedere [trovare l'ID tenant di Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).  È anche possibile ottenere l'ID tenant di Office 365 usando il seguente PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Dopo aver ottenuto l'ID tenant di Office 365, impostare OneDrive per chiedere di spostare le cartelle note in OneDrive usando il PowerShell seguente.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>Usare i file OneDrive su richiesta

Gli studenti potrebbero avere molti file all'interno degli account OneDrive. Per risparmiare spazio nel computer e ridurre i tempi di download, è consigliabile fare in modo che tutti i file archiviati nell'account OneDrive dello studente siano su richiesta.  I file su richiesta vengono scaricati solo dopo che un utente ha eseguito l'accesso al file.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Accedi automaticamente agli utenti a OneDrive

È possibile impostare OneDrive in modo che acceda automaticamente con le credenziali di Windows dell'utente che ha eseguito l'accesso.  L'accesso automatico è utile per le classi in cui lo studente accede con le proprie credenziali di Office 365 School.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>Disabilitare l'esercitazione visualizzata alla fine dell'installazione di OneDrive

Questa impostazione consente di impedire l'avvio dell'esercitazione in un Web browser alla fine del programma di installazione di OneDrive.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>Consente di impostare le dimensioni massime di un file da scaricare automaticamente

Questa impostazione viene usata in combinazione con l'accesso automatico degli utenti al client di sincronizzazione OneDrive con le credenziali di Windows nei dispositivi in cui non sono abilitati i file OneDrive su richiesta. A tutti gli utenti con un OneDrive maggiore della soglia specificata (in MB) verrà richiesto di scegliere le cartelle che vogliono sincronizzare prima che il client di sincronizzazione OneDrive (OneDrive. exe) scarichi i file.  Nell'esempio "1111-2222-3333-4444" è l'ID tenant di Office 365 e 0005000 imposta una soglia di 5 GB.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Installare e configurare Microsoft Office 365

### <a name="install-microsoft-office-365"></a>Installare Microsoft Office 365

Se il computer modello richiede Office, è consigliabile installare Office tramite lo [strumento di distribuzione di Office (ODT)](https://www.microsoft.com/download/details.aspx?id=49117 ). È necessario creare un file di configurazione riutilizzabile usando il [servizio di configurazione client di office 365](https://config.office.com/) per scegliere l'architettura, le funzionalità necessarie per Office e la frequenza di aggiornamento.

1. Passare al [servizio configurazione client di Office 365](https://config.office.com/) e scaricare il file di configurazione.
2. Scaricare [lo strumento di distribuzione di Office](https://www.microsoft.com/download/details.aspx?id=49117).  Il file scaricato verrà `setup.exe`.
3. Eseguire `setup.exe /download configuration.xml` per scaricare i componenti di Office.
4. Eseguire `setup.exe /configure configuration.xml` per installare i componenti di Office.

### <a name="change-the-microsoft-office-365-update-channel"></a>Modificare il canale di aggiornamento Microsoft Office 365

Utilizzando lo strumento di configurazione di Office, è possibile impostare la frequenza con cui Office riceve gli aggiornamenti.  Tuttavia, se è necessario modificare la frequenza con cui Office riceve gli aggiornamenti dopo l'installazione, è possibile modificare l'URL del canale di aggiornamento.  Gli indirizzi URL del canale di aggiornamento sono reperibili in [modificare il canale di aggiornamento dopo aver abilitato i client di Office 365 per la ricezione di aggiornamenti da Configuration Manager](https://docs.microsoft.com/sccm/sum/deploy-use/manage-office-365-proplus-updates#change-the-update-channel-after-you-enable-office-365-clients-to-receive-updates-from-configuration-manager). Nell'esempio seguente viene illustrato come impostare Office 365 per l'utilizzo del canale di aggiornamento mensile.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Installare e configurare gli aggiornamenti

### <a name="install-the-latest-windows-updates"></a>Installare gli aggiornamenti di Windows più recenti

È consigliabile installare gli aggiornamenti Microsoft più recenti nel computer modello per motivi di sicurezza prima di pubblicare la macchina virtuale del modello.  È anche possibile evitare che gli studenti si interrompano durante il lavoro quando gli aggiornamenti vengono eseguiti in momenti imprevisti.

1. Avviare **le impostazioni** dal menu Start
2. Fare clic su **aggiorna** & sicurezza
3. Fare clic su **Controlla aggiornamenti**
4. Gli aggiornamenti vengono scaricati e installati.

È anche possibile usare PowerShell per aggiornare il computer modello.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Alcuni aggiornamenti potrebbero richiedere il riavvio del computer.  Verrà richiesto se è necessario riavviare il computer.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>Installare gli aggiornamenti più recenti per le app Microsoft Store

Si consiglia di aggiornare tutte le app Microsoft Store alle versioni più recenti.  Di seguito sono riportate le istruzioni per aggiornare manualmente le applicazioni dal Microsoft Store.  

1. Avviare **Microsoft Store** applicazione.
2. Fare clic sui puntini di sospensione (...) accanto alla foto dell'utente nell'angolo superiore dell'applicazione.
3. Selezionare **Scarica** e Aggiorna dal menu a discesa.
4. Fare clic sul pulsante **get update** .

È anche possibile usare PowerShell per aggiornare Microsoft Store applicazioni già installate.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Arrestare gli aggiornamenti automatici di Windows

Dopo l'aggiornamento di Windows alla versione più recente, è possibile prendere in considerazione l'arresto degli aggiornamenti di Windows.  Gli aggiornamenti automatici potrebbero interferire con l'ora della classe pianificata.  Se il corso è più lungo, prendere in considerazione la possibilità di chiedere agli studenti di controllare manualmente la disponibilità di aggiornamenti o di impostare gli aggiornamenti automatici per un periodo di tempo non compreso nelle ore della classe pianificata.  Per ulteriori informazioni sulle opzioni di personalizzazione per Windows Update, vedere la pagina relativa alla [gestione delle impostazioni di Windows Update aggiuntive](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings).

Gli aggiornamenti automatici di Windows possono essere interrotti usando il seguente script di PowerShell.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Installare i Language Pack esterni

Se sono necessarie altre lingue installate nella macchina virtuale, è possibile aggiungerle tramite la Microsoft Store.

1. Avvia Microsoft Store
2. Cerca "Language Pack"
3. Scegliere la lingua da installare

Se è già stato effettuato l'accesso alla macchina virtuale modello, usare il [collegamento "installa il Language Pack"](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) per passare direttamente alla pagina delle impostazioni appropriate.

## <a name="remove-unneeded-built-in-apps"></a>Rimuovere le app predefinite non necessarie

Windows 10 è dotato di molte applicazioni predefinite che potrebbero non essere necessarie per una classe specifica. Per semplificare l'immagine del computer per gli studenti, potrebbe essere necessario disinstallare alcune applicazioni dal computer modello.  Per visualizzare un elenco delle applicazioni installate, usare il cmdlet `Get-AppxPackage` di PowerShell.  Nell'esempio seguente vengono illustrate tutte le applicazioni installate che possono essere rimosse.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Per rimuovere un'applicazione, usare il cmdlet Remove-appx.  Nell'esempio seguente viene illustrato come rimuovere tutti gli elementi correlati a XBox.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Installare applicazioni comuni correlate all'insegnamento

Installare altre app comunemente usate per l'insegnamento tramite l'app di Windows Store. I suggerimenti includono applicazioni come [Microsoft whiteboard app](https://www.microsoft.com/store/productId/9MSPC6MP8FM4), [Microsoft teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)e [Minecraft Education Edition](https://education.minecraft.net/). Queste applicazioni devono essere installate manualmente tramite Windows Store o tramite i rispettivi siti Web nella macchina virtuale del modello.

## <a name="conclusion"></a>Conclusione

Questo articolo ha illustrato i passaggi facoltativi per preparare la VM modello di Windows per una classe efficace.  I passaggi includono l'installazione di OneDrive e l'installazione di Office 365, l'installazione degli aggiornamenti per Windows e l'installazione degli aggiornamenti per Microsoft Store app.  È stato inoltre illustrato come impostare gli aggiornamenti di una pianificazione più adatta per la classe.  

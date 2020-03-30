---
title: Preparare e personalizzare un'immagine del disco rigido virtuale master - AzurePrepare and customize a master VHD image - Azure
description: Come preparare, personalizzare e caricare un'immagine master di Windows Virtual Desktop in Azure.How to prepare, customize and upload a Windows Virtual Desktop master image to Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127720"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparare e personalizzare un'immagine master di disco rigido virtuale

Questo articolo illustra come preparare un'immagine del disco rigido virtuale master per il caricamento in Azure, incluso come creare macchine virtuali (VM) e installare software in esse. Le istruzioni riguardano una configurazione specifica di Desktop virtuale Windows che può essere usata con processi esistenti dell'organizzazione.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Windows 10 Enterprise multi-session is available in the Azure Image Gallery. Ci sono due opzioni per personalizzare questa immagine.

La prima opzione consiste nel eseguire il provisioning di una macchina virtuale (VM) in Azure seguendo le istruzioni in [Creare una macchina virtuale da un'immagine gestita](../virtual-machines/windows/create-vm-generalized-managed.md)e quindi passare a Preparazione e installazione del [software](set-up-customize-master-image.md#software-preparation-and-installation).

La seconda opzione consiste nel creare l'immagine in locale scaricando l'immagine, eseguendo il provisioning di una macchina virtuale Hyper-V e personalizzandola in base alle proprie esigenze, trattate nella sezione seguente.

### <a name="local-image-creation"></a>Creazione di immagini locali

Dopo aver scaricato l'immagine in un percorso locale, aprire la console di **gestione di Hyper-V** per creare una macchina virtuale con il disco rigido virtuale copiato. Le istruzioni seguenti sono una versione semplice, ma è possibile trovare istruzioni più dettagliate in [Creare una macchina virtuale in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Per creare una macchina virtuale con il disco rigido virtuale copiato:To create a VM with the copied VHD:

1. Aprire la **Creazione guidata nuova macchina virtuale**.

2. Nella pagina Specifica generazione selezionare **Generazione 1**.

    ![Schermata della pagina Specifica generazione. È selezionata l'opzione "Generazione 1".](media/a41174fd41302a181e46385e1e701975.png)

3. In Tipo di checkpoint disabilitare i checkpoint deselezionando la casella di controllo.

    ![Schermata della sezione Tipo di checkpoint della pagina Checkpoint.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

È inoltre possibile eseguire il cmdlet seguente in PowerShell per disabilitare i checkpoint.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fisso

Se si crea una macchina virtuale da un disco rigido virtuale esistente, viene creato un disco dinamico per impostazione predefinita. Può essere cambiato in un disco fisso selezionando **Modifica disco...** come mostrato nell'immagine seguente. Per istruzioni più dettagliate, vedere Preparare un disco rigido virtuale di Windows o un disco rigido virtuale per il caricamento in Azure.For more detailed instructions, see [Prepare a Windows VHD or VHDX to upload to Azure.](../virtual-machines/windows/prepare-for-upload-vhd-image.md)

![Schermata dell'opzione Modifica disco.](media/35772414b5a0f81f06f54065561d1414.png)

È inoltre possibile eseguire il seguente cmdlet PowerShell per modificare il disco in un disco fisso.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparazione e installazione del software

Questa sezione illustra come preparare e installare FSLogix e Windows Defender, nonché alcune opzioni di configurazione di base per le app e il Registro di sistema dell'immagine. 

Se si installa Office 365 ProPlus e OneDrive nella macchina virtuale, passare a [Installare Office in un'immagine del disco rigido virtuale master](install-office-on-wvd-master-image.md) e seguire le istruzioni per installare le app. Al termine, tornare a questo articolo.

Se gli utenti devono accedere a determinate applicazioni lob, è consigliabile installarle dopo aver completato le istruzioni di questa sezione.

### <a name="set-up-user-profile-container-fslogix"></a>Configurare il contenitore dei profili utente (FSLogix)Set up user profile container (FSLogix)

Per includere il contenitore FSLogix come parte dell'immagine, seguire le istruzioni in [Creare un contenitore di profili per un pool host usando una condivisione file.](create-host-pools-user-profile.md#configure-the-fslogix-profile-container) È possibile testare la funzionalità del contenitore FSLogix con [questa guida introduttiva.](/fslogix/configure-cloud-cache-tutorial/)

### <a name="configure-windows-defender"></a>Configurare Windows Defender

Se Windows Defender è configurato nella macchina virtuale, assicurarsi che sia configurato per non eseguire l'analisi dell'intero contenuto dei file VHD e VHDX durante l'allegato.

Questa configurazione rimuove solo la scansione dei file VHD e VHDX durante l'allegato, ma non influisce sull'analisi in tempo reale.

Per istruzioni più dettagliate su come configurare Windows Defender in Windows Server, vedere [Configurare le esclusioni antivirus](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/)di Windows Defender in Windows Server.

Per ulteriori informazioni su come configurare Windows Defender per escludere determinati file dall'analisi, vedere [Configurare e convalidare le esclusioni in base all'estensione e](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)al percorso della cartella .

### <a name="disable-automatic-updates"></a>Disabilitare Aggiornamenti automatici

Per disattivare Aggiornamenti automatici tramite Criteri di gruppo locali:

1. Aprire **Modelli\\\\amministrativi dell'Editor Criteri di gruppo locali Componenti\\**di Windows Aggiornamento .
2. Fare clic con il pulsante destro del mouse su **Configura aggiornamento automatico** e impostarlo su **Disabilitato**.

È inoltre possibile eseguire il comando seguente al prompt dei comandi per disabilitare Aggiornamenti automatici.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Specificare il layout Start per i PC Windows 10 (facoltativo)

Eseguire questo comando per specificare un layout di schermata Start per i PC Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurare il reindirizzamento del fuso orario

Il reindirizzamento del fuso orario può essere applicato a livello di Criteri di gruppo poiché tutte le macchine virtuali in un pool host fanno parte dello stesso gruppo di sicurezza.

Per reindirizzare i fusi orari:

1. Sul server Active Directory, aprire la **Console Gestione Criteri di gruppo**.
2. Espandere il dominio e gli oggetti Criteri di gruppo.
3. Fare clic con il pulsante destro del mouse **sull'oggetto Criteri di gruppo** creato per le impostazioni di Criteri di gruppo e scegliere **Modifica**.
4. Nell'Editor **Gestione Criteri di gruppo**passare a Modelli**amministrativi** > **Criteri** >  **di configurazione** > computer Componenti di**Windows Componenti** > **Desktop remoto Servizi** > **Desktop remoto Dispositivo** > host sessione Desktop remoto**e Reindirizzamento risorse**.
5. Abilitare l'impostazione **Consenti reindirizzamento fuso orario.**

È inoltre possibile eseguire questo comando sull'immagine master per reindirizzare i fusi orari:You can also run this command on the master image to redirect time zones:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Disattiva senso di archiviazione

Per l'host di sessione di Desktop virtuale di Windows che usa Windows 10 Enterprise o Windows 10 Enterprise multisessione, è consigliabile disabilitare Storage Sense. È possibile disabilitare Storage Sense nel menu Impostazioni in **Archiviazione,** come illustrato nella schermata seguente:

![Schermata del menu Archiviazione in Impostazioni. L'opzione "Senso di archiviazione" è disattivata.](media/storagesense.png)

È inoltre possibile modificare l'impostazione con il Registro di sistema eseguendo il comando seguente:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Includere un supporto linguistico aggiuntivo

In questo articolo non viene illustrato come configurare il supporto della lingua e delle aree internazionali. Per altre informazioni, vedere gli articoli seguenti:

- [Aggiungere lingue alle immagini di Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Funzionalità su richiesta](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Funzionalità di lingua e area geografica su richiesta (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Altre applicazioni e configurazione del Registro di sistema

In questa sezione viene illustrata la configurazione dell'applicazione e del sistema operativo. Tutta la configurazione in questa sezione viene eseguita tramite le voci del Registro di sistema che possono essere eseguite dalla riga di comando e dagli strumenti regedit.

>[!NOTE]
>È possibile implementare le procedure consigliate nella configurazione con gli oggetti Criteri di gruppo (GPO) o le importazioni del Registro di sistema. L'amministratore può scegliere una delle due opzioni in base ai requisiti dell'organizzazione.

Per la raccolta hub di commenti e suggerimenti dei dati di telemetria in Windows 10 Enterprise multisessione, eseguire questo comando:For feedback hub collection of telemetry data on Windows 10 Enterprise multi-session, run this command:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Eseguire il comando seguente per correggere gli arresti anomali di Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Immettere i seguenti comandi nell'editor del Registro di sistema per risolvere il supporto della risoluzione 5k. È necessario eseguire i comandi prima di poter abilitare lo stack side-by-side.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Preparare l'immagine per il caricamento in AzurePrepare the image for upload to Azure

Dopo aver completato la configurazione e installato tutte le applicazioni, seguire le istruzioni in [Preparare un disco rigido virtuale di Windows o un disco rigido virtuale per caricare in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) per preparare l'immagine.

Dopo aver preparato l'immagine per il caricamento, assicurarsi che la macchina virtuale rimanga nello stato off o deallocato.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Caricare un'immagine master in un account di archiviazione in AzureUpload master image to a storage account in Azure

Questa sezione si applica solo quando l'immagine master è stata creata localmente.

Le istruzioni seguenti illustrano come caricare l'immagine master in un account di archiviazione di Azure.The following instructions will tell you how to upload your master image into an Azure storage account. Se non si ha già un account di archiviazione di Azure, seguire le istruzioni in [questo articolo](/azure/javascript/tutorial-vscode-static-website-node-03) per crearne uno.

1. Convertire l'immagine della macchina virtuale (VHD) in Risolto, se non è già stato fatto. Se non converti l'immagine in Fisso, non puoi crearla correttamente.

2. Caricare il disco rigido virtuale in un contenitore BLOB nell'account di archiviazione. È possibile caricare rapidamente con lo [strumento Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Per altre informazioni sullo strumento Storage Explorer, vedere [questo articolo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Schermata della finestra di ricerca dello strumento Esplora archivi di Microsoft Azure. La casella di controllo "Carica file con estensione vhd o vhdx come BLOB di pagina (scelta consigliata)" è selezionata.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Passare quindi al portale di Azure nel browser e cercare "Immagini". La ricerca dovrebbe condurti alla pagina **Crea immagine,** come mostrato nella schermata seguente:

    ![Schermata della pagina Crea immagine del portale di Azure, con valori di esempio per l'immagine.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Una volta creata l'immagine, dovresti vedere una notifica simile a quella nella schermata seguente:

    ![Cattura di schermata della notifica "immagine creata con successo".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un'immagine, è possibile creare o aggiornare i pool host. Per altre informazioni su come creare e aggiornare i pool host, vedere gli articoli seguenti:To learn more about how to create and update host pools, see the following articles:

- [Creare un pool di host con un modello di Azure Resource Manager](create-host-pools-arm-template.md)
- [Esercitazione: Creare un pool host con Azure MarketplaceTutorial: Create a host pool with Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Creare un pool di host con PowerShell](create-host-pools-powershell.md)
- [Creare un contenitore di profili per un pool di host con una condivisione file](create-host-pools-user-profile.md)
- [Configurare il metodo di bilanciamento del carico di Desktop virtuale Windows](configure-host-pool-load-balancing.md)

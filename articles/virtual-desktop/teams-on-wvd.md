---
title: Microsoft teams sul desktop virtuale Windows-Azure
description: Come usare Microsoft teams sul desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 11/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 101b3a05591a7815ba28756bb5b07e855b64e769
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505547"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Usare Microsoft teams sul desktop virtuale di Windows

>[!IMPORTANT]
>L'ottimizzazione dei supporti per i team è supportata per gli ambienti Microsoft 365 Government (GCC). L'ottimizzazione dei supporti per i team non è supportata per GCC-High o DoD.

>[!NOTE]
>L'ottimizzazione dei supporti per Microsoft teams è disponibile solo per il client desktop di Windows nei computer Windows 10. Per le ottimizzazioni dei supporti è necessario Windows desktop client versione 1.2.1026.0 o successiva.

Microsoft teams sul desktop virtuale Windows supporta la chat e la collaborazione. Con le ottimizzazioni dei supporti, supporta anche la funzionalità di chiamata e riunione. Per altre informazioni su come usare Microsoft teams in ambienti Virtual Desktop Infrastructure (VDI), vedere [Teams for virtualizzated Desktop Infrastructure](/microsoftteams/teams-for-vdi/).

Con l'ottimizzazione multimediale per Microsoft teams, il client desktop di Windows gestisce audio e video localmente per le chiamate e le riunioni dei team. È comunque possibile utilizzare Microsoft teams su desktop virtuale Windows con altri client senza chiamate e riunioni ottimizzate. Le funzionalità chat team e collaborazione sono supportate in tutte le piattaforme. Per reindirizzare i dispositivi locali nella sessione remota, vedere [personalizzare Remote Desktop Protocol proprietà di un pool host](#customize-remote-desktop-protocol-properties-for-a-host-pool).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter usare Microsoft teams sul desktop virtuale di Windows, è necessario eseguire queste operazioni:

- [Preparare la rete](/microsoftteams/prepare-network/) per Microsoft teams.
- Installare il [client desktop di Windows](connect-windows-7-10.md) in un dispositivo Windows 10 o Windows 10 Internet che soddisfi i requisiti hardware per i team di Microsoft teams [in un computer Windows](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/).
- Connettersi a una macchina virtuale (VM) di Windows 10 multisessione o Windows 10 Enterprise.

## <a name="install-the-teams-desktop-app"></a>Installare l'app desktop per i team

In questa sezione viene illustrato come installare l'app desktop per i team nell'immagine di macchina virtuale Windows 10 multisessione o Windows 10 Enterprise. Per altre informazioni, vedere [Install or Update the teams desktop app on VDI](/microsoftteams/teams-for-vdi#install-or-update-the-teams-desktop-app-on-vdi).

### <a name="prepare-your-image-for-teams"></a>Preparare l'immagine per i team

Per abilitare l'ottimizzazione dei supporti per i team, impostare la seguente chiave del registro di sistema nell'host:

1. Dal menu Start eseguire **Regedit** come amministratore. Passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams**. Creare la chiave teams se non esiste già.

2. Creare il valore seguente per la chiave teams:

| Nome             | Tipo   | Dati/valore  |
|------------------|--------|-------------|
| IsWVDEnvironment | DWORD  | 1           |

### <a name="install-the-teams-websocket-service"></a>Installare il servizio WebSocket Teams

Installare la versione più recente di [Desktop remoto il servizio redirector WebRTC](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE4AQBt) nell'immagine di macchina virtuale. Se si verifica un errore di installazione, installare la [versione più recente di Microsoft Visual C++ ridistribuibile](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) e riprovare.

#### <a name="latest-websocket-service-versions"></a>Versioni più recenti del servizio WebSocket

La tabella seguente elenca le versioni più recenti del servizio WebSocket:

|Versione        |Data di rilascio  |
|---------------|--------------|
|1.0.2006.11001 |07/28/2020    |
|0.11.0         |05/29/2020    |

#### <a name="updates-for-version-10200611001"></a>Aggiornamenti per la versione 1.0.2006.11001

- È stato risolto un problema per cui la riduzione dell'app team durante una chiamata o una riunione causava il calo del video in arrivo.
- Aggiunta del supporto per la selezione di un monitoraggio da condividere nelle sessioni desktop con più monitor.

### <a name="install-microsoft-teams"></a>Installare Microsoft Teams

È possibile distribuire l'app desktop per i team usando un'installazione per computer o per utente. Per installare Microsoft teams nell'ambiente desktop virtuale Windows:

1. Scaricare il [pacchetto MSI teams](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) corrispondente all'ambiente in uso. È consigliabile usare il programma di installazione a 64 bit in un sistema operativo a 64 bit.

      > [!IMPORTANT]
      > L'aggiornamento più recente della versione del client desktop dei team 1.3.00.21759 risolto un problema per cui i team mostravano il fuso orario UTC in chat, Channels e Calendar. La nuova versione del client visualizzerà il fuso orario della sessione remota.

2. Eseguire uno dei comandi seguenti per installare il file MSI nella VM host:

    - Installazione per utente

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name>
        ```

        Questo processo è l'installazione predefinita, che installa i team nella cartella **% AppData%** User. I team non funzioneranno correttamente con l'installazione per utente in un'installazione non permanente.

    - Installazione per computer

        ```powershell
        msiexec /i <path_to_msi> /l*v <install_logfile_name> ALLUSER=1
        ```

        In questo modo, i team vengono installati nella cartella Program Files (x86) in un sistema operativo a 32 bit e nella cartella programmi in un sistema operativo a 64 bit. A questo punto, la configurazione dell'immagine dorata è stata completata. L'installazione di team per computer è necessaria per le configurazioni non permanenti.

        Sono disponibili due flag che è possibile impostare quando si installano i team, **ALLUSER = 1** e **ALLUSERS = 1**. È importante comprendere la differenza tra questi parametri. Il parametro **ALLUSER = 1** viene usato solo negli ambienti VDI per specificare un'installazione per computer. Il parametro **ALLUSERS = 1** può essere usato in ambienti non VDI e VDI. Quando si imposta questo parametro, il programma di **installazione teams Machine-Wide** viene visualizzato in programma e funzionalità nel pannello di controllo, nonché nelle app & funzionalità nelle impostazioni di Windows. Tutti gli utenti con credenziali di amministratore nel computer possono disinstallare i team.

        > [!NOTE]
        > Gli utenti e gli amministratori non possono disabilitare l'avvio automatico per i team durante l'accesso in questo momento.

3. Per disinstallare MSI dalla macchina virtuale host, eseguire questo comando:

      ```powershell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      Questa operazione Disinstalla i team dalla cartella Program Files (x86) o Program Files, a seconda dell'ambiente del sistema operativo.

      > [!NOTE]
      > Quando si installano i team con l'impostazione MSI ALLUSER = 1, gli aggiornamenti automatici saranno disabilitati. È consigliabile assicurarsi di aggiornare i team almeno una volta al mese. Per altre informazioni sulla distribuzione dell'app desktop per i team, vedere [distribuire l'app desktop per i team nella macchina virtuale](/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm/).

### <a name="verify-media-optimizations-loaded"></a>Verificare le ottimizzazioni del supporto caricate

Dopo aver installato il servizio WebSocket e l'app desktop teams, attenersi alla procedura seguente per verificare che siano state caricate le ottimizzazioni dei supporti Team:

1. Chiudere e riavviare l'applicazione teams.

2. Selezionare l'immagine del profilo utente e quindi fare clic **su informazioni su**.

3. Selezionare la **versione**.

      Se le ottimizzazioni dei supporti sono state caricate, il banner visualizzerà **Wvd media optimized**. Se il banner mostra che i **supporti Wvd non sono connessi** , chiudere l'app teams e riprovare.

4. Selezionare l'immagine del profilo utente e quindi selezionare **Impostazioni**.

      Se le ottimizzazioni del supporto sono state caricate, i dispositivi audio e le fotocamere disponibili localmente verranno enumerati nel menu dispositivo. Se il menu Mostra **audio remoto** , chiudere l'app teams e riprovare. Se i dispositivi non vengono ancora visualizzati nel menu, verificare le impostazioni di privacy nel PC locale. Verificare che le **Settings**  >  **Privacy**  >  **autorizzazioni** per l'app privacy dell'impostazione **Consenti alle app di accedere al microfono** siano **On** attivate. Disconnettersi dalla sessione remota, quindi riconnettersi e controllare di nuovo i dispositivi audio e video. Per partecipare a chiamate e riunioni con video, è inoltre necessario concedere l'autorizzazione per l'accesso delle app alla fotocamera.

      Se le ottimizzazioni non vengono caricate, disinstallare e reinstallare i team e verificare di nuovo.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

L'utilizzo di team in un ambiente virtualizzato è diverso dall'utilizzo di team in un ambiente non virtualizzato. Per ulteriori informazioni sulle limitazioni dei team negli ambienti virtualizzati, consultare i [team per l'infrastruttura desktop virtualizzata](/microsoftteams/teams-for-vdi#known-issues-and-limitations).

### <a name="client-deployment-installation-and-setup"></a>Distribuzione, installazione e configurazione del client

- Con l'installazione per computer, i team su VDI non vengono aggiornati automaticamente nello stesso modo in cui sono i client di team non VDI. Per aggiornare il client, è necessario aggiornare l'immagine di macchina virtuale installando un nuovo file MSI.
- L'ottimizzazione dei supporti per i team è supportata solo per il client desktop di Windows nei computer che eseguono Windows 10.
- L'utilizzo di proxy HTTP espliciti definiti in un endpoint non è supportato.

### <a name="calls-and-meetings"></a>Chiamate e riunioni

- Il client desktop dei team negli ambienti desktop virtuali Windows non supporta gli eventi live. Per il momento, è consigliabile unire gli eventi live dal [client Web teams](https://teams.microsoft.com) nella sessione remota.
- Le chiamate o le riunioni non supportano attualmente la condivisione delle applicazioni. Le sessioni desktop supportano la condivisione desktop.
- Dare al controllo e assumere il controllo non sono attualmente supportati.
- I team nel desktop virtuale Windows supportano solo un input video in entrata alla volta. Ciò significa che ogni volta che un utente tenta di condividere lo schermo, viene visualizzata la schermata invece della schermata del leader della riunione.
- A causa delle limitazioni di WebRTC, la risoluzione del flusso video in entrata e in uscita è limitata a 720p.
- L'app teams non supporta i pulsanti nascosti o i controlli LED con altri dispositivi.

Per i team problemi noti che non sono correlati agli ambienti virtualizzati, vedere [team di supporto all'interno dell'organizzazione](/microsoftteams/known-issues)

## <a name="uservoice-site"></a>Sito di UserVoice

Inviare commenti e suggerimenti per Microsoft teams su desktop virtuale Windows nel sito teams [UserVoice](https://microsoftteams.uservoice.com/).

## <a name="collect-teams-logs"></a>Raccogli log dei team

Se si verificano problemi con l'app desktop dei team nell'ambiente desktop virtuale di Windows, raccogliere i log client in **% AppData% \Microsoft\Teams\logs.txt** nella macchina virtuale host.

Se si verificano problemi relativi alle chiamate e alle riunioni, raccogliere i registri dei client Web di team con la combinazione di tasti **CTRL**  +  **ALT**  +  **MAIUSC**  +  **1**. I log verranno scritti nel **log di diagnostica%userprofile%\Downloads\MSTeams DATE_TIME.txt** nella VM host.

## <a name="contact-microsoft-teams-support"></a>Contatta il supporto tecnico Microsoft Teams

Per contattare il supporto Microsoft teams, passare all'interfaccia di [amministrazione di Microsoft 365](/microsoft-365/admin/contact-support-for-business-products).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Personalizzare le proprietà Remote Desktop Protocol per un pool host

La personalizzazione delle proprietà di Remote Desktop Protocol (RDP) di un pool host, ad esempio l'esperienza a più monitor o l'abilitazione del reindirizzamento audio e microfono, consente di offrire un'esperienza ottimale agli utenti in base alle esigenze.

L'abilitazione dei reindirizzamenti del dispositivo non è necessaria quando si usano i team con l'ottimizzazione del supporto. Se si usano i team senza ottimizzazione dei supporti, impostare le proprietà RDP seguenti per abilitare il reindirizzamento del microfono e della fotocamera:

- `audiocapturemode:i:1` Abilita l'acquisizione audio dal dispositivo locale e reindirizza le applicazioni audio nella sessione remota.
- `audiomode:i:0` riproduce l'audio nel computer locale.
- `camerastoredirect:s:*` reindirizza tutte le fotocamere.

Per altre informazioni, vedere [personalizzare le proprietà Remote Desktop Protocol per un pool host](customize-rdp-properties.md).

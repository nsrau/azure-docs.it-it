---
title: Risolvere problemi
description: Informazioni sulla risoluzione dei problemi per il rendering remoto di AzureTroubleshooting information for Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: ac7e721a863414cf0617177885e0ff1c9e9a35d4
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617863"
---
# <a name="troubleshoot"></a>Risolvere problemi

In questa pagina sono elencati i problemi comuni che interferiscono con il rendering remoto di Azure e i modi per risolverli.

## <a name="client-cant-connect-to-server"></a>Il client non riesce a connettersi al server

Assicurati che i firewall (sul dispositivo, all'interno dei router, ecc.) non blocchino le seguenti porte:

* **50051 (TCP)** - necessario per la connessione iniziale (handshake HTTP)
* **8266 (TCP-UDP)** - necessario per il trasferimento dei dati
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - necessario per [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Errore 'Disconnesso: VideoFormatNotAvailable'

Verifica che la GPU supporti la decodifica video hardware. Consultate [Sviluppo PC](../overview/system-requirements.md#development-pc).

Se si lavora su un computer portatile con due GPU, è possibile che la GPU in esecuzione per impostazione predefinita, non fornisce funzionalità di decodifica video hardware. In tal caso, prova a forzare l'app a usare l'altra GPU. Questo è spesso possibile nelle impostazioni del driver GPU.

## <a name="h265-codec-not-available"></a>Codec H265 non disponibile

Esistono due motivi per cui il server potrebbe rifiutarsi di connettersi con un errore di **codec non disponibile.**

**Il codec H265 non è installato:**

Per prima cosa assicurati di installare le **estensioni video HEVC** come indicato nella sezione [Software](../overview/system-requirements.md#software) dei requisiti di sistema.

Se i problemi persistono, assicurarsi che la scheda grafica supporti H265 e che sia installato il driver grafico più recente. Per informazioni specifiche sul fornitore, vedere la sezione [Development PC](../overview/system-requirements.md#development-pc) dei requisiti di sistema.

**Il codec è installato, ma non può essere utilizzato:**

Il motivo di questo problema è un'impostazione di protezione non corretta sulle DLL. Questo problema non si manifesta quando si tenta di guardare i video codificati con H265. Reinstallato il codec non risolve il problema. Al contrario, eseguire la procedura seguente:

1. Aprire una **PowerShell con diritti di amministratore** ed eseguireOpen a PowerShell with admin rights and run

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Questo comando dovrebbe `InstallLocation` restituire il codec, qualcosa come:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Aprire la cartella in Esplora risorse
1. Dovrebbero essere presenti una sottocartella **x86** e **x64.** Fare clic con il pulsante destro del mouse su una delle cartelle e scegliere **Proprietà**
    1. Selezionare la scheda **Sicurezza** e fare clic sul pulsante **Impostazioni avanzate**
    1. Fare clic su **Cambia** per il **proprietario**
    1. Digitare **Administrators** nel campo di testo
    1. Fare clic su **Controlla nomi** e **OK**
1. Ripetere i passaggi precedenti per l'altra cartella
1. Ripetere anche i passaggi precedenti in ogni file DLL all'interno di entrambe le cartelle. Dovrebbero essere presenti complessivamente quattro DLL.

Per verificare che le impostazioni siano corrette, eseguire questa operazione per ognuna delle quattro DLL:

1. Selezionare **Proprietà > Modifica > protezione**
1. Scorrere l'elenco di tutti i **gruppi / utenti** e assicurarsi che ognuno abbia il Read & **Execute** right impostato (il segno di spunta nella colonna **Allow** deve essere spuntato)

## <a name="low-video-quality"></a>Bassa qualità video

La qualità video può essere compromessa dalla qualità della rete o dal codec video H265 mancante.

* Vedere la procedura per identificare i problemi di [rete](#unstable-holograms).
* Consultare [i requisiti](../overview/system-requirements.md#development-pc) di sistema per l'installazione del driver di grafica più recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Il video registrato con MRC non riflette la qualità dell'esperienza dal vivo

Un video può essere registrato su Hololens attraverso [Mixed Reality Capture (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Tuttavia il video risultante ha una qualità peggiore rispetto all'esperienza dal vivo per due motivi:
* Il framerate video è limitato a 30 Hz invece di 60 Hz.
* Le immagini video non passano attraverso la fase di elaborazione della [riproiezione](../overview/features/late-stage-reprojection.md) in fase avanzata, quindi il video sembra essere più inverso.

Entrambi sono limitazioni intrinseche della tecnica di registrazione.

## <a name="black-screen-after-successful-model-loading"></a>Schermo nero dopo il caricamento del modello

Se si è connessi al runtime di rendering e si è caricato correttamente un modello, ma in seguito viene visualizzata solo una schermata nera, ciò può avere alcune cause distinte.

Prima di eseguire un'analisi più approfondita, è consigliabile testare le operazioni seguenti:We recommend testing the following things before doing a more in-depth analysis:

* Il codec H265 è installato? Anche se dovrebbe esserci un fallback al codec H264, abbiamo visto casi in cui questo fallback non ha funzionato correttamente. Consultare [i requisiti](../overview/system-requirements.md#development-pc) di sistema per l'installazione del driver di grafica più recente.
* Quando si utilizza un progetto Unity, chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/compilare nuovamente il progetto. In alcuni casi i dati memorizzati nella cache hanno causato il funzionamento non corretto dell'esempio senza un motivo ovvio.

Se questi due passaggi non sono stati utili, è necessario scoprire se i fotogrammi video vengono ricevuti dal client o meno. È possibile eseguire query a livello di codice, come illustrato nel capitolo [Query sulle prestazioni lato server.](../overview/features/performance-queries.md) Ha `FrameStatistics struct` un membro che indica quanti fotogrammi video sono stati ricevuti. Se questo numero è maggiore di 0 e aumenta nel tempo, il client riceve fotogrammi video effettivi dal server. Di conseguenza deve trattarsi di un problema sul lato client.

### <a name="common-client-side-issues"></a>Problemi comuni sul lato client

**Il modello non è all'interno del frustum di visualizzazione:**

In molti casi, il modello viene visualizzato correttamente ma si trova al di fuori del frustum della fotocamera. Un motivo comune è che il modello è stato esportato con un perno lontano dal centro in modo che venga ritagliato dal piano di ritaglio lontano della videocamera. È utile eseguire una query sul riquadro di delimitazione del modello a livello di codice e visualizzare la casella con Unity come una casella di riga o stamparne i valori nel registro di debug.

**La pipeline di rendering Unity non include gli hook di rendering:The Unity render pipeline doesn't include the render hooks:**

Il rendering remoto di Azure esegue l'hook nella pipeline di rendering Unity per eseguire la composizione dei fotogrammi con il video e per eseguire la riproiezione. Per verificare l'esistenza di questi hook, aprire il menu *Window > Analysis > Frame debugger*. Abilitarlo e assicurarsi che siano `HolographicRemotingCallbackPass` presenti due voci per la pipeline:Enable it and make sure there are two entries for the in the pipeline:

![Debugger telaio Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Il codice Unity che usa l'API di rendering remoto non viene compilato

Impostare il tipo di *compilazione* della soluzione Unity su **Debug**. Durante il test di ARR `UNITY_EDITOR` nell'editor di Unity, la definizione è disponibile solo nelle compilazioni 'Debug'. Si noti che ciò non è correlato al tipo di compilazione utilizzato per le [applicazioni distribuite](../quickstarts/deploy-to-hololens.md), in cui è necessario preferire le build 'Release'.

## <a name="unstable-holograms"></a>Ologrammi instabili

Nel caso in cui gli oggetti sottoposti a rendering sembrino muoversi insieme ai movimenti della testa, è possibile che si verifichino problemi con *la riproiezione* a stadio avanzato (LSR). Fare riferimento alla sezione sulla [riproiezione a stadio superiore](../overview/features/late-stage-reprojection.md) per indicazioni su come affrontare una situazione di questo tipo.

Un altro motivo per gli ologrammi instabili (wobbling, deformazione, jittering o jumping olograms) può essere una scarsa connettività di rete, in particolare una larghezza di banda di rete insufficiente o una latenza troppo elevata. Un buon indicatore per la qualità della connessione `ARRServiceStats.VideoFramesReused`di rete è il valore [delle statistiche sulle prestazioni.](../overview/features/performance-queries.md) I fotogrammi riutilizzati indicano situazioni in cui un vecchio fotogramma video doveva essere riutilizzato sul lato client perché non era disponibile alcun nuovo fotogramma video, ad esempio a causa della perdita di pacchetti o a causa di variazioni nella latenza di rete. Se `ARRServiceStats.VideoFramesReused` spesso è maggiore di zero, ciò indica un problema di rete.

Un altro valore `ARRServiceStats.LatencyPoseToReceiveAvg`da considerare è . Deve essere costantemente al di sotto di 100 ms. Se vengono visualizzati valori più elevati, significa che si è connessi a un data center troppo lontano.

Per un elenco delle potenziali attenuazioni, vedere le linee guida per la [connettività](../reference/network-requirements.md#guidelines-for-network-connectivity)di rete .

## <a name="next-steps"></a>Passaggi successivi

* [Requisiti di sistema](../overview/system-requirements.md)
* [Requisiti di rete](../reference/network-requirements.md)

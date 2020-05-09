---
title: Risolvere problemi
description: Informazioni sulla risoluzione dei problemi per il rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: c1b807c6e4fa269ac2ab8d7eacd3ca1d4f81a1ca
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792616"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questa pagina elenca i problemi comuni che interferiscono con il rendering remoto di Azure e i modi per risolverli.

## <a name="client-cant-connect-to-server"></a>Il client non è in grado di connettersi al server

Assicurarsi che i firewall (sul dispositivo, all'interno dei router e così via) non blocchino le seguenti porte:

* **50051 (TCP)** : obbligatorio per la connessione iniziale (handshake http)
* **8266 (TCP + UDP)** : obbligatorio per il trasferimento dei dati
* **5000 (** TCP), **5433 (tcp)**, **8443 (TCP** )-obbligatorio per [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Errore ' disconnected: VideoFormatNotAvailable '

Verificare che la GPU supporti la decodifica video hardware. Vedere [PC di sviluppo](../overview/system-requirements.md#development-pc).

Se si lavora su un computer portatile con due GPU, è possibile che la GPU su cui si esegue per impostazione predefinita non fornisca funzionalità di decodifica video hardware. In tal caso, provare a forzare l'uso dell'altra GPU nell'app. Questa operazione è spesso possibile nelle impostazioni del driver della GPU.

## <a name="h265-codec-not-available"></a>Il codec H265 non è disponibile

Esistono due motivi per cui il server potrebbe rifiutare la connessione con un errore di **codec non disponibile** .

**Il codec H265 non è installato:**

Assicurarsi prima di tutto di installare le **estensioni video HEVC** come indicato nella sezione [software](../overview/system-requirements.md#software) dei requisiti di sistema.

Se si verificano ancora problemi, verificare che la scheda grafica supporti H265 e che sia installato il driver grafico più recente. Vedere la sezione [computer di sviluppo](../overview/system-requirements.md#development-pc) dei requisiti di sistema per informazioni specifiche del fornitore.

**Il codec è installato, ma non può essere usato:**

Il motivo di questo problema è un'impostazione di sicurezza non corretta per le dll. Questo problema non si manifesta quando si tenta di guardare video codificati con H265. Per reinstallare il codec non è possibile risolvere il problema. Eseguire invece i passaggi seguenti:

1. Aprire una **PowerShell con diritti di amministratore** ed eseguire

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Il comando deve restituire il `InstallLocation` valore del codec, ad esempio:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Apri la cartella in Esplora risorse
1. Devono essere presenti una sottocartella **x86** e **x64** . Fare clic con il pulsante destro del mouse su una delle cartelle e scegliere **Proprietà** .
    1. Selezionare la scheda **sicurezza** e fare clic sul pulsante impostazioni **Avanzate**
    1. Fare clic su **Cambia** per il **proprietario**
    1. Digitare gli **amministratori** nel campo di testo
    1. Fare clic su **Controlla nomi** e **OK**
1. Ripetere i passaggi precedenti per l'altra cartella
1. Ripetere anche i passaggi precedenti in ogni file DLL all'interno di entrambe le cartelle. Dovrebbero essere presenti quattro dll completamente.

Per verificare che le impostazioni siano corrette, eseguire questa operazione per ognuna delle quattro dll:

1. Selezionare le **proprietà > sicurezza > modifica**
1. Scorrere l'elenco di tutti i **gruppi e gli utenti** e verificare che ognuno disponga del set di diritti di **lettura & Execute** (il segno di spunta nella colonna **Consenti** deve essere selezionato)

## <a name="low-video-quality"></a>Qualità video ridotta

La qualità del video può essere compromessa dalla qualità della rete o dal codec video H265 mancante.

* Vedere i passaggi per [identificare i problemi di rete](#unstable-holograms).
* Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver grafico più recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Il video registrato con MRC non riflette la qualità dell'esperienza Live

Un video può essere registrato in Hololens tramite l' [acquisizione di realtà mista (MRC)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Il video risultante presenta tuttavia una qualità peggiore rispetto all'esperienza Live per due motivi:
* Il framerate video è limitato a 30 Hz rispetto a 60 Hz.
* Le immagini video non passano attraverso il passaggio di elaborazione della [riproiezione della fase ritardata](../overview/features/late-stage-reprojection.md) , quindi il video sembra essere choppier.

Entrambi sono limitazioni intrinseche della tecnica di registrazione.

## <a name="black-screen-after-successful-model-loading"></a>Schermata nera dopo il caricamento del modello completato

Se si è connessi al runtime di rendering e un modello è stato caricato correttamente, ma in seguito viene visualizzata una schermata nera, questo può avere alcune cause distinte.

Prima di eseguire un'analisi più approfondita, è consigliabile testare gli elementi seguenti:

* Il codec H265 è installato? Sebbene esista un fallback per il codec H264, abbiamo riscontrato casi in cui questo fallback non funzionasse correttamente. Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver grafico più recente.
* Quando si usa un progetto Unity, chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/compilare nuovamente il progetto. In alcuni casi, i dati memorizzati nella cache hanno impedito il corretto funzionamento dell'esempio per nessun motivo ovvio.

Se questi due passaggi non sono utili, è necessario verificare se i fotogrammi video sono ricevuti o meno dal client. Questo può essere sottoposto a query a livello di codice, come illustrato nel capitolo [query sulle prestazioni lato server](../overview/features/performance-queries.md) . `FrameStatistics struct` Dispone di un membro che indica il numero di fotogrammi video ricevuti. Se questo numero è maggiore di 0 e aumenta nel tempo, il client riceve i fotogrammi video effettivi dal server. Di conseguenza, deve essere un problema sul lato client.

### <a name="common-client-side-issues"></a>Problemi comuni lato client

**Il modello supera i limiti della macchina virtuale selezionata, in particolare il numero massimo di poligoni:**

Vedere limitazioni specifiche delle [dimensioni della macchina virtuale](../reference/limits.md#overall-number-of-polygons).

**Il modello non si trova all'interno della vista tronco:**

In molti casi, il modello viene visualizzato correttamente ma si trova al di fuori della fotocamera tronco. Un motivo comune è che il modello è stato esportato con un pivot esterno al centro, in modo che venga ritagliato dal piano di ritaglio a distanza della fotocamera. Consente di eseguire una query del rettangolo di delimitazione del modello a livello di codice e di visualizzare la casella Unity come una casella a linee o di stamparne i valori nel log di debug.

Inoltre, il processo di conversione genera un [file JSON di output](../how-tos/conversion/get-information.md) insieme al modello convertito. Per eseguire il debug dei problemi di posizionamento del modello, vale `boundingBox` la pena esaminare la voce nella [sezione outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Il rettangolo di delimitazione viene descritto come `min` una `max` posizione e nello spazio 3D, in metri. Quindi una coordinata di 1000,0 significa che è a 1 chilometro dall'origine.

Potrebbero esserci due problemi con questo rettangolo di delimitazione che portano alla geometria invisibile:
* **La casella può essere distante dal centro**, quindi l'oggetto viene troncato completamente a causa del ritaglio del piano a distanza. I `boundingBox` valori in questo caso avranno un aspetto simile al `min = [-2000, -5,-5], max = [-1990, 5,5]`seguente:, usando un offset grande sull'asse x come esempio qui. Per risolvere questo tipo di problema, abilitare l' `recenterToOrigin` opzione nella [configurazione di conversione del modello](../how-tos/conversion/configure-model-conversion.md).
* **La casella può essere centrata, ma avere un ordine di grandezza troppo grande**. Ciò significa che, anche se la fotocamera viene avviata al centro del modello, la relativa geometria viene ritagliata in tutte le direzioni. In `boundingBox` questo caso i valori tipici sono simili ai seguenti `min = [-1000,-1000,-1000], max = [1000,1000,1000]`:. Il motivo di questo tipo di problema è in genere una mancata corrispondenza di scalabilità delle unità. Per compensare, specificare un [valore di ridimensionamento durante la conversione](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) o contrassegnare il modello di origine con le unità corrette. La scalabilità può essere applicata anche al nodo radice quando si carica il modello in fase di esecuzione.

**La pipeline di rendering Unity non include gli hook di rendering:**

Hook di rendering remoto di Azure nella pipeline di rendering Unity per eseguire la composizione del frame con il video e per eseguire la riproiezione. Per verificare che questi hook esistano, aprire la finestra di menu *> Analysis > frame debugger*. Abilitarla e verificare che siano presenti due voci per la `HolographicRemotingCallbackPass` nella pipeline:

![Debugger frame Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Il codice Unity che usa l'API per il rendering remoto non viene compilato

### <a name="use-debug-when-compiling-for-unity-editor"></a>Usare debug durante la compilazione per l'editor di Unity

Cambiare il *tipo di compilazione* della soluzione Unity di cui eseguire il **debug**. Quando si esegue il test di ARR nell'editor `UNITY_EDITOR` di Unity, l'oggetto define è disponibile solo nelle compilazioni ' debug '. Si noti che questo non è correlato al tipo di compilazione usato per [le applicazioni distribuite](../quickstarts/deploy-to-hololens.md), in cui è preferibile eseguire le compilazioni "release".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Errori di compilazione quando si compilano esempi di Unity per HoloLens 2

Sono stati rilevati errori non corretti durante il tentativo di compilare esempi di Unity (avvio rapido, ShowCaseApp,..) per HoloLens 2. Visual Studio non è in grado di copiare alcuni file anche se sono presenti. Se si raggiunge questo problema:
* Rimuovere tutti i file Unity temporanei dal progetto e riprovare.
* Assicurarsi che i progetti si trovino in una directory su disco con un percorso ragionevolmente breve, perché il passaggio di copia talvolta sembra riscontrare problemi con nomi di file lunghi.
* Se ciò non è utile, è possibile che MS Sense interferisca con il passaggio di copia. Per impostare un'eccezione, eseguire questo comando del registro di sistema dalla riga di comando (sono necessari i diritti di amministratore):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Ologrammi instabili

Se gli oggetti di cui è stato eseguito il rendering sembrano essere spostati insieme ai movimenti Head, è possibile che si verifichino problemi con la *riproiezione in ritardo della fase* (LSR). Per istruzioni su come affrontare una situazione di questo tipo, fare riferimento alla sezione relativa alla [riproiezione tardiva delle fasi](../overview/features/late-stage-reprojection.md) .

Un altro motivo per gli ologrammi instabili (oscillazione, distorsione, jittering o ologrammi di salto) può essere una connettività di rete insufficiente, in particolare una larghezza di banda di rete insufficiente o una latenza troppo elevata. Un buon indicatore per la qualità della connessione di rete è il valore `ARRServiceStats.VideoFramesReused`delle [statistiche sulle prestazioni](../overview/features/performance-queries.md) . I frame riutilizzati indicano situazioni in cui è necessario riutilizzare un fotogramma video precedente sul lato client perché non è disponibile alcun nuovo frame video, ad esempio a causa di una perdita di pacchetti o a causa di variazioni nella latenza di rete. Se `ARRServiceStats.VideoFramesReused` è di frequente maggiore di zero, ciò indica un problema di rete.

Un altro valore da esaminare è `ARRServiceStats.LatencyPoseToReceiveAvg`. Deve essere costantemente inferiore a 100 ms. Se vengono visualizzati valori più alti, significa che si è connessi a un data center troppo lontano.

Per un elenco delle possibili mitigazioni, vedere le [linee guida per la connettività di rete](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Passaggi successivi

* [Requisiti di sistema](../overview/system-requirements.md)
* [Requisiti di rete](../reference/network-requirements.md)

---
title: Risolvere problemi
description: Informazioni sulla risoluzione dei problemi per Rendering remoto di Azure
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 14184c09cc9d5eebab7f33323cd8ce587fdf9e88
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014592"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questa pagina elenca i problemi comuni che interferiscono con Rendering remoto di Azure e i metodi per risolverli.

## <a name="cant-link-storage-account-to-arr-account"></a>Non è possibile collegare l'account di archiviazione all'account ARR

In alcuni casi, durante il [collegamento di un account di archiviazione](../how-tos/create-an-account.md#link-storage-accounts), l'account di Rendering remoto non è presente nell'elenco. Per risolvere questo problema passare all'account ARR nel portale di Azure e selezionare **Identità** nel gruppo **Impostazioni** a sinistra. Verificare che **Stato** sia impostato su **Attivo**.
![Debugger fotogrammi di Unity](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Il client non riesce a connettersi al server

Assicurarsi che i firewall (sul dispositivo, nei router e così via) non blocchino le porte seguenti:

* **50051 (TCP)** : richiesta per la connessione iniziale (handshake HTTP)
* **8266 (TCP+UDP)** : richiesta per il trasferimento dei dati
* **5000 (TCP)** , **5433 (TCP)** , **8443 (TCP)** : richieste per [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Errore ' `Disconnected: VideoFormatNotAvailable` '

Verificare che la GPU supporti la decodifica video hardware. Vedere [PC di sviluppo](../overview/system-requirements.md#development-pc).

Se si lavora su un computer portatile con due GPU, è possibile che la GPU su cui l'utente esegue operazioni per impostazione predefinita non fornisca funzionalità di decodifica video hardware. In tal caso, provare a forzare l'uso dell'altra GPU nell'app. Per svolgere l'operazione spesso è necessario accedere alle impostazioni del driver della GPU.

## <a name="retrieve-sessionconversion-status-fails"></a>Il recupero dello stato della sessione/conversione non riesce

L'invio troppo frequente dei comandi dell'API REST provocherà la limitazione del server e la restituzione di un errore. Il codice di stato HTTP nel case di limitazione delle richieste è 429 ("numero eccessivo di richieste"). Come regola generale, è necessario un ritardo di **5-10 secondi tra le chiamate successive**.

Si noti che questo limite non solo influiscono sulle chiamate all'API REST quando viene chiamato direttamente, ma anche sulle rispettive controparti C#/C + +, ad esempio `Session.GetPropertiesAsync` , `Session.RenewAsync` o `Frontend.GetAssetConversionStatusAsync` .

Se si verifica la limitazione lato server, modificare il codice per eseguire le chiamate con minore frequenza. Il server Reimposta lo stato di limitazione ogni minuto, quindi è sicuro eseguire di nuovo il codice dopo un minuto.

## <a name="h265-codec-not-available"></a>Il codec H265 non è disponibile

Esistono due motivi per cui il server potrebbe rifiutare la connessione con un `codec not available` errore.

**Il codec H265 non è installato:**

Assicurarsi anzitutto di aver installato le **estensioni video HEVC** come indicato nella sezione [Software](../overview/system-requirements.md#software) dei requisiti di sistema.

Se ancora si riscontrano problemi, verificare che la scheda grafica supporti il codec H265 e che sia installato il driver grafico più recente. Per informazioni specifiche del fornitore, vedere la sezione [PC di sviluppo](../overview/system-requirements.md#development-pc) dei requisiti di sistema.

**Il codec è installato, ma non può essere usato:**

Il motivo di questo problema è un'impostazione di sicurezza non corretta per le DLL. Questo problema non viene rilevato quando si tenta di guardare video codificati con il codec H265. Reinstallare il codec non risolve il problema. In tal caso, seguire la procedura seguente:

1. Aprire una sessione di **PowerShell con diritti di amministratore** e avviare l'esecuzione

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Il comando deve restituire il parametro `InstallLocation` del codec, ad esempio:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Aprire la cartella in Esplora risorse
1. Si dovrebbero visualizzare le sottocartelle **x86** e **x64**. Fare clic con il pulsante destro del mouse su una delle cartelle e scegliere **Proprietà**
    1. Selezionare la scheda **Sicurezza** e fare clic sul pulsante delle impostazioni **Avanzate**
    1. Fare clic su **Modifica** per **Proprietario**
    1. Digitare **Amministratori** nel campo di testo
    1. Fare clic su **Controlla nomi** e quindi su **OK**
1. Ripetere la procedura precedente per l'altra cartella
1. Ripetere i passaggi precedenti anche in ogni file DLL all'interno di entrambe le cartelle. Dovrebbero essere presenti quattro DLL.

Per verificare che le impostazioni siano corrette, effettuare l'operazione per ognuna delle quattro DLL:

1. Fai clic su **Proprietà > Sicurezza > Modifica**.
1. Scorrere l'elenco di tutti i **Gruppi/utenti** e assicurarsi che per ognuno sia stato impostato il diritto **Lettura ed esecuzione** (deve essere presente il segno di spunta nella colonna **Consenti**)

## <a name="low-video-quality"></a>Qualità video scarsa

La qualità del video può essere compromessa dalla qualità della rete o dall'assenza del codec video H265.

* Vedere i passaggi per [identificare i problemi di rete](#unstable-holograms).
* Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver di grafica più recente.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>I video registrati con MRC non riflettono la qualità dell'esperienza dal vivo

Un video può essere registrato in Hololens tramite [MRC (Mixed Reality Capture, Acquisizione di Realtà mista)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Il video risultante presenta tuttavia una qualità peggiore rispetto all'esperienza dal vivo per due motivi:
* La frequenza di fotogrammi del video è limitata a 30 Hz e non a 60 Hz.
* Le immagini del video saltano il passaggio di elaborazione della [riproiezione con ritardo della fase](../overview/features/late-stage-reprojection.md), pertanto il video ha un aspetto maggiormente mosso.

Entrambe sono limitazioni intrinseche della tecnica di registrazione.

## <a name="black-screen-after-successful-model-loading"></a>Schermata nera dopo il completamento del caricamento del modello

Se dopo essersi connessi al runtime di rendering e aver caricato correttamente un modello viene visualizzata una schermata nera, possono esservi diverse cause.

Si consiglia di provare le soluzioni seguenti prima di svolgere un'analisi più approfondita:

* Il codec H265 è installato? Sebbene sia previsto un fallback per il codec H264, sono stati riscontrati casi in cui tale fallback non funzionava correttamente. Vedere i [requisiti di sistema](../overview/system-requirements.md#development-pc) per l'installazione del driver di grafica più recente.
* Quando si lavora con un progetto di Unity, chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/creare nuovamente il progetto. In alcuni casi, i dati memorizzati nella cache hanno impedito il funzionamento corretto dell'esempio, senza nessun motivo apparente.

Se questi due passaggi non sono stati utili, è necessario verificare se i fotogrammi del video vengono ricevuti o meno dal client. La query può essere eseguita a livello di codice come indicato nel capitolo [Query sulle prestazioni lato server](../overview/features/performance-queries.md). Il parametro `FrameStatistics struct` presenta un membro che indica il numero di fotogrammi video ricevuti. Se il numero è maggiore di 0 e aumenta nel tempo, il client riceve i fotogrammi video effettivi dal server. Di conseguenza, deve trattarsi di un problema lato client.

### <a name="common-client-side-issues"></a>Problemi comuni lato client

**Il modello supera i limiti della macchina virtuale selezionata, in particolare il numero massimo di poligoni:**

Vedere limiti specifici per le [dimensioni del server](../reference/limits.md#overall-number-of-polygons).

**Il modello non si trova all'interno della fotocamera tronco:**

In molti casi, il modello viene visualizzato correttamente ma si trova al di fuori del frustum della fotocamera. Una causa frequente potrebbe essere legata al fatto che il modello è stato esportato con una trasformazione tramite pivot decentrata in modo che venga ritagliato dal piano di ritaglio anteriore della fotocamera. Consente di eseguire una query sul rettangolo di selezione del modello a livello di codice e di visualizzare il riquadro di Unity come riquadro della riga o di stamparne i valori nel log di debug.

Inoltre, il processo di conversione genera un [file json di output](../how-tos/conversion/get-information.md) insieme al modello convertito. Per eseguire il debug dei problemi di posizionamento del modello è utile notare la voce `boundingBox` nella [sezione outputStatistics](../how-tos/conversion/get-information.md#the-outputstatistics-section):

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

Il rettangolo di selezione è descritto come una posizione `min` e `max` nello spazio 3D, con un valore espresso in metri. Di conseguenza, una coordinata di 1000,0 indica una distanza di 1 chilometro dall'origine.

Con questo rettangolo di selezione possono sorgere due problemi da ricondurre alla geometria invisibile:
* **Il rettangolo può essere distante dal centro**, quindi l'oggetto viene ritagliato completamente a causa del ritaglio del piano anteriore. I valori `boundingBox` in questo caso hanno un aspetto simile al seguente: `min = [-2000, -5,-5], max = [-1990, 5,5]`, usando uno spostamento consistente sull'asse x come riportato nell'esempio di seguito. Per risolvere questo tipo di problema, abilitare l'opzione `recenterToOrigin` nella [configurazione della conversione del modello](../how-tos/conversion/configure-model-conversion.md).
* **Il rettangolo può essere centrato ma gli ordini di grandezza sono troppo ampi**. Ciò significa che, benché la fotocamera si avvii al centro del modello, la relativa geometria viene ritagliata in tutte le direzioni. In genere i valori `boundingBox` in questo caso hanno un aspetto simile al seguente: `min = [-1000,-1000,-1000], max = [1000,1000,1000]`. Il motivo di questo tipo di problema è solitamente una mancata corrispondenza nel dimensionamento delle unità. Per compensare, specificare un [valore di ridimensionamento durante la conversione](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) o contrassegnare il modello di origine con le unità corrette. Il dimensionamento può essere applicato anche al nodo radice durante il caricamento del modello al runtime.

**La pipeline di rendering di Unity non include gli hook di rendering:**

Rendering remoto di Azure include gli hook alla pipeline di rendering di Unity per eseguire la composizione del fotogramma con il video ed eseguire la riproiezione. Per verificare che questi hook esistano, aprire il menu *:::no-loc text="Window > Analysis > Frame debugger":::* . Abilitarlo e verificare la presenza di due voci per `HolographicRemotingCallbackPass` nella pipeline:

![Debugger fotogrammi di Unity](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>Il rendering del modello a scacchi viene eseguito dopo il caricamento

Se l'immagine sottoposta a rendering è simile alla seguente: ![ scacchiera, ](../reference/media/checkerboard.png) il renderer raggiunge i [limiti del poligono per le dimensioni di configurazione standard](../reference/vm-sizes.md). Per attenuare, passare a dimensioni di configurazione **Premium** o ridurre il numero di poligoni visibili.

## <a name="the-rendered-image-in-unity-is-upside-down"></a>L'immagine sottoposta a rendering in Unity è capovolta

Assicurarsi di seguire l' [esercitazione Unity: visualizzare esattamente i modelli remoti](../tutorials/unity/view-remote-models/view-remote-models.md) . Un'immagine rovesciata indica che Unity è necessario per creare una destinazione di rendering fuori schermo. Questo comportamento non è attualmente supportato e crea un notevole effetto sulle prestazioni in HoloLens 2.

I motivi di questo problema potrebbero essere MSAA, HDR o abilitare la post-elaborazione. Verificare che il profilo di bassa qualità sia selezionato e impostato come predefinito in Unity. A tale scopo, passare a *modifica > impostazioni progetto... > qualità*.

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Il codice Unity che usa l'API di Rendering remoto non viene compilato

### <a name="use-debug-when-compiling-for-unity-editor"></a>Eseguire il debug durante la compilazione per l'editor di Unity

Modificare il *tipo di compilazione* della soluzione Unity in **Debug**. Quando si esegue il test di ARR nell'editor di Unity, il parametro `UNITY_EDITOR` definito è disponibile solo nelle compilazioni di debug. Tenere presente che questo comportamento non è correlato al tipo di compilazione usato per le [applicazioni distribuite](../quickstarts/deploy-to-hololens.md), in cui è necessario preferire le compilazioni "Release".

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Errori di compilazione quando si compilano esempi di Unity per HoloLens 2

Provando a compilare esempi di Unity (avvio rapido, ShowCaseApp...) per HoloLens 2 sono stati riscontati degli errori spuri. Visual Studio non è in grado di copiare alcuni file sebbene siano presenti. Se si rileva questo problema:
* Rimuovere tutti i file temporanei di Unity dal progetto e riprovare. Ciò significa chiudere Unity, eliminare la *libreria* temporanea e le cartelle *obj* nella directory del progetto e caricare/compilare nuovamente il progetto.
* Assicurarsi che i progetti si trovino in una directory su disco con un percorso ragionevolmente breve, dal momento che il passaggio di copia talvolta riscontra problemi con nomi di file troppo lunghi.
* Se la soluzione non è utile a risolvere il problema, è possibile che MS Sense interferisca con il passaggio di copia. Per configurare un'eccezione, eseguire questo comando del registro dalla riga di comando (sono necessari i diritti di amministratore):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>Le compilazioni Arm64 per i progetti Unity hanno esito negativo perché manca AudioPluginMsHRTF.dll

`AudioPluginMsHRTF.dll`Per Arm64 è stato aggiunto al pacchetto di *realtà misto Windows* *(com. Unity. XR. windowsmr. metro)* nella versione 3.0.1. Assicurarsi che la versione 3.0.1 o successiva sia installata tramite Gestione pacchetti Unity. Dalla barra dei menu di Unity passare a *finestra > gestione pacchetti* e cercare il pacchetto di *realtà mista di Windows* .

## <a name="unstable-holograms"></a>Ologrammi instabili

Se gli oggetti di cui è stato eseguito il rendering sembrano sportarsi con i movimenti della testa, potrebbe trattarsi di problemi con la *Riproiezione con ritardo della fase*. Per istruzioni su come affrontare una situazione di questo tipo, fare riferimento alla sezione in [Riproiezione con ritardo della fase](../overview/features/late-stage-reprojection.md).

Gli ologrammi instabili (oscillazione, distorsione, tremolio o salto degli ologrammi) possono derivare anche da una connettività di rete insufficiente, in particolare da una larghezza di banda di rete insufficiente o una latenza troppo elevata. Un buon indicatore per la qualità della connessione di rete è il valore sulle [statistiche delle prestazioni](../overview/features/performance-queries.md)`ARRServiceStats.VideoFramesReused`. I fotogrammi riusati indicano situazioni in cui è necessario riusare un fotogramma video precedente sul lato client perché non è disponibile alcun nuovo fotogramma video, ad esempio a causa di una perdita di pacchetti o di variazioni nella latenza di rete. Se il valore `ARRServiceStats.VideoFramesReused` è spesso superiore a 0, significa che si è riscontrato un problema di rete.

Un altro valore da esaminare è `ARRServiceStats.LatencyPoseToReceiveAvg`. Deve essere sempre inferiore a 100 ms. Se si visualizzano valori più alti, significa che il data center a cui si è connessi è troppo lontano.

Per un elenco delle possibili mitigazioni, vedere le [Linee guida per la connettività di rete](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="z-fighting"></a>Z-fighting

Sebbene ARR offra la [funzionalità di mitigazione per il combattimento z](../overview/features/z-fighting-mitigation.md), il combattimento z può comunque essere visualizzato nella scena. Questa guida mira a risolvere questi problemi rimanenti.

### <a name="recommended-steps"></a>Procedure consigliate

Usare il flusso di lavoro seguente per attenuare la lotta z:

1. Testare la scena con le impostazioni predefinite di ARR (mitigazione z-Fighting su)

1. Disabilitare la mitigazione del combattimento z tramite la relativa [API](../overview/features/z-fighting-mitigation.md) 

1. Modificare la fotocamera vicino a un piano più vicino a un intervallo più vicino

1. Risolvere i problemi relativi alla scena tramite la sezione successiva

### <a name="investigating-remaining-z-fighting"></a>Analisi delle rimanenti lotte z

Se i passaggi precedenti sono stati esauriti e la restante z-Fighting è inaccettabile, è necessario analizzare la cause sottostante del combattimento z. Come indicato nella [pagina della funzionalità di mitigazione per il combattimento z](../overview/features/z-fighting-mitigation.md), esistono due motivi principali per il combattimento z: la perdita di precisione della profondità all'estremità dell'intervallo di profondità e le superfici che si intersecano durante l'esecuzione complanare. La perdita di precisione della profondità è un'eventualità matematica e può essere mitigata solo seguendo il passaggio 3 precedente. Le superfici complanari indicano un difetto dell'asset di origine e sono più corrette nei dati di origine.

ARR presenta una funzionalità che consente di determinare se le superfici potrebbero combattere z: [evidenziazione della scacchiera](../overview/features/z-fighting-mitigation.md). È anche possibile determinare visivamente la causa della lotta z. La prima animazione seguente mostra un esempio di perdita della precisione della profondità nella distanza e la seconda mostra un esempio di superfici quasi complanari:

![profondità-precisione-z-combattimento](./media/depth-precision-z-fighting.gif)  ![complanante-z-Fighting](./media/coplanar-z-fighting.gif)

Confrontare questi esempi con la z-Fighting per determinare la causare o, facoltativamente, seguire questo flusso di lavoro Step-by-Step:

1. Posizionare la fotocamera sopra le superfici di combattimento z per esaminare direttamente la superficie.
1. Spostare lentamente la fotocamera indietro, lontano dalle superfici.
1. Se il combattimento z è sempre visibile, le superfici sono perfettamente complanari. 
1. Se il combattimento z è visibile nella maggior parte dei casi, le superfici sono quasi complanari.
1. Se il combattimento z è visibile solo da lontano, la causa è la mancanza di precisione della profondità.

Le superfici complanari possono avere diverse cause:

* Un oggetto è stato duplicato dall'applicazione di esportazione a causa di un errore o di approcci del flusso di lavoro diversi.

    Verificare questi problemi con le rispettive applicazioni e il supporto dell'applicazione.

* Le superfici vengono duplicate e capovolte per apparire a doppio lato nei renderer che usano la selezione front-end o back-face.

    L'importazione tramite la [conversione del modello](../how-tos/conversion/model-conversion.md) determina la facciata principale del modello. Per impostazione predefinita viene utilizzato il doppio lato. Il rendering della superficie verrà eseguito come un sottile muro con un'illuminazione fisicamente corretta da entrambi i lati. L'unilateralità può essere implicita dai flag nell'asset di origine o forzata in modo esplicito durante la [conversione del modello](../how-tos/conversion/model-conversion.md). Inoltre, facoltativamente, la [modalità a singolo lato](../overview/features/single-sided-rendering.md) può essere impostata su "normale".

* Gli oggetti si intersecano negli asset di origine.

     Gli oggetti trasformati in modo tale che alcune delle superfici si sovrappongono creano anche un combattimento z. Anche la trasformazione di parti dell'albero della scena nella scena importata in ARR può creare questo problema.

* Le superfici vengono create espressamente per il tocco, ad esempio decalcomanie o testo sulle pareti.



## <a name="next-steps"></a>Passaggi successivi

* [Requisiti di sistema](../overview/system-requirements.md)
* [Requisiti di rete](../reference/network-requirements.md)

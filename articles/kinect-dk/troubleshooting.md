---
title: Problemi noti e risoluzione dei problemi relativi a Kinect di Azure
description: Per informazioni su alcuni dei problemi noti e suggerimenti per la risoluzione dei problemi, vedere l'articolo relativo all'uso di Sensor SDK con Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: risoluzione dei problemi, aggiornamento, bug, Kinect, feedback, ripristino, registrazione, suggerimenti
ms.openlocfilehash: 2db7b17ffc8b6595107dbc52fae719aa9f2d15db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276797"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Problemi noti e risoluzione dei problemi relativi a Kinect di Azure

Questa pagina contiene i problemi noti e i suggerimenti per la risoluzione dei problemi quando si usa Sensor SDK con Azure Kinect DK. Vedere anche [pagine del supporto tecnico](https://aka.ms/kinectsupport) per problemi specifici dell'hardware del prodotto.

## <a name="known-issues"></a>Problemi noti

- Problemi di compatibilità con i controller host USB di ASMedia (ad esempio, il chipset ASM1142)
  - Alcuni casi di utilizzo del driver USB Microsoft possono essere sbloccati
  - Molti PC hanno anche controller host alternativi e la modifica della porta USB3 può essere utile

Per altri problemi relativi al sensore SDK, vedere [problemi di GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Raccolta di log

La registrazione per K4A.dll è abilitata tramite le variabili di ambiente. Per impostazione predefinita, la registrazione viene inviata a stdout e vengono generati solo gli errori e i messaggi critici. Queste impostazioni possono essere modificate in modo che la registrazione venga inviata a un file. Il livello di dettaglio può anche essere regolato in base alle esigenze. Di seguito è riportato un esempio, per Windows, di abilitazione della registrazione in un file, denominato K4a. log, e acquisisce i messaggi di avviso e di livello superiore.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Eseguire uno scenario dal prompt dei comandi (ad esempio, Launch Viewer)
4. Passare a K4a. log e condividere il file.

Per ulteriori informazioni, vedere la sezione seguente ritagliare da un file di intestazione:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

La registrazione per l'SDK di rilevamento del corpo K4ABT.dll è simile, ad eccezione del fatto che gli utenti devono modificare un set diverso di nomi di variabili di ambiente:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Il dispositivo non viene enumerato in gestione dispositivi

- Controllare il LED di stato dietro il dispositivo. Se si sta lampeggiando in ambra, si verifica un problema di connettività USB e non si ottiene una potenza sufficiente. Il cavo alimentatore deve essere collegato all'alimentatore fornito. Quando il cavo di alimentazione ha un tipo USB connesso, il dispositivo richiede una potenza maggiore di quella fornita da una porta USB del computer. Quindi, non connettersi ad essa a una porta PC o a un hub USB.
- Controllare che il cavo di alimentazione sia connesso e usare la porta USB3 per i dati.
- Provare a modificare la porta USB3 per la connessione dati (raccomandazione di usare la porta USB vicino alla scheda madre, ad esempio, nella parte posteriore del computer).
- Controllare che il cavo, i cavi danneggiati o di qualità inferiore possano causare un'enumerazione non affidabile (il dispositivo mantiene il "lampeggio" in gestione dispositivi).
- Se si è connessi al computer portatile e in esecuzione a batteria, potrebbe essere necessario limitare l'alimentazione alla porta.
- Riavviare il computer host.
- Se il problema persiste, è possibile che si verifichi un problema di compatibilità.
- Se si verifica un errore durante l'aggiornamento del firmware e il dispositivo non è stato recuperato autonomamente, eseguire il [ripristino delle impostazioni predefinite](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk).

## <a name="azure-kinect-viewer-fails-to-open"></a>Non è possibile aprire il Visualizzatore Kinect di Azure

- Verificare prima che il dispositivo venga enumerato in Windows Device Manager.

    ![Fotocamere Kinect di Azure in gestione dispositivi di Windows](./media/resources/viewer-fails.png)

- Controllare se sono presenti altre applicazioni che usano il dispositivo (ad esempio, applicazione Windows fotocamera). Solo un'applicazione alla volta può accedere al dispositivo.
- Controllare il log k4aviewer. Err per i messaggi di errore.
- Aprire l'applicazione della fotocamera Windows e verificarne il funzionamento.
- Dispositivo del ciclo di alimentazione, lo streaming di attesa ha causato l'interruzione prima di usare il dispositivo.
- Riavviare il computer host.
- Assicurarsi di usare i driver grafici più recenti nel PC.
- Se si usa la propria build di SDK, provare a usare la versione ufficialmente rilasciata se il problema viene risolto.
- Se il problema persiste, [raccogliere i log](troubleshooting.md#collecting-logs) e il feedback del file.

## <a name="cannot-find-microphone"></a>Non è possibile trovare il microfono

- Verificare prima che la matrice di microfoni venga enumerata in Device Manager.
- Se un dispositivo viene enumerato e funziona in modo corretto in Windows, il problema potrebbe essere dovuto al caso in cui le finestre di aggiornamento del firmware hanno assegnato un ID contenitore diverso alla fotocamera di profondità.
- È possibile provare a reimpostarlo passando a Device Manager, facendo clic con il pulsante destro del mouse su "array di microfoni Kinect di Azure" e scegliendo "Disinstalla dispositivo". Al termine, scollegare e riconnettere il sensore.

    ![Array Mic Kinect di Azure](./media/resources/mic-not-found.png)

- Dopo aver riavviato il Visualizzatore Kinect di Azure e riprovare.

## <a name="device-firmware-update-issues"></a>Problemi di aggiornamento del firmware del dispositivo

- Se il numero di versione corretto non viene segnalato dopo l'aggiornamento, potrebbe essere necessario spegnere il dispositivo.
- Se l'aggiornamento del firmware viene interrotto, è possibile che si verifichi uno stato non valido e che non venga enumerato. Scollegare e riconnettere il dispositivo e attendere 60 secondi per verificare se è possibile ripristinarlo.
In caso contrario, eseguire un [ripristino delle impostazioni predefinite](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)

## <a name="image-quality-issues"></a>Problemi di qualità dell'immagine

- Avviare il [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) e controllare il posizionamento del dispositivo per interferenze o se il sensore è bloccato o se l'obiettivo è modificato.
- Provare diverse modalità operative per restringere il sistema se si verifica un problema in modalità specifica.
- Per condividere problemi di qualità delle immagini con il team, è possibile:

1) Prendere in pausa la visualizzazione del [Visualizzatore Kinect di Azure](azure-kinect-viewer.md) e scattare uno screenshot o
2) Eseguire la registrazione usando il [registratore Kinect di Azure](azure-kinect-recorder.md), ad esempio `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Timestamp del dispositivo incoerenti o imprevisti

La chiamata a ```k4a_device_set_color_control``` può indurre temporaneamente le modifiche temporali al dispositivo che potrebbero richiedere alcune acquisizioni per stabilizzarsi. Evitare di chiamare l'API nel ciclo di acquisizione dell'immagine per evitare di reimpostare il calcolo temporale interno con ogni nuova immagine. Chiamare invece l'API prima di avviare la fotocamera o solo quando è necessario modificare il valore all'interno del ciclo di acquisizione dell'immagine. In particolare, evitare di chiamare ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)``` .

## <a name="usb3-host-controller-compatibility"></a>Compatibilità del controller host USB3

Se il dispositivo non viene enumerato in gestione dispositivi, è possibile che sia collegato a un controller USB3 non supportato. 

Per Azure Kinect DK in **Windows, Intel**, **Texas Instruments (ti)** e **Renesas** sono gli *unici controller host supportati*. Azure Kinect SDK sulle piattaforme Windows si basa su un ID contenitore unificato e deve estendersi ai dispositivi USB 2,0 e 3,0 in modo che l'SDK possa trovare i dispositivi di profondità, colore e audio che si trovano fisicamente nello stesso dispositivo. In Linux è possibile che siano supportati più controller host perché questa piattaforma si basa su un numero minore di ID del contenitore e su numeri di serie del dispositivo. 

L'argomento dei controller host USB diventa ancora più complicato quando in un PC è installato più di un controller host. Quando i controller host sono combinati, è possibile che si verifichino problemi nel caso in cui alcune porte funzionino correttamente e altre non funzionino affatto. A seconda del modo in cui le porte vengono cablate, è possibile che vengano visualizzate tutte le porte anteriori con il Kinect di Azure

**Windows:** Per individuare il controller host aperto Device Manager

1. Visualizza-> dispositivi per tipo 
2. Con la connessione di Azure Kinect selezionare le fotocamere->fotocamera di Azure Kinect 4K
3. Visualizza-> dispositivi per connessione

![Risoluzione dei problemi della porta USB](./media/resources/usb-troubleshooting.png)

Per comprendere meglio quale porta USB è connessa al PC, ripetere questi passaggi per ogni porta USB durante la connessione di Azure Kinect DK a porte USB diverse sul computer.

## <a name="depth-camera-auto-powers-down"></a>Riduzione automatica delle capacità della fotocamera

Il laser usato dalla fotocamera di profondità per calcolare i dati di profondità dell'immagine ha una durata limitata. Per ottimizzare il ciclo di vita dei laser, la videocamera Depth rileva quando i dati di profondità non vengono usati. La profondità della fotocamera si accende quando il dispositivo è in streaming per alcuni minuti, ma il PC host non sta leggendo i dati. Influisca inoltre sulla sincronizzazione di più dispositivi in cui i dispositivi subordinati vengono avviati in uno stato in cui la fotocamera di profondità è in streaming e i frame di profondità sono attivamente in attesa dell'avvio della sincronizzazione delle acquisizioni da parte del dispositivo master. Per evitare questo problema negli scenari di acquisizione di più dispositivi, verificare che il dispositivo master venga avviato entro un minuto dal primo avvio subordinato. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Uso di body tracking SDK con Unreal

Per usare Body Tracking SDK con Unreal, verificare di aver aggiunto `<SDK Installation Path>\tools` alla variabile di ambiente `PATH` e copiato `dnn_model_2_0.onnx` e `cudnn64_7.dll` a `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` .

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sul supporto](support.md)

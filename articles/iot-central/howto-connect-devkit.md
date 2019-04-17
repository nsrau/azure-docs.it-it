---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central nello sviluppo di dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 8558168b7eb35743c0fd590ded17ef13446533c4
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617819"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

Questo articolo descrive come connettere un dispositivo MXChip IoT DevKit (DevKit) all'applicazione Microsoft Azure IoT Central nello sviluppo di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura descritta in questo articolo, sono necessarie le risorse seguenti:

1. Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
1. Un dispositivo DevKit. Per acquistare un dispositivo DevKit, vedere [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Applicazione Sample Devkits

Un'applicazione creata dal **esempio Devkits** modello di applicazione include un **MXChip** modello del dispositivo definisce le caratteristiche di dispositivo seguenti:

- Misurazioni di dati di telemetria per **umidità**, **temperatura**, **pressione**, **Magnetometro** (misurata lungo X, Y, asse Z), **Accelerometro** (misurata lungo X, Y, asse Z), e **Giroscopio** (misurata lungo X, Y, asse Z).
- Misura di stato **lo stato del dispositivo**.
- Misura di eventi per **fatto clic sul pulsante B**.
- Le impostazioni per **tensione**, **corrente**, **velocità della ventola**e un **IR** attiva/disattiva.
- Le proprietà del dispositivo **morire numeri** e **posizione dei dispositivi**, che è una proprietà di posizione.
- Proprietà cloud **prodotta In**.
- I comandi **Echo** e **conto alla rovescia**. Quando un dispositivo reale riceve un' **Echo** comando, viene visualizzato il valore inviato sullo schermo del dispositivo. Quando un dispositivo reale riceve un **conto alla rovescia** comando, i cicli LED tramite un modello, e il dispositivo invia i valori del conto alla rovescia a IoT Central.

Per informazioni dettagliate sulla configurazione, vedere [dettagli del dispositivo MXChip modello](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

### <a name="get-your-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

Nell'applicazione Azure IoT Central, aggiungere un dispositivo reale dal **MXChip** modello del dispositivo e prendere nota dei dettagli della connessione dispositivo: **Definire l'ambito ID, ID dispositivo e chiave primaria**:

1. Aggiungere un **vero e proprio dispositivo** Device Explorer, selezionare **+ nuovo > reale** per aggiungere un dispositivo reale.

    * Immettere una minuscola **ID dispositivo**, oppure usare suggeriti **ID dispositivo**.
    * Immettere un **nome dispositivo**, oppure usare il nome suggerito

    ![Aggiungere un dispositivo](media/howto-connect-devkit/add-device.png)

1. Per ottenere i dettagli di connessione, il dispositivo **ambito ID**, **ID dispositivo**, e **Primary key**, selezionare **Connect** nella pagina del dispositivo.

    ![Dettagli della connessione](media/howto-connect-devkit/device-connect.png)

1. Prendere nota dei dettagli della connessione. Si è temporaneamente disconnessi da internet quando si prepara il dispositivo DevKit nel passaggio successivo.

### <a name="prepare-the-devkit-device"></a>Preparare il dispositivo DevKit

Se è stato usato in precedenza il dispositivo e si desidera riconfigurare in modo che usi una rete Wi-Fi diversa, una stringa di connessione o una misura di dati di telemetria, premere entrambi il **un'** e **B** pulsanti nello stesso momento. Se non funziona, premere **reimpostare** pulsante e riprovare.

#### <a name="to-prepare-the-devkit-device"></a>Per preparare il dispositivo DevKit

1. Scaricare la versione più recente del firmware predefinito di Azure IoT Central per MXChip dalla pagina dei [rilasci](https://aka.ms/iotcentral-docs-MXChip-releases) GitHub.
1. Connettere il dispositivo DevKit al computer di sviluppo usando un cavo USB. In Windows viene visualizzata una finestra di Esplora file per un'unità mappata all'archiviazione nel dispositivo DevKit. Ad esempio, l'unità potrebbe chiamarsi **AZ3166 (D:)**.
1. Trascinare il file **iotCentral.bin** nella finestra dell'unità. Al termine del processo di copia il dispositivo viene riavviato con il nuovo firmware.

1. Al riavvio del dispositivo DevKit viene visualizzata la schermata seguente:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Se la schermata visualizza altri elementi, reimpostare il dispositivo e premere i pulsanti **A** e **B** sul dispositivo nello stesso momento per riavviare il dispositivo.

1. Il dispositivo è ora in modalità punto di accesso (AP). È possibile connettersi a questo punto di accesso Wi-Fi dal computer o dal dispositivo mobile.

1. Nel computer, telefono o tablet connettersi al nome della rete Wi-Fi visualizzato sullo schermo del dispositivo. Quando ci si connette alla rete, si ha accesso a internet. Questo stato è previsto e si è connessi solo a questa rete per un breve periodo di tempo mentre si configura il dispositivo.

1. Aprire un Web browser e passare a [http://192.168.0.1/start](http://192.168.0.1/start). Viene visualizzata la pagina Web seguente:

    ![Pagina di configurazione del dispositivo](media/howto-connect-devkit/configpage.png)

    Nella pagina web, immettere:
    - Il nome della rete Wi-Fi
    - la password di rete Wi-Fi
    - Il codice PIN mostrato sul display del dispositivo
    - I dettagli della connessione **ambito ID**, **ID dispositivo**, e **chiave primaria** del dispositivo (deve aver già salvato questo seguendo i passaggi)
    - Selezionare tutte le misurazioni di dati di telemetria disponibili

1. Dopo aver scelto di **configurare il dispositivo**, viene visualizzata questa pagina:

    ![Dispositivo configurato](media/howto-connect-devkit/deviceconfigured.png)

1. Premere il pulsante di **reimpostazione** sul dispositivo.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Al riavvio del dispositivo DevKit sulla schermata appare quanto segue:

* Il numero di messaggi di telemetria inviati.
* Il numero di errori.
* Il numero di proprietà desiderate ricevute e il numero di proprietà segnalate inviate.

> [!NOTE]
> Se il dispositivo viene visualizzato un ciclo quando si tenta di connettersi, controllare se il dispositivo è **bloccato** in IoT Central, e **Unblock** il dispositivo in modo che può connettersi all'app.

Agitare il dispositivo per l'invio di una proprietà segnalata. Il dispositivo invia un numero casuale come proprietà **Numero stampo** del dispositivo.

È possibile visualizzare le misure di telemetria e i valori delle proprietà segnalate e configurare le impostazioni in Azure IoT Central:

1. Usare **Device Explorer** per passare alla pagina delle **misure** per il dispositivo MXChip reale aggiunto:

    ![Passare al dispositivo reale](media/howto-connect-devkit/realdevicenew.png)

1. Nella pagina delle **misure** è possibile visualizzare i dati di telemetria provenienti dal dispositivo MXChip:

    ![Visualizzare i dati di telemetria dal dispositivo reale](media/howto-connect-devkit/devicetelemetrynew.png)

1. Nella pagina delle **proprietà** è possibile visualizzare l'ultimo numero stampo e il percorso dispositivo segnalato dal dispositivo:

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Nella pagina delle **impostazioni** è possibile aggiornare le impostazioni per il dispositivo MXChip:

    ![Visualizzare le impostazioni del dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Nel **comandi** pagina, è possibile chiamare le **Echo** e **conto alla rovescia** comandi:

    ![Chiamata di comandi](media/howto-connect-devkit/devicecommands.png)

1. Nella pagina **Dashboard** è possibile visualizzare la mappa della posizione

    ![Visualizzare il dashboard dispositivo](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>Scaricare il codice sorgente

Per esaminare e modificare il codice del dispositivo, è possibile scaricarlo da GitHub. Se si prevede di modificare il codice, è opportuno seguire queste istruzioni per [preparare l'ambiente di sviluppo](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) per il sistema operativo desktop.

Per scaricare il codice sorgente, eseguire il comando seguente nel computer desktop:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

Il comando precedente scarica il codice sorgente in una cartella denominata `iot-central-firmware`.

> [!NOTE]
> Se **git** non è installato nell'ambiente di sviluppo, è possibile scaricarlo da [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Esaminare il codice

Usare Visual Studio Code per aprire la `MXCHIP/mxchip_advanced` cartella di `iot-central-firmware` cartella:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Per vedere come i dati di telemetria viene inviato all'applicazione di Azure IoT Central, aprire il **telemetry.cpp** del file nei `src` cartella:

- La funzione `TelemetryController::buildTelemetryPayload` crea il payload della telemetria JSON usando i dati dei sensori nel dispositivo.

- La funzione `TelemetryController::sendTelemetryPayload` chiamate `sendTelemetry` nel **AzureIOTClient.cpp** per inviare il payload JSON all'IoT Hub usato dall'applicazione Azure IoT Central.

Per vedere come vengono segnalati i valori delle proprietà per l'applicazione Azure IoT Central, aprire il **telemetry.cpp** del file nei `src` cartella:

- La funzione `TelemetryController::loop` invia il **percorso** proprietà segnalata approssimativamente ogni 30 secondi. Usa il `sendReportedProperty` funzionare nel **AzureIOTClient.cpp** file di origine.

- La funzione `TelemetryController::loop` invia il **dieNumber** proprietà segnalata quando l'accelerometro dispositivo rileva un doppio tocco. Usa il `sendReportedProperty` funzionare nel **AzureIOTClient.cpp** file di origine.

Per vedere come risponde il dispositivo per i comandi richiamati dall'applicazione IoT Central, aprire il **registeredMethodHandlers.cpp** del file nei `src` cartella:

- Il **dmEcho** funzione è il gestore per il **echo** comando. Viene illustrato il **displayedValue** presentata nel payload sullo schermo del dispositivo.

- Il **dmCountdown** funzione è il gestore per il **conto alla rovescia** comando. Modifica il colore del LED del dispositivo e Usa una proprietà segnalata per inviare il valore del conto alla rovescia all'applicazione IoT Central. La proprietà segnalata ha lo stesso nome del comando. La funzione Usa il `sendReportedProperty` funzionare nel **AzureIOTClient.cpp** file di origine.

Il codice nel **AzureIOTClient.cpp** file di origine Usa le funzioni dalle [Microsoft Azure IoT SDK e librerie per C](https://github.com/Azure/azure-iot-sdk-c) per interagire con l'IoT Hub.

Per informazioni su come modificare, compilare e caricare il codice di esempio nel dispositivo, vedere il file **readme.md** nella cartella `MXCHIP/mxchip_advanced`.

## <a name="mxchip-device-template-details"></a>Dettagli del modello di dispositivo MXChip

Un'applicazione creata dal modello di applicazione Sample Devkits include un modello di dispositivo MXChip con le caratteristiche seguenti:

### <a name="measurements"></a>Misure

#### <a name="telemetry"></a>Telemetria

| Nome campo     | Unità  | Minima | Massima | Cifre decimali |
| -------------- | ------ | ------- | ------- | -------------- |
| umidità       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>Stati 
| NOME          | Nome visualizzato   | NORMALE | ATTENZIONE | PERICOLO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stato del dispositivo   | Verde  | Arancione  | Rosso    | 

#### <a name="events"></a>Eventi 
| NOME             | Nome visualizzato      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Pulsante B premuto  | 

### <a name="settings"></a>Impostazioni

Impostazioni numeriche

| Nome visualizzato | Nome campo | Unità | Cifre decimali | Minima | Massima | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Tensione      | setVoltage | Volt | 0              | 0       | 240     | 0       |
| Current      | setCurrent | Amp  | 0              | 0       | 100     | 0       |
| Velocità della ventola    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Impostazioni attivazione/disattivazione

| Nome visualizzato | Nome campo | Testo attivato | Testo disattivato | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATTIVA      | DISATTIVA      | Off     |

### <a name="properties"></a>Properties

| Type            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Proprietà dispositivo | Percorso dispositivo   | location  | location    |
| Text            | Prodotto in     | manufacturedIn   | N/D       |

### <a name="commands"></a>Comandi:

| Nome visualizzato | Nome campo | Tipo restituito | Nome visualizzato del campo di input | Nome del campo di input | Tipo di campo di input |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo (eco)       | text        | valore da visualizzare         | displayedValue   | text             |
| Conto alla rovescia    | Conto alla rovescia  | number      | Il conteggio da               | countFrom        | number           |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere Raspberry Pi all'applicazione Azure IoT Central, il passaggio successivo consigliato sia per informazioni su come [configurare un modello di dispositivo personalizzata](howto-set-up-template.md) per dispositivo IoT.

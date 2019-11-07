---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central in qualità di sviluppatore di dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4e04ae7d9594ac064c9f3707c797fb2709a79cb6
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583037"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Questo articolo descrive come connettere un dispositivo MXChip IoT DevKit (DevKit) all'applicazione Microsoft Azure IoT Central nello sviluppo di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare i passaggi descritti in questo articolo, sono necessarie le risorse seguenti:

1. Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central.md).
1. Un dispositivo DevKit. Per acquistare un dispositivo DevKit, vedere [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/).

## <a name="sample-devkits-application"></a>Applicazione Sample Devkits

Un'applicazione creata dal modello di applicazione **devkits di esempio** include un modello di dispositivo **MXChip** che definisce le caratteristiche del dispositivo seguenti:

- Misurazioni di telemetria per **umidità**, **temperatura**, **pressione**, **magnetometro** (misurata lungo l'asse x, y, z), **accelerometro** (misurato lungo l'asse x, y, z) e **giroscopio** (misurato lungo l'asse x, y, z).
- Misurazione dello stato per **lo stato del dispositivo**.
- Misurazione dell'evento per il **pulsante B premuto**.
- Impostazioni relative a **tensione**, **corrente**, **velocità della ventola**e interruttore **IR** .
- Il **numero** delle proprietà del dispositivo e il **percorso del dispositivo**, ovvero una proprietà location.
- Proprietà Cloud **prodotta in**.
- Comandi **echo** e **Countdown**. Quando un dispositivo reale riceve un comando **echo** , Visualizza il valore inviato sulla visualizzazione del dispositivo. Quando un dispositivo reale riceve un comando di **conto alla rovescia** , il LED scorre un modello e il dispositivo invia i valori del conto alla rovescia a IOT Central.

Per informazioni dettagliate sulla configurazione, vedere [Dettagli del modello di dispositivo MXChip](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

### <a name="get-your-device-connection-details"></a>Ottenere i dettagli della connessione del dispositivo

Nell'applicazione IoT Central di Azure aggiungere un dispositivo reale dal modello di dispositivo **MXChip** e prendere nota dei dettagli della connessione del dispositivo: **ID ambito, ID dispositivo e chiave primaria**:

1. Aggiungere un **dispositivo reale** da Device Explorer, selezionare **+ nuovo > reale** per aggiungere un dispositivo reale.

    * Immettere un **ID dispositivo**minuscolo o usare l' **ID dispositivo**suggerito.
    * Immettere un **nome di dispositivo**o usare il nome suggerito

    ![Aggiungere un dispositivo](media/howto-connect-devkit/add-device.png)

1. Per ottenere i dettagli di connessione del dispositivo, **ID ambito**, **ID dispositivo**e **chiave primaria**, selezionare **Connetti** nella pagina dispositivo.

    ![Dettagli di connessione](media/howto-connect-devkit/device-connect.png)

1. Prendere nota dei dettagli della connessione. Si è temporaneamente disconnessi da Internet quando si prepara il dispositivo DevKit nel passaggio successivo.

### <a name="prepare-the-devkit-device"></a>Preparare il dispositivo DevKit

Se in precedenza è stato usato il dispositivo e si vuole riconfigurarlo per usare una rete Wi-Fi diversa, una stringa di connessione o una misurazione della telemetria, premere contemporaneamente i pulsanti **a** e **B** . In caso contrario, fare clic sul pulsante **Reimposta** e riprovare.

#### <a name="to-prepare-the-devkit-device"></a>Per preparare il dispositivo DevKit

1. Scaricare la versione più recente del firmware predefinito di Azure IoT Central per MXChip dalla pagina dei [rilasci](https://aka.ms/iotcentral-docs-MXChip-releases) GitHub.
1. Connettere il dispositivo DevKit al computer di sviluppo usando un cavo USB. In Windows viene visualizzata una finestra di Esplora file per un'unità mappata all'archiviazione nel dispositivo DevKit. Ad esempio, l'unità potrebbe chiamarsi **AZ3166 (D:)** .
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

1. Nel computer, telefono o tablet connettersi al nome della rete Wi-Fi visualizzato sullo schermo del dispositivo. Quando ci si connette a questa rete, non si ha accesso a Internet. Questo stato è previsto e si è connessi a questa rete solo per un breve periodo di tempo durante la configurazione del dispositivo.

1. Aprire un Web browser e passare a [http://192.168.0.1/start](http://192.168.0.1/start). Viene visualizzata la pagina Web seguente:

    ![Pagina di configurazione del dispositivo](media/howto-connect-devkit/configpage.png)

    Nella pagina Web immettere:
    - Nome della rete Wi-Fi
    - Password della rete Wi-Fi
    - Codice PIN visualizzato nella visualizzazione del dispositivo
    - L'ID dell' **ambito**dei dettagli della connessione, l' **ID del dispositivo**e la **chiave primaria** del dispositivo (dovrebbe essere già stato salvato in seguito alla procedura)
    - Seleziona tutte le misurazioni di telemetria disponibili

1. Dopo aver scelto di **configurare il dispositivo**, viene visualizzata questa pagina:

    ![Dispositivo configurato](media/howto-connect-devkit/deviceconfigured.png)

1. Premere il pulsante di **reimpostazione** sul dispositivo.

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Al riavvio del dispositivo DevKit sulla schermata appare quanto segue:

* Il numero di messaggi di telemetria inviati.
* Il numero di errori.
* Il numero di proprietà desiderate ricevute e il numero di proprietà segnalate inviate.

> [!NOTE]
> Se il dispositivo viene visualizzato in ciclo durante il tentativo di connessione, controllare se il dispositivo è **bloccato** in IOT Central e **sbloccare** il dispositivo in modo che possa connettersi all'app.

Agitare il dispositivo per inviare una proprietà segnalata. Il dispositivo invia un numero casuale come proprietà **Numero stampo** del dispositivo.

È possibile visualizzare le misure di telemetria e i valori delle proprietà segnalate e configurare le impostazioni in Azure IoT Central:

1. Usare i **dispositivi** per passare alla pagina **misure** per il dispositivo MXChip effettivo aggiunto:

    ![Passare al dispositivo reale](media/howto-connect-devkit/realdevicenew.png)

1. Nella pagina delle **misure** è possibile visualizzare i dati di telemetria provenienti dal dispositivo MXChip:

    ![Visualizzare i dati di telemetria dal dispositivo reale](media/howto-connect-devkit/devicetelemetrynew.png)

1. Nella pagina delle **proprietà** è possibile visualizzare l'ultimo numero stampo e il percorso dispositivo segnalato dal dispositivo:

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-devkit/devicepropertynew.png)

1. Nella pagina delle **impostazioni** è possibile aggiornare le impostazioni per il dispositivo MXChip:

    ![Visualizzare le impostazioni del dispositivo](media/howto-connect-devkit/devicesettingsnew.png)

1. Nella pagina **comandi** è possibile chiamare i comandi **echo** e **Countdown** :

    ![Comandi di chiamata](media/howto-connect-devkit/devicecommands.png)

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

Usare Visual Studio Code per aprire la cartella `MXCHIP/mxchip_advanced` nella cartella `iot-central-firmware`:

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Per vedere come vengono inviati i dati di telemetria all'applicazione IoT Central di Azure, aprire il file di **telemetria. cpp** nella cartella `src`:

- La funzione `TelemetryController::buildTelemetryPayload` crea il payload della telemetria JSON usando i dati dei sensori nel dispositivo.

- La funzione `TelemetryController::sendTelemetryPayload` chiama `sendTelemetry` in **AzureIOTClient. cpp** per inviare il payload JSON all'hub Internet usato dall'applicazione Azure IOT Central.

Per visualizzare il modo in cui i valori delle proprietà vengono segnalati all'applicazione IoT Central di Azure, aprire il file di **telemetria. cpp** nella cartella `src`:

- La funzione `TelemetryController::loop` Invia la proprietà del **percorso** indicata approssimativamente ogni 30 secondi. Usa la funzione `sendReportedProperty` nel file di origine **AzureIOTClient. cpp** .

- La funzione `TelemetryController::loop` Invia la proprietà segnalata **dieNumber** quando l'accelerometro del dispositivo rileva un doppio tocco. Usa la funzione `sendReportedProperty` nel file di origine **AzureIOTClient. cpp** .

Per visualizzare il modo in cui il dispositivo risponde ai comandi chiamati dall'applicazione IoT Central, aprire il file **registeredMethodHandlers. cpp** nella cartella `src`:

- La funzione **dmEcho** è il gestore per il comando **echo** . Mostra i **displayedValue** archiviati nel payload sullo schermo del dispositivo.

- La funzione **dmCountdown** è il gestore per il comando **Countdown** . Viene modificato il colore del LED del dispositivo e viene usata una proprietà segnalata per inviare il valore di conto alla rovescia all'applicazione IoT Central. Il nome della proprietà segnalata è identico a quello del comando. La funzione usa la funzione `sendReportedProperty` nel file di origine **AzureIOTClient. cpp** .

Il codice nel file di origine **AzureIOTClient. cpp** usa le funzioni degli [SDK e delle librerie di Microsoft Azure Internet per](https://github.com/Azure/azure-iot-sdk-c) interagire con l'hub Internet.

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
| Name          | Nome visualizzato   | NORMALE | ATTENZIONE | PERICOLO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Stato del dispositivo   | Verde  | Arancione  | Rosso    | 

#### <a name="events"></a>Events 
| Name             | Nome visualizzato      | 
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

### <a name="properties"></a>Proprietà

| Tipo            | Nome visualizzato | Nome campo | Tipo di dati |
| --------------- | ------------ | ---------- | --------- |
| Proprietà dispositivo | Numero stampo   | dieNumber  | number    |
| Proprietà dispositivo | Percorso dispositivo   | location  | location    |
| Text            | Prodotto in     | manufacturedIn   | N/D       |

### <a name="commands"></a>Comandi:

| Nome visualizzato | Nome campo | Tipo restituito | Nome visualizzato del campo di input | Nome campo di input | Tipo di campo di input |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| Echo         | echo (eco)       | text        | valore da visualizzare         | displayedValue   | text             |
| Conto alla rovescia    | Conto alla rovescia  | number      | Conteggio da               | countFrom        | number           |

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un DevKit di MXChip Internet all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [configurare un modello di dispositivo personalizzato](howto-set-up-template.md) per il proprio dispositivo.

---
title: Connettere un dispositivo DevKit all'applicazione Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central nello sviluppo di dispositivi.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1097d14c824bcdebb9062fcf63b04e9837b0d170
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811614"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central

Questo articolo descrive come connettere un dispositivo MXChip IoT DevKit (DevKit) all'applicazione Microsoft Azure IoT Central nello sviluppo di dispositivi.

## <a name="before-you-begin"></a>Prima di iniziare

Per seguire la procedura descritta in questo articolo, sono necessari gli elementi seguenti:

1. Un'applicazione Azure IoT Central creata dal modello di applicazione **Sample Devkits**. Per altre informazioni, vedere la [guida introduttiva per la creazione di un'applicazione](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Un dispositivo DevKit. Per acquistare un dispositivo DevKit, vedere [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Applicazione Sample Devkits

Un'applicazione creata dal modello di applicazione **Sample Devkits** include un modello di dispositivo **MXChip** con le caratteristiche seguenti:

- I dati di telemetria contenenti le misure del dispositivo relative a **umidità**, **temperatura**, **pressione**, **magnetometro** (lungo gli assi X, Y e Z), **accelerometro** (lungo gli assi X, Y e Z) e **giroscopio** (lungo gli assi X, Y e Z).
- Lo stato contenente una misura di esempio per lo **stato del dispositivo**.
- La misura relativa a un evento di tipo **Pulsante B premuto**. 
- Le impostazioni di visualizzazione per **tensione**, **corrente** e **velocità della ventola** e un comando di attivazione/disattivazione del **controllo a infrarossi**.
- Le proprietà relative al **numero dello stampo** e alla **posizione del dispositivo** oltre alla proprietà cloud relativa al **luogo di produzione**. 

Per informazioni dettagliate sulla configurazione, vedere [Dettagli del modello di dispositivo MXChip](#mxchip-device-template-details).


## <a name="add-a-real-device"></a>Aggiungere un dispositivo reale

Nell'applicazione Azure IoT Central aggiungere un dispositivo reale dal modello di dispositivo **MXChip** e prendere nota dei dettagli relativi alla connessione del dispositivo (**ID ambito, ID dispositivo e chiave primaria**).

1. Aggiungere un **dispositivo reale** da Device Explorer, fare clic su **+Nuovo > Reale** per aggiungere un dispositivo reale.

    * Immettere l'ID dispositivo **<span style="color:Red">(in lettere minuscole)</span>** o usare l'ID dispositivo suggerito.
    * Immettere il nome del dispositivo o usare il nome suggerito

    ![Aggiungere un dispositivo](media/howto-connect-devkit-experimental/add-device.png)

1. Per ottenere i dettagli della connessione, ad esempio **ID ambito, ID dispositivo e chiave primaria** per il dispositivo aggiunto, fare clic su **Connetti** nella pagina del dispositivo.

    ![Dettagli della connessione](media/howto-connect-devkit-experimental/device-connect.png)

1. Assicurarsi di salvare questi dettagli, poiché verrà eseguita una disconnessione temporanea da Internet durante la preparazione del dispositivo DevKit.

### <a name="prepare-the-devkit-device"></a>Preparare il dispositivo DevKit

> [!NOTE]
> Se il dispositivo è stato usato in precedenza e le credenziali Wi-Fi sono state salvate, per riconfigurare il dispositivo in modo che usi una rete Wi-Fi, una stringa di connessione o una misura di telemetria differente, premere contemporaneamente i pulsanti **A** e **B** sulla lavagna. Se non funziona, premere il pulsante di **reimpostazione** e riprovare.

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

1. Nel computer, telefono o tablet connettersi al nome della rete Wi-Fi visualizzato sullo schermo del dispositivo. Quando ci si connette a questa rete, non si può accedere a Internet. Questo stato è previsto e si rimane connessi a questa rete solo per un breve periodo di tempo durante la configurazione del dispositivo.

1. Aprire un Web browser e passare a [http://192.168.0.1/start](http://192.168.0.1/start). Viene visualizzata la pagina Web seguente:

    ![Pagina di configurazione del dispositivo](media/howto-connect-devkit-experimental/configpage.png)

    Nella pagina Web: 
    - aggiungere il nome della rete Wi-Fi 
    - la password della rete Wi-Fi
    - CODICE PIN visualizzato sul display del dispositivo 
    - i dettagli della connessione **ID ambito, ID dispositivo e chiave primaria** del dispositivo (devono essere già stati salvati seguendo la procedura)      
    - Selezionare tutte le misure di telemetria disponibili. 

1. Dopo aver scelto di **configurare il dispositivo**, viene visualizzata questa pagina:

    ![Dispositivo configurato](media/howto-connect-devkit-experimental/deviceconfigured.png)

1. Premere il pulsante di **reimpostazione** sul dispositivo.


## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Al riavvio del dispositivo DevKit sulla schermata appare quanto segue:

* Il numero di messaggi di telemetria inviati.
* Il numero di errori.
* Il numero di proprietà desiderate ricevute e il numero di proprietà segnalate inviate.

> [!NOTE]
> Se il dispositivo sembra entrare in un ciclo durante la connessione, verificare se il dispositivo è *bloccato* in IoT Central e *sbloccare* il dispositivo in modo che possa connettersi all'app.

Scuotere il dispositivo aumenta il numero di proprietà segnalate inviate. Il dispositivo invia un numero casuale come proprietà **Numero stampo** del dispositivo.

È possibile visualizzare le misure di telemetria e i valori delle proprietà segnalate e configurare le impostazioni in Azure IoT Central:

1. Usare **Device Explorer** per passare alla pagina delle **misure** per il dispositivo MXChip reale aggiunto:

    ![Passare al dispositivo reale](media/howto-connect-devkit-experimental/realdevicenew.png)

1. Nella pagina delle **misure** è possibile visualizzare i dati di telemetria provenienti dal dispositivo MXChip:

    ![Visualizzare i dati di telemetria dal dispositivo reale](media/howto-connect-devkit-experimental/devicetelemetrynew.png)

1. Nella pagina delle **proprietà** è possibile visualizzare l'ultimo numero stampo e il percorso dispositivo segnalato dal dispositivo:

    ![Visualizzare le proprietà del dispositivo](media/howto-connect-devkit-experimental/devicepropertynew.png)

1. Nella pagina delle **impostazioni** è possibile aggiornare le impostazioni per il dispositivo MXChip:

    ![Visualizzare le impostazioni del dispositivo](media/howto-connect-devkit-experimental/devicesettingsnew.png)

1. Nella pagina **Dashboard** è possibile visualizzare la mappa della posizione

    ![Visualizzare il dashboard dispositivo](media/howto-connect-devkit-experimental/devicedashboardnew.png)


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

Usare Visual Studio Code, che è stato installato durante la preparazione dell'ambiente di sviluppo, per aprire la cartella `AZ3166` nella cartella `iot-central-firmware`: 

![Visual Studio Code](media/howto-connect-devkit-experimental/vscodeview.png)

Per vedere in che modo i dati di telemetria vengono inviati all'applicazione Azure IoT Central, aprire il file **main_telemetry.cpp** nella cartella di origine.

La funzione `buildTelemetryPayload` crea il payload della telemetria JSON usando i dati dei sensori nel dispositivo.

La funzione `sendTelemetryPayload` chiama `sendTelemetry` in **iotHubClient.cpp** per inviare il payload JSON all'hub IoT usato dall'applicazione Azure IoT Central.

Per vedere in che modo i valori delle proprietà vengono segnalati all'applicazione Azure IoT Central, aprire il file **main_telemetry.cpp** nella cartella di origine.

La funzione `telemetryLoop` invia la proprietà segnalata **doubleTap** quando l'accelerometro rileva un doppio tocco. Usa la funzione `sendReportedProperty` nel file di origine **iotHubClient.cpp**.

Il codice nel file di origine **iotHubClient.cpp** usa le funzioni [degli SDK e delle librerie di Microsoft Azure IoT per C](https://github.com/Azure/azure-iot-sdk-c) per interagire con l'hub IoT.

Per informazioni su come modificare, compilare e caricare il codice di esempio nel dispositivo, vedere il file **readme.md** nella cartella `AZ3166`.

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



## <a name="next-steps"></a>Passaggi successivi

Ora che si conosce la procedura per collegare un dispositivo DevKit all'applicazione Azure IoT Central, ecco i passi successivi suggeriti:

* [Preparare e connettere un dispositivo Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

---
title: Connettere un dispositivo Plug and Play per IoT Central | Microsoft Docs
description: In qualità di sviluppatore di dispositivi, informazioni su come usare Visual Studio Code per creare e testare un dispositivo Plug and Play che si connette a IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6eb73c1c2b768d82143890415702950905f261f6
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997236"
---
# <a name="use-visual-studio-code-to-create-an-iot-plug-and-play-device-that-connects-to-iot-central"></a>Usare Visual Studio Code per creare un dispositivo Plug and Play Internet che si connette a IoT Central

Questa guida dettagliata illustra come:

* Come operatore, per aggiungere e configurare un dispositivo reale nell'applicazione IoT Central di Azure.

* Per gli sviluppatori di dispositivi, usare Visual Studio Code per creare un dispositivo [plug and Play](../iot-pnp/overview-iot-plug-and-play.md) per la connessione all'applicazione IoT Central.

Usare un [modello di funzionalità del dispositivo](./concepts-architecture-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) per definire il dispositivo che si connette a IOT Central. In questa guida viene usato un modello che definisce un dispositivo sensore ambientale.

Lo sviluppatore del dispositivo utilizza il modello per generare il codice client del dispositivo e il generatore utilizza il modello per [creare un modello di dispositivo](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) in IOT Central. Il modello funge da contratto tra il dispositivo e l'applicazione IoT Central.

La sezione di questa guida che descrive come compilare il codice generato presuppone che si stia usando Windows.

Questa guida illustra come eseguire queste operazioni:

* Importare un modello di funzionalità del dispositivo in un modello di dispositivo in IoT Central
* Aggiungere un dashboard al modello che Visualizza i dati di telemetria del dispositivo
* Aggiungere un dispositivo reale dal modello
* Importare un modello di funzionalità del dispositivo in Visual Studio Code
* Generare un'applicazione client per dispositivi C dal modello
* Compilare l'applicazione client del dispositivo C e connetterla a IoT Central

## <a name="prerequisites"></a>Prerequisiti

Per testare il codice del dispositivo in questa guida, è necessario disporre di un'applicazione IoT Central creata dal modello di applicazione di **Anteprima** . Se non si dispone già di un'applicazione IoT Central da usare, completare la Guida introduttiva [creare un'applicazione Azure IOT Central (funzionalità di anteprima)](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json):

Per usare il modello di funzionalità del dispositivo in questa guida, è necessario:

* [Visual Studio Code](https://code.visualstudio.com/download): Visual Studio Code è disponibile per più piattaforme
* Estensione di Azure per dispositivi Workbench per Visual Studio Code. Usare la procedura seguente per installare l'estensione Azure Azure per i dispositivi Workbench in VS Code:

    1. In VS Code selezionare la scheda **estensioni** .
    1. Cercare **Azure il Workbench del dispositivo**.
    1. Selezionare **Installa**.

    > [!NOTE]
    > La versione corrente del generatore di codice nell'estensione non supporta i tipi di schema Geopoint e **vector** , né i tipi semantici di **accelerazione**, **velocità**e **posizione** . Questi tipi di schema e semantico sono supportati da IoT Central.

    > [!NOTE]
    > Per lavorare con IoT Central, il modello di funzionalità del dispositivo deve avere tutte le interfacce definite inline nello stesso file.

Per compilare il codice C generato in Windows in questa guida, è necessario:

* [Visual Studio (community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) : assicurarsi di includere il componente **Gestione pacchetti NuGet** e lo **sviluppo desktop con carico di C++**  lavoro quando si installa Visual Studio.
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/) : quando si installa **CMake**, selezionare l'opzione **Aggiungi CMake al percorso di sistema**.
* Una copia locale dell'SDK Azure:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

Per seguire le istruzioni fornite dal dispositivo alla fine di questa procedura, è necessario installare anche:

* [Node.js](https://nodejs.org)
* Lo [strumento DPS-keygen](https://www.npmjs.com/package/dps-keygen):

    ```cmd/sh
    npm i -g dps-keygen
    ```

## <a name="create-device-template"></a>Crea modello di dispositivo

Creare una cartella denominata **pnp_app** nella cartella **Azure--SDK-c** che contiene l'SDK di Azure per le cose che è stato clonato. Scaricare il modello di funzionalità del dispositivo [EnvironmentalSensor. capabilitymodel. JSON](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) da GitHub e salvare il file nella cartella **pnp_app** . Sostituire le due istanze di `<YOUR_COMPANY_NAME_HERE>` con il nome. Usare solo i caratteri a-z, A-Z, 0-9 e il carattere di sottolineatura. Prendere nota del valore completo del `"@id"` campo che identifica in modo univoco il modello di funzionalità del dispositivo, che sarà necessario in un secondo momento. Questo modello include due interfacce:

* Interfaccia comune **DeviceManagement** .
* interfaccia personalizzata **EnvironmentalSensor** .

Per creare un modello di dispositivo per il dispositivo sensore ambientale in IoT Central:

1. Passare alla pagina **modelli di dispositivo** e selezionare **+ nuovo**. Scegliere **personalizzata**.

1. Immettere **sensore ambientale** come nome modello dispositivo.

1. Scegliere **Importa modello di capacità**. Individuare quindi il file **EnvironmentalSensor. capabilitymodel. JSON** creato e selezionare **Apri**. Le due interfacce, **informazioni sul dispositivo** e **sensore di ambiente**, vengono visualizzate nel **modello di capacità del sensore ambientale**.

1. Selezionare **visualizzazioni** e quindi **visualizzare il dispositivo**.

1. Nell'elenco dei campi che è possibile aggiungere al Dashboard selezionare i due valori di telemetria, **umidità** e **temperatura** e selezionare **combina**. Quindi scegliere **Salva**.

È ora disponibile un modello di dispositivo che usa il modello di **sensore ambientale** e che include un dashboard semplice per visualizzare i dati di telemetria da un dispositivo.

## <a name="publish-the-template-and-add-a-real-device"></a>Pubblicare il modello e aggiungere un dispositivo reale

Per pubblicare il modello e aggiungere un dispositivo reale, passare alla pagina **modelli di dispositivo** e selezionare il modello di dispositivo **sensore ambientale** . Selezionare **pubblica**, rivedere le informazioni e selezionare **pubblica**.

Prima di connettere un'applicazione client, è necessario aggiungere un dispositivo nella pagina **dispositivi** e ottenere le informazioni di connessione:

1. Passare alla pagina **dispositivi** e selezionare **sensore ambientale**. La pagina **dispositivi** consente di gestire i dispositivi che possono connettersi all'applicazione.

1. Per aggiungere un dispositivo reale, scegliere **+ nuovo**, modificare l'ID del dispositivo in **Sensor01**e selezionare **Crea**. Assicurarsi che **Simulated** sia **disattivato**.

1. Nell'elenco dei dispositivi selezionare il dispositivo **sensore ambientale** . Selezionare quindi **Connetti**.

1. Prendere nota dell' **ID ambito**, dell' **ID dispositivo**e della **chiave primaria**. Quindi selezionare **Chiudi**.

## <a name="generate-a-device-client-application"></a>Generare un'applicazione client per dispositivi

Usare Visual Studio Code per generare un'applicazione client del dispositivo Skeleton dal modello di funzionalità del dispositivo:

1. Avviare Visual Studio Code e aprire la cartella **pnp_app** .

1. Premere **CTRL + MAIUSC + P** per aprire il riquadro comandi.

1. Immettere **plug and Play** e quindi selezionare **Genera stub del codice del dispositivo**.

1. Selezionare il file del modello di funzionalità del dispositivo **EnvironmentalSensor. capabilitymodel. JSON** .

1. Immettere **sensor_app** come nome del progetto.

1. Scegliere **ANSI C** come lingua.

1. Scegliere il **progetto CMake** come destinazione.

1. Scegliere **via DPS (servizio Device provisioning)** come metodo di connessione.

Visual Studio Code apre una nuova finestra con il codice C generato nella cartella **sensor_app** .

## <a name="add-connection-details-to-the-device-client"></a>Aggiungere i dettagli della connessione al client del dispositivo

Per aggiungere le informazioni di connessione al client del dispositivo, aprire **Main. c** nella cartella che contiene il codice generato:

1. Sostituire `[DPS Id Scope]` con il valore dell' **ID ambito** di cui si è preso nota in precedenza.

1. Sostituire `[DPS symmetric key]` con il valore della **chiave primaria** di cui si è preso nota in precedenza.

1. Sostituire `[device registration Id]` con il valore dell' **ID del dispositivo** annotato in precedenza.

1. Salvare le modifiche.

## <a name="build-and-run-the-client"></a>Compilare ed eseguire il client

Per compilare ed eseguire il client, è necessario personalizzare la compilazione per l'SDK di Azure:

1. Aprire il file **CMakeLists. txt** nella radice della cartella **Azure--SDK-c** .

1. Aggiungere la riga seguente alla fine di questo file per includere la nuova app client nella compilazione:

    ```txt
    add_subdirectory(pnp_app/sensor_app)
    ```

1. Salvare le modifiche.

1. Aprire un prompt dei comandi e passare alla cartella **Azure-** Internet.

1. Per compilare l'applicazione, eseguire i comandi seguenti:

    ```cmd
    mkdir cmake
    cd cmake
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. Per eseguire l'applicazione, eseguire il comando seguente dallo stesso prompt dei comandi:

    ```cmd
    pnp_app\sensor_app\Release\sensor_app.exe
    ```

## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Dopo alcuni minuti, è possibile visualizzare i dati di telemetria dal dispositivo nel dashboard del dispositivo nell'applicazione IoT Central.

## <a name="connect-device-first"></a>Connetti dispositivo-primo

È possibile connettere un dispositivo Plug and Play tramite una connessione del dispositivo, come descritto in [connettività](concepts-connectivity-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Il processo di individuazione segue questo ordine:

1. Associa al modello di dispositivo se è già pubblicato nell'applicazione IoT Central.

1. Recupera il modello di funzionalità dal [repository pubblico](https://aka.ms/ACFI) dei modelli di funzionalità pubblicate e certificate.

Usando Visual Studio Code e il comando **Genera stub del codice del dispositivo** a cui viene fatto riferimento in precedenza, è possibile seguire questa procedura per connettere prima il dispositivo e importare automaticamente il modello di funzionalità del dispositivo pubblicato dal repository pubblico in IOT Central:

1. Usare un modello di funzionalità del dispositivo esistente pubblicato nel repository pubblico. È necessario il modello di capacità completa del dispositivo e annotare l'URN di questo modello.

1. Attenersi alla procedura descritta in precedenza per [generare un'applicazione client del dispositivo](#generate-a-device-client-application) da usare Visual Studio Code e generare il codice del dispositivo.

1. Dall'applicazione IoT Central passare alla scheda **Amministrazione** e selezionare la sezione connessione del **dispositivo** . Prendere nota dell' **ID ambito** e dei valori di **chiave primaria** per l'applicazione.

    > [!NOTE]
    > La **chiave primaria** mostrata in questa pagina è una firma di accesso condiviso di gruppo che è possibile usare per generare più chiavi del dispositivo per l'applicazione.

1. Usare lo strumento di [keygen DPS](https://www.npmjs.com/package/dps-keygen) per generare una chiave del dispositivo dalla chiave primaria di cui si è preso nota in precedenza. Per generare la chiave del dispositivo, eseguire il comando seguente:

    ```cmd/sh
    dps-keygen -mk:<Primary_Key> -di:<device_id>
    ```

1. Seguire i passaggi della sezione precedente [aggiungere i dettagli della connessione al client del dispositivo](#add-connection-details-to-the-device-client) per aggiungere l' **ID ambito**, la **chiave primaria**e l' **ID dispositivo**.

1. Attenersi alla procedura descritta nella sezione precedente per [compilare ed eseguire il client](#build-and-run-the-client).

1. A questo punto si noterà che il dispositivo si connette all'applicazione IoT Central e porta automaticamente il modello di funzionalità del dispositivo dal repository pubblico come modello di dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un dispositivo reale a IoT Central, un passaggio successivo suggerito consiste nell'acquisire altre informazioni sui modelli di dispositivo in [configurare un modello di dispositivo](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

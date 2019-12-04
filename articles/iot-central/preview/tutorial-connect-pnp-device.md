---
title: Connettere un dispositivo Plug and Play IoT (anteprima) ad Azure IoT Central | Microsoft Docs
description: Usare un modello di funzionalità di dispositivo per generare il codice del dispositivo. Eseguire quindi il codice del dispositivo. Il dispositivo si connetterà all'applicazione IoT Central e sarà possibile usare le visualizzazioni generate automaticamente.
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: be03684f89382f198c13540bbdfb3de5bf8513a6
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404560"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>Esercitazione: Usare un modello di funzionalità di dispositivo per creare un dispositivo Plug and Play IoT e connetterlo all'applicazione IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Un _modello di funzionalità di dispositivo_ descrive le funzionalità di un dispositivo [Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md). IoT Central può usare un modello di funzionalità di dispositivo per creare un modello e le visualizzazioni per un dispositivo alla prima connessione del dispositivo.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Visual Studio Code per creare un dispositivo Plug and Play IoT usando un modello di funzionalità di dispositivo.
> * Eseguire il codice del dispositivo in Windows per connetterlo all'applicazione IoT Central.
> * Visualizzare i dati di telemetria simulati inviati dal dispositivo.

## <a name="prerequisites"></a>Prerequisiti

Completare l'argomento di avvio rapido [Creare un'applicazione Azure IoT Central (funzionalità di anteprima)](./quick-deploy-iot-central.md) per creare un'applicazione IoT Central usando il modello **App personalizzata > Anteprima applicazione**.

Per completare questa esercitazione, è necessario installare il software seguente nel computer locale:

* [Visual Studio Build Tools](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) con **Strumenti di compilazione C++** e carichi di lavoro del **componente Gestione pacchetti NuGet**. In alternativa, se si dispone già di [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 o 2015 con gli stessi carichi di lavoro installati.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/): quando si installa **CMake**, selezionare l'opzione **Add CMake to the system PATH** (Aggiungi CMake a PATH di sistema).
* [Visual Studio Code](https://code.visualstudio.com/).
* [Node.js](https://nodejs.org/)
* L'utilità `dps-keygen`:

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>Installare Azure IoT Tools

Usare la procedura seguente per installare il pacchetto di estensione Azure IoT Tools in VS Code:

1. In VS Code selezionare **Estensioni**.
1. Cercare **Azure IoT Tools**.
1. Selezionare **Installa**.

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

In questa esercitazione si userà lo strumento di gestione librerie [Vcpkg](https://github.com/microsoft/vcpkg) per installare Azure IoT SDK per dispositivi per C nell'ambiente di sviluppo.

1. Aprire un prompt dei comandi. Eseguire questo comando per installare Vcpkg:

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    Quindi, per associare l'[integrazione](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md) a livello di utente, eseguire questo comando. La prima volta che si esegue il comando sono necessari diritti amministrativi:

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. Installare Azure IoT SDK per dispositivi per C Vcpkg:

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>Generare la chiave del dispositivo

Per connettere un dispositivo a un'applicazione Azure IoT Central, è necessaria una chiave del dispositivo. Per generare una chiave del dispositivo:

1. Accedere all'applicazione IoT Central creata con il modello **App personalizzata > Anteprima applicazione** nell'argomento di avvio rapido [Creare un'applicazione Azure IoT Central (funzionalità di anteprima)](./quick-deploy-iot-central.md).

1. Passare alla pagina **Amministrazione** e selezionare **Connessione del dispositivo**.

1. Prendere nota dei valori di **Ambito ID** e **Chiave primaria** visualizzati quando si seleziona **Visualizza chiavi**. Questi valori vengono usati più avanti nell'esercitazione.

    ![Connessione del dispositivo](./media/tutorial-connect-pnp-device/device-connection.png)

1. Aprire un prompt dei comandi ed eseguire il comando seguente per generare una chiave del dispositivo:

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    Prendere nota della _chiave dispositivo_ generata. Questo valore sarà necessario in un passaggio successivo dell'esercitazione.

## <a name="download-your-model"></a>Scaricare il modello

In questa esercitazione si usa un modello di funzionalità di dispositivo per un dispositivo MxChip IoT DevKit. Per eseguire il codice, non è necessario un dispositivo DevKit effettivo. In questa esercitazione viene compilato il codice per l'esecuzione in Windows.

1. Creare una cartella denominata `central_app` e aprirla in VS Code.

1. Premere **CTRL+MAIUSC+P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Open Model Repository** (Apri repository dei modelli). Selezionare **Public repository** (Repository pubblico). VS Code visualizza un elenco di modelli di funzionalità di dispositivo nel repository dei modelli pubblico.

1. Selezionare il modello di funzionalità di dispositivo **MXChip IoT DevKit** con ID `urn:mxchip:mxchip_iot_devkit:1`. Selezionare quindi **Scarica**. A questo punto nella cartella `central_app` è presente una copia del modello di funzionalità di dispositivo.

![Repository dei modelli e modello di funzionalità di dispositivo](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> Per usare IoT Central, il modello di funzionalità di dispositivo deve includere tutte le interfacce definite inline nello stesso file.

## <a name="generate-the-c-code-stub"></a>Generare lo stub del codice C

A questo punto, si dispone del modello di funzionalità di dispositivo **MXChip IoT DevKit** e delle relative interfacce associate ed è possibile generare il codice del dispositivo che implementa il modello. Per generare lo stub del codice C in VS Code:

1. Dopo avere aperto la cartella con i file del modello di funzionalità di dispositivo, premere **CTRL+MAIUSC+P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

    > [!NOTE]
    > La prima volta che si usa l'utilità del generatore di codice di Plug and Play IoT è necessario attendere alcuni secondi il completamento del download.

1. Selezionare il file del modello di funzionalità di dispositivo **MXChip IoT DevKit** appena scaricato.

1. Immettere il nome di progetto **devkit_device**.

1. Scegliere **ANSI C** come linguaggio.

1. Scegliere **Via DPS (Device Provisioning Service) symmetric key** (Tramite chiave simmetrica DPS) come metodo di connessione.

1. Scegliere **CMake Project on Windows** (Progetto CMake in Windows) come tipo di progetto. Non scegliere **MXChip IoT DevKit Project** (Progetto MXChip IoT DevKit) perché questa opzione presuppone che sia disponibile un dispositivo DevKit effettivo.

1. Scegliere la modalità **Via Vcpkg** (Tramite Vcpkg) per includere l'SDK.

1. VS Code apre una nuova finestra contenente i file stub di codice generati nella cartella `devkit_device`.

![Codice del dispositivo generato](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>Compilare il codice

Per compilare lo stub del codice del dispositivo generato, si usa l'SDK per dispositivi. L'applicazione creata simula un dispositivo **MXChip IoT DevKit** e si connette all'applicazione IoT Central. L'applicazione invia proprietà e dati di telemetria e riceve comandi.

1. Al prompt dei comandi creare una sottodirectory `cmake` nella cartella `devkit_device` e passare a tale cartella:

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per compilare lo stub del codice generato. Sostituire il segnaposto `<directory of your Vcpkg repo>` con il percorso della copia del repository **Vcpkg**:

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    Se si usa Visual Studio 2017 o 2015, è necessario specificare il generatore CMake in base agli strumenti di compilazione usati:

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. Al termine della compilazione, eseguire l'applicazione dallo stesso prompt dei comandi. Sostituire `<scopeid>` e `<devicekey>` con i valori annotati in precedenza:

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. L'applicazione del dispositivo inizia a inviare dati all'hub IoT. A volte viene visualizzato l'errore `Error registering device for DPS` la prima volta che si esegue il comando precedente. Se viene visualizzato questo errore, ripetere il comando.

## <a name="view-the-device"></a>Visualizzare il dispositivo

Dopo che il codice del dispositivo è connesso a IoT Central, è possibile visualizzare le proprietà e i dati di telemetria inviati:

1. Nell'applicazione IoT Central passare alla pagina **Dispositivi** e selezionare il dispositivo **mxchip-01**. Questo dispositivo è stato aggiunto automaticamente alla connessione del codice del dispositivo:

    ![Pagina di panoramica](./media/tutorial-connect-pnp-device/overview-page.png)

    Dopo un paio di minuti, questa pagina mostra i grafici dei dati di telemetria inviati dal dispositivo.

1. Selezionare la pagina **Informazioni su** per visualizzare i valori delle proprietà inviate dal dispositivo.

1. Selezionare la pagina **Comandi** per chiamare comandi nel dispositivo. È possibile notare che dispositivo risponde al prompt dei comandi in cui è in esecuzione il codice del dispositivo.

1. Passare alla pagina **Modelli di dispositivo** per visualizzare il modello creato da IoT Central usando il modello di funzionalità di dispositivo del repository pubblico:

    ![Pagina Modelli di dispositivo](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come connettere un dispositivo Plug and Play IoT generato da un modello di funzionalità di dispositivo nel repository dei modelli pubblico.

Per altre informazioni sui modelli di funzionalità di dispositivo e su come creare modelli personalizzati, continuare con la guida pratica:

> [!div class="nextstepaction"]
> [Creare un gruppo di dispositivi](./tutorial-use-device-groups.md)

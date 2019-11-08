---
title: Usare Visual Studio e Visual Studio Code per compilare i dispositivi Plug and Play Preview | Microsoft Docs
description: Usa Visual Studio e Visual Studio Code per accelerare la creazione di Plug and Play dei modelli di dispositivo e l'implementazione del codice del dispositivo.
author: liydu
ms.author: liydu
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ce5a93cdd44af7f199d59d459daa46b4adb0719
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748045"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Usare Visual Studio e Visual Studio Code per compilare dispositivi Plug and Play

Gli strumenti di Azure Internet per la Visual Studio Code offrono un ambiente integrato per la creazione di modelli di funzionalità del dispositivo (DCM) e interfacce, la pubblicazione in repository di modelli e la generazione di codice Skeleton C per implementare l'applicazione del dispositivo.

Questo articolo illustra come:

- Genera il codice del dispositivo e il progetto di applicazione.
- Usare il codice generato nel progetto del dispositivo.
- Eseguire l'iterazione rigenerando il codice di scheletro.

Per altre informazioni sull'uso del VS Code per sviluppare dispositivi Internet, vedere [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

## <a name="prerequisites"></a>Prerequisiti

Installare [Visual Studio Code](https://code.visualstudio.com/).

Usare la procedura seguente per installare il pacchetto di estensione in VS Code.

1. In VS Code selezionare scheda **estensioni** .
1. Cercare e installare **gli strumenti di Azure** per le cose dal Marketplace.

## <a name="generate-device-code-and-project"></a>Genera codice e progetto del dispositivo

In VS Code usare **CTRL + MAIUSC + P** per aprire il riquadro comandi, immettere **plug and Play**Internet e selezionare **Genera stub del codice del dispositivo** per configurare il codice di scheletro e il tipo di progetto. Nell'elenco seguente vengono descritti in dettaglio i singoli passaggi:

- **File DCM da utilizzare per la generazione del codice**. Per generare il codice del dispositivo Skeleton, aprire la cartella che contiene i file di interfaccia e di DCM implementati. Se il generatore di codice non riesce a trovare un'interfaccia richiesta da DCM, viene scaricata dal repository del modello in base alle esigenze.

- **Lingua del codice del dispositivo**. Attualmente, il generatore di codice supporta solo ANSI C.

- **Nome del progetto**. Il nome del progetto viene usato come nome della cartella per il codice generato e per altri file di progetto. Per impostazione predefinita, la cartella è posizionata accanto al file DCM. Per evitare di dover copiare manualmente la cartella del codice generata ogni volta che si aggiorna il file DCM e si rigenera il codice del dispositivo, è necessario salvare il file DCM nella stessa cartella della cartella del progetto.

- **Metodo per la connessione ad Azure**. I file generati contengono anche codice per configurare il dispositivo per la connessione all'hub di Azure. È possibile scegliere di connettersi direttamente all' [Hub Azure](https://docs.microsoft.com/azure/iot-hub) Internet o usare il [servizio Device provisioning](https://docs.microsoft.com/azure/iot-dps).

    - **Tramite la stringa di connessione del dispositivo dell'hub**Internet: specificare la stringa di connessione del dispositivo per l'applicazione del dispositivo per connettersi direttamente all'hub Internet.
    - **Tramite chiave simmetrica DPS**: specificare l' **ambito ID**, la **chiave simmetrica** e l' **ID dispositivo** per l'applicazione del dispositivo necessari per connettersi all'hub Internet o IOT Central usando DPS.

- **Tipo di progetto**. Il generatore di codice genera anche un progetto CMake o Arduino. Attualmente, i tipi di progetto supportati sono:

    - **Progetto CMake in Windows**: per un progetto di dispositivo che usa [CMake](https://cmake.org/) come sistema di compilazione in Windows. Questa opzione genera `CMakeLists.txt` con le configurazioni dell'SDK per dispositivi nella stessa cartella del codice C.
    - **Progetto CMake in Linux**: per un progetto di dispositivo che usa [CMake](https://cmake.org/) come sistema di compilazione in Linux. Questa opzione genera `CMakeLists.txt` con le configurazioni dell'SDK per dispositivi nella stessa cartella del codice C.
    - **Progetto MXChip DevKit**: per un progetto di dispositivo che viene eseguito in un dispositivo [MXChip DevKit](https://aka.ms/iot-devkit) . Questa opzione genera un progetto Arduino che è possibile [usare in vs code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) o nell'IDE di Arduino per compilare ed eseguire su un dispositivo devkit.

- **Tipo di SDK del dispositivo**. Se si seleziona CMake come tipo di progetto, questo è il passaggio per configurare il modo in cui il codice generato includerà Azure Internet per dispositivi SDK nel `CMakeLists.txt`:

    - **Tramite**il codice sorgente: il codice generato si basa sul [codice sorgente dell'SDK del dispositivo](https://github.com/Azure/azure-iot-sdk-c) da includere in e compilare insieme a esso. Questa operazione è consigliata quando è stato personalizzato il codice sorgente dell'SDK per dispositivi.
    - **Tramite vcpkg**: il codice generato si basa sull' [SDK del dispositivo vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) da includere in e compilarlo insieme. Questo è il metodo consigliato per i dispositivi che eseguono Windows, Linux o macOS.

    > [!NOTE]
    > il supporto macOS per Azure vcpkg SDK per dispositivi C è in corso.

Il generatore di codice tenta di usare i file DCM e di interfaccia presenti nella cartella locale. Se i file di interfaccia non si trovano nella cartella locale, il generatore di codice li Cerca nel repository del modello pubblico o nel repository del modello aziendale. I [file di interfaccia comuni](./concepts-common-interfaces.md) vengono archiviati nel repository del modello pubblico.

Al termine della generazione del codice, l'estensione apre una nuova finestra di VS Code con il codice. Se si apre un file generato, ad esempio **Main. c**, è possibile che IntelliSense segnali che non è in grado di aprire i file di origine di c SDK. Per abilitare IntelliSense e l'esplorazione del codice corretti, attenersi alla procedura seguente per includere l'origine C SDK:

1. In vs code usare **CTRL + MAIUSC + P** per aprire il riquadro comandi, digitare e selezionare **CC++/: Edit Configurations (JSON)** per aprire il file **c_cpp_properties. JSON** .

1. Aggiungere il percorso di SDK per dispositivi nella sezione `includePath`:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Salvare il file.

## <a name="use-the-generated-code"></a>Usare il codice generato

Le istruzioni seguenti descrivono come usare il codice generato nel progetto di dispositivo personalizzato in diverse piattaforme di computer di sviluppo. Le istruzioni presuppongono che si stia usando una stringa di connessione del dispositivo dell'hub Internet con il codice generato:

### <a name="linux"></a>Linux

Per compilare il codice del dispositivo insieme al Device C SDK vcpkg usando CMake in un ambiente Linux, ad esempio Ubuntu o Debian:

1. Aprire un'applicazione Terminal.

1. Installare **GCC**, **git**, `cmake`e tutte le dipendenze usando il comando `apt-get`:

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verificare che la versione di `cmake` sia superiore a **2.8.12** e che la versione di **GCC** sia superiore a **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Installare vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Quindi, per associare l' [integrazione](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)a livello di utente, eseguire:

    ```bash
    ./vcpkg integrate install
    ```

1. Installare Azure vcpkg SDK per dispositivi C:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Creare una sottodirectory `cmake` nella cartella contenente lo stub del codice generato e passare alla cartella seguente:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per usare CMake per compilare l'SDK per dispositivi e lo stub del codice generato:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Una volta completata la compilazione, eseguire l'applicazione specificando come parametro la stringa di connessione del dispositivo hub Internet.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Per compilare il codice del dispositivo insieme al Device C SDK in Windows con CMake e iC++ compilatori di Visual Studio c dalla riga di comando, vedere la [guida introduttiva plug and Play](./quickstart-create-pnp-device.md). I passaggi seguenti illustrano come compilare il codice del dispositivo insieme al dispositivo C SDK vcpkg come progetto CMake in Visual Studio.

1. Seguire i passaggi illustrati nella [Guida introduttiva](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device#prepare-the-development-environment) per installare Azure Internet per dispositivi SDK per C tramite vcpkg.

1. Installare [Visual Studio 2019 (community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) : assicurarsi di includere il componente **Gestione pacchetti NuGet** e lo **sviluppo di applicazioni desktop con carico C++ di** lavoro.

1. Aprire Visual Studio, scegliere **File > apri > CMake.** per aprire il `CMakeLists.txt` nella cartella contiene il codice generato.

1. Nella barra degli strumenti **generale** trovare l'elenco a discesa **configurazioni** . Selezionare **Gestisci configurazione** per aggiungere l'impostazione CMake per il progetto.

    ![Gestisci configurazione](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nelle **impostazioni di CMake**aggiungere una nuova configurazione e selezionare **x86-debug** come destinazione.

1. In **argomenti del comando cmake**aggiungere la riga seguente:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Salvare il file.

1. Passare a **x86-debug** nell'elenco a discesa delle **configurazioni** . Per la creazione della cache per la CMake sono necessari alcuni secondi. Visualizzare la finestra output per visualizzare lo stato di avanzamento.

    ![Output CMake](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul `CMakeLists.txt` nella cartella radice e scegliere **Compila** dal menu di scelta rapida per compilare lo stub del codice generato con SDK per dispositivi.

1. Al termine della compilazione, al prompt dei comandi eseguire l'applicazione specificando come parametro la stringa di connessione del dispositivo dell'hub Internet.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Per altre informazioni sull'uso di CMake in Visual Studio, vedere [compilare il progetto CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

I passaggi seguenti illustrano come compilare il codice del dispositivo insieme al codice sorgente del dispositivo C SDK in macOS con CMake:

1. Aprire l'applicazione Terminal.

1. Usare [Homebrew](https://homebrew.sh) per installare tutte le dipendenze:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Verificare che [CMake](https://cmake.org/) sia almeno la versione **2.8.12**:

    ```bash
    cmake --version
    ```

1. [Patch curl](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) alla versione più recente disponibile.

1. Nella cartella che contiene il codice generato clonare il repository dell' [SDK di Azure](https://github.com/Azure/azure-iot-sdk-c) :

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Creare una cartella denominata `cmake` sotto la cartella che contiene il codice generato e passare a tale cartella.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per usare CMake per compilare l'SDK per dispositivi e lo stub del codice generato:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Una volta completata la compilazione, eseguire l'applicazione specificando come parametro la stringa di connessione del dispositivo hub Internet.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Eseguire l'iterazione rigenerando il codice di scheletro

Il generatore di codice può rigenerare il codice se si aggiornano i file di interfaccia o DCM. Supponendo che sia già stato generato il codice del dispositivo da un file DCM, per rigenerare il codice:

1. Dopo avere aperto la cartella con i file del modello di funzionalità di dispositivo, premere **CTRL+MAIUSC+P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

1. Scegliere il file DCM aggiornato.

1. Selezionare **rigenera di nuovo il codice per {nome progetto}** .

1. Il generatore di codice usa l'impostazione precedente configurata e rigenera il codice. Tuttavia, non sovrascrive i file che possono contenere codice utente, ad esempio `main.c` e `{project_name}_impl.c`.

> [!NOTE]
> Se si aggiorna l'ID URN nel file di interfaccia, questo viene considerato come una nuova interfaccia. Quando si rigenera il codice, il generatore di codice genera il codice per l'interfaccia ma non sovrascrive quello originale nel file di `{project_name}_impl.c`.

## <a name="problems-and-feedback"></a>Problemi e commenti e suggerimenti

Azure Internet per gli strumenti è un progetto open source su GitHub. Per eventuali problemi e richieste di funzionalità, è possibile [creare un problema in GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come usare Visual Studio e Visual Studio Code per generare codice Skeleton C per implementare l'applicazione del dispositivo. Un passaggio successivo suggerito consiste nell'apprendere come [installare e usare](./howto-install-iot-explorer.md) lo strumento Azure Internet Explorer.

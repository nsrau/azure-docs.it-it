---
title: Usare Visual Studio e il codice di Visual Studio per compilare dispositivi Plug and Play Preview IoT . Documenti Microsoft
description: Usa Visual Studio e Visual Studio Code per accelerare la creazione di modelli di dispositivi Plug and Play IoT e l'implementazione del codice del dispositivo.
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159235"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>Usare Visual Studio e il codice di Visual Studio per compilare dispositivi Plug and Play IoTUse Visual Studio and Visual Studio Code to build IoT Plug and Play devices

Gli strumenti IoT di Azure per visual Studio Code offrono un ambiente integrato per la creazione di modelli di funzionalità del dispositivo (DCM) e interfacce, pubblicare nei repository del modello e generare lo scheletro di codice C per implementare l'applicazione del dispositivo.

Questo articolo illustra come:

- Generare codice del dispositivo e progetto dell'applicazione.
- Usare il codice generato nel progetto del dispositivo.
- Eseguire l'iterazione rigenerando il codice della struttura.

Per altre informazioni sull'uso del codice VS [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)per sviluppare dispositivi IoT, vedere .

## <a name="prerequisites"></a>Prerequisiti

Installare [Visual Studio Code](https://code.visualstudio.com/).

Utilizzare la procedura seguente per installare il pacchetto di estensione nel codice VS.

1. In Codice VS, selezionare la scheda **Estensioni.**
1. Cercare e installare gli strumenti **IoT di Azure** dal marketplace.

## <a name="generate-device-code-and-project"></a>Genera codice e progetto del dispositivo

Nel codice VS, utilizzare **Ctrl , Maiusc e P** per aprire la tavolozza dei comandi, immettere **IoT Plug and Play**e selezionare Genera **stub codice dispositivo** per configurare il codice scheletro e il tipo di progetto. Nell'elenco seguente viene descritto in dettaglio ogni passaggio:The following list describes each step in detail:

- **DCM da utilizzare per generare il codice**. Per generare lo scheletro del codice del dispositivo, aprire la cartella che contiene il DCM e i file di interfaccia che implementa. Se il generatore di codice non riesce a trovare un'interfaccia necessaria per un DCM, la scarica dal repository dei modelli in base alle esigenze.

- **Lingua del codice del dispositivo**. Attualmente, il generatore di codice supporta solo ANSI C.

- **Nome del progetto**. Il nome del progetto viene utilizzato come nome della cartella per il codice generato e altri file di progetto. Per impostazione predefinita, la cartella viene posizionata accanto al file DCM. Per evitare di dover copiare manualmente la cartella del codice generato ogni volta che si aggiorna il DCM e si rigenera il codice del dispositivo, mantenere il file DCM nella stessa cartella della cartella del progetto.

- **Metodo per la connessione ad Azure IoT**. The generated files also contain code to configure the device to connect to Azure IoT Hub. È possibile scegliere di connettersi direttamente [all'hub Di Azure IoT](https://docs.microsoft.com/azure/iot-hub) o usare il [servizio Device Provisioning.](https://docs.microsoft.com/azure/iot-dps)

    - Tramite la stringa di connessione del **dispositivo IoT Hub:** specificare la stringa di connessione del dispositivo affinché l'applicazione del dispositivo si connetta direttamente all'hub IoT.
    - **Tramite chiave simmetrica DPS**: specificare **l'ambito ID**, la **chiave simmetrica** e l'ID **dispositivo** per l'applicazione del dispositivo necessari per connettersi all'hub IoT o a IoT Central utilizzando DPS.

- **Tipo di progetto**. Il generatore di codice genera anche un progetto CMake o Arduino. Attualmente, i tipi di progetto supportati sono:

    - **CMake Project on Windows**: per un progetto per dispositivi che utilizza [CMake](https://cmake.org/) come sistema di compilazione in Windows. Questa opzione `CMakeLists.txt` genera con le configurazioni di DEVICE SDK nella stessa cartella del codice C.
    - **CMake Project su Linux**: per un progetto per dispositivi che utilizza [CMake](https://cmake.org/) come sistema di compilazione su Linux. Questa opzione `CMakeLists.txt` genera con le configurazioni di DEVICE SDK nella stessa cartella del codice C.
    - **MXChip IoT DevKit project**: per un progetto per dispositivi che viene eseguito su un dispositivo [MXChip IoT DevKit.](https://aka.ms/iot-devkit) Questa opzione genera un progetto Arduino che è possibile [usare nel codice VS](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) o nell'IDE di Arduino per compilare ed eseguire in un dispositivo DevKit IoT.

- **Tipo SDK dispositivo**. Se si seleziona CMake come tipo di progetto, questo è il passaggio per `CMakeLists.txt`configurare il modo in cui il codice generato includerà Azure IoT C device SDK in :

    - **Tramite codice sorgente**: il codice generato si basa sul [codice sorgente dell'SDK](https://github.com/Azure/azure-iot-sdk-c) del dispositivo da includere in e compilare con esso. Questa operazione è consigliata se è stato personalizzato il codice sorgente dell'SDK del dispositivo.
    - **Via Vcpkg**: il codice generato si basa sul [dispositivo SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c) da includere e compilare insieme ad esso. Questo è il modo consigliato per i dispositivi che eseguono Windows, Linux o macOS.

    > [!NOTE]
    > Il supporto di macOS per Azure IoT C device SDK Vcpkg sta lavorando in corso.

Il generatore di codice tenta di utilizzare DCM e i file di interfaccia che si trovano nella cartella locale. Se i file di interfaccia non si trova nella cartella locale, il generatore di codice li cerca nel repository dei modelli pubblici o nel repository dei modelli aziendali. [I file di interfaccia comuni](./concepts-common-interfaces.md) vengono archiviati nel repository dei modelli pubblici.

Al termine della generazione del codice, l'estensione apre una nuova finestra del codice VS con il codice. Se si apre un file generato, ad esempio **main.c**, è possibile che IntelliSense riporti che non è possibile aprire i file di origine di C SDK. Per abilitare l'IntelliSense corretto e l'esplorazione del codice, utilizzare la procedura seguente per includere l'origine C SDK:

1. Nel codice VS, utilizzare **Ctrl , Maiusc e P** per aprire la tavolozza dei comandi, digitare e selezionare **C/C : Modifica configurazioni (JSON)** per aprire il file **c_cpp_properties.json.**

1. Aggiungere il percorso dell'SDK `includePath` del dispositivo nella sezione:

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. Salvare il file.

## <a name="use-the-generated-code"></a>Utilizzare il codice generato

Le istruzioni seguenti descrivono come usare il codice generato nel proprio progetto per dispositivi su piattaforme di macchine di sviluppo diverse. Le istruzioni presuppongono l'uso di una stringa di connessione del dispositivo dell'hub IoT con il codice generato:The instructions assume you're using an IoT Hub device connection string with the generated code:

### <a name="linux"></a>Linux

Per compilare il codice del dispositivo con il dispositivo C SDK Vcpkg usando CMake in un ambiente Linux come Ubuntu o Debian:

1. Aprire un'applicazione terminale.

1. Installare **GCC**, `cmake` **Git**e tutte `apt-get` le dipendenze utilizzando il comando :

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verificare che la versione di `cmake` sia superiore alla **2.8.12** e che la versione di **GCC** sia superiore alla **4.4.7**.

    ```bash
    cmake --version
    gcc --version
    ```

1. Installare Vcpkg:

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    Quindi, per collegare [l'integrazione](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)a livello di utente, eseguire:

    ```bash
    ./vcpkg integrate install
    ```

1. Installare Azure IoT SDK per dispositivi per C Vcpkg:

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. Creare `cmake` una sottodirectory nella cartella contiene lo stub del codice generato e passare a tale cartella:

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per usare CMake per compilare l'SDK del dispositivo e lo stub del codice generato:

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. Al termine della compilazione, eseguire l'applicazione specificando la stringa di connessione del dispositivo Dell'hub IoT come parametro.

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>WINDOWS

Per compilare il codice del dispositivo con il dispositivo C SDK in Windows utilizzando CMake e i compilatori di Visual Studio C/C, dalla riga di comando, vedere la [guida introduttiva di Plug and Play IoT](./quickstart-create-pnp-device-windows.md). I passaggi seguenti illustrano come compilare il codice del dispositivo con il dispositivo C SDK Vcpkg come progetto CMake in Visual Studio.

1. Seguire i passaggi nella [guida introduttiva](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment) per installare l'SDK del dispositivo Azure IoT per C tramite Vcpkg.

1. Installare [Visual Studio 2019 (Community, Professional o Enterprise):](https://visualstudio.microsoft.com/downloads/) assicurarsi di includere il componente di **gestione pacchetti NuGet** e il carico di lavoro **Sviluppo Desktop con C.**

1. Aprire Visual Studio, scegliere File > Apri `CMakeLists.txt` > **CMake...** per aprire la cartella nella cartella contiene il codice generato.

1. Nella barra degli strumenti **Generale,** individuare l'elenco a discesa **Configurazioni.** Selezionare **Gestisci configurazione** per aggiungere l'impostazione CMake per il progetto.

    ![Gestire la configurazione](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. In **Impostazioni CMake**aggiungere una nuova configurazione e selezionare **x86-Debug** come destinazione.

1. In **Argomenti comando CMake**aggiungere la riga seguente:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Salvare il file.

1. Passare a **x86-Debug** nell'elenco a discesa **Configurazioni.** Sono necessari alcuni secondi per il CMake per generare la cache per esso. Visualizzare la finestra Output per visualizzare lo stato di avanzamento.

    ![CMake Output](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. In **Esplora soluzioni**fare clic `CMakeLists.txt` con il pulsante destro del mouse sulla cartella radice e **scegliere Compila** dal menu di scelta rapida per compilare lo stub del codice generato con l'SDK del dispositivo.

1. Dopo la compilazione ha esito positivo, al prompt dei comandi eseguire l'applicazione specificando la stringa di connessione del dispositivo Hub IoT come parametro.

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Per ulteriori informazioni sull'utilizzo di CMake in Visual Studio, consultate Compilazione di [un progetto CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

I passaggi seguenti illustrano come compilare il codice del dispositivo con il codice sorgente C SDK del dispositivo in macOS usando CMake:

1. Aprire l'applicazione terminale.

1. Utilizzare [Homebrew](https://homebrew.sh) per installare tutte le dipendenze:

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. Verificare che [CMake](https://cmake.org/) sia almeno la versione **2.8.12:**

    ```bash
    cmake --version
    ```

1. [Patch CURL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os) all'ultima versione disponibile.

1. Nella cartella che contiene il codice generato clonare il repository [di Azure IoT C SDK:In](https://github.com/Azure/azure-iot-sdk-c) the folder that contains the generated code, clone the Azure IoT C SDK repository:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Creare una `cmake` cartella denominata nella cartella che contiene il codice generato e passare a tale cartella.

    ```bash
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per usare CMake per compilare l'SDK del dispositivo e lo stub del codice generato:

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. Al termine della compilazione, eseguire l'applicazione specificando la stringa di connessione del dispositivo Dell'hub IoT come parametro.

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Iterazione rigenerando il codice dello scheletro

Il generatore di codice può rigenerare il codice se si aggiornano i file DCM o di interfaccia. Supponendo che il codice del dispositivo sia già stato generato da un file DCM, per rigenerare il codice:

1. Dopo avere aperto la cartella con i file del modello di funzionalità di dispositivo, premere **CTRL+MAIUSC+P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

1. Scegliere il file DCM aggiornato.

1. Selezionare **Rigenera codice per il nome**del progetto.

1. Il generatore di codice utilizza l'impostazione precedente configurata e rigenera il codice. Tuttavia, non sovrascrive i file che possono `main.c` `{project_name}_impl.c`contenere codice utente come e .

> [!NOTE]
> Se aggiorni l'ID URN nel file di interfaccia, questo viene considerato come una nuova interfaccia. Quando si genera nuovamente il codice, il generatore di codice genera codice `{project_name}_impl.c` per l'interfaccia ma non sovrascrive quello originale nel file.

## <a name="problems-and-feedback"></a>Problemi e feedback

Azure IoT Tools is an open-sourced project on GitHub. Per eventuali problemi e richieste di funzionalità, è possibile [creare un problema in GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo delle procedure, si è appreso come usare Visual Studio e visual Studio Code per generare lo scheletro di codice C per implementare l'applicazione per dispositivi. Un passaggio successivo consigliato consiste nell'imparare a installare e usare lo strumento Azure IoT Explorer.A suggested next step is to learn how to [Install and use Azure IoT explorer](./howto-install-iot-explorer.md) tool.

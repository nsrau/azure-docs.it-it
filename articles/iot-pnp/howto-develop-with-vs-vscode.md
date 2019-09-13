---
title: Usare Visual Studio e Visual Studio Code per compilare i dispositivi Plug and Play Preview | Microsoft Docs
description: Usa Visual Studio e Visual Studio Code per accelerare la creazione di Plug and Play dei modelli di dispositivo e l'implementazione del codice del dispositivo.
author: liydu
ms.author: liydu
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d68a3f99096ca64b357239f61cf7ff17d6fc3f83
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935729"
---
# <a name="use-azure-iot-tools-for-visual-studio-code"></a>Usare gli strumenti di Azure per la Visual Studio Code

Gli strumenti di Azure Internet per la Visual Studio Code offrono un ambiente integrato per la creazione di modelli di funzionalità del dispositivo (DCM) e interfacce, la pubblicazione in repository di modelli e la generazione di codice Skeleton C per implementare l'applicazione del dispositivo.

Questo articolo illustra come:

- Genera il codice del dispositivo e il progetto di applicazione.
- Usare il codice generato nel progetto del dispositivo.
- Eseguire l'iterazione rigenerando il codice di scheletro.

Per ulteriori informazioni sull'utilizzo del VS Code per sviluppare dispositivi Internet, vedere [https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench).

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

- **Tipo di progetto**. Il generatore di codice genera anche un file di progetto in modo che sia possibile integrare il codice nel progetto o nel progetto SDK per dispositivi. Attualmente, i tipi di progetto supportati sono:

    - **Progetto CMake**: per un progetto di dispositivo che usa [CMake](https://cmake.org/) come sistema di compilazione. Questa opzione genera un `CMakeLists.txt` file nella stessa cartella del codice C.
    - **Progetto MXChip DevKit**: per un progetto di dispositivo che viene eseguito in un dispositivo [MXChip DevKit](https://aka.ms/iot-devkit) . Questa opzione genera un progetto Arduino che è possibile [usare in vs code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) o nell'IDE di Arduino per compilare ed eseguire su un dispositivo devkit.

- **Metodo per la connessione ad Azure**. I file generati contengono anche codice per configurare il dispositivo per la connessione all'hub di Azure. È possibile scegliere di connettersi direttamente all' [Hub Azure](https://docs.microsoft.com/azure/iot-hub) Internet o usare il [servizio Device provisioning](https://docs.microsoft.com/azure/iot-dps).

    - **Tramite la stringa di connessione del dispositivo dell'hub**Internet: specificare la stringa di connessione del dispositivo per l'applicazione del dispositivo per connettersi direttamente all'hub Internet.
    - **Tramite chiave simmetrica DPS**: specificare l' **ID ambito**, l' **ID registrazione**e la **chiave** di firma di accesso condiviso per l'applicazione del dispositivo necessari per connettersi all'hub Internet o IOT Central usando DPS.

Il generatore di codice tenta di usare i file DCM e di interfaccia presenti nella cartella locale. Se i file di interfaccia non si trovano nella cartella locale, il generatore di codice li Cerca nel repository del modello pubblico o nel repository del modello aziendale. I [file di interfaccia comuni](./concepts-common-interfaces.md) vengono archiviati nel repository del modello pubblico.

Al termine della generazione del codice, l'estensione apre una nuova finestra di VS Code con il codice. Se si apre un file generato, ad esempio **Main. c**, è possibile che IntelliSense segnali che non è in grado di aprire i file di origine di c SDK. Per abilitare IntelliSense e l'esplorazione del codice corretti, attenersi alla procedura seguente per includere l'origine C SDK:

1. In VS Code premere **CTRL + MAIUSC + P** per aprire il riquadro comandi, digitare e selezionare **C/:C++ Modificare le configurazioni (JSON** ) per aprire il file **c_cpp_properties. JSON** .

1. Aggiungere il percorso di Device SDK nella `includePath` sezione:

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

Per compilare il codice del dispositivo insieme al Device C SDK usando CMake in un ambiente Linux, ad esempio Ubuntu o Debian:

1. Aprire un'applicazione Terminal.

1. Installare **GCC**, **git**, `cmake`e tutte le dipendenze usando `apt-get` il comando:

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Verificare che la versione `cmake` di sia superiore a **2.8.12** e che la versione di **GCC** sia superiore a **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

1. Clonare il repository [SDK di Azure](https://github.com/Azure/azure-iot-sdk-c) per i C:

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Copiare la cartella che contiene il codice generato nella cartella radice SDK del dispositivo.

1. In vs code aprire il `CMakeLists.txt` file nella cartella radice dell'SDK del dispositivo.

1. Aggiungere la riga seguente alla fine del `CMakeLists.txt` file per includere la cartella stub del codice del dispositivo quando si compila l'SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Creare una cartella denominata `cmake` nella cartella radice dell'SDK del dispositivo e passare a tale cartella.

    ```sh
    mkdir cmake
    cd cmake
    ```

1. Eseguire i comandi seguenti per usare CMake per compilare l'SDK per dispositivi e lo stub del codice generato:

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. Una volta completata la compilazione, eseguire l'applicazione specificando come parametro la stringa di connessione del dispositivo hub Internet.

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

Per compilare il codice del dispositivo insieme al Device C SDK in Windows con CMake e iC++ compilatori di Visual Studio c dalla riga di comando, vedere la [guida introduttiva plug and Play](./quickstart-create-pnp-device.md). I passaggi seguenti illustrano come compilare il codice del dispositivo insieme al Device C SDK come progetto CMake in Visual Studio.

1. Installare [Visual Studio 2019 (community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/) : assicurarsi di includere il componente **Gestione pacchetti NuGet** e lo **sviluppo di applicazioni desktop con carico C++ di** lavoro.

1. Aprire Visual Studio e nella pagina attività **iniziali** selezionare **clona o Estrai codice**:

1. In **percorso repository**clonare il repository di Azure Internet per l' [SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    Per completare questa operazione, è possibile visualizzare lo stato di avanzamento nel riquadro **Team Explorer** .

1. Aprire il repository **Azure-Azure-SDK-c** nel **Team Explorer**, selezionare **Branchs**, cercare il ramo **Public-Preview** ed estrarlo.

    ![Anteprima pubblica](media/howto-develop-with-vs-vscode/vs-public-preview.png)

1. Copiare la cartella che contiene il codice generato nella cartella radice SDK del dispositivo.

1. Aprire la `azure-iot-sdk-c` cartella in Visual Studio.

1. Aprire il `CMakeLists.txt` file nella cartella radice SDK del dispositivo.

1. Aggiungere la riga seguente alla fine del `CMakeLists.txt` file per includere la cartella stub del codice del dispositivo quando si compila l'SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Nella barra degli strumenti **generale** trovare l'elenco a discesa **configurazioni** . Selezionare **Gestisci configurazione** per aggiungere l'impostazione CMake per il progetto.

    ![Gestisci configurazione](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. Nelle **impostazioni di CMake**aggiungere una nuova configurazione e selezionare **x64-Release** come destinazione.

1. In **argomenti del comando cmake**aggiungere la riga seguente:

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. Salvare il file.

1. Nella **Esplora soluzioni**fare clic con il pulsante destro del `CMakeLists.txt` mouse sul nella cartella radice e scegliere **Compila** dal menu di scelta rapida per compilare l'SDK per dispositivi e lo stub del codice generato.

1. Al termine della compilazione, al prompt dei comandi eseguire l'applicazione specificando come parametro la stringa di connessione del dispositivo dell'hub Internet.

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> Per altre informazioni sull'uso di CMake in Visual Studio, vedere [compilare il progetto CMake](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects) .

### <a name="macos"></a>macOS

I passaggi seguenti illustrano come compilare il codice del dispositivo insieme al Device C SDK in macOS con CMake:

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

1. Clonare il repository [SDK di Azure](https://github.com/Azure/azure-iot-sdk-c) per i C:

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Copiare la cartella che contiene il codice generato nella cartella radice SDK del dispositivo.

1. In vs code aprire il `CMakeLists.txt` file nella cartella radice dell'SDK del dispositivo.

1. Aggiungere la riga seguente alla fine del `CMakeLists.txt` file per includere la cartella stub del codice del dispositivo quando si compila l'SDK:

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. Creare una cartella denominata `cmake` nella cartella radice dell'SDK del dispositivo e passare a tale cartella.

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
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>Eseguire l'iterazione rigenerando il codice di scheletro

Il generatore di codice può rigenerare il codice se si aggiornano i file di interfaccia o DCM. Supponendo che sia già stato generato il codice del dispositivo da un file DCM, per rigenerare il codice:

1. Dopo avere aperto la cartella con i file del modello di funzionalità di dispositivo, premere **CTRL+MAIUSC+P** per aprire il riquadro comandi, immettere **Plug and Play IoT** e selezionare **Generate Device Code Stub** (Genera stub codice dispositivo).

1. Scegliere il file DCM aggiornato.

1. Selezionare **rigenera di nuovo il codice per {nome progetto}** .

1. Il generatore di codice usa l'impostazione precedente configurata e rigenera il codice. Tuttavia, non sovrascrive i file che possono contenere codice utente, ad esempio `main.c` e `{project_name}_impl.c`.

> [!NOTE]
> Se si aggiorna l'ID URN nel file di interfaccia, questo viene considerato come una nuova interfaccia. Quando si rigenera il codice, il generatore di codice genera il codice per l'interfaccia ma non sovrascrive quello originale nel `{project_name}_impl.c` file.

## <a name="problems-and-feedback"></a>Problemi e commenti e suggerimenti

Azure Internet per gli strumenti è un progetto open source su GitHub. Per eventuali problemi e richieste di funzionalità, è possibile [creare un problema in GitHub](https://github.com/microsoft/vscode-azure-iot-tools/issues/new).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sulle procedure si è appreso come usare Visual Studio e Visual Studio Code per generare codice Skeleton C per implementare l'applicazione del dispositivo. Un passaggio successivo suggerito consiste nell'apprendere come [installare e usare](./howto-install-iot-explorer.md) lo strumento Azure Internet Explorer.

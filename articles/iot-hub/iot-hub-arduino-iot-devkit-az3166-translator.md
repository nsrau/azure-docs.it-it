---
title: Creare un traduttore IoT DevKit con Funzioni di Azure e Servizi cognitivi | Microsoft Docs
description: Usare il microfono su un dispositivo IoT DevKit per ricevere un messaggio vocale e Servizi cognitivi di Azure per convertirlo in testo tradotto in lingua inglese.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: df7e7b426a8c85c8051d7f588c706a6f8811e183
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518914"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>Usare IoT DevKit AZ3166 con Funzioni di Azure e Servizi cognitivi per creare un traduttore di lingue

Questo articolo illustra come trasformare IoT DevKit in un traduttore di lingue usando [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/). Il dispositivo registrerà la voce e la convertirà in testo in lingua inglese visualizzato sullo schermo del DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per tale scheda con [Azure IoT Device Workbench](https://aka.ms/iot-workbench) oppure il pacchetto di estensione [Azure IoT Tools](https://aka.ms/azure-iot-tools) in Visual Studio Code. Il [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) contiene applicazioni di esempio che consentono di creare prototipi di soluzioni IoT.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare la procedura di questa esercitazione, effettuare le attività seguenti:

* Preparare il DevKit seguendo i passaggi descritti in [Connettere DevKit di IoT AZ3166 all'hub IoT di Azure nel cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="create-azure-cognitive-service"></a>Creare un servizio cognitivo di Azure

1. Nel portale di Azure fare clic su **Crea una risorsa** e cercare **Riconoscimento vocale**. Compilare il modulo per creare il servizio di riconoscimento vocale.
  ![Servizio di riconoscimento vocale](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Passare al servizio di riconoscimento vocale appena creato, fare clic sulla sezione **Chiavi** per copiare e prendere nota di **Key1** per l'accesso da DevKit.
  ![Copiare le chiavi](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Aprire il progetto di esempio

1. Assicurarsi che il DevKit IoT **non** sia connesso al computer. Avviare per prima cosa Visual Studio Code, quindi connettere il DevKit al computer.

1. Premere `F1` per aprire il riquadro comandi, digitare e selezionare **Azure IoT Device Workbench: Open Examples** (Azure IoT Device Workbench: Apri esempi). Quindi selezionare **IoT DevKit** (DevKit di IoT) come lavagna.

1. Nella pagina di esempi di IoT Workbench individuare **DevKit Translator** (Traduttore DevKit) e fare clic su **Open Sample** (Apri esempio). Selezionare quindi il percorso predefinito per scaricare il codice di esempio.
  ![Aprire esempio](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>Usare il servizio di riconoscimento vocale con Funzioni di Azure

1. In VS Code premere `F1`, digitare e selezionare **Azure IoT Device Workbench: Provision Azure Services...** (Effettua il provisioning dei servizi di Azure). ![Effettuare il provisioning dei servizi di Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Seguire la procedura per completare il provisioning dell'hub IoT di Azure e di Funzioni di Azure.
   ![Passaggi di provisioning](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Prendere nota del nome del dispositivo dell'hub IoT di Azure creato.

1. Apri `Functions\DevKitTranslatorFunction.cs` e aggiornare le seguenti righe di codice con il nome del dispositivo e la chiave di servizio di riconoscimento vocale si è preso nota.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Premere `F1`, digitare e selezionare **Azure IoT Device Workbench: Deploy to Azure...** (Distribuisce in Azure). Se VS Code chiede conferma per la ridistribuzione, fare clic su **Sì**.
   ![Avviso di distribuzione](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Assicurarsi che la distribuzione abbia esito positivo.
   ![Distribuzione riuscita](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Nel portale di Azure passare alla sezione **App per le funzioni** e trovare l'app per le funzioni di Azure appena creata. Fare clic su `devkit_translator`, quindi fare clic su **</> Recupera URL della funzione** per copiare l'URL.
   ![Copiare l'URL della funzione](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Incollare l'URL nel file `azure_config.h`.
   ![Configurazione di Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Se l'app per le funzioni non funziona correttamente, consultare questa sezione delle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) per risolvere il problema.

## <a name="build-and-upload-device-code"></a>Compilare e caricare il codice del dispositivo

1. Attivare la **modalità di configurazione** per DevKit seguendo questa procedura:
   * Tenere premuto il pulsante **A**.
   * Premere e rilasciare il pulsante **Reset**.

   Nella schermata verranno visualizzati l'ID DevKit e la voce **Configuration** (Configurazione).

   ![Modalità di configurazione di DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Premere `F1`, digitare e selezionare **Azure IoT Device Workbench: Configure Device Settings... > Config Device Connection String** (Configura impostazioni dispositivo > Configura stringa di connessione dispositivo). Selezionare **Select IoT Hub Device Connection String** (Selezionare stringa di connessione dispositivo hub IoT) per configurarla nel DevKit.
   ![Configurare la stringa di connessione](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Verrà visualizzata una notifica quando l'operazione viene completata.
   ![Configurazione completa della stringa di connessione](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Premere nuovamente `F1`, digitare e selezionare **Azure IoT Device Workbench: Upload Device Code** (Azure IoT Device Workbench: Carica il codice del dispositivo). Vengono avviati la compilazione e il caricamento nel codice nel DevKit.
   ![Caricamento del dispositivo](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>Verificare il progetto

Dopo l'inizializzazione dell'app, seguire le istruzioni sullo schermo del DevKit. La lingua di origine predefinita è il cinese.

Per selezionare un'altra lingua per la traduzione:

1. Premere il pulsante A per passare alla modalità di configurazione.

2. Premere il pulsante B per scorrere tra le lingue di origine supportate.

3. Premere il pulsante A per confermare la scelta della lingua di origine.

4. Premere e tenere premuto il pulsante B mentre si parla, quindi rilasciarlo per avviare la traduzione.

5. Il testo tradotto in lingua inglese compare sullo schermo.

![Scorrere per selezionare la lingua](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Risultato della traduzione](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

Nella schermata dei risultati della traduzione è possibile:

- Premere i pulsanti A e B per scorrere e selezionare la lingua di origine.

- Premere il pulsante B per parlare. Per inviare la voce e ottenere il testo della traduzione, rilasciare il pulsante B.

## <a name="how-it-works"></a>Funzionamento

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

IoT DevKit registra la voce e invia una richiesta HTTP per attivare Funzioni di Azure. Funzioni di Azure chiama l'API Traduzione vocale di Servizi cognitivi per eseguire la traduzione. Quando riceve il testo della traduzione, Funzioni di Azure invia al DevKit un messaggio da cloud a dispositivo. La traduzione viene quindi visualizzata sullo schermo.

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti su IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare i canali seguenti per ricevere assistenza:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come usare IoT DevKit come traduttore usando Funzioni di Azure e Servizi cognitivi. In questa procedura si è appreso come:

> [!div class="checklist"]
> * Usare l'attività di Visual Studio Code per l'automazione del provisioning cloud
> * Configurare la stringa di connessione al dispositivo IoT Azure
> * Distribuire la funzione di Azure
> * Testare la traduzione di messaggi vocali

Procedere alle esercitazioni successive per apprendere come:

> [!div class="nextstepaction"]
> [Connettere IoT DevKit AZ3166 all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)

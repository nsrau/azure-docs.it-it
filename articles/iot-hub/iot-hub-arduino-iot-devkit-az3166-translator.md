---
title: Traduttore IoT DevKit con Funzioni di Azure e Servizi cognitivi | Microsoft Docs
description: Usare un microfono sul dispositivo IoT DevKit per ricevere messaggi vocali e Servizi cognitivi di Azure per convertirli in testo tradotto in lingua inglese.
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 5f74e43d20b5954ae4808f82a34372d1e2190481
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>Usare IoT DevKit AZ3166 con Funzioni di Azure e Servizi cognitivi per creare un traduttore di lingue

Questo articolo illustra come trasformare IoT DevKit in un traduttore di lingue usando [Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/). Il dispositivo registrerà la voce e la convertirà in testo in lingua inglese visualizzato sullo schermo del DevKit.

[MXChip IoT DevKit](https://aka.ms/iot-devkit) è una scheda all-in-one compatibile con Arduino con sensori e periferiche avanzati. È possibile sviluppare per questa scheda tramite l'[estensione di Visual Studio Code per Arduino](https://aka.ms/arduino). Per la scheda esiste un [catalogo di progetti](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) in crescita che rappresenta una guida per sviluppare e creare prototipi di soluzioni di Internet delle cose (IoT) che usano i servizi di Microsoft Azure.

## <a name="what-you-need"></a>Elementi necessari

Completare la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) per:

* Connettere il dispositivo DevKit alla rete Wi-Fi
* Preparare l'ambiente di sviluppo

Una sottoscrizione di Azure attiva. Se non è disponibile, è possibile registrarsi tramite uno di questi metodi:

* Attivare una [versione di prova gratuita di 30 giorni dell'account di Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html)
* Richiedere il [credito Azure](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) se si ha un abbonamento a MSDN o una sottoscrizione di Visual Studio

## <a name="step-1-open-the-project-folder"></a>Passaggio 1. Aprire la cartella del progetto

### <a name="a-start-vs-code"></a>R. Avviare Visual Studio Code

- Assicurarsi che il DevKit non sia connesso al PC.
- Avviare Visual Studio Code
- Connettere il dispositivo DevKit al computer.

Visual Studio Code individua DevKit automaticamente e visualizza una pagina di introduzione:

![Pagina Introduzione](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. Aprire la cartella degli esempi di Arduino

Espandere la sezione **ARDUINO EXAMPLES > Examples for MXCHIP AZ3166 > AzureIoT** (ESEMPI ARDUINO > Esempi per MXCHIP AZ3166 e selezionare **DevKitTranslator**. Verrà visualizzata una nuova finestra di Visual Studio Code con la cartella di progetto DEVKITTRANSLATOR all'interno.

![Esempi per IoT DevKit](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

Se si chiude il riquadro involontariamente, è possibile riaprirlo. Premere `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) per aprire il riquadro comandi, digitare **Arduino** e quindi cercare e selezionare **Arduino: Examples**.

## <a name="step-2-provision-azure-services"></a>Passaggio 2. Eseguire il provisioning dei servizi di Azure

Nella finestra della soluzione digitare `Ctrl+P` (macOS: `Cmd+P`) e immettere `task cloud-provision`.

Nel terminale di Visual Studio Code una riga di comando interattiva guiderà nel processo di provisioning dei servizi di Azure necessari:

![Attività di provisioning cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>Passaggio 3. Distribuire Funzioni di Azure

Usare `Ctrl+P` (macOS: `Cmd+P`) per eseguire `task cloud-deploy` e distribuire il codice di Funzioni di Azure. Il completamento di questo processo richiede in genere da 2 a 5 minuti:

![Attività di distribuzione cloud](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Dopo la corretta distribuzione di Funzioni di Azure, compilare il file azure_config.h con il nome dell'app per le funzioni. Per trovarlo si può passare al [portale di Azure](https://portal.azure.com/):

![Trovare il nome dell'app per le funzioni di Azure](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> Se la funzione di Azure non funziona correttamente, consultare questa sezione delle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) per risolvere il problema.

## <a name="step-4-build-and-upload-the-device-code"></a>Passaggio 4. Compilare e caricare il codice del dispositivo

1. Usare `Ctrl+P` (macOS: `Cmd+P`) per eseguire `task config-device-connection`.

2. Il terminale chiederà se si vuole usare la stringa di connessione recuperata nel passaggio `task cloud-provision`. È anche possibile immettere una stringa di connessione del dispositivo personalizzata selezionando **"Crea nuovo..."**

3. Il terminale richiederà di passare alla modalità di configurazione. A tale scopo, tenere premuto il pulsante A, quindi premere e rilasciare il pulsante di reimpostazione. La schermata visualizza l'ID DevKit e la voce 'Configuration' (Configurazione).
  ![Verifica e caricamento dello sketch Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. Al termine di `task config-device-connection`, fare clic su `F1` per caricare i comandi di Visual Studio Code e selezionare `Arduino: Upload`. Visual Studio Code inizia quindi a verificare e a caricare lo sketch Arduino: ![Verifica e caricamento dello sketch Arduino](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

Il DevKit viene riavviato e inizia a eseguire il codice.

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

- Premere il pulsante A e poi B per scorrere e selezionare la lingua di origine.
- Premere il pulsante B per parlare, rilasciarlo per inviare la voce e ottenere il testo della traduzione

## <a name="how-it-works"></a>Funzionamento

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Lo sketch Arduino registra la voce e invia una richiesta HTTP per attivare Funzioni di Azure. Funzioni di Azure chiama l'API Traduzione vocale di Servizi cognitivi per eseguire la traduzione. Quando riceve il testo della traduzione, Funzioni di Azure invia al DevKit un messaggio da cloud a dispositivo. La traduzione viene quindi visualizzata sullo schermo.

## <a name="change-device-id"></a>Modificare l'ID dispositivo

L'ID dispositivo predefinito registrato nell'hub IoT di Azure è **AZ3166**. Per modificarlo, seguire le [istruzioni qui](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).

## <a name="problems-and-feedback"></a>Problemi e commenti

In caso di problemi, fare riferimento alle [domande frequenti](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) o contattare Microsoft tramite i canali seguenti:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Passaggi successivi

Ora è possibile usare IoT DevKit come traduttore usando Funzioni di Azure e Servizi cognitivi. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Usare l'attività di Visual Studio Code per l'automazione del provisioning cloud
> * Configurare la stringa di connessione al dispositivo IoT Azure
> * Distribuire Funzioni di Azure
> * Testare la traduzione di messaggi vocali

Procedere alle esercitazioni successive per apprendere come:

> [!div class="nextstepaction"]
> [Connettere il dispositivo IoT DevKit AZ3166 ad Azure IoT Suite per il monitoraggio remoto](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
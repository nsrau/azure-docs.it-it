---
title: Usare i dispositivi gemelli di Azure per controllare il LED utente del dispositivo MXChip IoT DevKit | Microsoft Docs
description: Questa esercitazione mostra come monitorare gli stati del DevKit e controllare il LED utente usando i dispositivi gemelli dell'hub IoT di Azure.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 6bc1255c5bbb9cf74c97b88600f34e7fcd90ae4f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343152"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

È possibile usare questo esempio per monitorare le informazioni WiFi e gli stati dei sensori del dispositivo MXChip IoT DevKit e controllare il colore del LED utente usando la funzione dei dispositivi gemelli dell'hub IoT di Azure.

## <a name="what-you-learn"></a>Contenuto dell'esercitazione

- Come monitorare gli stati dei sensori di MXChip IoT DevKit.

- Come usare dispositivi gemelli di Azure per controllare il colore del LED RGB del DevKit.

## <a name="what-you-need"></a>Elementi necessari

- Configurare l'ambiente di sviluppo seguendo la [Guida introduttiva](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

- Dalla finestra del terminale GitBash (o da un'altra interfaccia della riga di comando Git) digitare i comandi seguenti:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Eseguire il provisioning dei servizi di Azure

1. Fare clic sul menu a discesa **Attività** in Visual Studio Code e selezionare **Esegui attività...** - **cloud-provision**.

2. Lo stato di avanzamento viene visualizzato nella scheda **TERMINALE** del pannello **Benvenuti**.

3. Quando viene richiesto con il messaggio *What subscription would you like to choose?* (Quale sottoscrizione si vuole scegliere?), selezionare una sottoscrizione.

4. Selezionare o scegliere un gruppo di risorse. 
 
   > [!NOTE]
   > Se si dispone già di un hub IoT gratuito, ignorare questo passaggio.

5. Quando viene richiesto con il messaggio *What IoT hub would you like to choose?* (Quale hub IoT si vuole scegliere?), selezionare o creare un hub IoT.

6. Viene visualizzato un output simile a *app per le funzioni: nome app per le funzioni: xxx*. Annotare il nome dell'app per le funzioni, che verrà usato in un passaggio successivo.

7. Attendere il completamento della distribuzione del modello di Azure Resource Manager, indicato dalla visualizzazione del messaggio *Resource Manager template deployment: Done* (Distribuzione del modello di Resource Manager: completata).

## <a name="deploy-function-app"></a>Distribuire l'app per le funzioni

1. Fare clic sul menu a discesa **Attività** in Visual Studio Code e selezionare **Esegui attività...** - **cloud-deploy**.

2. Attendere il completamento del processo di caricamento del codice dell'app per le funzioni. Verrà visualizzato il messaggio *function app deploys: Done* (Distribuzione app per le funzioni: completata).

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Configurare la stringa di connessione al dispositivo hub IoT in DevKit

1. Connettere il dispositivo MXChip IoT DevKit al computer.

2. Fare clic sul menu a discesa **Attività** in Visual Studio Code e selezionare **Esegui attività...** - **config-device-connection**

3. Sul dispositivo MXChip IoT DevKit premere e tenere premuto il pulsante **A**, premere il pulsante di**reimpostazione**, quindi rilasciare il pulsante **A** per fare in modo che il DevKit entri in modalità di configurazione.

4. Attendere il completamento del processo di configurazione della stringa di connessione.

## <a name="upload-arduino-code-to-devkit"></a>Caricare il codice Arduino in DevKit

Con il dispositivo MXChip IoT DevKit connesso al computer:

1. Fare clic sul menu a discesa **Attività** in Visual Studio Code e selezionare **Esegui attività di compilazione...** Lo sketch Arduino viene compilato e caricato nel dispositivo DevKit.

2. Dopo il caricamento dello sketch viene visualizzato il messaggio *Build & Upload Sketch: success* (Compilazione e caricamento sketch: operazione riuscita).

## <a name="monitor-devkit-state-in-browser"></a>Monitorare lo stato del dispositivo DevKit nel browser

1. In un Web browser aprire il file `DevKitState\web\index.html` creato durante il passaggio [Elementi necessari](#whatyouneed).

2. Viene visualizzata la pagina Web seguente:![Specificare il nome dell'app per le funzioni.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Immettere il nome dell'app per le funzioni annotato in precedenza.

4. Scegliere il pulsante **Connetti**

5. Entro pochi secondi la pagina viene aggiornata e mostra lo stato della connessione WiFi del dispositivo DevKit e lo stato di ognuno dei sensori onboard.

## <a name="control-the-devkits-user-led"></a>Controllare il LED utente del dispositivo DevKit

1. Fare clic sull'immagine del LED utente nell'illustrazione della pagina Web.

2. Entro pochi secondi la schermata viene aggiornata e mostra lo stato di colore corrente del LED utente.

3. Provare a modificare il valore del colore del LED RGB facendo clic in vari punti dei dispositivi di scorrimento RGB.

## <a name="example-operation"></a>Esempio di funzionamento

![Procedura di test di esempio](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> È possibile visualizzare i dati non elaborati del dispositivo gemello nel portale di Azure: Hub IoT -\> Dispositivi IoT -\> *\<dispositivo utente\>* -\> Dispositivo gemello.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come:
- Connettere un dispositivo MXChip IoT DevKit all'acceleratore di soluzioni di monitoraggio remoto di Azure IoT.
- Usare la funzione dei dispositivi gemelli di Azure IoT per rilevare e controllare il colore del LED RGB del DevKit.

Ecco i passaggi successivi suggeriti:

* [Panoramica dell'acceleratore di soluzioni di monitoraggio remoto di Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Connect an MXChip IoT DevKit device to your Azure IoT Central application](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit) (Connettere un dispositivo MXChip IoT DevKit all'applicazione Azure IoT Central)

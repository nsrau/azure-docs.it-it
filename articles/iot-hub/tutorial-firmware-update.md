---
title: Aggiornare il firmware del dispositivo con l'hub IoT di Azure | Microsoft Docs
description: Implementare un processo di aggiornamento del firmware di un dispositivo usando processi e dispositivi gemelli.
services: iot-hub
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2019
ms.custom: mvc
ms.openlocfilehash: 1418a9815e155a0c491fc65b16307fa2755bd964
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59008903"
---
# <a name="tutorial-implement-a-device-firmware-update-process"></a>Esercitazione: Implementare un processo di aggiornamento del firmware del dispositivo

Potrebbe essere necessario aggiornare il firmware nei dispositivi connessi all'hub IoT. È ad esempio possibile aggiungere nuove funzionalità al firmware o applicare patch di sicurezza. In molti scenari IoT non è possibile visitare fisicamente e quindi applicare manualmente gli aggiornamenti del firmware ai dispositivi. Questa esercitazione illustra come avviare e monitorare il processo di aggiornamento del firmware in modalità remota tramite un'applicazione back-end connessa all'hub.

Per creare e monitorare il processo di aggiornamento del firmware, l'applicazione back-end in questa esercitazione crea una _configurazione_ nell'hub IoT. La [gestione automatica dei dispositivi](iot-hub-auto-device-config.md) dell'hub IoT usa questa configurazione per aggiornare un set di _proprietà desiderate del dispositivo gemello_ in tutti i refrigeratori. Le proprietà desiderate specificano i dettagli dell'aggiornamento del firmware necessario. Mentre i refrigeratori eseguono il processo di aggiornamento del firmware, segnalano lo stato all'applicazione back-end usando le _proprietà segnalate dal dispositivo gemello_. L'applicazione back-end può usare la configurazione per monitorare le proprietà segnalate inviate dal dispositivo e per tenere traccia del processo di aggiornamento del firmware fino al completamento:

![Processo di aggiornamento del firmware](media/tutorial-firmware-update/Process.png)

In questa esercitazione si completano le attività seguenti:

> [!div class="checklist"]
> * Creare un hub IoT e aggiungere un dispositivo di test nel registro delle identità dei dispositivi.
> * Creare una configurazione per definire l'aggiornamento del firmware.
> * Simulare il processo di aggiornamento del firmware in un dispositivo.
> * Ricevere gli aggiornamenti di stato dal dispositivo durante l'avanzamento dell'aggiornamento del firmware.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Le due applicazioni di esempio eseguite in questa guida introduttiva sono scritte in Node.js. È necessario avere Node.js v4.x.x o versione successiva nel computer di sviluppo.

È possibile scaricare Node.js per più piattaforme da [nodejs.org](https://nodejs.org).

È possibile verificare la versione corrente di Node.js installata nel computer di sviluppo tramite il comando seguente:

```cmd/sh
node --version
```

Scaricare il progetto di esempio di Node.js da https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip ed estrarre l'archivio ZIP.

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Per completare questa esercitazione, la sottoscrizione di Azure deve avere un hub IoT con un dispositivo aggiunto al registro delle identità dei dispositivi. La voce del registro delle identità del dispositivo consente al dispositivo simulato in esecuzione in questa esercitazione di connettersi all'hub.

Se non si dispone già di un hub IoT impostato nella sottoscrizione, è possibile configurarne uno con lo script dell'interfaccia della riga di comando seguente. Questo script usa il nome **tutorial-iot-hub** per l'hub IoT; è consigliabile sostituire questo nome con uno univoco in fase di esecuzione. Lo script crea il gruppo di risorse e l'hub nell'area **Stati Uniti centrali**, che è possibile sostituire con un'area più vicina all'utente. Lo script recupera la stringa di connessione del servizio hub IoT, utilizzabile nell'applicazione di esempio di back-end per la connessione all'hub IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Create a resource group
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later
az iot hub show-connection-string --name $hubname -o table

```

Questa esercitazione usa un dispositivo simulato chiamato **MyFirmwareUpdateDevice**. Lo script seguente aggiunge questo dispositivo nel registro delle identità dei dispositivi, imposta un valore di tag e ne recupera la stringa di connessione:

```azurecli-interactive
# Set the name of your IoT hub
hubname=tutorial-iot-hub

# Create the device in the identity registry
az iot hub device-identity create --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Add a device type tag
az iot hub device-twin update --device-id MyFirmwareUpdateDevice --hub-name $hubname --set tags='{"devicetype":"chiller"}'

# Retrieve the device connection string, you need this later
az iot hub device-identity show-connection-string --device-id MyFirmwareUpdateDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

> [!TIP]
> Se si eseguono questi comandi in un prompt dei comandi di Windows o in prompt di PowerShell, vedere la pagina [azure-iot-cli-extension tips](https://github.com/Azure/azure-iot-cli-extension/wiki/Tips
) per informazioni su come racchiudere tra virgolette le stringhe JSON.

## <a name="start-the-firmware-update"></a>Avviare l'aggiornamento del firmware

Si crea una [configurazione di gestione automatica dei dispositivi](iot-hub-automatic-device-management.md#create-a-configuration) nell'applicazione back-end per iniziare il processo di aggiornamento del firmware in tutti i dispositivi contrassegnati con **devicetype** chiller. In questa sezione verrà illustrato come:

* Creare una configurazione da un'applicazione back-end.
* Monitorare il processo fino al completamento.

### <a name="use-desired-properties-to-start-the-firmware-upgrade-from-the-back-end-application"></a>Usare le proprietà desiderate per avviare l'aggiornamento del firmware dall'applicazione back-end

Per visualizzare il codice dell'applicazione back-end che crea la configurazione, passare alla cartella **iot-hub/Tutorials/FirmwareUpdate** nel progetto Node.js di esempio scaricato. Aprire quindi il file ServiceClient.js in un editor di testo.

L'applicazione back-end crea la configurazione seguente:

[!code-javascript[Automatic device management configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=configuration "Automatic device management configuration")]

La configurazione include le sezioni seguenti:

* `content` specifica le proprietà desiderate del firmware inviate ai dispositivi selezionati.
* `metrics` specifica le query da eseguire che segnalano lo stato dell'aggiornamento del firmware.
* `targetCondition` seleziona i dispositivi per ricevere l'aggiornamento del firmware.
* `priorty` imposta la priorità relativa di questa configurazione rispetto alle altre configurazioni.

L'applicazione back-end usa il codice seguente per creare la configurazione per impostare le proprietà desiderate:

[!code-javascript[Create configuration](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=createConfiguration "Create configuration")]

Dopo la creazione della configurazione, l'applicazione monitora l'aggiornamento del firmware:

[!code-javascript[Monitor firmware update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/ServiceClient.js?name=monitorConfiguration "Monitor firmware update")]

Una configurazione segnala due tipi di metriche:

* Metriche di sistema che segnalano il numero di dispositivi di destinazione e il numero di dispositivi a cui è stato applicato l'aggiornamento.
* Metriche personalizzate generate dalle query aggiungete alla configurazione. In questa esercitazione le query segnalano quanti dispositivi si trovano in ogni fase del processo di aggiornamento.

### <a name="respond-to-the-firmware-upgrade-request-on-the-device"></a>Rispondere alla richiesta di aggiornamento del firmware sul dispositivo

Per visualizzare il codice del dispositivo simulato che gestisce le proprietà desiderate del firmware inviate dall'applicazione back-end, passare alla cartella **iot-hub/Tutorials/FirmwareUpdate** nel progetto Node.js di esempio scaricato. Aprire quindi il file SimulatedDevice.js in un editor di testo.

L'applicazione del dispositivo simulato crea un gestore per gli aggiornamenti delle proprietà desiderate **properties.desired.firmware** nel dispositivo gemello. Nel gestore esegue alcuni controlli di base sulle proprietà desiderate prima di avviare il processo di aggiornamento:

[!code-javascript[Handle desired property update](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=initiateUpdate "Handle desired property update")]

## <a name="update-the-firmware"></a>Aggiornare il firmware

La funzione **initiateFirmwareUpdateFlow** esegue l'aggiornamento. Questa funzione usa la funzione **waterfall** per eseguire in sequenza le fasi del processo di aggiornamento. In questo esempio l'aggiornamento del firmware include quattro fasi. La prima fase scarica l'immagine, la seconda verifica l'immagine con un checksum, la terza applica l'immagine e l'ultima riavvia il dispositivo:

[!code-javascript[Firmware update flow](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=firmwareupdateflow "Firmware update flow")]

Durante il processo di aggiornamento, il dispositivo simulato segnala l'avanzamento con le proprietà segnalate:

[!code-javascript[Reported properties](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=reportedProperties "Reported properties")]

Il frammento seguente illustra l'implementazione della fase di download. Durante la fase di download, il dispositivo simulato usa le proprietà segnalate per inviare informazioni sullo stato all'applicazione back-end:

[!code-javascript[Download image phase](~/iot-samples-node/iot-hub/Tutorials/FirmwareUpdate/SimulatedDevice.js?name=downloadimagephase "Download image phase")]

## <a name="run-the-sample"></a>Eseguire l'esempio

In questa sezione si eseguono due applicazioni di esempio per osservare come un'applicazione back-end crea la configurazione per gestire il processo di aggiornamento del firmware sul dispositivo simulato.

Per eseguire le applicazioni back-end e del dispositivo simulato, sono necessarie le stringhe di connessione del servizio e del dispositivo. Le stringhe di connessione sono state annotate in fase di creazione delle risorse all'inizio di questa esercitazione.

Per eseguire l'applicazione del dispositivo simulato, aprire una shell o una finestra del prompt dei comandi e passare alla cartella **iot-hub/Tutorials/FirmwareUpdate** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Per eseguire l'applicazione back-end, aprire un'altra shell o un'altra finestra del prompt dei comandi. Passare quindi alla cartella **iot-hub/Tutorials/FirmwareUpdate** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

La schermata seguente mostra l'output dell'applicazione del dispositivo simulato e mostra come risponde all'aggiornamento delle proprietà desiderate del firmware dall'applicazione back-end:

![Dispositivo simulato](./media/tutorial-firmware-update/SimulatedDevice.png)

La schermata seguente mostra l'output dell'applicazione back-end ed evidenzia come crea la configurazione per aggiornare le proprietà desiderate del firmware:

![Applicazione back-end](./media/tutorial-firmware-update/BackEnd1.png)

La schermata seguente mostra l'output dell'applicazione back-end ed evidenzia come monitora le metriche di aggiornamento del firmware dal dispositivo simulato:

![Applicazione back-end](./media/tutorial-firmware-update/BackEnd2.png)

A causa della latenza nel registro delle identità dei dispositivi dell'hub IoT, potrebbe non essere visibile ogni aggiornamento dello stato inviato all'applicazione back-end. È anche possibile visualizzare le metriche nel portale nella sezione **Automatic device management -> IoT device configuration** (Gestione automatica dei dispositivi -> Configurazione del dispositivo IoT) dell'hub IoT:

![Visualizzare la configurazione nel portale](./media/tutorial-firmware-update/portalview.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di completare l'esercitazione successiva, lasciare il gruppo di risorse e l'hub IoT per riutilizzarli in seguito.

Se l'hub IoT non è più necessario, eliminarlo insieme al gruppo di risorse nel portale, Per farlo, selezionare il gruppo di risorse **tutorial-iot-hub-rg** che contiene l'hub IoT e fare clic su **Elimina**.

In alternativa, usare l'interfaccia della riga di comando:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come implementare un processo di aggiornamento del firmware per i dispositivi connessi. Passare all'esercitazione successiva per informazioni su come usare gli strumenti del portale dell'hub IoT di Azure e i comandi dell'interfaccia della riga di comando di Azure per testare la connettività dei dispositivi.

> [!div class="nextstepaction"]
> [Usare un dispositivo simulato per testare la connettività con l'hub IoT](tutorial-connectivity.md)

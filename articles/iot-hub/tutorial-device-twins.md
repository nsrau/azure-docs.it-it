---
title: Sincronizzare lo stato del dispositivo dall'hub IoT di Azure | Microsoft Docs
description: Usare dispositivi gemelli per sincronizzare lo stato tra i dispositivi e l'hub IoT
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
ms.assetid: ''
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/14/2018
ms.author: dobett
ms.custom: mvc
ms.openlocfilehash: 3d0f24331243c22fa356de7778a89185df2cde4e
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003211"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>Esercitazione: Configurare i dispositivi da un servizio back-end

Oltre a ricevere dati di telemetria dai dispositivi, potrebbe essere necessario configurare i dispositivi dal servizio back-end. Quando si invia una configurazione desiderata ai dispositivi, potrebbe anche essere necessario ricevere aggiornamenti di stato e di conformità da tali dispositivi. Ad esempio, è possibile impostare un intervallo di temperatura operativa di destinazione per un dispositivo o raccogliere informazioni sulla versione del firmware dai dispositivi.

Per sincronizzare le informazioni sullo stato tra un dispositivo e un hub IoT, si usano _dispositivi gemelli_. Un [dispositivo gemello](iot-hub-devguide-device-twins.md) è un documento JSON, associato con un dispositivo specifico e archiviato dall'hub IoT nel cloud, in cui è possibile eseguire [query](iot-hub-devguide-query-language.md). Un dispositivo gemello contiene _proprietà desiderate_, _proprietà segnalate_ e _tag_. Una proprietà desiderata viene impostata da un'applicazione back-end e letta da un dispositivo. Una proprietà segnalata viene impostata da un dispositivo e letta da un'applicazione back-end. Un tag viene impostato da un'applicazione back-end e non viene mai inviato a un dispositivo. I tag vengono usati per organizzare i dispositivi. Questa esercitazione illustra come usare le proprietà desiderate e segnalate per sincronizzare le informazioni sullo stato:

![Riepilogo dei dispositivi gemelli](media/tutorial-device-twins/DeviceTwins.png)

In questa esercitazione si eseguono le seguenti attività:

> [!div class="checklist"]
> * Creare un hub IoT e aggiungere un dispositivo di test nel registro delle identità.
> * Usare le proprietà desiderate per inviare informazioni sullo stato al dispositivo simulato.
> * Usare le proprietà segnalate per ricevere informazioni sullo stato dal dispositivo simulato.

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

Per completare questa esercitazione, la sottoscrizione di Azure deve contenere un hub IoT con un dispositivo aggiunto al registro delle identità del dispositivo. La voce del registro delle identità del dispositivo consente al dispositivo simulato in esecuzione in questa esercitazione di connettersi all'hub.

Se non si dispone già di un hub IoT impostato nella sottoscrizione, è possibile configurarne uno con lo script dell'interfaccia della riga di comando seguente. Questo script usa il nome **tutorial-iot-hub** per l'hub IoT; è consigliabile sostituire questo nome con uno univoco in fase di esecuzione. Lo script crea il gruppo di risorse e l'hub nell'area **Stati Uniti centrali**, che è possibile sostituire con un'area più vicina all'utente. Lo script recupera la stringa di connessione del servizio hub IoT, utilizzabile nell'esempio di back-end per la connessione all'hub IoT:

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --hub-name $hubname -o table

```

Questa esercitazione usa un dispositivo simulato chiamato **MyTwinDevice**. Lo script seguente aggiunge questo dispositivo nel registro delle identità e ne recupera la stringa di connessione:

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>Inviare informazioni sullo stato

Le proprietà desiderate vengono usate per inviare informazioni sullo stato da un'applicazione back-end a un dispositivo. In questa sezione verrà illustrato come:

* Ricevere ed elaborare le proprietà desiderate in un dispositivo.
* Inviare le proprietà desiderate da un'applicazione back-end.

Per visualizzare il codice di esempio del dispositivo simulato che riceve le proprietà desiderate, passare alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di esempio di Node.js scaricato. Aprire quindi il file SimulatedDevice.js in un editor di testo.

Le sezioni seguenti descrivono il codice in esecuzione nel dispositivo simulato che risponde alle modifiche delle proprietà desiderate inviate dall'applicazione back-end:

### <a name="retrieve-the-device-twin-object"></a>Recuperare l'oggetto dispositivo gemello

Il codice seguente si connette all'hub IoT tramite una stringa di connessione del dispositivo:

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

Il codice seguente ottiene un dispositivo gemello dall'oggetto client:

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>Proprietà desiderate di esempio

È possibile strutturare le proprietà desiderate in modo utile all'applicazione. Questo esempio usa una proprietà di livello superiore chiamata **fanOn** e raggruppa le proprietà restanti in **componenti** distinti. Il frammento di codice JSON seguente mostra la struttura delle proprietà desiderate usate in questa esercitazione:

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>Creare gestori

È possibile creare gestori per gli aggiornamenti delle proprietà desiderate che rispondono agli aggiornamenti a livelli diversi della gerarchia JSON. Ad esempio, questo gestore rileva tutte le modifiche delle proprietà desiderate inviate al dispositivo da un'applicazione back-end. La variabile **delta** contiene le proprietà desiderate inviate dal back-end della soluzione:

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

Il gestore seguente reagisce solo alle modifiche apportate alla proprietà desiderata **fanOn**:

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>Gestori per più proprietà

Nel frammento di codice JSON di esempio con le proprietà desiderate riportato in precedenza il nodo **climate** sotto **components** contiene due proprietà, **minTemperature** e  **maxTemperature**.

Un oggetto **twin** locale del dispositivo archivia un set completo di proprietà desiderate e segnalate. Il **delta** inviato dal back-end potrebbe aggiornare solo un subset di proprietà desiderate. Nel frammento di codice seguente, se il dispositivo simulato riceve un aggiornamento per un solo valore tra **minTemperature** e **maxTemperature**, viene usato il valore nel dispositivo gemello locale per l'altro valore per configurare il dispositivo:

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

L'oggetto **twin** locale archivia un set completo di proprietà desiderate e segnalate. Il **delta** inviato dal back-end potrebbe aggiornare solo un subset di proprietà desiderate.

### <a name="handle-insert-update-and-delete-operations"></a>Gestire le operazioni di inserimento, aggiornamento ed eliminazione

Le proprietà desiderate inviate dal back-end non indicano quale operazione viene eseguita su una determinata proprietà desiderata. Il codice deve essere in grado di dedurre l'operazione dal set corrente di proprietà desiderate archiviate in locale e dalle modifiche inviate dall'hub.

Il frammento di codice seguente mostra in che modo il dispositivo simulato gestisce le operazioni di inserimento, aggiornamento ed eliminazione nell'elenco dei **componenti** nelle proprietà desiderate. È possibile vedere come usare i valori **null** per indicare che un componente deve essere eliminato:

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>Inviare le proprietà desiderate a un dispositivo dal back-end

Si è visto come un dispositivo implementa i gestori per ricevere gli aggiornamenti delle proprietà desiderate. Questa sezione illustra come inviare modifiche delle proprietà desiderate a un dispositivo da un'applicazione back-end.

Per visualizzare il codice di esempio del dispositivo simulato che riceve le proprietà desiderate, passare alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di esempio di Node.js scaricato. Aprire quindi il file ServiceClient.js in un editor di testo.

Il frammento di codice seguente mostra come connettersi al registro delle identità del dispositivo e accedere al dispositivo gemello per un dispositivo specifico:

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

Il frammento di codice seguente mostra diverse *patch* delle proprietà desiderate che l'applicazione back-end invia al dispositivo:

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

Il frammento di codice seguente illustra in che modo l'applicazione back-end invia un aggiornamento delle proprietà desiderate a un dispositivo:

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>Eseguire le applicazioni

In questa sezione vengono eseguite due applicazioni di esempio per osservare come un'applicazione back-end invia gli aggiornamenti delle proprietà desiderate all'applicazione del dispositivo simulato.

Per eseguire le applicazioni back-end e del dispositivo simulato, sono necessarie le stringhe di connessione del servizio e del dispositivo. Le stringhe di connessione sono state annotate in fase di creazione delle risorse all'inizio di questa esercitazione.

Per eseguire l'applicazione del dispositivo simulato, aprire una shell o una finestra del prompt dei comandi e passare alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Per eseguire l'applicazione back-end, aprire un'altra shell o un'altra finestra del prompt dei comandi. Passare quindi alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

La schermata seguente mostra l'output dell'applicazione del dispositivo simulato ed evidenzia come viene gestito l'aggiornamento della proprietà desiderata **maxTemperature**. È possibile vedere in che modo vengono eseguiti i gestori del componente relativo alle condizioni climatiche e del gestore di livello superiore:

![Dispositivo simulato](./media/tutorial-device-twins/SimulatedDevice1.png)

La schermata seguente mostra l'output dell'applicazione back-end ed evidenzia come viene inviato l'aggiornamento della proprietà desiderata **maxTemperature**:

![Applicazione back-end](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>Ricevere informazioni sullo stato

L'applicazione back-end riceve le informazioni sullo stato da un dispositivo come proprietà segnalate. Un dispositivo imposta le proprietà segnalate e le invia all'hub. Un'applicazione back-end è in grado di leggere i valori correnti delle proprietà segnalate da un dispositivo gemello archiviato nell'hub.

### <a name="send-reported-properties-from-a-device"></a>Inviare le proprietà segnalate da un dispositivo

È possibile inviare aggiornamenti ai valori delle proprietà segnalate come patch. Il frammento di codice seguente mostra un modello per la patch che il dispositivo simulato invia. Il dispositivo simulato aggiorna i campi nella patch prima di inviarla all'hub:

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

Il dispositivo simulato usa la funzione seguente per inviare la patch contenente le proprietà segnalate all'hub:

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>Elaborare le proprietà segnalate

Un'applicazione back-end accede ai valori correnti delle proprietà segnalate per un dispositivo tramite il dispositivo gemello. Il frammento di codice seguente mostra come l'applicazione back-end legge i valori delle proprietà segnalate per il dispositivo simulato:

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>Eseguire le applicazioni

In questa sezione vengono eseguite due applicazioni di esempio per osservare come un'applicazione del dispositivo simulato invia gli aggiornamenti delle proprietà segnalate a un'applicazione back-end.

Vengono eseguite le stesse due applicazioni di esempio eseguite per vedere in che modo le proprietà desiderate sono inviate a un dispositivo.

Per eseguire le applicazioni back-end e del dispositivo simulato, sono necessarie le stringhe di connessione del servizio e del dispositivo. Le stringhe di connessione sono state annotate in fase di creazione delle risorse all'inizio di questa esercitazione.

Per eseguire l'applicazione del dispositivo simulato, aprire una shell o una finestra del prompt dei comandi e passare alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

Per eseguire l'applicazione back-end, aprire un'altra shell o un'altra finestra del prompt dei comandi. Passare quindi alla cartella **iot-hub/Tutorials/DeviceTwins** nel progetto di Node.js scaricato. Eseguire quindi i comandi seguenti:

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

La schermata seguente mostra l'output dell'applicazione del dispositivo simulato ed evidenzia come viene inviato all'hub un aggiornamento delle proprietà segnalate:

![Dispositivo simulato](./media/tutorial-device-twins/SimulatedDevice2.png)

La schermata seguente mostra l'output dell'applicazione back-end ed evidenzia come viene ricevuto ed elaborato un aggiornamento delle proprietà segnalate da un dispositivo:

![Applicazione back-end](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di completare l'esercitazione successiva, lasciare il gruppo di risorse e l'hub IoT per riutilizzarli in seguito.

Se l'hub IoT non è più necessario, eliminarlo insieme al gruppo di risorse nel portale, Per farlo, selezionare il gruppo di risorse **tutorial-iot-hub-rg** che contiene l'hub IoT e fare clic su **Elimina**.

In alternativa, usare l'interfaccia della riga di comando:

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come sincronizzare le informazioni sullo stato tra i dispositivi e l'hub IoT. Passare all'esercitazione successiva per imparare a usare i dispositivi gemelli per implementare un processo di aggiornamento del firmware.

> [!div class="nextstepaction"]
[Implementare un processo di aggiornamento del firmware del dispositivo](tutorial-firmware-update.md)

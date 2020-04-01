---
title: Autenticare i dispositivi downstream - Azure IoT Edge Documenti Microsoft
description: Come autenticare i dispositivi downstream o foglia nell'hub IoT e instradare la connessione tramite i dispositivi gateway Edge di Azure IoT.How to authenticate downstream devices or leaf devices to IoT Hub, and route their connection through Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411504"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream con l'hub IoT di Azure

In uno scenario gateway trasparente, i dispositivi downstream (talvolta denominati dispositivi foglia o dispositivi figlio) necessitano di identità nell'hub IoT come qualsiasi altro dispositivo. Questo articolo illustra le opzioni per l'autenticazione di un dispositivo downstream nell'hub IoT e quindi illustra come dichiarare la connessione gateway.

Esistono tre passaggi generali per configurare una connessione gateway trasparente di successo. In questo articolo viene illustrato il secondo passaggio:This article covers the second step:

1. Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e instradare i messaggi alla destinazione appropriata. Per ulteriori informazioni, consultate [Configurare un dispositivo Edge IoT in modo che funga da gateway trasparente.](how-to-create-transparent-gateway.md)
2. **Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub IoT e sapere per comunicare tramite il dispositivo gateway.**
3. Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

I dispositivi downstream possono eseguire l'autenticazione con l'hub IoT usando uno dei tre metodi seguenti: chiavi simmetriche (talvolta denominate chiavi di accesso condiviso), certificati autofirmati X.509 o certificati firmati dall'autorità di certificazione (CA) X.509. I passaggi di autenticazione sono simili a quelli usati per configurare qualsiasi dispositivo non IoT-Edge con hub IoT, con piccole differenze per dichiarare la relazione gateway.

I passaggi descritti in questo articolo illustrano il provisioning manuale dei dispositivi, non il provisioning automatico con il servizio di provisioning dei dispositivi hub IoT (DPS) di Azure.The steps in this article show manual device provisioning, not automatic provisioning with the Azure IoT Hub Device Provisioning Service (DPS). Il provisioning di dispositivi downstream con DPS non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti in [Configurare un dispositivo Edge IoT in modo che funga da gateway trasparente.](how-to-create-transparent-gateway.md) Se si usa l'autenticazione X.509 per il dispositivo downstream, è necessario usare lo stesso script di generazione di certificati configurato nell'articolo gateway trasparente.

Questo articolo fa riferimento al *nome host* del gateway in diversi punti. Il nome host del gateway viene dichiarato nel parametro **hostname** del file config.yaml nel dispositivo gateway IoT Edge. È indicato nella stringa di connessione del dispositivo downstream. Il nome host del gateway deve essere risolvibile in un indirizzo IP, utilizzando DNS o una voce del file host.

## <a name="register-device-symmetric-key"></a>Dispositivo di registrazione (chiave simmetrica)

L'autenticazione a chiave simmetrica, o autenticazione con chiave di accesso condiviso, è il modo più semplice per eseguire l'autenticazione con l'hub IoT.Symmetric key authentication, or shared access key authentication, is the simplest way to authenticate with IoT Hub. With symmetric key authentication, a base64 key is associated with your IoT device ID in IoT Hub. Includi tale chiave nelle applicazioni IoT in modo che il dispositivo possa presentarla quando si connette all'hub IoT.

### <a name="create-the-device-identity"></a>Creare l'identità del dispositivoCreate the device identity

Aggiungere un nuovo dispositivo IoT nell'hub IoT usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione IoT per il codice di Visual Studio.Add a new IoT device in your IoT hub, using either the Azure portal, Azure CLI, or the IoT extension for Visual Studio Code. Tenere presente che i dispositivi a valle devono essere identificati nell'hub IoT come normali dispositivi IoT, non come dispositivi IoT Edge.

Quando si crea la nuova identità del dispositivo, fornire le informazioni seguenti:When you create the new device identity, provide the following information:

* Creare un ID per il dispositivo.

* Selezionare **Chiave simmetrica** come tipo di autenticazione.

* Facoltativamente, scegliere **di impostare un dispositivo padre** e selezionare il dispositivo gateway IoT Edge a cui si connetterà il dispositivo downstream. Questo passaggio è facoltativo per l'autenticazione a chiave simmetrica, ma è consigliabile perché l'impostazione di un dispositivo padre abilita [le funzionalità offline](offline-capabilities.md) per il dispositivo downstream. È sempre possibile aggiornare i dettagli del dispositivo per aggiungere o modificare l'elemento padre in un secondo momento.

   ![Creare l'ID dispositivo con l'autenticazione della chiave simmetrica nel portaleCreate device ID with symmetric key auth in portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

È possibile usare [l'estensione IoT per l'interfaccia della riga](https://github.com/Azure/azure-iot-cli-extension) di comando di Azure per completare la stessa operazione. L'esempio seguente crea un nuovo dispositivo IoT con autenticazione a chiave simmetrica e assegna un dispositivo padre:The following example creates a new IoT device with symmetric key authentication and assigns a parent device:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi e la gestione padre/figlio, vedere il contenuto di riferimento per i comandi dell'identità del [dispositivo hub az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)


Successivamente, [recuperare e modificare la stringa](#retrieve-and-modify-connection-string) di connessione in modo che il dispositivo sappia di connettersi tramite il gateway.

## <a name="register-device-x509-self-signed"></a>Dispositivo di registrazione (X.509 autofirmato)

Per l'autenticazione autofirmata X.509, talvolta definita autenticazione di identificazione personale, è necessario creare nuovi certificati da inserire nel dispositivo IoT.For X.509 self-signed authentication, sometimes referred to as thumbprint authentication, you need to create new certificates to place on your IoT device. Questi certificati contesto di identificazione personale che si condividono con l'hub IoT per l'autenticazione.

Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile [creare certificati dimostrativi per testare le funzionalità dei dispositivi IoT Edge](how-to-create-test-certificates.md). Quando si generano certificati di prova per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Utilizzando il certificato CA, creare due certificati di dispositivo (primario e secondario) per il dispositivo downstream.

   The device certificate must have the Subject Name set to the Device ID that you will use when registering the IoT device in the Azure IoT Hub. Questa impostazione è obbligatoria per l'autenticazione.

2. Recuperare l'impronta digitale SHA1 (denominata identificazione personale nell'interfaccia dell'hub IoT) da ogni certificato, ovvero una stringa di caratteri esadecimali di 40. Utilizzare il seguente comando openssl per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Eseguire questo comando due volte, una volta per il certificato primario e una per il certificato secondario. Le impronte digitali vengono fornite per entrambi i certificati quando si registra un nuovo dispositivo IoT utilizzando certificati X.509 autofirmati.

3. Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con i valori seguenti:Navigate to your IoT hub in the Azure portal and create a new IoT device identity with the following values:

   * Specificare **l'ID dispositivo** che corrisponde al nome del soggetto dei certificati del dispositivo.
   * Selezionare **X.509 Autofirmato** come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiate dai certificati primari e secondari del dispositivo.
   * Selezionare **Imposta un dispositivo padre** e scegliere il dispositivo gateway IoT Edge a cui si connetterà il dispositivo downstream. Un dispositivo padre è necessario per l'autenticazione X.509 di un dispositivo downstream.

   ![Creare l'ID dispositivo con l'autenticazione autofirmata X.509 nel portaleCreate device ID with X.509 self-signed auth in portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare anche una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati del dispositivo downstream.

   Farete riferimento a questi file nelle applicazioni del dispositivo foglia che si connettono all'hub IoT.You'll reference these files in the leaf device applications that connect to IoT Hub. È possibile usare un servizio come L'insieme di [credenziali delle](https://docs.microsoft.com/azure/key-vault) chiavi di Azure o una funzione come Protocollo di copia [sicura](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

5. A seconda della lingua preferita, esaminare esempi di come è possibile fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C'è: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare [l'estensione IoT per l'interfaccia della riga](https://github.com/Azure/azure-iot-cli-extension) di comando di Azure per completare la stessa operazione di creazione del dispositivo. L'esempio seguente crea un nuovo dispositivo IoT con l'autenticazione autofirmata X.509 e assegna un dispositivo padre:The following example creates a new IoT device with X.509 self-signed authentication and assigns a parent device:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi, la generazione di certificati e la gestione padre e figlio, vedere il contenuto di riferimento per i comandi [dell'identità del dispositivo dell'hub az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Successivamente, [recuperare e modificare la stringa](#retrieve-and-modify-connection-string) di connessione in modo che il dispositivo sappia di connettersi tramite il gateway.

## <a name="register-device-x509-ca-signed"></a>Dispositivo di registrazione (firma CA X.509)

Per l'autenticazione firmata dall'autorità di certificazione (CA) X.509, è necessario un certificato CA radice registrato nell'hub IoT usato per firmare i certificati per il dispositivo IoT.For X.509 certificate authority (CA) signed authentication, you need a root CA certificate registered in IoT Hub that you use to sign certificates for your IoT device. Qualsiasi dispositivo che utilizza un certificato che è stato emesso dal certificato della CA radice o uno dei relativi certificati intermedi sarà consentito l'autenticazione.

Questa sezione si basa sulle istruzioni descritte nell'articolo dell'hub IoT Configurare la [sicurezza X.509 nell'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md)di Azure. Seguire i passaggi in questa sezione per sapere quali valori usare per configurare un dispositivo downstream che si connette tramite un gateway.

Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile [creare certificati dimostrativi per testare le funzionalità dei dispositivi IoT Edge](how-to-create-test-certificates.md). Quando si generano certificati di prova per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Seguire le istruzioni nella sezione Registrare i [certificati CA X.509 nell'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) di Configurare la *sicurezza X.509 nell'hub IoT*di Azure. In questa sezione, eseguire la procedura seguente:

   1. Caricare un certificato CA radice. Se si usano i certificati demo, la CA radice è ** \<il percorso>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verificare di essere proprietari del certificato della CA radice.

2. Seguire le istruzioni nella sezione [Creare un dispositivo X.509 per l'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) di Configurare la *sicurezza X.509 nell'hub IoT*di Azure. In questa sezione, eseguire la procedura seguente:

   1. Aggiungere un nuovo dispositivo. Specificare un nome minuscolo per **l'ID dispositivo**e scegliere il tipo di autenticazione **X.509 CA signed**.
   2. Impostare un dispositivo padre. Per i dispositivi downstream, selezionare **Imposta un dispositivo padre** e scegliere il dispositivo gateway IoT Edge che fornirà la connessione all'hub IoT.For downstream devices, select Set a parent device and choose the IoT Edge gateway device that will provide the connection to IoT Hub.

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato nell'hub IoT per creare questa catena. Usare lo stesso ID dispositivo minuscolo fornito all'identità del dispositivo nel portale.

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare anche una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati del dispositivo downstream.

   Farete riferimento a questi file nelle applicazioni del dispositivo foglia che si connettono all'hub IoT.You'll reference these files in the leaf device applications that connect to IoT Hub. È possibile usare un servizio come L'insieme di [credenziali delle](https://docs.microsoft.com/azure/key-vault) chiavi di Azure o una funzione come Protocollo di copia [sicura](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

5. A seconda della lingua preferita, esaminare esempi di come è possibile fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C'è: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare [l'estensione IoT per l'interfaccia della riga](https://github.com/Azure/azure-iot-cli-extension) di comando di Azure per completare la stessa operazione di creazione del dispositivo. L'esempio seguente crea un nuovo dispositivo IoT con l'autenticazione firmata dalla CA X.509 e assegna un dispositivo padre:The following example creates a new IoT device with X.509 CA signed authentication and assigns a parent device:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Per altre informazioni, vedere il contenuto di riferimento dell'interfaccia della riga di comando di Azure per i comandi [dell'identità del dispositivo dell'hub az iot.](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Successivamente, [recuperare e modificare la stringa](#retrieve-and-modify-connection-string) di connessione in modo che il dispositivo sappia di connettersi tramite il gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperare e modificare la stringa di connessioneRetrieve and modify connection string

Dopo aver creato un'identità del dispositivo IoT nel portale, è possibile recuperarne le chiavi primarie o secondarie. Una di queste chiavi deve essere inclusa nella stringa di connessione usata dalle applicazioni per comunicare con l'hub IoT.One of these keys needs to be included in the connection string that applications use to communicate with IoT Hub. Per l'autenticazione a chiave simmetrica, l'hub IoT fornisce la stringa di connessione completamente formata nei dettagli del dispositivo per comodità. È necessario aggiungere informazioni aggiuntive sul dispositivo gateway alla stringa di connessione.

Le stringhe di connessione per i dispositivi downstream richiedono i componenti seguenti:Connection strings for downstream devices need the following components:

* Hub IoT a cui si connette il dispositivo:`Hostname={iothub name}.azure-devices.net`
* ID del dispositivo registrato con l'hub:`DeviceID={device ID}`
* Chiave primaria o secondaria:`SharedAccessKey={key}`
* Dispositivo gateway con cui si connette il dispositivo. Specificare il valore **hostname** dal file config.yaml del dispositivo del gateway Edge IoT:`GatewayHostName={gateway hostname}`

Tutti insieme, una stringa di connessione completa è simile al:All together, a complete connection string looks like:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se è stata stabilita una relazione padre/figlio per questo dispositivo downstream, è possibile semplificare la stringa di connessione chiamando direttamente il gateway come host di connessione. Le relazioni padre/figlio sono necessarie per l'autenticazione X.509, ma sono facoltative per l'autenticazione a chiave simmetrica. Ad esempio:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

A questo punto, si dovrebbe avere un dispositivo IoT Edge registrato e configurato come gateway. Si dispone anche di un dispositivo IoT downstream registrato e che punta al dispositivo gateway. Il passaggio finale consiste nell'inserire i certificati nel dispositivo downstream in modo che possa connettersi in modo sicuro al gateway.

Continuare con l'articolo successivo della serie gateway [Connettere un dispositivo downstream a un gateway Edge di Azure.](how-to-connect-downstream-device.md)

## <a name="next-steps"></a>Passaggi successivi

Completando questo articolo, dovresti avere un dispositivo IoT Edge che funziona come gateway trasparente e un dispositivo downstream registrato con un hub IoT. Successivamente, è necessario configurare i dispositivi downstream in modo che considerino attendibile il dispositivo gateway e si connettano ad esso in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

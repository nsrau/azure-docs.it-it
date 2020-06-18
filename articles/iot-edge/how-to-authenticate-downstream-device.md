---
title: Autenticare i dispositivi downstream - Azure IoT Edge | Microsoft Docs
description: Come autenticare i dispositivi downstream o i dispositivi foglia nell'hub IoT e instradare la relativa connessione tramite i dispositivi gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3edd29703f74c7671537fbcf08159dd830e5453c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726227"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream con l'hub IoT di Azure

In uno scenario di gateway trasparente, i dispositivi downstream (talvolta detti dispositivi foglia o dispositivi figlio) necessitano di identità nell'hub IoT come qualsiasi altro dispositivo. Questo articolo illustra le opzioni per l'autenticazione di un dispositivo downstream nell'hub IoT e spiega quindi come dichiarare la connessione gateway.

Ci sono tre passaggi generali per configurare correttamente una connessione gateway trasparente. Questo articolo illustra il secondo passaggio:

1. Il dispositivo gateway deve potersi connettere in modo sicuro ai dispositivi downstream, ricevere le comunicazioni dai dispositivi downstream e instradare i messaggi alla destinazione appropriata. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).
2. **Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione nell'hub IoT, nonché essere in grado di comunicare tramite il dispositivo gateway.**
3. Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

I dispositivi downstream possono eseguire l'autenticazione nell'hub IoT usando uno dei tre metodi seguenti: chiavi simmetriche (talvolta dette chiavi di accesso condiviso), certificati X.509 autofirmati o certificati X.509 firmati dall'autorità di certificazione (CA). I passaggi di autenticazione sono simili a quelli usati per configurare qualsiasi dispositivo non IoT Edge con l'hub IoT, con piccole differenze per dichiarare la relazione del gateway.

I passaggi descritti in questo articolo illustrano il provisioning di dispositivi manuale, non il provisioning automatico con il servizio Device Provisioning in hub IoT di Azure. Il provisioning dei dispositivi downstream con il servizio Device Provisioning non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi in [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Se si usa l'autenticazione X.509 per il dispositivo downstream, è necessario usare lo stesso script di generazione del certificato configurato nell'articolo relativo al gateway trasparente.

Questo articolo si riferisce al *nome host del gateway*  in diversi punti. Il nome host del gateway viene dichiarato nel parametro **hostname** del file config.yaml nel dispositivo gateway IoT Edge. Viene fatto riferimento a tale valore nella stringa di connessione del dispositivo downstream. Il nome host del gateway deve essere risolvibile in un indirizzo IP, tramite DNS o una voce di file host.

## <a name="register-device-symmetric-key"></a>Registrare il dispositivo (chiave simmetrica)

L'autenticazione tramite chiave simmetrica, o autenticazione tramite chiave di accesso condiviso, è il modo più semplice per eseguire l'autenticazione nell'hub IoT. Con l'autenticazione tramite chiave simmetrica, una chiave Base64 viene associata all'ID dispositivo IoT nell'hub IoT. Questa chiave viene inclusa nelle applicazioni IoT, in modo che il dispositivo possa presentarla quando si connette all'hub IoT.

### <a name="create-the-device-identity"></a>Creare l'identità del dispositivo

Aggiungere un nuovo dispositivo IoT nell'hub IoT usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione IoT per Visual Studio Code. Tenere presente che i dispositivi downstream devono essere identificati nell'hub IoT come normali dispositivi IoT, non come dispositivi IoT Edge.

Quando si crea la nuova identità del dispositivo, fornire le informazioni seguenti:

* Creare un ID per il dispositivo.

* Selezionare **Chiave simmetrica** come tipo di autenticazione.

* Facoltativamente, scegliere **Imposta un dispositivo padre** e selezionare il dispositivo gateway IoT Edge tramite cui si connetterà il dispositivo downstream. Questo passaggio è facoltativo per l'autenticazione tramite chiave simmetrica, ma è consigliato perché l'impostazione di un dispositivo padre abilita le [funzionalità offline](offline-capabilities.md) per il dispositivo downstream. È sempre possibile aggiornare i dati del dispositivo per aggiungere o modificare l'elemento padre in un secondo momento.

   ![Creare l'ID dispositivo con l'autenticazione tramite chiave simmetrica nel portale](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

È possibile usare l'[estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) per completare la stessa operazione. L'esempio seguente crea un nuovo dispositivo IoT con autenticazione tramite chiave simmetrica e assegna un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi e la gestione di dispositivi padre e figlio, vedere il contenuto di riferimento per i comandi [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest).


Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

## <a name="register-device-x509-self-signed"></a>Registrare il dispositivo (X.509 autofirmato)

Per l'opzione di autenticazione X.509 autofirmato, detta anche autenticazione tramite identificazione personale, è necessario creare nuovi certificati da inserire nel dispositivo IoT. Questi certificati hanno un'identificazione personale che viene condivisa con l'hub IoT per l'autenticazione.

Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile [creare certificati demo per testare le funzionalità del dispositivo IoT Edge](how-to-create-test-certificates.md). Quando si generano certificati di test per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Usando il certificato della CA, creare due certificati del dispositivo (primario e secondario) per il dispositivo downstream.

   Il certificato del dispositivo deve avere il nome soggetto impostato sull'ID dispositivo che si userà per la registrazione del dispositivo IoT nell'hub IoT di Azure. Questa impostazione è obbligatoria per l'autenticazione.

2. Recuperare l'impronta digitale SHA1 (detta identificazione personale nell'interfaccia dell'hub IoT) di ogni certificato, costituita da una stringa esadecimale di 40 caratteri. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Eseguire questo comando due volte, una volta per il certificato primario e una per il certificato secondario. Si forniscono le impronte digitali per entrambi i certificati quando si registra un nuovo dispositivo IoT usando certificati X.509 autofirmati.

3. Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con i valori seguenti:

   * Fornire l'**ID dispositivo** corrispondente al nome soggetto dei certificati del dispositivo.
   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiate dai certificati primario e secondario del dispositivo.
   * Selezionare **Imposta un dispositivo padre** e scegliere il dispositivo gateway IoT Edge tramite cui si connetterà il dispositivo downstream. Per l'autenticazione di un dispositivo downstream con l'opzione X.509 autofirmato è necessario un dispositivo padre.

   ![Creare l'ID dispositivo con l'opzione di autenticazione X.509 autofirmato nel portale](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati dei dispositivi downstream.

   Si farà riferimento a questi file nelle applicazioni dei dispositivi foglia che si connettono all'hub IoT. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come il [protocollo Secure Copy](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

5. A seconda del linguaggio preferito, vedere gli esempi di come fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C#: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare l'[estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) per completare la stessa operazione di creazione del dispositivo. L'esempio seguente crea un nuovo dispositivo IoT con l'opzione di autenticazione X.509 autofirmato e assegna un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi, la generazione di certificati e la gestione di dispositivi padre e figlio, vedere il contenuto di riferimento per i comandi [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest).

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

## <a name="register-device-x509-ca-signed"></a>Registrare il dispositivo (X.509 firmato dall'Autorità di certificazione)

Per l'opzione di autenticazione X.509 firmato dall'Autorità di certificazione, è necessario un certificato CA radice registrato nell'hub IoT che si usa per firmare i certificati per il dispositivo IoT. Qualsiasi dispositivo che usa un certificato che è stato rilasciato tramite il certificato CA radice o qualsiasi certificato intermedio sarà autorizzato a eseguire l'autenticazione.

Questa sezione si basa sulle istruzioni illustrate nell'articolo relativo all'hub IoT [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md). Eseguire la procedura descritta in questa sezione per informazioni sui valori da usare per configurare un dispositivo downstream che si connette tramite un gateway.

Se non si dispone di un'autorità di certificazione per creare certificati X.509, è possibile [creare certificati demo per testare le funzionalità del dispositivo IoT Edge](how-to-create-test-certificates.md). Quando si generano certificati di test per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Seguire le istruzioni riportate nella sezione [Registrare i certificati della CA X.509 nell'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) dell'articolo *Configurare la sicurezza X.509 nell'hub IoT di Azure*. In tale sezione si eseguono i passaggi seguenti:

   1. Aggiornare un certificato CA radice. Se si usano i certificati demo, la CA radice è **\<path>/certs/azure-iot-test-only.root.ca.cert.pem**.

   2. Verificare di possedere il certificato CA radice.

2. Seguire le istruzioni riportate nella sezione [Creare un dispositivo X.509 per l'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) dell'articolo *Configurare la sicurezza X.509 nell'hub IoT di Azure*. In tale sezione si eseguono i passaggi seguenti:

   1. Aggiungere un nuovo dispositivo. Specificare un nome in lettere minuscole per **ID dispositivo** e scegliere il tipo di autenticazione **X.509 firmato dall'Autorità di certificazione**.
   2. Impostare un dispositivo padre. Per i dispositivi downstream, selezionare **Imposta un dispositivo padre** e scegliere il dispositivo gateway IoT Edge che consentirà la connessione all'hub IoT.

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato nell'hub IoT per creare la catena. Usare lo stesso ID dispositivo in lettere minuscole assegnato all'identità del dispositivo nel portale.

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati dei dispositivi downstream.

   Si farà riferimento a questi file nelle applicazioni dei dispositivi foglia che si connettono all'hub IoT. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come il [protocollo Secure Copy](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

5. A seconda del linguaggio preferito, vedere gli esempi di come fare riferimento ai certificati X.509 nelle applicazioni IoT:

   * C#: [Configurare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare l'[estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) per completare la stessa operazione di creazione del dispositivo. L'esempio seguente crea un nuovo dispositivo IoT con l'opzione di autenticazione X.509 firmato dall'Autorità di certificazione e assegna un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Per altre informazioni, vedere il contenuto di riferimento dell'interfaccia della riga di comando di Azure per i comandi [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity?view=azure-cli-latest).

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo disponga delle informazioni per la connessione tramite il gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperare e modificare la stringa di connessione

Dopo aver creato un'identità del dispositivo IoT nel portale, è possibile recuperare le relative chiavi primaria e secondaria. Una di queste chiavi deve essere inclusa nella stringa di connessione usata dalle applicazioni per comunicare con l'hub IoT. Per l'autenticazione tramite chiave simmetrica, l'hub IoT fornisce la stringa di connessione completa nei dati del dispositivo, per praticità. È necessario aggiungere ulteriori informazioni sul dispositivo gateway nella stringa di connessione.

Per le stringhe di connessione per i dispositivi downstream sono necessari i componenti seguenti:

* Hub IoT a cui il dispositivo si connette: `Hostname={iothub name}.azure-devices.net`
* ID dispositivo registrato nell'hub: `DeviceID={device ID}`
* Chiave primaria o secondaria: `SharedAccessKey={key}`
* Dispositivo gateway attraverso cui il dispositivo si connette. Fornire il valore di **hostname** presente nel file config.yaml del dispositivo gateway IoT Edge: `GatewayHostName={gateway hostname}`

Nel complesso, una stringa di connessione completa ha un aspetto simile al seguente:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se è stata stabilita una relazione padre/figlio per questo dispositivo downstream, è possibile semplificare la stringa di connessione chiamando il gateway direttamente come host della connessione. Le relazioni padre/figlio sono necessarie per l'autenticazione X.509, ma facoltative per l'autenticazione tramite chiave simmetrica. Ad esempio:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

A questo punto, sarà disponibile un dispositivo IoT Edge registrato e configurato come gateway. Si disporrà anche di un dispositivo IoT downstream registrato e che punta al relativo dispositivo gateway. Il passaggio finale consiste nell'inserire i certificati nel dispositivo downstream in modo che possa connettersi in modo sicuro al gateway.

Continuare con l'articolo successivo della serie dedicata al gateway [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Completando questo articolo, si disporrà di un dispositivo IoT Edge funzionante come gateway trasparente e di un dispositivo downstream registrato in un hub IoT. Sarà quindi necessario configurare i dispositivi downstream per considerare attendibile il dispositivo gateway e connettersi in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

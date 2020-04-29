---
title: Autenticare i dispositivi downstream-Azure IoT Edge | Microsoft Docs
description: Come autenticare i dispositivi downstream o i dispositivi foglia nell'hub Internet e instradare la connessione tramite i dispositivi Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 999204cf2fc8ce18b42f873b9d34af4e6c08052b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80411504"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream con l'hub IoT di Azure

In uno scenario di gateway trasparente, i dispositivi downstream (talvolta denominati dispositivi foglia o dispositivi figlio) necessitano di identità nell'hub Internet come qualsiasi altro dispositivo. Questo articolo illustra le opzioni per l'autenticazione di un dispositivo downstream nell'hub Internet e quindi illustra come dichiarare la connessione del gateway.

Sono disponibili tre passaggi generali per configurare una connessione del gateway trasparente corretta. Questo articolo illustra il secondo passaggio:

1. Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata. Per altre informazioni, vedere [configurare un dispositivo IOT Edge per agire come gateway trasparente](how-to-create-transparent-gateway.md).
2. **Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub Internet e conoscere la comunicazione tramite il dispositivo gateway.**
3. Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

I dispositivi downstream possono eseguire l'autenticazione con l'hub Internet tramite uno dei tre metodi seguenti: chiavi simmetriche (talvolta denominate chiavi di accesso condivise), certificati autofirmati X. 509 o certificati firmati da autorità di certificazione X. 509. I passaggi di autenticazione sono simili ai passaggi usati per configurare qualsiasi dispositivo perimetrale con l'hub Internet, con piccole differenze per dichiarare la relazione del gateway.

I passaggi descritti in questo articolo illustrano il provisioning manuale dei dispositivi, non il provisioning automatico con il servizio Device provisioning in hub Azure. Il provisioning di dispositivi downstream con DPS non è supportato.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti in [configurare un dispositivo IOT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Se si usa l'autenticazione X. 509 per il dispositivo downstream, è necessario usare lo stesso script di generazione del certificato configurato nell'articolo gateway trasparente.

Questo articolo si riferisce al *nome host del gateway* in diversi punti. Il nome host del gateway viene dichiarato nel parametro **hostname** del file config. YAML sul dispositivo gateway IOT Edge. Viene indicato nella stringa di connessione del dispositivo downstream. Il nome host del gateway deve essere risolvibile in un indirizzo IP, usando DNS o una voce del file host.

## <a name="register-device-symmetric-key"></a>Registra dispositivo (chiave simmetrica)

L'autenticazione con chiave simmetrica, o l'autenticazione della chiave di accesso condivisa, è il modo più semplice per eseguire l'autenticazione con l'hub. Con l'autenticazione con chiave simmetrica, una chiave Base64 è associata all'ID del dispositivo Internet all'interno dell'hub. Questa chiave viene inclusa nelle applicazioni Internet, in modo che il dispositivo possa presentarlo quando si connette all'hub Internet.

### <a name="create-the-device-identity"></a>Creare l'identità del dispositivo

Aggiungere un nuovo dispositivo Internet all'interno dell'hub Internet, usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione Internet per Visual Studio Code. Tenere presente che i dispositivi downstream devono essere identificati nell'hub delle cose come dispositivi normali, non IoT Edge dispositivi.

Quando si crea la nuova identità del dispositivo, fornire le seguenti informazioni:

* Creare un ID per il dispositivo.

* Selezionare **chiave simmetrica** come tipo di autenticazione.

* Facoltativamente, scegliere di **impostare un dispositivo padre** e selezionare il IOT Edge dispositivo gateway a cui si connetterà il dispositivo downstream. Questo passaggio è facoltativo per l'autenticazione con chiave simmetrica, ma è consigliabile perché l'impostazione di un dispositivo padre consente le [funzionalità offline](offline-capabilities.md) per il dispositivo downstream. È sempre possibile aggiornare i dettagli del dispositivo per aggiungere o modificare l'elemento padre in un secondo momento.

   ![Creare l'ID dispositivo con l'autenticazione con chiave simmetrica nel portale](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Per completare la stessa operazione, è possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione con chiave simmetrica e viene assegnato un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Per ulteriori informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi e la gestione di elementi padre/figlio, vedere il contenuto di riferimento per i comandi [Device-Identity di AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) Internet


Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo conosca la connessione tramite il gateway.

## <a name="register-device-x509-self-signed"></a>Registrare il dispositivo (X. 509 autofirmato)

Per l'autenticazione autofirmata X. 509, a volte definita autenticazione con identificazione personale, è necessario creare nuovi certificati da collocare sul dispositivo. Questi certificati hanno un'identificazione personale che si condivide con l'hub Internet per l'autenticazione.

Se non si dispone di un'autorità di certificazione per creare certificati X. 509, è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md). Quando si generano certificati di test per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Usando il certificato della CA, creare due certificati del dispositivo (primario e secondario) per il dispositivo downstream.

   Il certificato del dispositivo deve avere il nome soggetto impostato sull'ID del dispositivo che si userà per la registrazione del dispositivo Internet delle cose nell'hub Azure. Questa impostazione è obbligatoria per l'autenticazione.

2. Recuperare l'impronta digitale SHA1 (denominata identificazione digitale nell'interfaccia dell'hub Internet) da ogni certificato, ovvero una stringa di caratteri esadecimale 40. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell/bash
   openssl x509 -in <primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Eseguire questo comando due volte, una volta per il certificato primario e una volta per il certificato secondario. Si forniscono le impronte digitali per entrambi i certificati quando si registra un nuovo dispositivo Internet con certificati X. 509 autofirmati.

3. Passare all'hub Internet delle cose nel portale di Azure e creare una nuova identità del dispositivo Internet con i valori seguenti:

   * Specificare l' **ID del dispositivo** che corrisponde al nome del soggetto dei certificati del dispositivo.
   * Selezionare **X. 509 autofirmato** come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiate dai certificati primari e secondari del dispositivo.
   * Selezionare **imposta un dispositivo padre** e scegliere il IOT Edge dispositivo gateway a cui si connetterà il dispositivo downstream. Per l'autenticazione X. 509 di un dispositivo downstream è necessario un dispositivo padre.

   ![Creare l'ID dispositivo con l'autenticazione autofirmata X. 509 nel portale](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati downstream.

   Si farà riferimento a questi file nelle applicazioni per dispositivi foglia che si connettono all'hub Internet. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

5. A seconda del linguaggio preferito, vedere esempi di come fare riferimento ai certificati X. 509 nelle applicazioni Internet delle cose:

   * C#: [configurare la sicurezza X. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure per completare la stessa operazione di creazione del dispositivo. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione autofirmata X. 509 e viene assegnato un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi, la generazione di certificati e la gestione di elementi padre e figlio, vedere il contenuto di riferimento per i comandi [AZ Internet Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo conosca la connessione tramite il gateway.

## <a name="register-device-x509-ca-signed"></a>Registra dispositivo (firma CA X. 509)

Per l'autenticazione firmata (CA) dell'autorità di certificazione X. 509, è necessario un certificato CA radice registrato nell'hub delle cose da usare per firmare i certificati per il dispositivo. Qualsiasi dispositivo che usa un certificato che è stato rilasciato dal certificato CA radice o da qualsiasi certificato intermedio sarà autorizzato a eseguire l'autenticazione.

Questa sezione è basata sulle istruzioni descritte nell'articolo sull'hub Internet per la [configurazione della sicurezza X. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md). Eseguire la procedura descritta in questa sezione per informazioni sui valori da usare per configurare un dispositivo downstream che si connette tramite un gateway.

Se non si dispone di un'autorità di certificazione per creare certificati X. 509, è possibile [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md). Quando si generano certificati di test per il dispositivo downstream, usare lo stesso certificato CA radice che ha generato i certificati per il dispositivo gateway.

1. Seguire le istruzioni riportate nella sezione [registrare i certificati della CA x. 509](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) nella sezione dell'hub Internet per *configurare la sicurezza x. 509 nell'hub Azure*. In questa sezione si eseguono i passaggi seguenti:

   1. Caricare un certificato CA radice. Se si usano i certificati demo, la CA radice è ** \<Path>/certs/Azure-IOT-Test-only.root.ca.cert.pem**.

   2. Verificare di essere proprietario del certificato CA radice.

2. Seguire le istruzioni riportate nella sezione [creare un dispositivo x. 509 per l'hub Internet per](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *configurare la sicurezza x. 509 nell'hub Azure*. In questa sezione si eseguono i passaggi seguenti:

   1. Aggiungere un nuovo dispositivo. Specificare un nome in minuscolo per l' **ID dispositivo**e scegliere il tipo di autenticazione **firmata dall'autorità di certificazione X. 509**.
   2. Impostare un dispositivo padre. Per i dispositivi downstream selezionare **imposta un dispositivo padre** e scegliere il IOT Edge dispositivo gateway che fornirà la connessione all'hub.

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato nell'hub Internet per creare la catena. Usare lo stesso ID dispositivo minuscolo assegnato all'identità del dispositivo nel portale.

4. Copiare il certificato e le chiavi del dispositivo in qualsiasi posizione nel dispositivo downstream. Spostare inoltre una copia del certificato CA radice condiviso che ha generato sia il certificato del dispositivo gateway che i certificati downstream.

   Si farà riferimento a questi file nelle applicazioni per dispositivi foglia che si connettono all'hub Internet. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

5. A seconda del linguaggio preferito, vedere esempi di come fare riferimento ai certificati X. 509 nelle applicazioni Internet delle cose:

   * C#: [configurare la sicurezza X. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node. js: [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

È possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure per completare la stessa operazione di creazione del dispositivo. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione firmata dalla CA X. 509 e viene assegnato un dispositivo padre:

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Per altre informazioni, vedere il contenuto di riferimento dell'interfaccia della riga di comando di Azure per i comandi [AZ all Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) .

Successivamente, [recuperare e modificare la stringa di connessione](#retrieve-and-modify-connection-string) in modo che il dispositivo conosca la connessione tramite il gateway.

## <a name="retrieve-and-modify-connection-string"></a>Recuperare e modificare la stringa di connessione

Dopo aver creato un'identità del dispositivo Internet nel portale, è possibile recuperare le relative chiavi primarie o secondarie. Una di queste chiavi deve essere inclusa nella stringa di connessione usata dalle applicazioni per comunicare con l'hub Internet. Per l'autenticazione con chiave simmetrica, l'hub Internet fornisce la stringa di connessione completamente formattata nei dettagli del dispositivo per praticità. È necessario aggiungere informazioni aggiuntive sul dispositivo gateway alla stringa di connessione.

Per le stringhe di connessione per i dispositivi downstream sono necessari i componenti seguenti:

* Hub Internet delle cose a cui si connette il dispositivo:`Hostname={iothub name}.azure-devices.net`
* ID dispositivo registrato con l'hub:`DeviceID={device ID}`
* Chiave primaria o secondaria:`SharedAccessKey={key}`
* Il dispositivo gateway con cui si connette il dispositivo. Specificare il valore del **nome host** dal file config. YAML del dispositivo gateway IOT Edge:`GatewayHostName={gateway hostname}`

Insieme, una stringa di connessione completa ha un aspetto simile al seguente:

```
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se è stata stabilita una relazione padre/figlio per questo dispositivo downstream, è possibile semplificare la stringa di connessione chiamando il gateway direttamente come host della connessione. Le relazioni padre/figlio sono necessarie per l'autenticazione X. 509, ma facoltative per l'autenticazione con chiave simmetrica. Ad esempio:

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

A questo punto, è necessario avere un dispositivo IoT Edge registrato e configurato come gateway. Si dispone anche di un dispositivo di tutto il downstream registrato e che punta al dispositivo gateway. Il passaggio finale consiste nell'inserire i certificati sul dispositivo downstream in modo che sia possibile connettersi in modo sicuro al gateway.

Continuare con l'articolo successivo della serie gateway, [connettere un dispositivo downstream a un gateway Azure IOT Edge](how-to-connect-downstream-device.md).

## <a name="next-steps"></a>Passaggi successivi

Completando questo articolo, è necessario avere un dispositivo IoT Edge funzionante come gateway trasparente e un dispositivo downstream registrato con un hub Internet. Successivamente, è necessario configurare i dispositivi downstream per considerare attendibile il dispositivo gateway e connettersi in modo sicuro. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

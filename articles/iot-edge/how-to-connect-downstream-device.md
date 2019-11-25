---
title: 'Connettere i dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457132"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Connettere un dispositivo downstream a un gateway Azure IoT Edge

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

Questo articolo presenta i problemi comuni relativi alle connessioni ai dispositivi downstream e contiene le istruzioni per configurare i dispositivi downstream attraverso le informazioni seguenti: 

* Presentazione dei concetti di base relativi al protocollo Transport Layer Security (TLS) e ai certificati. 
* Descrizione del funzionamento delle librerie TLS su sistemi operativi diversi e di come ogni sistema operativo gestisce i certificati.
* Alcuni esempi di Azure IoT in diversi linguaggi per semplificare le attività iniziali. 

In questo articolo i termini *gateway* e *gateway IoT Edge* si riferiscono a un dispositivo IoT Edge configurato come gateway trasparente. 

## <a name="prerequisites"></a>Prerequisiti 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>Preparare un dispositivo downstream

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

Per connettere un dispositivo downstream a un gateway Azure IoT Edge, sono necessari due elementi:

* Un'applicazione o un dispositivo configurato con una stringa di connessione del dispositivo hub IoT cui siano aggiunte le informazioni necessarie per la connessione dell'applicazione o del dispositivo al gateway. 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>Nozioni fondamentali su TLS e sui certificati

La sfida di connettere in modo sicuro dispositivi downstream a IoT Edge viene gestita esattamente come per le altre comunicazioni client/server sicure che hanno luogo tramite internet. Un client e un server comunicano in modo sicuro tramite Internet usando il protocollo [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS viene integrato tramite costrutti di [infrastruttura a chiave pubblica (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) denominati certificati. TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

Quando un client si connette a un server, il server presenta una catena di certificati, denominata *catena di certificati server*. Una catena di certificati è in genere costituita da un certificato dell'autorità di certificazione (CA) radice, uno o più certificati della CA intermedia e infine il certificato del server stesso. Un client stabilisce relazioni di trust con un server verificando crittograficamente l'intera catena di certificati server. This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. Per convalidare una catena di certificati server, un client richiede una copia del certificato della CA radice che è stato usato per creare (o rilasciare) il certificato del server. Nel caso di connessione a siti Web, in genere un browser viene preconfigurato con certificati della CA di uso comune in modo da fornire al client un processo semplificato. 

Quando un dispositivo si connette all'hub IoT di Azure, il dispositivo è il client e il servizio cloud hub IoT è il server. Il servizio cloud hub IoT è supportato da un certificato della CA radice denominato **Baltimore CyberTrust Root**, disponibile a livello pubblico e ampiamente utilizzato. Poiché il certificato della CA dell'hub IoT è già installato nella maggior parte dei dispositivi, molte implementazioni di TLS (OpenSSL, Schannel, LibreSSL) lo usano automaticamente durante la convalida del certificato server. Un dispositivo in grado di connettersi correttamente all'hub IoT può avere problemi quando tenta di connettersi a un gateway IoT Edge.

Quando un dispositivo si connette al gateway IoT Edge, il dispositivo downstream è il client, mentre il dispositivo gateway è il server. Azure IoT Edge permette agli operatori (o agli utenti) di creare catene di certificati del gateway in base alle esigenze. L'operatore può scegliere di usare un certificato della CA pubblico, come Baltimore, o un certificato della CA radice autofirmato (o interno). I certificati della CA pubblici prevedono costi associati e di conseguenza vengono usati in genere in scenari di produzione. I certificati della CA autofirmati vengono scelti per scenari di sviluppo e test. The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

Quando si usa un certificato della CA radice autofirmato per un gateweay IoT Edge, questo deve essere installato su o fornito a tutti i dispositivi downstream che tentano di connettersi al gateway. 

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Per altre informazioni sui certificati di IoT Edge e su alcune implicazioni correlate alla produzione, vedere [Dettagli sull'utilizzo di certificati di IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

I comandi seguenti offrono un esempio di come installare un certificato della CA in un host Ubuntu. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

I passaggi seguenti offrono un esempio di come installare un certificato della CA in un host Windows. This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. Dal menu Start cercare e selezionare **Gestisci i certificati computer**. Verrà avviata un'utilità denominata **certlm**.
2. Passare a **Certificati - Computer locale** > **Autorità di certificazione radice attendibili**.
3. Fare clic con il pulsante destro del mouse su **Certificati** e scegliere **Tutte le attività** > **Importa**. Viene avviata l'Importazione guidata certificati. 
4. Seguire i passaggi indicati e importare il file del certificato `<path>/azure-iot-test-only.root.ca.cert.pem`. Al termine, verrà visualizzato il messaggio "Importazione completata". 

È anche possibile installare certificati a livello di codice tramite API .NET, come mostrato nell'esempio .NET più avanti in questo articolo. 

In genere le applicazioni usano lo stack TLS fornito da Windows denominato [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) per la connessione sicura tramite TLS. Schannel *richiede* che tutti i certificati siano installati nell'archivio certificati di Windows prima di tentare di stabilire una connessione TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usare i certificati con gli SDK di Azure IoT

Questa sezione descrive la connessione degli SDK di Azure IoT a un dispositivo IoT Edge tramite semplici applicazioni di esempio. L'obiettivo di tutti gli esempi consiste nel connettere il dispositivo client e inviare messaggi di telemetria al gateway, quindi chiudere la connessione e uscire. 

Prima di usare gli esempi a livello di applicazione, preparare due elementi:

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Percorso completo del certificato della CA radice copiato e salvato in una posizione nel dispositivo downstream.

    Ad esempio `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Questa sezione fornisce un'applicazione di esempio per la connessione di un client dispositivo NodeJS Azure IoT a un gateway IoT Edge. For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

1. Ottenere l'esempio per **edge_downstream_device.js** nel [repository degli esempi di Azure IoT SDK per dispositivi per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file edge_downstream_device.js aggiornare le variabili **connectionString** e **edge_ca_cert_path**. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 

Per informazioni sull'esempio in esecuzione, il frammento di codice seguente mostra in che modo il client SDK legge il file del certificato e lo usa per stabilire una connessione TLS sicura: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Questa sezione presenta un'applicazione di esempio per la connessione a un client dispositivo .NET Azure IoT a un gateway IoT Edge. Tuttavia, le applicazioni .NET sono automaticamente in grado di usare i certificati installati nell'archivio certificati del sistema in host sia Linux sia Windows.

1. Ottenere l'esempio per **EdgeDownstreamDevice** nella [cartella degli esempi di IoT Edge per .NET](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file **Properties/launchSettings.json**aggiornare le variabili **DEVICE_CONNECTION_STRING** e **CA_CERTIFICATE_PATH**. Se si vuole usare il certificato installato nell'archivio certificati attendibili nel sistema host, lasciare vuota questa variabile. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 

Per installare a livello di codice un certificato attendibile nell'archivio certificati tramite un'applicazione .NET, fare riferimento alla funzione **InstallCACert()** nel file **EdgeDownstreamDevice/Program.cs**. Poiché questa operazione è idempotente, può essere eseguita più volte con gli stessi valori senza produrre altri effetti. 

### <a name="c"></a>C

Questa sezione presenta un'applicazione di esempio per la connessione a un client dispositivo C Azure IoT a un gateway IoT Edge. L'SDK per C supporta molte librerie TLS, tra cui OpenSSL WolfSSL e Schannel. Per altre informazioni, vedere [Azure IoT SDK per C](https://github.com/Azure/azure-iot-sdk-c). 

1. Ottenere l'applicazione **iotedge_downstream_device_sample** nel [repository degli esempi di Azure IoT SDK per dispositivi per C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file iotedge_downstream_device_sample.c aggiornare le variabili **connectionString** e **edge_ca_cert_path**. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 

Azure IoT SDK per dispositivi per C fornisce un'opzione per registrare un certificato della CA durante la configurazione del client. Questa operazione non installa il certificato ovunque, ma usa invece un formato di stringa del certificato in memoria. Il certificato salvato viene fornito allo stack TLS sottostante quando si stabilisce una connessione. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

Se negli host Windows non si usa OpenSSL o un'altra libreria TLS, per impostazione predefinita l'SDK usa Schannel. Per il corretto funzionamento di Schannel, il certificato della CA radice di IoT Edge deve essere installato nell'archivio certificati di Windows e non deve essere impostato tramite l'operazione `IoTHubDeviceClient_SetOption`. 

### <a name="java"></a>Java

Questa sezione presenta un'applicazione di esempio per la connessione a un client dispositivo Java Azure IoT a un gateway IoT Edge. 

1. Ottenere l'esempio per **Send-event** nel [repository degli esempi di Azure IoT SDK per dispositivi per Java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo.

### <a name="python"></a>Python

Questa sezione presenta un'applicazione di esempio per la connessione di un cliente dispositivo Python Azure IoT a un gateway IoT Edge. 

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 


## <a name="test-the-gateway-connection"></a>Testare la connessione al gateway

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'estensione delle [funzionalità offline](offline-capabilities.md) ai dispositivi downstream in IoT Edge. 

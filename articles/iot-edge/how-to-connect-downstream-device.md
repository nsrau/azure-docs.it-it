---
title: 'Connettere i dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: Come configurare i dispositivi downstream o foglia per connettersi ai dispositivi del gateway Edge di Azure IoT.How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3113f01341d2a1ec6160cfea3eb9d12d18b8495c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687175"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Connettere un dispositivo downstream a un gateway Azure IoT Edge

In questo articolo vengono fornite istruzioni per stabilire una connessione attendibile tra i dispositivi downstream e i gateway trasparenti IoT Edge.This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo downstream potrebbe anche essere un'applicazione in esecuzione sul dispositivo gateway IoT Edge stesso.

Esistono tre passaggi generali per configurare una connessione gateway trasparente di successo. In questo articolo viene illustrato il terzo passaggio:This article covers the third step:

1. Il dispositivo gateway deve connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e instradare i messaggi alla destinazione appropriata. Per ulteriori informazioni, consultate [Configurare un dispositivo Edge IoT in modo che funga da gateway trasparente.](how-to-create-transparent-gateway.md)
2. Il dispositivo downstream necessita di un'identità del dispositivo per poter eseguire l'autenticazione con l'hub IoT e sapere di comunicare tramite il dispositivo gateway. Per altre informazioni, vedere Autenticare un dispositivo downstream nell'hub IoT di Azure.For more information, see [Authenticate a downstream device to Azure IoT Hub.](how-to-authenticate-downstream-device.md)
3. **Il dispositivo downstream deve connettersi al dispositivo gateway in modo sicuro.**

Questo articolo presenta i problemi comuni relativi alle connessioni ai dispositivi downstream e contiene le istruzioni per configurare i dispositivi downstream attraverso le informazioni seguenti:

* Presentazione dei concetti di base relativi al protocollo Transport Layer Security (TLS) e ai certificati.
* Descrizione del funzionamento delle librerie TLS su sistemi operativi diversi e di come ogni sistema operativo gestisce i certificati.
* Alcuni esempi di Azure IoT in diversi linguaggi per semplificare le attività iniziali.

In questo articolo i termini *gateway* e *gateway IoT Edge* si riferiscono a un dispositivo IoT Edge configurato come gateway trasparente.

## <a name="prerequisites"></a>Prerequisiti

* Disporre del file di certificato **azure-iot-test-only.root.ca.cert.pem** generato in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Il dispositivo downstream usa questo certificato per convalidare l'identità del dispositivo gateway.
* Disporre della stringa di connessione modificata che punta al dispositivo gateway, come illustrato in [Autenticare un dispositivo downstream nell'hub IoT](how-to-authenticate-downstream-device.md)di Azure.

## <a name="prepare-a-downstream-device"></a>Preparare un dispositivo downstream

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo downstream potrebbe anche essere un'applicazione in esecuzione sul dispositivo gateway IoT Edge stesso. Tuttavia, un altro dispositivo IoT Edge non può essere a valle di un gateway IoT Edge.However, another IoT Edge device cannot be downstream of an IoT Edge gateway.

>[!NOTE]
>I dispositivi IoT con identità registrate nell'hub IoT possono usare [i moduli gemelli](../iot-hub/iot-hub-devguide-module-twins.md) per isolare processi, hardware o funzioni diversi in un singolo dispositivo. I gateway IoT Edge supportano le connessioni ai moduli downstream utilizzando l'autenticazione a chiave simmetrica ma non l'autenticazione con certificato X.509.IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication.

Per connettere un dispositivo downstream a un gateway Azure IoT Edge, sono necessari due elementi:

* Un'applicazione o un dispositivo configurato con una stringa di connessione del dispositivo hub IoT cui siano aggiunte le informazioni necessarie per la connessione dell'applicazione o del dispositivo al gateway.

    Questo passaggio è illustrato in [Autenticare un dispositivo downstream nell'hub IoT](how-to-authenticate-downstream-device.md)di Azure.This step is explained in Authenticate a downstream device to Azure IoT Hub .

* Il dispositivo o l'applicazione deve considerare attendibile il certificato **della CA radice** del gateway per convalidare le connessioni TLS al dispositivo gateway.

    Questo passaggio è illustrato in dettaglio nella parte restante di questo articolo. Questo passaggio può essere eseguito in due modi: installando il certificato CA nell'archivio certificati del sistema operativo o (per determinate lingue) facendo riferimento al certificato all'interno delle applicazioni usando gli SDK di Azure IoT.This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>Nozioni fondamentali su TLS e sui certificati

La sfida di connettere in modo sicuro dispositivi downstream a IoT Edge viene gestita esattamente come per le altre comunicazioni client/server sicure che hanno luogo tramite internet. Un client e un server comunicano in modo sicuro tramite Internet usando il protocollo [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS viene integrato tramite costrutti di [infrastruttura a chiave pubblica (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) denominati certificati. TLS è una specifica abbastanza complessa e affronta un'ampia gamma di argomenti relativi alla protezione di due endpoint. In questa sezione vengono riepilogati i concetti rilevanti per connettere in modo sicuro i dispositivi a un gateway Edge IoT.

Quando un client si connette a un server, il server presenta una catena di certificati, denominata *catena di certificati server*. Una catena di certificati è in genere costituita da un certificato dell'autorità di certificazione (CA) radice, uno o più certificati della CA intermedia e infine il certificato del server stesso. Un client stabilisce relazioni di trust con un server verificando crittograficamente l'intera catena di certificati server. Questa convalida client della catena di certificati server è *denominata convalida della catena di server*. Il client sfida crittograficamente il servizio a dimostrare il possesso della chiave privata associata al certificato del server in un processo denominato *prova di possesso*. La combinazione di convalida della catena di server e prova di possesso è chiamata *autenticazione del server*. Per convalidare una catena di certificati server, un client richiede una copia del certificato della CA radice che è stato usato per creare (o rilasciare) il certificato del server. Nel caso di connessione a siti Web, in genere un browser viene preconfigurato con certificati della CA di uso comune in modo da fornire al client un processo semplificato.

Quando un dispositivo si connette all'hub IoT di Azure, il dispositivo è il client e il servizio cloud hub IoT è il server. Il servizio cloud hub IoT è supportato da un certificato della CA radice denominato **Baltimore CyberTrust Root**, disponibile a livello pubblico e ampiamente utilizzato. Poiché il certificato della CA dell'hub IoT è già installato nella maggior parte dei dispositivi, molte implementazioni di TLS (OpenSSL, Schannel, LibreSSL) lo usano automaticamente durante la convalida del certificato server. Un dispositivo in grado di connettersi correttamente all'hub IoT può avere problemi quando tenta di connettersi a un gateway IoT Edge.

Quando un dispositivo si connette al gateway IoT Edge, il dispositivo downstream è il client, mentre il dispositivo gateway è il server. Azure IoT Edge permette agli operatori (o agli utenti) di creare catene di certificati del gateway in base alle esigenze. L'operatore può scegliere di usare un certificato della CA pubblico, come Baltimore, o un certificato della CA radice autofirmato (o interno). I certificati della CA pubblici prevedono costi associati e di conseguenza vengono usati in genere in scenari di produzione. I certificati della CA autofirmati vengono scelti per scenari di sviluppo e test. Gli articoli di configurazione del gateway trasparente elencati nell'introduzione usano certificati CA radice autofirmati.

Quando si usa un certificato della CA radice autofirmato per un gateweay IoT Edge, questo deve essere installato su o fornito a tutti i dispositivi downstream che tentano di connettersi al gateway.

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Per altre informazioni sui certificati di IoT Edge e su alcune implicazioni correlate alla produzione, vedere [Dettagli sull'utilizzo di certificati di IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornire il certificato della CA radice

Per verificare i certificati del dispositivo gateway, il dispositivo downstream necessita di una propria copia del certificato della CA radice. Se sono stati utilizzati gli script forniti nel repository Git di IoT Edge per creare certificati di prova, il certificato della CA radice è denominato **azure-iot-test-only.root.ca.cert.pem**. Se non hai già fatto parte degli altri passaggi di preparazione del dispositivo downstream, sposta questo file del certificato in qualsiasi directory sul dispositivo downstream. È possibile usare un servizio come [L'insieme](https://docs.microsoft.com/azure/key-vault) di credenziali delle chiavi di Azure o una funzione come Protocollo di copia [sicura](https://www.ssh.com/ssh/scp/) per spostare il file del certificato.

## <a name="install-certificates-in-the-os"></a>Installare i certificati nel sistema operativo

L'installazione del certificato della CA radice nell'archivio certificati del sistema operativo consente in genere alla maggior parte delle applicazioni di utilizzare il certificato della CA radice. Esistono alcune eccezioni, ad esempio le applicazioni NodeJS che non utilizzano l'archivio certificati del sistema operativo, ma preferiscono l'archivio certificati interno del runtime del nodo. Se non è possibile installare il certificato a livello di sistema operativo, passare a [Usare i certificati con SDK di Azure IoT.](#use-certificates-with-azure-iot-sdks)

### <a name="ubuntu"></a>Ubuntu

I comandi seguenti offrono un esempio di come installare un certificato della CA in un host Ubuntu. Questo esempio presuppone che si stia usando il certificato **azure-iot-test-only.root.ca.cert.pem** dagli articoli sui prerequisiti e che il certificato sia stato copiato in un percorso nel dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Verrà visualizzato il messaggio "Aggiornamento dei certificati in /etc/ssl/certs... 1 aggiunto, 0 rimosso; fatto.

### <a name="windows"></a>WINDOWS

I passaggi seguenti offrono un esempio di come installare un certificato della CA in un host Windows. Questo esempio presuppone che si stia usando il certificato **azure-iot-test-only.root.ca.cert.pem** dagli articoli sui prerequisiti e che il certificato sia stato copiato in un percorso nel dispositivo downstream.

È possibile installare i certificati usando Import-Certificate di PowerShell come amministratore:You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

È inoltre possibile installare i certificati utilizzando l'utilità **certlm:**

1. Dal menu Start cercare e selezionare **Gestisci i certificati computer**. Verrà avviata un'utilità denominata **certlm**.
2. Passare a Certificati -**Autorità di certificazione radice del**computer >  **locale**attendibili .
3. Fare clic con il pulsante destro del mouse su **Certificati** e scegliere **Tutte le attività** > **Importa**. Viene avviata l'Importazione guidata certificati.
4. Seguire i passaggi indicati e importare il file del certificato `<path>/azure-iot-test-only.root.ca.cert.pem`. Al termine, verrà visualizzato il messaggio "Importazione completata".

È anche possibile installare certificati a livello di codice tramite API .NET, come mostrato nell'esempio .NET più avanti in questo articolo.

In genere le applicazioni usano lo stack TLS fornito da Windows denominato [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) per la connessione sicura tramite TLS. Schannel *richiede* che tutti i certificati siano installati nell'archivio certificati di Windows prima di tentare di stabilire una connessione TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usare i certificati con gli SDK di Azure IoT

Questa sezione descrive la connessione degli SDK di Azure IoT a un dispositivo IoT Edge tramite semplici applicazioni di esempio. L'obiettivo di tutti gli esempi consiste nel connettere il dispositivo client e inviare messaggi di telemetria al gateway, quindi chiudere la connessione e uscire.

Prima di usare gli esempi a livello di applicazione, preparare due elementi:

* La stringa di connessione dell'hub IoT del dispositivo downstream è stata modificata in modo da puntare al dispositivo gateway e tutti i certificati necessari per autenticare il dispositivo downstream nell'hub IoT. Per altre informazioni, vedere Autenticare un dispositivo downstream nell'hub IoT di Azure.For more information, see [Authenticate a downstream device to Azure IoT Hub.](how-to-authenticate-downstream-device.md)

* Percorso completo del certificato della CA radice copiato e salvato in una posizione nel dispositivo downstream.

    Ad esempio: `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>NodeJS

Questa sezione fornisce un'applicazione di esempio per la connessione di un client dispositivo NodeJS Azure IoT a un gateway IoT Edge. Per le applicazioni NodeJS, è necessario installare il certificato ca radice a livello di applicazione, come illustrato di seguito. Le applicazioni NodeJS non utilizzano l'archivio certificati del sistema.

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

1. Ottenere l'esempio per **send_message** da [Azure IoT device SDK for Python esempi.](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios)
2. Assicurarsi di essere in esecuzione in un contenitore IoT `EdgeHubConnectionString` Edge `EdgeModuleCACertificateFile` o in uno scenario di debug, impostare le variabili di ambiente e .
3. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo.

## <a name="test-the-gateway-connection"></a>Testare la connessione al gateway

Usare questo comando di esempio per verificare che il dispositivo downstream possa connettersi al dispositivo gateway:Use this sample command to test that your downstream device can connect to the gateway device:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Questo comando verifica le connessioni tramite MQTTS (porta 8883). Se si utilizza un protocollo diverso, regolare il comando in base alle esigenze per AMQPS (5671) o HTTPS (433)

L'output di questo comando può essere lungo, incluse le informazioni su tutti i certificati nella catena. Se la connessione ha esito positivo, `Verification: OK` `Verify return code: 0 (ok)`verrà visualizzata una riga come o .

![Verificare la connessione al gatewayVerify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Risolvere i problemi relativi alla connessione gatewayTroubleshoot the gateway connection

Se il dispositivo foglia dispone di una connessione intermittente al dispositivo gateway, provare a eseguire la procedura seguente per la risoluzione.

1. Il nome host del gateway nella stringa di connessione corrisponde al valore del nome host nel file ioT Edge config.yaml nel dispositivo gateway?
2. Il nome host del gateway è reso riutilizzabile a un indirizzo IP? È possibile risolvere le connessioni intermittenti utilizzando DNS o aggiungendo una voce di file host sul dispositivo foglia.
3. Le porte di comunicazione sono aperte nel firewall? La comunicazione basata sul protocollo utilizzato (MQTTS:8883/AMQPS:5671/HTTPS:433) deve essere possibile tra il dispositivo downstream e l'Edge IoT trasparente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'estensione delle [funzionalità offline](offline-capabilities.md) ai dispositivi downstream in IoT Edge.

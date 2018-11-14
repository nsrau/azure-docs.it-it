---
title: Configurare dispositivi downstream con Azure IoT Edge | Microsoft Docs
description: Come configurare dispositivi downstream o foglia per la connessione tramite dispositivi gateway Azure IoT Edge.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b9993650492574fc45b7f15fa3424060079f5fe
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50915072"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Connettere un dispositivo downstream a un gateway Azure IoT Edge

Azure IoT Edge permette scenari basati su gateway trasparente, in cui uno o più dispositivi possono passare i propri messaggi attraverso un unico dispositivo gateway che gestisce la connessione all'hub IoT. Dopo aver configurato il dispositivo gateway, è necessario identificare come connettersi in modo sicuro ai dispositivi downstream. 

Questo articolo presenta i problemi comuni relativi alle connessioni ai dispositivi downstream e contiene le istruzioni per configurare i dispositivi downstream attraverso le informazioni seguenti: 

* Presentazione dei concetti di base relativi al protocollo Transport Layer Security (TLS) e ai certificati. 
* Descrizione del funzionamento delle librerie TLS su sistemi operativi diversi e di come ogni sistema operativo gestisce i certificati.
* Alcuni esempi di Azure IoT in diversi linguaggi per semplificare le attività iniziali. 

In questo articolo i termini *gateway* e *gateway IoT Edge* si riferiscono a un dispositivo IoT Edge configurato come gateway trasparente. 

## <a name="prerequisites"></a>Prerequisiti

Prima di seguire le istruzioni contenute in questo articolo, è necessario preparare due dispositivi per l'uso:

1. Un dispositivo IoT Edge configurato come gateway trasparente. 
    [Configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)

    Dopo aver configurato il dispositivo gateway, copiare il certificato **azure-iot-test-only.root.ca.cert.pem**dal gateway e renderlo disponibile ovunque nel dispositivo downstream. 

2. Un dispositivo downstream con un'identità del dispositivo proveniente dall'hub IoT. 
    Non è possibile usare un dispositivo IoT Edge come dispositivo downstream. Usare invece un dispositivo registrato come dispositivo IoT normale nell'hub IoT. Nel portale è possibile registrare un nuovo dispositivo nella sezione **Dispositivi IoT**. In alternativa, è possibile usare l'interfaccia della riga di comando di Azure per [registrare un dispositivo](../iot-hub/quickstart-send-telemetry-c.md#register-a-device). Copiare la stringa di connessione e renderla disponibile per l'uso nelle sezioni successive. 

    Attualmente solo i dispositivi downstream con autenticazione con chiave simmetrica possono connettersi tramite gateway IoT Edge. Le autorità di certificazione X.509 e i certificati autofirmati X.509 non sono attualmente supportati. 

## <a name="prepare-a-downstream-device"></a>Preparare un dispositivo downstream

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Per scopi pratici, un dispositivo downstream può anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. 

Per connettere un dispositivo downstream a un gateway Azure IoT Edge, sono necessari due elementi:

1. Un'applicazione o un dispositivo configurato con una stringa di connessione del dispositivo hub IoT cui siano aggiunte le informazioni necessarie per la connessione dell'applicazione o del dispositivo al gateway. 

    Il formato della stringa di connessione è: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Aggiungere la proprietà **GatewayHostName** con il nome host del dispositivo gateway alla fine della stringa di connessione. Il valore di **GatewayHostName** deve corrispondere al valore di **hostname** nel file config.yaml del dispositivo gateway. 

    La stringa finale sarà simile a `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Il dispositivo o l'applicazione deve ritenere attendibile il certificato della **CA radice** o della **CA proprietaria** per convalidare le connessioni TLS al dispositivo gateway. 

    Questo passaggio più complesso viene descritto in dettaglio nella parte restante di questo articolo. Questo passaggio può essere eseguito in due modi: installando il certificato della CA nell'archivio certificati del sistema operativo o (per alcuni linguaggi) facendo riferimento al certificato all'interno delle applicazioni tramite gli SDK di Azure IoT.

## <a name="tls-and-certificate-fundamentals"></a>Nozioni fondamentali su TLS e sui certificati

La sfida di connettere in modo sicuro dispositivi downstream a IoT Edge viene gestita esattamente come per le altre comunicazioni client/server sicure che hanno luogo tramite internet. Un client e un server comunicano in modo sicuro tramite Internet usando il protocollo [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS viene integrato tramite costrutti di [infrastruttura a chiave pubblica (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) denominati certificati. TLS è una specifica piuttosto complessa che permette di gestire un'ampia gamma di argomenti correlati alla protezione di due endpoint, ma la sezione seguente descrive i requisiti per connettere in modo sicuro i dispositivi a un gateway IoT Edge.

Quando un client si connette a un server, il server presenta una catena di certificati, denominata *catena di certificati server*. Una catena di certificati è in genere costituita da un certificato dell'autorità di certificazione (CA) radice, uno o più certificati della CA intermedia e infine il certificato del server stesso. Un client stabilisce relazioni di trust con un server verificando crittograficamente l'intera catena di certificati server. Questa convalida della catena di certificati server da parte del client è denominata *autenticazione server*. Per convalidare una catena di certificati server, un client richiede una copia del certificato della CA radice che è stato usato per creare (o rilasciare) il certificato del server. Nel caso di connessione a siti Web, in genere un browser viene preconfigurato con certificati della CA di uso comune in modo da fornire al client un processo semplificato. 

Quando un dispositivo si connette all'hub IoT di Azure, il dispositivo è il client e il servizio cloud hub IoT è il server. Il servizio cloud hub IoT è supportato da un certificato della CA radice denominato **Baltimore CyberTrust Root**, disponibile a livello pubblico e ampiamente utilizzato. Poiché il certificato della CA dell'hub IoT è già installato nella maggior parte dei dispositivi, molte implementazioni di TLS (OpenSSL, Schannel, LibreSSL) lo usano automaticamente durante la convalida del certificato server. Un dispositivo in grado di connettersi correttamente all'hub IoT può avere problemi quando tenta di connettersi a un gateway IoT Edge.

Quando un dispositivo si connette al gateway IoT Edge, il dispositivo downstream è il client, mentre il dispositivo gateway è il server. Azure IoT Edge permette agli operatori (o agli utenti) di creare catene di certificati del gateway in base alle esigenze. L'operatore può scegliere di usare un certificato della CA pubblico, come Baltimore, o un certificato della CA radice autofirmato (o interno). I certificati della CA pubblici prevedono costi associati e di conseguenza vengono usati in genere in scenari di produzione. I certificati della CA autofirmati vengono scelti per scenari di sviluppo e test. Gli articoli sulla configurazione del gateway trasparente elencati nella sezione relativa ai prerequisiti usano certificati della CA radice autofirmati. 

Quando si usa un certificato della CA radice autofirmato per un gateweay IoT Edge, questo deve essere installato su o fornito a tutti i dispositivi downstream che tentano di connettersi al gateway. 

Per altre informazioni sui certificati di IoT Edge e su alcune implicazioni correlate alla produzione, vedere [Dettagli sull'utilizzo di certificati di IoT Edge](iot-edge-certs.md).

## <a name="install-certificates-using-the-os"></a>Installare i certificati tramite il sistema operativo

Questo articolo usa il termine *CA proprietaria* per fare riferimento al certificato della CA radice, perché questo è il termine usato dagli script nell'articolo relativo ai prerequisiti per il gateway. 

L'installazione del certificato della CA proprietaria nell'archivio certificati del sistema operativo permette in genere alla maggior parte delle applicazioni di usare il certificato della CA proprietaria. Esistono alcune eccezioni, ad esempio per le applicazioni NodeJS che non usano l'archivio certificati del sistema operativo, ma l'archivio certificati interno del runtime Node. Se non è possibile installare il certificato a livello di sistema operativo, fare riferimento agli esempi specifici di ogni linguaggio più avanti in questo articolo per usare il certificato con Azure IoT SDK nelle applicazioni. 

### <a name="ubuntu"></a>Ubuntu

I comandi seguenti offrono un esempio di come installare un certificato della CA in un host Ubuntu. Questo esempio presuppone l'uso del certificato **azure-iot-test-only.root.ca.cert.pem** indicato negli articoli relativi ai prerequisiti e che il certificato sia stato copiato in una posizione nel dispositivo downstream.  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Verrà visualizzato un messaggio che indica che è in corso l'aggiornamento in /etc/ssl/certs. (1 aggiunto, 0 rimossi).

### <a name="windows"></a>Windows

I passaggi seguenti offrono un esempio di come installare un certificato della CA in un host Windows. Questo esempio presuppone l'uso del certificato **azure-iot-test-only.root.ca.cert.pem** indicato negli articoli relativi ai prerequisiti e che il certificato sia stato copiato in una posizione nel dispositivo downstream.  

1. Dal menu Start cercare e selezionare **Gestisci i certificati computer**. Verrà avviata un'utilità denominata **certlm**.
2. Passare a **Certificati - Computer locale** > **Autorità di certificazione radice attendibili**.
3. Fare clic con il pulsante destro del mouse su **Certificati** e scegliere **Tutte le attività** > **Importa**. Viene avviata l'Importazione guidata certificati. 
4. Seguire i passaggi indicati e importare il file del certificato `<path>/azure-iot-test-only.root.ca.cert.pem`. Al termine, verrà visualizzato il messaggio "Importazione completata". 

È anche possibile installare certificati a livello di codice tramite API .NET, come mostrato nell'esempio .NET più avanti in questo articolo. 

In genere le applicazioni usano lo stack TLS fornito da Windows denominato [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) per la connessione sicura tramite TLS. Schannel *richiede* che tutti i certificati siano installati nell'archivio certificati di Windows prima di tentare di stabilire una connessione TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usare i certificati con gli SDK di Azure IoT

Questo articolo fa riferimento al certificato della CA radice usando il termine *CA proprietaria* perché questo è il termine usato dagli script che generano il certificato autofirmato negli articoli relativi ai prerequisiti. 

Questa sezione descrive la connessione degli SDK di Azure IoT a un dispositivo IoT Edge tramite semplici applicazioni di esempio. L'obiettivo di tutti gli esempi consiste nel connettere il dispositivo client e inviare messaggi di telemetria al gateway, quindi chiudere la connessione e uscire. 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>Concetti comuni a tutti gli SDK di Azure IoT

Prima di usare gli esempi a livello di applicazione, preparare due elementi:

1. Stringa di connessione dell'hub IoT del dispositivo downstream modificata in modo da puntare al dispositivo gateway.

    Il formato della stringa di connessione è: `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`. Aggiungere la proprietà **GatewayHostName** con il nome host del dispositivo gateway alla fine della stringa di connessione. Il valore di **GatewayHostName** deve corrispondere al valore di **hostname** nel file config.yaml del dispositivo gateway. 

    La stringa finale sarà simile a `HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`.

2. Percorso completo del certificato della CA radice copiato e salvato in una posizione nel dispositivo downstream.

    Ad esempio: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Questa sezione fornisce un'applicazione di esempio per la connessione di un client dispositivo NodeJS Azure IoT a un gateway IoT Edge. Per gli host Linux e Windows, è necessario installare il certificato della CA radice a livello di applicazione come mostrato qui, perché le applicazioni NodeJS non usano l'archivio certificati del sistema. 

1. Ottenere l'esempio per **edge_downstream_device.js** nel [repository degli esempi di Azure IoT SDK per dispositivi per Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file edge_downstream_device.js aggiornare le variabili **connectionString** e **edge_ca_cert_path**. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 

Per informazioni sull'esempio in esecuzione, il frammento di codice seguente mostra in che modo il client SDK legge il file del certificato e lo usa per stabilire una connessione TLS sicura: 

```nodejs
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

1. Ottenere l'esempio per **edge_downstream_client** nel [repository degli esempi di Azure IoT SDK per dispositivi per Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file edge_downstream_client.py aggiornare le variabili **CONNECTION_STRING** e **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 


## <a name="test-the-gateway-connection"></a>Testare la connessione al gateway

Di seguito è riportato un comando di esempio che consente di verificare che tutto sia stato configurato correttamente. Verrà visualizzato un messaggio di conferma della verifica.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'estensione delle [funzionalità offline](offline-capabilities.md) ai dispositivi downstream in IoT Edge. 
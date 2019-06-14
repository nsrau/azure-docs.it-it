---
title: 'Connettere i dispositivi downstream: Azure IoT Edge | Microsoft Docs'
description: Jak nakonfigurovat downstream o dispositivi foglia a cui connettersi i dispositivi gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058503"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Connettere un dispositivo downstream a un gateway Azure IoT Edge

Questo articolo fornisce istruzioni per stabilire una connessione attendibile tra i dispositivi downstream e i gateway trasparenti di IoT Edge. In uno scenario gateway trasparente, uno o più dispositivi possono passare i messaggi attraverso un dispositivo gateway singolo che gestisce la connessione all'IoT Hub. Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo downstream potrebbe anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. 

Esistono tre passaggi generali per configurare una connessione gateway trasparente ha esito positivo. Questo articolo illustra il terzo passaggio:

1. Il dispositivo gateway deve connettersi a dispositivi downstream in modo sicuro, ricevere le comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata. Per altre informazioni, vedere [configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).
2. Il dispositivo downstream richiede un'identità del dispositivo per poter eseguire l'autenticazione con l'IoT Hub e in grado di comunicare tramite il relativo dispositivo gateway. Per altre informazioni, vedere [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).
3. **Il dispositivo downstream deve essere in grado di connettere in modo sicuro il dispositivo gateway.**

Questo articolo presenta i problemi comuni relativi alle connessioni ai dispositivi downstream e contiene le istruzioni per configurare i dispositivi downstream attraverso le informazioni seguenti: 

* Presentazione dei concetti di base relativi al protocollo Transport Layer Security (TLS) e ai certificati. 
* Descrizione del funzionamento delle librerie TLS su sistemi operativi diversi e di come ogni sistema operativo gestisce i certificati.
* Alcuni esempi di Azure IoT in diversi linguaggi per semplificare le attività iniziali. 

In questo articolo i termini *gateway* e *gateway IoT Edge* si riferiscono a un dispositivo IoT Edge configurato come gateway trasparente. 

## <a name="prepare-a-downstream-device"></a>Preparare un dispositivo downstream

Un dispositivo downstream può essere qualsiasi applicazione o piattaforma la cui identità sia stata creata con il servizio cloud [hub IoT di Azure](https://docs.microsoft.com/azure/iot-hub). In molti casi, queste applicazioni usano [Azure IoT SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md). Un dispositivo downstream potrebbe anche essere un'applicazione in esecuzione nel dispositivo gateway IoT Edge stesso. 

Per connettere un dispositivo downstream a un gateway Azure IoT Edge, sono necessari due elementi:

* Un'applicazione o un dispositivo configurato con una stringa di connessione del dispositivo hub IoT cui siano aggiunte le informazioni necessarie per la connessione dell'applicazione o del dispositivo al gateway. 

    Questo passaggio è descritto nelle [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).

* Il dispositivo o l'applicazione deve considerare attendibile il gateway **CA radice** certificati per convalidare le connessioni TLS per il dispositivo gateway. 

    Questo passaggio è illustrato dettagliatamente nel resto di questo articolo. Questo passaggio può essere eseguita in due modi: installando il certificato della CA nell'archivio certificati del sistema operativo o (per alcuni linguaggi) facendo riferimento del certificato all'interno delle applicazioni usando gli SDK IoT di Azure.

## <a name="tls-and-certificate-fundamentals"></a>Nozioni fondamentali su TLS e sui certificati

La sfida di connettere in modo sicuro dispositivi downstream a IoT Edge viene gestita esattamente come per le altre comunicazioni client/server sicure che hanno luogo tramite internet. Un client e un server comunicano in modo sicuro tramite Internet usando il protocollo [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS viene integrato tramite costrutti di [infrastruttura a chiave pubblica (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) denominati certificati. TLS è una specifica influisce e risolve un'ampia gamma di argomenti correlati alla protezione di due endpoint. Questa sezione vengono riepilogati i concetti rilevanti per la connessione in modo sicuro i dispositivi a un gateway IoT Edge.

Quando un client si connette a un server, il server presenta una catena di certificati, denominata *catena di certificati server*. Una catena di certificati è in genere costituita da un certificato dell'autorità di certificazione (CA) radice, uno o più certificati della CA intermedia e infine il certificato del server stesso. Un client stabilisce relazioni di trust con un server verificando crittograficamente l'intera catena di certificati server. Questa convalida del client della catena di certificati server viene chiamata *convalida della catena di server*. Il client a livello di crittografia richiede il servizio per dimostrare che possiede la chiave privata associata al certificato server in un processo denominato *prova di possesso*. Viene chiamate la combinazione di convalida della catena di server e verifica del possesso *autenticazione server*. Per convalidare una catena di certificati server, un client richiede una copia del certificato della CA radice che è stato usato per creare (o rilasciare) il certificato del server. Nel caso di connessione a siti Web, in genere un browser viene preconfigurato con certificati della CA di uso comune in modo da fornire al client un processo semplificato. 

Quando un dispositivo si connette all'hub IoT di Azure, il dispositivo è il client e il servizio cloud hub IoT è il server. Il servizio cloud hub IoT è supportato da un certificato della CA radice denominato **Baltimore CyberTrust Root**, disponibile a livello pubblico e ampiamente utilizzato. Poiché il certificato della CA dell'hub IoT è già installato nella maggior parte dei dispositivi, molte implementazioni di TLS (OpenSSL, Schannel, LibreSSL) lo usano automaticamente durante la convalida del certificato server. Un dispositivo in grado di connettersi correttamente all'hub IoT può avere problemi quando tenta di connettersi a un gateway IoT Edge.

Quando un dispositivo si connette al gateway IoT Edge, il dispositivo downstream è il client, mentre il dispositivo gateway è il server. Azure IoT Edge permette agli operatori (o agli utenti) di creare catene di certificati del gateway in base alle esigenze. L'operatore può scegliere di usare un certificato della CA pubblico, come Baltimore, o un certificato della CA radice autofirmato (o interno). I certificati della CA pubblici prevedono costi associati e di conseguenza vengono usati in genere in scenari di produzione. I certificati della CA autofirmati vengono scelti per scenari di sviluppo e test. Gli articoli di programma di installazione di gateway trasparente elencati nella sezione introduttiva usano i certificati della CA radice autofirmato. 

Quando si usa un certificato della CA radice autofirmato per un gateweay IoT Edge, questo deve essere installato su o fornito a tutti i dispositivi downstream che tentano di connettersi al gateway. 

![Installazione del certificato del gateway](./media/how-to-create-transparent-gateway/gateway-setup.png)

Per altre informazioni sui certificati di IoT Edge e su alcune implicazioni correlate alla produzione, vedere [Dettagli sull'utilizzo di certificati di IoT Edge](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Fornire il certificato CA radice

Per verificare i certificati del dispositivo gateway, il dispositivo downstream deve essere la propria copia del certificato della CA radice. Se gli script forniti nel repository git di IoT Edge usato per creare i certificati di prova, quindi viene chiamato il certificato CA radice **azure-iot-test-only.root.ca.cert.pem**. Se non è disponibile come parte degli altri passaggi di preparazione dei dispositivi downstream, spostare questo file di certificato in qualsiasi directory nel dispositivo downstream. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione, ad esempio [protocollo copia sicura](https://www.ssh.com/ssh/scp/) per spostare il file del certificato.

## <a name="install-certificates-in-the-os"></a>Installare i certificati nel sistema operativo

Installare il certificato CA radice nell'archivio certificati del sistema operativo in genere consente la maggior parte delle applicazioni di utilizzare il certificato CA radice. Esistono alcune eccezioni, ad esempio NodeJS le applicazioni che non usano il certificato del sistema operativo archiviano ma piuttosto utilizzano l'archivio del certificato interno di nodo del runtime. Se è possibile installare il certificato a livello di sistema operativo, andare direttamente al [usare i certificati con Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

I comandi seguenti offrono un esempio di come installare un certificato della CA in un host Ubuntu. Questo esempio si presuppone che si usi il **azure-iot-test-only.root.ca.cert.pem** ficati gli articoli di prerequisiti e che è stato copiato il certificato in un percorso nel dispositivo downstream.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Verrà visualizzato un messaggio che indica che è in corso l'aggiornamento in /etc/ssl/certs. (1 aggiunto, 0 rimossi).

### <a name="windows"></a>Windows

I passaggi seguenti offrono un esempio di come installare un certificato della CA in un host Windows. Questo esempio si presuppone che si usi il **azure-iot-test-only.root.ca.cert.pem** ficati gli articoli di prerequisiti e che è stato copiato il certificato in un percorso nel dispositivo downstream.

1. Dal menu Start cercare e selezionare **Gestisci i certificati computer**. Verrà avviata un'utilità denominata **certlm**.
2. Passare a **Certificati - Computer locale** > **Autorità di certificazione radice attendibili**.
3. Fare clic con il pulsante destro del mouse su **Certificati** e scegliere **Tutte le attività** > **Importa**. Viene avviata l'Importazione guidata certificati. 
4. Seguire i passaggi indicati e importare il file del certificato `<path>/azure-iot-test-only.root.ca.cert.pem`. Al termine, verrà visualizzato il messaggio "Importazione completata". 

È anche possibile installare certificati a livello di codice tramite API .NET, come mostrato nell'esempio .NET più avanti in questo articolo. 

In genere le applicazioni usano lo stack TLS fornito da Windows denominato [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) per la connessione sicura tramite TLS. Schannel *richiede* che tutti i certificati siano installati nell'archivio certificati di Windows prima di tentare di stabilire una connessione TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Usare i certificati con gli SDK di Azure IoT

Questa sezione descrive la connessione degli SDK di Azure IoT a un dispositivo IoT Edge tramite semplici applicazioni di esempio. L'obiettivo di tutti gli esempi consiste nel connettere il dispositivo client e inviare messaggi di telemetria al gateway, quindi chiudere la connessione e uscire. 

Prima di usare gli esempi a livello di applicazione, preparare due elementi:

* Stringa di connessione dell'IoT Hub del dispositivo downstream modificata per puntare al dispositivo gateway ed eventuali certificati necessari per autenticare il dispositivo downstream all'IoT Hub. Per altre informazioni, vedere [autenticare un dispositivo all'IoT Hub di Azure a valle](how-to-authenticate-downstream-device.md).

* Percorso completo del certificato della CA radice copiato e salvato in una posizione nel dispositivo downstream.

    Ad esempio: `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Questa sezione fornisce un'applicazione di esempio per la connessione di un client dispositivo NodeJS Azure IoT a un gateway IoT Edge. Per le applicazioni NodeJS, è necessario installare il certificato CA radice a livello di applicazione, come illustrato di seguito. Applicazioni NodeJS non usano l'archivio certificati del sistema. 

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

1. Ottenere l'esempio per **edge_downstream_client** nel [repository degli esempi di Azure IoT SDK per dispositivi per Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Assicurarsi di avere predisposto tutti i prerequisiti per eseguire l'esempio esaminando il file **readme.md**. 
3. Nel file edge_downstream_client.py aggiornare le variabili **CONNECTION_STRING** e **TRUSTED_ROOT_CA_CERTIFICATE_PATH**. 
4. Vedere la documentazione dell'SDK per istruzioni su come eseguire l'esempio nel dispositivo. 


## <a name="test-the-gateway-connection"></a>Testare la connessione al gateway

Si tratta di un comando di esempio che consente di verificare che tutto ciò che è stato configurato correttamente. Verrà visualizzato un messaggio di conferma della verifica.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Risolvere i problemi di connessione del gateway

Se il dispositivo foglia contiene con connessione saltuaria alla relativo dispositivo gateway, provare le procedure seguenti per la risoluzione. 

1. Il nome host gateway nella stringa di connessione è identico al valore di nome host nel file config. yaml di IoT Edge nel dispositivo gateway?
2. È il nome host gateway può essere risolto in un indirizzo IP? È possibile risolvere connessioni intermittenti tramite DNS o tramite l'aggiunta di una voce del file host sul dispositivo foglia.
3. Vengono aperte nel firewall delle porte di comunicazione? Comunicazione basata sul protocollo usato (MQTTS:8883 / AMQPS:5671 / HTTPS:433) deve essere possibile tra dispositivi downstream e la trasparenza di IoT Edge.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull'estensione delle [funzionalità offline](offline-capabilities.md) ai dispositivi downstream in IoT Edge. 

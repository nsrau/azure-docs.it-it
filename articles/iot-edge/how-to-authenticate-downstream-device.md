---
title: L'autenticazione dei dispositivi downstream - Azure IoT Edge | Microsoft Docs
description: Come autenticare i dispositivi downstream o dispositivi foglia all'IoT Hub e indirizzare la connessione tramite i dispositivi gateway Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5785b0260474bd0eb861236a0bd78066475baacd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082391"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream all'IoT Hub di Azure

In uno scenario gateway trasparente, i dispositivi downstream, talvolta denominati dispositivi foglia o figlio, necessario le identità nell'IoT Hub, come qualsiasi altro dispositivo. Questo articolo illustra in modo dettagliato le opzioni per l'autenticazione di un dispositivo downstream all'IoT Hub e quindi viene illustrato come dichiarare la connessione del gateway.

Esistono tre passaggi generali per configurare una connessione gateway trasparente ha esito positivo. Questo articolo illustra il secondo passaggio:

1. Il dispositivo gateway deve essere in grado di connettersi ai dispositivi downstream in modo sicuro, ricevere le comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata. Per altre informazioni, vedere [configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md).
2. **Il dispositivo downstream deve avere un'identità del dispositivo sia in grado di eseguire l'autenticazione con l'IoT Hub e in grado di comunicare tramite il relativo dispositivo gateway.**
3. Il dispositivo downstream deve essere in grado di connettere in modo sicuro il dispositivo gateway. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

I dispositivi downstream possono autenticarsi con IoT Hub usando uno dei tre metodi: le chiavi simmetriche (talvolta dette le chiavi di accesso condiviso), i certificati X.509 autofirmati o certificati X.509 dell'autorità (CA) certificati autofirmati. I passaggi di autenticazione sono simili ai passaggi usati per configurare qualsiasi dispositivo IoT Edge con l'IoT Hub con piccole differenze per dichiarare la relazione di gateway.

La procedura in questo articolo illustra il provisioning dei dispositivi manuali, non automatic provisioning con il servizio di Provisioning di dispositivi di Azure IoT Hub. 

## <a name="symmetric-key-authentication"></a>Autenticazione con chiave simmetrica

L'autenticazione con chiave simmetrica o l'autenticazione chiave di accesso condiviso, è il modo più semplice per eseguire l'autenticazione con l'IoT Hub. Con l'autenticazione con chiave simmetrica, una chiave di base 64 è associata l'ID del dispositivo IoT nell'IoT Hub. Tale chiave è includere nelle applicazioni IoT in modo che il dispositivo di presentarle quando si connette all'IoT Hub. 

### <a name="create-the-device-identity"></a>Creare l'identità del dispositivo 

Aggiungere un nuovo dispositivo IoT nell'hub IoT, usando il portale di Azure, Azure CLI o l'estensione IoT per Visual Studio Code. Tenere presente che i dispositivi downstream devono essere identificati nell'IoT Hub come dispositivo di IoT normale, non su dispositivi IoT Edge. 

Quando si crea la nuova identità del dispositivo, fornire le informazioni seguenti: 

* Creare un ID per il dispositivo.

* Selezionare **chiave simmetrica** come tipo di autenticazione. 

* Facoltativamente, scegliere di **impostare un dispositivo padre** e selezionare il dispositivo gateway IoT Edge che questo dispositivo downstream si connetterà mediante. Questo passaggio è facoltativo per l'autenticazione con chiave simmetrica, ma è consigliabile in quanto impostazione di un dispositivo padre consente [le capacità offline](offline-capabilities.md) per il dispositivo downstream. È sempre possibile aggiornare i dettagli del dispositivo per aggiungere o modificare l'elemento padre in un secondo momento. 

   ![Creare l'ID del dispositivo con l'autenticazione chiave simmetrica nel portale](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

È possibile usare la [l'estensione della riga di comando di Azure IoT](https://github.com/Azure/azure-iot-cli-extension) per completare l'operazione stessa. Nell'esempio seguente crea un nuovo dispositivo IoT con l'autenticazione con chiave simmetrica e assegna un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Per altre informazioni sui comandi della riga di comando di Azure per creare un dispositivo e la gestione di relazione padre/figlio, vedere il contenuto di riferimento per [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandi.

### <a name="connect-to-iot-hub-through-a-gateway"></a>Connettersi all'IoT Hub tramite un gateway

Lo stesso processo viene utilizzato per autenticare IoT regolare i dispositivi all'IoT Hub con le chiavi simmetriche si applica anche ai dispositivi downstream. L'unica differenza è che è necessario aggiungere un puntatore per il dispositivo gateway per indirizzare la connessione o, negli scenari offline, per gestire l'autenticazione per conto dell'IoT Hub. 

Per l'autenticazione con chiave simmetrica, non è disponibile ulteriori operazioni che è necessario eseguire nel dispositivo per poter eseguire l'autenticazione con l'IoT Hub. È comunque necessario i certificati presenti in modo che il dispositivo downstream possa connettersi al relativo dispositivo gateway, come descritto in [connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

Dopo aver creato un'identità del dispositivo IoT nel portale, è possibile recuperare le chiavi primarie o secondarie. Una di queste chiavi deve essere incluso nella stringa di connessione da includere in qualsiasi applicazione che comunica con l'IoT Hub. Per l'autenticazione con chiave simmetrica, l'IoT Hub fornisce la stringa di connessione completamente formata nei dettagli del dispositivo per comodità. È necessario aggiungere informazioni aggiuntive relativi al dispositivo gateway per la stringa di connessione. 

Le stringhe di connessione della chiave simmetrica per i dispositivi downstream necessari i componenti seguenti: 

* L'hub IoT che si connette il dispositivo: `Hostname={iothub name}.azure-devices.net`
* L'ID del dispositivo registrato con l'hub: `DeviceID={device ID}`
* Una chiave primaria o secondaria: `SharedAccessKey={key}`
* Il dispositivo gateway che il dispositivo si connette tramite. Fornire il **hostname** valore dal file config. yaml del dispositivo gateway IoT Edge: `GatewayHostName={gateway hostname}`

Tutti gli elementi, una stringa di connessione completa simile:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se è stata stabilita una relazione padre/figlio per questo dispositivo downstream, è possibile semplificare la stringa di connessione chiamando il gateway direttamente come host di connessione. Ad esempio: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Autenticazione tramite certificato X.509 

Esistono due modi per autenticare un dispositivo IoT usando certificati X.509. Indipendentemente dal modo scelto per eseguire l'autenticazione, la procedura per connettere il dispositivo all'IoT Hub sono uguali. Scegliere i certificati autofirmati o con firma CA per l'autenticazione, quindi continuare a imparare a connettersi all'IoT Hub. 

Per altre informazioni su come l'IoT Hub Usa l'autenticazione X.509, vedere gli articoli seguenti: 
* [Autenticazione dei dispositivi con i certificati della CA X.509](../iot-hub/iot-hub-x509ca-overview.md)
* [Comprensione concettuale dei certificati della CA X.509 nel settore IoT](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Creare l'identità del dispositivo con i certificati X.509 autofirmati

Per l'autenticazione X.509 autofirmato, talvolta come l'autenticazione di identificazione personale, è necessario creare nuovi certificati da posizionare nel dispositivo IoT. Questi certificati hanno un'identificazione personale che si condivide con l'IoT Hub per l'autenticazione. 

Il modo più semplice per testare questo scenario è usare la stessa macchina usato per creare i certificati nel [configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Tale macchina dovrebbe già essere configurata con lo strumento giusto, certificato CA radice e un certificato CA intermedio per creare i certificati del dispositivo IoT. È possibile copiare i certificati finali e le chiavi private failover al dispositivo downstream in un secondo momento. Seguire i passaggi descritti nell'articolo di gateway, configurare openssl nel computer, sarà stato clonato il repository di IoT Edge per gli script di creazione di certificati di accesso. Quindi effettuate una directory di lavoro che chiamiamo  **\<WRKDIR >** per contenere i certificati. I certificati predefiniti sono pensati per lo sviluppo e test, pertanto solo degli ultimi 30 giorni. Si dovrebbe creare un certificato CA radice e un certificato intermedio. 

1. Passare alla directory di lavoro nella finestra di PowerShell o bash. 

2. Creare due certificati per il dispositivo downstream (primari e secondari). Fornire il nome del dispositivo, quindi l'etichetta primaria o secondaria. Queste informazioni viene utilizzate per denominare i file in modo che è possibile tenere traccia dei certificati per più dispositivi. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Recuperare l'impronta digitale SHA1 (denominata un'identificazione personale nell'interfaccia dell'IoT Hub) da ogni certificato, ovvero una stringa di 40 caratteri esadecimali. Usare il comando openssl seguenti per visualizzare il certificato e trovare l'ID digitale:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con i valori seguenti: 

   * Selezionare **X.509 autofirmato** come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiato dai certificati primario e secondario del dispositivo.
   * Selezionare **impostare un dispositivo padre** e scegliere il dispositivo gateway che il dispositivo downstream si connetterà tramite IoT Edge. Un dispositivo padre è necessario per l'autenticazione X.509 di un dispositivo downstream. 

   ![Creare l'ID dispositivo con l'autenticazione autofirmato X.509 nel portale](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. In qualsiasi directory nel dispositivo downstream, copiare i file seguenti:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Si farà riferimento a questi file nelle applicazioni dispositivo foglia che si connettono all'IoT Hub. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione, ad esempio [compilare copia sicura](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

È possibile usare la [l'estensione della riga di comando di Azure IoT](https://github.com/Azure/azure-iot-cli-extension) per completare l'operazione di creazione dispositivo stesso. Nell'esempio seguente crea un nuovo dispositivo IoT con l'autenticazione tramite certificato X.509 autofirmato e assegna un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Per altre informazioni sui comandi della riga di comando di Azure per la creazione di un dispositivo, la generazione del certificato e la gestione padre e figlio, vedere il contenuto di riferimento per [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandi.

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Creare il dispositivo identità con autorità di certificazione X.509 certificati autofirmati

Per un'autorità (CA) firmato autenticazione dei certificati X.509, è necessario un certificato CA radice registrato nell'IoT Hub che usano per firmare i certificati per il dispositivo IoT. Qualsiasi dispositivo con un certificato che è stata problemi tramite il certificato CA radice o uno qualsiasi dei relativi certificati intermedi potranno eseguire l'autenticazione. 

In questa sezione si basa su istruzioni dettagliate nell'articolo dell'IoT Hub [impostare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md). Seguire i passaggi descritti in questa sezione per conoscere i valori da usare per configurare un dispositivo downstream che si connette tramite un gateway. 

Il modo più semplice per testare questo scenario è usare la stessa macchina usato per creare i certificati nel [configurare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Tale macchina dovrebbe già essere configurata con lo strumento giusto, certificato CA radice e un certificato CA intermedio per creare i certificati del dispositivo IoT. È possibile copiare i certificati finali e le chiavi private failover al dispositivo downstream in un secondo momento. Seguire i passaggi descritti nell'articolo di gateway, configurare openssl nel computer, sarà stato clonato il repository di IoT Edge per gli script di creazione di certificati di accesso. Quindi effettuate una directory di lavoro che chiamiamo  **\<WRKDIR >** per contenere i certificati. I certificati predefiniti sono pensati per lo sviluppo e test, pertanto solo degli ultimi 30 giorni. Si dovrebbe creare un certificato CA radice e un certificato intermedio. 

1. Seguire le istruzioni nel [certificati di registrazione della CA X.509 all'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) sezione *impostare la sicurezza X.509 nell'hub IoT di Azure*. In questa sezione si eseguire la procedura seguente: 

   1. Caricare un certificato CA radice. Se si usano i certificati che è stato creato nell'articolo gateway trasparente, caricare  **\<WRKDIR > /certs/azure-iot-test-only.root.ca.cert.pem** come il file del certificato radice. 
   2. Verificare che si è proprietari di tale certificato CA radice. È possibile verificare il possesso con gli strumenti di certificato in \<WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Seguire le istruzioni nel [creare un dispositivo X.509 per l'hub IoT](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) sezione *impostare la sicurezza X.509 nell'hub IoT di Azure*. In questa sezione si eseguire la procedura seguente: 

   1. Aggiungere un nuovo dispositivo. Specificare un nome per caratteri minuscolo **ID dispositivo**, quindi scegliere il tipo di autenticazione **firmato da CA X.509**. 
   2. Impostare un dispositivo padre. Per i dispositivi downstream, selezionare **impostare un dispositivo padre** e scegliere il dispositivo gateway che fornirà la connessione all'IoT Hub di IoT Edge. 

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato in IoT Hub per stabilire questa catena. Usare lo stesso ID dispositivo minuscolo scelto per l'identità del dispositivo nel portale.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. In qualsiasi directory nel dispositivo downstream, copiare i file seguenti: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Si farà riferimento a questi file nelle applicazioni dispositivo foglia che si connettono all'IoT Hub. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione, ad esempio [compilare copia sicura](https://www.ssh.com/ssh/scp/) per spostare i file di certificato.

È possibile usare la [l'estensione della riga di comando di Azure IoT](https://github.com/Azure/azure-iot-cli-extension) per completare l'operazione di creazione dispositivo stesso. Nell'esempio seguente crea un nuovo dispositivo IoT con l'autenticazione della CA X.509 firmato e assegna un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Per altre informazioni sui comandi della riga di comando di Azure per creare un dispositivo e la gestione di relazione padre/figlio, vedere il contenuto di riferimento per [az iot hub device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) comandi.


### <a name="connect-to-iot-hub-through-a-gateway"></a>Connettersi all'IoT Hub tramite un gateway

Ogni SDK di IoT di Azure gestisce l'autenticazione X.509 in modo leggermente diverso. Tuttavia, lo stesso processo viene utilizzato per autenticare IoT regolare i dispositivi all'IoT Hub con certificati X.509 vale anche per i dispositivi downstream. L'unica differenza è che è necessario aggiungere un puntatore per il dispositivo gateway per indirizzare la connessione o, negli scenari offline, per gestire l'autenticazione per conto dell'IoT Hub. In generale, è possibile seguire gli stessi passaggi di autenticazione X.509 per tutti i dispositivi dell'IoT Hub, quindi è sufficiente sostituire il valore di **Hostname** nella stringa di connessione sia il nome host del dispositivo gateway. 

Le sezioni seguenti illustrano alcuni esempi per linguaggi diversi SDK. 

>[!IMPORTANT]
>Gli esempi seguenti illustrano come il SDK dell'Hub IoT Usa i certificati per autenticare i dispositivi. In una distribuzione di produzione, è consigliabile archiviare tutti i segreti, ad esempio private o chiavi di firma di accesso condiviso in un modulo di protezione hardware (HSM). 

#### <a name="net"></a>.NET

Per un esempio di un C# programmare l'autenticazione all'IoT Hub con certificati X.509, vedere [impostare la sicurezza X.509 nell'hub IoT di Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Alcune delle righe di chiave di questo esempio vengono elencate qui per illustrare il processo di autenticazione.

Quando si dichiara il nome host per l'istanza di DeviceClient, usare il nome host del dispositivo gateway IoT Edge. Il nome host è reperibile nel file config. yaml del dispositivo gateway. 

Se si usano i certificati di prova forniti da repository git di IoT Edge, è la chiave per i certificati **1234**.

```csharp
try
{
    var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
    var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
    var deviceClient = DeviceClient.Create("<gateway hostname>", auth, TransportType.Amqp_Tcp_Only);

    if (deviceClient == null)
    {
        Console.WriteLine("Failed to create DeviceClient!");
    }
    else
    {
        Console.WriteLine("Successfully created DeviceClient!");
        SendEvent(deviceClient).Wait();
    }

    Console.WriteLine("Exiting...\n");
}
catch (Exception ex)
{
    Console.WriteLine("Error in sample: {0}", ex.Message);
}
```

#### <a name="c"></a>C

Per un esempio di un programma C, l'autenticazione all'IoT Hub con certificati X.509, vedere il SDK di IoT C [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) esempio. Alcune delle righe di chiave di questo esempio vengono elencate qui per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il **HostName** parametro. Il nome host è reperibile nel file config. yaml del dispositivo gateway. 

```C
// If your downstream device uses X.509 authentication (self signed or X.509 CA) then
// resulting connection string should look like the following:
// "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
static const char* connectionString = "[Downstream device IoT Edge connection string]";

// Path to the Edge "owner" root CA certificate
static const char* edge_ca_cert_path = "[Path to root CA certificate]";

// When the downstream device uses X.509 authentication, a certificate and key 
// in PRM format must be provided.
static const char * x509_device_cert_path = "[Path to primary or secondary device cert]";
static const char * x509_device_key_path = "[Path to primary or secondary device key]";

int main(void)
{
    // Create the iothub handle here
    device_handle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, protocol);

    // Provide the Azure IoT device client with the same root
    // X509 CA certificate that was used to set up the IoT Edge gateway runtime
    if (edge_ca_cert_path != NULL)
    {
        cert_string = obtain_edge_ca_certificate();
        (void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
    }

    if ((x509_device_cert_path != NULL) && (x509_device_key_path != NULL))
    {
        const char *x509certificate = obtain_file_contents(x509_device_cert_path);
        const char *x509privatekey = obtain_file_contents(x509_device_key_path);
        if ((IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_CERT, x509certificate) != IOTHUB_CLIENT_OK) ||
            (IoTHubDeviceClient_SetOption(device_handle, OPTION_X509_PRIVATE_KEY, x509privatekey) != IOTHUB_CLIENT_OK)
            )
        {
            printf("failure to set options for x509, aborting\r\n");
            exit(1);
        }
    }
}
```

#### <a name="nodejs"></a>Node.js

Per un esempio di un'applicazione Node. js l'autenticazione all'IoT Hub con certificati X.509, vedere il SDK IoT di Node. js [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) esempio. Alcune delle righe di chiave di questo esempio vengono elencate qui per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il **HostName** parametro. Il nome host è reperibile nel file config. yaml del dispositivo gateway. 

Se si usano i certificati di prova forniti da repository git di IoT Edge, è la chiave per i certificati **1234**.

```node
// String containing Hostname and Device Id in the following format:
//  "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
var connectionString = '<DEVICE CONNECTION STRING WITH x509=true>';
var certFile = '<PATH-TO-CERTIFICATE-FILE>';
var keyFile = '<PATH-TO-KEY-FILE>';
var passphrase = '<KEY PASSPHRASE IF ANY>';

// fromConnectionString must specify a transport constructor, coming from any transport package.
var client = Client.fromConnectionString(connectionString, Protocol);

var options = {
   cert : fs.readFileSync(certFile, 'utf-8').toString(),
   key : fs.readFileSync(keyFile, 'utf-8').toString(),
   passphrase: passphrase
 };

// Calling setOptions with the x509 certificate and key (and optionally, passphrase) will configure the client transport to use x509 when connecting to IoT Hub
client.setOptions(options);
```

#### <a name="python"></a>Python

Per un esempio di un programma Python l'autenticazione all'IoT Hub con certificati X.509, vedere il SDK di IoT Java [iothub_client_sample_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) esempio. Alcune delle righe di chiave di questo esempio vengono elencate qui per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il **HostName** parametro. Il nome host è reperibile nel file config. yaml del dispositivo gateway. 

```python
# String containing Hostname, Device Id in the format:
# "HostName=<gateway device hostname>;DeviceId=<device_id>;x509=true"
CONNECTION_STRING = "[Device Connection String]"

X509_CERTIFICATE = (
    "-----BEGIN CERTIFICATE-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXX""\n"
    "-----END CERTIFICATE-----"
)

X509_PRIVATEKEY = (
    "-----BEGIN RSA PRIVATE KEY-----""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "...""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX""\n"
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    "-----END RSA PRIVATE KEY-----"
)

def iothub_client_init():
    # prepare iothub client
    client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    # this brings in x509 privateKey and certificate
    client.set_option("x509certificate", X509_CERTIFICATE)
    client.set_option("x509privatekey", X509_PRIVATEKEY)

    return client
```

#### <a name="java"></a>Java

Per un esempio di un programma Java l'autenticazione all'IoT Hub con certificati X.509, vedere il SDK di IoT Java [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) esempio. Alcune delle righe di chiave di questo esempio vengono elencate qui per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il **HostName** parametro. Il nome host è reperibile nel file config. yaml del dispositivo gateway. 

```java
//PEM encoded representation of the public key certificate
private static String publicKeyCertificateString =
    "-----BEGIN CERTIFICATE-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END CERTIFICATE-----\n";

//PEM encoded representation of the private key
private static String privateKeyString =
    "-----BEGIN EC PRIVATE KEY-----\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
    "-----END EC PRIVATE KEY-----\n";

DeviceClient client = new DeviceClient(connectionString, protocol, publicKeyCertificateString, false, privateKeyString, false);
```

## <a name="next-steps"></a>Passaggi successivi

Completando questo articolo, si deve avere un dispositivo IoT Edge come gateway trasparente funziona e registrato un dispositivo downstream con un hub IoT. Successivamente, è necessario configurare i dispositivi downstream per considerare attendibile il dispositivo gateway e inviano messaggi ad esso. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

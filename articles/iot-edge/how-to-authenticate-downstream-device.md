---
title: Autenticare i dispositivi downstream-Azure IoT Edge | Microsoft Docs
description: Come autenticare i dispositivi downstream o i dispositivi foglia nell'hub Internet e instradare la connessione tramite i dispositivi Azure IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1634d7cd3dfe8d118e220fa8620ef6467c15ea2c
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983012"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Autenticare un dispositivo downstream con l'hub IoT di Azure

In uno scenario di gateway trasparente, i dispositivi downstream (talvolta denominati dispositivi foglia o dispositivi figlio) necessitano di identità nell'hub Internet come qualsiasi altro dispositivo. Questo articolo illustra le opzioni per l'autenticazione di un dispositivo downstream nell'hub Internet e quindi illustra come dichiarare la connessione del gateway.

Sono disponibili tre passaggi generali per configurare una connessione del gateway trasparente corretta. Questo articolo illustra il secondo passaggio:

1. Il dispositivo gateway deve essere in grado di connettersi in modo sicuro ai dispositivi downstream, ricevere comunicazioni dai dispositivi downstream e indirizzare i messaggi alla destinazione appropriata. Per altre informazioni, vedere [configurare un dispositivo IOT Edge per agire come gateway trasparente](how-to-create-transparent-gateway.md).
2. **Il dispositivo downstream deve avere un'identità del dispositivo per poter eseguire l'autenticazione con l'hub Internet e conoscere la comunicazione tramite il dispositivo gateway.**
3. Il dispositivo downstream deve essere in grado di connettersi in modo sicuro al dispositivo gateway. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

I dispositivi downstream possono eseguire l'autenticazione con l'hub Internet tramite uno dei tre metodi seguenti: chiavi simmetriche (talvolta denominate chiavi di accesso condivise), certificati autofirmati X. 509 o certificati firmati da autorità di certificazione X. 509. I passaggi di autenticazione sono simili ai passaggi usati per configurare qualsiasi dispositivo perimetrale con l'hub Internet, con piccole differenze per dichiarare la relazione del gateway.

I passaggi descritti in questo articolo illustrano il provisioning manuale dei dispositivi, non il provisioning automatico con il servizio Device provisioning in hub Azure. 

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti in [configurare un dispositivo IOT Edge come gateway trasparente](how-to-create-transparent-gateway.md).

Questo articolo si riferisce al *nome host del gateway* in diversi punti. Il nome host del gateway viene dichiarato nel parametro **hostname** del file config. YAML sul dispositivo gateway IOT Edge. Viene usato per creare i certificati in questo articolo ed è indicato nella stringa di connessione dei dispositivi downstream. Il nome host del gateway deve essere risolvibile in un indirizzo IP, usando DNS o una voce del file host.

## <a name="symmetric-key-authentication"></a>Autenticazione con chiave simmetrica

L'autenticazione con chiave simmetrica, o l'autenticazione della chiave di accesso condivisa, è il modo più semplice per eseguire l'autenticazione con l'hub. Con l'autenticazione con chiave simmetrica, una chiave Base64 è associata all'ID del dispositivo Internet all'interno dell'hub. Questa chiave viene inclusa nelle applicazioni Internet, in modo che il dispositivo possa presentarlo quando si connette all'hub Internet. 

### <a name="create-the-device-identity"></a>Creare l'identità del dispositivo 

Aggiungere un nuovo dispositivo Internet all'interno dell'hub Internet, usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'estensione Internet per Visual Studio Code. Tenere presente che i dispositivi downstream devono essere identificati nell'hub Internet come dispositivo per le cose normali, non IoT Edge dispositivi. 

Quando si crea la nuova identità del dispositivo, fornire le seguenti informazioni: 

* Creare un ID per il dispositivo.

* Selezionare **chiave simmetrica** come tipo di autenticazione. 

* Facoltativamente, scegliere di **impostare un dispositivo padre** e selezionare il IOT Edge dispositivo gateway a cui si connetterà il dispositivo downstream. Questo passaggio è facoltativo per l'autenticazione con chiave simmetrica, ma è consigliabile perché l'impostazione di un dispositivo padre consente le [funzionalità offline](offline-capabilities.md) per il dispositivo downstream. È sempre possibile aggiornare i dettagli del dispositivo per aggiungere o modificare l'elemento padre in un secondo momento. 

   ![Creare l'ID dispositivo con l'autenticazione con chiave simmetrica nel portale](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

Per completare la stessa operazione, è possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione con chiave simmetrica e viene assegnato un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID}
```

Per ulteriori informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi e la gestione di elementi padre/figlio, vedere il contenuto di riferimento per i comandi [Device-Identity di AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) Internet

### <a name="connect-to-iot-hub-through-a-gateway"></a>Connettersi all'hub Internet tramite un gateway

Lo stesso processo viene usato per autenticare i dispositivi normali per l'hub delle cose con chiavi simmetriche valide anche per i dispositivi downstream. L'unica differenza è che è necessario aggiungere un puntatore al dispositivo gateway per indirizzare la connessione o, in scenari offline, per gestire l'autenticazione per conto dell'hub Internet. 

Per l'autenticazione con chiave simmetrica, non è necessario eseguire operazioni aggiuntive sul dispositivo per autenticarsi con l'hub Internet. I certificati sono ancora necessari, in modo che il dispositivo downstream possa connettersi al dispositivo gateway, come descritto in [connettere un dispositivo downstream a un gateway Azure IOT Edge](how-to-connect-downstream-device.md).

Dopo aver creato un'identità del dispositivo Internet nel portale, è possibile recuperare le relative chiavi primarie o secondarie. Una di queste chiavi deve essere inclusa nella stringa di connessione inclusa in qualsiasi applicazione che comunica con l'hub Internet. Per l'autenticazione con chiave simmetrica, l'hub Internet fornisce la stringa di connessione completamente formattata nei dettagli del dispositivo per praticità. È necessario aggiungere informazioni aggiuntive sul dispositivo gateway alla stringa di connessione. 

Per le stringhe di connessione di chiavi simmetriche per i dispositivi downstream sono necessari i componenti seguenti: 

* Hub Internet delle cose a cui si connette il dispositivo:`Hostname={iothub name}.azure-devices.net`
* ID dispositivo registrato con l'hub:`DeviceID={device ID}`
* Chiave primaria o secondaria:`SharedAccessKey={key}`
* Il dispositivo gateway con cui si connette il dispositivo. Specificare il valore del **nome host** dal file config. YAML del dispositivo gateway IOT Edge:`GatewayHostName={gateway hostname}`

Insieme, una stringa di connessione completa ha un aspetto simile al seguente:

``` 
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Se è stata stabilita una relazione padre/figlio per questo dispositivo downstream, è possibile semplificare la stringa di connessione chiamando il gateway direttamente come host della connessione. Ad esempio: 

```
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

## <a name="x509-authentication"></a>Autenticazione X. 509 

Esistono due modi per autenticare un dispositivo Internet delle cose usando i certificati X. 509. Indipendentemente dal modo in cui si sceglie di eseguire l'autenticazione, i passaggi per connettere il dispositivo all'hub Internet è lo stesso. Scegliere certificati autofirmati o firmati da autorità di certificazione per l'autenticazione, quindi continuare a scoprire come connettersi all'hub Internet. 

Per altre informazioni su come l'hub Internet usa l'autenticazione X. 509, vedere gli articoli seguenti: 
* [Autenticazione del dispositivo con certificati della CA X. 509](../iot-hub/iot-hub-x509ca-overview.md)
* [Conoscenza concettuale dei certificati della CA X. 509 nel settore dell'it](../iot-hub/iot-hub-x509ca-concept.md)

### <a name="create-the-device-identity-with-x509-self-signed-certificates"></a>Creare l'identità del dispositivo con certificati autofirmati X. 509

Per l'autenticazione autofirmata X. 509, a volte definita autenticazione con identificazione personale, è necessario creare nuovi certificati da collocare sul dispositivo. Questi certificati hanno un'identificazione personale che si condivide con l'hub Internet per l'autenticazione. 

Il modo più semplice per eseguire il test di questo scenario consiste nell'usare lo stesso computer usato per creare i certificati in [configurare un dispositivo IOT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Il computer dovrebbe essere già configurato con lo strumento appropriato, il certificato della CA radice e il certificato della CA intermedia per creare i certificati del dispositivo Internet delle cose. È possibile copiare i certificati finali e le relative chiavi private sul dispositivo downstream in seguito. Seguendo i passaggi descritti nell'articolo del gateway, si configura OpenSSL nel computer, quindi si clona il repository IoT Edge per accedere agli script di creazione del certificato. È stata quindi creata una directory di lavoro che chiameremo  **\<WRKDIR >** per conservare i certificati. I certificati predefiniti sono progettati per lo sviluppo e il test, quindi solo negli ultimi 30 giorni. È necessario aver creato un certificato CA radice e un certificato intermedio. 

1. Passare alla directory di lavoro in una finestra bash o PowerShell. 

2. Creare due certificati (primario e secondario) per il dispositivo downstream. Specificare il nome del dispositivo e quindi l'etichetta primaria o secondaria. Queste informazioni vengono usate per assegnare un nome ai file, in modo che sia possibile tenere traccia dei certificati per più dispositivi. 

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

3. Recuperare l'impronta digitale SHA1 (denominata identificazione digitale nell'interfaccia dell'hub Internet) da ogni certificato, ovvero una stringa di caratteri esadecimale 40. Usare il comando OpenSSL seguente per visualizzare il certificato e trovare l'impronta digitale:

   ```PowerShell/bash
   openssl x509 -in <WORKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

4. Passare all'hub Internet delle cose nel portale di Azure e creare una nuova identità del dispositivo Internet con i valori seguenti: 

   * Selezionare **X. 509** autofirmato come tipo di autenticazione.
   * Incollare le stringhe esadecimali copiate dai certificati primari e secondari del dispositivo.
   * Selezionare **imposta un dispositivo padre** e scegliere il IOT Edge dispositivo gateway a cui si connetterà il dispositivo downstream. Per l'autenticazione X. 509 di un dispositivo downstream è necessario un dispositivo padre. 

   ![Creare l'ID dispositivo con l'autenticazione autofirmata X. 509 nel portale](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

5. Copiare i file seguenti in qualsiasi directory del dispositivo downstream:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device name>*.key.pem`

   Si farà riferimento a questi file nelle applicazioni per dispositivi foglia che si connettono all'hub Internet. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

È possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure per completare la stessa operazione di creazione del dispositivo. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione autofirmata X. 509 e viene assegnato un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Per altre informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi, la generazione di certificati e la gestione di elementi padre e figlio, vedere il contenuto di riferimento per i comandi [AZ Internet Hub Device-Identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)

### <a name="create-the-device-identity-with-x509-ca-signed-certificates"></a>Creare l'identità del dispositivo con certificati X. 509 firmati dall'autorità di certificazione

Per l'autenticazione firmata (CA) dell'autorità di certificazione X. 509, è necessario un certificato CA radice registrato nell'hub delle cose da usare per firmare i certificati per il dispositivo. Qualsiasi dispositivo che usa un certificato che è stato rilasciato dal certificato CA radice o da qualsiasi certificato intermedio sarà autorizzato a eseguire l'autenticazione. 

Questa sezione è basata sulle istruzioni descritte nell'articolo sull'hub Internet per la [configurazione della sicurezza X. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md). Eseguire la procedura descritta in questa sezione per informazioni sui valori da usare per configurare un dispositivo downstream che si connette tramite un gateway. 

Il modo più semplice per testare questo scenario consiste nell'usare lo stesso computer usato per creare i certificati in [configurare un dispositivo IOT Edge come gateway trasparente](how-to-create-transparent-gateway.md). Il computer dovrebbe essere già configurato con lo strumento appropriato, il certificato della CA radice e il certificato della CA intermedia per creare i certificati del dispositivo Internet delle cose. È possibile copiare i certificati finali e le relative chiavi private sul dispositivo downstream in seguito. Seguendo i passaggi descritti nell'articolo del gateway, si configura OpenSSL nel computer, quindi si clona il repository IoT Edge per accedere agli script di creazione del certificato. È stata quindi creata una directory di lavoro che chiameremo  **\<WRKDIR >** per conservare i certificati. I certificati predefiniti sono progettati per lo sviluppo e il test, quindi solo negli ultimi 30 giorni. È necessario aver creato un certificato CA radice e un certificato intermedio. 

1. Seguire le istruzioni riportate nella sezione [registrare i certificati della CA x. 509](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) nella sezione dell'hub Internet per *configurare la sicurezza x. 509 nell'hub Azure*. In questa sezione si eseguono i passaggi seguenti: 

   1. Caricare un certificato CA radice. Se si usano i certificati creati nell'articolo gateway trasparente, caricare  **\<WRKDIR >/certs/Azure-IOT-Test-only.root.ca.cert.pem** come file del certificato radice. 
   2. Verificare di essere proprietario del certificato CA radice. È possibile verificare il possesso con gli strumenti CERT \<in WRKDIR >. 

      ```powershell
      New-CACertsVerificationCert "<verification code from Azure portal>"
      ```

      ```bash
      ./certGen.sh create_verification_certificate <verification code from Azure portal>"
      ```

2. Seguire le istruzioni riportate nella sezione [creare un dispositivo x. 509 per l'hub Internet per](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *configurare la sicurezza x. 509 nell'hub Azure*. In questa sezione si eseguono i passaggi seguenti: 

   1. Aggiungere un nuovo dispositivo. Specificare un nome in minuscolo per l' **ID dispositivo**e scegliere il tipo di autenticazione firmata dall' **autorità di certificazione X. 509**. 
   2. Impostare un dispositivo padre. Per i dispositivi downstream selezionare **imposta un dispositivo padre** e scegliere il IOT Edge dispositivo gateway che fornirà la connessione all'hub. 

3. Creare una catena di certificati per il dispositivo downstream. Usare lo stesso certificato CA radice caricato nell'hub Internet per creare la catena. Usare lo stesso ID dispositivo minuscolo assegnato all'identità del dispositivo nel portale.

   ```powershell
   New-CACertsDevice "<device id>"
   ```

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

4. Copiare i file seguenti in qualsiasi directory del dispositivo downstream: 

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>*.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>*-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-device-<device id>*.key.pem`

   Si farà riferimento a questi file nelle applicazioni per dispositivi foglia che si connettono all'hub Internet. È possibile usare un servizio come [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) o una funzione come [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) per spostare i file del certificato.

È possibile usare l' [estensione Internet per l'interfaccia](https://github.com/Azure/azure-iot-cli-extension) della riga di comando di Azure per completare la stessa operazione di creazione del dispositivo. Nell'esempio seguente viene creato un nuovo dispositivo Internet con autenticazione firmata dalla CA X. 509 e viene assegnato un dispositivo padre: 

```cli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Per ulteriori informazioni sui comandi dell'interfaccia della riga di comando di Azure per la creazione di dispositivi e la gestione di elementi padre/figlio, vedere il contenuto di riferimento per i comandi [Device-Identity di AZ](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) Internet


### <a name="connect-to-iot-hub-through-a-gateway"></a>Connettersi all'hub Internet tramite un gateway

Ogni SDK Azure è in grado di gestire l'autenticazione X. 509 in modo leggermente diverso. Tuttavia, lo stesso processo viene usato per autenticare i dispositivi normali per l'hub Internet con certificati X. 509 anche per i dispositivi downstream. L'unica differenza è che è necessario aggiungere un puntatore al dispositivo gateway per indirizzare la connessione o, in scenari offline, per gestire l'autenticazione per conto dell'hub Internet. In generale, è possibile seguire gli stessi passaggi di autenticazione X. 509 per tutti i dispositivi dell'hub, quindi sostituire semplicemente il valore di **hostname** nella stringa di connessione in modo che corrisponda al nome host del dispositivo gateway. 

Le sezioni seguenti illustrano alcuni esempi per diversi linguaggi SDK. 

>[!IMPORTANT]
>Gli esempi seguenti illustrano il modo in cui gli SDK dell'hub Internet usa i certificati per autenticare i dispositivi. In una distribuzione di produzione è consigliabile archiviare tutti i segreti come le chiavi private o SAS in un modulo di protezione hardware (HSM). 

#### <a name="net"></a>.NET

Per un esempio di autenticazione C# di un programma nell'hub Internet con certificati x. 509, vedere [configurare la sicurezza x. 509 nell'hub Azure](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates). Di seguito sono riportate alcune delle linee chiave di questo esempio per illustrare il processo di autenticazione.

Quando si dichiara il nome host per l'istanza di DeviceClient, usare il nome host del dispositivo gateway IoT Edge. Il nome host si trova nel file config. YAML del dispositivo gateway. 

Se si usano i certificati di test forniti dal repository IoT Edge git, la chiave per i certificati è **1234**.

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

Per un esempio di un programma C che esegue l'autenticazione nell'hub Internet con certificati X. 509, vedere l'esempio [iotedge_downstream_device_sample](https://github.com/Azure/azure-iot-sdk-c/tree/x509_edge_bugbash/iothub_client/samples/iotedge_downstream_device_sample) di c. Di seguito sono riportate alcune delle linee chiave di questo esempio per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il parametro **hostname** . Il nome host si trova nel file config. YAML del dispositivo gateway. 

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

Per un esempio di un programma node. js che esegue l'autenticazione nell'hub Internet con certificati X. 509, vedere l'esempio [simple_sample_device_x509. js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js) dell'SDK di node. js. Di seguito sono riportate alcune delle linee chiave di questo esempio per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il parametro **hostname** . Il nome host si trova nel file config. YAML del dispositivo gateway. 

Se si usano i certificati di test forniti dal repository IoT Edge git, la chiave per i certificati è **1234**.

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

Per un esempio di un programma Python che esegue l'autenticazione nell'hub Internet con certificati X. 509, vedere l'esempio [iothub_client_sample_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) SDK per Java. Di seguito sono riportate alcune delle linee chiave di questo esempio per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il parametro **hostname** . Il nome host si trova nel file config. YAML del dispositivo gateway. 

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

Per un esempio di un programma Java che esegue l'autenticazione nell'hub Internet con i certificati X. 509, vedere l'esempio [SendEventX509. Java](https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample_x509.py) dell'SDK di Java. Di seguito sono riportate alcune delle linee chiave di questo esempio per illustrare il processo di autenticazione.

Quando si definisce la stringa di connessione per il dispositivo downstream, usare il nome host del dispositivo gateway IoT Edge per il parametro **hostname** . Il nome host si trova nel file config. YAML del dispositivo gateway. 

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

Completando questo articolo, è necessario avere un dispositivo IoT Edge funzionante come gateway trasparente e un dispositivo downstream registrato con un hub Internet. Successivamente, è necessario configurare i dispositivi downstream per considerare attendibile il dispositivo gateway e inviare messaggi. Per altre informazioni, vedere [Connettere un dispositivo downstream a un gateway Azure IoT Edge](how-to-connect-downstream-device.md).

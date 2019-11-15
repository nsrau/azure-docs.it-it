---
title: Eseguire OPC Publisher - Azure | Microsoft Docs
description: Questo articolo descrive come eseguire OPC Publisher ed eseguirne il debug. Include anche considerazioni sulle prestazioni e sulla memoria.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66e2cb30dcd58b7ad0c6cedbb547f75c8039bc58
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824143"
---
# <a name="run-opc-publisher"></a>Eseguire OPC Publisher

Questo articolo descrive come eseguire OPC Publisher ed eseguirne il debug. Include anche considerazioni sulle prestazioni e sulla memoria.

## <a name="command-line-options"></a>Opzioni della riga di comando

Per visualizzare la sintassi dell'applicazione, usare l'opzione della riga di comando `--help` come illustrato di seguito:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

La stringa di connessione del proprietario dell'hub IoT viene in genere specificata solo alla prima esecuzione dell'applicazione. La stringa di connessione è crittografata e archiviata nell'archivio certificati della piattaforma. Nelle esecuzioni successive l'applicazione legge la stringa di connessione dall'archivio certificati. Se si specifica la stringa di connessione a ogni esecuzione, il dispositivo creato per l'applicazione nel registro dei dispositivi hub IoT viene rimosso e creato nuovamente.

## <a name="run-natively-on-windows"></a>Eseguire l'applicazione in modalità nativa in Windows

Aprire il progetto **opcpublisher.sln** con Visual Studio, compilare la soluzione e pubblicarla. È possibile avviare l'applicazione nella **directory di destinazione** in cui è stata eseguita la pubblicazione come indicato di seguito:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Usare un contenitore creato in modo automatico

Compilare il contenitore personalizzato e avviarlo nel modo seguente:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Usare un contenitore di Registro Container Microsoft

In Registro Container Microsoft è disponibile un contenitore predefinito. Avviarlo come indicato di seguito:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Per informazioni sulle architetture di sistemi operativi e processori supportate, vedere [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher). Se l'architettura di sistema operativo e CPU è supportata, Docker seleziona automaticamente il contenitore corretto.

## <a name="run-as-an-azure-iot-edge-module"></a>Eseguire l'applicazione come modulo Azure IoT Edge

OPC Publisher è pronto per l'uso come modulo [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge). Quando si usa OPC Publisher come modulo IoT Edge, gli unici protocolli di trasporto supportati sono **Amqp_Tcp_Only** e **Mqtt_Tcp_Only**.

Per aggiungere OPC Publisher come modulo alla distribuzione di IoT Edge, passare alle impostazioni dell'hub IoT nel portale di Azure e completare la procedura seguente:

1. Passare a **IoT Edge** e creare o selezionare il dispositivo IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli).
1. Selezionare **Aggiungi** in **Moduli di distribuzione** e quindi **Modulo IoT Edge**.
1. Nel campo **Nome** immettere **publisher**.
1. Nel campo **URI immagine** immettere `mcr.microsoft.com/iotedge/opc-publisher:<tag>`.
1. I tag disponibili sono indicati in [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Incollare il codice JSON seguente nel campo **Opzioni di creazione del contenitore**:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Con questa configurazione IoT Edge viene configurato per l'avvio di un contenitore denominato **publisher** usando l'immagine di OPC Publisher. Il nome host del sistema del contenitore è impostato su **publisher**. Per chiamare OPC Publisher, si usa l'argomento della riga di comando seguente: `--aa`. Con questa opzione OPC Publisher considera attendibili i certificati dei server OPC UA a cui si connette. È possibile usare qualsiasi opzione della riga di comando di OPC Publisher. L'unica limitazione è data dalle dimensioni di **Opzioni di creazione del contenitore** supportate da IoT Edge.

1. Lasciare invariate le altre impostazioni e fare clic su **Save** (Salva).
1. Se si vuole elaborare in locale l'output di OPC Publisher con un altro modulo IoT Edge, tornare alla pagina **Imposta moduli**. Passare quindi alla scheda **Specifica route** e aggiungere una nuova route simile al codice JSON seguente:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. Di nuovo nella pagina **Imposta moduli** selezionare **Avanti** finché non si raggiunge l'ultima pagina della configurazione.
1. Selezionare **Invia** per inviare la configurazione a IoT Edge.
1. Quando è stato avviato IoT Edge nel dispositivo Edge e il contenitore Docker **publisher** è in esecuzione, è possibile esaminare l'output del log di OPC Publisher usando `docker logs -f publisher` oppure controllando il file di log. Nell'esempio precedente il file di log si trova al livello superiore di `d:\iiotegde\publisher-publisher.log`. È anche possibile usare lo [strumento iot-edge-opc-publisher-diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Rendere i file di configurazione accessibili nell'host

Per rendere i file di configurazione del modulo IoT Edge accessibili nel file system host, usare le impostazioni seguenti di **Opzioni di creazione del contenitore**. L'esempio seguente è relativo a una distribuzione eseguita con contenitori Linux per Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Con queste opzioni OPC Publisher legge i nodi che deve pubblicare dal file `./pn.json` e la directory di lavoro del contenitore viene impostata su `/appdata` all'avvio. Con queste impostazioni OPC Publisher legge il file `/appdata/pn.json` dal contenitore per ottenerne la configurazione. Senza l'opzione `--pf` OPC Publisher prova a leggere il file di configurazione predefinito `./publishednodes.json`.

Il file di log, che usa il nome predefinito `publisher-publisher.log`, viene scritto in `/appdata`, directory in cui viene creata anche la directory `CertificateStores`.

Per rendere tutti questi file disponibili nel file system host, la configurazione del contenitore richiede un volume con montaggio di associazione. L'associazione `d://iiotedge:/appdata` esegue il mapping della directory `/appdata`, che corrisponde alla directory di lavoro corrente all'avvio del contenitore, alla directory host `d://iiotedge`. Senza questa opzione i dati del file non vengono mantenuti al successivo avvio del contenitore.

Se si eseguono contenitori Windows, la sintassi del parametro `Binds` è diversa. All'avvio del contenitore la directory di lavoro è `c:\appdata`. Per inserire il file di configurazione nella directory `d:\iiotedge` nell'host, specificare il mapping seguente nella sezione `HostConfig`:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Anche se si eseguono contenitori Linux in Linux, la sintassi del parametro `Binds` è diversa. All'avvio del contenitore la directory di lavoro è `/appdata`. Per inserire il file di configurazione nella directory `/iiotedge` nell'host, specificare il mapping seguente nella sezione `HostConfig`:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Considerazioni relative all'uso di un contenitore

Nelle sezioni seguenti sono elencati alcuni aspetti da tenere in considerazione quando si usa un contenitore:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Accedere al server OPC UA di OPC Publisher

Per impostazione predefinita, il server OPC UA di OPC Publisher è in ascolto sulla porta 62222. Per esporre questa porta in ingresso in un contenitore, usare il comando seguente:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Abilitare la risoluzione dei nomi tra contenitori

Per abilitare la risoluzione dei nomi dall'interno del contenitore in altri contenitori, creare una rete bridge Docker definita dall'utente e connettere il contenitore a tale rete con l'opzione `--network`. Assegnare anche un nome al contenitore usando l'opzione `--name` come illustrato di seguito:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Il contenitore è ora accessibile agli altri contenitori nella stessa rete tramite il nome `publisher`.

### <a name="access-other-systems-from-within-the-container"></a>Accedere ad altri sistemi dall'interno del contenitore

È possibile accedere ad altri contenitori usando i parametri descritti nella sezione precedente. Se il sistema operativo in cui è ospitato Docker è abilitato per DNS, l'accesso a tutti i sistemi noti a DNS è disponibile.

Nelle reti che usano la risoluzione dei nomi NetBIOS è possibile abilitare l'accesso ad altri sistemi avviando il contenitore con l'opzione `--add-host`. Questa opzione consente di aggiungere in modo efficace una voce al file host del contenitore:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Assegnare un nome host

OPC Publisher usa il nome host del computer in cui è in esecuzione per generare il certificato e l'endpoint. Docker sceglie un nome host casuale se non ne viene impostato uno con l'opzione `-h`. L'esempio seguente mostra come impostare il nome host interno del contenitore su `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Usare i montaggi di associazione (file system condiviso)

Invece di usare il file system del contenitore, è possibile scegliere il file system host per archiviare informazioni di configurazione e file di log. Per configurare questa opzione, usare l'opzione `-v` di `docker run` nella modalità con montaggio di associazione.

## <a name="opc-ua-x509-certificates"></a>Certificati X.509 di OPC UA

OPC UA usa i certificati X.509 per eseguire l'autenticazione del client e del server OPC UA quando stabiliscono una connessione e per crittografare le comunicazioni tra loro. Per gestire tutti i certificati, OPC Publisher usa gli archivi certificati gestiti dallo stack di OPC UA. All'avvio, OPC Publisher verifica la presenza di un certificato per se stesso. Se nell'archivio certificati non è presente alcun certificato e non ne è stato passato nessuno dalla riga di comando, OPC Publisher crea un certificato autofirmato. Per altre informazioni, vedere il metodo **InitApplicationSecurityAsync** in `OpcApplicationConfigurationSecurity.cs`.

I certificati autofirmati non offrono alcun tipo di sicurezza perché non sono firmati da una CA attendibile.

In OPC Publisher sono disponibili opzioni della riga di comando per:

- Recuperare le informazioni della richiesta di firma del certificato dell'applicazione corrente usato da OPC Publisher.
- Effettuare il provisioning di OPC Publisher con un certificato firmato da una CA.
- Effettuare il provisioning di OPC Publisher con una nuova coppia di chiavi e il certificato corrispondente firmato da una CA.
- Aggiungere i certificati a un archivio certificati di un peer o un'autorità di certificazione attendibile.
- Aggiungere un elenco di revoche di certificati.
- Rimuovere un certificato da un archivio certificati di un peer o di autorità di certificazione attendibili.

Con tutte queste opzioni è possibile passare parametri usando file o stringhe con codifica Base64.

Il tipo di archivio predefinito per tutti gli archivi certificati è il file system, che può essere modificato usando le opzioni della riga di comando. Dal momento che il contenitore non offre l'archiviazione permanente nel proprio file system, è necessario scegliere un tipo di archivio diverso. Usare l'opzione `-v` di Docker per salvare in modo permanente gli archivi certificati nel file system host o in un volume Docker. Se si usa un volume Docker, è possibile passare i certificati usando stringhe con codifica Base64.

L'ambiente di runtime influisce sulla modalità di salvataggio permanente dei certificati. Evitare di creare nuovi archivi certificati ogni volta che si esegue l'applicazione:

- Con l'esecuzione in modalità nativa in Windows non è possibile usare un archivio certificati dell'applicazione di tipo `Directory` perché si verifica un errore durante l'accesso alla chiave privata. In questo caso, usare l'opzione `--at X509Store`.
- Con l'esecuzione come contenitore Docker Linux è possibile eseguire il mapping degli archivi certificati al file system host con l'opzione `-v <hostdirectory>:/appdata` di docker run. Con questa opzione il certificato viene mantenuto tra un'esecuzione e l'altra dell'applicazione.
- Se si sceglie l'esecuzione come contenitore Docker Linux e si vuole usare un archivio X509 per il certificato dell'applicazione, usare l'opzione `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` di docker run e l'opzione `--at X509Store` dell'applicazione.

## <a name="performance-and-memory-considerations"></a>Considerazioni su prestazioni e memoria

Questa sezione illustra le opzioni per la gestione della memoria e delle prestazioni:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parametri della riga di comando per controllare le prestazioni e la memoria

Quando si esegue OPC Publisher, è necessario essere a conoscenza dei requisiti delle prestazioni e delle risorse di memoria disponibili nell'host.

La memoria e le prestazioni sono interdipendenti e dipendono entrambi dalla configurazione del numero di nodi configurati per la pubblicazione. Assicurarsi che i parametri seguenti soddisfino i requisiti:

- Intervallo di invio dell'hub IoT: `--si`
- Dimensioni dei messaggi dell'hub IoT (impostazione predefinita `1`): `--ms`
- Capacità della coda degli elementi monitorati: `--mq`

Il parametro `--mq` consente di controllare il limite superiore della capacità della coda interna, che memorizza nel buffer tutte le notifiche di modifica valore dei nodi OPC. Se OPC Publisher non riesce a inviare rapidamente messaggi all'hub IoT, questa coda memorizza le notifiche nel buffer. Il parametro consente di impostare il numero di notifiche memorizzabili nel buffer. Se si nota che il numero di elementi in questa coda aumenta durante le esecuzioni dei test, per evitare di perdere messaggi è opportuno:

- Ridurre l'intervallo di invio dell'hub IoT
- Aumentare le dimensioni dei messaggi dell'hub IoT

Il parametro `--si` impone a OPC Publisher di inviare messaggi all'hub IoT all'intervallo specificato. OPC Publisher invia un messaggio non appena vengono raggiunte le dimensioni del messaggio specificate dal parametro `--ms` oppure non appena viene raggiunto l'intervallo specificato dal parametro `--si`. Per disabilitare l'opzione relativa alle dimensioni dei messaggi, usare `--ms 0`. In questo caso, per inviare i dati in batch, OPC Publisher usa le dimensioni massime possibili dei messaggi dell'hub IoT, pari a 256 KB.

Il parametro `--ms` consente di inviare in batch i messaggi inviati all'hub IoT. Il protocollo usato determina se il sovraccarico dell'invio di un messaggio all'hub IoT è elevato rispetto al tempo effettivo di invio del payload. Se lo scenario corrente consente la latenza quando i dati vengono inseriti dall'hub IoT, configurare OPC Publisher in modo da usare le dimensioni massime dei messaggi, ovvero 256 KB.

Prima di usare OPC Publisher in scenari di produzione, testare prestazioni e memoria nelle condizioni di tali scenari. È possibile usare il parametro `--di` per specificare l'intervallo in secondi in cui OPC Publisher scrive le informazioni di diagnostica.

### <a name="test-measurements"></a>Misurazioni di test

La diagnostica di esempio seguente mostra le misurazioni con valori diversi per i parametri `--si` e `--ms` quando si pubblicano 500 nodi con un intervallo di pubblicazione OPC di 1 secondo.  Per il test è stata usata una build di debug di OPC Publisher in esecuzione in Windows 10 in modalità nativa per 120 secondi. Come protocollo dell'hub IoT è stato usato il protocollo MQTT predefinito.

#### <a name="default-configuration---si-10---ms-262144"></a>Configurazione predefinita (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Con la configurazione predefinita i dati vengono inviati all'hub IoT ogni 10 secondi oppure quando sono disponibili 256 KB di dati che l'hub IoT può inserire. Con questa configurazione viene aggiunta una latenza moderata di circa 10 secondi, ma si riduce la probabilità di perdita dei dati causata da dimensioni eccessive dei messaggi. L'output di diagnostica indica che non sono presenti aggiornamenti persi dei nodi OPC: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Intervallo di invio costante (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Quando le dimensioni dei messaggi sono impostate su 0, OPC Publisher esegue internamente l'invio in batch dei dati usando le dimensioni massime supportate dall'hub IoT, ovvero 256 KB. L'output della diagnostica mostra che le dimensioni medie dei messaggi sono pari a 115.019 byte. Con questa configurazione OPC Publisher non perde alcun aggiornamento dei valori dei nodi OPC e, rispetto alla configurazione predefinita, mostra una latenza inferiore.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Invio di tutti gli aggiornamenti dei valori dei nodi OPC (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Con questa configurazione viene inviato un messaggio all'hub IoT per ogni modifica dei valori dei nodi OPC. La diagnostica mostra che le dimensioni medie dei messaggi sono ridotte, ovvero pari 234 byte. Il vantaggio di questa configurazione è che OPC Publisher non aggiunge alcuna latenza. Il numero di aggiornamenti persi dei valori dei nodi OPC (`monitored item notifications enqueue failure: 44624`) è elevato, di conseguenza questa configurazione non è adatta per scenari in cui sono presenti volumi elevati di dati di telemetria da pubblicare.

### <a name="maximum-batching---si-0---ms-262144"></a>Valore massimo per invio in batch (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Con questa configurazione viene inviato in batch il maggior numero possibile di aggiornamenti dei valori dei nodi OPC. Le dimensioni massime dei messaggi dell'hub IoT sono pari a 256 KB e vengono configurate qui. Non è richiesto alcun intervallo di invio, di conseguenza la latenza è determinata dalla quantità di dati che l'hub IoT deve inserire. Con questa configurazione si riduce la probabilità di perdita di valori dei nodi OPC, di conseguenza si tratta di una scelta appropriata per la pubblicazione di un numero elevato di nodi. Quando si usa questa configurazione, assicurarsi che lo scenario non implichi condizioni che causano una latenza elevata se le dimensioni dei messaggi non raggiungono 256 KB.

## <a name="debug-the-application"></a>Eseguire il debug dell'applicazione

Per eseguire il debug dell'applicazione, aprire il file di soluzione **opcpublisher.sln** con Visual Studio e usare gli strumenti di debug di Visual Studio.

Se è necessario accedere al server OPC UA in OPC Publisher, assicurarsi che il firewall consenta l'accesso alla porta su cui è in ascolto il server. La porta predefinita è: 62222.

## <a name="control-the-application-remotely"></a>Controllare l'applicazione in modalità remota

Per configurare i nodi da pubblicare, è possibile usare metodi diretti dell'hub IoT.

OPC Publisher implementa alcune chiamate aggiuntive a metodi diretti dell'hub IoT per leggere:

- Informazioni generali.
- Informazioni di diagnostica su sessioni, sottoscrizioni ed elementi monitorati di OPC.
- Informazioni di diagnostica su messaggi ed eventi dell'hub IoT.
- Log di avvio.
- Ultime 100 righe del log.
- Arresto dell'applicazione.

I repository GitHub seguenti contengono gli strumenti per [configurare i nodi da pubblicare](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) e [leggere le informazioni di diagnostica](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Entrambi gli strumenti sono disponibili anche come contenitori in Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Usare un server OPC UA di esempio

Se non è disponibile alcun server OPC UA effettivo, per iniziare è possibile usare il [PLC OPC UA di esempio](https://github.com/Azure-Samples/iot-edge-opc-plc). Questo PLC di esempio è disponibile anche in Docker Hub.

Implementa un certo numero di tag, che generano dati casuali e tag con anomalie. Se è necessario simulare valori di tag aggiuntivi, è possibile estendere l'esempio.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso come eseguire OPC Publisher, per continuare è consigliabile approfondire la conoscenza di [OPC Twin](overview-opc-twin.md) e [OPC Vault](overview-opc-vault.md).

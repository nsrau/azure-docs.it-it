---
title: Usare il server di pubblicazione OPC di fabbrica connessa per Azure IoT Suite | Microsoft Docs
description: Come creare e distribuire l'implementazione di riferimento del server di pubblicazione OPC di fabbrica connessa.
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2018
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Server di pubblicazione OPC per Azure IoT Edge

Questo articolo descrive come usare l'implementazione di riferimento del server di pubblicazione OPC. L'implementazione di riferimento illustra come usare Azure IoT Edge per:

- Connettersi ai server OPC UA esistenti.
- Pubblicare i dati di telemetria codificati JSON da questi server nel formato *Pub/Sub* OPC UA, usando un payload JSON, nell'hub IoT di Azure. È possibile usare uno dei protocolli di trasporto supportati da Azure IoT Edge.

Questa applicazione di riferimento include:

- Un *client* OPC UA per la connessione ai server OPC UA esistenti nella rete.
- Un *server* OPC UA in ascolto sulla porta 62222 che è possibile usare per gestire i dati pubblicati.

L'applicazione viene implementata usando .NET Core e può essere eseguita sulle piattaforme supportate da .NET Core.

Il server di pubblicazione implementa la logica di ripetizione dei tentativi quando stabilisce le connessioni agli endpoint. Il server di pubblicazione prevede che gli endpoint risponderanno a un determinato numero di richieste keep-alive. La logica di ripetizione dei tentativi consente al server di pubblicazione di rilevare le condizioni, ad esempio un'interruzione dell'alimentazione per un server OPC UA.

Per ogni singolo intervallo di pubblicazione in un server OPC UA, crea una sottoscrizione separata in cui vengono aggiornati tutti i nodi con questo intervallo di pubblicazione.

Per ridurre il carico di rete, il server di pubblicazione supporta l'invio in batch dei dati all'hub IoT. Un batch viene inviato all'hub IoT solo quando vengono raggiunte la dimensioni del pacchetto configurato.

Questa applicazione usa stack di riferimento OPC UA di OPC Foundation e pertanto si applicano le restrizioni di licenza. Consultare la documentazione OPC UA e i termini di licenza alla pagina http://opcfoundation.github.io/UA-.NETStandardLibrary/.

È possibile trovare il codice sorgente del server di pubblicazione OPC nel repository GitHub del [server di pubblicazione OPC per Azure IoT Edge](https://github.com/Azure/iot-edge-opc-publisher).

## <a name="prerequisites"></a>prerequisiti

Per compilare l'applicazione, è necessario [.NET Core SDK 1.1.](https://docs.microsoft.com/dotnet/core/sdk) per il sistema operativo usato.

## <a name="build-the-application"></a>Compilare l'applicazione.

### <a name="as-native-windows-application"></a>Come applicazione Windows nativa

Aprire il progetto `OpcPublisher.sln` con Visual Studio 2017 e compilare la soluzione premendo F7.

### <a name="as-docker-container"></a>Come contenitore Docker

Per compilare l'applicazione come contenitore Docker di Windows, usare il file di configurazione `Dockerfile.Windows`.

Per compilare l'applicazione come contenitore Docker di Linux, usare il file di configurazione `Dockerfile`.

Dalla radice del repository del computer di sviluppo digitare il comando seguente in una finestra della console:

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

L'opzione `-f` per `docker build` è facoltativa e per impostazione predefinita viene usato il file di configurazione `Dockerfile`.

Docker consente anche di creare direttamente da un repository git. È possibile creare un contenitore Docker di Linux con il comando seguente:

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>Configurare i nodi OPC UA da pubblicare

Per configurare i nodi OPC UA per cui è necessario pubblicare i valori nell'hub IoT di Azure, creare un file di configurazione formattato come JSON. Il nome predefinito per questo file di configurazione è `publishednodes.json`. L'applicazione aggiorna e salva il file di configurazione quando usa i metodi del server OPC UA **PublishNode** o **UnpublishNode**.

La sintassi del file di configurazione è la seguente:

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>Eseguire l'applicazione

### <a name="command-line-options"></a>Opzioni della riga di comando

Per visualizzare l'uso completo dell'applicazione, usare l'opzione della riga di comando `--help`. L'esempio seguente illustra la struttura di un comando:

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` è il nome dell'applicazione OPC UA da usare. Questo parametro è obbligatorio. Il nome dell'applicazione viene anche usato per registrare il server di pubblicazione nel registro di sistema del dispositivo hub IoT.

`IoT Hubconnectionstring` è la stringa di connessione del proprietario dell'hub IoT. Questo parametro è facoltativo e,

Sono supportate le opzioni seguenti:

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
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
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

È possibile usare le seguenti variabili di ambiente per controllare l'applicazione:
- `_HUB_CS`: imposta la stringa di connessione del proprietario dell'hub IoT
- `_GW_LOGP`: imposta il nome del file di log da usare
- `_TPC_SP`: imposta il percorso in cui archiviare i certificati delle stazioni attendibili
- `_GW_PNFP`: imposta il nome file del file di configurazione del server di pubblicazione

Gli argomenti della riga di comando ignorano le impostazioni della variabile di ambiente.

In genere, specificare la stringa di connessione del proprietario dell'hub IoT solo al primo avvio dell'applicazione. La stringa di connessione è crittografata e archiviata nell'archivio certificati della piattaforma.

Per le chiamate successive, la stringa di connessione viene letta dall'archivio certificati della piattaforma e viene usata di nuovo. Se si specifica la stringa di connessione a ogni avvio, il dispositivo nel registro di sistema del dispositivo hub IoT viene rimosso e ricreato ogni volta.

### <a name="native-on-windows"></a>Nativo in Windows

Per eseguire l'applicazione in modo nativo in Windows, aprire il progetto `OpcPublisher.sln` con Visual Studio 2017, compilare la soluzione e pubblicarla. È possibile avviare l'applicazione nella **Directory di destinazione** pubblicata con:

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>Usare un contenitore creato in modo automatico

Per eseguire l'applicazione in un contenitore creato in modo automatico, creare e avviare il contenitore:

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>Usare un contenitore di hub.docker.com

In DockerHub è disponibile un contenitore predefinito. Per avviarlo eseguire il comando seguente:

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>Importante quando si usa un contenitore

#### <a name="access-to-the-publisher-opc-ua-server"></a>Accedere al server di pubblicazione OPC UA

Il server di pubblicazione OPC UA per impostazione predefinita è in ascolto sulla porta 62222. Per esporre questa porta in ingresso in un contenitore, è necessario usare l'opzione `docker run` `-p`:

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>Abilitare la risoluzione dei nomi tra contenitori

Per abilitare la risoluzione dei nomi all'interno di un contenitore per altri contenitori, è necessario:

- Creare una rete bridge del docker definita dall'utente.
- Collegare il contenitore alla rete usando l'opzione `--network`.
- Assegnare un nome al contenitore usando l'opzione `--name`.

L'esempio seguente mostra queste opzioni di configurazione:

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Il contenitore può ora essere raggiunto da altri contenitori in rete tramite il nome `publisher`.

#### <a name="assign-a-hostname"></a>Assegnare un nome host

Il server di pubblicazione usa il nome host del computer in cui è in esecuzione per generare il certificato e l'endpoint. Docker sceglie un nome host casuale a meno che l'utente non ne imposti uno con l'opzione `-h`. Di seguito è riportato un esempio su come impostare il nome host interno del contenitore in `publisher`:

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>Uso di bind mount (file system condiviso)

In alcuni scenari, si desidera leggere le informazioni di configurazione, o scrivere i file di log nell'host anziché nel file system del contenitore. Per configurare questo comportamento, usare l'opzione `-v` di `docker run` nella modalità montaggio di associazione. Ad esempio: 

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>Archivio per certificati X509

L'archiviazione di certificati X509 non funziona con i montaggi di associazioni, perché le autorizzazioni del percorso dell'archivio devono essere `rw` per il proprietario. È invece necessario usare l'opzione `-v` di `docker run` nella modalità volume.

## <a name="debug-the-application"></a>Eseguire il debug dell'applicazione

### <a name="native-on-windows"></a>Nativo in Windows

Aprire il progetto `OpcPublisher.sln` con Visual Studio 2017 e avviare il debug dell'app premendo F5.

### <a name="in-a-docker-container"></a>In un contenitore Docker

Visual Studio 2017 supporta il debug di applicazioni in un contenitore Docker tramite `docker-compose`. Tuttavia, questo metodo non consente di passare i parametri della riga di comando.

Un'opzione alternativa di debug supportata da VS2017 consiste nell'eseguire il debug su `ssh`. È possibile usare il file di configurazione di compilazione docker `Dockerfile.ssh` nella radice del repository per creare un contenitore SSH abilitato:

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

È ora possibile avviare il contenitore per eseguire il debug del server di pubblicazione:

```cmd/sh
docker run -it publisherssh
```

Nel contenitore è necessario avviare il daemon **ssh** manualmente:

```cmd/sh
service ssh start
```

A questo punto, è possibile creare una sessione **ssh** come utente `root` con password `Passw0rd`.

Per preparare il debug dell'applicazione nel contenitore, completare i passaggi aggiuntivi seguenti:

1. Sul lato host creare un file `launch.json`:

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. Compilare il progetto e pubblicarlo in una directory a scelta.

1. Usare uno strumento come `WinSCP` per copiare i file pubblicati nella directory `/root/publisher` del contenitore. Se si sceglie di usare una directory diversa, aggiornare la proprietà `cdw` nel file `launch.json`.

È ora possibile avviare il debug usando il comando seguente nella finestra di comando di Visual Studio:

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>Passaggi successivi

Come suggerito il passaggio successivo consiste nell'imparare come [distribuire un gateway in Windows o Linux per la soluzione preconfigurata di fabbrica connessa](iot-suite-connected-factory-gateway-deployment.md).
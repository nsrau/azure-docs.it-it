---
title: Configurare OPC Publisher - Azure | Microsoft Docs
description: Come configurare OPC Publisher
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: bccab4dde5e17ec30a0b8c5e36dd78bdd1bdff93
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605716"
---
# <a name="configure-opc-publisher"></a>Configurare OPC Publisher

È possibile configurare OPC Publisher per specificare:

- Le modifiche ai dati del nodo OPC UA da pubblicare.
- Gli eventi di OPC UA da pubblicare.
- Il formato dei dati di telemetria.

È possibile configurare OPC Publisher usando i file di configurazione o le chiamate al metodo.

## <a name="use-configuration-files"></a>Usare i file di configurazione

Questa sezione descrive le opzioni disponibili per configurare la pubblicazione del nodo OPC UA con i file di configurazione.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Usare un file di configurazione per configurare le modifiche ai dati di pubblicazione

Il modo più semplice per configurare i nodi OPC UA da pubblicare è quello di usare un file di configurazione. Il formato di file di configurazione è documentato in [publishednodes](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) all'interno del repository.

La sintassi dei file di configurazione è cambiata nel corso del tempo. OPC Publisher è ancora in grado di leggere i formati precedenti, ma li converte in un formato più recente nel momento in cui salva la configurazione in modo permanente.

L'esempio seguente illustra il formato del file di configurazione:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Usare un file di configurazione per configurare gli eventi pubblici

Per pubblicare eventi OPC UA, è possibile usare lo stesso file di configurazione adottato per la modifica dei dati.

Nell'esempio seguente viene illustrato come configurare la pubblicazione di eventi generati dal [server SimpleEvents](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server). Il server SimpleEvents disponibile nel [repository OPC Foundation](https://github.com/OPCFoundation/UA-.NETStandard) è:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Usare le chiamate al metodo

Questa sezione descrive le chiamate al metodo che è possibile usare per configurare OPC Publisher.

### <a name="configure-using-opc-ua-method-calls"></a>Configurare tramite chiamate al metodo OPC UA

OPC Publisher include un server OPC UA, a cui è accessibile accedere dalla porta 62222. Se il nome host è **server di pubblicazione**, l'endpoint dell'endpoint è: `opc.tcp://publisher:62222/UA/Publisher`.

L'endpoint espone i quattro metodi seguenti:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>Configurare tramite chiamate a metodi diretti dell'hub IoT

OPC Publisher implementa le seguenti chiamate a metodi diretti dell'hub IoT:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- GetDiagnosticInfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- ExitApplication
- GetInfo

Il formato del payload JSON della richiesta e delle risposte del metodo è definito nel file [opcpublisher/HubMethodModel.cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs).

Se si chiama un metodo sconosciuto nel modulo, la risposta sarà costituita da una stringa in cui si specifica che il metodo non è implementato. È possibile chiamare un metodo sconosciuto per eseguire il ping del modulo.

### <a name="configure-username-and-password-for-authentication"></a>Configurare il nome utente e la password per l'autenticazione

La modalità di autenticazione può essere impostata tramite chiamate a un metodo diretto dell'hub IoT. Il payload deve contenere la proprietà **OpcAuthenticationMode**, oltre a nome utente e password:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

La password viene crittografata tramite il client del carico di lavoro dell'hub IoT e archiviata nella configurazione del server di pubblicazione. Per ripristinare l'autenticazione anonima, usare il metodo con il payload seguente:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Se nel payload non è impostata la proprietà **OpcAuthenticationMode**, le impostazioni di autenticazione rimangono invariate nella configurazione.

## <a name="configure-telemetry-publishing"></a>Configurare la pubblicazione dei dati di telemetria

Quando OPC Publisher riceve una notifica relativa alla modifica di un valore in un nodo pubblicato, viene generato un messaggio in formato JSON, successivamente inviato all'hub IoT.

È possibile configurare il contenuto del messaggio JSON usando un file di configurazione. Se non è presente alcun file di configurazione con l'opzione `--tc`, viene usata una configurazione predefinita compatibile con l'[acceleratore di soluzioni di connected factory](https://github.com/Azure/azure-iot-connected-factory).

Se OPC Publisher è configurato per l'invio di messaggi in batch, vengono inviati come una matrice JSON valida.

I dati di telemetria vengono ricavati dalle origini seguenti:

- La configurazione del nodo OPC Publisher
- L'oggetto **MonitoredItem** dello stack OPC UA per il quale OPC Publisher ha ricevuto una notifica.
- L'argomento passato a questa notifica, che fornisce informazioni dettagliate sulla modifica dei valori dei dati.

I dati di telemetria inseriti nel messaggio in formato JSON costituiscono una selezione di importanti proprietà di questi oggetti. Se sono necessarie altre proprietà, è necessario modificare la codebase di OPC Publisher.

La sintassi del file di configurazione è la seguente:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le modalità di configurazione di OPC Publisher, il passaggio successivo consigliato è imparare a [Eseguire OPC Publisher](howto-opc-publisher-run.md).

---
title: Pubblicare e sottoscrivere con Azure IoT Edge | Microsoft Docs
description: Usare IoT Edge broker MQTT per pubblicare e sottoscrivere i messaggi
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1ace40098e1d53c6199accea755ffb6969781663
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015664"
---
# <a name="publish-and-subscribe-with-azure-iot-edge"></a>Pubblicare e sottoscrivere con Azure IoT Edge

È possibile utilizzare Azure IoT Edge broker MQTT per pubblicare e sottoscrivere i messaggi. Questo articolo illustra come connettersi a questo broker, pubblicare e sottoscrivere i messaggi sugli argomenti definiti dall'utente e usare le primitive di messaggistica dell'hub Internet. Il IoT Edge broker MQTT è incorporato nell'hub IoT Edge. Per ulteriori informazioni, vedere [le funzionalità di Service Broker dell'hub IOT Edge](iot-edge-runtime.md).

> [!NOTE]
> IoT Edge broker MQTT è attualmente disponibile in anteprima pubblica.

## <a name="pre-requisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione valida
- [Interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) della riga di comando di Azure con l' `azure-iot` estensione CLI installata. Per altre informazioni, vedere [la procedura di installazione dell'estensione Azure Internet per l'interfaccia della riga di comando di](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-iot)Azure.
- Un **Hub** Internet delle cose dello SKU F1, S1, S2 o S3.
- Avere un **dispositivo IOT Edge con la versione 1,2 o successiva**. Poiché IoT Edge MQTT Broker è attualmente disponibile in anteprima pubblica, impostare le variabili di ambiente seguenti su true nel contenitore edgeHub per abilitare il broker MQTT:

   | Nome | Valore |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- **Client di mosquitto** installati nel dispositivo IOT Edge. Questo articolo usa i client mosquitto più diffusi che includono [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) e [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html). In alternativa, è possibile usare altri client MQTT. Per installare i client di mosquitto in un dispositivo Ubuntu, eseguire il comando seguente:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    Non installare il server mosquitto perché potrebbe causare il blocco delle porte MQTT (8883 e 1883) e il conflitto con l'hub IoT Edge.

## <a name="connect-to-iot-edge-hub"></a>Connettersi all'hub IoT Edge

La connessione a IoT Edge Hub segue gli stessi passaggi descritti nell'articolo relativo alla [connessione a un hub Internet con un client MQTT generico](../iot-hub/iot-hub-mqtt-support.md) o alla [Descrizione concettuale dell'hub IOT Edge](iot-edge-runtime.md). Questi passaggi sono i seguenti:

1. Facoltativamente, il client MQTT stabilisce una *connessione sicura* con l'hub IoT Edge usando Transport Layer Security (TLS)
2. Il client di MQTT esegue l' *autenticazione* per IOT Edge Hub
3. L'hub IoT Edge *autorizza* il client MQTT in base ai criteri di autorizzazione

### <a name="secure-connection-tls"></a>Connessione protetta (TLS)

Transport Layer Security (TLS) viene usato per stabilire una comunicazione crittografata tra il client e l'hub IoT Edge.

Per disabilitare TLS, usare la porta 1883 (MQTT) e associare il contenitore edgeHub alla porta 1883.

Per abilitare TLS, se un client si connette sulla porta 8883 (MQTTS) al broker MQTT, viene avviato un canale TLS. Il Broker invia la relativa catena di certificati che il client deve convalidare. Per convalidare la catena di certificati, è necessario installare il certificato radice di MQTT broker come certificato attendibile nel client. Se il certificato radice non è attendibile, la libreria client verrà rifiutata dal broker MQTT con un errore di verifica del certificato. La procedura da seguire per installare il certificato radice del broker nel client è identica a quella del [gateway trasparente](how-to-create-transparent-gateway.md) e viene descritta nella documentazione [preparare un dispositivo downstream](how-to-connect-downstream-device.md#prepare-a-downstream-device) .

### <a name="authentication"></a>Authentication

Per eseguire l'autenticazione di un client MQTT, è necessario prima di tutto inviare un pacchetto di connessione al broker MQTT per avviare una connessione nel nome. Questo pacchetto fornisce tre tipi di informazioni di autenticazione: a `client identifier` , a `username` e `password` :

-   Il `client identifier` campo è il nome del dispositivo o del nome del modulo nell'hub Internet. Viene usata la sintassi seguente:

    - Per un dispositivo: `<device_name>`

    - Per un modulo: `<device_name>/<module_name>`

   Per connettersi a MQTT broker, un dispositivo o un modulo deve essere registrato nell'hub Internet.

   Si noti che il broker non consentirà la connessione di due client usando le stesse credenziali. Il broker disconnetterà il client già connesso se un secondo client si connette utilizzando le stesse credenziali.

- Il `username` campo viene derivato dal nome del dispositivo o del modulo e dal nome del IoTHub a cui appartiene il dispositivo usando la sintassi seguente:

    - Per un dispositivo: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

    - Per un modulo: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- Il `password` campo del pacchetto di connessione dipende dalla modalità di autenticazione:

    - Nel caso dell' [autenticazione con chiavi simmetriche](how-to-authenticate-downstream-device.md#symmetric-key-authentication), il `password` campo è un token SAS.
    - Nel caso dell' [autenticazione autofirmata X. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), il `password` campo non è presente. In questa modalità di autenticazione è necessario un canale TLS. Il client deve connettersi alla porta 8883 per stabilire una connessione TLS. Durante l'handshake TLS, il broker MQTT richiede un certificato client. Questo certificato viene usato per verificare l'identità del client e quindi il `password` campo non è necessario in un secondo momento quando viene inviato il pacchetto di connessione. Inviando un certificato client e il campo password si verificherà un errore e la connessione verrà chiusa. Le librerie MQTT e le librerie client TLS hanno in genere un modo per inviare un certificato client quando si avvia una connessione. È possibile vedere un esempio dettagliato nella sezione [uso del certificato X509 per l'autenticazione client](how-to-authenticate-downstream-device.md#x509-self-signed-authentication).

I moduli distribuiti da IoT Edge usano [l'autenticazione con chiavi simmetriche](how-to-authenticate-downstream-device.md#symmetric-key-authentication) e possono chiamare l' [API del carico di lavoro IOT Edge](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) locale per ottenere a livello di codice un token SAS anche quando è offline.

### <a name="authorization"></a>Autorizzazione

Dopo l'autenticazione di un client MQTT per IoT Edge Hub, è necessario che sia autorizzato a connettersi. Una volta stabilita la connessione, deve essere autorizzato a pubblicare o sottoscrivere argomenti specifici. Queste autorizzazioni vengono concesse dall'hub IoT Edge in base ai relativi criteri di autorizzazione. Il criterio di autorizzazione è un set di istruzioni espresse come una struttura JSON che viene inviata all'hub IoT Edge tramite il gemello. Modificare un dispositivo gemello dell'hub IoT Edge per configurare i criteri di autorizzazione.

> [!NOTE]
> Per l'anteprima pubblica, la modifica dei criteri di autorizzazione del broker MQTT è disponibile solo tramite Visual Studio, Visual Studio Code o l'interfaccia della riga di comando di Azure. Il portale di Azure attualmente non supporta la modifica del dispositivo gemello dell'hub IoT Edge e dei relativi criteri di autorizzazione.

Ogni istruzione dei criteri di autorizzazione è costituita dalla combinazione di `identities` , `allow` o `deny` effetto, `operations` e `resources` :

- `identities` descrivere l'oggetto del criterio. Deve eseguire il mapping all'oggetto `client identifier` inviato dai client nel pacchetto Connect.
- `allow``deny`in alternativa, definire se consentire o negare le operazioni.
- `operations` definire le azioni da autorizzare. `mqtt:connect``mqtt:publish`e `mqtt:subscribe` sono attualmente le tre azioni supportate.
- `resources` definire l'oggetto dei criteri. Può trattarsi di un argomento o di un modello di argomento definito con [caratteri jolly MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107).

Di seguito è riportato un esempio di un criterio di autorizzazione che non consente esplicitamente al client "rogue_client" di connettersi, consente a tutti i client Azure per la connessione e consente a "sensor_1" di pubblicare in un argomento `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Un paio di aspetti da tenere presenti quando si scrivono i criteri di autorizzazione:
- Richiede la `$edgeHub` versione dello schema gemello 1,2
- Per impostazione predefinita, tutte le operazioni vengono negate.
- Le istruzioni di autorizzazione vengono valutate nell'ordine in cui sono visualizzate nella definizione JSON. Inizia osservando `identities` e quindi selezionando le prime istruzioni Allow o Deny che corrispondono alla richiesta. In caso di conflitti tra le istruzioni Allow e Deny, l'istruzione DENY prevale.
- Nel criterio di autorizzazione è possibile usare diverse variabili, ad esempio le sostituzioni:
    - `{{iot:identity}}` rappresenta l'identità del client attualmente connesso. Ad esempio `myDevice` nel caso di un dispositivo, `myEdgeDevice/SampleModule` nel caso di un modulo.
    - `{{iot:device_id}}` rappresenta l'identità del dispositivo attualmente connesso. Ad esempio `myDevice` nel caso di un dispositivo, `myEdgeDevice` nel caso di un modulo.
    - `{{iot:module_id}}` rappresenta l'identità del modulo attualmente connesso. Ad esempio, nel caso di un dispositivo, `SampleModule` nel caso di un modulo.
    - `{{iot:this_device_id}}` rappresenta l'identità del dispositivo IoT Edge che esegue i criteri di autorizzazione. Ad esempio `myIoTEdgeDevice`.

L'autorizzazione per gli argomenti dell'hub Internet viene gestita in modo leggermente diverso rispetto agli argomenti definiti dall'utente. Ecco i punti chiave da ricordare:
- Per i dispositivi o i moduli di Azure, è necessaria una regola di autorizzazione esplicita per connettersi a IoT Edge broker MQTT Hub. Di seguito è riportato un criterio di autorizzazione di connessione predefinito.
- Per impostazione predefinita, i dispositivi o i moduli di Azure sono in grado di accedere ai propri argomenti dell'hub Internet senza alcuna regola di autorizzazione esplicita. Tuttavia, le autorizzazioni derivano da relazioni padre/figlio in tal caso e queste relazioni devono essere impostate. I moduli IoT Edge vengono impostati automaticamente come elementi figlio del dispositivo IoT Edge, ma i dispositivi devono essere impostati in modo esplicito come elementi figlio del gateway di IoT Edge.
- I dispositivi o i moduli di Azure sono in grado di accedere agli argomenti, inclusi gli argomenti relativi all'hub degli argomenti, di altri dispositivi o moduli che forniscono regole di autorizzazione esplicite appropriate.

Ecco un criterio di autorizzazione predefinito che può essere usato per consentire a tutti i dispositivi o moduli Azure Internet di **connettersi** al Broker:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Ora che si è appreso come connettersi alla IoT Edge MQTT broker, è possibile vedere come usarlo per pubblicare e sottoscrivere i messaggi prima sugli argomenti definiti dall'utente, quindi sugli argomenti dell'hub Internet e infine su un altro broker di MQTT.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Pubblicare e sottoscrivere argomenti definiti dall'utente

In questo articolo si userà un client denominato **sub_client** che sottoscrive un argomento e un altro client denominato **pub_client** che pubblica in un argomento. Si userà l' [autenticazione con chiave simmetrica](how-to-authenticate-downstream-device.md#symmetric-key-authentication) , ma è possibile eseguire la stessa operazione con l'autenticazione [autofirmata x. 509](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) o con l' [autenticazione firmata dall'autorità di certificazione x. 509](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).

### <a name="create-publisher-and-subscriber-clients"></a>Creazione di client di server di pubblicazione e Sottoscrittore

Creare due dispositivi Internet all'interno dell'hub Internet e ottenere le password. Usando l'interfaccia della riga di comando di Azure dal terminale per:

1. Creare due dispositivi Internet all'interno dell'hub degli elementi, come padre per il dispositivo IoT Edge:

    ```azurecli-interactive
    az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
    ```

2. Ottenere le password generando un token SAS:

    - Per un dispositivo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
       ```
    
       dove 3600 è la durata del token SAS in secondi, ad esempio 3600 = 1 ora.
    
    - Per un modulo:
    
       ```azurecli-interactive
       az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
       ```
    
       dove 3600 è la durata del token SAS in secondi, ad esempio 3600 = 1 ora.

3. Copiare il token SAS che corrisponde al valore corrispondente alla chiave "SAS" dell'output. Ecco un esempio di output del comando dell'interfaccia della riga di comando di Azure sopra:

    ```
    {
       "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
    }
    ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Autorizzare client di pubblicazione e Sottoscrittore

Per autorizzare il server di pubblicazione e il Sottoscrittore, modificare il IoT Edge gemello dell'hub usando l'interfaccia della riga di comando di Azure, Visual Studio o Visual Studio Code per includere i criteri di autorizzazione seguenti

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               },
               {
                  "identities": [
                     "pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ],
                     }
                  ],
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>Autenticazione con chiavi simmetriche senza TLS

#### <a name="subscribe"></a>Sottoscrivi

Connettere il client di **sub_client** MQTT al broker MQTT e sottoscrivere il eseguendo `test_topic` il comando seguente sul dispositivo IOT Edge:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

dove `<edge_device_address>`  =  `localhost` in questo esempio il client viene eseguito nello stesso dispositivo come IOT Edge.

Si noti che in questo primo esempio viene utilizzata la porta 1883 (MQTT), ad esempio senza TLS. Un altro esempio con la porta 8883 (MQTTS), ad esempio con TLS abilitato, viene visualizzato nella sezione successiva.

Il client **sub_client** MQTT è ora avviato ed è in attesa dei messaggi in arrivo `test_topic` .

#### <a name="publish"></a>Pubblica

Connettere il client di **pub_client** MQTT al broker MQTT e pubblicare un messaggio nello stesso modo in `test_topic` cui sopra eseguendo il comando seguente sul dispositivo IOT Edge da un altro terminale:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

dove `<edge_device_address>`  =  `localhost` in questo esempio il client viene eseguito nello stesso dispositivo come IOT Edge.

Eseguendo il comando, il **sub_client** client MQTT riceve il messaggio "Hello".

### <a name="symmetric-keys-authentication-with-tls"></a>Autenticazione con chiavi simmetriche con TLS

Per abilitare TLS, la porta deve essere modificata da 1883 (MQTT) a 8883 (MQTTS) e i client devono avere il certificato radice di MQTT Broker per poter convalidare la catena di certificati inviata dal broker di MQTT. Questa operazione può essere eseguita attenendosi alla procedura descritta nella sezione [connessione sicura (TLS)](#secure-connection-tls).

Poiché i client sono in esecuzione nello stesso dispositivo di MQTT Broker nell'esempio precedente, si applicano gli stessi passaggi per abilitare TLS semplicemente modificando il numero di porta da 1883 (MQTT) a 8883 (MQTTS).

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>Argomenti relativi alla pubblicazione e alla sottoscrizione degli hub Internet

Gli [SDK per dispositivi Azure](https://github.com/Azure/azure-iot-sdks) Internet è già consentito ai client di eseguire le operazioni dell'hub Internet, ma non consentono la pubblicazione o la sottoscrizione di argomenti definiti dall'utente. È possibile eseguire le operazioni dell'hub Internet con qualsiasi client MQTT usando la semantica di pubblicazione e sottoscrizione, purché vengano rispettati i protocolli primitivi dell'hub degli oggetti. Verranno esaminate le specifiche per comprendere il funzionamento di questi protocolli.

### <a name="send-telemetry-data-to-iot-hub"></a>Inviare dati di telemetria all'hub IoT

L'invio di dati di telemetria all'hub Internet è simile alla pubblicazione in un argomento definito dall'utente, ma usando un argomento specifico dell'hub degli argomenti:

- Per un dispositivo, i dati di telemetria vengono inviati in un argomento: `devices/<device_name>/messages/events`
- Per un modulo, i dati di telemetria vengono inviati in un argomento: `devices/<device_name>/<module_name>/messages/events`

Inoltre, creare una route, ad esempio, `FROM /messages/* INTO $upstream` per inviare dati di telemetria dal IOT Edge broker MQTT all'hub Internet. Per altre informazioni sul routing, vedere [dichiarare le route](module-composition.md#declare-routes).

### <a name="get-twin"></a>Ottenere un dispositivo gemello

Il recupero del dispositivo/modulo gemello non è un modello MQTT tipico. Il client deve inviare una richiesta per il dispositivo gemello che verrà utilizzato dall'hub.

Per ricevere i dispositivi gemelli, il client deve sottoscrivere un argomento specifico dell'hub Internet `$iothub/twin/res/#` . Questo nome di argomento viene ereditato dall'hub Internet e tutti i client devono sottoscrivere lo stesso argomento. Non significa che i dispositivi o i moduli ricevano il gemello tra loro. Hub e hub IoT Edge sa quale gemello deve essere recapitato dove, anche se tutti i dispositivi sono in ascolto dello stesso nome di argomento. 

Una volta effettuata la sottoscrizione, il client deve chiedere il gemello pubblicando un messaggio in un argomento specifico dell'hub Internet, `$iothub/twin/GET/?rid=<request_id>/#` dove  `<request_id>` è un identificatore arbitrario. L'hub Internet delle cose invierà quindi la risposta con i dati richiesti nell'argomento `$iothub/twin/res/200/?rid=<request_id>` , a cui il client sottoscrive. Questo è il modo in cui un client può associare le richieste alle risposte.

### <a name="receive-twin-patches"></a>Ricevere patch per i dispositivi gemelli

Per ricevere le patch gemelle, un client deve sottoscrivere un argomento IoTHub speciale `$iothub/twin/PATCH/properties/desired/#` . Una volta effettuata la sottoscrizione, il client riceve le patch gemelle inviate dall'hub Internet in questo argomento. 

### <a name="receive-direct-methods"></a>Ricezione di metodi diretti

La ricezione di un metodo diretto è molto simile alla ricezione di dispositivi gemelli completi con l'aggiunta che il client deve confermare che ha ricevuto la chiamata. Per prima cosa, il client sottoscrive l'argomento speciale dell'hub Internet `$iothub/methods/POST/#` . Quindi, una volta ricevuto un metodo diretto in questo argomento, il client deve estrarre l'identificatore della richiesta `rid` dall'argomento secondario in cui viene ricevuto il metodo diretto e pubblicare infine un messaggio di conferma nell'argomento speciale dell'hub Internet `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Inviare metodi diretti

L'invio di un metodo diretto è una chiamata HTTP e pertanto non passa attraverso MQTT Broker. Per inviare un metodo diretto all'hub Internet, vedere [comprendere e richiamare metodi diretti](../iot-hub/iot-hub-devguide-direct-methods.md). Per inviare un metodo diretto localmente a un altro modulo, vedere questo [esempio di chiamata al metodo diretto di Azure per C# SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>Pubblicare e sottoscrivere i broker MQTT

Per connettere due broker MQTT, l'hub IoT Edge include un Bridge MQTT. Un Bridge MQTT viene comunemente usato per connettere un broker MQTT in esecuzione a un altro broker MQTT. Viene in genere eseguito il push di un solo subset del traffico locale a un altro broker.

> [!NOTE]
> Il Bridge Hub IoT Edge attualmente può essere usato solo tra dispositivi IoT Edge annidati. Non può essere usato per inviare dati all'hub delle cose, poiché l'hub Internet non è un broker MQTT completo. Per altre informazioni sul supporto delle funzionalità di broker MQTT per l'hub Internet, vedere [comunicare con l'hub Internet delle cose usando il protocollo MQTT](../iot-hub/iot-hub-mqtt-support.md). Per altre informazioni sull'annidamento di IoT Edge dispositivi, vedere [connettere un dispositivo IOT Edge downstream a un gateway di Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices) 

In una configurazione annidata, il Bridge MQTT dell'hub IoT Edge funge da client del broker MQTT padre, quindi è necessario impostare le regole di autorizzazione per il EdgeHub padre per consentire al EdgeHub figlio di pubblicare e sottoscrivere argomenti specifici definiti dall'utente per cui è configurato il Bridge.

Il IoT Edge Bridge MQTT viene configurato tramite una struttura JSON che viene inviata all'hub IoT Edge tramite il gemello. Modificare un dispositivo gemello dell'hub IoT Edge per configurare il Bridge MQTT.

> [!NOTE]
> Per l'anteprima pubblica, la configurazione del Bridge MQTT è disponibile solo tramite Visual Studio, Visual Studio Code o l'interfaccia della riga di comando di Azure. Il portale di Azure attualmente non supporta la modifica del dispositivo gemello dell'hub IoT Edge e della relativa configurazione MQTT Bridge.

Il Bridge MQTT può essere configurato per connettere un broker MQTT di IoT Edge Hub a più broker esterni. Per ogni broker esterno sono necessarie le impostazioni seguenti:

- `endpoint` è l'indirizzo di Remote MQTT broker a cui connettersi. Sono attualmente supportati solo i dispositivi IoT Edge padre e sono definiti dalla variabile `$upstream` .
- `settings` definisce gli argomenti per il Bridge per un endpoint. Per la configurazione possono essere presenti più impostazioni per ogni endpoint e vengono usati i valori seguenti:
    - `direction`: `in` per sottoscrivere gli argomenti del broker remoto o `out` per pubblicare negli argomenti del broker remoto
    - `topic`: modello di argomento principale per cui trovare una corrispondenza. Per definire questo modello, è possibile usare i [caratteri jolly MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) . È possibile applicare prefissi diversi a questo modello di argomento sul broker locale e sul broker remoto.
    - `outPrefix`: Prefisso applicato a al `topic` modello sul broker remoto.
    - `inPrefix`: Prefisso applicato a al `topic` modello sul broker locale.

Di seguito è riportato un esempio di una configurazione IoT Edge Bridge MQTT che consente di ripubblicare tutti i messaggi ricevuti sugli argomenti `alerts/#` di un dispositivo IOT Edge padre in un dispositivo figlio IOT Edge sugli stessi argomenti e di ripubblicare tutti i messaggi inviati sugli argomenti `/local/telemetry/#` di un dispositivo di IOT Edge figlio in un dispositivo IOT Edge padre su argomenti `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "",
                    "inPrefix": "/local/telemetry",
                    "outPrefix": "/remote/messages"
                }
            ]
        }]
    }
}
```
Altre note sul Bridge MQTT Hub IoT Edge:
- Il protocollo MQTT verrà usato automaticamente come protocollo upstream quando viene usato il broker MQTT e IoT Edge viene usato in una configurazione annidata, ad esempio con un `parent_hostname` specificato. Per altre informazioni sui protocolli upstream, vedere [comunicazione cloud](iot-edge-runtime.md#cloud-communication). Per altre informazioni sulle configurazioni annidate, vedere [connettere un dispositivo IOT Edge downstream a un gateway di Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sull'hub IoT Edge](iot-edge-runtime.md#iot-edge-hub)

---
title: Protocollo di estensione gRPC - Azure
description: Questo articolo illustra come usare il protocollo di estensione gRPC per inviare messaggi tra il modulo Analisi video live e un'estensione personalizzata di intelligenza artificiale o visione artificiale.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 288dcd1a11c7c42d8796d3b17f2bfd56f562aaf1
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89448071"
---
# <a name="grpc-extension-protocol"></a>Protocollo di estensione gRPC

Questo articolo illustra come usare il protocollo di estensione gRPC per inviare messaggi tra il modulo Analisi video live e un'estensione personalizzata di intelligenza artificiale o visione artificiale.

gRPC è un framework RPC moderno, open source e a prestazioni elevate, eseguibile in qualsiasi ambiente. Il servizio di trasporto gRPC usa lo streaming bidirezionale HTTP/2 tra:

* Client gRPC (Analisi video live nel modulo di IoT Edge) e 
* Server gRPC (estensione personalizzata)

Una sessione gRPC è una singola connessione dal client gRPC al server gRPC sulla porta TCP/TLS. 

In una singola sessione: il client invia un descrittore di flusso multimediale seguito da fotogrammi video al server come messaggio [protobuf](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc) nella sessione di streaming gRPC. Il server convalida il descrittore di flussi, analizza il fotogramma video e restituisce i risultati delle inferenze come messaggio protobuf.

![Contratto dell'estensione gRPC](./media/grpc-extension-protocol/grpc.png)

## <a name="implementing-grpc-protocol"></a>Implementazione del protocollo gRPC

### <a name="creating-a-grpc-connection"></a>Creazione di una connessione gRPC

L'estensione personalizzata deve implementare il servizio gRPC seguente:

```
service MediaGraphExtension {
  rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
}
```

Dopo la chiamata, verrà aperto un flusso bidirezionale per la trasmissione dei messaggi tra l'estensione gRPC e il grafico di Analisi video live. Il primo messaggio inviato in questo flusso da ogni parte conterrà un valore MediaStreamDescriptor, che definisce le informazioni che verranno inviate nei MediaSamples seguenti.

È ad esempio possibile che l'estensione del grafico invii il messaggio, espresso qui in JSON, per indicare che invierà fotogrammi con codifica 416x416 rgb24 incorporati nei messaggi gRPC all'estensione personalizzata.

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": {
        "graph_identifier": {
            "media_services_arm_id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/microsoft.media/mediaservices/mediaAccountName",
            "graph_instance_name": "mediaGraphName",
            "graph_node_name": "grpcExtension"
        },
        "media_descriptor": {
            "timescale": 90000,
            "video_frame_sample_format": {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

L'estensione personalizzata invierà quindi come risposta il messaggio seguente, per indicare che restituisce solo inferenze.

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": {
        "extension_identifier": "customExtensionName"    }
}
```

Dopo che entrambe le parti hanno scambiato descrittori multimediali, Analisi video live inizierà la trasmissione di fotogrammi all'estensione.

### <a name="sequence-numbers"></a>Numeri di sequenza

Il nodo dell'estensione gRPC e l'estensione personalizzata mantengono un set separato di numeri di sequenza, che vengono assegnati ai rispettivi messaggi. Questi numeri di sequenza devono aumentare in maniera monotona a partire da 1. È possibile ignorare`ack_sequence_number` se non viene confermato alcun messaggio, come accade ad esempio quando viene inviato il primo messaggio.

È necessario confermare una richiesta dopo che il messaggio corrispondente è stato elaborato completamente. Nel caso di un trasferimento di memoria condivisa, questa conferma indica che il mittente può liberare la memoria condivisa e che il ricevitore non eseguirà più l'accesso a tale memoria. Il mittente deve conservare la memoria condivisa fino alla conferma.

### <a name="reading-embedded-content"></a>Lettura del contenuto incorporato

Il contenuto incorporato può essere letto direttamente dal messaggio `MediaSample` fino al campo `content_byte`. I dati verranno codificati in base a `MediaDescriptor`.

### <a name="reading-content-from-shared-memory"></a>Lettura di contenuto dalla memoria condivisa

Durante il trasferimento di contenuto tramite la memoria condivisa, il mittente includerà `SharedMemoryBufferTransferProperties` in `MediaStreamDescriptor` quando viene stabilita una sessione per la prima volta. L'operazione può avere un aspetto analogo al seguente (espresso in JSON):

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

Il ricevitore apre quindi il file `/dev/shm/inference_client_share_memory_2146989006636459346`. Il mittente invierà un messaggio `MediaSample`, che contiene `ContentReference` che fa riferimento a una posizione specifica in questo file.

```
{
    "timestamp": 143598615750000,
    "content_reference": {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

Il ricevitore legge quindi i dati da questa posizione nel file.

Per consentire al contenitore di Analisi video live di comunicare sulla memoria condivisa, è necessario configurare correttamente la modalità IPC del contenitore. Questa operazione può essere eseguita in molti modi, ma di seguito sono riportate alcune configurazioni consigliate.

* Quando si comunica con un motore di inferenza gRPC in esecuzione nel dispositivo host, è necessario impostare la modalità IPC su host.
* Quando si comunica con un server gRPC in esecuzione in un altro modulo di IoT Edge, è necessario impostare la modalità IPC su condivisibile per il modulo di Analisi video live e su `container:liveVideoAnalytics` per l'estensione personalizzata, dove `liveVideoAnalytics` è il nome del modulo di Analisi video live.

Di seguito è riportato l'aspetto di questo approccio in un dispositivo gemello con la prima opzione indicata.

```
"liveVideoAnalytics": {
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": {
    "image": "mcr.microsoft.com/media/live-video-analytics:1",
    "createOptions": 
      "HostConfig": {
        "IpcMode": "host"
      }
    }
  }
}
```

Per altre informazioni sulle modalità IPC, vedere https://docs.docker.com/engine/reference/run/#ipc-settings---ipc.

## <a name="media-graph-grpc-extension-contract-definitions"></a>Definizioni del contratto dell'estensione gRPC per grafico multimediale

Questa sezione definisce il contratto gRPC che definisce il flusso di dati.

### <a name="protocol-messages"></a>Messaggi del protocollo

![Messaggi del protocollo](./media/grpc-extension-protocol/grpc2.png)
 
### <a name="client-authentication"></a>Autenticazione client

Gli implementatori delle estensioni personalizzate possono convalidare l'autenticità delle connessioni gRPC in ingresso per assicurarsi che provengano dal nodo dell'estensione gRPC. Il nodo fornirà una voce nelle intestazioni della richiesta in base a cui eseguire la convalida.

È possibile usare le credenziali nome utente/password per ottenere questo risultato. Durante la creazione di un nodo dell'estensione gRPC, vengono fornite credenziali analoghe alle seguenti:

```
{
  "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "tcp://customExtension:8081",
    "credentials": {
      "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

Quando la richiesta gRPC viene inviata, l'intestazione seguente verrà inclusa nei metadati della richiesta, simulando l'autenticazione HTTP di base.

`x-ms-authentication: Basic (Base64 Encoded username:password)`

## <a name="using-grpc-over-tls"></a>Uso di gRPC su TLS

È possibile proteggere su TLS una connessione gRPC usata per le inferenze. Questo approccio risulta utile nelle situazioni in cui la sicurezza della rete tra Analisi video live e il motore di inferenze non può essere garantita. TLS crittograferà eventuali contenuti incorporati nei messaggi gRPC, provocando un sovraccarico aggiuntivo per la CPU durante la trasmissione di fotogrammi a una frequenza elevata.

Le opzioni di verifica IgnoreHostname e IgnoreSignature non sono supportate da gRPC, quindi il certificato del server presentato dal motore di inferenze deve contenere un valore CN che corrisponde esattamente all'indirizzo IP/nome host nell'URL dell'endpoint del nodo dell'estensione gRPC.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sullo schema dei metadati dell'inferenza](inference-metadata-schema.md)

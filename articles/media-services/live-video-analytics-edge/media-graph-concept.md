---
title: Concetto di grafico multimediale-Azure
description: Un grafico multimediale consente di definire la posizione in cui devono essere acquisiti i supporti, la modalità di elaborazione e la posizione in cui devono essere recapitati i risultati. Questo articolo fornisce una descrizione dettagliata del concetto di grafico multimediale.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8c6775da6804b5079c89cae73d4621dd8067e90a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798840"
---
# <a name="media-graph"></a>Grafico multimediale

## <a name="suggested-pre-reading"></a>Letture consigliate

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)

## <a name="overview"></a>Panoramica

Un grafico multimediale consente di definire la posizione in cui devono essere acquisiti i supporti, la modalità di elaborazione e la posizione in cui devono essere recapitati i risultati. A tale scopo, è necessario connettere componenti, o nodi, nel modo desiderato. Il diagramma seguente fornisce una rappresentazione grafica di un grafico multimediale.  

![Rappresentazione grafica di un grafico multimediale](./media/media-graph/overview.png)

Analisi video in tempo reale su IoT Edge supporta diversi tipi di origini, processori e sink.

* I **nodi di origine** consentono l'acquisizione di contenuti multimediali nel grafico multimediale. Il supporto in questo contesto, concettualmente, potrebbe essere un flusso audio, un flusso video, un flusso di dati o un flusso con dati audio, video e/o combinati in un singolo flusso.
* I **nodi del processore** consentono l'elaborazione di supporti all'interno del grafico multimediale.
* I **nodi sink** consentono di recapito i risultati dell'elaborazione ai servizi e alle app all'esterno del grafico multimediale.

## <a name="media-graph-topologies-and-instances"></a>Topologie e istanze del grafico multimediale 

Analisi video in tempo reale su IoT Edge consente di gestire i grafici multimediali tramite due concetti, ovvero "topologia del grafo" e "istanza del grafo". Una topologia Graph consente di definire il progetto di un grafico, con parametri come segnaposto per i valori. La topologia definisce quali nodi vengono utilizzati nel grafico multimediale e come sono connessi all'interno del grafico multimediale. Se ad esempio si vuole registrare il feed da una fotocamera, è necessario un grafo con un nodo di origine che riceve video e un nodo sink che scrive il video.

I valori per i parametri nella topologia vengono specificati quando si creano istanze di Graph che fanno riferimento alla topologia. In questo modo è possibile creare più istanze che fanno riferimento alla stessa topologia, ma con valori diversi per i parametri specificati nella topologia. Nell'esempio precedente, è possibile usare i parametri per rappresentare l'indirizzo IP della fotocamera e il nome del video registrato. È possibile creare molte istanze del grafo con tale topologia, una per ogni fotocamera di un edificio, ad esempio, ciascuna con un indirizzo IP specifico e un nome specifico.

## <a name="media-graph-states"></a>Stati del grafico multimediale  

Un grafico multimediale può trovarsi in uno degli Stati seguenti:

* Inattivo: rappresenta lo stato in cui un grafico multimediale è configurato ma non attivo.
* Attivazione: stato in cui viene creata un'istanza di un grafico multimediale, ovvero lo stato di transizione tra inattivo e attivo.
* Attivo: stato quando un grafico multimediale è attivo. 

    > [!NOTE]
    >  Il grafico multimediale può essere attivo senza il flusso di dati (ad esempio, l'origine video di input passa alla modalità offline).
* Disattivazione: questo è lo stato di transizione di un grafico multimediale da attivo a inattivo.

Il diagramma seguente illustra la macchina a Stati del grafico multimediale.

![Macchina a Stati del grafico multimediale](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Origini, processori e sink  

Analisi video in tempo reale su IoT Edge supporta i tipi di nodi seguenti all'interno di un grafico multimediale:

### <a name="sources"></a>Origini 

#### <a name="rtsp-source"></a>Origine RTSP 

Un nodo di origine RTSP consente di inserire supporti da un [RTSP] ( https://tools.ietf.org/html/rfc2326 Server. Le telecamere di sorveglianza e basate su IP trasmettono i dati in un protocollo denominato RTSP (Real-Time-Streaming-Protocol), che è diverso rispetto ad altri tipi di dispositivi, ad esempio telefoni e videocamere. Questo protocollo viene usato per stabilire e controllare le sessioni multimediali tra un server (la fotocamera) e un client. Il nodo di origine RTSP in un grafico multimediale funge da client ed è in grado di stabilire una sessione con un server RTSP. Molti dispositivi come la maggior parte delle [fotocamere IP](https://en.wikipedia.org/wiki/IP_camera) hanno un server RTSP incorporato. [ONVIF](https://www.onvif.org/) impone a RTSP di essere supportato nella definizione dei [profili G, S & i](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) dispositivi conformi. Il nodo di origine RTSP richiede di specificare un URL RTSP, insieme alle credenziali per abilitare una connessione autenticata.

#### <a name="iot-hub-message-source"></a>Origine messaggio dell'hub Internet 

Analogamente ad altri [moduli IOT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-module), analisi video in tempo reale sul modulo IOT Edge può ricevere messaggi tramite l' [Hub IOT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Questi messaggi possono essere inviati da altri moduli o da app in esecuzione sul dispositivo perimetrale o dal cloud. Questi messaggi vengono recapitati (indirizzati) a un [input denominato](../../iot-edge/module-composition.md#sink) nel modulo. Un nodo di origine del messaggio dell'hub Internet consente a tali messaggi di raggiungere un grafico multimediale. Questi messaggi o segnali possono quindi essere usati internamente nel grafico multimediale, in genere per attivare i cancelli dei segnali (vedere le [porte del segnale](#signal-gate-processor) di seguito). 

Ad esempio, è possibile avere un modulo IoT Edge che genera un messaggio quando viene aperta una porta. Il messaggio da tale modulo può essere instradato a IoT Edge Hub, da dove può essere indirizzato all'origine dei messaggi dell'hub Internet di un grafico multimediale. All'interno del grafico multimediale, l'origine del messaggio dell'hub Internet è in grado di passare l'evento a un processore di Gate del segnale, che può quindi attivare la registrazione del video da un'origine RTSP in un file. 

### <a name="processors"></a>Processori  

#### <a name="motion-detection-processor"></a>Processore rilevamento movimento 

Il nodo processore rilevamento movimento consente di rilevare il movimento in video live. Esamina i fotogrammi video in ingresso e determina se è presente un movimento nel video. Se viene rilevato un movimento, questo passa sui fotogrammi video al componente downstream e genera un evento. Il nodo del processore di rilevamento del movimento (insieme ad altri nodi) può essere usato per attivare la registrazione del video in arrivo quando viene rilevato un movimento.

#### <a name="frame-rate-filter-processor"></a>Processore filtro frequenza frame  

Il nodo processore filtro frequenza frame consente di campionare i frame dal flusso video in ingresso a una frequenza specificata. In questo modo è possibile ridurre il numero di frame inviati ai componenti del flusso inferiore, ad esempio il nodo del processore di estensione HTTP, per un'ulteriore elaborazione.

#### <a name="http-extension-processor"></a>Processore di estensione HTTP

Il nodo processore di estensione HTTP consente di connettere il proprio modulo di IoT Edge a un grafico multimediale. Questo nodo accetta i fotogrammi video decodificati come input e inoltra tali frame a un endpoint HTTP REST esposto dal modulo. Questo nodo è in grado di eseguire l'autenticazione con l'endpoint REST, se necessario. Inoltre, il nodo dispone di un formattatore di immagine incorporato per la scalabilità e la codifica dei fotogrammi video prima che vengano inoltrati all'endpoint REST. Scaler dispone di opzioni per le proporzioni dell'immagine da mantenere, imbottire o allungare. Il codificatore di immagini supporta formati JPEG, PNG o BMP.

#### <a name="grpc-extension-processor"></a>processore di estensione gRPC

Il nodo del processore di estensione gRPC accetta i fotogrammi video decodificati come input e inoltra tali frame a un endpoint [gRPC](terminology.md#grpc) esposto dal modulo. Inoltre, il nodo dispone di un formattatore di immagine incorporato per la scalabilità e la codifica dei fotogrammi video prima che vengano inoltrati all'endpoint gRPC. Scaler dispone di opzioni per le proporzioni dell'immagine da mantenere, imbottire o allungare. Il codificatore di immagini supporta formati JPEG, PNG o BMP.

#### <a name="signal-gate-processor"></a>Processore Gate Signal  

Il nodo processore del Gate del segnale consente di eseguire in modo condizionale il supporto da un nodo a un altro. Funge anche da buffer, consentendo la sincronizzazione di supporti ed eventi. Un caso di utilizzo tipico consiste nell'inserire un nodo del processore di controllo del segnale tra il nodo di origine RTSP e il nodo sink di asset e l'output di un nodo processore del rilevamento di movimento per attivare il controllo. Con un grafo multimediale di questo tipo, si dovrebbe registrare il video solo quando viene rilevato un movimento.

### <a name="sinks"></a>Sink  

#### <a name="asset-sink"></a>Sink di asset  

Un nodo di sink di asset consente di scrivere dati multimediali (video e/o audio) in un asset di servizi multimediali di Azure. In un grafico multimediale può essere presente un solo nodo di sink di asset. Vedere la sezione [Asset](terminology.md#asset) per ulteriori informazioni sugli asset e il relativo ruolo nella registrazione e nella riproduzione dei supporti. Per informazioni dettagliate sul modo in cui vengono usate le proprietà di questo nodo, vedere l'articolo relativo alla [registrazione video continua](continuous-video-recording-concept.md) .

#### <a name="file-sink"></a>Sink di file  

Il nodo sink di file consente di scrivere dati multimediali (video e/o audio) in una posizione nella file system locale del dispositivo IoT Edge. Può essere presente un solo nodo di sink di file in un grafico multimediale e deve essere a valle di un nodo del processore del Gate del segnale. In questo modo, la durata dei file di output viene limitata ai valori specificati nelle proprietà del nodo del processore del Gate del segnale.

#### <a name="iot-hub-message-sink"></a>Sink messaggi dell'hub Internet  

Un nodo del sink di messaggi dell'hub Internet consente di pubblicare eventi nell'hub IoT Edge. L'hub IoT Edge può quindi instradare i dati ad altri moduli o app sul dispositivo perimetrale o all'hub di tutto il cloud (per le route specificate nel manifesto di distribuzione). Il nodo del sink di messaggi dell'hub Internet può accettare eventi da processori upstream, ad esempio un nodo del processore di rilevamento del movimento, o da un servizio di inferenza esterno tramite un nodo processore di estensione HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Regole sull'uso dei nodi

Vedere [limitazioni sulle topologie dei grafi](quotas-limitations.md#limitations-on-graph-topologies-at-preview) per altre regole su come usare i diversi nodi in un grafico multimediale.

## <a name="scenarios"></a>Scenari

Utilizzando una combinazione di origini, processori e sink definiti in precedenza, è possibile creare grafici multimediali per un'ampia gamma di scenari che comportano l'analisi dei video in tempo reale. Scenari di esempio:

* [Registrazione continua di video](continuous-video-recording-concept.md)
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)
* [Live Video Analytics senza registrazione video](analyze-live-video-concept.md)

## <a name="next-steps"></a>Passaggi successivi

Per vedere come è possibile eseguire il rilevamento del movimento su un feed video live, vedere [Guida introduttiva: eseguire analisi video in tempo reale con il proprio modello](use-your-model-quickstart.md).

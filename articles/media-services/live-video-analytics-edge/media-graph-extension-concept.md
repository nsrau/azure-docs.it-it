---
title: Informazioni sull'estensione Grafico multimediale - Azure
description: Analisi video live su IoT Edge consente di estendere le funzionalità di elaborazione del grafico multimediale tramite un nodo di estensione del grafico.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 74929cc51a868d20952f1e25432f5343e4821d08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "89569331"
---
# <a name="media-graph-extension"></a>Estensione Grafico multimediale

Analisi video live su IoT Edge consente di estendere le funzionalità di elaborazione del grafico multimediale tramite un nodo di estensione del grafico. Il plug-in di estensione di analisi può usare le tecniche di elaborazione di immagini tradizionali o i modelli di intelligenza artificiale per visione artificiale. Le estensioni del grafico vengono abilitate includendo un nodo del processore di estensione in un grafico multimediale. Il nodo del processore di estensione inoltra i fotogrammi video all'endpoint configurato e funge da interfaccia per l'estensione. La connessione può essere effettuata a un endpoint locale o remoto e può essere protetta tramite l'autenticazione e la crittografia TLS, se necessario. Il nodo del processore di estensione del grafico consente inoltre la scalabilità e la codifica facoltative dei fotogrammi video prima che vengano inviati all'estensione personalizzata.

Analisi video live supporta due tipi di processori di estensione del grafico multimediale:

* [Processore di estensione HTTP](media-graph-concept.md#http-extension-processor)
* [Processore di estensione gRPC](media-graph-concept.md#grpc-extension-processor)

## <a name="http-extension-processor"></a>Processore di estensione HTTP

Il processore di estensione HTTP consente scenari di estendibilità con il protocollo HTTP, in cui le prestazioni e/o l'utilizzo ottimale delle risorse non sono prioritari. È possibile esporre l'intelligenza artificiale a un grafico multimediale tramite un endpoint REST HTTP. 

Usare il nodo del processore di estensione HTTP quando:

* Si vuole una migliore interoperabilità con i sistemi di inferenza HTTP esistenti.
* Il trasferimento dei dati a prestazioni ridotte è accettabile.
* Si vuole usare una semplice interfaccia richiesta-risposta per Analisi video live.

## <a name="grpc-extension-processor"></a>Processore di estensione gRPC

Il processore di estensione gRPC consente scenari di estendibilità con un protocollo strutturato a prestazioni elevate basato su gRPC. È ideale per scenari in cui le prestazioni e/o l'utilizzo ottimale delle risorse sono una priorità. Il processore di estensione gRPC consente di ottenere tutti i vantaggi delle definizioni dei dati strutturati. gRPC offre prestazioni di trasferimento di contenuto elevate usando:

* [Memoria condivisa predefinita](https://en.wikipedia.org/wiki/Shared_memory) o 
* Incorporamento diretto del contenuto nel corpo dei messaggi gRPC 

Il processore di estensione gRPC può essere usato per inviare le proprietà dei file multimediali insieme allo scambio di messaggi di inferenza.
Usare quindi un nodo del processore di estensione gRPC quando:

* Si vuole usare un contratto strutturato, ad esempio, i messaggi strutturati per le richieste e le risposte
* Si vogliono usare i buffer del protocollo ([protobuf](https://developers.google.com/protocol-buffers)) come formato di interscambio dei messaggi sottostanti per la comunicazione
* Si vuole comunicare con un server gRPC in una sessione a flusso singolo invece che nel modello tradizionale di richiesta-risposta che necessita di un gestore di richieste personalizzato per analizzare le richieste in ingresso e chiamare le funzioni di implementazione corrette. 
* Si vuole ottenere una bassa latenza e una comunicazione con velocità effettiva elevata tra Analisi video live e il modulo.

## <a name="use-your-inferencing-model-with-live-video-analytics"></a>Usare il modello di inferenza con Analisi video live

Le estensioni del grafico multimediale consentono di eseguire modelli di inferenza scelti in qualsiasi runtime di inferenza disponibile, ad esempio ONNX, TensorFlow, PyTorch o altri, nel proprio contenitore Docker. Per ottenere prestazioni ottimali, è necessario distribuire l'estensione personalizzata di inferenza insieme al modulo Edge di Analisi video live e quindi richiamarlo tramite il processore di estensione HTTP o il processore di estensione gRPC incluso nella topologia del grafico. La frequenza delle chiamate nell'estensione personalizzata può essere inoltre limitata aggiungendo facoltativamente un [processore di rilevamento dei movimenti](media-graph-concept.md#motion-detection-processor) e un [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor) upstream al processore dell'estensione multimediale.

Il diagramma seguente illustra il flusso di dati generale:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph-extension/analyze-live-video-with-AI-inference-service.svg" alt-text="Servizio di inferenza di intelligenza artificiale":::

## <a name="samples"></a>Esempi

Vedere alcuni esempi di [notebook Jupyter](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/readme.md) per Analisi video live. Questi notebook forniranno istruzioni dettagliate per le **estensioni del grafo multimediale** in:

* Come creare un'immagine del contenitore Docker di un servizio di estensione
* Come distribuire il servizio di estensione come contenitore insieme al contenitore di Analisi video live
* Come usare un grafo multimediale di Analisi video live con un client di estensione e come puntarlo all'endpoint di estensione (HTTP/gRPC)
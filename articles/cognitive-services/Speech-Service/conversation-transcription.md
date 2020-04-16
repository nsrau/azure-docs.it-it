---
title: Trascrizione conversazione (anteprima) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La trascrizione della conversazione è una soluzione per le riunioni, che combina riconoscimento, ID altoparlante e diarizzazione per fornire la trascrizione di qualsiasi conversazione.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402503"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Che cos'è la trascrizione conversazione nelle riunioni (anteprima)?

La trascrizione della conversazione è una soluzione di [sintesi](speech-to-text.md) vocale che combina il riconoscimento vocale, l'identificazione dell'oratore e l'attribuzione delle frasi a ciascun oratore (nota anche come _diarizzazione)_ per fornire la trascrizione in tempo reale e/o asincrona di qualsiasi conversazione. La trascrizione della conversazione distingue gli altoparlanti in una conversazione per determinare chi ha detto cosa e quando e rende facile per gli sviluppatori aggiungere parole di sintesi vocale alle applicazioni che eseguono la diarizzazione su più altoparlanti.

## <a name="key-features"></a>Funzionalità principali

- **Timestamp:** ogni espressione dell'altoparlante ha un timestamp, in modo che sia possibile trovare facilmente quando è stata detta una frase.
- **Trascrizioni leggibili** - le trascrizioni hanno formattazione e punteggiatura aggiunte automaticamente per garantire che il testo corrisponda strettamente a ciò che veniva detto.
- **Profili utente:** i profili utente vengono generati raccogliendo campioni vocali utente e inviandoli alla generazione della firma.
- **Identificazione degli altoparlanti:** gli oratori vengono identificati utilizzando i profili utente e a ciascuno viene assegnato un identificatore di _altoparlante._
- **Diarizzazione multi-altoparlante** - determinare chi ha detto cosa sintetizzando il flusso audio con ogni identificatore altoparlante.
- **Trascrizione** in tempo reale – fornire trascrizioni in tempo reale di chi sta dicendo cosa e quando la conversazione sta accadendo.
- **trascrizione asincrona** – fornire trascrizioni con maggiore precisione utilizzando un flusso audio multicanale.

> [!NOTE]
> Anche se la trascrizione della conversazione non pone un limite al numero di altoparlanti nella stanza, è ottimizzata per 2-10 altoparlanti per sessione.

## <a name="use-cases"></a>Casi d'uso

### <a name="inclusive-meetings"></a>Riunioni inclusive

Per rendere le riunioni inclusive per tutti, come i partecipanti sordi e con problemi di udito, è importante avere la trascrizione in tempo reale. La trascrizione della conversazione in modalità in tempo reale prende l'audio della riunione e determina chi sta dicendo cosa, consentendo a tutti i partecipanti alla riunione di seguire la trascrizione e partecipare alla riunione senza ritardi.

### <a name="improved-efficiency"></a>Miglioramento dell'efficienza

I partecipanti alla riunione possono concentrarsi sulla riunione e lasciare la notazione alla trascrizione della conversazione. I partecipanti possono partecipare attivamente alla riunione e seguire rapidamente i passaggi successivi, utilizzando la trascrizione invece di prendere appunti e potenzialmente mancare qualcosa durante la riunione.

## <a name="how-it-works"></a>Funzionamento

Questa è una panoramica di alto livello di come funziona la trascrizione delle conversazioni.

![Diagramma di trascrizione dell'importazione delle conversazioni](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Ingressi previsti

- **Flusso audio multicanale:** per informazioni sulle specifiche e sulla progettazione, vedere [Microfono Microsoft Speech Device SDK](https://aka.ms/cts/microphone). Per altre informazioni o per acquistare un kit di sviluppo, vedere [Get Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Esempi vocali utente:** la trascrizione delle conversazioni richiede profili utente prima della conversazione. Sarà necessario raccogliere le registrazioni audio da ogni utente, quindi inviare le registrazioni al servizio di [generazione della firma](https://aka.ms/cts/signaturegenservice) per convalidare l'audio e generare profili utente.

## <a name="real-time-vs-asynchronous"></a>In tempo reale e asincrono

La trascrizione della conversazione offre tre modalità di trascrizione:

### <a name="real-time"></a>Tempo reale

I dati audio vengono elaborati in tempo reale per restituire l'identificatore dell'altoparlante, ovvero la trascrizione. Selezionare questa modalità se il requisito della soluzione di trascrizione consiste nel fornire ai partecipanti alla conversazione una visualizzazione in tempo reale della conversazione in corso. Ad esempio, la creazione di un'applicazione per rendere le riunioni più accessibili ai partecipanti non udenti e con problemi di udito è un caso d'uso ideale per la trascrizione in tempo reale.

### <a name="asynchronous"></a>Asincrona

I dati audio vengono elaborati in batch per restituire l'identificatore e la trascrizione dell'altoparlante. Selezionare questa modalità se la soluzione di trascrizione è necessario fornire una maggiore precisione senza visualizzazione trascrizione in tempo reale. Ad esempio, se si desidera creare un'applicazione per consentire ai partecipanti alla riunione di recuperare facilmente le riunioni perse, utilizzare la modalità di trascrizione asincrona per ottenere risultati di trascrizione ad alta precisione.

### <a name="real-time-plus-asynchronous"></a>In tempo reale più asincrono

I dati audio vengono elaborati in tempo reale per restituire l'identificatore dell'altoparlante, la trascrizione, e, inoltre, viene creata una richiesta per ottenere anche una trascrizione ad alta precisione tramite l'elaborazione asincrona. Selezionare questa modalità se l'applicazione ha bisogno di trascrizione in tempo reale, ma richiede anche una trascrizione di precisione più elevata da utilizzare dopo la conversazione o la riunione.

## <a name="language-support"></a>Lingue supportate

Attualmente, Conversation Transcription supporta "en-US" e "zh-CN" nelle seguenti regioni: *centralus* e *eastasia*. Se è necessario un ulteriore supporto delle impostazioni locali, contattare la funzionalità di [trascrizione conversazionale](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trascrivere conversazioni in tempo reale](how-to-use-conversation-transcription-service.md)

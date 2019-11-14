---
title: Che cos'è la trascrizione delle conversazioni (anteprima)-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La trascrizione delle conversazioni è una soluzione di sintesi vocale che combina il riconoscimento vocale, l'identificazione del relatore e l'attribuzione di frasi a ogni altoparlante (anche noto come "diare") per fornire la trascrizione in tempo reale e/o asincrona di qualsiasi conversazione.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 0c15b053028bd707159a632c98faaea8b9954a9b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075840"
---
# <a name="what-is-conversation-transcription-preview"></a>Che cos'è la trascrizione delle conversazioni (anteprima)?

La trascrizione delle conversazioni è una soluzione di [sintesi vocale](speech-to-text.md) che combina il riconoscimento vocale, l'identificazione del relatore e l'attribuzione di frasi a ogni altoparlante (anche noto come " _diare_") per fornire in tempo reale e/o asincrono trascrizione di qualsiasi conversazione. La trascrizione delle conversazioni distingue gli speaker in una conversazione per determinare chi ha detto cosa e quando e rende più semplice per gli sviluppatori l'aggiunta di riconoscimento vocale alle applicazioni che eseguono la messa in funzione del MultiSpeaker.

## <a name="key-features"></a>Funzionalità principali

- **Timestamp** : ogni espressione speaker ha un timestamp, in modo che sia possibile trovare facilmente il momento in cui è stata pronunciata una frase.
- **Trascrizioni leggibili** : la formattazione e la punteggiatura sono state aggiunte automaticamente per garantire che il testo corrisponda esattamente a quanto detto.
- **Profili utente** : i profili utente vengono generati raccogliendo esempi vocali dell'utente e inviando tali profili alla generazione della firma.
- **Identificazione altoparlante** : gli altoparlanti vengono identificati usando i profili utente e viene assegnato un _identificatore altoparlante_ a ognuno di essi.
- Messa in **funzione del MultiSpeaker** : determinare chi ha detto cosa sintetizzando il flusso audio con ogni identificatore del altoparlante.
- **Trascrizione in tempo reale** : fornisce trascrizioni Live di chi sta dicendo cosa e quando si verifica la conversazione.
- **trascrizione asincrona** : fornisce le trascrizioni con maggiore precisione usando un flusso audio multicanale.

> [!NOTE]
> Anche se la trascrizione delle conversazioni non impone un limite al numero di altoparlanti presenti nella chat, è ottimizzato per 2-10 altoparlanti per sessione.

## <a name="use-cases"></a>Casi d'uso

### <a name="inclusive-meetings"></a>Riunioni Inclusive

Per rendere le riunioni inclusive per tutti gli utenti, ad esempio i partecipanti che sono sordi e difficili da ascoltare, è importante avere una trascrizione in tempo reale. La trascrizione delle conversazioni in modalità in tempo reale prende la riunione audio e determina chi sta dicendo, consentendo a tutti i partecipanti della riunione di seguire la trascrizione e partecipare alla riunione senza un ritardo.

### <a name="improved-efficiency"></a>Efficienza migliorata

I partecipanti alla riunione possono concentrarsi sulla riunione e lasciare la trascrizione delle conversazioni. I partecipanti possono partecipare attivamente alla riunione e proseguire rapidamente con i passaggi successivi, usando la trascrizione anziché prendere nota e potenzialmente mancare qualcosa durante la riunione.

## <a name="how-it-works"></a>Funzionamento

Si tratta di una panoramica di alto livello del funzionamento della trascrizione delle conversazioni.

![Diagramma di trascrizione della conversazione di importazione](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Input previsti

- **Flusso audio multicanale** : per informazioni dettagliate e di progettazione, vedere [microfono per dispositivi Microsoft Speech SDK](https://aka.ms/cts/microphone). Per ulteriori informazioni o per acquistare un kit di sviluppo, vedere la pagina relativa a come [ottenere Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Esempi Voice utente** : la trascrizione delle conversazioni richiede i profili utente prima della conversazione. È necessario raccogliere le registrazioni audio da ogni utente, quindi inviare le registrazioni al servizio di [generazione della firma](https://aka.ms/cts/signaturegenservice) per convalidare l'audio e generare i profili utente.

## <a name="real-time-vs-asynchronous"></a>Confronto tra tempo reale e asincrono

La trascrizione delle conversazioni offre tre modalità di trascrizione:

### <a name="real-time"></a>Tempo reale

I dati audio vengono elaborati in tempo reale per restituire l'identificatore e la trascrizione. Selezionare questa modalità se il requisito della soluzione di trascrizione consiste nel fornire ai partecipanti di conversazioni una visualizzazione della trascrizione in tempo reale della conversazione in corso. Ad esempio, la compilazione di un'applicazione per rendere le riunioni più accessibili da parte di membri sordi e difficili da ascoltare è un caso d'uso ideale per la trascrizione in tempo reale.

### <a name="asynchronous"></a>Asincrono

I dati audio sono batch elaborati per restituire l'identificatore e la trascrizione del altoparlante. Selezionare questa modalità se il requisito della soluzione di trascrizione è fornire un'accuratezza più elevata senza visualizzazione della trascrizione in tempo reale. Se, ad esempio, si desidera compilare un'applicazione per consentire ai partecipanti della riunione di recuperare facilmente riunioni mancanti, utilizzare la modalità di trascrizione asincrona per ottenere risultati di trascrizione ad alta precisione.

### <a name="real-time-plus-asynchronous"></a>Tempo reale più asincrono

I dati audio vengono elaborati in tempo reale per restituire l'identificatore e la trascrizione del altoparlante. Inoltre, viene creata una richiesta per ottenere una trascrizione ad alta precisione tramite l'elaborazione asincrona. Selezionare questa modalità se l'applicazione ha bisogno di trascrizione in tempo reale, ma richiede anche una trascrizione di accuratezza più elevata da usare in seguito alla conversazione o alla riunione.

## <a name="language-support"></a>Supporto per le lingue

Attualmente, la trascrizione delle conversazioni supporta "en-US" e "zh-CN" nelle aree seguenti: *centralus* e *eastasia*. Se è necessario un supporto aggiuntivo per le impostazioni locali, contattare la [funzionalità di trascrizione delle conversazioni](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Trascrivere le conversazioni in tempo reale](how-to-use-conversation-transcription-service.md)

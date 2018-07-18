---
title: Documentazione di Speech SDK di Servizi cognitivi | Microsoft Docs
description: 'Note sulla versione: modifiche alle versioni più recenti'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378996"
---
# <a name="release-notes"></a>Note sulla versione

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Speech SDK di Servizi cognitivi 0.4.0: versione di giugno 2018

**Modifiche funzionali**

- AudioInputStream

  Un riconoscimento è ora in grado di usare un flusso come un'origine audio. Per informazioni dettagliate, vedi le [procedure correlate](how-to-use-audio-input-streams.md).

- Formato dettagliato dell'output

  Durante la creazione di `SpeechRecognizer`, è possibile richiedere un formato di output `Detailed` o `Simple`. Il `DetailedSpeechRecognitionResult` contiene punteggio di attendibilità, testo riconosciuto, forma lessicale non elaborata, forma normalizzata e forma normalizzata con messaggi dal contenuto volgare mascherati.

**Modifica di rilievo**

- Modifica da `SpeechRecognitionResult.Text` a `SpeechRecognitionResult.RecognizedText` in linguaggio C#.

**Correzioni di bug**

- Correggi un possibile problema di callback nel livello USP durante l'arresto.

- Se un riconoscimento usa un file di input audio, significa che esso contiene l'handle del file più a lungo rispetto al necessario.

- Sono stati rimossi diversi deadlock tra message pump e riconoscimento.

- Attiva un risultato `NoMatch` quando la risposta dal servizio è scaduta.

- Le librerie di Media Foundation in Windows sono a caricamento ritardato. Questa libreria è richiesta solo per l'input del microfono.

- La velocità di caricamento dei dati audio è limitata a circa due volte la velocità dell'audio originale.

- In Windows, gli assembly C# .NET hanno ora un nome sicuro.

- Correzione della documentazione: `Region` è un'informazione obbligatoria per la creazione di un riconoscimento.

Sono stati aggiunti altri esempi che sono costantemente in corso l'aggiornamento. Per il set di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Speech SDK di Servizi cognitivi 0.2.12733: versione di maggio 2018

La prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.

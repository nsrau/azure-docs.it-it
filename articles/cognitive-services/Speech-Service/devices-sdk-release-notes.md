---
title: Documentazione di Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Le note sulla versione forniscono un registro degli aggiornamenti, dei miglioramenti, delle correzioni di bug e delle modifiche apportate all'SDK dei dispositivi di riconoscimento vocale. Questo articolo viene aggiornato con ogni versione dell'SDK dei dispositivi di riconoscimento vocale.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371614"
---
# <a name="release-notes-speech-devices-sdk"></a>Note sulla versione: Speech Devices SDK

L'elenco nelle sezioni seguenti è modificato nelle versioni più recenti.

## <a name="speech-devices-sdk-190"></a>SDK 1.9.0 dei dispositivi di riconoscimento vocale:

- Vengono forniti i file binari iniziali per [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64).
- Roobo v1 ora utilizza Maven per Speech SDK
- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.9.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>SDK 1.7.0 dei dispositivi vocali:

- Linux ARM è ora supportato.
- Vengono forniti i file binari iniziali per [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) (Linux ARM64).
- Gli utenti `AudioConfig.fromDefaultMicrophoneInput()` di `AudioConfig.fromMicrophoneInput(deviceName)` Windows possono utilizzare o specificare il microfono da utilizzare.
- La dimensione della libreria è stata ottimizzata.
- Supporto per il riconoscimento di più turni usando lo stesso oggetto di riconoscimento vocale/intento.
- Correggere il blocco occasionale che si verifica durante l'arresto del riconoscimento.
- Le app di esempio ora contengono un file participants.properties di esempio per illustrare il formato del file.
- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.7.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>SDK 1.6.0 dei dispositivi di riconoscimento vocale:

- Supporta Azure Kinect DK in Windows e Linux con [un'applicazione di esempio](https://aka.ms/sdsdk-download) comuneSupport [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) on Windows and Linux with common sample application
- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.6.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>SDK 1.5.1 dispositivi vocali:

- Includi [transcrizione di conversazione](conversation-transcription-service.md) nell'app di esempio.
- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.5.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Dispositivi vocali SDK 1.5.0: versione 2019-maggio

- Speech Devices SDK è ora GA e non è più un'anteprima gated.
- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.5.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
- La nuova tecnologia delle parole chiave apporta miglioramenti significativi alla qualità, vedere Modifiche di rilievo.
- Nuova pipeline di elaborazione audio per un migliore riconoscimento del campo lontano.

**Modifiche che causano un'interruzione**

- A causa della nuova tecnologia delle parole chiave, tutte le parole chiave devono essere ricreate nel nostro portale migliorato per le parole chiave. Per rimuovere completamente le vecchie parole chiave dal dispositivo disinstallare l'applicazione precedente.
  - adb disinstallare com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech Devices SDK 1.4.0: versione 2019-Apr

- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.4.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech Devices SDK 1.3.1: versione 2019-Mar

- Aggiornato il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.3.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
- Gestione delle parole chiave aggiornata, vedere Interruzione delle modifiche.
- L'applicazione di esempio aggiunge la scelta della lingua sia per il riconoscimento vocale che per la traduzione.

**Modifiche che causano un'interruzione**

- [L'installazione di una parola chiave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) è stata semplificata, ora fa parte dell'app e non richiede un'installazione separata sul dispositivo.
- Il riconoscimento delle parole chiave è cambiato e sono supportati due eventi.
  - `RecognizingKeyword,`indica che il risultato del discorso contiene testo (non verificato) delle parole chiave.
  - `RecognizedKeyword`, indica che il riconoscimento delle parole chiave è stato completato riconoscendo la parola chiave specificata.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Dispositivi vocali SDK 1.1.0: versione 2018-novembre

- Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 1.1.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
- L'accuratezza del riconoscimento vocale in campo lontano è stata migliorata grazie a un nuovo algoritmo di elaborazione audio.
- L'applicazione di esempio ha aggiunto il supporto al riconoscimento vocale in cinese.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK 1.0.1: versione 2018-ottobre

- Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 1.0.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
- L'accuratezza del riconoscimento vocale risulterà migliorata grazie a un nuovo algoritmo di elaborazione audio.
- Un bug relativo alla sessione audio di riconoscimento continuo è stato corretto.

**Modifiche che causano un'interruzione**

- Questa versione introduce numerose modifiche di rilievo. Vedere [questa pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) per informazioni dettagliate relative alle API.
- I file di modello KWS non sono compatibili con Speech Devices SDK 1.0.1. I file di parole chiave esistenti verranno eliminati dopo la scrittura dei nuovi file di parole chiave nel dispositivo.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Periferiche vocali SDK 0.5.0: versione 2018-agosto

- È stata migliorata l'accuratezza del riconoscimento vocale correggendo un bug nel codice di elaborazione audio.
- Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 0.5.0. Per altre informazioni, vedere le relative [Note sulla versione](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Dispositivi vocali SDK 0.2.12733: versione 2018-maggio

Prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.

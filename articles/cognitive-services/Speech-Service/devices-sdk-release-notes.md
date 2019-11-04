---
title: Documentazione di Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: 'Note sulla versione: modifiche alle versioni più recenti'
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: ba1db1ccba6a1849756e75c9b9f7078371da5bfb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464753"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Note sulla versione di Speech Devices SDK di Servizi cognitivi
L'elenco nelle sezioni seguenti è modificato nelle versioni più recenti.

## <a name="speech-devices-sdk-160"></a>Speech Devices SDK 1.6.0:

*   Supporto di [Azure Kinect dk](https://azure.microsoft.com/services/kinect-dk/) in Windows e Linux con l' [applicazione di esempio](https://aka.ms/sdsdk-download) comune
*   Aggiornamento del componente [SDK vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.6.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Speech Devices SDK 1.5.1:

*   Includere la [trascrizione delle conversazioni](conversation-transcription-service.md) nell'app di esempio.
*   Aggiornamento del componente [SDK vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.5.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Servizi cognitivi Speech Devices SDK 1.5.0:2019-versione di maggio

*   Speech Devices SDK è ora GA e non è più un'anteprima gestita.
*   Aggiornamento del componente [SDK vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.5.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
*   La nuova tecnologia per le parole chiave offre miglioramenti significativi della qualità, vedere modifiche di rilievo.
*   Nuova pipeline di elaborazione audio per migliorare il riconoscimento dei campi.

**Modifiche di rilievo**

*   A causa della nuova tecnologia keyword, tutte le parole chiave devono essere ricreate nel portale delle parole chiave migliorato. Per rimuovere completamente le parole chiave obsolete dal dispositivo, disinstallare l'app precedente.
    - ADB disinstallare com. Microsoft. coginitiveservices. Speech. Samples. sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Servizi cognitivi Speech Devices SDK 1.4.0:2019-versione apr

* Aggiornamento del componente [SDK vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.4.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Servizi cognitivi Speech Devices SDK 1.3.1:2019-versione mar

* Aggiornamento del componente [SDK vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) alla versione 1.3.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
*   Gestione delle parole chiave aggiornata. vedere modifiche di rilievo.
*   L'applicazione di esempio aggiunge la lingua scelta per il riconoscimento vocale e la traduzione.

**Modifiche di rilievo**

*   L' [installazione di una parola chiave](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) è stata semplificata ed è ora inclusa nell'app e non è necessaria un'installazione separata sul dispositivo.
*   Il riconoscimento delle parole chiave è stato modificato e sono supportati due eventi.
    - RecognizingKeyword, indica che il risultato del riconoscimento vocale contiene testo della parola chiave non verificato.
    - RecognizedKeyword, indica che il riconoscimento delle parole chiave ha completato il riconoscimento della parola chiave specificata.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Speech Devices SDK di Servizi cognitivi 1.1.0: versione di novembre 2018

* Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 1.1.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
* L'accuratezza del riconoscimento vocale in campo lontano è stata migliorata grazie a un nuovo algoritmo di elaborazione audio.
* L'applicazione di esempio ha aggiunto il supporto al riconoscimento vocale in cinese.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Speech Devices SDK di Servizi cognitivi 1.0.1: versione di ottobre 2018

* Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 1.0.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew).
* L'accuratezza del riconoscimento vocale risulterà migliorata grazie a un nuovo algoritmo di elaborazione audio.  
* Un bug relativo alla sessione audio di riconoscimento continuo è stato corretto.

**Modifiche di rilievo**

* Questa versione introduce numerose modifiche di rilievo. Vedere [questa pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) per informazioni dettagliate relative alle API.
* I file di modello KWS non sono compatibili con Speech Devices SDK 1.0.1. I file delle parole chiave esistenti verranno eliminati dopo che i nuovi file di parole chiave vengono scritti nel dispositivo.

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK di Servizi cognitivi 0.5.0: versione di agosto 2018

* È stata migliorata l'accuratezza del riconoscimento vocale correggendo un bug nel codice di elaborazione audio.
* Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 0.5.0. Per altre informazioni, vedere le relative [Note sulla versione](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK di Servizi cognitivi 0.2.12733: versione di maggio 2018

Prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.

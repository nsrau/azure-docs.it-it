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
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 33a03b24de56ab1090cc8e07c9619eda17f33e27
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64711895"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Note sulla versione di Speech Devices SDK di Servizi cognitivi

L'elenco nelle sezioni seguenti è modificato nelle versioni più recenti.

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Dispositivi vocale di servizi cognitivi SDK 1.4.0: Rilascio: aprile 2019 

* Aggiornato il [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente alla versione 1.4.0. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Dispositivi vocale di servizi cognitivi 1.3.1 SDK: Versione-marzo 2019 

* Aggiornato il [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) componente alla versione 1.3.1. Per altre informazioni, vedere le relative [Note sulla versione](https://aka.ms/csspeech/whatsnew). 
*   Word riattivazione aggiornata la gestione, vedere le modifiche di rilievo.
*   Applicazione di esempio aggiunge una scelta del linguaggio per il riconoscimento vocale e traduzione.

**Modifiche di rilievo** 

*   [Installazione di una parola riattivazione](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) è stato semplificato, fa ora parte dell'app e non richiede installazione separata sul dispositivo.
*   Il riconoscimento delle parole la riattivazione è stato modificato e sono supportati due eventi.
    - RecognizingKeyword, indica il risultato di riconoscimento vocale contiene testo (parola chiave) (non verificato).
    - RecognizedKeyword, indica che il riconoscimento (parola chiave) completato riconoscendo la parola chiave Data.


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
* I file di modello KWS non sono compatibili con Speech Devices SDK 1.0.1. I file Wake Word esistenti verranno eliminati dopo la scrittura di quelli nuovi nel dispositivo. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Speech Devices SDK di Servizi cognitivi 0.5.0: versione di agosto 2018

* È stata migliorata l'accuratezza del riconoscimento vocale correggendo un bug nel codice di elaborazione audio.
* Il componente [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) è stato aggiornato alla versione 0.5.0. Per altre informazioni, vedere le relative [Note sulla versione](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Speech Devices SDK di Servizi cognitivi 0.2.12733: versione di maggio 2018

Prima versione di anteprima pubblica di Speech SDK di Servizi cognitivi.

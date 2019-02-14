---
title: Informazioni su Sintesi vocale - Servizio Voce
titleSuffix: Azure Cognitive Services
description: L'API Sintesi vocale offre più di 75 voci in più di 45 lingue e impostazioni locali. Per usare caratteri voce standard, è sufficiente specificare il nome della voce con pochi altri parametri quando si chiama il servizio Voce.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0836ae4a9041db27cfed35dd0f1fc0df6e541aff
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859335"
---
# <a name="about-the-text-to-speech-api"></a>Informazioni sull'API Sintesi vocale

L'API **Sintesi vocale** converte il testo di input in parlato dal *suono naturale*.

Per generare il parlato, l'applicazione invia richieste HTTP POST all'API Sintesi vocale, dove il testo viene sintetizzato in una voce dal suono umano e restituita come file audio. Sono supportate numerosi lingue e voci.

Gli scenari in cui viene adottata la sintesi vocale includono:

* *Miglioramento dell'accessibilità:* la tecnologia di **sintesi vocale** consente ai proprietari e agli autori di contenuto di rispondere ai diversi modi in cui le persone interagiscono con il contenuto. Gli utenti con disturbi della vista o difficoltà di lettura o leggibilità apprezzano di poter utilizzare il contenuto oralmente. L'output vocale permette alle persone anche di fruire con maggiore facilità del contenuto testuale, ad esempio giornali o blog, sui dispositivi mobili mentre viaggiano o si allenano.

* *Risposta in scenari multitasking:* la **sintesi vocale** consente alle persone di acquisire informazioni importanti in modo rapido e comodo, mentre sono alla guida o in altre situazioni in cui non sarebbe pratico leggere. Un'applicazione comune in quest'ambito è la navigazione.

* *Miglioramento dell'apprendimento in più modi:* le persone apprendono in modi diversi. Gli esperti di apprendimento online hanno dimostrato che, fornendo voce e testo insieme, diventa più semplice assimilare e memorizzare le informazioni.

* *Distribuzione di bot o assistenti intuitivi:* la capacità di parlare può essere un aspetto fondamentale per un chat bot intelligente o un assistente virtuale. Sempre più aziende stanno sviluppando chat bot per offrire ai clienti esperienze di supporto coinvolgenti. La voce crea un'altra dimensione perché consente di ascoltare le risposte del bot (ad esempio, al telefono).

## <a name="voice-support"></a>Supporto vocale

Il servizio di **sintesi vocale** di Microsoft offre più di 75 voci in più di 45 lingue e impostazioni locali. Per usare questi "caratteri voce" standard, è sufficiente specificare il nome della voce con pochi altri parametri quando si chiama l'API REST del servizio. Per altre informazioni sulle lingue, le impostazioni locali e le voci supportate, vedere [Supporto per le lingue](language-support.md#text-to-speech).

> [!IMPORTANT]
> I costi variano per voci standard, personalizzate e neurali. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="neural-voices"></a>Voci neurali

La funzionalità di sintesi vocale neurale può essere usata per rendere più naturali e coinvolgenti le interazioni con chat bot e assistenti virtuali, per convertire testo digitale (come gli e-book) in audiolibri e per migliorare i sistemi dei navigatori per le automobili. Con la prosodia naturale simile al linguaggio umano e l'articolazione chiara delle parole, la sintesi vocale neurale ha ridotto in modo significativo le difficoltà di ascolto durante l'interazione con i sistemi di intelligenza artificiale. Per altre informazioni sulle voci neurali, vedere [Supporto per le lingue](language-support.md#text-to-speech).

### <a name="custom-voices"></a>Voci personalizzate

La personalizzazione della voce del servizio Sintesi vocale consente di creare una voce distintiva e unica per il proprio marchio, ovvero un *carattere voce*. Per creare un carattere voce, effettuare una registrazione in studio e caricare gli script associati come dati di training. Il servizio crea quindi un modello vocale univoco ottimizzato per la registrazione. È possibile usare questo carattere voce per la sintesi vocale. Per altre informazioni, vedere [Caratteri voce personalizzati](how-to-customize-voice-font.md).

## <a name="api-capabilities"></a>Funzionalità API

Molte delle funzionalità dell'API **Sintesi vocale**, soprattutto per quanto riguarda la personalizzazione, sono disponibili tramite REST. La tabella seguente riepiloga le funzionalità di ogni metodo di accesso all'API. Per un elenco completo di funzionalità e i dettagli dell'API, vedere le [informazioni di riferimento su Swagger](https://westus.cris.ai/swagger/ui/index).

| Caso d'uso | REST | SDK |
|-----|-----|-----|----|
| Caricare set di dati per l'adattamento vocale | Sì | No  |
| Creare e gestire i modelli di caratteri voce | Sì | No  |
| Creare e gestire distribuzioni di caratteri voce | Sì | No  |
| Creare e gestire test di caratteri voce| Sì | No  |
| Gestisci sottoscrizioni | Sì | No  |

> [!NOTE]
> L'API implementa la limitazione delle richieste dell'API a 25 ogni 5 secondi. Le intestazioni dei messaggi informeranno dei limiti.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita per i servizi Voce](https://azure.microsoft.com/try/cognitive-services/)
* [Avvio rapido: Sintesi vocale, Python](quickstart-python-text-to-speech.md)
* [Avvio rapido: Sintesi vocale, .NET Core](quickstart-dotnet-text-to-speech.md)
* [Informazioni di riferimento sulle API REST](rest-apis.md)

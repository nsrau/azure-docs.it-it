---
title: Informazioni su Sintesi vocale - Servizio Voce
titleSuffix: Azure Cognitive Services
description: Il servizio di sintesi vocale Microsoft offre più di 75 voci in più di 45 lingue e impostazioni locali. Per usare caratteri voce standard, è sufficiente specificare il nome della voce con pochi altri parametri quando si chiama il servizio Voce.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: erhopf
ms.openlocfilehash: 2f79ecbb42ba6453c7fb615114c0b7e56402ada3
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363064"
---
# <a name="about-the-text-to-speech-api"></a>Informazioni sull'API Sintesi vocale

L'API **Sintesi vocale** del servizio Voce converte il testo di input in parlato dal *suono naturale*.

Per generare il parlato, l'applicazione invia richieste HTTP POST al servizio Voce, dove il testo viene sintetizzato in una voce dal suono umano e restituita come file audio. Sono supportate numerosi lingue e voci.

Gli scenari in cui viene adottata la sintesi vocale includono:

* *Miglioramento dell'accessibilità:* la tecnologia di **sintesi vocale** consente ai proprietari e agli autori del contenuto di rispondere ai diversi modi in cui le persone interagiscono con il contenuto. Gli utenti con disturbi della vista o difficoltà di lettura o leggibilità apprezzano di poter utilizzare il contenuto oralmente. L'output vocale permette alle persone anche di fruire con maggiore facilità del contenuto testuale, ad esempio giornali o blog, sui dispositivi mobili mentre viaggiano o si allenano.

* *Risposta in scenari multitasking:* la **sintesi vocale** consente alle persone di assimilare importanti informazioni rapidamente e comodamente mentre sono alla guida o in altre situazioni in cui non sarebbe pratico leggere. Un'applicazione comune in quest'ambito è la navigazione.

* *Miglioramento dell'apprendimento in più modi:* le persone apprendono in modi diversi. Gli esperti di apprendimento online hanno dimostrato che, fornendo voce e testo insieme, diventa più semplice assimilare e memorizzare le informazioni.

* *Distribuzione di bot o assistenti intuitivi:* la capacità di parlare può essere un aspetto fondamentale per un chat bot intelligente o un assistente virtuale. Sempre più aziende stanno sviluppando chat bot per offrire ai clienti esperienze di supporto coinvolgenti. La voce crea un'altra dimensione perché consente di ascoltare le risposte del bot (ad esempio, al telefono).

## <a name="voice-support"></a>Supporto vocale

Il servizio di **sintesi vocale** di Microsoft offre più di 75 voci in più di 45 lingue e impostazioni locali. Per usare questi "caratteri voce" standard, è sufficiente specificare il nome della voce con pochi altri parametri quando si chiama l'API REST del servizio. Per informazioni dettagliate sulle voci supportate, vedere [Lingue supportate](language-support.md#text-to-speech).

Se si vuole usare una voce particolare per l'applicazione, è possibile creare [caratteri voce](how-to-customize-voice-font.md) personalizzati dai propri campioni vocali.

## <a name="api-capabilities"></a>Funzionalità API

Molte delle funzionalità dell'API **Sintesi vocale**, soprattutto per quanto riguarda la personalizzazione, sono disponibili tramite REST. La tabella seguente riepiloga le funzionalità di ogni metodo di accesso all'API. Per un elenco completo di funzionalità e i dettagli dell'API, vedere le [informazioni di riferimento su Swagger](https://westus.cris.ai/swagger/ui/index).

| Caso d'uso | REST | SDK |
|-----|-----|-----|----|
| Caricare set di dati per l'adattamento vocale | Yes | No  |
| Creare e gestire i modelli di caratteri voce | Yes | No  |
| Creare e gestire distribuzioni di caratteri voce | Yes | No  |
| Creare e gestire test di caratteri voce| Yes | No  |
| Gestisci sottoscrizioni | Yes | No  |

> [!NOTE]
> L'API implementa la limitazione delle richieste dell'API a 25 ogni 5 secondi. Le intestazioni dei messaggi informeranno dei limiti.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sulla sintesi vocale tramite l'API REST](how-to-text-to-speech.md)

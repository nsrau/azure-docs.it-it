---
title: Informazioni su Sintesi vocale
description: Panoramica delle funzionalità di sintesi vocale.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: d111a9f852b849df15dbd056a7210fac82cee190
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324256"
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

Il servizio di **sintesi vocale** di Microsoft offre più di 75 voci in più di 45 lingue e impostazioni locali. Per usare questi "caratteri voce" standard, è sufficiente specificare il nome della voce con pochi altri parametri quando si chiama l'API REST del servizio. Per informazioni dettagliate sulle voci supportate, vedere [Lingue supportate](https://docs.microsoft.com/azure/cognitive-services/speech-service/supported-languages#text-to-speech). 

Se si vuole usare una voce particolare per l'applicazione, è possibile creare [caratteri voce](how-to-customize-voice-font.md) personalizzati dai propri campioni vocali.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
* [Informazioni sulla sintesi vocale tramite l'API REST](how-to-text-to-speech.md)

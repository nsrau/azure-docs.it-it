---
title: Panoramica del Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Il Servizio di riconoscimento vocale personalizzato è un servizio basato sul cloud che consente agli utenti di personalizzare i modelli conversione voce/testo per la trascrizione della voce in testo scritto.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/07/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: da88989753069f7ba8ca2c2e2806a648f3df4e3c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948367"
---
# <a name="what-is-custom-speech-service"></a>Informazioni sul Servizio di riconoscimento vocale personalizzato

Il Servizio di riconoscimento vocale personalizzato è un servizio basato sul cloud che consente agli utenti di personalizzare i modelli conversione voce/testo per la trascrizione della voce in testo scritto.
Per usare il Servizio di riconoscimento vocale personalizzato, vedere il [portale di Servizi di riconoscimento vocale personalizzato](https://cris.ai).

Il Servizio di riconoscimento vocale personalizzato consente di creare modelli linguistici e modelli acustici personalizzati su misura per l'applicazione e gli utenti. Caricando dati vocali e/o di testo specifici nel Servizio di riconoscimento vocale personalizzato, è possibile creare modelli personalizzati che possono essere usati insieme ai modelli conversione voce/testo all'avanguardia esistenti di Microsoft.

Ad esempio, se si aggiunge l'interazione vocale all'app di un telefono cellulare, un tablet o un PC, è possibile creare un modello linguistico personalizzato che può essere usato insieme al modello acustico di Microsoft per creare un endpoint per il riconoscimento vocale progettato specificamente per l'app. Se l'applicazione è progettata per essere usata in un ambiente specifico o da una popolazione di utenti specifica, è possibile anche creare e distribuire un modello acustico personalizzato con questo servizio.


## <a name="how-do-speech-recognition-systems-work"></a>Come funzionano i sistemi di riconoscimento vocale?
I sistemi di riconoscimento vocale sono costituiti da diversi componenti che interagiscono. Due dei componenti principali sono il modello acustico e il modello linguistico.

Il modello acustico è un classificatore che etichetta brevi frammenti di audio in una serie di fonemi, o unità di suono, in una lingua specifica. Ad esempio, la parola inglese "speech" è costituita da quattro fonemi "s p iy ch". Queste classificazioni vengono effettuate con una frequenza di 100 volte al secondo.

Il modello linguistico è una distribuzione probabilistica di sequenze di parole. Il modello linguistico aiuta il sistema a decidere tra sequenze di parole con suoni simili, in base alla probabilità delle sequenze di parole stesse. Ad esempio, le frasi "recognize speech" e "wreck a nice beach" presentano un suono simile ma è molto più probabile che ricorra la prima e quindi il modello linguistico le assegnerà un punteggio più alto.

I modelli sia acustici che linguistici sono modelli statistici appresi dai dati di training. Di conseguenza, garantiscono risultati migliori se il riconoscimento vocale rilevato nelle applicazioni è simile ai dati osservati durante il training. Il training dei modelli acustici e linguistici nel motore di riconoscimento vocale Microsoft è stato eseguito su un'enorme raccolta di parlato e testo garantendo prestazioni all'avanguardia per gli scenari d'uso più comuni, ad esempio l'interazione con Cortana nello smartphone, tablet o PC, la ricerca sul Web con comandi vocali o la dettatura di messaggi di testo a un amico.

## <a name="why-use-the-custom-speech-service"></a>Perché usare il Servizio di riconoscimento vocale personalizzato?
Anche se il motore di riconoscimento vocale Microsoft è di qualità elevata, è destinato agli scenari descritti sopra. Tuttavia, se si prevede che le query vocali inviate all'applicazione contengano vocaboli particolari, ad esempio nomi di prodotto o gergo specifico, che raramente vengono usati nel parlato normale, personalizzando il modello linguistico è possibile ottenere prestazioni migliori.

Ad esempio, se si sta sviluppando un'app che permette di eseguire ricerche vocali in MSDN, è probabile che alcuni termini, come ad esempio "orientato a oggetti", "spazio dei nomi" o "dot net", ricorrano con maggiore frequenza rispetto a quanto accade nelle applicazioni vocali tipiche. Con la personalizzazione del modello linguistico, il sistema può apprendere questi termini.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare il Servizio di riconoscimento vocale personalizzato, vedere il [portale del Servizio di riconoscimento vocale personalizzato] (https://cris.ai).

* [esercitazione introduttiva](cognitive-services-custom-speech-get-started.md)
* [DOMANDE FREQUENTI](cognitive-services-custom-speech-faq.md)
* [Glossario](cognitive-services-custom-speech-glossary.md)

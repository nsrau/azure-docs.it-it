---
title: Che cos'è il Servizio decisionale personalizzato? - Servizi cognitivi di Azure | Microsoft Docs
description: Questo articolo offre una panoramica del Servizio decisionale personalizzato di Azure, un'API basata sul cloud per la gestione di processi decisionali contestualizzati che acquisisce precisione con l'esperienza.
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378993"
---
# <a name="what-is-custom-decision-service"></a>Che cos'è il Servizio decisionale personalizzato?

In una tipica applicazione Web o per dispositivi mobili una pagina principale è collegata a diversi articoli o altri tipi di contenuto. Quando la pagina principale viene caricata, l'applicazione può richiedere al Servizio decisionale personalizzato di classificare gli articoli inclusi nella pagina. In questo modo, quando un utente sceglie un articolo facendovi clic sopra, può essere inviata una seconda richiesta al Servizio decisionale personalizzato, che registra il risultato della decisione dell'utente.

Il Servizio decisionale personalizzato è facile da usare, in quanto richiede solo un feed RSS per il contenuto e l'aggiunta di poche righe di codice JavaScript nell'applicazione.

Il Servizio decisionale personalizzato converte il contenuto in funzionalità per l'apprendimento automatico. Il sistema usa queste funzionalità per riconoscere il contenuto in termini di testo, immagini, video e valutazione complessiva. Il sistema usa altri [servizi cognitivi Microsoft](https://www.microsoft.com/cognitive-services), come [Entity Linking](../entitylinking/home.md), [Analisi del testo](../text-analytics/overview.md), [Emozioni](../emotion/home.md) e [Visione artificiale](../computer-vision/home.md).

Tra i casi d'uso comuni per il Servizio decisionale personalizzato sono inclusi i seguenti:

* Personalizzazione di articoli in un sito Web di notizie
* Personalizzazione di contenuti video in una portale multimediale
* Ottimizzazione del posizionamento di annunci o delle pagine Web cui indirizza ogni annuncio
* Classificazione degli articoli consigliati in un sito Web per gli acquisti.

Il Servizio decisionale personalizzato è attualmente disponibile come *anteprima pubblica gratuita*. Il servizio può personalizzare un elenco di articoli in un sito Web o un'app. L'estrazione di funzionalità è ottimizzata per contenuti in lingua inglese. Per altre lingue, tra cui spagnolo, francese, tedesco, portoghese e giapponese, sono disponibili [funzionalità limitate](../text-analytics/overview.md). Questa documentazione sarà sottoposta a revisione quando saranno disponibili nuove funzionalità.

Il Servizio decisionale personalizzato può essere usato in applicazioni non incluse nell'ambito della personalizzazione di contenuti. Queste applicazioni possono essere adatte per un'anteprima personalizzata. Per altre informazioni, è possibile [contattare Microsoft](https://azure.microsoft.com/overview/sales-number/).

## <a name="api-usage-modes"></a>Modalità di utilizzo delle API

Il Servizio decisionale personalizzato può essere applicato a pagine Web e app per dispositivi mobili. Le API possono essere chiamate da un browser o da un'app. L'utilizzo delle API è simile in entrambi i casi, ma differisce per alcuni dettagli.

## <a name="glossary-of-terms"></a>Glossario dei termini

In questa documentazione ricorrono spesso diversi termini:

* **Set di azioni**: set di elementi di contenuto che il Servizio decisionale personalizzato deve classificare. Questo set può essere specificato come endpoint *RSS* o *Atom*.
* **Classificazione**: ogni richiesta al Servizio decisionale personalizzato specifica uno o più set di azioni. Il sistema risponde selezionando tutte le opzioni di contenuto da questi set e li restituisce in ordine classificato.
* **Funzione di callback**: questa funzione, specificata personalmente, esegue il rendering del contenuto nell'interfaccia utente. Il contenuto viene ordinato in base alla classificazione restituita dal Servizio decisionale personalizzato.
* **Riconoscimento**: misura che indica il modo in cui l'utente ha risposto al contenuto restituito. Il Servizio decisionale personalizzato misura la risposta dell'utente tramite clic. I clic vengono segnalati al sistema tramite codice personalizzato inserito nell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* [Registrare l'applicazione](custom-decision-service-get-started-register.md) nel Servizio decisionale personalizzato
* Iniziare a ottimizzare una [pagina Web](custom-decision-service-get-started-browser.md) o un'[app per smartphone](custom-decision-service-get-started-app.md).
* Consultare le [informazioni di riferimento sull'API](custom-decision-service-api-reference.md) per altre informazioni sulla funzionalità fornita.
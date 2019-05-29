---
title: Informazioni sull'API Viso
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare il servizio Viso per rilevare e analizzare i visi nelle immagini.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: c45fd508c14c368c6c9057b9fdeea8df9d8a52c3
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905680"
---
# <a name="what-is-the-azure-face-api"></a>Informazioni sull'API Viso di Azure

L'API Viso di Servizi cognitivi di Azure offre gli algoritmi per rilevare, riconoscere e analizzare i visi umani nelle immagini. La possibilità di elaborare le informazioni relative ai visi umani è importante in molti scenari di software diversi, tra cui sicurezza, interfaccia utente naturale, analisi e gestione del contenuto di immagini, app per dispositivi mobili e robotica.

L'API Viso offre diverse funzioni, descritte nelle sezioni seguenti. Proseguire la lettura per altre informazioni su ogni funzione.

## <a name="face-detection"></a>Rilevamento del viso

L'API Viso rileva i visi umani in un'immagine e restituisce le coordinate del rettangolo delle posizioni corrispondenti. Facoltativamente, la funzionalità di rilevamento viso può estrarre una serie di attributi relativi al viso, come posa, posizione della testa, sesso, età, emozione, barba/baffi e occhiali.

> [!NOTE]
> La funzionalità di rilevamento viso è disponibile anche tramite l'[API Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Se però si vogliono eseguire altre operazioni con i dati relativi ai visi, è preferibile usare l'API Viso, ovvero il servizio descritto in questo articolo.

![Immagine di una donna e di un uomo, con rettangoli disegnati intorno ai visi e informazioni su età e sesso](./Images/Face.detection.jpg)

Per altre informazioni sul rilevamento del viso, vedere l'articolo che illustra i concetti relativi al [rilevamento del viso](concepts/face-detection.md). Consultare inoltre la documentazione di riferimento dell'[API di rilevamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Verifica del viso

L'API di verifica esegue un'autenticazione su due visi rilevati o un'autenticazione da un viso rilevato a un oggetto persona. In pratica, valuta se due visi appartengano alla stessa persona. Questo è potenzialmente utile negli scenari di sicurezza. Per altre informazioni, vedere l'articolo che illustra i concetti relativi al [riconoscimento del viso](concepts/face-recognition.md) o consultare la documentazione di riferimento dell'[API di verifica](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Individuazione di visi simili

L'API di individuazione di visi simili confronta un viso di destinazione con un set di visi candidati e individua un set più piccolo di visi simili a quello di destinazione. Sono supportate due modalità di utilizzo, ovvero matchPerson e matchFace. La modalità matchPerson restituisce visi simili dopo aver filtrato in base all'appartenenza alla stessa persona usando l'[API di verifica](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). La modalità matchFace ignora il filtro di appartenenza alla stessa persona. Restituisce un elenco di visi candidati simili, che possono o meno appartenere alla stessa persona.

L'esempio seguente mostra il viso di destinazione:

![Una donna sorridente](./Images/FaceFindSimilar.QueryFace.jpg)

E questi sono i visi candidati:

![Cinque immagini di persone sorridenti. Le immagini a e b mostrano la stessa persona.](./Images/FaceFindSimilar.Candidates.jpg)

Per trovare quattro visi simili, la modalità matchPerson restituisce a e b, che mostrano la stessa persona del viso di destinazione. La modalità matchFace restituisce a, b, c, e d, esattamente quattro candidati, anche se alcuni non appartengono alla stessa persona della destinazione o la somiglianza è minore. Per altre informazioni, vedere l'articolo che illustra i concetti relativi al [riconoscimento del viso](concepts/face-recognition.md) o consultare la documentazione di riferimento dell'[API di individuazione di visi simili](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Raggruppamento dei visi

L'API di raggruppamento divide un set di visi sconosciuti in più gruppi basati sulla somiglianza. Ogni gruppo è un subset proprio indipendente del set di visi originale. Tutti i visi nello stesso gruppo appartengono probabilmente alla stessa persona. Possono essere presenti gruppi diversi per una singola persona, che si differenziano per un altro fattore, ad esempio l'espressione. Per altre informazioni, vedere l'articolo che illustra i concetti relativi al [riconoscimento del viso](concepts/face-recognition.md) o consultare la documentazione di riferimento dell'[API di raggruppamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificazione delle persone

L'API di identificazione viene usata per identificare un viso rilevato confrontandolo con un database di persone. Questa funzionalità può essere utile per l'aggiunta automatica di tag alle immagini nel software di gestione delle foto. È possibile creare questo database in anticipo e modificarlo nel tempo.

L'immagine seguente mostra un esempio di un database denominato "myfriends". Ogni gruppo può contenere fino a 1 milione di oggetti persona diversi. Per ogni oggetto persona è possibile registrare fino a 248 visi.

![Griglia con tre colonne per persone diverse, ognuna con tre righe di immagini dei visi](./Images/person.group.clare.jpg)

Dopo la creazione e il training di un database, è possibile eseguire l'identificazione in base al gruppo con un nuovo viso rilevato. Se il viso viene identificato come una persona nel gruppo, viene restituito l'oggetto persona.

Per altre informazioni sull'identificazione delle persone, vedere l'articolo che illustra i concetti relativi al [riconoscimento del viso](concepts/face-recognition.md) o consultare la documentazione di riferimento dell'[API di identificazione](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="use-containers"></a>Usare i contenitori

[Usare il contenitore Viso](face-how-to-install-containers.md) per rilevare, riconoscere e identificare visi installando un contenitore Docker standardizzato più vicino ai dati.

## <a name="sample-apps"></a>App di esempio

Le applicazioni di esempio seguenti illustrano alcuni dei modi in cui si può usare l'API Viso:

- [API Viso Microsoft: libreria client ed esempio Windows](https://github.com/Microsoft/Cognitive-Face-Windows). È un'app WPF che illustra diversi scenari di rilevamento, analisi e identificazione del viso.
- [App UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes). È un'app della piattaforma UWP (Universal Windows Platform) che usa l'identificazione del viso insieme a riconoscimento vocale, Cortana, input penna e fotocamera in uno scenario di condivisione note in una famiglia.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutte le risorse di Servizi cognitivi, gli sviluppatori che usano il servizio Viso devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina su Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Seguire un argomento di avvio rapido per implementare un semplice scenario di rilevamento del viso nel codice:

- [Guida introduttiva: Rilevare i visi in un'immagine usando .NET SDK con C#](quickstarts/csharp.md). Sono disponibili altre lingue.

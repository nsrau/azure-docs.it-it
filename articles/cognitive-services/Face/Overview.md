---
title: Che cos'è il servizio Viso di Azure?
titleSuffix: Azure Cognitive Services
description: Il servizio Viso di Azure offre algoritmi di intelligenza artificiale da usare per rilevare, riconoscere e analizzare i visi umani nelle immagini.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: riconoscimento facciale, riconoscimento volto, software di riconoscimento facciale, analisi del viso, corrispondenza dei volti, app di riconoscimento facciale, ricerca di volti per immagine, ricerca basata su riconoscimento facciale
ms.openlocfilehash: 600ca48cc19ee8723b423e484ec96736a55ae7fc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95532257"
---
# <a name="what-is-the-azure-face-service"></a>Che cos'è il servizio Viso di Azure?

> [!WARNING]
> L'11 giugno 2020 Microsoft ha annunciato che non venderà la tecnologia di riconoscimento facciale ai dipartimenti di polizia degli Stati Uniti fino a quando non verranno adottate norme rigorose, che tengano conto dei diritti umani. Di conseguenza, i clienti non possono usare le funzionalità di riconoscimento facciale o le funzionalità incluse in Servizi di Azure, ad esempio Viso o Video Indexer, se il cliente è un dipartimento di polizia degli Stati Uniti o consente l'uso di tali servizi a tale dipartimento.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Il servizio Viso di Azure offre algoritmi di intelligenza artificiale che rilevano, riconoscono e analizzano i visi umani nelle immagini. Il software di riconoscimento facciale è importante in molti scenari diversi, tra cui sicurezza, interfaccia utente naturale, analisi e gestione del contenuto di immagini, app per dispositivi mobili e robotica.

Il servizio Viso offre varie funzioni di analisi facciale, descritte nelle sezioni seguenti.

## <a name="face-detection"></a>Rilevamento del viso

Il servizio Viso rileva i visi umani in un'immagine e restituisce le coordinate del rettangolo delle posizioni corrispondenti. Facoltativamente, la funzionalità di rilevamento volto può estrarre una serie di attributi relativi al viso, come posizione della testa, sesso, età, emozione, barba/baffi e occhiali.

> [!NOTE]
> La funzionalità di rilevamento volto è disponibile anche tramite il [servizio Visione artificiale](../computer-vision/overview.md). Tuttavia, se si vogliono eseguire ulteriori operazioni con i dati relativi al viso, occorre usare questo servizio.

![Immagine di una donna e di un uomo, con rettangoli disegnati intorno ai visi e informazioni su età e sesso](./Images/Face.detection.jpg)

Per altre informazioni sul rilevamento del viso, vedere l'articolo che illustra i concetti relativi al [rilevamento del viso](concepts/face-detection.md). Consultare inoltre la documentazione di riferimento dell'[API di rilevamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Verifica del viso

L'API di verifica esegue un'autenticazione in base a due visi rilevati o da un viso rilevato con un oggetto persona. In pratica, valuta se due visi appartengano alla stessa persona. Questo è potenzialmente utile negli scenari di sicurezza. Per altre informazioni, vedere [Concetti relativi al riconoscimento del viso](concepts/face-recognition.md) o la documentazione di riferimento dell'[API di verifica](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="find-similar-faces"></a>Individuazione di visi simili

L'API di individuazione di visi simili confronta un viso di destinazione con un set di visi candidati e individua un set più piccolo di visi simili a quello di destinazione. Questo è utile per eseguire una ricerca di volti per immagine. 

Sono supportate due modalità di utilizzo, ovvero **matchPerson** e **matchFace**. La modalità **matchPerson** restituisce visi simili dopo aver filtrato in base all'appartenenza alla stessa persona usando l'[API di verifica](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). La modalità **matchFace** ignora il filtro di appartenenza alla stessa persona. Restituisce un elenco di visi candidati simili, che possono appartenere o meno alla stessa persona.

L'esempio seguente mostra il viso di destinazione:

![Una donna sorridente](./Images/FaceFindSimilar.QueryFace.jpg)

E queste immagini sono i visi candidati:

![Cinque immagini di persone sorridenti. Le immagini a e b mostrano la stessa persona.](./Images/FaceFindSimilar.Candidates.jpg)

Per trovare quattro visi simili, la modalità **matchPerson** restituisce a e b, che mostrano la stessa persona del viso di destinazione. La modalità **matchFace** restituisce a, b, c, e d, ossia esattamente quattro candidati, anche se alcuni non appartengono alla stessa persona di destinazione o presentano una somiglianza minore. Per altre informazioni, vedere [Concetti relativi al riconoscimento del viso](concepts/face-recognition.md) o la documentazione di riferimento dell'[API di individuazione di visi simili](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="face-grouping"></a>Raggruppamento dei visi

L'API di raggruppamento divide un set di visi sconosciuti in più gruppi basati sulla somiglianza. Ogni gruppo è un subset proprio indipendente del set di visi originale. Tutti i visi nello stesso gruppo appartengono probabilmente alla stessa persona. Possono essere presenti gruppi diversi per una singola persona, che si differenziano per un altro fattore, ad esempio l'espressione. Per altre informazioni, vedere [Concetti relativi al riconoscimento del viso](concepts/face-recognition.md) o la documentazione di riferimento dell'[API di raggruppamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="person-identification"></a>Identificazione delle persone

L'API di identificazione viene usata per identificare un viso rilevato confrontandolo con un database di persone (ricerca basata su riconoscimento facciale). Questa funzionalità può essere utile per l'aggiunta automatica di tag alle immagini nel software di gestione delle foto. È possibile creare questo database in anticipo e modificarlo nel tempo.

L'immagine seguente mostra un esempio di un database denominato `"myfriends"`. Ogni gruppo può contenere fino a 1 milione di oggetti persona diversi. Per ogni oggetto persona è possibile registrare fino a 248 visi.

![Griglia con tre colonne per persone diverse, ognuna con tre righe di immagini dei visi](./Images/person.group.clare.jpg)

Dopo la creazione e il training di un database, è possibile eseguire l'identificazione in base al gruppo con un nuovo viso rilevato. Se il viso viene identificato come una persona nel gruppo, viene restituito l'oggetto persona.

Per altre informazioni, vedere [Concetti relativi al riconoscimento del viso](concepts/face-recognition.md) o la documentazione di riferimento dell'[API di identificazione](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="deploy-on-premises-using-docker-containers"></a>Distribuire in locale con i contenitori Docker

[Usare il contenitore di Viso (anteprima)](face-how-to-install-containers.md) per distribuire le funzionalità dell'API in locale. Questo contenitore Docker consente di avvicinare il servizio ai dati per motivi di conformità, sicurezza o di altro tipo.

## <a name="sample-apps"></a>App di esempio

Le applicazioni di esempio seguenti illustrano alcuni modi in cui usare il servizio Viso:

- [API Viso: libreria client ed esempio Windows](https://github.com/Microsoft/Cognitive-Face-Windows). È un'app WPF che illustra diversi scenari di rilevamento, analisi e identificazione del viso.
- [App UWP FamilyNotes](https://github.com/Microsoft/Windows-appsample-familynotes). È un'app della piattaforma UWP (Universal Windows Platform) che usa l'identificazione del viso insieme a riconoscimento vocale, Cortana, input penna e fotocamera in uno scenario di condivisione note in una famiglia.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come per tutte le risorse di Servizi cognitivi, gli sviluppatori che usano il servizio Viso devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina su Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Seguire una guida di avvio rapido per codificare i componenti di base di un'app di riconoscimento volto nel linguaggio preferito.

- [Avvio rapido sulla libreria client](quickstarts/client-libraries.md).
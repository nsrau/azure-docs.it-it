---
title: Linee guida di progettazione della divulgazione
titleSuffix: Azure Cognitive Services
description: Introduzione alle linee guida per la progettazione della divulgazione e valutazione del livello di divulgazione.
services: cognitive-services
author: angle
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: angle
ms.openlocfilehash: d3b3f90be7dda28db9a27aa96282acee61e6c8fc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507341"
---
# <a name="disclosure-design-guidelines"></a>Linee guida di progettazione della divulgazione
Scopri come creare e gestire l'attendibilità dei clienti grazie alla trasparenza della natura sintetica dell'esperienza vocale.

## <a name="what-is-disclosure"></a>Che cos'è la divulgazione?

La divulgazione è uno strumento che consente agli utenti&#39;di riconoscerne l'interazione o l'ascolto di una voce generata in modo sintetico.

## <a name="why-is-disclosure-necessary"></a>Perché è necessaria la divulgazione?

La necessità di divulgare le origini sintetiche di una voce generata dal computer è relativamente nuova. In passato, le voci generate dal computer erano ovviamente che nessuno li avrebbe mai scambiati per una persona reale. Ogni giorno, tuttavia, il realismo delle voci sintetiche migliora e diventano sempre più indistinguibili dalle voci umane.

## <a name="goals"></a>Obiettivi
Questi sono i principi da tenere presenti quando si progettano esperienze vocali sintetiche:

**Rafforzare l'attendibilità**
<br>Progettare con l'intenzione di fallire il test di Turing senza riduzioni dell'esperienza. È possibile consentire agli utenti di interagire con una voce sintetica, consentendo loro di interagire facilmente con l'esperienza.

**Adatta al contesto di utilizzo**
<br>È possibile comprendere quando, dove e come gli utenti interagiranno con la voce sintetica per fornire il tipo di divulgazione appropriato al momento giusto.

**Imposta previsioni chiare**
<br>Consente agli utenti di individuare e comprendere facilmente le funzionalità dell'agente. Offri opportunità per ottenere altre informazioni sulla tecnologia Voice sintetica su richiesta.

**Accetta errore**
<br>Usare i momenti di errore per rafforzare le funzionalità dell'agente.

## <a name="how-to-use-this-guide"></a>Come usare questa guida

Questa guida consente di determinare quali modelli di divulgazione sono più adatti per l'esperienza vocale sintetica. Vengono quindi offerti esempi di come e quando usarli. Ognuno di questi modelli è progettato per massimizzare la trasparenza con gli utenti sulla sintesi vocale, pur rimanendo vero per la progettazione umana.

Considerando il vasto corpo delle linee guida di progettazione per le esperienze vocali, ci concentreremo specificamente su:

1. [**Valutazione della divulgazione**](#disclosure-assessment): processo per determinare il tipo di divulgazione consigliato per l'esperienza vocale sintetica

2. [**Come divulgare**](concepts-disclosure-patterns.md): esempi di modelli di divulgazione che possono essere applicati all'esperienza vocale sintetica

3. [**Quando divulgare**](concepts-disclosure-patterns.md#when-to-disclose): momenti ottimali di divulgazione durante il percorso utente

## <a name="disclosure-assessment"></a>Valutazione della divulgazione
Si prendano&#39; in considerazione le aspettative degli utenti su un'interazione e sul contesto in cui si verificheranno la voce. Se il contesto rende chiaro che una voce sintetica è &quot;parlando, la divulgazione&quot; può essere minima, momentanea o anche non necessaria. I tipi principali di contesto che influiscono sulla divulgazione includono il tipo di persona, il tipo di scenario e il livello di esposizione. Consente inoltre di prendere in considerazione chi potrebbe essere in ascolto.

### <a name="understand-context"></a>Informazioni sul contesto

Utilizzare questo foglio di lavoro per determinare il contesto dell'esperienza vocale sintetica. Questa operazione verrà applicata nel passaggio successivo per determinare il livello di divulgazione.

|                                    | Contesto di utilizzo                                                                                                                                                                                                                                                                                                                                                       | Rischi potenziali & problemi                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. tipo di utente**               | **Se si applica una delle condizioni seguenti, il proprio utente si integra nella categoria ' Human-like persona ':**<br><br><ul><li> Il tipo di utente incarna un vero e proprio se si tratta di una rappresentazione fittizia. (ad esempio, foto o un rendering generato dal computer di una persona reale)<br><br><li> La voce sintetica è basata sulla voce di una persona reale molto riconoscibile (ad esempio, celebrità, figura politica) | Le rappresentazioni più simili a quelle che si associano all'utente, maggiore sarà la probabilità che un utente lo associ a una persona reale, o che ritenga che il contenuto venga parlato da una persona reale invece che dal computer. </ul>                                                                                                                                                                      |
| **2. tipo di scenario**            | **Se si applica una delle condizioni seguenti, l'esperienza vocale rientra nella categoria ' sensibile ':**<br><br><ul><li> Ottiene o Visualizza le informazioni personali dell'utente <br><br> <li> Trasmette notizie/informazioni sensibili al tempo (ad esempio, avviso di emergenza)<br><br><li> Mira a aiutare gli utenti reali a comunicare tra loro (ad esempio, legge i messaggi di posta elettronica/testi personali)<br><br> <li> Fornisce assistenza medica/sanitaria </ul>            | L'uso di una voce sintetica potrebbe non essere appropriato o affidabile per le persone che lo usano quando gli argomenti sono correlati a questioni sensibili, personali o urgenti. Possono anche prevedere lo stesso livello di empatia e consapevolezza contestuale di un vero uomo. |
| **3. livello di esposizione** |**L'esperienza vocale si riferisce probabilmente alla categoria ' High ' se:** <br><br><ul><li>L'utente può udire o interagire con la voce sintetica di frequente o per un lungo periodo di tempo </ul>                                                                                                                                                                             | L'importanza della trasparenza e della creazione di relazioni di trust con gli utenti è ancora più elevata quando si stabiliscono relazioni a lungo termine.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinare il livello di divulgazione

Usare il diagramma seguente per determinare se l'esperienza della voce sintetica richiede una divulgazione elevata o ridotta in base al contesto di utilizzo.

  ![Diagramma di valutazione della divulgazione](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Nota sulla trasparenza](https://aka.ms/neural-tts-transparency-note)
* [Linee guida per la distribuzione responsabile della tecnologia Voice sintetica](concepts-guidelines-responsible-deployment-synthetic.md)
* [Cenni preliminari sul controllo](concepts-gating-overview.md)

## <a name="next-steps"></a>Passaggi successivi

* [Schemi di progettazione della divulgazione](concepts-disclosure-patterns.md)

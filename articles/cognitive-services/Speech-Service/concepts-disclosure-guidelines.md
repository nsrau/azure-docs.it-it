---
title: Linee guida per la progettazione della divulgazione
titleSuffix: Azure Cognitive Services
description: Introduzione alle linee guida di progettazione della divulgazione e alla valutazione del livello di divulgazione.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776386"
---
# <a name="disclosure-design-guidelines"></a>Linee guida sulla progettazione di informative
Scopri come creare e mantenere la fiducia con i clienti essendo trasparente sulla natura sintetica della tua esperienza vocale.

## <a name="what-is-disclosure"></a>Che cos'è la divulgazione?

La divulgazione è un mezzo per far sapere alle persone che&#39;interagire con o ascoltare una voce generata sinteticamente.

## <a name="why-is-disclosure-necessary"></a>Perché è necessaria la divulgazione?

La necessità di rivelare le origini sintetiche di una voce generata al computer è relativamente nuova. In passato, le voci generate al computer erano ovviamente che, nessuno le avrebbe mai scambiate per una persona reale. Ogni giorno, tuttavia, il realismo delle voci sintetiche migliora, e diventano sempre più indistinguibili dalle voci umane.

## <a name="goals"></a>Obiettivi
Questi sono i principi da tenere a mente quando si progettano esperienze vocali sintetiche:

**Rafforzare la fiducia**
<br>Progettare con l'intenzione di fallire il Test di Turing senza degradare l'esperienza. Lascia che gli utenti stiano interagendo con una voce sintetica, consentendo loro di interagire senza problemi con l'esperienza.

**Adattarsi al contesto di utilizzo**
<br>Scopri quando, dove e come gli utenti interagiranno con la voce sintetica per fornire il giusto tipo di divulgazione al momento giusto.

**Stabilisci chiare aspettative**
<br>Consentire agli utenti di individuare e comprendere facilmente le funzionalità dell'agente. Offrire opportunità per saperne di più sulla tecnologia vocale sintetica su richiesta.

**Abbracciare il fallimento**
<br>Utilizzare i momenti di incapacità per rafforzare le capacità dell'agente.

## <a name="how-to-use-this-guide"></a>Come usare questa guida

Questa guida consente di determinare quali modelli di divulgazione sono più adatti per la vostra esperienza vocale sintetica. Offriamo quindi esempi di come e quando utilizzarli. Ognuno di questi modelli è progettato per massimizzare la trasparenza con gli utenti sul discorso sintetico, pur rimanendo fedele al design centrato sull'uomo.

Considerando il vasto corpo di linee guida progettuali sulle esperienze vocali, ci concentriamo qui specificamente su:

1. [**Valutazione della divulgazione**](#disclosure-assessment): Un processo per determinare il tipo di divulgazione consigliato per l'esperienza vocale sintetica

2. [**Come divulgare**](concepts-disclosure-patterns.md): Esempi di modelli di divulgazione che possono essere applicati alla tua esperienza vocale sintetica

3. [**Quando divulgare**](concepts-disclosure-patterns.md#when-to-disclose): Momenti ottimali da divulgare durante il percorso dell'utente

## <a name="disclosure-assessment"></a>Valutazione della divulgazione
Considera gli utenti&#39; le aspettative su un'interazione e sul contesto in cui sperimenteranno la voce. Se il contesto chiarisce &quot;che&quot; una voce sintetica sta parlando, la divulgazione può essere minima, momentanea o addirittura inutile. I principali tipi di contesto che influiscono sulla divulgazione includono il tipo di persona, il tipo di scenario e il livello di esposizione. Aiuta anche a considerare chi potrebbe essere in ascolto.

### <a name="understand-context"></a>Comprendere il contesto

Utilizzare questo foglio di lavoro per determinare il contesto dell'esperienza vocale sintetica. Lo applicherai nel passaggio successivo in cui determinerai il tuo livello di divulgazione.

|                                    | Contesto d'uso                                                                                                                                                                                                                                                                                                                                                       | Rischi & sfide potenziali                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Tipo di persona**               | **Se si applica una delle seguenti condizioni, la tua persona rientra nella categoria "Persona umana":**<br><br><ul><li> Persona incarna un vero essere umano, che si tratti di una rappresentazione fittizia o meno. (ad esempio, una fotografia o un rendering generato al computer di una persona reale)<br><br><li> La voce sintetica si basa sulla voce di una persona reale ampiamente riconoscibile (ad esempio, celebrità, figura politica) | Più rappresentazioni umane danno la tua persona, più è probabile che un utente la associ a una persona reale o li inducono a credere che il contenuto sia parlato da una persona reale piuttosto che generata dal computer. </ul>                                                                                                                                                                      |
| **2. Tipo di scenario**            | **Se si applica una delle seguenti condizioni, la tua esperienza vocale rientra nella categoria "Sensibile":**<br><br><ul><li> Ottiene o visualizza informazioni personali dall'utente <br><br> <li> Trasmette notizie/informazioni sensibili al tempo (ad es. avviso di emergenza)<br><br><li> Ha lo scopo di aiutare le persone reali a comunicare tra loro (ad esempio, legge le e-mail/testi personali)<br><br> <li> Fornisce assistenza medica/sanitaria </ul>            | L'uso di voce sintetica potrebbe non risentirsi appropriato o affidabile per le persone che lo utilizzano quando gli argomenti sono legati a questioni sensibili, personali o urgenti. Possono anche aspettarsi lo stesso livello di empatia e consapevolezza contestuale di un vero essere umano. |
| **3. Livello di esposizione** |**La tua esperienza vocale probabilmente rientra nella categoria "Alto" se:** <br><br><ul><li>L'utente ascolterà o interagirà con la voce sintetica frequentemente o per un lungo periodo di tempo </ul>                                                                                                                                                                             | L'importanza di essere trasparenti e di costruire la fiducia con gli utenti è ancora più alta quando si stabiliscono relazioni a lungo termine.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinare il livello di divulgazione

Utilizzare il diagramma seguente per determinare se l'esperienza vocale sintetica richiede un'elevata o bassa divulgazione in base al contesto di utilizzo.

  ![Diagramma di valutazione della divulgazione](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Documentazione di riferimento

* [Divulgazione per il talento vocale](https://aka.ms/disclosure-voice-talent)
* [Linee guida per la distribuzione responsabile della tecnologia vocale sintetica](concepts-guidelines-responsible-deployment-synthetic.md)
* [Panoramica di Gating](concepts-gating-overview.md)

## <a name="next-steps"></a>Passaggi successivi

* [Schemi progettuali per le informative](concepts-disclosure-patterns.md)

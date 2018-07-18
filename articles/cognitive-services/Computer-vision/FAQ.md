---
title: Domande frequenti sull'API Visione artificiale | Microsoft Docs
description: Ottenere risposte alle domande frequenti sull'API Visione artificiale in Servizi cognitivi Microsoft.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372953"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Domande frequenti sull'API Visione artificiale
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Visione artificiale su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/)

-----

**Domanda**: *è possibile eseguire il training dell'API Visione artificiale per l'uso di tag personalizzati?  Ad esempio, se si vogliono aggiungere immagini di razze di gatti per eseguire il training dell'intelligenza artificiale e quindi ottenere il valore della razza per una richiesta di intelligenza artificiale.*

**Risposta**: questa funzione non è attualmente disponibile. Tuttavia, i tecnici Microsoft stanno lavorando per introdurre questa funzionalità in Visione artificiale.

-----

**Domanda**: *è possibile usare Visione artificiale in locale senza una connessione a Internet?*

**Risposta**: attualmente non è disponibile una soluzione locale.

-----

**Domanda**: *quali lingue sono supportate da Visione artificiale?*

**Risposta**: le lingue supportate includono:

| | | Lingue supportate | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Danese (da-DK)  | Olandese (nl-NL)     | Inglese           | Finlandese (fi-FI)            |Francese (fr-FR)
| Tedesco (de-DE)  | Greco (el-GR)     | Ungherese (hu-HU) | Italiano (it-IT)            | Giapponese (ja-JP)
| Coreano (ko-KR)  | Norvegese (nb-NO) | Polacco (pl-PL)    | Portoghese (pt-BR) (pt-PT) | Russo (ru-RU)
| Spagnolo (es-ES)   | Svedese (sv-SV)     | Turco (tr-TU)   |                            |

-----

**Domanda**: *è possibile usare Visione artificiale per leggere targhe?*

**Risposta**: l'API Visione artificiale offre un buon livello di rilevamento del testo con OCR, ma non è attualmente ottimizzata per le targhe. Microsoft si impegna a migliorare costantemente i servizi e il riconoscimento OCR delle targhe automobilistiche è stato aggiunto all'elenco delle richieste di funzionalità.

-----

**Domanda:** *quali lingue sono supportate per il riconoscimento della grafia?*

**Risposta**: attualmente è supportata solo la lingua inglese.

-----

**Domanda**: *quali superfici di scrittura sono supportate per il riconoscimento della grafia?*

**Risposta**: la tecnologia supporta diversi tipi di superfici, inclusi lavagne, carta bianca e foglietti adesivi gialli.

-----

**Domanda**: *quanto tempo richiede l'operazione di riconoscimento della grafia?*

**Risposta**: la quantità di tempo richiesta dipende dalla lunghezza del testo. Per i testi più lunghi, possono essere necessari vari secondi. Pertanto, al termine dell'operazione Recognize Handwritten Text (Riconosci testo scritto a mano) potrebbe essere necessario attendere prima di poter recuperare i risultati tramite l'operazione Get Handwritten Text Operation Result (Ottieni risultato operazione riconoscimento testo scritto a mano).

-----

**Domanda**: *in quale modo la tecnologia di riconoscimento della grafia gestisce il testo inserito tramite un punto di inserimento all'interno di una riga?*

**Risposta**: il testo di questo tipo viene restituito come riga separata dall'operazione di riconoscimento della grafia.

-----

**Domanda**: *in quale modo la tecnologia di riconoscimento della grafia gestisce le parole o le righe barrate?*

**Risposta**: se le parole sono barrate con più righe per renderle irriconoscibili, l'operazione di riconoscimento della grafia non le preleva. Tuttavia, se le parole sono barrate con una sola riga, la barratura viene considerata un elemento di disturbo e le parole vengono prelevate dall'operazione di riconoscimento della grafia.

-----

**Domanda**: *quali orientamenti del testo sono supportati per la tecnologia di riconoscimento della grafia?*

**Risposta**: l'operazione di riconoscimento della grafia può prelevare testo con angolazioni fino a 30-40 gradi.

-----

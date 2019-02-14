---
title: Domande frequenti - Visione artificiale
titlesuffix: Azure Cognitive Services
description: Risposte alle domande frequenti sull'API Visione artificiale in Servizi cognitivi di Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e1bc40f10547eb6c79a41cab8a3ac5fd2f500415
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874079"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Domande frequenti sull'API Visione artificiale

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Visione artificiale su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/)

-----

**Domanda:** *È possibile eseguire il training dell'API Visione artificiale per l'uso di tag personalizzati?  Ad esempio, se si vogliono aggiungere immagini di razze di gatti per eseguire il training dell'intelligenza artificiale e quindi ottenere il valore della razza per una richiesta di intelligenza artificiale.*

**Risposta:** Questa funzione attualmente non è disponibile. Tuttavia, i tecnici Microsoft stanno lavorando per introdurre questa funzionalità in Visione artificiale.

-----

**Domanda:** *È possibile usare Visione artificiale localmente senza una connessione a Internet?*

**Risposta:** Attualmente non è disponibile un'opzione locale.

-----

**Domanda:** *Quali lingue sono supportate con Visione artificiale?*

**Risposta:** Le lingue supportate comprendono:

| | | Lingue supportate | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Danese (da-DK)  | Olandese (nl-NL)     | Inglese           | Finlandese (fi-FI)            |Francese (fr-FR)
| Tedesco (de-DE)  | Greco (el-GR)     | Ungherese (hu-HU) | Italiano (it-IT)            | Giapponese (ja-JP)
| Coreano (ko-KR)  | Norvegese (nb-NO) | Polacco (pl-PL)    | Portoghese (pt-BR) (pt-PT) | Russo (ru-RU)
| Spagnolo (es-ES)   | Svedese (sv-SV)     | Turco (tr-TR)   |                            |

-----

**Domanda:** *È possibile usare Visione artificiale per leggere targhe?*

**Risposta:** L'API Visione artificiale offre un buon livello di rilevamento del testo con OCR, ma non è attualmente ottimizzata per le targhe. Microsoft si impegna a migliorare costantemente i servizi e il riconoscimento OCR delle targhe automobilistiche è stato aggiunto all'elenco delle richieste di funzionalità.

-----

**Domanda:** *Quali lingue sono supportate per il riconoscimento della grafia?*

**Risposta:** Attualmente è supportata solo la lingua inglese.

-----

**Domanda:** *Quali superfici di scrittura sono supportate per il riconoscimento della grafia?*

**Risposta:** La tecnologia supporta diversi tipi di superfici, inclusi lavagne, carta bianca e foglietti adesivi gialli.

-----

**Domanda:** *Quanto tempo richiede l'operazione di riconoscimento della grafia?*

**Risposta:** La quantità di tempo richiesta dipende dalla lunghezza del testo. Per i testi più lunghi, possono essere necessari vari secondi. Pertanto, al termine dell'operazione Recognize Handwritten Text (Riconosci testo scritto a mano) potrebbe essere necessario attendere prima di poter recuperare i risultati tramite l'operazione Get Handwritten Text Operation Result (Ottieni risultato operazione riconoscimento testo scritto a mano).

-----

**Domanda:** *In quale modo la tecnologia di riconoscimento della grafia gestisce il testo inserito tramite un punto di inserimento all'interno di una riga?*

**Risposta:** Il testo di questo tipo viene restituito come riga separata dall'operazione di riconoscimento della grafia.

-----

**Domanda:** *In quale modo la tecnologia di riconoscimento della grafia gestisce le parole o le righe barrate?*

**Risposta:** Se le parole sono barrate con più righe per renderle irriconoscibili, l'operazione di riconoscimento della grafia non le preleva. Tuttavia, se le parole sono barrate con una sola riga, la barratura viene considerata un elemento di disturbo e le parole vengono prelevate dall'operazione di riconoscimento della grafia.

-----

**Domanda:** *Quali orientamenti del testo sono supportati per la tecnologia di riconoscimento della grafia?*

**Risposta:** L'operazione di riconoscimento della grafia può prelevare testo con angolazioni fino a 30-40 gradi.

-----

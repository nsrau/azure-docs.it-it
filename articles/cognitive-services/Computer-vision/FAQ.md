---
title: Domande frequenti - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Risposte alle domande frequenti sull'API Visione artificiale in Servizi cognitivi di Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68564599"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Domande frequenti sull'API Visione artificiale

> [!TIP]
> Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Visione artificiale su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/)

---

**Domanda**: è *possibile eseguire il training di API visione artificiale per usare tag personalizzati?  Si desidera, ad esempio, inserire le immagini delle razze cat per eseguire il training dell'intelligenza artificiale, quindi ricevere il valore di generazione su una richiesta di intelligenza artificiale.*

**Risposta**: questa funzione non è attualmente disponibile. Tuttavia, i tecnici Microsoft stanno lavorando per introdurre questa funzionalità in Visione artificiale.

---

**Domanda**: *è possibile usare Visione artificiale in locale senza una connessione a Internet?*

**Risposta**: attualmente non è disponibile una soluzione locale.

---

**Domanda**: *è possibile usare Visione artificiale per leggere targhe?*

**Risposta**: l'API Visione artificiale offre un buon livello di rilevamento del testo con OCR, ma non è attualmente ottimizzata per le targhe. Microsoft si impegna a migliorare costantemente i servizi e il riconoscimento OCR delle targhe automobilistiche è stato aggiunto all'elenco delle richieste di funzionalità.

---

**Domanda**: *quali superfici di scrittura sono supportate per il riconoscimento della grafia?*

**Risposta**: la tecnologia supporta diversi tipi di superfici, inclusi lavagne, carta bianca e foglietti adesivi gialli.

---

**Domanda**: *quanto tempo richiede l'operazione di riconoscimento della grafia?*

**Risposta**: la quantità di tempo richiesta dipende dalla lunghezza del testo. Per i testi più lunghi, possono essere necessari vari secondi. Pertanto, al termine dell'operazione Recognize Handwritten Text (Riconosci testo scritto a mano) potrebbe essere necessario attendere prima di poter recuperare i risultati tramite l'operazione Get Handwritten Text Operation Result (Ottieni risultato operazione riconoscimento testo scritto a mano).

---

**Domanda**: *in quale modo la tecnologia di riconoscimento della grafia gestisce il testo inserito tramite un punto di inserimento all'interno di una riga?*

**Risposta**: il testo di questo tipo viene restituito come riga separata dall'operazione di riconoscimento della grafia.

---

**Domanda**: *in quale modo la tecnologia di riconoscimento della grafia gestisce le parole o le righe barrate?*

**Risposta**: se le parole sono barrate con più righe per renderle irriconoscibili, l'operazione di riconoscimento della grafia non le preleva. Tuttavia, se le parole sono barrate con una sola riga, la barratura viene considerata un elemento di disturbo e le parole vengono prelevate dall'operazione di riconoscimento della grafia.

---

**Domanda**: *quali orientamenti del testo sono supportati per la tecnologia di riconoscimento della grafia?*

**Risposta**: l'operazione di riconoscimento della grafia può prelevare testo con angolazioni fino a 30-40 gradi.

---

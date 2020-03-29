---
title: Concetti relativi alla progettazione con simulazione dell'acustica
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale spiega in che modo Project Acoustics incorpora la simulazione acustica al processo di sound design.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854302"
---
# <a name="project-acoustics-design-process-concepts"></a>Concetti relativi al processo di progettazione dell'acustica del progettoProject Acoustics Design Process Concepts

Questa panoramica concettuale spiega in che modo Project Acoustics incorpora la simulazione acustica fisica nel processo di sound design.

## <a name="sound-design-with-audio-dsp-parameters"></a>Sound design con parametri DSP audio

I titoli interattivi 3D ottengono il loro particolare suono utilizzando blocchi DSP (audio Digital Signal Processing) ospitati in un motore audio. Questi blocchi variano in complessità dalla semplice miscelazione, al riverbero, eco, ritardo, equalizzazione, compressione e limitazione, e altri effetti. La selezione, l'organizzazione e l'impostazione di parametri su questi effetti è responsabilità del sound designer, che costruisce un grafico audio che raggiunge gli obiettivi estetici e di gioco dell'esperienza.

In un titolo interattivo, mentre i suoni e l'ascoltatore si muovono nello spazio 3D, come si adattano questi parametri alle condizioni mutevoli? Il sound designer spesso dispone volumi in tutto lo spazio che sono programmati per attivare le modifiche dei parametri per ottenere cambiamenti negli effetti di riverbero, per esempio, o per anatra suoni nel mix come l'ascoltatore si sposta da una parte della scena all'altra. Sono inoltre disponibili sistemi acustici in grado di automatizzare alcuni di questi effetti.

I titoli 3D utilizzano sistemi di illuminazione e fisica cinetica che sono motivati dalla fisica ma regolati dal designer per raggiungere un mix di obiettivi di immersione e di gioco. Una finestra di progettazione visiva non imposta i valori dei singoli pixel, ma regola i modelli 3D, i materiali e i sistemi di trasporto leggero che sono tutti fisicamente basati per scambiare l'estetica visiva e i costi della CPU. Quale sarebbe il processo equivalente per l'audio? Project Acoustics è un primo passo nell'esplorazione di questa domanda. Per prima cosa toccheremo cosa significa trasportare energia acustica attraverso uno spazio.

![Screenshot della scena AltSpace sovrapposta a zone di riverbero](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Risposte d'impulso: Collegare acusticamente due punti nello spazio

Se si ha familiarità con il design audio, si può avere familiarità con le risposte agli impulsi acustici. Una risposta all'impulso acustico modella il trasporto di un suono da una sorgente a un ascoltatore. Pertanto, una risposta agli impulsi può catturare ogni effetto interessante dell'acustica della stanza come l'occlusione e il riverbero. Le risposte Impulse hanno anche alcune proprietà potenti che consentono la scala degli effetti DSP audio. L'aggiunta di due segnali audio insieme e l'elaborazione con una risposta all'impulso dà lo stesso risultato dell'applicazione della risposta all'impulso separatamente a ciascun segnale e l'aggiunta dei risultati. Anche la propagazione acustica e le risposte agli impulsi non dipendono dall'audio in fase di elaborazione, ma solo dalla scena da modellare e dalle posizioni di origine e listener. In breve, una risposta all'impulso distilla l'effetto della scena sulla propagazione del suono.

Una risposta agli impulsi cattura ogni interessante effetto acustico della stanza, e possiamo applicarlo all'audio in modo efficiente con un filtro, e possiamo ottenere risposte agli impulsi dalla misurazione o dalla simulazione. Ma cosa succede se non vogliamo che l'acustica corrisponda esattamente alla fisica, ma piuttosto modellarla per soddisfare le esigenze emotive di una scena? Ma proprio come i valori dei pixel, una risposta agli impulsi è solo una lista di migliaia di numeri, come possiamo regolarla per soddisfare le esigenze estetiche? E se vogliamo avere occlusione/ostruzione che varia senza intoppi mentre passa attraverso le porte o dietro gli ostacoli, quante risposte agli impulsi abbiamo bisogno per ottenere un effetto liscio? Cosa succede se la sorgente si muove velocemente? Come interpoliamo?

Sembra difficile usare la simulazione e le risposte agli impulsi per alcuni aspetti dell'acustica nei titoli interattivi. Ma possiamo ancora costruire un sistema di trasporto audio che supporta le regolazioni del progettista se siamo in grado di collegare le nostre risposte agli impulsi dalla simulazione con i nostri parametri di effetto DSP audio familiari.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Collegamento della simulazione al DSP audio con i parametri

Una risposta d'impulso contiene ogni interessante (e ogni effetto acustico poco interessante). I blocchi DSP audio, quando i loro parametri sono impostati correttamente, possono rendere interessante effetto acustico. L'utilizzo della simulazione acustica per guidare un blocco DSP audio per automatizzare il trasporto audio in una scena 3D è solo una questione di misurazione dei parametri DSP audio da una risposta all'impulso. Questa misurazione è ben compresa per alcuni effetti acustici comuni e importanti, tra cui l'occlusione, l'ostruzione, la portalling e il riverbero.

Ma se la simulazione è collegata direttamente ai parametri DSP audio, dov'è la regolazione del progettista? Cosa abbiamo guadagnato? Bene, otteniamo una notevole quantità di memoria scaricando le risposte agli impulsi e mantenendo alcuni parametri DSP. E per dare al progettista un po'di potere sul risultato finale, abbiamo solo bisogno di trovare un modo per inserire il progettista tra la simulazione e l'audio DSP.

![Grafico con risposta all'impulso stilizzato con parametri sovrapposti](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Sound design trasformando i parametri DSP audio dalla simulazione

Considera l'effetto che i tuoi occhiali da sole hanno sulla tua visione del mondo. In una giornata luminosa, gli occhiali possono ridurre la lucentezza a qualcosa di più confortevole. In una stanza buia, potresti non essere in grado di vedere nulla. Gli occhiali non impostano un certo livello di luminosità in tutte le situazioni; fanno solo tutto più scuro.

Se usiamo la simulazione per guidare il nostro DSP audio utilizzando parametri di occlusione e riverbero, possiamo aggiungere un filtro dopo il simulatore per regolare i parametri che il DSP 'vede'. Il filtro non imporresti un certo livello di occlusione o lunghezza della coda del riverbero, proprio come gli occhiali da sole non fanno di ogni stanza la stessa luminosità. Il filtro potrebbe solo rendere ogni occluder occlude meno. O occludere di più. Aggiungendo e regolando un filtro di parametro di occlusione "darkening", le stanze grandi e aperte avrebbero ancora poco o nessun effetto di occlusione, mentre le porte aumenterebbero da un effetto medio a un forte effetto di occlusione, mantenendo la levigatezza nelle transizioni degli effetti che la simulazione fornisce.

In questo paradigma, l'attività del progettista cambia dalla scelta dei parametri acustici per ogni situazione, alla selezione e alla regolazione dei filtri da applicare ai parametri DSP più importanti provenienti dalla simulazione. Eleva le attività del progettista dalle strette preoccupazioni di impostare transizioni fluide alle maggiori preoccupazioni dell'intensità degli effetti di occlusione e riverbero e della presenza di fonti nel mix. Naturalmente, quando la situazione lo richiede, un filtro sempre disponibile è semplicemente tornare a scegliere i parametri DSP per una fonte specifica in una situazione specifica.

## <a name="sound-design-in-project-acoustics"></a>Sound design in Project Acoustics

Il pacchetto Project Acoustics integra ciascuno dei componenti descritti in precedenza: un simulatore, un codificatore che estrae i parametri e costruisce l'asset acustico, il DSP audio e una selezione di filtri. Sound design con Project Acoustics comporta la scelta dei parametri per i filtri che regolano i parametri di occlusione e riverbero derivati dalla simulazione e applicati al DSP audio, con controlli dinamici esposti all'interno dell'editor di gioco e del motore audio.

## <a name="next-steps"></a>Passaggi successivi
* Prova il paradigma di progettazione utilizzando la [guida introduttiva di Project Acoustics per Unity](unity-quickstart.md) o la Guida [introduttiva all'acustica](unreal-quickstart.md) del progetto per Unreal
* Esplora i controlli di [progettazione dell'acustica](unity-workflow.md) del progetto per Unity o i controlli di [progettazione Project Acoustics per Unreal](unreal-workflow.md)


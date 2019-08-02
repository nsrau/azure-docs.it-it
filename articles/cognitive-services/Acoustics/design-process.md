---
title: Concetti relativi alla progettazione con simulazione dell'acustica
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale illustra come il progetto Acoustics incorpora una simulazione acustica per il processo di progettazione del suono.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: c7e6f17d3e7b9712dd853bcf309bb73fa10ac156
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704846"
---
# <a name="project-acoustics-design-process-concepts"></a>Concetti di progettazione del processo acustico di Project

Questa panoramica concettuale illustra come il progetto Acoustics incorpora una simulazione acustica fisica nel processo di progettazione del suono.

## <a name="sound-design-with-audio-dsp-parameters"></a>Progettazione audio con parametri audio DSP

i titoli 3D interattivi raggiungono un suono particolare usando blocchi DSP (audio Digital Signal Processing) ospitati in un motore audio. Questi blocchi sono limitati dalla semplice combinazione a riverberazione, eco, ritardo, uguaglianza, compressione e limitazione e altri effetti. La selezione, la disposizione e l'impostazione di parametri su questi effetti sono responsabilità della finestra di progettazione audio, che crea un grafico audio che raggiunge gli obiettivi estetici e di gioco dell'esperienza.

In un titolo interattivo, quando i suoni e i listener si muovono nello spazio 3D, in che modo questi parametri si adattano alle condizioni mutevoli? La finestra di progettazione audio spesso disporrà i volumi in tutto lo spazio programmati per attivare le modifiche dei parametri per ottenere le modifiche negli effetti di riverberazione, ad esempio, o per i suoni Duck nella combinazione quando il listener si sposta da una parte della scena a un'altra. Sono disponibili anche sistemi acustici che consentono di automatizzare alcuni di questi effetti.

i titoli 3D usano sistemi di illuminazione e di fisica cinematica, che sono motivati dalla fisica ma sono regolati da designer per ottenere una combinazione di obiettivi di coinvolgimento e gioco. Una finestra di progettazione visiva non imposta i singoli valori dei pixel, bensì regola i modelli 3D, i materiali e i sistemi di trasporto leggeri, che sono tutti fisicamente in grado di commettere i costi estetici visivi e della CPU. Quale sarebbe il processo equivalente per l'audio? Il progetto Acoustics è un primo passaggio nell'esplorazione di questa domanda. Prima di tutto verrà illustrato il significato del trasporto di energia acustica tramite uno spazio.

![Screenshot della scena AltSpace sovrapposta alle zone di riverbero](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Risposte impulsive: Connessione acustica di due punti nello spazio

Se si ha familiarità con la progettazione audio, è possibile che si abbia familiarità con le risposte all'impulso acustico. Una risposta impulso acustico modella il trasporto di un suono da un'origine a un listener. Pertanto, una risposta impulsiva può acquisire tutti gli effetti interessanti dell'acustica della stanza, ad esempio l'occlusione e la riverberazione. Le risposte Impulse hanno anche determinate proprietà potenti che consentono di ridimensionare gli effetti DSP audio. L'aggiunta di due segnali audio insieme e l'elaborazione con una risposta impulsiva genera lo stesso risultato dell'applicazione della risposta Impulse separatamente a ogni segnale e l'aggiunta dei risultati. Anche le risposte di propagazione e impulso acustico non dipendono dall'audio in fase di elaborazione, ma solo sulla scena modellata e le posizioni di origine e di listener. In breve, una risposta impulsiva distilla l'effetto della scena sulla propagazione del suono.

Una risposta impulsiva acquisisce ogni effetto acustico di una stanza interessante ed è possibile applicarla all'audio in modo efficiente con un filtro e ottenere risposte impulsive dalla misurazione o dalla simulazione. Ma cosa accade se non si vuole che l'acustica corrisponda esattamente alla fisica, ma piuttosto la modellazione per soddisfare le esigenze emotive di una scena? Per quanto riguarda i valori dei pixel, una risposta impulsiva è semplicemente un elenco di migliaia di numeri, come è possibile regolarlo per soddisfare le esigenze estetiche? E cosa accade se si vuole avere un'occlusione o un ostacolo che varia in modo uniforme passando attraverso le porte o dietro gli ostacoli, il numero di risposte impulsive necessarie per ottenere un effetto uniforme? Cosa accade se l'origine si sposta velocemente? Come si interpolano?

Sembra difficile usare la simulazione e le risposte impulsive per alcuni aspetti di acustica nei titoli interattivi. Tuttavia, è comunque possibile creare un sistema di trasporto audio che supporta le regolazioni della finestra di progettazione se è possibile connettere le nostre risposte impulsive dalla simulazione con i nostri comuni parametri effetti DSP audio.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Connessione della simulazione al DSP audio con parametri

Una risposta impulsiva contiene ogni effetto acustico interessante (e non interessante). I blocchi DSP audio, quando i parametri sono impostati correttamente, possono eseguire il rendering di un effetto acustico interessante. L'uso della simulazione acustica per guidare un blocco DSP audio per automatizzare il trasporto audio in una scena 3D è solo la misurazione dei parametri audio DSP da una risposta di impulso. Questa misurazione è ben riconosciuta per determinati effetti acustici comuni e importanti, tra cui occlusione, ostruzione, portabilità e riverberazione.

Ma se la simulazione è connessa direttamente ai parametri audio DSP, dove è la regolazione della finestra di progettazione? Cosa è stato acquisito? In questo modo, si ottiene una quantità significativa di memoria eliminando le risposte impulsive e mantenendo alcuni parametri DSP. E per dare alla finestra di progettazione una certa potenza rispetto al risultato finale, è necessario trovare solo un modo per inserire la finestra di progettazione tra la simulazione e il DSP audio.

![Grafico con risposta impulsiva stilizzata con parametri sovrapposti](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Progettazione sonora mediante la trasformazione di parametri audio DSP dalla simulazione

Prendere in considerazione l'effetto degli occhialini sulla propria visione del mondo. In una giornata brillante, gli occhiali possono ridurre la luminosità a qualcosa di più comodo. In una stanza scura, potrebbe non essere possibile vedere niente. Gli occhiali non impostano un certo livello di luminosità in tutte le situazioni. si tratta semplicemente di una cosa più scura.

Se si usa la simulazione per guidare il DSP audio usando i parametri di occlusione e riverberazione, è possibile aggiungere un filtro dopo il simulatore per modificare i parametri che il DSP "vede". Il filtro non impone un certo livello di occlusione o lunghezza della coda del riverbero, molto simile a quello degli occhiali che non rendono ogni stanza la stessa luminosità. Il filtro potrebbe fare in modo che ogni occlusione occludere less. O occludere. Grazie all'aggiunta e alla regolazione di un filtro di parametri di occlusione "oscuramento", le stanze aperte di grandi dimensioni non hanno alcun effetto sull'occlusione, mentre le porte aumentano da media a un effetto di occlusione forte, mantenendo al tempo stesso la fluidità nelle transizioni effettivi fornito dalla simulazione.

In questo paradigma, l'attività della finestra di progettazione passa dalla scelta dei parametri acustici per ogni situazione, alla selezione e alla regolazione dei filtri da applicare ai parametri DSP più importanti provenienti dalla simulazione. Eleva le attività della finestra di progettazione dalle strette preoccupazioni legate alla configurazione di transizioni uniformi ai problemi più elevati relativi all'intensità degli effetti di occlusione e riverberazione e alla presenza di origini nella combinazione. Naturalmente, quando la situazione richiede, un filtro sempre disponibile è semplicemente tornare alla scelta dei parametri DSP per un'origine specifica in una situazione specifica.

## <a name="sound-design-in-project-acoustics"></a>Progettazione audio nell'acustica del progetto

Il pacchetto Acoustics del progetto integra ognuno dei componenti descritti in precedenza: un simulatore, un codificatore che estrae i parametri e compila l'asset acustico, il DSP audio e una selezione di filtri. La progettazione audio con l'acustica del progetto comporta la scelta dei parametri per i filtri che regolano i parametri di occlusione e riverberazione derivati dalla simulazione e applicati al DSP audio, con controlli dinamici esposti nell'editor del gioco e nel motore audio.

## <a name="next-steps"></a>Passaggi successivi
* Provare il paradigma di progettazione usando la [Guida introduttiva di Project Acoustics per Unity](unity-quickstart.md) o la [Guida introduttiva di Project Acoustics per Unreal](unreal-quickstart.md)
* Esplorare i [controlli di progettazione di Acoustics Project per Unity](unity-workflow.md) o i [controlli di progettazione Acoustics di Project per Unreal](unreal-workflow.md)


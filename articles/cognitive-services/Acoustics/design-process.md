---
title: Concetti relativi alla progettazione con simulazione acustica
titlesuffix: Azure Cognitive Services
description: Questa panoramica concettuale illustra le modalità progetto acustica incorpora simulazione acustico al processo di progettazione.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: dd27b660dfdd1f4bcec89291b10fd87750ad4c49
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136157"
---
# <a name="project-acoustics-design-process-concepts"></a>Concetti di progetto acustica progettazione processo

Questa panoramica concettuale illustra le modalità progetto acustica incorpora simulazione acustico fisico nel processo di progettazione.

## <a name="sound-design-with-audio-dsp-parameters"></a>Progettazione con i parametri DSP audio

Titoli interattivi 3D raggiungono il suono specifico usando blocchi (DSP) ospitati in un motore di audio di elaborazione dei segnali digitali audio. Queste vanno blocchi in termini di complessità da semplice combinazione, a riverbero, eco, ritardo, compensazione, la compressione e la limitazione e altri effetti. La selezione, la disposizione e impostazione dei parametri questi effetti è responsabilità della finestra di progettazione audio che crea un grafico audio che raggiunge gli obiettivi di estetica e la modalità di gioco dell'esperienza.

In un titolo interattivo, come i suoni e listener spostarsi nello spazio 3D, come questi parametri adattarsi alle mutevoli condizioni? La finestra di progettazione audio organizzerà spesso volumi nello spazio di cui vengono programmati per attivare le modifiche di parametro per ottenere le modifiche in effetti riverbero, ad esempio, o a Duck Typing suoni nella combinazione di quando il listener viene spostato da una parte della scena a altra. I sistemi acustica sono anche disponibili che possono automatizzare alcuni di questi effetti.

Titoli 3D usano sistemi di fisica di illuminazione e cinematica motivata fisica ma progettazione regolate per ottenere una combinazione di obiettivi di full immersion e la modalità di gioco. Finestra di progettazione visiva, non imposta i valori dei singoli pixel, ma piuttosto regola modelli 3D, materiali e sistemi di trasporto chiaro che sono tutti fisicamente in base a compromesso visual estetica e i costi della CPU. Che cosa sarebbe il processo equivalente per l'audio? Progetto acustica è un primo passaggio per l'esplorazione di questa domanda. Prima di tutto parleremo in che cosa significa acustici energia tramite uno spazio di trasporto.

![zone riverbero](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Risposta all'impulso: Acustica che collega due punti nello spazio

Se si ha familiarità con progettazione audio, è possibile conoscere le risposte impulse acustico. Una modello acustico impulse Response, risposta modella il trasporto di un suono da un'origine a un listener. Di conseguenza una impulse Response, risposta possibile acquisire ogni effetto interessano di acustica stanza, ad esempio relative all'occlusione e riverbero. Le risposte Impulse hanno anche determinate proprietà potenti che consentono di effetti DSP audio per la scalabilità. Somma di due segnali audio e l'elaborazione di una risposta all'impulso restituisce lo stesso risultato applicando la risposta all'impulso separatamente per ogni segnale e aggiungendo i risultati. Modello acustica propagazione e le risposte all'impulso non dipendono anche l'audio in fase di elaborazione, solo sulla scena in fase di modellazione e i percorsi di origine e il listener. In breve, una impulse Response, risposta estrae l'effetto della scena sulla propagazione audio.

Una risposta all'impulso acquisisce ogni interessante effetto acustico chat, è possibile applicarlo a audio in modo efficiente con un filtro ed è possibile ottenere le risposte impulse dalla misura o una simulazione. Ma cosa accade se si non desidera piuttosto acustica in modo che corrisponda esattamente la fisica, ma piuttosto l'attenzione in modo che corrisponda le esigenze di una scena emotiva? Ma molto come valori di pixel, una risposta all'impulso è semplicemente un elenco di migliaia di numeri, come possiamo abbiamo eventualmente modificarlo per soddisfare le esigenze estetiche? E cosa accade se si vuole che occlusione/ostruzione che varia in modo uniforme mentre si passano attraverso considerarle o dietro gli ostacoli, il numero di risposte impulse, dobbiamo inserire un effetto smooth? Cosa accade se l'origine si sposta rapidamente? Come abbiamo interpolare?

Sembra difficile da usare simulazione e le risposte impulse alcuni aspetti della acustica nei titoli interattivi. Ma è comunque possibile creare un sistema audio di trasporto che supporta le regolazioni della finestra di progettazione se sarà possibile connettersi i tempi di risposta all'impulso di simulazione con i parametri di effetto DSP audio familiari.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>La connessione di simulazione per DSP audio con parametri

Una risposta all'impulso contiene ogni interessanti (e ogni non interessanti) effetto acustici. Blocchi DSP audio, quando i relativi parametri sono impostati correttamente, possono rendere interessante effetto acustici. Usa simulazione acustici per indirizzare un blocco DSP audio per automatizzare audio trasporto in una scena 3D è solo una questione di misurare i parametri DSP audio da una risposta all'impulso. Questa misurazione sia ben chiaro per determinati effetti acustici importanti e comuni tra cui occlusione ostruzione, portalling e riverbero.

Ma se la simulazione è connesso direttamente ai parametri DSP audio, in cui è la regolazione della finestra di progettazione? Che cosa abbiamo un guadagno? Bene, abbiamo un guadagno una quantità significativa di memoria indietro di rimuovendo le risposte impulse e mantenendo alcuni parametri DSP. E per offrire la finestra di progettazione energia alcuni sul risultato finale, è solo necessario trovare un modo per inserire la finestra di progettazione tra la simulazione e DSP audio.

![parametri di risposta all'impulso](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Progettazione trasformando i parametri DSP audio di simulazione

Prendere in considerazione l'effetto che di occhiali da sole sono sulla visualizzazione del mondo. In un giorno brillante, bicchiere possono ridurre la forma di luminosità su un valore più a proprio agio. In una stanza scura, potrebbe non essere in grado di visualizzare i risultati affatto. I bicchieri non vengono impostate un certo livello di luminosità in tutte le situazioni. rendono semplicemente tutti gli elementi più scura.

Se si utilizza una simulazione per guidare i nostri DSP audio usando parametri occlusione e riverbero, possiamo aggiungere un filtro dopo il simulatore per regolare i parametri che DSP 'rileva'. Il filtro non applicare un certo livello di occlusione né riverbero molto lunghezza della parte finale, ad esempio occhiali da sole non apportare tutte le stanze la luminosità stesso. Il filtro potrebbe essere sufficiente Assicurarsi ogni occluder nasconde i colori minore. O nasconde i colori più. Aggiungendo e modificando un filtro di parametro occlusione 'scurimento', stanze di grandi dimensioni, aprire non sarebbe comunque necessario praticamente alcun effetto, relative all'occlusione mentre considerarle aumenterebbe da un supporto per un effetto occlusione sicuro, mantenendo la rotondità in vigore transizioni che fornisce la simulazione.

In questo paradigma, attività della finestra di progettazione cambia da scelta acustici parametri per ogni situazione, alla selezione e regolando i filtri da applicare per i parametri più importanti DSP provenienti da simulazione. Le attività della finestra di progettazione eleva dagli aspetti più stretto della configurazione di transizioni per i problemi di maggiore dell'intensità dell'occlusione e riverbero effetti e la presenza di origini nella combinazione di. Naturalmente, quando la situazione richiede, un filtro è sempre disponibile è sufficiente tornare indietro per scegliere i parametri DSP per un'origine specifica in una situazione specifica.

## <a name="sound-design-in-project-acoustics"></a>Progettazione in acustica progetto

I pacchetto progetto acustica integra ognuno dei componenti descritti in precedenza: un simulatore, un codificatore che estrae i parametri e compila l'asset acustica DSP audio e una selezione di filtri. Progettazione con progetto acustica comporta scegliendo i parametri per i filtri che regolare i parametri occlusione e riverbero derivato dalla simulazione e applicare a DSP audio, con controlli dinamici esposti all'interno di editor di gioco e il motore di audio.

## <a name="next-steps"></a>Passaggi successivi
* Provare il paradigma di progettazione tramite le [acustica progetto di avvio rapido per Unity](unity-quickstart.md) o il [acustica progetto di avvio rapido per Unreal](unreal-quickstart.md)
* Esplorare le [acustica progetto di progettazione controlli per Unity](unity-workflow.md) o il [acustica progetto di progettazione controlli per Unreal](unreal-workflow.md)


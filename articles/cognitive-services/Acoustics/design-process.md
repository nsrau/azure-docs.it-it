---
title: Panoramica del processo di progettazione per Project Acoustics
titlesuffix: Azure Cognitive Services
description: Questo documento descrive come esprimere la finalità della progettazione in tutte e tre le fasi del flusso di lavoro di Project Acoustics.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 46e2e276086f836ff881fde1db6462f6e7788e22
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901363"
---
# <a name="design-process-overview"></a>Panoramica del processo di progettazione
È possibile esprimere la finalità della progettazione in tutte e tre le fasi del flusso di lavoro di Project Acoustics: impostazione scena pre-bake, posizionamento della sorgente sonora e progettazione post-bake. Il processo richiede meno markup associato al posizionamento dei volumi di riverbero pur mantenendo il controllo sull'audio di una scena.

## <a name="pre-bake-design"></a>Progettazione pre-bake
Il processo di impostazione scena pre-bake genera la scena e i metadati usati per la simulazione delle onde sonore, che include la selezione degli elementi della scena che faranno parte della simulazione per fornire occlusioni, riflessi e riverbero. I metadati per la scena sono costituiti dalla selezione dei materiali acustici per ogni elemento della scena. I materiali acustici controllano la quantità di energia sonora riflessa da ogni superficie.

Il coefficiente di assorbimento predefinito per tutte le superfici è pari a 0,04, che corrisponde a una riflessione elevata. È possibile ottenere effetti estetici e di gioco regolando in tutta la scena i coefficienti di assorbimento di materiali diversi, che sono particolarmente importanti per gli ascoltatori quando sentono le transizioni da un'area a un'altra della scena. Ad esempio, passando da una stanza con riverbero scuro a una con riverbero chiaro, la scena esterna senza riverbero migliora l'impatto della transizione. Per ottenere questo effetto, aumentare i coefficienti di assorbimento nei materiali della scena esterna.

Il tempo di riverbero di un determinato materiale in una stanza è inversamente proporzionale al coefficiente di assorbimento e la maggior parte dei materiali ha valori di assorbimento compresi tra 0,01 e 0,20. I materiali con coefficienti di assorbimento non compresi in questo intervallo sono molto assorbenti.

![Grafico del tempo di riverbero](media/ReverbTimeGraph.png)

La [procedura dettagliata sull'interfaccia utente di bake](bake-ui-walkthrough.md) descrive in dettaglio i controlli pre-bake.

## <a name="sound-source-placement"></a>Posizionamento della sorgente sonora
Visualizzare i voxel e i punti di probe in fase di esecuzione consente di eseguire il debug dei problemi relativi alle fonti sonore bloccate all'interno della geometria voxelizzata. Per attivare/disattivare la visualizzazione della griglia di voxel e dei punti di probe, fare clic sulla casella di controllo corrispondente nel menu Gizmos, in alto a destra della visualizzazione della scena. Se la sorgente sonora è all'interno di un wall voxel, spostarla in un air voxel.

![Menu Gizmos](media/GizmosMenu.png)  

La visualizzazione dei voxel consente di determinare se ai componenti visivi del gioco è applicata una trasformazione. In questo caso, applicare la stessa trasformazione all'elemento GameObject che ospita **Acoustics Manager**.

### <a name="voxel-size-discrepancies"></a>Discrepanze nella dimensione dei voxel
È possibile notare che la dimensione dei voxel utilizzati per illustrare quali dei mesh della scena partecipano al bake acustico è diversa nelle viste di runtime e della fase di progettazione. Questa differenza non influisce sulla qualità e la granularità della frequenza di simulazione selezionata, ma è piuttosto un effetto collaterale dell'uso della scena con voxel al runtime. Al runtime i voxel di simulazione vengono "affinati" per supportare l'interpolazione tra le posizioni di origine. Questo permette anche, in fase di progettazione, di posizionare origini audio più vicine ai mesh della scena rispetto a quanto consentito dalla dimensione dei voxel di simulazione, poiché le origini all'interno di un voxel che contengono un mesh trattato per l'acustica non emettono alcun suono.

Ecco due immagini che mostrano la differenza tra voxel di progettazione (pre-bake) e voxel di runtime (post-bake) come sono visualizzati dal plug-in Unity:

Voxel della fase di progettazione:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Voxel di runtime:

![VoxelsRuntime](media/VoxelsRuntime.png)

Per stabilire se il mesh dei voxel rappresenti in modo accurato o meno i mesh di architettura e struttura della scena è necessario usare i voxel della modalità progettazione e non la vista di runtime dei voxel affinati.

## <a name="post-bake-design"></a>Progettazione post-bake
I risultati del bake vengono archiviati nel file ACE come parametri di occlusione e riverbero per tutte le coppie sorgente-posizione ascoltatori nell'intera scena. Questo risultato fisicamente accurato può essere usato per il progetto così com'è ed il punto iniziale ideale per la progettazione. Il processo di progettazione post-bake specifica le regole per trasformare i parametri del risultato del bake in fase di esecuzione.

### <a name="distance-based-attenuation"></a>Attenuazione basate sulla distanza
Il DSP audio fornito dal plug-in di spazializzazione di Unity **Microsoft Acoustics** rispetta l'attenuazione basata sulla distanza per ogni sorgente incorporata nell'editor Unity. I controlli per l'attenuazione basata sulla distanza sono nel componente **Audio Source** (Sorgente audio) del pannello **Inspector** (Controllo) delle sorgenti sonore in **3D Sound Settings** (Impostazioni audio 3D):

![Attenuazione della distanza](media/distanceattenuation.png)

I calcoli acustici vengono eseguiti in un'area "di simulazione", un riquadro centrato intorno alla posizione del lettore. Se un'origine audio è distante dal lettore, fuori da quest'area simulazione, solo la geometria all'interno del riquadro influirà sulla propagazione dell'audio, ad esempio causando occlusione, cosa che funziona ragionevolmente bene quando sono presenti elementi di occlusione in prossimità del lettore. Quando il lettore si trova invece in uno spazio aperto, ma gli elementi di occlusione sono vicino all'origine audio distante, la rimozione dell'effetto occlusione può risultare non realistica. La soluzione alternativa suggerita in questi casi è assicurarsi che l'attenuazione dell'audio diminuisca a 0 a circa 45 metri, la distanza orizzontale predefinita dal lettore al lato del riquadro.

### <a name="tuning-scene-parameters"></a>Regolazione dei parametri della scena
Per regolare i parametri per tutte le sorgenti, fare clic sulla barra del canale in **Audio Mixer** (Mixer audio) di Unity e regolare i parametri nell'effetto **Acoustics Mixer** (Mixer acustica).

![Personalizzazione del mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Regolazione dei parametri della sorgente
Collegando lo script **AcousticsAdjust** a un'origine, è possibile regolare i parametri per tale origine. Per collegare lo script, fare clic su **Add Component** (Aggiungi componente) nella parte inferiore del pannello **Inspector** (Controllo) e passare a **Scripts > Acoustics Adjust** (Script > Regolazione acustica). Lo script ha sei controlli:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Enable Acoustics** (Abilita acustica): controlla se l'acustica è applicazione a questa sorgente. Se deselezionato, la sorgente sarà spazializzata con HRTF, ma senza acustica, vale a dire senza parametri di ostruzione, occlusione e riverbero dinamico, ad esempio livello e tempo di decadimento. Il riverbero viene comunque applicato con un livello e un tempo di decadimento fissi.
* **Occlusion** (Occlusione): applicare un moltiplicatore al livello di dB di occlusione calcolato dal sistema acustico. Se questo moltiplicatore è maggiore di 1, l'occlusione sarà estrema; con i valori minori di 1, l'effetto di occlusione è più sottile; il valore 0 disattiva l'occlusione.
* **Transmission (dB)** (Trasmissione (dB)): impostare l'attenuazione, in dB, causata dalla trasmissione attraverso la geometria. Impostare questo dispositivo di scorrimento al livello più basso per disattivare la trasmissione. Il segnale audio iniziale viene spazializzato quando arriva intorno alla geometria della scena (portaling). La trasmissione fornisce un segnale in arrivo aggiuntivo che viene spazializzato nella direzione della linea di visuale. Si noti che viene applicata anche la curva di attenuazione della distanza per l'origine.
* **Wetness (dB)** (Intensità riverbero (dB)): consente di regolare la potenza del riverbero, in dB, in base alla distanza dall'origine. I valori positivi rendono un suono più riverberante, mentre i valori negativi lo rendono più secco. Fare clic sul controllo curva, la linea verde, per visualizzare l'editor della curva. Modificare la curva facendo clic con il pulsante sinistro del mouse per aggiungere punti e trascinando i punti per formare la funzione desiderata. L'asse x è la distanza dall'origine e l'asse y è la regolazione del riverbero in dB. Per altri dettagli su come modificare le curve, vedere questo [Manuale di Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Per riportare la curva alla forma predefinita, fare clic su **Wetness** (Intensità riverbero) e selezionare **Reset** (Ripristina).
* **Decay Time Scale** (Scala tempo di decadimento): regola un moltiplicatore per il tempo di decadimento. Se ad esempio il risultato del bake specifica un tempo di decadimento di 750 millisecondi, ma questo valore è impostato su 1,5, il tempo di decadimento applicato alla sorgente è di 1.125 millisecondi.
* **Outdoorness** (Effetto all'aperto): una regolazione additiva della stima del sistema acustico di quanto il riverbero dovrebbe risultare "esterno" per un'origine. Impostando questa proprietà su 1, l'audio dell'origine apparirà sempre come se fosse all'aperto, mentre impostandola su -1 l'audio apparirà sempre come se fosse al chiuso.

Diverse sorgenti possono richiedere impostazioni diverse per ottenere alcuni effetti estetici o di gioco. Il dialogo è un possibile esempio. L'orecchio umano percepisce meglio il riverbero nel parlato, mentre il dialogo spesso deve essere comprensibile per il gioco. È possibile tenere conto di questo aspetto senza rendere la finestra di dialogo non diegetica spostando **Wetness** (Intensità riverbero) verso il basso, regolando il parametro **Perceptual Distance Warp** (Distorsione della distanza percettiva) descritto di seguito, aumentando **Transmission** (Trasmissione) per ottenere la propagazione dell'audio attraverso le pareti e/o riducendo **Occlusion** (Occlusione) dal valore 1 per ottenere maggiore audio attraverso i portali.

Collegando lo script **AcousticsAdjustExperimental** a un'origine, è possibile regolare parametri sperimentali aggiuntivi per tale origine. Per collegare lo script, fare clic su **Add Component** (Aggiungi componente) nella parte inferiore del pannello **Inspector** (Controllo) e passare a **Scripts > Acoustics Adjust Experimental** (Script > Regolazione acustica sperimentale). Attualmente è disponibile un solo controllo sperimentale:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Perceptual Distance Warp** (Distorsione della distanza percettiva): applicare una distorsione esponenziale alla distanza utilizzata per calcolare il rapporto segnale-riverbero. I livelli di riverbero vengno calcolati per tutto lo spazio e variano in modo uniforme con la distanza creando effetti adeguati di distanza percepita. I valori di distorsione maggiori di 1 esasperano questo effetto aumentando i livelli di riverbero correlati alla distanza con il risultato di un audio "distante", mentre i valori di distorsione minori di 1 applicano una variazione più sottile del riverbero in base alla distanza con il risultato di un audio più "presente".


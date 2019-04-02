---
title: Esercitazione sulla progettazione in Unity per Progetto Acustica
titlesuffix: Azure Cognitive Services
description: Questa esercitazione descrive il flusso di lavoro della progettazione per Progetto Acustica in Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 01783aa12f586f61583b1503c796f9b523770104
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310626"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Esercitazione sulla progettazione in Unity per Progetto Acustica
Questa esercitazione descrive gli strumenti e il flusso di lavoro della progettazione per Progetto Acustica in Unity.

Prerequisiti:
* Unity 2018.2+ per Windows
* Una scena Unity con un asset di acustica di cui è stato effettuato il bake

Per questa esercitazione sono disponibili due modi per ottenere una scena Unity con un asset di acustica di cui è stato effettuato il bake:
* [Aggiungere Progetto Acustica al progetto Unity](unity-integration.md), quindi [ottenere un account Azure Batch](create-azure-account.md) e successivamente [effettuare il bake della scena Unity](unity-baking.md)
* In alternativa, usare il [contenuto di esempio di Progetto Acustica per Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Esaminare i concetti del processo di progettazione
Progetto Acustica usa i metodi di elaborazione del segnale digitale (digital signal processing, DSP) audio comuni per elaborare le sorgenti e consente di controllare proprietà acustiche familiari, tra cui occlusione, mix wet/dry e lunghezza della coda di riverbero (RT60). Il concetto cardine del [processo di progettazione di Progetto Acustica](design-process.md) è che anziché impostare direttamente queste proprietà, si definisce come vengono usati i risultati della simulazione per gestire le proprietà stesse. Le impostazioni predefinite per ogni controllo rappresentano un'acustica fisicamente accurata.

## <a name="design-acoustics-for-each-source"></a>Progettare l'acustica per ogni sorgente
Progetto Acustica fornisce una serie di controlli di progettazione acustica specifici per sorgente. È così possibile controllare il mix in una scena enfatizzando alcune sorgenti e de-enfatizzandone altre.

### <a name="adjust-distance-based-attenuation"></a>Regolare l'attenuazione basata sulla distanza
Il DSP audio fornito dal plug-in di spazializzazione Unity di **Progetto Acustica** rispetta l'attenuazione basata sulla distanza per ogni sorgente predefinita nell'editor Unity. I controlli per l'attenuazione basata sulla distanza sono nel componente **Audio Source** (Sorgente audio) del pannello **Inspector** (Controllo) delle sorgenti sonore in **3D Sound Settings** (Impostazioni audio 3D):

![Screenshot del riquadro di opzioni per l'attenuazione basata sulla distanza in Unity](media/distance-attenuation.png)

I calcoli acustici vengono eseguiti in un'area "di simulazione", un riquadro centrato intorno alla posizione del lettore. Se un'origine audio è distante dal lettore, fuori da quest'area simulazione, solo la geometria all'interno del riquadro influirà sulla propagazione dell'audio, ad esempio causando occlusione, cosa che funziona ragionevolmente bene quando sono presenti elementi di occlusione in prossimità del lettore. Quando il lettore si trova invece in uno spazio aperto, ma gli elementi di occlusione sono vicino all'origine audio distante, la rimozione dell'effetto occlusione può risultare non realistica. La soluzione alternativa suggerita in questi casi è assicurarsi che l'attenuazione dell'audio diminuisca a 0 a circa 45 m, la distanza orizzontale predefinita dal lettore al lato del riquadro.

![Screenshot del pannello di opzioni SpeakerMode in Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Modificare occlusione e trasmissione
Collegando lo script **AcousticsAdjust** a un'origine, è possibile regolare i parametri per tale origine. Per collegare lo script, fare clic su **Add Component** (Aggiungi componente) nella parte inferiore del pannello **Inspector** (Controllo) e passare a **Scripts > Acoustics Adjust** (Script > Regolazione acustica). Lo script ha sei controlli:

![Screenshot dello script AcousticsAdjust in Unity](media/acoustics-adjust.png)

* **Enable Acoustics** (Abilita acustica): controlla se l'acustica è applicazione a questa sorgente. Se deselezionata, l'origine viene spazializzata con HRTF o tramite panoramica, ma non vi sarà alcuna acustica. Ciò significa che non vi sarà alcuna ostruzione, occlusione né parametri di riverbero dinamico, ad esempio livello e tempo di decadimento. Il riverbero viene comunque applicato con un livello e un tempo di decadimento fissi.
* **Occlusion** (Occlusione): applicare un moltiplicatore al livello di dB di occlusione calcolato dal sistema acustico. Se questo moltiplicatore è maggiore di 1, l'occlusione sarà estrema; con i valori minori di 1, l'effetto di occlusione è più sottile; il valore 0 disattiva l'occlusione.
* **Transmission (dB)** (Trasmissione (dB)): impostare l'attenuazione, in dB, causata dalla trasmissione attraverso la geometria. Impostare questo dispositivo di scorrimento al livello più basso per disattivare la trasmissione. Il segnale audio iniziale viene spazializzato quando arriva intorno alla geometria della scena (portaling). La trasmissione fornisce un segnale in arrivo aggiuntivo che viene spazializzato nella direzione della linea di visuale. Si noti che viene applicata anche la curva di attenuazione della distanza per l'origine.

### <a name="adjust-reverberation"></a>Modificare il riverbero
* **Wetness (dB)** (Intensità riverbero (dB)): consente di regolare la potenza del riverbero, in dB, in base alla distanza dall'origine. I valori positivi rendono un suono più riverberante, mentre i valori negativi lo rendono più secco. Fare clic sul controllo curva, la linea verde, per visualizzare l'editor della curva. Modificare la curva facendo clic con il pulsante sinistro del mouse per aggiungere punti e trascinando i punti per formare la funzione desiderata. L'asse x è la distanza dall'origine e l'asse y è la regolazione del riverbero in dB. Per altri dettagli su come modificare le curve, consultare il [Manuale di Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Per riportare la curva alla forma predefinita, fare clic su **Wetness** (Intensità riverbero) e selezionare **Reset** (Ripristina).
* **Decay Time Scale** (Scala tempo di decadimento): regola un moltiplicatore per il tempo di decadimento. Se ad esempio il risultato del bake specifica un tempo di decadimento di 750 millisecondi, ma questo valore è impostato su 1,5, il tempo di decadimento applicato alla sorgente è di 1.125 millisecondi.
* **Outdoorness** (effetto all'aperto): una regolazione additiva della stima del sistema acustico di quanto il riverbero dovrebbe risultare "esterno" per una sorgente. Impostando questo valore su 1, l'audio della sorgente verrà percepito sempre come se fosse completamente all'aperto, mentre impostandolo su -1 l'audio verrà percepito come se fosse completamente al chiuso.

Collegando lo script **AcousticsAdjustExperimental** a un'origine, è possibile regolare parametri sperimentali aggiuntivi per tale origine. Per collegare lo script, fare clic su **Add Component** (Aggiungi componente) nella parte inferiore del pannello **Inspector** (Controllo) e passare a **Scripts > Acoustics Adjust Experimental** (Script > Regolazione acustica sperimentale). Attualmente è disponibile un solo controllo sperimentale:

![Screenshot dello script AcousticsAdjustExperimental in Unity](media/acoustics-adjust-experimental.png)

* **Perceptual Distance Warp** (Distorsione della distanza percettiva): applicare una distorsione esponenziale alla distanza utilizzata per calcolare il rapporto segnale-riverbero. I livelli di riverbero vengno calcolati per tutto lo spazio e variano in modo uniforme con la distanza creando effetti adeguati di distanza percepita. L'alterazione dei valori maggiori di 1 comporta un'esagerazione dell'effetto dovuta all'aumento dei livelli di riverbero relativi alla distanza; ciò rende il suono "distante". L'alterazione dei valori inferiori a 1 rende la modifica del riverbero relativa alla distanza più tenue; ciò rende il suono più "presente".

## <a name="design-acoustics-for-all-sources"></a>Progettare l'acustica per tutte le sorgenti
Per regolare i parametri per tutte le sorgenti, fare clic sulla barra del canale in **Audio Mixer** (Mixer audio) di Unity e regolare i parametri nell'effetto **Project Acoustics Mixer** (Mixer acustica di progetto).

![Screenshot del pannello di personalizzazioni Mixer di Progetto Acustica in Unity](media/mixer-parameters.png)

* **Regolazione intensità riverbero**: consente di regolare la potenza di riverbero, nel database, in tutte le origini della scena in base alla distanza dell'ascoltatore. I valori positivi rendono un suono più riverberante, mentre i valori negativi lo rendono più secco.
* **Scalabilità RT60**: moltiplicazione scalare per tempo di riverbero.
* **Panoramica d'uso**: controlla se l'audio viene restituito come panoramica binaurale (0) o multicanale (1). Qualsiasi valore escluso 1 indica una panoramica binaurale. L'output binaurale è spazializzato con HRTF per l'uso con auricolari, mentre l'output multicanale è spazializzato con VBAP per l'uso con i sistemi di voce surround multicanale. Se si utilizza la panoramica multicanale, assicurarsi di selezionare la modalità altoparlante che corrisponda alle impostazioni del dispositivo, disponibile in **Impostazioni del progetto** > **Audio**.

## <a name="check-proper-sound-source-placement"></a>Verificare il corretto posizionamento delle sorgenti audio
Le sorgenti audio poste all'interno dei voxel occupati non verranno sottoposte a trattamento acustico. Poiché i voxel si estendono oltre la geometria visibile della scena, è possibile posizionare una sorgente all'interno di un voxel quando appare non occlusa dalla geometria visiva. È possibile visualizzare i voxel di Progetto Acustica selezionando la casella di controllo della griglia di voxel nel menu **Gizmos**, nella parte superiore destra della vista **Scene** (Scena).

![Screenshot del menu Gizmos in Unity](media/gizmos-menu.png)  

La visualizzazione dei voxel consente anche di determinare se ai componenti visivi del gioco è applicata una trasformazione. In questo caso, applicare la stessa trasformazione all'elemento GameObject che ospita **Acoustics Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Voxel in fase di bake e di esecuzione
È possibile visualizzare i voxel nella finestra dell'editor in fase di progettazione del gioco e nella finestra del gioco in runtime. Le dimensioni dei voxel differiscono in queste viste perché l'interpolazione acustica in runtime usa una griglia di voxel più fine per risultati di interpolazione più uniformi. Il posizionamento delle sorgenti audio deve essere verificato usando i voxel in runtime.

Voxel della fase di progettazione:

![Screenshot dei voxel di Progetto Acustica in fase di progettazione](media/voxels-design-time.png)

Voxel di runtime:

![Screenshot dei voxel di Progetto Acustica in fase di esecuzione](media/voxels-runtime.png)

## <a name="next-steps"></a>Passaggi successivi
* Esaminare i case study che evidenziano i concetti alla base del [processo di progettazione](design-process.md)


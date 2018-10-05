---
title: Panoramica del processo di progettazione per l'acustica - Servizi cognitivi
description: Questo documento descrive come esprimere la finalità della progettazione in tutte e tre le fasi del flusso di lavoro di Project Acoustics.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 8f594be67c4677fae00cb01598d3899e30dae1e8
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433225"
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

## <a name="post-bake-design"></a>Progettazione post-bake
I risultati del bake vengono archiviati nel file ACE come parametri di occlusione e riverbero per tutte le coppie sorgente-posizione ascoltatori nell'intera scena. Questo risultato fisicamente accurato può essere usato per il progetto così com'è ed il punto iniziale ideale per la progettazione. Il processo di progettazione post-bake specifica le regole per trasformare i parametri del risultato del bake in fase di esecuzione.

### <a name="distance-based-attenuation"></a>Attenuazione basate sulla distanza
Il DSP audio fornito dal plug-in di spazializzazione di Unity **Microsoft Acoustics** rispetta l'attenuazione basata sulla distanza per ogni sorgente incorporata nell'editor Unity. I controlli per l'attenuazione basata sulla distanza sono nel componente **Audio Source** (Sorgente audio) del pannello **Inspector** (Controllo) delle sorgenti sonore in **3D Sound Settings** (Impostazioni audio 3D):

![Attenuazione della distanza](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>Regolazione dei parametri della scena
Per regolare i parametri per tutte le sorgenti, fare clic sulla barra del canale in **Audio Mixer** (Mixer audio) di Unity e regolare i parametri nell'effetto **Acoustics Mixer** (Mixer acustica).

![Personalizzazione del mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Regolazione dei parametri della sorgente
Collegando lo script **AcousticsSourceCustomization** a una sorgente, è possibile regolare i parametri per tale sorgente. Per collegare lo script, fare clic su **Add Component** (Aggiungi componente) nella parte inferiore del pannello **Inspector** (Controllo) e passare a **Scripts > Acoustics Source Customization** (Script > Personalizzazione sorgente acustica). Lo script include tre parametri:

![Personalizzazione della sorgente](media/SourceCustomization.png)

* **Reverb Power Adjust** (Regolazione potenza riverbero): regola la potenza del riverbero, in dB. I valori positivi rendono un suono più riverberante, mentre i valori negativi lo rendono più secco.
* **Decay Time Scale** (Scala tempo di decadimento): regola un moltiplicatore per il tempo di decadimento. Se ad esempio il risultato del bake specifica un tempo di decadimento di 750 millisecondi, ma questo valore è impostato su 1,5, il tempo di decadimento applicato alla sorgente è di 1.125 millisecondi.
* **Enable Acoustics** (Abilita acustica): controlla se l'acustica è applicazione a questa sorgente. Se deselezionato, la sorgente sarà spazializzata con HRTF, ma senza acustica, vale a dire senza parametri di ostruzione, occlusione e riverbero dinamico, ad esempio livello e tempo di decadimento. Il riverbero viene comunque applicato con un livello e un tempo di decadimento fissi.

Diverse sorgenti possono richiedere impostazioni diverse per ottenere alcuni effetti estetici o di gioco. Il dialogo è un possibile esempio. L'orecchio umano percepisce meglio il riverbero nel parlato, mentre il dialogo spesso deve essere comprensibile per il gioco. È possibile considerare questo aspetto, senza rendere il dialogo non diegetico, riducendo la potenza del riverbero.

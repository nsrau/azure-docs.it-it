---
title: Tipi di interazione degli strumenti di disegno e scelte rapide da tastiera sulla mappa Mappe di Microsoft Azure
description: Come disegnare e modificare forme utilizzando mouse, touch screen o tastiera in Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fb8a1e1a8c29086553500bdad2e4604d1e1ef471
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198293"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipi di interazione e scelte rapide da tastiera nel modulo degli strumenti di disegno

Questo articolo descrive tutti i diversi modi per disegnare e modificare le forme utilizzando il mouse, il touchscreen o le scelte rapide da tastiera.

Il gestore di disegni supporta tre diversi modi di interagire con la mappa, per disegnare forme.

* `click`- Le coordinate vengono aggiunte quando si fa clic con il mouse o il tocco.
* `freehand `- Le coordinate vengono aggiunte quando il mouse o il tocco viene trascinato sulla mappa.
* `hybrid`- Le coordinate vengono aggiunte quando si fa clic o si trascina il mouse o il tocco.

## <a name="how-to-draw-shapes"></a>Come disegnare forme

 Prima di disegnare una forma, impostare l'opzione `drawingMode` del gestore di disegni su un'impostazione di disegno supportata. Questa impostazione può essere programmata o richiamata premendo uno dei pulsanti di disegno sulla barra degli strumenti. La modalità di disegno rimane abilitata, anche dopo che è stata disegnata una forma, semplificando il disegno di forme aggiuntive dello stesso tipo. Impostare a livello di codice la modalità di disegno su uno stato di inattività. In alternativa, passare a uno stato di inattività facendo clic sul pulsante delle modalità di disegno correnti sulla barra degli strumenti.

Le sezioni successive illustrano tutti i diversi modi in cui le forme possono essere disegnate sulla mappa.

### <a name="how-to-draw-a-point"></a>Come disegnare un punto

Quando il gestore `draw-point` di disegno è in modalità di disegno, è possibile eseguire le seguenti azioni per disegnare punti sulla mappa. Questi metodi funzionano con tutte le modalità di interazione.

**Inizia a disegnare**
 - Fare clic con il pulsante sinistro del mouse o toccare la mappa per aggiungere un punto alla mappa. 
 - Se il mouse si trova `F` sopra la mappa, premere il tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo fornisce una maggiore precisione per l'aggiunta di un punto alla mappa. Ci sarà meno movimento sul mouse a causa del movimento di pressione del pulsante sinistro del mouse.
 - Continuare a fare clic, toccare o premere `F` per aggiungere altri punti alla mappa.
 
**Termina disegno**
 - Fare clic su un pulsante qualsiasi nella barra degli strumenti di disegno. 
 - Impostare a livello di codice la modalità di disegno. 
 - Premere `C` il tasto .

**Annullare il disegno**
 - Premere `Escape` il tasto .

### <a name="how-to-draw-a-line"></a>Come disegnare una linea

Quando il gestore `draw-line` di disegno è in modalità, è possibile eseguire le seguenti azioni per disegnare punti sulla mappa, a seconda della modalità di interazione.

**Inizia a disegnare**
 - Modalità clic
   * Fare clic con il pulsante sinistro del mouse o toccare la mappa per aggiungere ogni punto di una linea sulla mappa. Una coordinata viene aggiunta alla linea per ogni clic o tocco. 
   * Se il mouse si trova `F` sopra la mappa, premere il tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo fornisce una maggiore precisione per l'aggiunta di un punto alla mappa. Ci sarà meno movimento sul mouse a causa del movimento di pressione del pulsante sinistro del mouse.
   * Continuare a fare clic fino a quando tutti i punti desiderati non sono stati aggiunti alla linea.
 - Modalità a mano libera
   * Premi il pulsante sinistro del mouse o tocca la mappa e trascina il mouse o tocca il punto. Le coordinate vengono aggiunte alla linea quando il mouse o il punto di tocco si sposta sulla mappa. Non appena viene attivato il mouse o l'evento di ritocco, il disegno viene completato. La frequenza con cui vengono aggiunte `freehandInterval` le coordinate è definita dall'opzione dei gestori di disegni.
 - Modalità ibrida
   * Alternare i metodi click e freehand, come desiderato, mentre si disegna una singola linea. Ad esempio, fai clic su alcuni punti, quindi tieni premuto e trascina il mouse per aggiungere un gruppo di punti, quindi fai clic su alcuni altri. 

**Termina disegno**
 - Modalità ibrida/clic
   * Fare doppio clic sulla mappa nell'ultimo punto. 
   * Fare clic su un pulsante qualsiasi nella barra degli strumenti di disegno. 
   * Impostare a livello di codice la modalità di disegno. 
 - Modalità a mano libera
   * Rilasciare il pulsante del mouse o il punto di tocco.
 - Premere `C` il tasto .

**Annullare il disegno**
 - Premere `Escape` il tasto .

### <a name="how-to-draw-a-polygon"></a>Come disegnare un poligono

Quando il gestore `draw-polygon` di disegno è in modalità, è possibile eseguire le seguenti azioni per disegnare punti sulla mappa, a seconda della modalità di interazione.

**Inizia a disegnare**
 - Modalità clic
   * Fare clic con il pulsante sinistro del mouse o toccare la mappa per aggiungere ogni punto di un poligono sulla mappa. Una coordinata viene aggiunta al poligono per ogni clic o tocco. 
   * Se il mouse si trova `F` sopra la mappa, premere il tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo fornisce una maggiore precisione per l'aggiunta di un punto alla mappa. Ci sarà meno movimento sul mouse a causa del movimento di pressione del pulsante sinistro del mouse.
   * Continuare a fare clic fino a quando tutti i punti desiderati non sono stati aggiunti al poligono.
 - Modalità a mano libera
   * Premi il pulsante sinistro del mouse o tocca la mappa e trascina il mouse o tocca il punto. Le coordinate vengono aggiunte al poligono quando il mouse o il punto di tocco si sposta sulla mappa. Non appena viene attivato il mouse o l'evento di ritocco, il disegno viene completato. La frequenza con cui vengono aggiunte `freehandInterval` le coordinate è definita dall'opzione dei gestori di disegni.
 - Modalità ibrida
   * Alternare tra i metodi click e freehand, come desiderato, mentre si disegna un singolo poligono. Ad esempio, fai clic su alcuni punti, quindi tieni premuto e trascina il mouse per aggiungere un gruppo di punti, quindi fai clic su alcuni altri. 

**Termina disegno**
 - Modalità ibrida/clic
   * Fare doppio clic sulla mappa nell'ultimo punto. 
   * Fare clic sul primo punto del poligono.
   * Fare clic su un pulsante qualsiasi nella barra degli strumenti di disegno. 
   * Impostare a livello di codice la modalità di disegno. 
 - Modalità a mano libera
   * Rilasciare il pulsante del mouse o il punto di tocco.
 - Premere `C` il tasto .

**Annullare il disegno**
 - Premere `Escape` il tasto .

### <a name="how-to-draw-a-rectangle"></a>Procedura di disegno di un rettangolo

Quando il gestore `draw-rectangle` di disegno è in modalità, è possibile eseguire le seguenti azioni per disegnare punti sulla mappa, a seconda della modalità di interazione. La forma generata seguirà la [specifica GeoJSON estesa per i rettangoli.](extend-geojson.md#rectangle)

**Inizia a disegnare**
 - Premere il pulsante sinistro del mouse o toccare la mappa per aggiungere il primo angolo del rettangolo e trascinare per creare il rettangolo. 

**Termina disegno**
 - Rilasciare il pulsante del mouse o il punto di tocco.
 - Impostare a livello di codice la modalità di disegno. 
 - Premere `C` il tasto .

**Annullare il disegno**
 - Premere `Escape` il tasto .

### <a name="how-to-draw-a-circle"></a>Come disegnare un cerchio

Quando il gestore `draw-circle` di disegno è in modalità, è possibile eseguire le seguenti azioni per disegnare punti sulla mappa, a seconda della modalità di interazione. La forma generata seguirà la [specifica GeoJSON estesa per i cerchi.](extend-geojson.md#circle)

**Inizia a disegnare**
 - Premere il pulsante sinistro del mouse o toccare la mappa per aggiungere il centro del cerchio e trascinare con un raggio per i cerchi. 

**Termina disegno**
 - Rilasciare il pulsante del mouse o il punto di tocco.
 - Impostare a livello di codice la modalità di disegno. 
 - Premere `C` il tasto .

**Annullare il disegno**
 - Premere `Escape` il tasto .

## <a name="keyboard-shortcuts"></a>Tasti di scelta rapida

Gli strumenti di disegno supportano le scelte rapide da tastiera. Questi tasti di scelta rapida sono funzionali quando la mappa è a fuoco.

| Chiave      | Azione                            |
|----------|-----------------------------------|
| `C` | Completa qualsiasi disegno in corso e imposta la modalità di disegno su inattiva. Lo stato attivo si sposterà sull'elemento della mappa di primo livello.  |
| `Escape` | Annulla qualsiasi disegno in corso e imposta la modalità di disegno su inattiva. Lo stato attivo si sposterà sull'elemento della mappa di primo livello.  |
| `F` | Aggiunge una coordinata a un punto, una linea o un poligono se il mouse si trova sulla mappa. Azione equivalente di fare clic sulla mappa in modalità clic o ibrida. Questa scorciatoia consente disegni più precisi e veloci. È possibile utilizzare una mano per posizionare il mouse e l'altra per premere il pulsante senza spostare il mouse dal gesto di pressione. |

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulle classi nel modulo degli strumenti di disegno:

> [!div class="nextstepaction"]
> [Gestione disegni](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest)

> [!div class="nextstepaction"]
> [Barra degli strumenti Disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar?view=azure-node-latest)

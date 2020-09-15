---
title: Tipi di interazione degli strumenti di disegno e tasti di scelta rapida nella mappa | Mappe Microsoft Azure
description: Come creare e modificare forme usando un mouse, un touchscreen o una tastiera in Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 429baa4d5dc85476192758e4500f4c1f14610e6d
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90089310"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Tipi di interazione e tasti di scelta rapida nel modulo strumenti di disegno

In questo articolo vengono illustrati tutti i diversi modi per creare e modificare forme utilizzando un mouse, un touchscreen o scelte rapide da tastiera.

Il gestore del disegno supporta tre diverse modalità di interazione con la mappa, per disegnare forme.

* `click` -Le coordinate vengono aggiunte quando si fa clic con il mouse o il tocco.
* `freehand ` -Le coordinate vengono aggiunte quando il mouse o il tocco viene trascinato sulla mappa.
* `hybrid` -Le coordinate vengono aggiunte quando si fa clic o si trascina il mouse o il tocco.

## <a name="how-to-draw-shapes"></a>Come creare forme

 Prima di poter disegnare una forma, impostare l' `drawingMode` opzione di gestione del disegno su un'impostazione di disegno supportata. Questa impostazione può essere programmata o richiamata premendo uno dei pulsanti di disegno sulla barra degli strumenti. La modalità di disegno rimane abilitata, anche dopo che è stata disegnata una forma, semplificando il disegno di forme aggiuntive dello stesso tipo. Imposta a livello di codice la modalità di disegno su uno stato inattivo. In alternativa, passare a uno stato inattivo facendo clic sul pulsante modalità di disegno corrente sulla barra degli strumenti.

Le sezioni successive delineano tutti i diversi modi in cui è possibile disegnare forme sulla mappa.

### <a name="how-to-draw-a-point"></a>Come creare un punto

Quando la gestione del disegno è in `draw-point` modalità di disegno, è possibile eseguire le operazioni seguenti per disegnare punti sulla mappa. Questi metodi funzionano con tutte le modalità di interazione.

**Avvia disegno**
 - Fare clic sul pulsante sinistro del mouse oppure toccare la mappa per aggiungere un punto alla mappa. 
 - Se il mouse si trova sulla mappa, premere il `F` tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo garantisce una maggiore precisione per l'aggiunta di un punto alla mappa. Lo spostamento sul mouse sarà inferiore a causa del movimento di pressione del pulsante sinistro del mouse.
 - Continua a fare clic, toccare o premere `F` per aggiungere altri punti alla mappa.
 
**Fine disegno**
 - Fare clic su qualsiasi pulsante sulla barra degli strumenti del disegno. 
 - Impostare la modalità di disegno a livello di codice. 
 - Premere il `C` tasto.

**Annulla disegno**
 - Premere il `Escape` tasto.

### <a name="how-to-draw-a-line"></a>Come creare una linea

Quando la gestione del disegno è in `draw-line` modalità, è possibile eseguire le operazioni seguenti per disegnare punti sulla mappa, a seconda della modalità di interazione.

**Avvia disegno**
 - Modalità clic
   * Fare clic sul pulsante sinistro del mouse oppure toccare la mappa per aggiungere ogni punto di una linea sulla mappa. Viene aggiunta una coordinata alla riga per ogni clic o tocco. 
   * Se il mouse si trova sulla mappa, premere il `F` tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo garantisce una maggiore precisione per l'aggiunta di un punto alla mappa. Lo spostamento sul mouse sarà inferiore a causa del movimento di pressione del pulsante sinistro del mouse.
   * Continua a fare clic fino a quando tutti i punti desiderati non sono stati aggiunti alla riga.
 - Modalità a mano libera
   * Premere il pulsante sinistro del mouse oppure toccare la mappa e trascinare il mouse oppure toccare il punto. Le coordinate vengono aggiunte alla riga quando il mouse o il punto di tocco si sposta intorno alla mappa. Non appena viene attivato il mouse o l'evento di tocco, il disegno viene completato. La frequenza con cui vengono aggiunte le coordinate viene definita dall'opzione di gestione del disegno `freehandInterval` .
 - Modalità ibrida
   * Alternare i metodi click e Freehand, come desiderato, durante il disegno di una singola riga. Ad esempio, fare clic su alcuni punti, quindi tenendo premuto il mouse per aggiungere una serie di punti, quindi fare clic su altri. 

**Fine disegno**
 - Modalità ibrido/clic
   * Fare doppio clic sulla mappa nell'ultimo punto. 
   * Fare clic su qualsiasi pulsante sulla barra degli strumenti del disegno. 
   * Impostare la modalità di disegno a livello di codice. 
 - Modalità a mano libera
   * Rilasciare il pulsante del mouse o il punto di tocco.
 - Premere il `C` tasto.

**Annulla disegno**
 - Premere il `Escape` tasto.

### <a name="how-to-draw-a-polygon"></a>Come creare un poligono

Quando la gestione del disegno è in `draw-polygon` modalità, è possibile eseguire le operazioni seguenti per disegnare punti sulla mappa, a seconda della modalità di interazione.

**Avvia disegno**
 - Modalità clic
   * Fare clic sul pulsante sinistro del mouse oppure toccare la mappa per aggiungere ogni punto di un poligono sulla mappa. Viene aggiunta una coordinata al poligono per ogni clic o tocco. 
   * Se il mouse si trova sulla mappa, premere il `F` tasto e verrà aggiunto un punto in corrispondenza della coordinata del puntatore del mouse. Questo metodo garantisce una maggiore precisione per l'aggiunta di un punto alla mappa. Lo spostamento sul mouse sarà inferiore a causa del movimento di pressione del pulsante sinistro del mouse.
   * Continua a fare clic fino a quando non sono stati aggiunti tutti i punti desiderati al poligono.
 - Modalità a mano libera
   * Premere il pulsante sinistro del mouse oppure toccare la mappa e trascinare il mouse oppure toccare il punto. Le coordinate vengono aggiunte al poligono quando il mouse o il punto di tocco si sposta intorno alla mappa. Non appena viene attivato il mouse o l'evento di tocco, il disegno viene completato. La frequenza con cui vengono aggiunte le coordinate viene definita dall'opzione di gestione del disegno `freehandInterval` .
 - Modalità ibrida
   * Alternare i metodi click e Freehand, come desiderato, durante il disegno di un singolo poligono. Ad esempio, fare clic su alcuni punti, quindi tenendo premuto il mouse per aggiungere una serie di punti, quindi fare clic su altri. 

**Fine disegno**
 - Modalità ibrido/clic
   * Fare doppio clic sulla mappa nell'ultimo punto. 
   * Fare clic sul primo punto del poligono.
   * Fare clic su qualsiasi pulsante sulla barra degli strumenti del disegno. 
   * Impostare la modalità di disegno a livello di codice. 
 - Modalità a mano libera
   * Rilasciare il pulsante del mouse o il punto di tocco.
 - Premere il `C` tasto.

**Annulla disegno**
 - Premere il `Escape` tasto.

### <a name="how-to-draw-a-rectangle"></a>Procedura di disegno di un rettangolo

Quando la gestione del disegno è in `draw-rectangle` modalità, è possibile eseguire le operazioni seguenti per disegnare punti sulla mappa, a seconda della modalità di interazione. La forma generata seguirà la [specifica GeoJSON estesa per i rettangoli](extend-geojson.md#rectangle).

**Avvia disegno**
 - Premere il pulsante sinistro del mouse oppure toccare la mappa per aggiungere il primo angolo del rettangolo e trascinare per creare il rettangolo. 

**Fine disegno**
 - Rilasciare il pulsante del mouse o il punto di tocco.
 - Impostare la modalità di disegno a livello di codice. 
 - Premere il `C` tasto.

**Annulla disegno**
 - Premere il `Escape` tasto.

### <a name="how-to-draw-a-circle"></a>Come creare un cerchio

Quando la gestione del disegno è in `draw-circle` modalità, è possibile eseguire le operazioni seguenti per disegnare punti sulla mappa, a seconda della modalità di interazione. La forma generata seguirà la [specifica GeoJSON estesa per i cerchi](extend-geojson.md#circle).

**Avvia disegno**
 - Premere il pulsante sinistro del mouse oppure toccare la mappa per aggiungere il centro del cerchio e trascinare i cerchi in un raggio. 

**Fine disegno**
 - Rilasciare il pulsante del mouse o il punto di tocco.
 - Impostare la modalità di disegno a livello di codice. 
 - Premere il `C` tasto.

**Annulla disegno**
 - Premere il `Escape` tasto.

## <a name="keyboard-shortcuts"></a>Scelte rapide da tastiera

Gli strumenti di disegno supportano le scelte rapide da tastiera. Questi tasti di scelta rapida sono funzionali quando la mappa è attiva.

| Chiave      | Azione                            |
|----------|-----------------------------------|
| `C` | Completa qualsiasi disegno in corso e imposta la modalità di disegno su inattivo. Lo stato attivo passa all'elemento della mappa di primo livello.  |
| `Escape` | Annulla qualsiasi disegno in corso e imposta la modalità di disegno su inattivo. Lo stato attivo passa all'elemento della mappa di primo livello.  |
| `F` | Aggiunge una coordinata a un punto, una linea o un poligono se il mouse si trova sulla mappa. Azione equivalente quando si fa clic sulla mappa quando si usa la modalità clic o ibrida. Questo collegamento consente un disegno più preciso e più rapido. È possibile utilizzare una sola mano per posizionare il mouse e altro per premere il pulsante senza lo spostamento del mouse dal movimento di stampa. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle classi, vedere il modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Gestione disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Barra degli strumenti disegno](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

---
title: Aggiungere una barra degli strumenti di disegno ad Azure Maps | Microsoft Docs
description: Come aggiungere una barra degli strumenti di disegno a una mappa usando Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 63848ecf92daecf28f6e0046e87da15355aa35c1
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561831"
---
# <a name="drawing-tool-events"></a>Eventi dello strumento di disegno

Quando si usano gli strumenti di disegno su una mappa, è spesso utile rispondere a determinati eventi quando l'utente disegna sulla mappa. Nella tabella seguente sono elencati tutti gli eventi supportati dalla classe `DrawingManager`.

| Evento | Description |
|-------|-------------|
| `drawingchanged` | Generato quando una coordinata in una forma è stata aggiunta o modificata. | 
| `drawingchanging` | Generato quando viene visualizzata una coordinata di anteprima per una forma. Ad esempio, viene attivato più volte quando viene trascinata una coordinata. | 
| `drawingcomplete` | Generato quando una forma è stata completata o è stata rilevata la modalità di modifica. |
| `drawingmodechanged` | Generato quando viene modificata la modalità di disegno. La nuova modalità di disegno viene passata nel gestore eventi. |
| `drawingstarted` | Generato quando l'utente inizia a disegnare una forma o inserisce una forma in modalità di modifica.  |

Il codice seguente illustra il funzionamento degli eventi nel modulo strumenti di disegno. Creare forme sulla mappa e osservare gli eventi generati.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi degli strumenti di disegno" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>eventi degli strumenti di disegno</a> penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Esempi

Di seguito sono riportati alcuni esempi di scenari comuni che usano gli eventi degli strumenti di disegno.

### <a name="select-points-in-polygon-area"></a>Seleziona punti nell'area poligono

Nel codice seguente viene illustrato come monitorare il disegno di forme che rappresentano aree poligonali (poligoni, rettangoli e cerchi) e determinare quali punti dati sulla mappa si trovano nell'area disegnata. L'evento `drawingcomplete` viene usato per attivare la logica Select. Nella logica Select tutti i punti dati sulla mappa vengono sottoposti a loop e testati per l'intersezione con l'area poligonale della forma disegnata. Questo esempio usa la libreria open source di [Turf. js](https://turfjs.org/) per eseguire un calcolo di intersezione spaziale.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Seleziona dati nell'area poligono disegnata" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere la penna <a href='https://codepen.io/azuremaps/pen/XWJdeja'>selezionare i dati nell'area poligono disegnata</a> da mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Estrai e cerca nell'area poligono

Nel codice seguente viene illustrato come eseguire una ricerca di punti di interesse all'interno di un'area della forma dopo che l'utente ha completato il disegno della forma. L'evento `drawingcomplete` viene usato per attivare la logica di ricerca. Se l'utente disegna un rettangolo o un poligono, viene eseguita una ricerca all'interno della geometria. Se viene disegnato un cerchio, il raggio e la posizione centrale vengono usati per eseguire un punto di ricerca di interesse. L'evento `drawingmodechanged` viene usato per determinare quando l'utente passa a una modalità di disegno e cancella l'area di disegno.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Estrai e cerca nell'area poligono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Per informazioni sulle mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>, vedere l' <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>Area poligono per la ricerca e la ricerca nella</a> penna.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Creazione di uno strumento di misurazione

Il codice seguente mostra come è possibile usare gli eventi di disegno per creare uno strumento di misurazione. Il `drawingchanging` viene utilizzato per monitorare la forma mentre viene disegnata. Quando l'utente sposta il mouse, vengono calcolate le dimensioni della forma. L'evento `drawingcomplete` viene utilizzato per eseguire un calcolo finale sulla forma dopo che è stato disegnato. L'evento `drawingmodechanged` viene utilizzato per determinare quando l'utente passa a una modalità di disegno e cancella l'area di disegno e le informazioni di misurazione precedenti.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Strumento di misurazione" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere lo <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>strumento di misurazione</a> delle penne di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Ottenere i dati di forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e tasti di scelta rapida](drawing-tools-interactions-keyboard-shortcuts.md)

Altre informazioni sul modulo Servizi:

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)

Vedere altri esempi di codice:

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)

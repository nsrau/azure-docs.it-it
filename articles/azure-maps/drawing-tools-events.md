---
title: Aggiunta di una barra degli strumenti di disegno a una mappa Mappe di Microsoft Azure
description: In questo articolo imparerai come aggiungere una barra degli strumenti di disegno a una mappa usando Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: cf9c79f608aa3ffd1137be41ff3348f62b890867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198310"
---
# <a name="drawing-tool-events"></a>Eventi dello strumento di disegno

Quando si utilizzano gli strumenti di disegno su una mappa, è utile reagire a determinati eventi mentre l'utente disegna sulla mappa. Questa tabella elenca tutti gli `DrawingManager` eventi supportati dalla classe.

| Event | Descrizione |
|-------|-------------|
| `drawingchanged` | Generato quando una coordinata di una forma è stata aggiunta o modificata. | 
| `drawingchanging` | Generato quando viene visualizzata una coordinata di anteprima per una forma. Ad esempio, questo evento verrà generato più volte quando viene trascinata una coordinata. | 
| `drawingcomplete` | Generato quando una forma ha terminato di essere disegnata o portata fuori dalla modalità di modifica. |
| `drawingmodechanged` | Generato quando la modalità di disegno è cambiata. La nuova modalità di disegno viene passata al gestore eventi. |
| `drawingstarted` | Generato quando l'utente inizia a disegnare una forma o mette una forma in modalità di modifica.  |

Nel codice seguente viene illustrato il funzionamento degli eventi nel modulo Strumenti di disegno. Disegna forme sulla mappa e guarda come gli eventi si attivano.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi degli strumenti di disegno" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere gli eventi degli <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>strumenti</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>disegno penna di Azure Maps ( ) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Esempi

Vediamo alcuni scenari comuni che utilizzano gli eventi degli strumenti di disegno.

### <a name="select-points-in-polygon-area"></a>Selezionare i punti nell'area del poligono

Questo codice illustra come monitorare un evento di un utente che disegna forme. Per questo esempio, il codice monitora forme di poligoni, rettangoli e cerchi. Quindi, determina quali punti dati sulla mappa si trovano all'interno dell'area disegnata. L'evento `drawingcomplete` viene utilizzato per attivare la logica di selezione. Nella logica di selezione, il codice scorre in ciclo tutti i punti dati sulla mappa. Controlla se è presente un'intersezione del punto e dell'area della forma disegnata. In questo esempio viene utilizzata la libreria [Turf.js](https://turfjs.org/) open source per eseguire un calcolo dell'intersezione spaziale.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selezionare i dati nell'area poligono disegnata" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere i dati di Selezione penna <a href='https://codepen.io/azuremaps/pen/XWJdeja'>nell'area poligono disegnato</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Disegnare e cercare nell'area del poligono

Questo codice cerca i punti di interesse all'interno dell'area di una forma dopo che l'utente ha finito di disegnare la forma. Puoi modificare ed eseguire il codice facendo clic su "Modifica sulla penna del codice" nell'angolo in alto a destra del fotogramma. L'evento `drawingcomplete` viene utilizzato per attivare la logica di ricerca. Se l'utente disegna un rettangolo o un poligono, viene eseguita una ricerca all'interno della geometria. Se viene disegnato un cerchio, il raggio e la posizione centrale vengono utilizzati per eseguire una ricerca del punto di interesse. L'evento `drawingmodechanged` viene utilizzato per determinare quando l'utente passa alla modalità di disegno e questo cancella l'area di disegno.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Disegnare e cercare nell'area del poligono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere disegno <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>penna e ricerca nell'area poligono</a> da Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) su <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Creare uno strumento di misura

Il codice seguente mostra come utilizzare gli eventi di disegno per creare uno strumento di misurazione. L'oggetto `drawingchanging` viene utilizzato per monitorare la forma, mentre viene disegnata. Quando l'utente sposta il mouse, vengono calcolate le dimensioni della forma. L'evento `drawingcomplete` viene utilizzato per eseguire un calcolo finale sulla forma dopo che è stata disegnata. L'evento `drawingmodechanged` viene utilizzato per determinare quando l'utente passa alla modalità di disegno. Inoltre, `drawingmodechanged` l'evento cancella l'area di disegno e cancella le vecchie informazioni di misurazione.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Strumento di misurazione" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Vedere <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>lo strumento di misurazione</a> della penna di Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come utilizzare le funzioni aggiuntive del modulo degli strumenti di disegno:

> [!div class="nextstepaction"]
> [Ottenere i dati della forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e scelte rapide da tastiera](drawing-tools-interactions-keyboard-shortcuts.md)

Ulteriori informazioni sul modulo Servizi:

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)

Vedere altri esempi di codice:

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)

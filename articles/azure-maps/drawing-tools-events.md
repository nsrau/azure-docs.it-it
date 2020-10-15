---
title: Eventi dello strumento di disegno | Mappe Microsoft Azure
description: In questo articolo viene spiegato come aggiungere una barra degli strumenti di disegno a una mappa usando Microsoft Azure Maps Web SDK
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089344"
---
# <a name="drawing-tool-events"></a>Eventi degli strumenti di disegno

Quando si usano gli strumenti di disegno su una mappa, è utile rispondere a determinati eventi mentre l'utente disegna sulla mappa. Questa tabella elenca tutti gli eventi supportati dalla classe `DrawingManager`.

| Event | Descrizione |
|-------|-------------|
| `drawingchanged` | Generato quando viene aggiunta o modificata una coordinata in una forma. | 
| `drawingchanging` | Generato quando viene visualizzata una coordinata di anteprima per una forma. Questo evento ad esempio viene attivato più volte quando viene trascinata una coordinata. | 
| `drawingcomplete` | Generato dopo che è stata disegnata una forma o dopo che è stata disattivata la modalità di modifica. |
| `drawingmodechanged` | Generato quando viene modificata la modalità di disegno. La nuova modalità di disegno viene passata nel gestore eventi. |
| `drawingstarted` | Generato quando l'utente inizia a disegnare una forma o attiva la modalità di modifica per una forma.  |

Il codice seguente illustra il funzionamento degli eventi nel modulo strumenti di disegno. Disegnare forme sulla mappa e osservare gli eventi generati.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventi degli strumenti di disegno" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere gli <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>eventi degli strumenti di disegno</a> (penna) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Esempi

Verranno esaminati alcuni scenari comuni che usano gli eventi degli strumenti di disegno.

### <a name="select-points-in-polygon-area"></a>Selezionare punti nell'area poligono

Questo codice illustra come monitorare un evento di un utente che disegna forme. Per questo esempio, il codice monitora le forme di poligoni, rettangoli e cerchi. Determina quindi i punti dati della mappa che si trovano all'interno dell'area disegnata. L'evento `drawingcomplete` viene usato per attivare la logica di selezione. Nella logica di selezione il codice esegue il ciclo continuo di tutti i punti dati sulla mappa. Verifica se esiste un'intersezione tra il punto e l'area della forma disegnata. Questo esempio usa la libreria open source [Turf.js](https://turfjs.org/) per eseguire un calcolo di intersezione spaziale.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Selezionare dati nell'area poligono disegnata" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere la <a href='https://codepen.io/azuremaps/pen/XWJdeja'>selezione dati nell'area poligono disegnata</a> (penna) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Disegnare ed eseguire ricerche nell'area poligono

Questo codice cerca i punti di interesse all'interno dell'area di una forma dopo che l'utente ha terminato di disegnare la forma. È possibile modificare ed eseguire il codice facendo clic su 'Edit on CodePen' (Modifica in CodePen) nell'angolo in alto a destra del fotogramma. L'evento `drawingcomplete` viene usato per attivare la logica di ricerca. Se l'utente disegna un rettangolo o un poligono, viene eseguita una ricerca all'interno della geometria. Se viene disegnato un cerchio, vengono usati il raggio e il punto centrale per eseguire la ricerca dei punti di interesse. L'evento `drawingmodechanged` viene usato per determinare quando l'utente passa alla modalità di disegno e questo evento cancella l'area di disegno.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Disegnare ed eseguire ricerche nell'area poligono" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere il <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>disegno e la ricerca nell'area poligono</a> (penna) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Creare uno strumento di misurazione

Il codice seguente mostra come è possibile usare gli eventi di disegno per creare uno strumento di misurazione. L'evento `drawingchanging` viene usato per monitorare la forma mentre viene disegnata. Quando l'utente sposta il mouse, vengono calcolate le dimensioni della forma. L'evento `drawingcomplete` viene usato per eseguire un calcolo finale sulla forma dopo che è stata disegnata. L'evento `drawingmodechanged` viene usato per determinare quando l'utente passa a una modalità di disegno. L'evento `drawingmodechanged` inoltre cancella l'area di disegno e le informazioni di misurazione precedenti.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Strumento di misurazione" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Vedere lo <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>strumento di misurazione</a> (penna) di Mappe di Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) in <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Passaggi successivi

Vedere le informazioni su come usare le funzionalità aggiuntive del modulo strumenti di disegno:

> [!div class="nextstepaction"]
> [Ottenere i dati della forma](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Tipi di interazione e scelte rapide da tastiera](drawing-tools-interactions-keyboard-shortcuts.md)

Vedere altre informazioni sul modulo Servizi:

> [!div class="nextstepaction"]
> [Modulo Servizi](how-to-use-services-module.md)

Vedere altri esempi di codice:

> [!div class="nextstepaction"]
> [Pagina dell'esempio di codice](https://aka.ms/AzureMapsSamples)

---
title: Livelli di zoom e griglia riquadri in Mappe di Azure | Microsoft Docs
description: Informazioni su livelli di zoom e griglia riquadri in Mappe di Azure
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261267"
---
# <a name="zoom-levels-and-tile-grid"></a>Livelli di zoom e griglia riquadri
Mappe di Azure usano il sistema di coordinate sferiche Mercatore (EPSG: 3857).

Il mondo è suddiviso in riquadri quadrati. Mappe di Azure fornisce riquadri raster e vettoriale per 23 livelli di zoom numerati da 0 a 22. Al livello di zoom 0 il mondo è incluso in un unico riquadro:

![Riquadro contenente il mondo](./media/zoom-levels-and-tile-grid/world0.png)

Per il rendering del mondo il livello di zoom 1 usa quattro riquadri, ovvero un quadrato 2 x 2

![Riquadro del mondo superiore sinistro](media/zoom-levels-and-tile-grid/world1a.png)     ![Riquadro del mondo superiore destro](media/zoom-levels-and-tile-grid/world1c.png) 

![Riquadro del mondo inferiore sinistro](media/zoom-levels-and-tile-grid/world1b.png)     ![Riquadro del mondo inferiore destro](media/zoom-levels-and-tile-grid/world1d.png) 

I riquadri di quello precedente, creare una griglia con di 2 suddivide in quattro ogni livello di zoom aggiuntiva<sup>zoom</sup> x 2<sup>zoom</sup>. Il livello di zoom 22 è una griglia di 2<sup>22</sup> x 2<sup>22</sup> ovvero 4.194.304 x 4.194.304 riquadri (per un totale di 17.592.186.044.416 riquadri).

I controlli mappa interattiva di mappe di Azure per il web e supporto per Android fare zoom avanti livelli 25 livelli di zoom numerati da 0 a 24. Anche se i dati di viaggio sarà disponibili solo a livello di zoom in quando sono disponibili le sezioni.

La tabella seguente fornisce i valori dell'elenco completo per i livelli di zoom:

|Livello di zoom|Metri/pixel|Metri/lato riquadro|
|--- |--- |--- |
|0|156543|40075008|
|1|78271,5|20037504|
|2|39135,8|10018764,8|
|3|19567,9|5009382,4|
|4|9783,9|2504678,4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611,5|156544|
|9|305,7|78259,2|
|10|152,9|39142,4|
|11|76,4|19558,4|
|12|38,2|9779,2|
|13|19,1|4889,6|
|14|9,6|2457,6|
|15|4.8|1228,8|
|16|2.4|614,4|
|17|1.2|307,2|
|18|0,6|152,8|
|19|0,3|76,4|
|20|0,15|38,2|
|21|0,075|19,1|
|22|0,0375|9,55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

I riquadri vengono chiamati dal livello di zoom e dalle coordinate x e y corrispondenti alla posizione del riquadro sulla griglia per il livello di zoom specifico.

Quando si determina il livello di zoom da usare, tenere presente che ogni percorso è una posizione fissa sul relativo riquadro. Ciò significa che il numero di riquadri necessari per visualizzare un'area specifica di un territorio dipende dalla posizione specifica della griglia di zoom nel mondo. Ad esempio, se sono presenti due punti distanti 900 metri l'uno dall'altro, *potrebbe* essere necessario usare solo tre riquadri per visualizzare il percorso tra i due punti con un livello di zoom 17. Se tuttavia il punto occidentale si trova sulla destra del relativo riquadro e il punto orientale si trova sulla sinistra del relativo riquadro, potrebbero essere necessari quattro riquadri:

![Scala di zoom di esempio](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Dopo aver definito il livello di zoom, sarà possibile calcolare i valore x e y. Il riquadro superiore sinistro in ogni griglia di zoom è x = 0, y = 0. il riquadro inferiore destro è x=2 = 2<sup>zoom -1</sup>, y = 2<sup>zoom-1</sup>.

Di seguito è illustrata la griglia di zoom per il livello di zoom 1:

![Griglia di zoom per il livello di zoom 1](media/zoom-levels-and-tile-grid/api_x_y.png)

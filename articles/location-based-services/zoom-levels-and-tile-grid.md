---
title: Livelli di zoom e griglia riquadri in Servizi Location Based di Azure | Microsoft Docs
description: Informazioni su livelli di zoom e griglia riquadri in Servizi Location Based di Azure
services: location-based-services
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Livelli di zoom e griglia riquadri
Servizi Location Based di Azure usa il sistema di coordinate sferiche per le proiezioni di Mercatore (EPSG: 3857).

Il mondo è suddiviso in riquadri quadrati. Il rendering (raster) ha 19 livelli di zoom numerati da 0 a 18. Il rendering (vettoriale) ha 21 livelli di zoom numerati da 0 a 20. Al livello di zoom 0 il mondo è incluso in un unico riquadro:

![Riquadro contenente il mondo](./media/zoom-levels-and-tile-grid/world0.png)

Per il rendering del mondo il livello di zoom 1 usa 4 riquadri, ovvero un quadrato 2 x 2

![Riquadro del mondo superiore sinistro](./media/zoom-levels-and-tile-grid/world1a.png)     ![Riquadro del mondo superiore destro](./media/zoom-levels-and-tile-grid/world1c.png) 

![Riquadro del mondo inferiore sinistro](./media/zoom-levels-and-tile-grid/world1b.png)     ![Riquadro del mondo inferiore destro](./media/zoom-levels-and-tile-grid/world1d.png) 


Ogni livello di zoom successivo suddivide in quattro i riquadri di quello precedente in modo da creare una griglia con di 2<sup>zoom</sup> x 2<sup>zoom</sup>. Il livello di zoom 20 è una griglia di 2<sup>20</sup> x 2<sup>20</sup> ovvero 1.048.576 x 1.048.576 riquadri (per un totale di 109.951.162.778).

La tabella completa dei valori per i livelli di zoom è riportata di seguito:

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

I riquadri vengono chiamati dal livello di zoom e dalle coordinate x e y corrispondenti alla posizione del riquadro sulla griglia per il livello di zoom specifico.

Quando si determina il livello di zoom da usare, è opportuno ricordare che ciascuna posizione è una posizione fissa sul relativo riquadro. Ciò significa che il numero di riquadri necessari per visualizzare un'area specifica di un territorio dipende dalla posizione specifica della griglia di zoom nel mondo. Ad esempio, se sono presenti due punti distanti 900 metri l'uno dall'altro, *potrebbe* essere necessario usare solo tre riquadri per visualizzare il percorso tra i due punti con un livello di zoom 17. Tuttavia, se il punto occidentale si trova sulla destra del relativo riquadro e il punto orientale si trova sulla sinistra del relativo riquadro, potrebbero essere necessari quattro riquadri:

![Scala di zoom di esempio](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Dopo aver definito il livello di zoom, sarà possibile calcolare i valore x e y. Il riquadro superiore sinistro in ogni griglia di zoom è x=0, y=0; il riquadro inferiore destro è x=2<sup>zoom -1</sup>, y=2<sup>zoom-1</sup>.

Di seguito è illustrata la griglia di zoom per il livello di zoom 1:

![Griglia di zoom per il livello di zoom 1](./media/zoom-levels-and-tile-grid/api_x_y.png)

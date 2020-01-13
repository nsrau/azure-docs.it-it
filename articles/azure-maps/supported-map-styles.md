---
title: Stili della mappa supportati | Mappe Microsoft Azure
description: In questo articolo vengono illustrati i diversi stili di rendering della mappa supportati dalle mappe Microsoft Azure.
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 92e48486777d82589a56074790dd709c5d525859
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910631"
---
# <a name="azure-maps-supported-map-styles"></a>Stili delle mappe supportati da Mappe di Azure
Mappe di Azure supporta vari stili di mappa predefiniti, come descritto di seguito.

## <a name="road"></a>strada
Una mappa **stradale** è una mappa standard che indica le strade, caratteristiche naturali e artificiali e indicazioni di tali caratteristiche.

![stile della mappa stradale](./media/supported-map-styles/road.png)

**API idonee:**
* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="blank-and-blank_accessible"></a>Blank e blank_accessible

Gli stili **blank** e **blank_accessible** map forniscono un'area di disegno vuota su cui visualizzare i dati. Lo stile **blank_accessible** continuerà a fornire aggiornamenti per la lettura dello schermo con i dettagli della posizione in cui si trova la mappa, anche se la mappa di base non viene visualizzata.

> [!Note]
> In Web SDK è possibile modificare il colore di sfondo della mappa impostando lo stile CSS `background-color` dell'elemento map DIV.

**API idonee:**
* Controllo mappa Web SDK

## <a name="satellite"></a>satellite 
Lo stile del **satellite** è una combinazione di immagini aeree e satellitari.

![stile mappa affiancata satellite](./media/supported-map-styles/satellite.png)

**API idonee:**
* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![stile mappa satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="grayscale_dark"></a>grayscale_dark
**gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![stile mappa gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API idonee:**
* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK 
* Controllo mappa Android


## <a name="grayscale_light"></a>grayscale_light
la **luce in grigio** è una versione leggera dello stile della mappa stradale.

![stile mappa chiaro in scala di grigi](./media/supported-map-styles/grayscale_light.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android


## <a name="night"></a>notte
**notte** è una versione scura dello stile della mappa stradale con strade colorate e simboli.

![stile mappa notturna](./media/supported-map-styles/night.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** è uno dei principali stili di Mappe di Azure, completato con contorni della Terra.

![stile mappa di rilievo ombreggiato](./media/supported-map-styles/shaded-relief.png)

**API idonee:**
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare uno stile di mappa in mappe di Azure:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

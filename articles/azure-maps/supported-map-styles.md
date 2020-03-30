---
title: Stili di mappa supportati Mappe di Microsoft Azure
description: In this article, you'll learn about different map rendering styles supported by Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334033"
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

## <a name="blank-and-blank_accessible"></a>vuoto e blank_accessible

Gli stili di mappa **vuoto** e **blank_accessible** forniscono un'area di disegno vuota su cui visualizzare i dati. Lo **stile blank_accessible** continuerà a fornire aggiornamenti dell'utilità per la lettura dello schermo con i dettagli della posizione della mappa, anche se la mappa di base non viene visualizzata.

> [!Note]
> Nel Web SDK è possibile modificare il colore di `background-color` sfondo della mappa impostando lo stile CSS dell'elemento DIV della mappa.

**API idonee:**
* Controllo mappa Web SDK

## <a name="satellite"></a>satellite 
Lo stile del **satellite** è una combinazione di immagini aeree e satellitari.

![stile mappa tessile satellitare](./media/supported-map-styles/satellite.png)

**API idonee:**
* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![satellite_road_labels stile mappa](./media/supported-map-styles/satellite-road-labels.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="grayscale_dark"></a>grayscale_dark
**gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![gray_scale stile mappa](./media/supported-map-styles/grayscale-dark.png)

**API idonee:**
* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK 
* Controllo mappa Android


## <a name="grayscale_light"></a>grayscale_light
**la luce in scala di grigi** è una versione leggera dello stile della mappa stradale.

![stile mappa di luce in scala di grigi](./media/supported-map-styles/grayscale-light.png)

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

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** è uno stile di mappa scuro con un contrasto maggiore rispetto agli altri stili.

![stile mappa scura ad alto contrasto](./media/supported-map-styles/high-contrast-dark.png)

**API idonee:**
* Controllo mappa Web SDK

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare uno stile di mappa in Azure Maps:Learn about how to set a map style in Azure Maps:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

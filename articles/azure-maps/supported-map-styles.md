---
title: Stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Stili delle mappe supportati da Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: cd16ee87b213c2bfa4c541c4d3c453a4499855d0
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73887722"
---
# <a name="azure-maps-supported-map-styles"></a>Stili delle mappe supportati da Mappe di Azure
Mappe di Azure supporta vari stili di mappa predefiniti, come descritto di seguito.

## <a name="road"></a>strada
Una mappa **stradale** è una mappa standard che indica le strade, caratteristiche naturali e artificiali e indicazioni di tali caratteristiche.

![strada](./media/supported-map-styles/road.png)

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

![satellite](./media/supported-map-styles/satellite.png)

**API idonee:**
* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="grayscale_dark"></a>grayscale_dark
**gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API idonee:**
* Controllo mappa Web SDK 
* Controllo mappa Android


## <a name="grayscale_light"></a>grayscale_light
la **luce in grigio** è una versione leggera dello stile della mappa stradale.

![grigio chiaro](./media/supported-map-styles/grayscale_light.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android


## <a name="night"></a>notte
**notte** è una versione scura dello stile della mappa stradale con strade colorate e simboli.

![notte](./media/supported-map-styles/night.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**road shaded relief** è uno dei principali stili di Mappe di Azure, completato con contorni della Terra.

![rilievo ombreggiato](./media/supported-map-styles/shaded-relief.png)

**API idonee:**
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare uno stile di mappa in mappe di Azure:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
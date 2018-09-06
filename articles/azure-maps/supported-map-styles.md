---
title: Stili delle mappe supportati in Mappe di Azure | Microsoft Docs
description: Stili delle mappe supportati da Mappe di Azure
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 04c8f43e3b484ceeb942ae13ea95baf7f0215b53
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344836"
---
# <a name="azure-maps-supported-map-styles"></a>Stili delle mappe supportati da Mappe di Azure
Mappe di Azure supporta quattro diversi stili predefiniti. Di seguito sono elencati gli stili e le relative descrizioni.

## <a name="road"></a>Strada
Una mappa **stradale** è una mappa standard che indica le strade, caratteristiche naturali e artificiali e indicazioni di tali caratteristiche.

![strada](./media/supported-map-styles/road.png)

**API idonee:**
* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa JS

## <a name="satellite"></a>Satellite 
Lo stile del **satellite** è una combinazione di immagini aeree e satellitari.

![satellite](./media/supported-map-styles/satellite.png)

**API idonee:**
* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa JS

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API idonee:**
* Controllo mappa JS

## <a name="grayscaledark"></a>Grayscale_Dark
**Gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API idonee:**
* Controllo mappa JS 
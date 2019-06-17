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
ms.openlocfilehash: db396ad06bf46cbbaf486696b68393a6a4214c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230834"
---
# <a name="azure-maps-supported-map-styles"></a>Stili delle mappe supportati da Mappe di Azure
Mappe di Azure supporta vari stili di mappa predefiniti, come descritto di seguito.

## <a name="road"></a>strada
Una mappa **stradale** è una mappa standard che indica le strade, caratteristiche naturali e artificiali e indicazioni di tali caratteristiche.

![strada](./media/supported-map-styles/road.png)

**API idonee:**
* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa JS
* Controllo mappa di Android

## <a name="satellite"></a>satellite 
Lo stile del **satellite** è una combinazione di immagini aeree e satellitari.

![satellite](./media/supported-map-styles/satellite.png)

**API idonee:**
* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa JS
* Controllo mappa di Android

## <a name="satelliteroadlabels"></a>satellite_road_labels
Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**API idonee:**
* Controllo mappa JS
* Controllo mappa di Android

## <a name="grayscaledark"></a>grayscale_dark
**gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**API idonee:**
* Controllo mappa JS 
* Controllo mappa di Android


## <a name="grayscalelight"></a>grayscale_light
**gradazioni di grigio chiaro** è una versione ridotta dello stile di Guida di orientamento.

![gradazioni di grigio chiaro](./media/supported-map-styles/grayscale_light.png)

**API idonee:**
* Controllo mappa JS
* Controllo mappa di Android


## <a name="night"></a>notte
**notte** è una versione scura dello stile della mappa stradale con strade colorate e simboli.

![notte](./media/supported-map-styles/night.png)

**API idonee:**
* Controllo mappa JS
* Controllo mappa di Android

## <a name="roadshadedrelief"></a>road_shaded_relief
**road shaded relief** è uno dei principali stili di Mappe di Azure, completato con contorni della Terra.

![rilievo ombreggiato](./media/supported-map-styles/shaded-relief.png)

**API idonee:**
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa JS
* Controllo mappa di Android

---
title: Stili mappa di mappe di Azure predefiniti supportati
description: Informazioni sugli stili di mappa predefiniti supportati da Azure Maps, ad esempio Road, blank_accessible, satellite, satellite_road_labels, road_shaded_relief e night.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f6ce2966d7e91bd0fc6a23ad2605316da4e2342e
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037729"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps supporta gli stili di mappa predefiniti

Mappe di Azure supporta vari stili di mappa predefiniti, come descritto di seguito.

## <a name="road"></a>strada

Una mappa **stradale** è una mappa standard che visualizza le strade. Vengono inoltre visualizzate le funzionalità naturali e artificiali e le etichette per queste funzionalità.

![stile della mappa stradale](./media/supported-map-styles/road.png)

**API idonee:**

* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="blank-and-blank_accessible"></a>Blank e blank_accessible

Gli stili **blank** e **blank_accessible** map forniscono un'area di disegno vuota per la visualizzazione dei dati. Lo stile di **blank_accessible** continuerà a fornire aggiornamenti per la lettura dello schermo con i dettagli della posizione della mappa, anche se la mappa di base non viene visualizzata.

> [!Note]
> In Web SDK è possibile modificare il colore di sfondo della mappa impostando lo `background-color` stile CSS dell'elemento map div.

**API idonee:**

* Controllo mappa Web SDK

## <a name="satellite"></a>satellite

Lo stile del **satellite** è una combinazione di immagini aeree e satellitari.

![stile mappa affiancata satellite](./media/supported-map-styles/satellite.png)

**API idonee:**

* [Riquadro del satellite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Questo stile di mappa è un ibrido tra le strade e le indicazioni sovrapposte alle immagini del satellite e aeree.

![stile mappa satellite_road_labels](./media/supported-map-styles/satellite-road-labels.png)

**API idonee:**

* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**gradazioni di grigio scuro** è una versione scura dello stile della mappa stradale.

![stile mappa gray_scale](./media/supported-map-styles/grayscale-dark.png)

**API idonee:**

* [Immagine mappa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="grayscale_light"></a>grayscale_light

la **luce in grigio** è una versione leggera dello stile della mappa stradale.

![stile mappa chiaro in scala di grigi](./media/supported-map-styles/grayscale-light.png)

**API idonee:**
* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="night"></a>notte

**notte** è una versione scura dello stile della mappa stradale con strade colorate e simboli.

![stile mappa notturna](./media/supported-map-styles/night.png)

**API idonee:**

* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

**road shaded relief** è uno dei principali stili di Mappe di Azure, completato con contorni della Terra.

![stile mappa di rilievo ombreggiato](./media/supported-map-styles/shaded-relief.png)

**API idonee:**

* [Riquadro della mappa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controllo mappa Web SDK
* Controllo mappa Android
* Oggetto visivo di Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** è uno stile di mappa scuro con un contrasto maggiore rispetto agli altri stili.

![stile mappa scura a contrasto elevato](./media/supported-map-styles/high-contrast-dark.png)

**API idonee:**

* Controllo mappa Web SDK
* Oggetto visivo di Power BI

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come impostare uno stile di mappa in mappe di Azure:

> [!div class="nextstepaction"]
> [Scegliere uno stile mappa](https://docs.microsoft.com/azure/azure-maps/choose-map-style)

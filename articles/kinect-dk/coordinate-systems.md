---
title: Sistemi di coordinate di Kinect DK di Azure
description: Descrizione dei sistemi di coordinate di Azure Kinect DK associata ai sensori di Azure DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, Sensor, SDK, depth fotocamera, TOF, principi, prestazioni, invalidamento
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276637"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Sistemi di coordinate di Kinect DK di Azure

In questo articolo vengono descritte le convenzioni usate per i sistemi di coordinate 2D e 3D.  Sono presenti sistemi di coordinate distinti associati al dispositivo di ogni sensore e le [funzioni di calibrazione](use-calibration-functions.md) possono trasformare i punti tra di essi. Le [funzioni di trasformazione](use-image-transformation.md) trasformano intere immagini tra i sistemi di coordinate.  

## <a name="2d-coordinate-systems"></a>sistemi di coordinate 2D

 Entrambe le fotocamere di profondità e colore sono associate a un sistema di coordinate 2D indipendente. Una coordinata [x, y] è rappresentata in unità di pixel, dove *x* è compreso tra 0 e Width-1 e *y* varia da 0 a altezza-1. La larghezza e l'altezza dipendono dalla modalità scelta in cui vengono gestite le fotocamere a colori e di profondità. La coordinata pixel `[0,0]` corrisponde al pixel superiore sinistro dell'immagine. Le coordinate pixel possono essere frazionarie che rappresentano le coordinate di subpixel.

Il sistema di coordinate 2D è centrato su 0, ovvero la coordinata del sottopixel `[0.0, 0.0]` rappresenta il centro e `[0.5,0.5]` l'angolo inferiore destro del pixel, come illustrato di seguito.

   ![sistema di coordinate 2D](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>sistemi di coordinate 3D

Ogni fotocamera, l'accelerometro e il giroscopio sono associati a un sistema di spazio delle coordinate 3D indipendente.

I punti nei sistemi di coordinata 3D sono rappresentati con i tripletti delle coordinate metrica [X, Y, Z] con unità in millimetri.

### <a name="depth-and-color-camera"></a>Fotocamera di profondità e colori

L'origine `[0,0,0]` si trova in corrispondenza del punto focale della fotocamera. Il sistema di coordinate è orientato in modo tale che l'asse X positivo punti a destra, l'asse Y positivo punta verso il basso e l'asse Z positivo punta verso il basso.

La profondità della fotocamera è inclinata di 6 gradi verso il basso della fotocamera dei colori, come illustrato di seguito. 

Sono presenti due illuminatori usati dalla fotocamera di profondità. L'illuminatore usato in modalità NFOV (Field-of-View) ristrette è allineato con il caso della fotocamera Depth, quindi l'illuminatore non è inclinato. L'illuminatore usato in modalità WFOV (Wide Field-of-View) è inclinato di 1,3 gradi aggiuntivi verso il basso rispetto alla fotocamera di profondità.

![convenzioni di coordinate 3D](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Giroscopio e accelerometro

L'origine del giroscopio `[0,0,0]` è identica all'origine della fotocamera di profondità. L'origine dell'accelerometro coincide con la posizione fisica. Sia il sistema di coordinate dell'accelerometro che quello del giroscopio sono gestiti correttamente. L'asse X positivo del sistema di coordinate punta indietro, l'asse Y positivo punta a sinistra e l'asse Z positivo punta verso il basso, come illustrato di seguito.

![Sistema di coordinate IMU](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Passaggi successivi

[Informazioni su Azure Kinect Sensor SDK](about-sensor-sdk.md)
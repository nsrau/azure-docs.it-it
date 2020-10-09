---
title: Mappa degli indici del corpo Kinect di Azure
description: Informazioni su come eseguire query su una mappa dell'indice di rilevamento del corpo in Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, porting, body, tracking, index, Segmentation, map
ms.openlocfilehash: 8c128e59cef515aafb4f59794f0f644b90fd625d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276753"
---
# <a name="azure-kinect-body-tracking-index-map"></a>Mappa dell'indice di rilevamento del corpo di Azure Kinect

La mappa dell'indice del corpo include la mappa di segmentazione dell'istanza per ogni corpo nell'acquisizione della fotocamera con profondità. Ogni pixel viene mappato al pixel corrispondente nell'immagine di profondità o IR. Il valore per ogni pixel rappresenta il corpo a cui appartiene il pixel. Può essere uno sfondo (valore `K4ABT_BODY_INDEX_MAP_BACKGROUND` ) o l'indice di un oggetto rilevato `k4abt_body_t` .

![Esempio di mappa dell'indice del corpo](./media/concepts/body-index-map.png)

>[!NOTE]
> L'indice del corpo è diverso dall'ID del corpo. È possibile eseguire una query sull'ID corpo da un indice del corpo specificato chiamando l'API: [k4abt_frame_get_body_id ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1d612404d133a279af847974e9359a92.html#ga1d612404d133a279af847974e9359a92).


## <a name="using-body-index-map"></a>Uso della mappa dell'indice del corpo

Il mapping dell'indice del corpo viene archiviato come `k4a_image_t` e ha la stessa risoluzione dell'immagine di profondità o IR. Ogni pixel è un valore a 8 bit. È possibile eseguire query da un oggetto `k4abt_frame_t` chiamando `k4abt_frame_get_body_index_map` . Lo sviluppatore è responsabile del rilascio della memoria per la mappa dell'indice del corpo chiamando `k4a_image_release()` .

## <a name="next-steps"></a>Passaggi successivi

[Creare la prima app di rilevamento del corpo](build-first-body-app.md)

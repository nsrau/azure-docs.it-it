---
title: Limitazioni
description: Limitazioni di codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 61085a5d28fcd74bbf6a393ddc0731e36094a63f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617499"
---
# <a name="limitations"></a>Limitazioni

Diverse funzionalità prevedono delle limitazioni in termini di dimensioni, conteggi o altro.

## <a name="azure-frontend"></a>Front-end di Azure

* Istanze AzureFrontend totali per processo: 16.
* Istanze AzureSession totali per AzureFrontend: 16.

## <a name="objects"></a>Oggetti

* Totale oggetti consentiti di un singolo tipo (Entity, CutPlaneComponent e così via): 16.777.215.
* Totale piani di taglio attivi consentiti: 8.

## <a name="geometry"></a>Geometria

* **Animazione:** Le animazioni sono limitate all'animazione delle singole trasformazioni degli oggetti di gioco. Le animazioni scheletriche con animazioni di skinning o vertici non sono supportate. Le tracce di animazione dal file di asset di origine non vengono mantenute. Al contrario, le animazioni di trasformazione degli oggetti devono essere gestite dal codice client.
* **Shader personalizzati:** La creazione di shader personalizzati non è supportata. È possibile utilizzare solo [materiali colori](../overview/features/color-materials.md) o [materiali di PBR](../overview/features/pbr-materials.md) predefiniti.
* **Numero massimo di materiali distinti** in un asset: 65.535. Per ulteriori informazioni sulla riduzione del numero di materiali automatico, vedere il capitolo [deduplicazione materiali](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Dimensione massima di una singola trama**: 16.384 x 16.384. Le trame di origine di dimensioni maggiori verranno ridotte in base al processo di conversione.

### <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale passate all'[API REST di gestione della sessione](../how-tos/session-rest-api.md#create-a-session):

| Dimensioni del server | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni |
|premium| nessun limite |

Per informazioni dettagliate su questa limitazione, vedere il capitolo [dimensioni del server](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Windows 10 Desktop**

* Win32/x64 è l'unica piattaforma Win32 supportata. Win32/x86 non è supportato.

**HoloLens 2**

* La funzionalità di [rendering dalla fotocamera PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.

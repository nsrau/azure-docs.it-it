---
title: Limitazioni
description: Limitazioni di codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b500a3777ee24d6615022dae2571d021bd0d675a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201172"
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

* La funzionalità di [rendering dalla fotocamera PV](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.
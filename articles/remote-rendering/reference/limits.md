---
title: Limitazioni
description: Limitazioni di codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 6a1a51ee09422607ae1392704add4d49d3367d57
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759048"
---
# <a name="limitations"></a>Limitazioni

Diverse funzionalità prevedono delle limitazioni in termini di dimensioni, conteggi o altro.

## <a name="azure-frontend"></a>Front-end di Azure

* Istanze AzureFrontend totali per processo: 16.
* Istanze AzureSession totali per AzureFrontend: 16.

## <a name="objects"></a>Oggetti

* Totale oggetti consentiti di un singolo tipo (Entity, CutPlaneComponent e così via): 16.777.215.
* Totale piani di taglio attivi consentiti: 8.

## <a name="materials"></a>Materiali

* Totale materiali consentiti in un asset: 65.535.

## <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale passate all'[API REST di gestione della sessione](../how-tos/session-rest-api.md#create-a-session):

| Dimensioni macchina virtuale | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni |
|premium| nessun limite |


## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Windows 10 Desktop**

* UWP/x86 è l'unica piattaforma UWP supportata. La piattaforma UWP/x64 non è supportata.

**Hololens 2**

* La funzionalità di [rendering dalla fotocamera PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.

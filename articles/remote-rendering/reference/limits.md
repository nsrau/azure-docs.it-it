---
title: Limitazioni
description: Limitazioni di codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: af935aeaaeee11ab50b327b7f7b5b77246cb974b
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566047"
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

* Totale materiali consentiti in un asset: 65.535.
* Dimensione massima di una singola trama: 16.384 x 16.384. Le trame di origine più grandi verranno ridimensionate in base al processo di conversione.

## <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale passate all'[API REST di gestione della sessione](../how-tos/session-rest-api.md#create-a-session):

| Dimensioni del server | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni |
|premium| nessun limite |

Per informazioni più dettagliate su questa limitazione, vedere il capitolo [dimensioni server](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Windows 10 Desktop**

* Win32/x64 è l'unica piattaforma Win32 supportata. Win32/x86 non è supportato.

**Hololens 2**

* La funzionalità di [rendering dalla fotocamera PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.

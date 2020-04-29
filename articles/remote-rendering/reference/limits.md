---
title: Limitazioni
description: Limitazioni del codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417692"
---
# <a name="limitations"></a>Limitazioni

Diverse funzionalità hanno dimensioni, conteggi o altre limitazioni.

## <a name="azure-frontend"></a>Front-end di Azure

* Totale istanze AzureFrontend per processo: 16.
* Totale istanze di AzureSession per AzureFrontend: 16.

## <a name="objects"></a>Oggetti

* Totale oggetti consentiti di un singolo tipo (Entity, CutPlaneComponent e così via): 16.777.215.
* Totale piani di taglio attivi consentiti: 8.

## <a name="materials"></a>Materiali

* Totale materiali consentiti in un asset: 65.535.

## <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale, come passato all' [API REST di gestione della sessione](../how-tos/session-rest-api.md#create-a-session):

| Dimensioni macchina virtuale | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni |
|premium| nessun limite |


## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Windows 10 desktop**

* La distribuzione "PC autonomo" da Unity non è supportata. In alternativa, usare UWP.
* UWP/x86 è l'unica piattaforma UWP supportata. UWP/x64 non è supportato.

**Hololens 2**

* La funzionalità [di rendering dalla fotocamera PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportata.

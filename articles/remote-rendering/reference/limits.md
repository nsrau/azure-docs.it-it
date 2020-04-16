---
title: Limitazioni
description: Limitazioni del codice per le funzionalità SDKCode limitations for SDK features
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417692"
---
# <a name="limitations"></a>Limitazioni

Un certo numero di feature hanno dimensioni, conteggio o altre limitazioni.

## <a name="azure-frontend"></a>Front-end di Azure

* Totale istanze di AzureFrontend per processo: 16.Total AzureFrontend instances per process: 16.
* Totale istanze di AzureSession per AzureFrontend: 16.Total AzureSession instances per AzureFrontend: 16.

## <a name="objects"></a>Oggetti

* Totale oggetti consentiti di un singolo tipo (Entity, CutPlaneComponent e così via): 16.777.215.
* Totale piani di taglio attivi consentiti: 8.

## <a name="materials"></a>Materiali

* Totale materiali ammissibili in un bene: 65.535.

## <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale passata [all'API REST](../how-tos/session-rest-api.md#create-a-session)di gestione della sessione:

| Dimensioni macchina virtuale | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni di abitanti |
|premium| nessun limite |


## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Desktop di Windows 10**

* La distribuzione "PC Standalone" di Unity non è supportata. Utilizzare invece UWP.
* UWP/x86 è l'unica piattaforma UWP supportata. UWP/x64 non è supportato.

**Hololens 2**

* Il [rendering dalla funzione fotocamera fotovoltaica](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.

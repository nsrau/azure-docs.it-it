---
title: Concetti dell'architettura di Azure IoT Central - Pannello solare | Microsoft Docs
description: Questo articolo presenta i concetti fondamentali relativi all'architettura dell'app di monitoraggio per pannelli solari di Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 00ef6cf6d4149f139876cb0c2d845133ba00157c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127507"
---
# <a name="azure-iot-central---solar-panel-app-architecture"></a>Azure IoT Central - Architettura dell'app per pannelli solari




Questo articolo contiene una panoramica dell'architettura del modello di applicazione per il monitoraggio dei pannelli solari. Il diagramma seguente mostra un'architettura di uso comune per l'app per pannelli solari in Azure che utilizza la piattaforma IoT Central.

> [!div class="mx-imgBorder"]
> ![architettura di contatori intelligenti](media/concept-iot-central-solar-panel/solar-panel-app-architecture.png)

L'architettura è costituita dai componenti seguenti. Alcune applicazioni potrebbero non richiedere tutti i componenti elencati qui.

## <a name="solar-panels-and-connectivity"></a>Pannelli solari e connettività 

I pannelli solari rappresentano una delle fonti più significative di energia rinnovabile. A seconda del tipo e dell'installazione, è possibile connettere il pannello solare tramite gateway o tramite altri dispositivi intermedi e sistemi proprietari. Potrebbe essere necessario creare un dispositivo bridge di IoT Central per connettere i dispositivi che non possono essere connessi direttamente. Il dispositivo bridge IoT Central è una soluzione open source i cui dettagli completi sono disponibili [qui](../core/howto-build-iotc-device-bridge.md). 



## <a name="iot-central-platform"></a>Piattaforma IoT Central
Azure IoT Central è una piattaforma che semplifica la creazione di una soluzione IoT e contribuisce a ridurre il carico di lavoro e i costi di gestione, operazioni e sviluppo IoT. Con IoT Central è possibile connettersi, monitorare e gestire facilmente le risorse di Internet delle cose (IoT) su larga scala. Dopo aver connesso i pannelli solari a IoT Central, il modello di app usa funzionalità predefinite, ad esempio modelli di dispositivi, comandi e dashboard. Il modello di app usa anche lo spazio di archiviazione IoT Central per gli scenari in tempo reale, ad esempio monitoraggio, analisi, regole e visualizzazione dei dati del contatore.


## <a name="extensibility-options-to-build-with-iot-central"></a>Opzioni di estendibilità per la compilazione con IoT Central
La piattaforma IoT Central offre due opzioni di estendibilità, ovvero l'esportazione continua dei dati e le API. I clienti e i partner possono scegliere tra queste opzioni di base e personalizzare le proprie soluzioni per esigenze specifiche. Uno dei nostri partner, ad esempio, ha configurato l'esportazione continua dei dati con Azure Data Lake Storage (ADLS). ADLS viene usato per la conservazione dei dati a lungo termine e altri scenari di archiviazione offline sicura, ad esempio per l'elaborazione batch, il controllo e la creazione di report. 

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver appreso le nozioni di base sull'architettura, è possibile [creare un'app per pannelli solari gratuitamente](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](../index.yml)
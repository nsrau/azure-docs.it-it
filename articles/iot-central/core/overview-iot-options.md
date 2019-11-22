---
title: Opzioni di Microsoft Azure IoT | Microsoft Docs
description: Scegliere la modalità di implementazione della soluzione Azure IoT con gli acceleratori di soluzioni Azure IoT Central e IoT o con hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 2ec6e1d00d331a7bb8dd7182dd4b0c91dd3d25a7
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049081"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Confrontare le opzioni di Azure IoT Central e Azure IoT Suite

Microsoft Azure IoT Central e Azure IoT offrono diverse opzioni per la creazione di una soluzione IoT. Queste opzioni sono appropriate per set diversi di requisiti dei clienti:

* [Azure IoT Central](overview-iot-central.md) è una piattaforma applicativa IoT che usa un approccio basato su modelli per consentire la creazione di soluzioni IoT di livello aziendale senza la necessità di competenze nello sviluppo di soluzioni cloud.

* Gli [acceleratori di soluzioni Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) sono una raccolta di livello aziendale di [acceleratori di soluzioni](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) basati sulla piattaforma distribuita come servizio (PaaS) di Azure che consentono di velocizzare lo sviluppo di soluzioni IoT personalizzate.

## <a name="azure-iot-hub"></a>Hub IoT Azure

Hub IoT di Azure è la piattaforma PaaS principale di Azure usata sia da Azure IoT Central che dagli acceleratori di soluzioni Azure IoT. L'hub IoT supporta comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e una soluzione cloud. L'hub IoT consente di risolvere problematiche associate all'implementazione IoT come le seguenti:

* Connettività e gestione di volumi elevati di dispositivi
* Inserimento di volumi elevati di dati di telemetria
* Comando e controllo dei dispositivi
* Applicazione della sicurezza dei dispositivi

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Confrontare gli acceleratori di soluzioni Azure IoT Central e Azure IoT

La scelta del prodotto Azure IoT è una parte essenziale della pianificazione della soluzione IoT. L'hub IoT è un singolo servizio di Azure che non offre, da solo, una soluzione IoT end-to-end, È possibile usare l'hub IoT come punto di partenza per qualsiasi soluzione IoT. Per usare l'hub IoT non è inoltre necessario usare gli acceleratori di soluzioni Azure IoT o Azure IoT Central. Sia gli acceleratori di soluzioni IoT che Azure IoT Central usano l'hub IoT insieme ad altri servizi di Azure.

La tabella seguente offre un riepilogo delle differenze principali tra gli acceleratori di soluzioni IoT e Azure IoT Central per consentire la scelta dell'opzione corretta per i propri requisiti:

|     | Azure IoT Central | Solution Accelerator di Azure IoT |
| --- | ----------- | --------- |
| Utilizzo primario                      | Accelerare il time-to-market per soluzioni IoT semplici che non richiedono una personalizzazione approfondita dei servizi.                                                    | Velocizzare lo sviluppo di una soluzione IoT personalizzata che necessita della massima flessibilità.                                                                                                                             |
| Accesso ai servizi PaaS sottostanti | Poiché si tratta di una soluzione completamente gestita, i servizi sottostanti non sono esposti.                                                                                            | L'utente ha accesso ai servizi di Azure sottostanti per gestirli o, se necessario, sostituirli.                                                                                                                    |
| Flessibilità                        | Media. È possibile usare l'esperienza utente basata su browser predefinita per personalizzare il modello di soluzione e aspetti dell'interfaccia utente. L'infrastruttura non è personalizzabile perché i diversi componenti non sono esposti. | Elevata. Il codice per i microservizi è open source e può essere modificato come si preferisce. È anche possibile personalizzare l'infrastruttura di distribuzione.                                               |
| Livello di competenze                        | Bassa. Sono necessarie competenze di modellazione per personalizzare la soluzione. Non sono richieste competenze nella creazione di codice.                                                                          | Medio-alto. Sono necessarie competenze in Java o .NET per personalizzare il back-end della soluzione e competenze in JavaScript per personalizzare la visualizzazione.                                                                       |
| Esperienza iniziale             | I modelli di applicazione e i modelli di dispositivo forniscono modelli predefiniti distribuibili in pochi minuti.                                                                                                  | Soluzioni preconfigurate implementano scenari IoT comuni, distribuibili in pochi minuti.                                                                                                                            |
| Prezzi                            | Struttura di prezzi semplice e prevedibile.                                                                                                                           | È possibile ottimizzare i servizi per controllare i costi.                                                                                                                                                            |

I prodotti da usare per creare la soluzione IoT dipendono da:

* Requisiti aziendali.
* Il tipo di soluzione che si vuole creare.
* Set di competenze dell'organizzazione per la creazione e la gestione della soluzione a lungo termine.

## <a name="next-steps"></a>Passaggi successivi

In base al prodotto e all'approccio scelti, i passaggi successivi consigliati sono i seguenti.

* **Azure IoT Central**: [Che cos'è Azure IoT Central?](overview-iot-central.md)
* **Acceleratori di soluzioni IoT**: [Informazioni sugli acceleratori di soluzioni Azure IoT](../../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **Hub IoT**: [Che cos'è l'hub IoT Azure?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)
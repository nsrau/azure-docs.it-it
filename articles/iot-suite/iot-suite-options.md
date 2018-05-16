---
title: Opzioni di Microsoft Azure IoT | Microsoft Docs
description: Scegliere la modalità di implementazione della soluzione IoT con gli acceleratori di soluzioni Azure IoT, Microsoft IoT Central o hub IoT di Azure.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.topic: get-started-article
ms.date: 11/10/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddda6bc265cbbaa61025891cf9bc7c28c998160c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="compare-azure-iot-options"></a>Confrontare le opzioni di Azure IoT

L'articolo [Azure e Internet delle cose](iot-suite-what-is-azure-iot.md) descrive una tipica architettura di soluzione IoT con i livelli seguenti:

* Connettività e gestione dei dispositivi
* e analisi dei dati
* Presentazione e integrazione aziendale

Per implementare questa architettura, Azure IoT offre diverse opzioni, ognuna appropriata per diversi set di requisiti dei clienti:

* Gli [acceleratori di soluzioni Azure IoT](index.md) sono una raccolta di livello aziendale di [acceleratori di soluzioni](iot-suite-what-are-solution-accelerators.md) basata sulla piattaforma distribuita come servizio (PaaS) di Azure che consentono di velocizzare lo sviluppo di soluzioni IoT personalizzate.

* [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions) è una soluzione SaaS (Software-as-a-Service) che usa un approccio basato su modelli per consentire la creazione di soluzioni IoT di livello aziendale senza competenze nello sviluppo di soluzioni cloud.

## <a name="azure-iot-hub"></a>Hub IoT Azure

Hub IoT di Azure è la piattaforma PaaS principale di Azure usata sia da Microsoft IoT Central che dagli acceleratori di soluzioni Azure IoT. L'hub IoT supporta comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e una soluzione cloud. L'hub IoT consente di risolvere problematiche associate all'implementazione IoT come le seguenti:

* Connettività e gestione di volumi elevati di dispositivi.
* Inserimento di volumi elevati di dati di telemetria.
* Comando e controllo dei dispositivi.
* Applicazione della sicurezza dei dispositivi.

## <a name="compare-azure-iot-solution-accelerators-and-microsoft-iot-central"></a>Confrontare gli acceleratori di soluzioni Azure IoT e Microsoft IoT Central

La scelta del prodotto Azure IoT è una parte essenziale della pianificazione della soluzione IoT. L'hub IoT è un singolo servizio di Azure che non offre, da solo, una soluzione IoT end-to-end, ma può essere usato come punto di partenza per qualsiasi soluzione IoT. Non è necessario usare gli acceleratori di soluzioni Azure IoT o Microsoft IoT Central per usare l'hub IoT. Sia gli acceleratori di soluzioni Azure IoT che Microsoft IoT Central usano l'hub IoT insieme ad altri servizi di Azure. La tabella seguente offre un riepilogo delle differenze principali tra gli acceleratori di soluzioni Azure IoT e Microsoft IoT Central per consentire la scelta dell'opzione corretta per i propri requisiti:

|                        | Acceleratori di soluzioni Azure IoT | Microsoft IoT Central |
| ---------------------- | --------- | ----------- |
| Utilizzo primario | Velocizzare lo sviluppo di una soluzione IoT personalizzata che necessita della massima flessibilità. | Accelerare il time-to-market per soluzioni IoT semplici che non richiedono una personalizzazione approfondita dei servizi. |
| Accesso ai servizi PaaS sottostanti          | L'utente ha accesso ai servizi di Azure sottostanti per gestirli o, se necessario, sostituirli. | SaaS. Soluzione completamente gestita. I servizi sottostanti non sono esposti. |
| Flessibilità            | Elevata. Il codice per i microservizi è open source e può essere modificato come si preferisce. È anche possibile personalizzare l'infrastruttura di distribuzione.| Media. È possibile usare l'esperienza utente basata su browser predefinita per personalizzare il modello di soluzione e aspetti dell'interfaccia utente. L'infrastruttura non è personalizzabile perché i diversi componenti non sono esposti.|
| Livello di competenze                 | Medio-alto. Sono necessarie competenze in Java o .NET per personalizzare il back-end della soluzione e competenze in JavaScript per personalizzare la visualizzazione. | Bassa. Sono necessarie competenze di modellazione per personalizzare la soluzione. Non sono richieste competenze nella creazione di codice. |
| Esperienza iniziale | Gli acceleratori di soluzioni implementano scenari IoT comuni, distribuibili in pochi minuti. | I modelli di applicazione e i modelli di dispositivo forniscono modelli predefiniti distribuibili in pochi minuti. |
| Prezzi                | È possibile ottimizzare i servizi per controllare i costi. | Struttura di prezzi semplice e prevedibile. |

La scelta del prodotto da usare per creare la propria soluzione IoT è determinata in definitiva da:

* Requisiti aziendali.
* Tipo di soluzione che si vuole creare.
* Set di competenze dell'organizzazione per la creazione e la gestione della soluzione a lungo termine.

## <a name="next-steps"></a>Passaggi successivi

In base al prodotto e all'approccio scelti, i passaggi successivi consigliati sono i seguenti.

* **Acceleratori di soluzioni Azure IoT**: [Quali sono gli acceleratori di soluzioni di Azure IoT?](iot-suite-what-are-solution-accelerators.md)
* **Microsoft IoT Central**: [Microsoft IoT Central](https://www.microsoft.com/internet-of-things/iot-central-saas-solutions).
* **Hub IoT**: [Panoramica del servizio hub IoT di Azure](../iot-hub/iot-hub-what-is-iot-hub.md).

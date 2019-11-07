---
title: Connettere l'applicazione Azure IoT Central a Dynamics 365 for Field Service | Microsoft Docs
description: Informazioni su come creare una soluzione end-to-end con Azure IoT Central e Dynamics 365 for Field Service
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 957e9337bf8ea5941b140ba4f3266417d36df6a7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498767"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Creare una soluzione end-to-end con Azure IoT Central e Dynamics 365 for Field Service 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Gli sviluppatori possono abilitare l'integrazione dell'applicazione Azure IoT Central con altri sistemi aziendali. 


Ad esempio, in una soluzione di Gestione rifiuti connessa è possibile ottimizzare l'invio dei camion di raccolta rifiuti in base ai dati dei sensori IoT inviati dai cassonetti connessi. Nell'[applicazione Gestione rifiuti connessa in IoT Central](./tutorial-connected-waste-management.md) è possibile configurare regole e azioni e impostarle per attivare la creazione di avvisi in Dynamics for Field Service. Per abilitare questo scenario si usa Microsoft Flow, che si può configurare direttamente in IoT Central per l'automazione dei flussi di lavoro tra applicazioni e servizi. Inoltre, in base alle attività dei servizi in Field Service, è possibile restituire le informazioni ad Azure IoT Central. 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Come connettere l'applicazione Azure IoT Central a Dynamics 365 for Field Service 

I processi di integrazione end-to-end seguenti possono essere implementati facilmente grazie a una semplice esperienza di configurazione:
* Azure IoT Central può inviare informazioni sulle anomalie dei dispositivi a Connected Field Service (come un avviso IoT) per la diagnosi.
* Connected Field Service può creare casi o ordini di lavoro attivati da anomalie dei dispositivi.
* Connected Field Service può pianificare le ispezioni dei tecnici per evitare tempi di inattività.
* Il dashboard del dispositivo Azure IoT Central può essere aggiornato con le informazioni di pianificazione e assistenza pertinenti.


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sui [modelli di IoT Central per enti pubblici](./overview-iot-central-government.md)
* Altre informazioni su [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)
* Altre informazioni su [Dynamics 365 for Field Service](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)

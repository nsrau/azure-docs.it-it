---
title: Architettura di riferimento per una soluzione di monitoraggio della qualità dell'acqua compilata con Azure IoT Central | Microsoft Docs
description: Informazioni sui concetti relativi a una soluzione di monitoraggio della qualità dell'acqua compilata con Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: cffa571028dac192e8b8806ee9ec932cf89eb1fe
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498775"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Architettura di riferimento per il monitoraggio qualità acqua 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

È possibile compilare soluzioni di monitoraggio della qualità dell'acqua con il **modello di app di Azure IoT Central** come applicazione IoT di base. Questo articolo offre indicazioni di alto livello sull'architettura di riferimento per la compilazione di una soluzione end-to-end. 


![Architettura di monitoraggio della qualità acqua](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Concetti:

1. Dispositivi e connettività  
1. IoT Central 
2. Estendibilità e integrazioni
3. Applicazioni aziendali

Verranno ora esaminati i componenti chiave che in genere fanno parte di una soluzione di monitoraggio della qualità dell'acqua.

## <a name="devices-and-connectivity"></a>Dispositivi e connettività 
In questa sezione, i dispositivi usati per il monitoraggio della qualità dell'acqua o per il monitoraggio del consumo dell'acqua saranno denominati, in termini generici, dispositivi acquatici intelligenti. I dispositivi acquatici intelligenti possono essere flussimetri, monitor di qualità dell'acqua, valvole intelligenti, rilevatori di perdite e così via.

I dispositivi usati nelle soluzioni Smart Water sono in genere connessi via reti Wide Area Network a basso consumo (LPWAN) tramite operatori di rete di terze parti. Per questi tipi di dispositivi, è possibile usare il [bridge di dispositivi Azure IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) per inviare i dati del dispositivo all'applicazione IoT in Azure IoT Central. In alternativa, è possibile che i gateway del dispositivo siano compatibili con IP e possano connettersi direttamente a IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central è una piattaforma per app IoT che consente di usare la propria soluzione IoT fin da subito. È possibile personalizzare la soluzione e integrarla con servizi di terze parti.
Dopo aver connesso i dispositivi acquatici intelligenti a IoT Central, è possibile gestire e controllare i dispositivi, il monitoraggio, gli avvisi, l'interfaccia utente con controllo degli accessi in base al ruolo, i dashboard configurabili con informazioni dettagliate e le opzioni di estendibilità. 

## <a name="extensibility-and-integrations"></a>Estendibilità e integrazioni 
È possibile estendere l'applicazione IoT su IoT Central e, facoltativamente:
* trasformare e integrare i dati IoT per l'analisi avanzata, ad esempio il training di modelli di Machine Learning, tramite l'esportazione continua dei dati dall'applicazione IoT Central
* automatizzare i flussi di lavoro in altri sistemi attivando azioni tramite Microsoft Flow o webhook dall'applicazione IoT Central
* eseguire l'accesso a livello di codice all'applicazione IoT su IoT Central tramite le API di IoT Central

## <a name="business-applications"></a>Applicazioni aziendali 
I dati IoT possono essere utili per supportare diverse applicazioni aziendali all'interno di un sistema di servizi idrici. Per informazioni su come connettere l'applicazione di monitoraggio della qualità dell'acqua di IoT Central con i servizi sul campo, vedere l'articolo su [come eseguire l'integrazione con Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [creare un'applicazione IoT Central di monitoraggio della qualità dell'acqua](./tutorial-water-quality-monitoring.md)
* Altre informazioni sui [modelli di IoT Central per enti pubblici](./overview-iot-central-government.md)
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)


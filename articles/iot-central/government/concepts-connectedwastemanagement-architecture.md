---
title: Architettura di riferimento per una soluzione di gestione rifiuti connessa creata con Azure IoT Central | Microsoft Docs
description: Informazioni sui concetti relativi a una soluzione di gestione rifiuti connessa creata con Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2905a7ab7999c66bde0a705ff129fa049617df10
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498874"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Architettura di riferimento per la gestione rifiuti connessa 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

È possibile creare una soluzione di gestione rifiuti connessa con il **modello di app di Azure IoT Central** come applicazione IoT di base. Questo articolo offre indicazioni generali sull'architettura di riferimento per la creazione di una soluzione end-to-end. 

![Architettura di gestione rifiuti connessa](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)


Concetti:

1. Dispositivi e connettività  
1. IoT Central 
2. Estendibilità e integrazioni
3. Applicazioni aziendali

Verranno ora esaminati i componenti chiave che in genere fanno parte di una soluzione di monitoraggio del consumo idrico.

## <a name="devices-and-connectivity"></a>Dispositivi e connettività 
I dispositivi usati in ambienti aperti, ad esempio i cassonetti dei rifiuti, possono in genere essere connessi via reti Wide Area Network a basso consumo (LPWAN) tramite operatori di rete di terze parti. Per questi tipi di dispositivi, è possibile usare il [bridge di dispositivi Azure IoT Central](https://docs.microsoft.com/azure/iot-central/core/howto-build-iotc-device-bridge) per inviare i dati dei dispositivi all'applicazione IoT in Azure IoT Central. In alternativa, è possibile usare gateway di dispositivo compatibili con IP e in grado di connettersi direttamente a IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central è una piattaforma per app IoT che consente di usare la propria soluzione IoT fin da subito. È possibile personalizzare la soluzione e integrarla con servizi di terze parti.
Dopo aver connesso i dispositivi intelligenti a IoT Central, è possibile gestire e controllare i dispositivi, eseguire il monitoraggio e gestire gli avvisi, nonché usare un'interfaccia utente con controllo degli accessi in base al ruolo, dashboard configurabili con informazioni dettagliate e opzioni di estendibilità. 

## <a name="extensibility-and-integrations"></a>Estendibilità e integrazioni 
È possibile estendere l'applicazione IoT in IoT Central e, facoltativamente:
* Trasformare e integrare i dati IoT per l'analisi avanzata, ad esempio il training di modelli di Machine Learning, tramite l'esportazione continua dei dati dall'applicazione IoT Central. 
* Automatizzare i flussi di lavoro in altri sistemi attivando azioni tramite Microsoft Flow o webhook dall'applicazione IoT Central.
* Eseguire l'accesso a livello di codice all'applicazione IoT in IoT Central tramite le API di IoT Central.

## <a name="business-applications"></a>Applicazioni aziendali 
I dati IoT possono essere usati per supportare diverse applicazioni aziendali all'interno di un sistema di gestione rifiuti. Per informazioni su come connettere l'applicazione di gestione rifiuti connessa di IoT Central ai servizi sul campo, vedere l'esercitazione su [come eseguire l'integrazione con Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 

## <a name="next-steps"></a>Passaggi successivi
* Vedere le informazioni su come [creare un'applicazione IoT Central di gestione dei rifiuti connessa](./tutorial-connected-waste-management.md)
* Ottenere altre informazioni sui [modelli di IoT Central per enti pubblici](./overview-iot-central-government.md)
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central)


---
title: Architettura di monitoraggio pazienti continuo in Azure IoT Central | Microsoft Docs
description: "Esercitazione: Informazioni sull'architettura di una soluzione per il monitoraggio continuo dei pazienti."
author: philmea
ms.author: philmea
ms.date: 09/14/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: ffecd09d1084188195da83568ab3fe32ef2cdaac
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90972219"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architettura del monitoraggio pazienti continuo

Questo articolo descrive l'architettura di una soluzione creata dal modello di applicazione per il **monitoraggio continuo dei pazienti**:

È possibile creare soluzioni di monitoraggio continuo dei pazienti usando il modello di app fornito e l'architettura descritta di seguito come linee guida.

:::image type="content" source="media/cpm-architecture.png" alt-text="Architettura del monitoraggio pazienti continuo":::

## <a name="details"></a>Dettagli

Questa sezione illustra in maggior dettaglio ogni parte del diagramma dell'architettura:

### <a name="bluetooth-low-energy-ble-medical-devices"></a>Dispositivi medici Bluetooth Low Energy (BLE)

Molti dispositivi medici indossabili usati nelle soluzioni IoT per i servizi sanitari sono di tipo BLE. Questi dispositivi non possono comunicare direttamente con il cloud ed è necessario usare un gateway per scambiare dati con la soluzione cloud. Questa architettura usa un'applicazione per telefoni cellulari come gateway.

### <a name="mobile-phone-gateway"></a>Gateway telefono cellulare

La funzione principale dell'applicazione per telefoni cellulari prevede la raccolta di dati tramite BLE dai dispositivi medici e la comunicazione con Azure IoT Central. L'app inoltre illustra ai pazienti come configurare il dispositivo e consente di visualizzare i dati sanitari personali. Altre soluzioni potrebbero usare un gateway tablet o un gateway statico in una stanza ospedaliera. È disponibile un'applicazione per dispositivi mobili open source di esempio per Android e iOS da usare come punto di partenza per lo sviluppo di applicazioni. Per altre informazioni, vedere l'[app per dispositivi mobili per il monitoraggio continuo dei pazienti di IoT Central](https://docs.microsoft.com/samples/iot-for-all/iotc-cpm-sample/iotc-cpm-sample/).

### <a name="export-to-azure-api-for-fhirreg"></a>Esportare nell'API di Azure per FHIR&reg;

Azure IoT Central è una soluzione conforme a HIPAA e dotata di certificazione HITRUST&reg;. È anche possibile inviare i dati sanitari dei pazienti ad altri servizi tramite l'[API di Azure per FHIR](../../healthcare-apis/overview.md). L'API di Azure per FHIR è un'API basata su standard per i dati sanitari clinici. Il [connettore di Azure IoT per FHIR](https://docs.microsoft.com/azure/healthcare-apis/iot-fhir-portal-quickstart) consente di usare l'API di Azure per FHIR come destinazione per l'esportazione continua di dati da IoT Central.

### <a name="machine-learning"></a>Machine Learning

Usare modelli di Machine Learning con i dati FHIR per generare informazioni dettagliate e supportare i processi decisionali del team di assistenza sanitaria. Per altre informazioni, vedere la [Documentazione di Azure Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Dashboard del provider

Usare i dati dell'API di Azure per FHIR per creare un dashboard di informazioni sui pazienti o integrarlo direttamente in una cartella clinica elettronica usata dai team di assistenza sanitaria. I team di assistenza sanitaria possono usare il dashboard per assistere i pazienti e identificare i primi segnali di avviso di deterioramento della salute. Per altre informazioni, vedere l'esercitazione [Creare un dashboard di provider di Power BI](howto-health-data-triage.md).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo consigliato consiste nell'acquisire [informazioni su come distribuire un modello di applicazione per il monitoraggio continuo dei pazienti](tutorial-continuous-patient-monitoring.md).

---
title: Architettura di monitoraggio pazienti continuo in Azure IoT Central | Microsoft Docs
description: Informazioni sull'architettura della soluzione di monitoraggio pazienti continuo.
author: philmea
ms.author: philmea
ms.date: 10/24/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: cf8cfc5fe75ff5364f812af034d772c7aadd891c
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027486"
---
# <a name="continuous-patient-monitoring-architecture"></a>Architettura del monitoraggio pazienti continuo

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

È possibile creare soluzioni di monitoraggio pazienti continuo sfruttando il modello di app fornito e usando l'architettura descritta di seguito come linee guida.

>[!div class="mx-imgBorder"] 
>![Architettura di Monitoraggio pazienti continuo](media/cpm-architecture.png)

1. Dispositivi medicali che comunicano tramite Bluetooth a basso consumo (Bluetooth Low Energy, BLE)
1. Gateway telefono cellulare che riceve dati Bluetooth a basso consumo e li invia a IoT Central
1. Esportazione continua dei dati sanitari dei pazienti all'API di Azure per FHIR&reg;
1. Machine Learning basato su dati interoperativi
1. Dashboard del team di assistenza basato sui dati FHIR

## <a name="details"></a>Dettagli
Questa sezione illustra in modo più dettagliato ogni parte del diagramma dell'architettura.

### <a name="ble-medical-devices"></a>Dispositivi medicali Bluetooth a basso consumo
Molti dispositivi medicali indossabili usati nel settore dell'assistenza sanitaria IoT sono dispositivi Bluetooth a basso consumo che non sono in grado di comunicare direttamente con il cloud e che devono quindi passare attraverso un gateway. Questa architettura suggerisce di usare un'applicazione per telefoni cellulari come gateway.

### <a name="mobile-phone-gateway"></a>Gateway telefono cellulare
La funzione principale dell'applicazione per telefoni cellulari prevede l'inserimento di dati Bluetooth a basso consumo dai dispositivi medicali e la comunicazione con Azure IoT Central. L'app può inoltre guidare i pazienti attraverso un flusso di configurazione e provisioning del dispositivo e aiutarli a visualizzare i dati sanitari personali. Altre soluzioni possono usare un gateway tablet o un gateway statico, se all'interno di una stanza ospedaliera, per ottenere lo stesso flusso di comunicazione.

### <a name="export-to-azure-api-for-fhirreg"></a>Esportare nell'API di Azure per FHIR&reg;
Azure IoT Central garantisce la conformità HIPAA e la certificazione HITRUST&reg;, ma potrebbe essere necessario inviare i dati sanitari dei pazienti all'API di Azure per FHIR. L'[API di Azure per FHIR](../../healthcare-apis/overview.md) è un'API conforme, basata su standard e completamente gestita per i dati sanitari clinici, che consente di creare nuovi sistemi di engagement con i dati sanitari. Consente lo scambio di dati rapido tramite le API per FHIR ed è supportata da un'offerta PaaS (Platform-as-a-Service) gestita nel cloud. Usando la funzionalità di esportazione dei dati continua di IoT Central, è possibile inviare i dati all'API di Azure per FHIR.

### <a name="machine-learning"></a>Machine learning
Dopo aver aggregato i dati e averli convertiti nel formato FHIR, è possibile creare modelli di Machine Learning in grado di arricchire le informazioni dettagliate e abilitare processi decisionali più intelligente per il team di assistenza sanitaria. È disponibile un'ampia gamma di servizi che possono essere usati per creare, eseguire il training e distribuire modelli di Machine Learning. Per altre informazioni su come usare le offerte di Machine Learning di Azure, vedere la [documentazione di Machine Learning](../../machine-learning/index.yml).

### <a name="provider-dashboard"></a>Dashboard del provider
I dati disponibili nell'API di Azure per FHIR possono essere usati per creare un dashboard di informazioni dettagliate sui pazienti o possono essere integrati direttamente in un sistema di cartelle mediche elettroniche per consentire ai team di assistenza sanitaria di visualizzare lo stato dei pazienti. I team di assistenza sanitaria possono usare questo dashboard per intraprendere le misure necessarie per i pazienti che richiedono assistenza e per individuare in anticipo eventuali segnali di peggioramento. Per informazioni su come creare un dashboard del provider in tempo reale di Power BI, seguire la [guida pratica](howto-health-data-triage.md).

## <a name="next-steps"></a>Passaggi successivi
* [Informazioni su come distribuire un modello di applicazione di monitoraggio pazienti continuo](tutorial-continuous-patient-monitoring.md)
---
title: Centro di micro-adempimenti di Azure IoT Central | Microsoft Docs
description: Informazioni su come creare un'applicazione per centri di micro-adempimenti usando il modello di IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020880"
---
# <a name="micro-fulfillment-center-architecture"></a>Architettura del centro di micro-adempimenti

Le soluzioni per centri di micro-adempimenti consentono di connettere, monitorare e gestire digitalmente tutti gli aspetti di un centro di adempimenti completamente automatizzato per ridurre i costi eliminando i tempi di inattività e aumentando la sicurezza e l'efficienza generale. Queste soluzioni possono essere create usando uno dei modelli di applicazione disponibili in IoT Central e l'architettura riportata di seguito come linee guida.

![Analisi punti vendita di Azure IoT Central](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Set di sensori IoT che inviano dati di telemetria a un dispositivo gateway
- Dispositivi gateway che inviano dati di telemetria e dati analitici aggregati a IoT Central
- Esportazione continua dei dati al servizio di Azure desiderato per la manipolazione
- I dati possono essere strutturati nel formato desiderato e inviati a un servizio di archiviazione
- Le applicazioni aziendali possono eseguire query sui dati e generare dati analitici a supporto delle operazioni di vendita al dettaglio
 
Verranno ora esaminati i componenti chiave che in genere fanno parte di una soluzione per centro di micro-adempimenti.

## <a name="robotic-carriers"></a>Operatori robotici

Una soluzione per centro di micro-adempimenti includerà probabilmente un ampio set di operatori robotici che generano tipi diversi di segnali di telemetria. Questi segnali possono essere inseriti da un dispositivo gateway, aggregati e quindi inviati a IoT Central come illustrato sul lato sinistro del diagramma dell'architettura.  

## <a name="condition-monitoring-sensors"></a>Sensori di monitoraggio delle condizioni

Una soluzione IoT inizia con un set di sensori che acquisiscono segnali significativi all'interno di un centro di adempimenti, come indicato dai tipi diversi di sensori a sinistra del diagramma dell'architettura riportato sopra.

## <a name="gateway-devices"></a>Dispositivi gateway

Molti sensori IoT possono inviare segnali non elaborati direttamente al cloud o a un dispositivo gateway situato nelle vicinanze. Il dispositivo gateway esegue l'aggregazione dei dati a livello perimetrale prima di inviare dati analitici di riepilogo a un'applicazione IoT Central. I dispositivi gateway sono anche responsabili dell'inoltro delle operazioni di comando e controllo ai dispositivi del sensore quando applicabile. 

## <a name="iot-central-application"></a>Applicazione IoT Central

L'applicazione Azure IoT Central inserisce i dati provenienti da tipi diversi di sensori IoT, robot e dispositivi gateway all'interno dell'ambiente del centro di adempimenti e genera un set di informazioni dettagliate significative.

Azure IoT Central offre all'esercente anche un'esperienza personalizzata consentendo di monitorare e gestire in remoto i dispositivi dell'infrastruttura.

## <a name="data-transform"></a>Trasformazione dei dati
L'applicazione Azure IoT Central all'interno di una soluzione può essere configurata per l'esportazione di informazioni aggregate o non elaborate in un set di servizi PaaS (Platform-as-a Service) di Azure che possono eseguire la manipolazione dei dati e arricchirli prima di inviarli a un'applicazione aziendale. 

## <a name="business-application"></a>Applicazione aziendale
I dati IoT possono essere utili per supportare diversi tipi di applicazioni aziendali distribuite all'interno di un ambiente retail. Un responsabile o un dipendente del centro di adempimenti può usare queste applicazioni per visualizzare informazioni aziendali dettagliate e intraprendere azioni significative in tempo reale. Per informazioni su come creare un dashboard di Power BI in tempo reale per il team di vendita al dettaglio, seguire l'[esercitazione](./tutorial-in-store-analytics-create-app-pnp.md).

## <a name="next-steps"></a>Passaggi successivi
* Iniziare con il modello di applicazione [Centro di micro-adempimenti](https://aka.ms/checkouttemplate). 
* Esaminare l'[esercitazione](https://aka.ms/mfc-tutorial) che illustra come creare una soluzione usando il modello di app Centro di micro-adempimenti.

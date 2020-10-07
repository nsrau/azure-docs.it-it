---
title: Architettura dell'analisi punti vendita
description: Informazioni su come creare un'applicazione per l'analisi dei punti vendita usando il modello di applicazione Pagamento in IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80999012"
---
# <a name="in-store-analytics-architecture"></a>Architettura di analisi punti vendita



Le soluzioni per l'analisi dei punti vendita consentono di monitorare varie condizioni all'interno di un ambiente di vendita al dettaglio. Queste soluzioni possono essere create usando uno dei modelli di applicazione disponibili in IoT Central e l'architettura riportata di seguito come linee guida.


![Analisi punti vendita di Azure IoT Central](./media/architecture/store-analytics-architecture-frame.png)

- Set di sensori IoT che inviano dati di telemetria a un dispositivo gateway
- Dispositivi gateway che inviano dati di telemetria e dati analitici aggregati a IoT Central
- Esportazione continua dei dati al servizio di Azure desiderato per la manipolazione
- I dati possono essere strutturati nel formato desiderato e inviati a un servizio di archiviazione
- Le applicazioni aziendali possono eseguire query sui dati e generare dati analitici a supporto delle operazioni di vendita al dettaglio
 
Verranno ora esaminati i componenti chiave che in genere fanno parte di una soluzione per l'analisi dei punti vendita.

## <a name="condition-monitoring-sensors"></a>Sensori di monitoraggio delle condizioni

Una soluzione IoT inizia con un set di sensori che acquisiscono segnali significativi all'interno di un ambiente di vendita al dettaglio, come indicato dai tipi diversi di sensori a sinistra del diagramma dell'architettura riportato sopra.

## <a name="gateway-devices"></a>Dispositivi gateway

Molti sensori IoT possono inviare segnali non elaborati direttamente al cloud o a un dispositivo gateway situato nelle vicinanze. Il dispositivo gateway esegue l'aggregazione dei dati a livello perimetrale prima di inviare dati analitici di riepilogo a un'applicazione IoT Central. I dispositivi gateway sono anche responsabili dell'inoltro delle operazioni di comando e controllo ai dispositivi del sensore quando applicabile. 

## <a name="iot-central-application"></a>Applicazione IoT Central

L'applicazione Azure IoT Central inserisce i dati provenienti da tipi diversi di sensori IoT e dispositivi gateway all'interno dell'ambiente di retail e genera un set di informazioni dettagliate significative.

Azure IoT Central offre all'esercente anche un'esperienza personalizzata consentendo di monitorare e gestire in remoto i dispositivi dell'infrastruttura.

## <a name="data-transform"></a>Trasformazione dei dati
L'applicazione Azure IoT Central all'interno di una soluzione può essere configurata per l'esportazione di dati analitici non elaborati o aggregati in un set di servizi Azure PaaS (Platform-as-a Service) che possono eseguire la manipolazione dei dati e arricchirli prima di inviarli a un'applicazione aziendale. 

## <a name="business-application"></a>Applicazione aziendale
I dati IoT possono essere utili per supportare diversi tipi di applicazioni aziendali distribuite all'interno di un ambiente retail. Un responsabile o un dipendente del punto vendita può usare queste applicazioni per visualizzare informazioni aziendali dettagliate e intraprendere azioni significative in tempo reale. Per informazioni su come creare un dashboard di Power BI in tempo reale per il team di vendita al dettaglio, seguire l'[esercitazione](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Passaggi successivi
* Iniziare con i modelli di applicazione [Analisi punti vendita - Pagamento](https://aka.ms/checkouttemplate) e [Analisi punti vendita - Monitoraggio delle condizioni](https://aka.ms/conditiontemplate). 
* Vedere l'[esercitazione end-to-end](https://aka.ms/storeanalytics-tutorial) che illustra come creare una soluzione usando uno dei modelli di applicazione per l'analisi dei punti vendita.

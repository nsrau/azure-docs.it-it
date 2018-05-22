---
title: Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto - Azure| Microsoft Docs
description: Questo articolo descrive come integrare i dati di una scheda SIM di Telefónica nella soluzione di monitoraggio remoto.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto

## <a name="overview"></a>Panoramica
I dispositivi IoT si connettono spesso al cloud tramite una scheda SIM che consente loro di inviare flussi di dati da qualsiasi posizione. La soluzione di monitoraggio remoto di Azure IoT consente l'integrazione dei dati di gestione della scheda SIM, in modo che gli operatori possano anche monitorare l'integrità del dispositivo attraverso i dati forniti dalla SIM. Monitoraggio remoto supporta l'integrazione predefinita con Telefónica IoT, consentendo ai clienti della piattaforma di connettività IoT di sincronizzare le soluzioni di gestione dei dati di connettività delle schede SIM dei propri dispositivi. Questa soluzione può essere estesa per supportare altri operatori telefonici tramite repository GitHub.
In questa esercitazione si apprenderà come:
* Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto
* Visualizzare i dati di telemetria in tempo reale
* Visualizzare i dati della scheda SIM 

## <a name="telefonica-iot-integration-setup"></a>Configurare l'integrazione con Telefónica IoT

### <a name="prerequisites"></a>Prerequisiti
Per sincronizzare i dati di connettività con la soluzione di monitoraggio remoto di Azure, seguire questi passaggi:

1.  Compilare un modulo di richiesta sul [sito di Telefónica](https://iot.telefonica.com/contact), selezionare l'opzione **Azure Remote Monitoring** (Monitoraggio remoto di Azure) specificando i propri dati di contatto.
2.  Telefónica attiverà l'account richiesto. 
3.  Se non si è ancora clienti di Telefónica e si desidera usufruire dei vantaggi di questo servizio o di altri servizi di connettività IoT pronti per il cloud, visitare il [sito di Telefónica](https://iot.telefonica.com/contact) e selezionare l'opzione **Connectivity** (Connettività).

### <a name="telefonica-sim-setup"></a>Configurare la scheda SIM di Telefónica
L'associazione della scheda SIM di Telefónica con l'ID del dispositivo gemello di Azure sarà basata sulla proprietà "alias" della scheda SIM di Telefónica IoT. 

Passare al [portale della piattaforma di connettività di Telefónica IoT](https://m2m-movistar-es.telefonica.com/), in SIM Inventory (Registro SIM) selezionare la SIM e aggiornare la proprietà "alias" di ogni SIM con l'ID del dispositivo gemello desiderato. 

Questa attività può essere eseguita anche in blocco. Per informazioni, vedere i manuali dell'utente della piattaforma di connettività di Telefónica IoT.

![Aggiornamento di Telefónica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Per connettere il proprio dispositivo a Monitoraggio remoto, è possibile seguire queste esercitazioni in cui viene usato il linguaggio [C](iot-suite-connecting-devices-linux.md) o [Node](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visualizzare i dati di telemetria del dispositivo e le proprietà della scheda SIM
Dopo aver configurato correttamente l'account di Telefónica e aver connesso il dispositivo, è possibile visualizzare i dettagli del dispositivo e i dati della scheda SIM.
I parametri di connettività seguenti possono essere pubblicati:
* ICCID
* IP
* Presenza sulla rete
* Stato della SIM
* Posizione basata su rete
* Utilizzo del traffico dati

![Dashboard](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le informazioni generali su come integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto di Azure IoT, è possibile seguire i passaggi consigliati seguenti per le soluzioni preconfigurate:

* [Gestire la soluzione di monitoraggio remoto di Azure IoT](iot-suite-remote-monitoring-explore.md)
* [Eseguire il monitoraggio avanzato](iot-suite-remote-monitoring-monitor.md)
* [Gestire i dispositivi](iot-suite-remote-monitoring-manage.md)
* [Risolvere i problemi dei dispositivi](iot-suite-remote-monitoring-maintain.md)


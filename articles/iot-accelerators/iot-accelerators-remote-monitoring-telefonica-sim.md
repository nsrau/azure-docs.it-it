---
title: Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto - Azure| Microsoft Docs
description: Questo articolo descrive come integrare i dati di una scheda SIM di Telefónica nella soluzione di monitoraggio remoto.
services: iot-suite
suite: iot-suite
author: hegate
manager: timlt
ms.author: hegate
ms.service: iot-suite
ms.date: 05/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 590a24113d66edacc6edcfe988330f643f1aa57a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367553"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto

I dispositivi IoT si connettono spesso al cloud tramite una scheda SIM che consente loro di inviare flussi di dati da qualsiasi posizione. La soluzione di monitoraggio remoto di Azure IoT consente l'integrazione dei dati di IoT Managed Connectivity, in modo che gli operatori possano monitorare l'integrità del dispositivo anche attraverso i dati offerti dalla SIM IoT.

Il monitoraggio remoto supporta l'integrazione predefinita con la connettività IoT di Telefónica, consentendo ai clienti della piattaforma di connettività IoT di sincronizzare i dati di connettività delle schede SIM dei dispositivi alle proprie soluzioni. Questa soluzione può essere estesa per supportare altri provider di connettività IoT mediante il [repository](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) GitHub.

In questa esercitazione si apprenderà come:

* Integrare i dati di una scheda SIM IoT di Telefónica nella soluzione di monitoraggio remoto
* Visualizzare i dati di telemetria in tempo reale
* Visualizzare i dati della scheda SIM

## <a name="telefnica-iot-integration-setup"></a>Configurazione dell'integrazione IoT con Telefónica

### <a name="prerequisites"></a>prerequisiti

Questa funzionalità aggiuntiva di monitoraggio remoto è attualmente in anteprima. Per sincronizzare i dati di connettività con la soluzione di monitoraggio remoto di Azure, seguire questi passaggi:

1. Compilare un modulo di richiesta sul [sito di Telefónica](https://iot.Telefónica.com/contact), selezionare l'opzione **Azure Remote Monitoring** (Monitoraggio remoto di Azure) specificando i propri dati di contatto.
2. Telefónica attiva l'account.
3. Se non si è ancora clienti di Telefónica e si vuole usufruire dei vantaggi di questo servizio o di altri servizi di connettività IoT pronti per il cloud, visitare il [sito di Telefónica](https://iot.Telefónica.com/contact) e selezionare l'opzione **Connectivity** (Connettività).

### <a name="telefnica-sim-setup"></a>Configurazione della scheda SIM di Telefónica
L'associazione della scheda SIM di Telefónica con l'ID del dispositivo gemello di Azure è basata sulla proprietà "alias" della scheda SIM IoT di Telefónica. 

Passare al [portale della piattaforma di connettività IoT di Telefónica](https://m2m-movistar-es.telefonica.com/) > SIM Inventory (Registro SIM) > selezionare la SIM e aggiornare la proprietà "alias" di ogni SIM con l'ID del dispositivo gemello di preferenza. Questa attività può essere eseguita anche in blocco. Per informazioni, vedere i manuali dell'utente della piattaforma di connettività IoT di Telefónica.

Questa attività può essere eseguita anche in blocco. Per informazioni, vedere i manuali dell'utente della piattaforma di connettività di Telefónica IoT.

![Aggiornamento di Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Per connettere il proprio dispositivo a Monitoraggio remoto, è possibile seguire queste esercitazioni in cui viene usato il linguaggio [C](iot-accelerators-connecting-devices-linux.md) o [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visualizzare i dati di telemetria del dispositivo e le proprietà della scheda SIM

Dopo aver configurato correttamente l'account di Telefónica e aver connesso il dispositivo, è possibile visualizzare i dettagli del dispositivo e i dati della scheda SIM.

I parametri di connettività seguenti vengono pubblicati:

* ICCID
* IP
* Presenza sulla rete
* Stato della SIM
* Posizione basata su rete
* Utilizzo del traffico dati

![dashboard](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le informazioni generali su come integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto di Azure IoT, è possibile seguire i passaggi consigliati seguenti per le soluzioni preconfigurate:

* [Gestire la soluzione di monitoraggio remoto di Azure IoT](iot-accelerators-remote-monitoring-explore.md)
* [Eseguire il monitoraggio avanzato](iot-accelerators-remote-monitoring-monitor.md)
* [Gestire i dispositivi](iot-accelerators-remote-monitoring-manage.md)
* [Risolvere i problemi dei dispositivi](iot-accelerators-remote-monitoring-maintain.md)


---
title: Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto - Azure| Microsoft Docs
description: Questo articolo descrive come integrare i dati di una SIM di Telefónica nella soluzione di monitoraggio remoto.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442240"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrare i dati di una scheda SIM nella soluzione di monitoraggio remoto

I dispositivi IoT si connettono spesso al cloud tramite una scheda SIM che consente loro di inviare flussi di dati da qualsiasi posizione. La soluzione di monitoraggio remoto di Azure IoT consente l'integrazione dei dati di IoT Managed Connectivity, in modo che gli operatori possano monitorare l'integrità del dispositivo anche attraverso i dati offerti dalla SIM IoT.

Il monitoraggio remoto offre un'integrazione predefinita con la connettività IoT di Telefónica, consentendo ai clienti che usano la piattaforma di connettività IoT di sincronizzare i dati di connettività SIM del dispositivo con le proprie soluzioni. Questa soluzione può essere estesa per supportare altri provider di connettività IoT mediante il [repository](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) GitHub.

In questa esercitazione si apprenderà come:

* Integrare i dati di una scheda SIM di Telefónica nella soluzione di monitoraggio remoto
* Visualizzare i dati di telemetria in tempo reale
* Visualizzare i dati della scheda SIM

## <a name="telefnica-iot-integration-setup"></a>Configurazione dell'integrazione IoT di Telefónica

### <a name="prerequisites"></a>Prerequisiti

Questa funzionalità aggiuntiva di monitoraggio remoto è attualmente in anteprima. Per sincronizzare i dati di connettività con la soluzione di monitoraggio remoto di Azure, seguire questi passaggi:

1. Compilare una richiesta sul [sito di Telefónica](https://iot.telefonica.com/contact), selezionare l'opzione **Azure Remote Monitoring** (Monitoraggio remoto di Azure) includendo le informazioni di contatto.
2. Telefónica attiva l'account.
3. Se non si è ancora cliente di Telefónica e si desidera sfruttare questo o altri servizi pronti per il cloud della connettività IoT, visitare il [sito di Telefónica](https://iot.telefonica.com/) e selezionare l'opzione **Connectivity** (Connettività).

### <a name="telefnica-sim-setup"></a>Configurazione della SIM di Telefónica
L'associazione della SIM di Telefónica e dell'ID del dispositivo gemello di Azure si basa sulla proprietà "alias" della SIM IoT di Telefónica. 

Passare a [Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) (Portale della piattaforma di connettività IoT di Telefónica) > SIM Inventory (Inventario SIM) > Select your SIM (Seleziona la tua SIM) e aggiornare ogni "alias" della SIM con l'ID del dispositivo gemello desiderato. Questa attività può essere eseguita anche in blocco. Fare riferimento ai manuali dell'utente della piattaforma di connettività IoT di Telefónica.

Questa attività può essere eseguita anche in blocco. Fare riferimento ai manuali dell'utente della piattaforma di connettività IoT di Telefónica.

![Telefónica Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Per connettere il proprio dispositivo a Monitoraggio remoto, è possibile seguire queste esercitazioni in cui viene usato il linguaggio [C](iot-accelerators-connecting-devices-linux.md) o [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Visualizzare i dati di telemetria del dispositivo e le proprietà della scheda SIM

Dopo aver configurato correttamente l'account di Telefónica e aver collegato il dispositivo, è possibile visualizzare i dettagli del dispositivo e i dati della SIM.

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

* [Gestire la soluzione di monitoraggio remoto di Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Eseguire il monitoraggio avanzato](iot-accelerators-remote-monitoring-monitor.md)
* [Gestire i dispositivi](iot-accelerators-remote-monitoring-manage.md)
* [Risolvere i problemi dei dispositivi](iot-accelerators-remote-monitoring-maintain.md)


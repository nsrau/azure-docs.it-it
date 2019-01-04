---
title: Migrazione dell'hub IoT di Azure alla funzionalità Impostazioni di diagnostica | Microsoft Docs
description: Come aggiornare l'hub IoT di Azure per l'uso della funzionalità Impostazioni di diagnostica anziché Monitoraggio operazioni per monitorare lo stato delle operazioni nell'hub IoT in tempo reale.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: kgremban
ms.openlocfilehash: 4a1517c1d5bb0f34c0f1b0ec81d074f8ec39aff5
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53546581"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Eseguire la migrazione dell'hub IoT dalla funzionalità Monitoraggio operazioni a Impostazioni di diagnostica

I clienti che usano la funzionalità [Monitoraggio operazioni](iot-hub-operations-monitoring.md) per tenere traccia delle operazioni nell'hub IoT possono eseguire la migrazione di tale flusso di lavoro alla funzionalità [Impostazioni di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md), una funzionalità di Monitoraggio di Azure. La funzionalità Impostazioni di diagnostica fornisce informazioni di diagnostica a livello di risorse per numerosi servizi di Azure.

La funzionalità Monitoraggio operazioni dell'hub IoT è deprecata e verrà rimossa nelle versioni future. In questo articolo viene descritta la procedura dettagliata per spostare i carichi di lavoro dalla funzionalità Monitoraggio operazioni alla funzionalità Impostazioni di diagnostica. Per altre informazioni sulla sequenza temporale relativa alla funzionalità deprecata, vedere [Monitorare le soluzioni IoT di Azure con Monitoraggio di Azure e Integrità risorse di Azure](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aggiornare l'hub IoT

Per aggiornare l'hub IoT nel portale di Azure, attivare la funzionalità Impostazioni di diagnostica e quindi disattivare la funzionalità Monitoraggio operazioni.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>Disattivare la funzionalità Monitoraggio operazioni

Dopo aver verificato la nuova impostazione della funzionalità Impostazioni di diagnostica nel flusso di lavoro, è possibile disattivare la funzionalità Monitoraggio operazioni. 

1. Nel menu dell'hub IoT selezionare **Monitoraggio operazioni**.

2. In ciascuna categoria di monitoraggio selezionare **Nessuno**.

3. Salvare le modifiche apportate all'impostazione.

## <a name="update-applications-that-use-operations-monitoring"></a>Aggiornare le applicazioni che usano la funzionalità Monitoraggio operazioni

Gli schemi delle funzionalità Monitoraggio operazioni e Impostazioni di diagnostica variano leggermente. È importante aggiornare subito le applicazioni che usano la funzionalità Monitoraggio operazioni per eseguire il mapping allo schema usato dalla funzionalità Impostazioni di diagnostica. 

La funzionalità Impostazioni di diagnostica consente inoltre di tenere traccia di cinque nuove categorie. Dopo avere aggiornato le applicazioni per lo schema esistente, aggiungere le nuove categorie:

* Operazioni da cloud a dispositivi gemelli
* Operazioni da dispositivi gemelli a cloud
* Query dei dispositivi gemelli
* Operazioni dei processi
* Metodi diretti

Per le strutture di schemi specifiche, vedere [Informazioni sullo schema per la funzionalità Impostazioni di diagnostica](iot-hub-monitor-resource-health.md#understand-the-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitoraggio degli eventi di connessione e disconnessione di un dispositivo con bassa latenza

Per monitorare gli eventi di connessione e disconnessione di un dispositivo, è consigliabile iscriversi all'[**evento** dispositivo disconnesso](iot-hub-event-grid.md#event-types) in Griglia di eventi di Azure per ricevere avvisi e monitorare lo stato di connessione del dispositivo. Per informazioni su come integrare il dispositivo connesso e gli eventi dispositivo connesso dall'hub IoT nella soluzione IoT, usare questa [esercitazione](iot-hub-how-to-order-connection-state-events.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi](iot-hub-monitor-resource-health.md)

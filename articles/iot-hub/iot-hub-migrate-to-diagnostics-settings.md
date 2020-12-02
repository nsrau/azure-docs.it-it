---
title: Eseguire la migrazione del monitoraggio delle operazioni dell'hub Azure per i log delle risorse dell'hub Internet in monitoraggio di Azure | Microsoft Docs
description: Come aggiornare l'hub Azure Internet per usare monitoraggio di Azure anziché il monitoraggio delle operazioni per monitorare lo stato delle operazioni nell'hub Internet in tempo reale.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: dfd819d82f5e35183802e33e5d423cad4de36c38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461595"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Eseguire la migrazione dell'hub Internet delle cose dal monitoraggio delle operazioni ai log delle risorse di monitoraggio

I clienti che usano il [monitoraggio delle operazioni](iot-hub-operations-monitoring.md) per tenere traccia dello stato delle operazioni nell'hub Internet possono eseguire la migrazione del flusso di lavoro ai [log delle risorse di monitoraggio di Azure](../azure-monitor/platform/platform-logs-overview.md), una funzionalità di monitoraggio di Azure I log delle risorse forniscono informazioni di diagnostica a livello di risorsa per molti servizi di Azure.

**La funzionalità di monitoraggio delle operazioni dell'hub Internet è deprecata** ed è stata rimossa dal portale. Questo articolo illustra i passaggi per spostare i carichi di lavoro dal monitoraggio delle operazioni ai log delle risorse di monitoraggio di Azure. Per altre informazioni sulla sequenza temporale relativa alla funzionalità deprecata, vedere [Monitorare le soluzioni IoT di Azure con Monitoraggio di Azure e Integrità risorse di Azure](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/).

## <a name="update-iot-hub"></a>Aggiornare l'hub IoT

Per aggiornare l'hub Internet delle cose nel portale di Azure, creare prima di tutto un'impostazione di diagnostica, quindi disattivare il monitoraggio delle operazioni.  

### <a name="create-a--diagnostic-setting"></a>Creare un'impostazione di diagnostica

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'hub IoT.

1. Nel riquadro sinistro, in **monitoraggio**, selezionare **impostazioni di diagnostica**. Quindi selezionare **Aggiungi impostazione di diagnostica**.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Screenshot che evidenzia l'opzione Impostazioni di diagnostica nella sezione Monitoraggio.":::

1. Nel riquadro **impostazioni di diagnostica** assegnare un nome all'impostazione di diagnostica.

1. In **Dettagli categoria** selezionare le categorie per le operazioni che si desidera monitorare. Per altre informazioni sulle categorie di operazioni disponibili con l'hub Internet, vedere [log delle risorse](monitor-iot-hub-reference.md#resource-logs).

1. In **Dettagli destinazione** scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione di queste destinazioni:

   * Archivia in un account di archiviazione
   * Streaming in un hub eventi
   * Inviare ai log di monitoraggio di Azure tramite un'area di lavoro Log Analytics

   Lo screenshot seguente mostra un'impostazione di diagnostica che instrada le operazioni nelle categorie connessioni e telemetria del dispositivo a un'area di lavoro Log Analytics:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Screenshot che mostra un'impostazione di diagnostica completata.":::

1. Selezionare **Salva** per salvare le impostazioni.

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata. Per altre informazioni sulla configurazione della diagnostica, vedere [raccogliere e utilizzare i dati di log dalle risorse di Azure](../azure-monitor/platform/platform-logs-overview.md).

Per informazioni più dettagliate su come creare le impostazioni di diagnostica, tra cui con PowerShell e l'interfaccia della riga di comando di Azure, vedere [impostazioni di diagnostica](../azure-monitor/platform/diagnostic-settings.md) nella documentazione di monitoraggio di Azure.

### <a name="turn-off-operations-monitoring"></a>Disattivare la funzionalità Monitoraggio operazioni

> [!NOTE]
> A partire dall'11 marzo 2019, la funzionalità monitoraggio operazioni viene rimossa dall'interfaccia portale di Azure dell'hub Internet. I passaggi seguenti non sono più applicabili. Per eseguire la migrazione, assicurarsi che le categorie corrette siano indirizzate a una destinazione con un'impostazione di diagnostica di monitoraggio di Azure precedente.

Una volta testate le nuove impostazioni di diagnostica nel flusso di lavoro, è possibile disattivare la funzionalità monitoraggio operazioni. 

1. Nel menu dell'hub IoT selezionare **Monitoraggio operazioni**.

2. In ciascuna categoria di monitoraggio selezionare **Nessuno**.

3. Salvare le modifiche apportate all'impostazione.

## <a name="update-applications-that-use-operations-monitoring"></a>Aggiornare le applicazioni che usano la funzionalità Monitoraggio operazioni

Gli schemi per il monitoraggio delle operazioni e i log delle risorse variano leggermente. È importante aggiornare attualmente le applicazioni che usano il monitoraggio delle operazioni per eseguire il mapping allo schema usato dai log delle risorse.

Inoltre, il log delle risorse dell'hub Internet offre cinque nuove categorie per il rilevamento. Dopo avere aggiornato le applicazioni per lo schema esistente, aggiungere le nuove categorie:

* Operazioni da cloud a dispositivi gemelli
* Operazioni da dispositivo gemello a cloud
* Query dei dispositivi gemelli
* Operazioni dei processi
* Metodi diretti

Per le strutture dello schema specifiche, vedere [log delle risorse](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Monitoraggio degli eventi di connessione e disconnessione di un dispositivo con bassa latenza

Per monitorare gli eventi di connessione e disconnessione del dispositivo nell'ambiente di produzione, è consigliabile sottoscrivere l' [evento **Device disconnected**](iot-hub-event-grid.md#event-types) in griglia di eventi per ottenere gli avvisi e monitorare lo stato di connessione del dispositivo. Per informazioni su come integrare il dispositivo connesso e gli eventi dispositivo connesso dall'hub IoT nella soluzione IoT, usare questa [esercitazione](iot-hub-how-to-order-connection-state-events.md).

## <a name="next-steps"></a>Passaggi successivi

[Monitorare l'hub IoT](monitor-iot-hub.md)
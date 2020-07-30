---
title: Configurare la diagnostica
titleSuffix: Azure Digital Twins
description: Vedere come abilitare la registrazione con le impostazioni di diagnostica.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87379639"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Abilitare la registrazione con le impostazioni di diagnostica

È possibile scegliere di inviare i dati di metrica a [log Analytics](../azure-monitor/log-query/get-started-portal.md), un endpoint di [Hub eventi](../event-hubs/event-hubs-about.md) o [archiviazione di Azure](../storage/blobs/storage-blobs-overview.md) abilitando la registrazione con le impostazioni di diagnostica per l'istanza.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Attivare le impostazioni di diagnostica con la portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **impostazioni di diagnostica** dal menu e quindi **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

3. Nella pagina seguente compilare i valori seguenti:
 * **Nome dell'impostazione di diagnostica**: assegnare un nome alle impostazioni di diagnostica.
 * **Dettagli categoria**: scegliere le operazioni che si desidera monitorare e selezionare le caselle per abilitare la diagnostica per tali operazioni. Le operazioni per cui le impostazioni di diagnostica possono inviare report sono:
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **Dettagli destinazione**: scegliere il percorso in cui si desidera inviare i log. È possibile selezionare qualsiasi combinazione delle tre opzioni:
    - Invia a Log Analytics
    - Archivia in un account di archiviazione
    - Streaming in un hub eventi

    Se necessario per la selezione della destinazione, è possibile che venga richiesto di specificare ulteriori dettagli.  
    
4. Salvare le nuove impostazioni. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata di nuovo nella pagina **impostazioni di diagnostica** per l'istanza. 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla configurazione della diagnostica, vedere [*raccogliere e utilizzare i dati di log dalle risorse di Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [ *procedura: visualizzare le metriche con monitoraggio di Azure*](how-to-view-metrics.md)
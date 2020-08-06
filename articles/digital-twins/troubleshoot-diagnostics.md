---
title: Configurare la diagnostica
titleSuffix: Azure Digital Twins
description: Vedere come abilitare la registrazione con le impostazioni di diagnostica.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809400"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Risoluzione dei problemi relativi ai dispositivi gemelli digitali di Azure: registrazione diagnostica

I dispositivi gemelli digitali di Azure raccolgono le [metriche](troubleshoot-metrics.md) per l'istanza del servizio che forniscono informazioni sullo stato delle risorse. È possibile usare queste metriche per valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse. Queste statistiche per l'utente consentono di vedere cosa accade con i dispositivi gemelli digitali di Azure e consentono di eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Questo articolo illustra come attivare la **registrazione diagnostica** per i dati di metrica dall'istanza di Azure Digital gemelli. È possibile usare questi log per risolvere i problemi relativi al servizio.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Attivare le impostazioni di diagnostica con la portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **impostazioni di diagnostica** dal menu e quindi **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

Le nuove impostazioni diventano effettive in circa 10 minuti. Successivamente, i log vengono visualizzati nella destinazione configurata di nuovo nella pagina **impostazioni di diagnostica** per l'istanza. 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla configurazione della diagnostica, vedere [*raccogliere e utilizzare i dati di log dalle risorse di Azure*](../azure-monitor/platform/platform-logs-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md).

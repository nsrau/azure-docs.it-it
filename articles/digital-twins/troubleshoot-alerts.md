---
title: Impostare gli avvisi
titleSuffix: Azure Digital Twins
description: Vedere come abilitare gli avvisi per le metriche di dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 1b296cd942e36817da2832467ab603ebd833f825
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87909869"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Risoluzione dei problemi di Azure Digital gemelli: avvisi

I dispositivi gemelli digitali di Azure raccolgono le [metriche](troubleshoot-metrics.md) per l'istanza del servizio che forniscono informazioni sullo stato delle risorse. È possibile usare queste metriche per valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse.

Gli **avvisi** inviano notifiche in modo proattivo quando si trovano importanti condizioni nei dati di metrica. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere [*Panoramica degli avvisi in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Attiva avvisi

Di seguito viene illustrato come abilitare gli avvisi per l'istanza di Azure Digital Twins:

1. Accedere al [portale di Azure](https://portal.azure.com) e passare all'istanza di Azure Digital gemelli. È possibile trovarlo digitando il relativo nome nella barra di ricerca del portale. 

2. Selezionare **avvisi** dal menu, quindi **+ nuova regola di avviso**.

    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot che mostra la pagina e il pulsante degli avvisi da aggiungere. Nessun avviso ancora configurato" lightbox="media/troubleshoot-alerts/alerts-pre.png":::

3. Nella pagina *Crea regola di avviso* che segue è possibile seguire le istruzioni per definire le condizioni, le azioni da attivare e i dettagli dell'avviso.     
    * I dettagli dell' **ambito** devono essere riempiti automaticamente con i dettagli dell'istanza.
    * Per personalizzare i trigger e le risposte degli avvisi, sarà necessario definire i dettagli relativi a **condizioni** e **gruppi di azioni** .

    :::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Screenshot che mostra la pagina creare una regola di avviso con le sezioni per ambito, condizione e gruppo di azione" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

Per una procedura dettagliata relativa alla compilazione di questi campi, vedere [*Panoramica degli avvisi in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md). Di seguito sono riportati alcuni esempi di come appariranno i passaggi per i dispositivi gemelli digitali di Azure.

Di seguito è riportato un estratto del processo di *selezione della condizione* che illustra i tipi di segnali di avviso disponibili per i dispositivi gemelli digitali di Azure. In questa pagina è possibile filtrare il tipo di segnale e selezionare il segnale desiderato da un elenco.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Screenshot che mostra la prima pagina Configura logica del segnale. È presente un'evidenziazione nella casella tipo di segnale per selezionare le metriche o i log attività e un elenco di metriche sotto che è possibile selezionare":::

Dopo aver selezionato un segnale, verrà richiesto di configurare la logica dell'avviso. È possibile filtrare in una dimensione, impostare un valore soglia per l'avviso e impostare la frequenza dei controlli per la condizione. Di seguito è riportato un esempio di configurazione di un avviso quando la metrica di frequenza media degli errori di routing supera il 5%.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Screenshot che mostra la seconda pagina Configura logica del segnale. La pagina mostra la cronologia delle metriche, dispone di un'area da filtrare in una dimensione, ad esempio le operazioni di griglia di eventi, e una sezione per la definizione della logica di avviso, ad esempio ' Average ' è maggiore di 5'":::
 
Dopo aver impostato gli avvisi, questi verranno visualizzati nella pagina degli *avvisi* per l'istanza.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Screenshot che mostra la pagina e il pulsante degli avvisi da aggiungere. È stato configurato un avviso" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sugli avvisi con monitoraggio di Azure, vedere [*Panoramica degli avvisi in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).
* Per informazioni sulle metriche dei dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: visualizzare le metriche con monitoraggio di Azure*](troubleshoot-metrics.md).
* Per informazioni su come abilitare la registrazione diagnostica per le metriche, vedere [*risoluzione dei problemi: configurare la diagnostica*](troubleshoot-diagnostics.md).

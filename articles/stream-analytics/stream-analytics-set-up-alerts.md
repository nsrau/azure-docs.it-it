---
title: Configurare avvisi per le query in Analisi di flusso | Microsoft Docs
description: Informazioni sugli avvisi di Analisi di flusso
keywords: configurare avvisi
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/26/2017
ms.author: samacha
ms.openlocfilehash: cdbd7da36f3eb2795fc4548915ebbf18200ca44d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Impostare gli avvisi per i processi di Analisi di flusso di Azure
## <a name="introduction-monitor-page"></a>Introduzione: Pagina di monitoraggio
È possibile configurare avvisi per attivare un avviso quando una metrica raggiunge una condizione specificata dall'utente. Ad esempio, si potrebbe configurare un avviso per una condizione simile alla seguente:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Possono essere configurate regole per le metriche tramite il portale o [a livello di codice](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sui dati dei log delle operazioni.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurare gli avvisi nel portale di Azure
1. Nel portale di Azure aprire il processo di Analisi di flusso per cui si intende creare un avviso. 

2. Nel pannello **Processo** fare clic sulla sezione **Monitoraggio**.  

3. Nel pannello **Metrica** fare clic sul comando **Aggiungi avviso**.

      ![Installazione del portale di Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Immettere un nome e una descrizione.

5. Usare i selettori per definire la condizione in cui verrà inviato l'avviso.

6. Fornire informazioni su dove dovrebbe andare l'avviso.

      ![Configurazione di un avviso per un processo di Analisi di flusso di Azure](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Per altre informazioni dettagliate sulla configurazione degli avvisi nel portale di Azure, vedere [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) (Ricevere notifiche di avviso).  


## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


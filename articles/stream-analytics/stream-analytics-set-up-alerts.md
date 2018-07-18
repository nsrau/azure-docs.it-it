---
title: Impostare gli avvisi di monitoraggio per i processi di Analisi di flusso di Azure
description: Questo articolo descrive come usare il portale di Azure per impostare il monitoraggio e gli avvisi per i processi di Analisi di flusso di Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412111"
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>Esercitazione: Impostare gli avvisi per i processi di Analisi di flusso di Azure
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
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


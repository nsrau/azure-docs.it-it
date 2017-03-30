---
title: Configurare avvisi per le query in Analisi di flusso | Documentazione Microsoft
description: Informazioni sugli avvisi di Analisi di flusso
keywords: configurare avvisi
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c8463e68600edefecd0f3bdd19c13b13f676b82b
ms.lasthandoff: 11/17/2016


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Impostare gli avvisi per i processi di Analisi di flusso di Azure
## <a name="introduction-monitor-page"></a>Introduzione: Pagina di monitoraggio
È possibile configurare avvisi per attivare un avviso quando una metrica raggiunge una condizione specificata dall'utente.

Ad esempio, "se gli eventi di output per gli ultimi 15 minuti sono meno di 100, inviare una notifica tramite e-mail all'ID: xyz@company.com".

Possono essere configurate regole per le metriche tramite il portale o [a livello di codice](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sui dati dei log delle operazioni.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configurare gli avvisi tramite il portale di Azure classico
Esistono due modi per configurare gli avvisi nel portale di Azure classico:  

1. Scheda **Monitoraggio** del processo dell’analisi di flusso  
2. Il Log delle operazioni in servizi di gestione  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configurare avvisi tramite la scheda Monitoraggio del processo nel portale
1. Selezionare la metrica nella scheda di monitoraggio, fare clic sul pulsante **Aggiungi regola** nella parte inferiore del dashboard e configurare le regole desiderate.  
   
   ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. Definire il nome e la descrizione dell'avviso  
   
   ![Creare una regola](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. Immettere soglie, finestra valutazione avviso e azioni per l'avviso  
   
   ![Definire le condizioni](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configurare avvisi tramite i log delle operazioni
1. Visitare la scheda **Avvisi** nei servizi di gestione nel [Portale di Azure classico](https://manage.windowsazure.com).  
2. Fare clic su **Aggiungi regola**  
   
   ![Criteri](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. Definire il nome e la descrizione dell'avviso. Selezionare 'Analisi di flusso' come Tipo di servizio e il nome del processo come Nome del servizio.  
   
   ![Definire l’avviso](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurare gli avvisi nel portale di Azure
Nel portale di Azure individuare il processo di analisi di flusso per cui si vuole essere avvisati e fare clic sulla sezione **Monitoraggio** .  Nel pannello **Metrica** fare clic sul comando **Aggiungi avviso**.

  ![Installazione del portale di Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

È possibile denominare la regola di avviso e scegliere una descrizione che verrà visualizzata nella notifica inviata tramite posta elettronica.

Quando si seleziona Metriche, è possibile scegliere una condizione e un valore soglia per la metrica.

  ![Metrica di selezione del portale di Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Per altre informazioni dettagliate sulla configurazione degli avvisi nel portale di Azure, vedere [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) (Ricevere notifiche di avviso).  

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



---
title: Introduzione al monitoraggio di Azure
description: Introduzione all'uso del monitoraggio di Azure per comprendere appieno il funzionamento delle risorse ed eseguire operazioni in base ai dati.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 70807db256f72b77bb29db3f6f59474a892f2939
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263362"
---
# <a name="get-started-with-azure-monitor"></a>Introduzione al monitoraggio di Azure
Monitoraggio di Azure è il servizio di piattaforma che mette a disposizione un'unica origine per il monitoraggio delle risorse di Azure. Con Monitoraggio di Azure è possibile visualizzare, fare query, indirizzare, archiviare ed effettuare operazioni sulle metriche e sui log provenienti dalle risorse di Azure. È possibile usare questi dati tramite il portale di Azure, i [cmdlet PowerShell di Monitoraggio di Azure](insights-powershell-samples.md), l'[interfaccia della riga di comando multipiattaforma](insights-cli-samples.md) o le [API REST di Monitoraggio di Azure](https://msdn.microsoft.com/library/dn931943.aspx). Questo articolo illustra alcuni componenti principali di Monitoraggio di Azure, usando il portale a scopo dimostrativo.

## <a name="walkthrough"></a>Procedura dettagliata
1. Nel portale passare a **Tutti i servizi** e trovare l'opzione **Monitoraggio**. Fare clic sull'icona a forma di stella per aggiungere l'opzione all'elenco dei preferiti per renderla facilmente accessibile dalla barra di navigazione a sinistra.

    ![Monitor nell'elenco dei servizi](./media/monitoring-get-started/monitor-more-services.png)
2. Fare clic sull'opzione **Monitoraggio** per aprire la pagina **Monitoraggio**, che riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Si apre prima la sezione **Panoramica**. La panoramica mostra un rollup di tutti gli avvisi di monitoraggio, errori e avvisi di integrità dei servizi correlati alle risorse della sottoscrizione.  

    ![Esplorazione della pagina Monitoraggio](./media/monitoring-get-started/monitor-blade-nav.png)

    Monitoraggio di Azure presenta tre categorie di base dei dati di monitoraggio: **log di attività**, **metriche** e **log di diagnostica**.
3. Fare clic su **Log di attività** per assicurarsi di visualizzare la sezione Log di attività.

    Il [**log di attività**](monitoring-overview-activity-logs.md) descrive tutte le operazioni eseguite sulle risorse nella sottoscrizione. L'uso del log attività consente di acquisire informazioni dettagliate su qualsiasi operazione di creazione, aggiornamento o eliminazione eseguita per le risorse della sottoscrizione. Ad esempio, Log di attività indica quando un'app Web è stata arrestata e da chi. Gli eventi del log di attività vengono archiviati nella piattaforma e sono disponibili per le query per 90 giorni.

    ![Log attività](./media/monitoring-get-started/monitor-act-log-blade.png)

    È possibile creare e salvare le query per i filtri comuni, quindi aggiungere le query più importanti a un dashboard del portale, così da sapere sempre se si sono verificati eventi che soddisfano i criteri configurati.
4. Filtrare la visualizzazione su un determinato gruppo di risorse nell'ultima settimana, quindi fare clic sul pulsante **Salva** . Assegnare un nome alla query.

    ![Salvare la query del Log di attività](./media/monitoring-get-started/monitor-act-log-save.png)
5. A questo punto, fare clic sul pulsante **Aggiungi** .

    ![Fare clic su Aggiungi per Log di attività](./media/monitoring-get-started/monitor-act-log-pin.png)

    La maggior parte delle visualizzazioni in questa procedura dettagliata può essere aggiunta a un dashboard. Ciò consente di creare un'unica origine di informazioni sui dati operativi nei propri servizi.
6. Tornare al dashboard. Come è possibile notare, adesso la query (e i risultati) viene visualizzata nel dashboard. Ciò è utile per visualizzare rapidamente le azioni di alto profilo che si sono verificate di recente nella sottoscrizione, ad esempio è stato assegnato un nuovo ruolo o è stata eliminata una macchina virtuale.

    ![Log di attività aggiunti al dashboard](./media/monitoring-get-started/monitor-act-log-db.png)
7. Tornare al riquadro **Monitoraggio** e fare clic sulla sezione **Metriche**. È prima necessario selezionare una risorsa filtrandola con le opzioni disponibili nella parte superiore della pagina.

    ![Filtrare le risorse per le metriche](./media/monitoring-get-started/monitor-met-filter.png)

    Tutte le risorse di Azure generano [**metriche**](monitoring-overview-metrics.md). Questa visualizzazione riunisce tutte le metriche in un unico pannello in modo da comprendere facilmente le prestazioni delle risorse. Inoltre, consultare la nostra nuovissima [esperienza di creazione di grafici per metriche](https://aka.ms/azuremonitor/new-metrics-charts) facendo clic sulla scheda **Metrica (anteprima)**.
8. Dopo aver selezionato una risorsa, tutte le metriche disponibili vengono visualizzate sul lato sinistro della pagina. È possibile creare un grafico di più metriche selezionando le metriche desiderate e modificando il tipo di grafico e l'intervallo di tempo. È inoltre possibile visualizzare tutti gli avvisi relativi alle metriche impostati su questa risorsa.

    ![Blade delle metriche](./media/monitoring-get-started/monitor-metric-blade.png)

   > [!NOTE]
   > Alcune metriche sono disponibili solo abilitando [Application Insights](../application-insights/app-insights-overview.md) e/o l'estensione Diagnostica di Azure per Windows o Linux sulla risorsa.
   >
   >

9. Una volta ottenuto il grafico desiderato, è possibile usare il pulsante **Aggiungi** per aggiungerlo al dashboard.
10. Tornare a **Monitoraggio** e fare clic su **Log di diagnostica**.

    ![Pannello Log di diagnostica](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    I [**log di diagnostica**](monitoring-overview-of-diagnostic-logs.md) sono log generati *da* una risorsa che forniscono i dati sul funzionamento di tale risorsa. Ad esempio, i numeri di regole del gruppo di sicurezza di rete e i log del flusso di lavoro delle app per la logica sono due tipologie di log di diagnostica. Questi log possono essere archiviati in un account di archiviazione, trasmessi a un hub eventi e/o inviati a [Log Analytics](../log-analytics/log-analytics-overview.md). Log Analytics è il prodotto di intelligence operativa di Microsoft per eseguire operazioni avanzate di ricerca e avviso.

    Nel portale è possibile visualizzare e filtrare un elenco di tutte le risorse nella sottoscrizione per scoprire se dispongono di log di diagnostica abilitati.
    > [!NOTE]
    > L'invio delle metriche multidimensionali tramite impostazioni di diagnostica non è attualmente supportato. Le metriche con dimensioni sono esportate come metriche a singola dimensione di tipo flat e aggregate a livello di valori di dimensione.
    >
    > *Ad esempio*: la metrica 'Messaggi in ingresso' su un hub eventi può essere esplorata e rappresentata in un grafico a livello di singola coda. Tuttavia, in caso di esportazione tramite impostazione di diagnostica, la metrica verrà rappresentata come tutti i messaggi in ingresso in tutte le code nell'hub eventi.
    >
    >

11. Fare clic su una risorsa nella pagina Log di diagnostica. Se i log di diagnostica vengono archiviati in un account di archiviazione, l'utente visualizzerà un elenco di log orari scaricabile.

    ![Log di diagnostica per una risorsa](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    È anche possibile fare clic su **Impostazioni di diagnostica** per configurare o modificare le impostazioni dell'archiviazione in un account di archiviazione, lo streaming all'hub eventi o l'invio a un'area di lavoro di Log Analytics.

    ![Abilitare i log di diagnostica](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Se i log di diagnostica sono stati impostati su Log Analytics, allora è possibile cercarli nella sezione **Ricerca Log** del portale.
12. Passare alla sezione **Avvisi (versione classica)** della pagina Monitoraggio.

    ![pannello avvisi pubblici](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Qui è possibile gestire tutti gli [**avvisi classici**](monitoring-overview-alerts.md) delle risorse di Azure, compresi gli avvisi sulle metriche, sugli eventi di log attività, sui test Web di Application Insights (posizioni) e sulla diagnostica proattiva di Application Insights. Gli avvisi si connettono ai gruppi di azioni. I [gruppi di azioni](monitoring-action-groups.md) consentono di inviare notifiche agli utenti o di eseguire specifiche azioni quando viene generato un avviso.

13. Fare clic su **Aggiungi avviso sulla metrica** per creare un avviso.

    ![Aggiungi avviso sulla metrica](./media/monitoring-get-started/monitor-alerts-add.png)

    A questo punto è possibile aggiungere un avviso al dashboard per visualizzarne facilmente lo stato in qualsiasi momento.

    Ora Monitoraggio di Azure ha anche gli [**avvisi più recenti**](https://aka.ms/azuremonitor/near-real-time-alerts) che possono essere valutati a una frequenza minima di un minuto.

14. La sezione Monitoraggio include anche i collegamenti alle applicazioni di [Application Insights](../application-insights/app-insights-overview.md) e alle soluzioni di gestione di [Log Analytics](../log-analytics/log-analytics-overview.md). Questi altri prodotti Microsoft si integrano pienamente con il monitoraggio di Azure.
15. Se Application Insights o Log Analytics non vengono usati, ci sono possibilità che il monitoraggio di Azure funzioni in collaborazione con le soluzioni di monitoraggio, registrazione e avviso attualmente in uso. Consultare la [pagina dedicata ai partner](monitoring-partners.md) per l'elenco completo dei partner e per ricevere le istruzioni di integrazione.

Seguendo questi passaggi e aggiungendo tutti i riquadri pertinenti a un dashboard, è possibile creare delle visualizzazioni complete dell'applicazione e dell'infrastruttura come quella mostrata di seguito:

![Dashboard del monitoraggio di Azure](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Passaggi successivi
* Vedere la [panoramica di tutti gli strumenti di monitoraggio di Azure](monitoring-overview.md) per informazioni su come vengono usati in Monitoraggio di Azure.

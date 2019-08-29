---
title: Monitorare le app per la logica con monitoraggio di Azure-app per la logica di Azure
description: Ottenere informazioni dettagliate e debug dei dati per la risoluzione dei problemi e la diagnosi delle esecuzioni di app per la logica con i log di monitoraggio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 08/29/2019
ms.openlocfilehash: a038a05f03ce7a209ae82203441750749bc6c4c4
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70138740"
---
# <a name="get-insights-and-debugging-data-for-logic-apps-by-using-azure-monitor-logs"></a>Ottenere informazioni dettagliate e i dati di debug per le app per la logica usando i log di monitoraggio di Azure

Per monitorare e ottenere dettagli di debug più completi sulle app per la logica, attivare i [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md) quando si crea l'app per la logica. I log di monitoraggio di Azure forniscono la registrazione e il monitoraggio di diagnostica per le app per la logica quando si installa la soluzione di gestione delle app per la logica nel portale di Azure. Questa soluzione offre anche informazioni aggregate per le esecuzioni dell'app per la logica con dettagli specifici, ad esempio lo stato, il tempo di esecuzione, lo stato di un nuovo invio e gli ID di correlazione. Questo articolo illustra come attivare i log di monitoraggio di Azure in modo da poter visualizzare gli eventi e i dati di runtime per le esecuzioni dell'app per la logica.

Questo argomento illustra come configurare i log di monitoraggio di Azure quando si crea l'app per la logica. Per attivare i log di monitoraggio di Azure per le app per la logica esistenti, seguire questa procedura per [attivare la registrazione diagnostica e inviare i dati di runtime dell'app per la logica ai log di monitoraggio di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Questa pagina descrive in precedenza i passaggi per eseguire queste attività con il Microsoft Operations Management Suite (OMS), che è stato [ritirato nel 2019 gennaio](../azure-monitor/platform/oms-portal-transition.md)e sostituisce i passaggi con i [log di monitoraggio di Azure](../azure-monitor/platform/data-platform-logs.md), che hanno sostituito il termine log Analytics. I dati di log vengono comunque archiviati in un'area di lavoro Log Analytics e vengano ancora raccolti e analizzati dallo stesso servizio Log Analytics. Per altre informazioni, vedere [modifiche](../azure-monitor/terminology.md)della terminologia di monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessaria un'area di lavoro Log Analytics. Informazioni su [come creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

## <a name="turn-on-logging-for-new-logic-apps"></a>Abilitare la registrazione per le nuove app per la logica

1. In [portale di Azure](https://portal.azure.com)creare l'app per la logica. Dal menu principale di Azure selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare una nuova app per la logica](media/logic-apps-monitor-your-logic-apps-oms/create-new-logic-app.png)

1. In **app**per la logica seguire questa procedura:

   1. Assegnare un nome all'app per la logica e selezionare la sottoscrizione di Azure personale.

   1. Creare o selezionare un gruppo di risorse di Azure. Selezionare la località per l'app per la logica.

   1. In **log Analytics**selezionare **on**.

   1. Dall'elenco **log Analytics area di lavoro** selezionare l'area di lavoro a cui si vogliono inviare i dati dalle esecuzioni dell'app per la logica.

      ![Specificare le informazioni sull'app per la logica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app-details.png)

      Al termine di questo passaggio, Azure crea l'app per la logica, che ora è associata all'area di lavoro Log Analytics personale. In questo passaggio viene inoltre installata automaticamente la soluzione di gestione delle app per la logica nell'area di lavoro.

   1. Al termine, selezionare **Crea**.

1. Per visualizzare le esecuzioni dell'app per la logica, [proseguire con la procedura](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Installare la soluzione di gestione delle app per la logica

Se sono già stati configurati i log di monitoraggio di Azure durante la creazione dell'app per la logica, ignorare questo passaggio. In questo caso, infatti, la soluzione di gestione delle app per la logica è già installata.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Nella casella di ricerca individuare "aree di lavoro di log Analytics" e selezionare **aree di lavoro log Analytics**.

   ![Selezionare "aree di lavoro Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. In **log Analytics aree di lavoro**selezionare l'area di lavoro.

   ![Selezionare l'area di lavoro Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. Nel riquadro Panoramica, in Introduzione **a log Analytics** > **configurare le soluzioni di monitoraggio**, selezionare **Visualizza soluzioni**.

   ![Selezionare "Visualizza soluzioni"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. In **Panoramica**selezionare **Aggiungi**.

   ![Selezionare "Aggiungi"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

1. Dopo l'apertura del **Marketplace** , nella casella di ricerca immettere "gestione app per la logica" e selezionare **gestione app**per la logica.

   ![Selezionare "Gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/select-logic-apps-management.png)

1. Nel riquadro Descrizione soluzione selezionare **Crea**.

   ![Selezionare "Crea" per "gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

1. Verificare e confermare l'area di lavoro Log Analytics in cui si vuole installare la soluzione e selezionare di nuovo **Crea** .

   ![Selezionare "Crea" per "gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/confirm-log-analytics-workspace.png)

   Quando Azure distribuisce la soluzione al gruppo di risorse di Azure che contiene l'area di lavoro Log Analytics, la soluzione viene visualizzata nel riquadro di riepilogo dell'area di lavoro.

   ![Riquadro di riepilogo area di lavoro](./media/logic-apps-monitor-your-logic-apps-oms/workspace-summary-pane-logic-apps-management.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Visualizzare informazioni sull'esecuzione dell'app per la logica

Dopo l'esecuzione dell'app per la logica, è possibile visualizzare lo stato e il numero delle esecuzioni nel riquadro **Gestione app per la logica**.

1. Passare all'area di lavoro log Analytics e selezionare **Riepilogo** > area di lavoro**gestione app**per la logica.

   ![Stato e numero di esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary.png)

   In questo riquadro le esecuzioni dell'app per la logica vengono raggruppate in base al nome o allo stato di esecuzione. Questa pagina mostra anche i dettagli sugli errori di azioni o trigger per le esecuzioni delle app per la logica.

   ![Riepilogo dello stato per le esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-runs-summary-details.png)

1. Per visualizzare tutte le esecuzioni per un'app per la logica o uno stato specifico, selezionare la riga relativa all'app per la logica o allo stato desiderato.

   Nell'esempio seguente sono visualizzate tutte le esecuzioni per un'app per la logica specifica:

   ![Visualizzare le esecuzioni e lo stato delle app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Questa pagina include opzioni avanzate: 

   * Colonna **Proprietà** rilevate: Per un'app per la logica in cui si impostano le proprietà rilevate, raggruppate per azioni, è possibile visualizzare tali proprietà da questa colonna. Per visualizzare le proprietà rilevate, selezionare **Visualizza**. Per cercare le proprietà rilevate, usare il filtro di colonna.

      ![Visualizzare le proprietà rilevate per un'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

      Tutte le proprietà rilevate che vengono aggiunte potrebbero richiedere 10-15 minuti per essere visualizzate la prima volta. Informazioni su [come aggiungere proprietà rilevate all'app per la logica](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Invia**di seguito: È possibile inviare nuovamente una o più esecuzioni di app per la logica non riuscite, riuscite o ancora in esecuzione. Selezionare le caselle di controllo per le esecuzioni che si desidera inviare nuovamente, quindi selezionare **Invia**di più.

     ![Inviare di nuovo le esecuzioni delle app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Per filtrare i risultati, è possibile eseguire il filtro lato client e lato server.

   * **Filtro lato client:** Per ogni colonna, selezionare i filtri desiderati, ad esempio:

     ![Esempi di filtri di colonna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro lato server**: Per selezionare un intervallo di tempo specifico o per limitare il numero di esecuzioni visualizzate, usare il controllo ambito nella parte superiore della pagina. Per impostazione predefinita, vengono visualizzati contemporaneamente solo 1.000 record.

     ![Modificare l'intervallo di tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)

1. Per visualizzare tutte le azioni e i relativi dettagli per un'esecuzione specifica, selezionare la riga per l'esecuzione di un'app per la logica.

   Di seguito è riportato un esempio in cui vengono illustrate tutte le azioni e i trigger per un'esecuzione di app per la logica specifica:

   ![Visualizzare le azioni per un'esecuzione dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)

1. In qualsiasi pagina dei risultati, per visualizzare la query dietro i risultati o per visualizzare tutti i risultati, selezionare **Visualizza tutto**per aprire la pagina **log** .

   ![Visualizza tutti i risultati](media/logic-apps-monitor-your-logic-apps-oms/logic-app-see-all.png)

   Nella pagina **logs** è possibile scegliere le opzioni seguenti:

   * Per visualizzare i risultati della query in una tabella, selezionare **tabella**.

   * Le query usano il [linguaggio di query kusto](https://aka.ms/LogAnalyticsLanguageReference), che è possibile modificare se si desidera visualizzare risultati diversi. Per modificare la query, aggiornare la stringa di query e selezionare **Esegui** per visualizzare i risultati nella tabella. 

     ![Log Analytics-visualizzazione query](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
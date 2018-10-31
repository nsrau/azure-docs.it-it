---
title: Monitorare le esecuzioni dell'app per la logica con Azure Log Analytics - App per la logica di Azure | Microsoft Docs
description: Ottenere informazioni e dati di debug dettagliati sulle esecuzioni dell'app per la logica con Log Analytics per la risoluzione dei problemi e la diagnostica
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457140"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorare e ottenere informazioni dettagliate sulle esecuzioni dell'app per la logica con Log Analytics

Per informazioni di monitoraggio e dati di debug più dettagliati, è possibile attivare Log Analytics nel momento in cui si crea un'app per la logica. Log Analytics offre funzionalità di monitoraggio e di registrazione della diagnostica per le esecuzioni dell'app per la logica tramite il portale di Azure. Quando si aggiunge la soluzione di gestione delle app per la logica, si ottengono lo stato aggregato delle esecuzioni dell'app per la logica e dettagli specifici come lo stato, il tempo di esecuzione, lo stato dei nuovi invii e gli ID di correlazione.

Questo articolo illustra come attivare Log Analytics, in modo da poter visualizzare i dati e gli eventi di runtime per l'esecuzione dell'app per la logica.

 > [!TIP]
 > Per monitorare le app per la logica esistenti, seguire questa procedura per [attivare la registrazione della diagnostica e inviare a Log Analytics i dati di runtime dell'app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisiti

Prima di iniziare, è necessario avere un'area di lavoro di Log Analytics. Informazioni su [come creare un'area di lavoro di Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Attivare la registrazione della diagnostica durante la creazione di app per la logica

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica. Scegliere **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare un'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Nella pagina **Crea app per la logica** eseguire le attività descritte di seguito:

   1. Assegnare un nome all'app per la logica e selezionare la sottoscrizione di Azure personale. 

   1. Creare o selezionare un gruppo di risorse di Azure.

   1. Impostare **Log Analytics** su **On**. 

   1. Nell'elenco delle aree di lavoro di Log Analytics selezionare l'area di lavoro in cui si vuole inviare i dati per le esecuzioni dell'app per la logica. 

      ![Creare l'app per la logica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Al termine di questo passaggio, Azure crea l'app per la logica, che ora è associata all'area di lavoro di Log Analytics personale. 
      Con questo passaggio, nell'area di lavoro viene anche installata automaticamente la soluzione di gestione delle app per la logica.

   1. Al termine dell'operazione, scegliere **Crea**.

1. Per visualizzare le esecuzioni dell'app per la logica, [proseguire con la procedura](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Installare la soluzione di gestione delle app per la logica

Se Log Analytics è già stato attivato al momento della creazione dell'app per la logica, ignorare questo passaggio. In questo caso, infatti, la soluzione di gestione delle app per la logica è già installata.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Nella casella di ricerca immettere "log analytics" come filtro e selezionare **Log Analytics**.

   ![Selezionare "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. In **Log Analytics** trovare e selezionare l'area di lavoro di Log Analytics. 

   ![Selezionare l'area di lavoro di Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. In **Configura soluzioni di monitoraggio** scegliere **Visualizza soluzioni**.

   ![Scegliere "Visualizza soluzioni"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Nella pagina Panoramica scegliere **Aggiungi** per aprire l'elenco **Soluzioni di gestione**. Nell'elenco selezionare **Gestione app per la logica**. 

   ![Selezionare "Gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Se non si riesce a trovare la soluzione, nella parte inferiore dell'elenco scegliere **Carica altro** finché non viene visualizzata la soluzione.

1. Scegliere **Crea** per installare la soluzione.

   ![Scegliere "Aggiungi" per "Logic Apps Management" ("Gestione delle app per la logica")](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Visualizzare le esecuzioni dell'app per la logica nell'area di lavoro di Log Analytics

1. Per visualizzare il numero e lo stato delle esecuzioni dell'app per la logica, andare nell'area di lavoro di Log Analytics e aprire la pagina di panoramica. 

   I dettagli sulle esecuzioni dell'app per la logica vengono visualizzati nel riquadro **Gestione app per la logica**. Per visualizzare un riepilogo con dettagli aggiuntivi sulle esecuzioni dell'app per la logica, scegliere il riquadro **Logic Apps Management** (Gestione delle app per la logica). 

   ![Riquadro di panoramica con il numero e lo stato delle esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   In questo riquadro le esecuzioni dell'app per la logica vengono raggruppate in base al nome o allo stato di esecuzione. 
   Questa pagina mostra anche i dettagli sugli errori di azioni o trigger per le esecuzioni delle app per la logica.

   ![Riepilogo dello stato per le esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Per visualizzare tutte le esecuzioni per un'app per la logica o uno stato specifico, selezionare la riga relativa all'app per la logica o allo stato desiderato.

   Nell'esempio seguente sono visualizzate tutte le esecuzioni per un'app per la logica specifica:

   ![Visualizzare le esecuzioni per un'app per la logica o uno stato](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Questa pagina include le opzioni avanzate seguenti:

   * **Proprietà rilevate:**

     Questa colonna mostra le proprietà rilevate per l'app per la logica, raggruppate per azioni. Per visualizzare le proprietà rilevate, scegliere **Visualizza**. 
     Per cercare le proprietà rilevate, usare il filtro di colonna.
   
     ![Visualizzare le proprietà rilevate per un'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Tutte le proprietà rilevate che vengono aggiunte potrebbero richiedere 10-15 minuti per essere visualizzate la prima volta. Informazioni su [come aggiungere proprietà rilevate all'app per la logica](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Invia di nuovo:** è possibile inviare di nuovo una o più esecuzioni di app per la logica che non sono riuscite, che sono riuscite o che sono ancora in esecuzione. Selezionare le caselle di controllo per le esecuzioni che si desidera inviare di nuovo, quindi scegliere **Invia di nuovo**. 

     ![Inviare di nuovo le esecuzioni delle app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Per filtrare questi risultati, è possibile applicare un filtro sia sul lato client sia sul lato server.

   * **Filtro lato client**: per ogni colonna scegliere i filtri desiderati, ad esempio:

     ![Esempi di filtri di colonna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtro lato server**: per scegliere un intervallo di tempo specifico o per limitare il numero di esecuzioni visualizzate, usare il controllo dell'ambito disponibile nella parte superiore della pagina. Per impostazione predefinita, vengono visualizzati contemporaneamente solo 1.000 record.
   
     ![Modificare l'intervallo di tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Per visualizzare tutte le azioni e i relativi dettagli per un'esecuzione specifica, selezionare la riga dell'esecuzione dell'app per la logica desiderata.

   L'esempio seguente mostra tutte le azioni di un'esecuzione specifica di un'app per la logica:

   ![Visualizzare le azioni per un'esecuzione dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. In qualsiasi pagina di risultati, per visualizzare la query che ha generato i risultati o per visualizzare tutti i risultati, scegliere **Mostra tutto**, che apre la pagina Ricerca log.
   
   ![Mostra tutto sulle pagine di risultati](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Nella pagina Ricerca log,

   * Per visualizzare i risultati della query in una tabella, scegliere **Tabella**.

   * Per modificare la query, è possibile modificare la stringa di query nella barra di ricerca. 
   Per un'esperienza migliore, scegliere **Analisi avanzata**.

     ![Visualizzare azioni e i dettagli per un'esecuzione dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Nella pagina di Azure Log Analytics è possibile aggiornare le query e visualizzare i risultati dalla tabella. Questa query usa [il linguaggio di query Kusto](https://aka.ms/LogAnalyticsLanguageReference), che può essere modificato se si preferisce visualizzare risultati diversi. 

     ![Azure Log Analytics - Visualizzazione Query](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
---
title: Monitorare e ottenere informazioni dettagliate sulle esecuzioni dell'app per la logica con OMS - App per la logica di Azure | Microsoft Docs
description: Monitorare le esecuzioni dell'app per la logica con Log Analytics e Operations Management Suite (OMS) per ottenere informazioni e dati di debug dettagliati per la risoluzione dei problemi e la diagnostica
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 91c747a4fbb3c3c3d799741e7d3b5d4646931136
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="monitor-and-get-insights-about-logic-app-runs-with-operations-management-suite-oms-and-log-analytics"></a>Monitorare e ottenere informazioni dettagliate sulle esecuzioni dell'app per la logica con Operations Management Suite (OMS) e Log Analytics

Per informazioni di monitoraggio e dati di debug più dettagliati, è possibile attivare Log Analytics nel momento in cui si crea un'app per la logica. Log Analytics offre funzionalità di monitoraggio e di registrazione della diagnostica per le esecuzioni di app per la logica tramite il portale di Operations Management Suite (OMS). Quando si aggiunge la soluzione di gestione delle app per la logica in OMS, si ottiene non solo lo stato aggregato delle esecuzioni dell'app per la logica, ma anche dettagli specifici come lo stato, il tempo di esecuzione, lo stato dei nuovi invii e gli ID di correlazione.

Questo argomento illustra come attivare Log Analytics o installare la soluzione di gestione delle app per la logica in OMS, in modo da poter visualizzare i dati e gli eventi di runtime per l'esecuzione dell'app per la logica in corso.

 > [!TIP]
 > Per monitorare le app per la logica esistenti, seguire questa procedura per [attivare la registrazione della diagnostica e inviare a OMS i dati di runtime dell'app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Requisiti

Prima di iniziare, è necessario avere un'area di lavoro di OMS. Informazioni su [come creare un'area di lavoro di OMS](../log-analytics/log-analytics-get-started.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Attivare la registrazione della diagnostica durante la creazione di app per la logica

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica. Scegliere **Nuovo** > **Enterprise Integration** > **App per la logica** > **Crea**.

   ![Creare un'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. Nella pagina **Crea app per la logica** eseguire le attività descritte di seguito:

   1. Assegnare un nome all'app per la logica e selezionare la sottoscrizione di Azure personale. 
   2. Creare o selezionare un gruppo di risorse di Azure.
   3. Impostare **Log Analytics** su **On**. 
   Selezionare l'area di lavoro OMS in cui si vuole inviare i dati relativi alle esecuzioni dell'app per la logica. 
   4. Quando si è pronti, scegliere **Aggiungi al dashboard** > **Crea**.

      ![Creare l'app per la logica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Al termine di questo passaggio, Azure crea l'app per la logica, che ora è associata all'area di lavoro OMS personale. 
      Con questo passaggio viene anche installata automaticamente la soluzione di gestione delle app per la logica nell'area di lavoro OMS.

3. Per visualizzare le esecuzioni dell'app per la logica in OMS, [proseguire la procedura](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution-in-oms"></a>Installare la soluzione di gestione delle app per la logica in OMS

Se Log Analytics è già stato attivato al momento della creazione dell'app per la logica, ignorare questo passaggio. In questo caso, infatti, la soluzione di gestione delle app per la logica è già installata in OMS.

1. Nel [portale di Azure](https://portal.azure.com) scegliere **Altri servizi**. Digitare "Log Analytics" come filtro e scegliere **Log Analytics**, come illustrato di seguito:

   ![Scegliere "Log Analytics"](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. In **Log Analytics** trovare e selezionare l'area di lavoro di OMS. 

   ![Selezionare l'area di lavoro di OMS](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. In **Gestione** scegliere **Portale di OMS**.

   ![Scegliere "Portale di OMS"](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. Nella home page di OMS scegliere **Raccolta soluzioni**.

   ![Scegliere "Raccolta soluzioni"](media/logic-apps-monitor-your-logic-apps-oms/solutions-gallery.png)

5. In **Tutte le soluzioni** trovare e scegliere il riquadro relativo a **Logic Apps Management** (Gestione delle app per la logica).

   ![Scegliere "Logic Apps Management" ("Gestione delle app per la logica")](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

6. Per installare la soluzione nell'area di lavoro OMS, scegliere **Aggiungi**.

   ![Scegliere "Aggiungi" per "Logic Apps Management" ("Gestione delle app per la logica")](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-oms-workspace"></a>Visualizzare le esecuzioni dell'app per la logica nell'area di lavoro OMS

1. Per visualizzare il numero e lo stato delle esecuzioni dell'app per la logica, andare alla pagina di panoramica dell'area di lavoro OMS. Verificare i dettagli nel riquadro **Logic Apps Management** (Gestione delle app per la logica).

   ![Riquadro di panoramica con il numero e lo stato delle esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Per visualizzare un riepilogo con dettagli aggiuntivi sulle esecuzioni dell'app per la logica, scegliere il riquadro **Logic Apps Management** (Gestione delle app per la logica).

   In questo riquadro le esecuzioni dell'app per la logica vengono raggruppate in base al nome o allo stato di esecuzione.

   ![Riepilogo dello stato per le esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Per visualizzare tutte le esecuzioni per un'app per la logica o uno stato specifico, selezionare la riga relativa all'app per la logica o allo stato desiderato.

   Nell'esempio seguente sono visualizzate tutte le esecuzioni per un'app per la logica specifica:

   ![Visualizzare le esecuzioni per un'app per la logica o uno stato](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   > [!NOTE]
   > Nella colonna **Nuovo invio** le esecuzioni risultanti da un nuovo invio di un'esecuzione sono contrassegnate con il valore "Sì".

4. Per filtrare questi risultati, è possibile applicare un filtro sia sul lato client sia sul lato server.

   * Filtro lato client: per ogni colonna scegliere i filtri desiderati. 
   Di seguito sono riportati alcuni esempi:

     ![Esempi di filtri di colonna](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtro lato server: per scegliere un intervallo di tempo specifico o per limitare il numero di esecuzioni visualizzate, usare il controllo dell'ambito disponibile nella parte superiore della pagina. 
   Per impostazione predefinita, vengono visualizzati contemporaneamente solo 1.000 record. 
   
     ![Modificare l'intervallo di tempo](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Per visualizzare tutte le azioni e i relativi dettagli per un'esecuzione specifica, selezionare una riga. Verrà visualizzata la pagina Ricerca log. 

   * Per visualizzare queste informazioni in una tabella, scegliere **Tabella**.
   * Per modificare la query, è possibile modificare la stringa di query nella barra di ricerca. 
   Per un'esperienza migliore, scegliere **Analisi avanzata**.

     ![Visualizzare azioni e i dettagli per un'esecuzione dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     Nella pagina di Azure Log Analytics è possibile aggiornare le query e visualizzare i risultati dalla tabella. 
     Questa query usa [il linguaggio di query Kusto](https://docs.loganalytics.io/learn/tutorials/getting_started_with_queries.html), che può essere modificato se si preferisce visualizzare risultati diversi. 

     ![Azure Log Analytics - Visualizzazione Query](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B](../logic-apps/logic-apps-monitor-b2b-message.md)


---
title: Monitorare le App per la logica con log di monitoraggio di Azure - App per la logica di Azure | Microsoft Docs
description: Ottenere informazioni dettagliati e dati di debug per la risoluzione dei problemi e la diagnostica delle esecuzioni di app per la logica con Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106211"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Monitorare le app per la logica con i log di Monitoraggio di Azure

Per monitorare e ottenere i dettagli del debug più completi sulle App per la logica, attivare [monitoraggio di Azure registra](../log-analytics/log-analytics-overview.md) quando crei app per la logica. Log di monitoraggio di Azure offre funzionalità di diagnostica di registrazione e monitoraggio per le App per la logica quando si installa la soluzione di gestione delle App per la logica nel portale di Azure. Questa soluzione offre anche informazioni aggregate per le esecuzioni dell'app per la logica con dettagli specifici, ad esempio lo stato, il tempo di esecuzione, lo stato di un nuovo invio e gli ID di correlazione. Questo articolo viene illustrato come attivare i log di monitoraggio di Azure in modo che è possibile visualizzare gli eventi di runtime e i dati dell'app per la logica viene eseguita.

Per abilitare i log di monitoraggio di Azure per le App per la logica esistenti, seguire questa procedura per [abilitare la registrazione diagnostica e inviare i dati di runtime di app per la logica per i log di monitoraggio di Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Questa pagina offriva in precedenza una descrizione dei passaggi per eseguire queste attività con Microsoft Operations Management Suite (OMS), che verrà [ritirato nel gennaio 2019](../azure-monitor/platform/oms-portal-transition.md), mentre ora riguarda gli stessi passaggi in Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessaria un'area di lavoro Log Analytics. Informazioni su [come creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Attivare la registrazione della diagnostica durante la creazione di app per la logica

1. Nel [portale di Azure](https://portal.azure.com) creare un'app per la logica. Scegliere **Crea una risorsa** > **Integrazione** > **App per la logica**.

   ![Creare un'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. Nella pagina **Crea app per la logica** eseguire le attività descritte di seguito:

   1. Assegnare un nome all'app per la logica e selezionare la sottoscrizione di Azure personale. 

   1. Creare o selezionare un gruppo di risorse di Azure.

   1. Impostare **Log Analytics** su **On**. 

   1. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro in cui si vuole inviare i dati per le esecuzioni dell'app per la logica. 

      ![Creare l'app per la logica](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Al termine di questo passaggio, Azure crea l'app per la logica, che ora è associata all'area di lavoro Log Analytics personale. 
      Con questo passaggio, nell'area di lavoro viene anche installata automaticamente la soluzione di gestione delle app per la logica.

   1. Al termine dell'operazione, scegliere **Crea**.

1. Per visualizzare le esecuzioni dell'app per la logica, [proseguire con la procedura](#view-logic-app-runs-oms).

## <a name="install-logic-apps-management-solution"></a>Installare la soluzione di gestione delle app per la logica

Se è già attivato i log di monitoraggio di Azure al momento della creazione di app per la logica, ignorare questo passaggio. In questo caso, infatti, la soluzione di gestione delle app per la logica è già installata.

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Nella casella di ricerca cercare "log analytics" e selezionare **Log Analytics**.

   ![Selezionare "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. In **Log Analytics** trovare e selezionare l'area di lavoro Log Analytics. 

   ![Selezionare l'area di lavoro Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. In **Introduzione a Log Analytics** > **Configura soluzioni di monitoraggio** scegliere **Visualizza soluzioni**.

   ![Scegliere "Visualizza soluzioni"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Nella pagina Panoramica scegliere **Aggiungi** per aprire l'elenco **Soluzioni di gestione**. Nell'elenco selezionare **Gestione app per la logica**. 

   ![Selezionare "Gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Se non si riesce a trovare la soluzione, nella parte inferiore dell'elenco scegliere **Carica altro** finché non viene visualizzata la soluzione.

1. Scegliere **Crea**, confermare l'area di lavoro Log Analytics in cui si desidera installare la soluzione e quindi scegliere di nuovo **Crea**.   

   ![Scegliere "Crea" per "Gestione app per la logica"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Se non si vuole usare un'area di lavoro esistente, è anche possibile creare una nuova area di lavoro in questo momento.

   Al termine, la soluzione di gestione delle app per la logica viene visualizzata nella pagina Panoramica. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Visualizzare informazioni sull'esecuzione dell'app per la logica

Dopo l'esecuzione dell'app per la logica, è possibile visualizzare lo stato e il numero delle esecuzioni nel riquadro **Gestione app per la logica**. 

1. Passare all'area di lavoro Log Analytics e aprire la pagina Panoramica. Scegliere **Gestione app per la logica**. 

   ![Stato e numero di esecuzioni dell'app per la logica](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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

   * **Filtro lato client:** per ogni colonna scegliere i filtri desiderati, ad esempio:

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
     
     Nella pagina log analitica, è possibile aggiornare le query e visualizzare i risultati dalla tabella. Questa query usa [il linguaggio di query Kusto](https://aka.ms/LogAnalyticsLanguageReference), che può essere modificato se si preferisce visualizzare risultati diversi. 

     ![log analitica - visualizzazione query](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare i messaggi B2B](../logic-apps/logic-apps-monitor-b2b-message.md)
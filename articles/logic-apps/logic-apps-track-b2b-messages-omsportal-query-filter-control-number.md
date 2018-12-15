---
title: Creare query di rilevamento per i messaggi B2B in Log Analytics - App per la logica di Azure | Microsoft Docs
description: Creare query per monitorare i messaggi AS2, X12 ed EDIFACT in Log Analytics di Azure per App per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 5cfab07e19e543b7a46fcce8f449a46395c144d6
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52995326"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-log-analytics-for-azure-logic-apps"></a>Creare query di rilevamento per i messaggi B2B in Log Analytics di Azure per App per la logica di Azure

Per trovare i messaggi AS2, X12 o EDIFACT che si vuole monitorare con [Azure Log Analytics](../log-analytics/log-analytics-overview.md) è possibile creare query per filtrare le azioni in base a criteri specifici. Ad esempio, è possibile trovare i messaggi in base a un numero di controllo interscambio specifico.

> [!NOTE]
> Questa pagina offriva in precedenza una descrizione dei passaggi per eseguire queste attività con Microsoft Operations Management Suite (OMS), che verrà [ritirato nel gennaio 2019](../azure-monitor/platform/oms-portal-transition.md), mentre ora riguarda gli stessi passaggi in Azure Log Analytics. 

## <a name="prerequisites"></a>Prerequisiti

* Un'app per la logica configurata con la registrazione diagnostica. Informazioni su [come creare un'app per la logica](quickstart-create-first-logic-app-workflow.md) e [come configurare la registrazione per tale app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Un account di integrazione configurato con il monitoraggio e la registrazione. Informazioni su [come creare un account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [come configurare il monitoraggio e la registrazione per tale account](../logic-apps/logic-apps-monitor-b2b-message.md).

* Se non è già stato fatto, [pubblicare i dati di diagnostica in Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) e [configurare la verifica dei messaggi in Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Creare query con filtri

Per trovare i messaggi in base a proprietà o valori specifici, è possibile creare query che usano filtri. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Nella casella di ricerca cercare "log analytics" e selezionare **Log Analytics**.

   ![Selezionare Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. In **Log Analytics** trovare e selezionare l'area di lavoro di Log Analytics. 

   ![Selezionare un'area di lavoro di Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Nel menu dell'area di lavoro in **Generale** selezionare **Log (versione classica)** oppure **Log**. 

   Questo esempio mostra come usare la vista Log nella versione classica. 
   Se si sceglie **Visualizza log** nella sezione **Ottimizzare l'esperienza di Log Analytics** in **Eseguire ricerche e analisi nei log** viene visualizzato **Log (versione classica)**. 

   ![Visualizza log classici](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. Nella casella di modifica della query iniziare a digitare il nome del campo da trovare. Quando si inizia a digitare,l'editor di query mostra le possibili corrispondenze e le operazioni che è possibile usare. Dopo aver creato la query scegliere **Esegui** oppure premere il tasto Invio.

   Questo esempio cerca le corrispondenze in **LogicAppB2B**. 
   Altre informazioni su [come trovare i dati in Log Analytics](../log-analytics/log-analytics-log-searches.md).

   ![Iniziare a digitare una stringa di query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Per modificare l'intervallo di tempo che si desidera visualizzare, nel riquadro sinistro selezionare nell'elenco delle durate oppure trascinare il dispositivo di scorrimento. 

   ![Modifica intervallo di tempo](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Per aggiungere un filtro alla query, scegliere **Aggiungi**. 

   ![Aggiungere un filtro alla query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. In **Aggiungi filtri** immettere il nome di filtro che si desidera trovare. Se si trova il filtro, selezionarlo. Nel riquadro sinistro selezionare di nuovo **Aggiungi**.

   Ad esempio, ecco una query diversa che cerca negli eventi **Type=="AzureDiagnostics"** e trova i risultati in base al numero di controllo interscambio selezionando il filtro **event_record_messageProperties_interchangeControlNumber_s**.

   ![Selezionare il valore del filtro](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Dopo aver scelto **Aggiungi**, la query viene aggiornata con l'evento e il valore del filtro selezionati. 
   Vengono ora filtrati anche i risultati precedenti. 

   Ad esempio, la query cerca **Type=="AzureDiagnostics"** e trova i risultati in base al numero controllo interscambio usando il filtro **event_record_messageProperties_interchangeControlNumber_s**.

   ![Risultati filtrati](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Salvare la query

Per salvare la query nella vista **Log (versione classica)**, seguire questa procedura:

1. Dalla query nella pagina **Log (versione classica)**, scegliere **Analisi**. 

   ![Scegliere "Analisi"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. Nella barra degli strumenti della query scegliere **Salva**.

   ![Scegliere "Save" (Salva)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Fornire i dettagli sulla query, ad esempio, assegnare un nome alla query, selezionare **Query**e specificare un nome di categoria. Al termine dell'operazione, scegliere **Salva**.

   ![Scegliere "Save" (Salva)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Per visualizzare le query salvate, tornare alla pagina di query. Nella barra degli strumenti della query scegliere **Ricerche salvate**.

   ![Scegliere" Ricerche salvate"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. In **Ricerche salvate** selezionare la query per visualizzare i risultati. 

   ![Selezionare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Per aggiornare la query e trovare risultati diversi, modificare la query.

## <a name="find-and-run-saved-queries"></a>Trovare ed eseguire le query salvate

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Tutti i servizi**. Nella casella di ricerca cercare "log analytics" e selezionare **Log Analytics**.

   ![Selezionare Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. In **Log Analytics** trovare e selezionare l'area di lavoro di Log Analytics. 

   ![Selezionare un'area di lavoro di Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. Nel menu dell'area di lavoro in **Generale** selezionare **Log (versione classica)** oppure **Log**. 

   Questo esempio mostra come usare la vista Log nella versione classica. 

1. Quando si apre la pagina di query, nella barra degli strumenti della query, scegliere **Ricerche salvate**.

   ![Scegliere" Ricerche salvate"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. In **Ricerche salvate** selezionare la query per visualizzare i risultati. 

   ![Selezionare la query](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   La query viene eseguita automaticamente, ma se non viene eseguita la query per qualsiasi motivo, nell'editor di query scegliere **Esegui**.

## <a name="next-steps"></a>Passaggi successivi

* [Schemi di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schemi di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schemi di rilevamento personalizzati](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)
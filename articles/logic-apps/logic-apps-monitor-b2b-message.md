---
title: Monitorare e configurare la registrazione per le transazioni B2B - App per la logica di Azure | Microsoft Docs
description: Monitorare i messaggi AS2, X12 ed EDIFACT, avviare la registrazione diagnostica per l'account di integrazione
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Monitorare e configurare la registrazione diagnostica per la comunicazione B2B negli account di integrazione

Dopo avere configurato la comunicazione B2B tra due processi o applicazioni aziendali in esecuzione usando l'account di integrazione, tali entità possono scambiarsi messaggi. Per verificare che la comunicazione funziona come previsto, è possibile impostare il monitoraggio per i messaggi AS2, X12 ed EDIFACT, nonché la registrazione diagnostica per l'account di integrazione attraverso il servizio [Log Analytics di Azure](../log-analytics/log-analytics-overview.md). Questo servizio di [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) consente di monitorare il cloud e gli ambienti locali, di gestirne la disponibilità e le prestazioni e di raccogliere dettagli ed eventi di runtime per ottimizzare il debug. È anche possibile [usare i dati diagnostici con altri servizi](#extend-diagnostic-data), ad esempio Archiviazione di Azure e Hub eventi di Azure.

## <a name="requirements"></a>Requisiti

* Un'app per la logica configurata con la registrazione diagnostica. Informazioni su [come configurare la registrazione per l'app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Soddisfatto questo requisito, sarà disponibile un'area di lavoro in [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Quando si configura la registrazione per l'account di integrazione è consigliabile usare la stessa area di lavoro di OMS. Se non si ha un'area di lavoro di OMS, vedere [come crearne una](../log-analytics/log-analytics-get-started.md).

* Un account di integrazione collegato all'app per la logica. Informazioni su [come creare un account di integrazione con un collegamento all'app per la logica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Attivare la registrazione diagnostica per l'account di integrazione

È possibile attivare la registrazione direttamente dall'account di integrazione o [usando il servizio Monitoraggio di Azure](#azure-monitor-service). Monitoraggio di Azure offre il monitoraggio di base con dati a livello di infrastruttura. Altre informazioni su [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Attivare la registrazione diagnostica direttamente dall'account di integrazione

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. In **Monitoraggio** scegliere **Log di diagnostica** come illustrato di seguito:

   ![Trovare e selezionare l'account di integrazione, quindi scegliere "Log di diagnostica"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Dopo aver selezionato l'account di integrazione, i valori riportati di seguito vengono selezionati automaticamente. Se questi valori sono corretti, scegliere **Abilita diagnostica**. In caso contrario, selezionare valori specifici:

   1. In **Sottoscrizione** selezionare la sottoscrizione di Azure da usare con l'account di integrazione.
   2. In **Gruppo di risorse** selezionare il gruppo di risorse da usare con l'account di integrazione.
   3. In **Tipo di risorsa** selezionare **Account di integrazione**. 
   4. In **Risorsa** selezionare il proprio account di integrazione. 
   5. Scegliere **Abilita diagnostica**.

   ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. In **Impostazioni di diagnostica** scegliere **Sì** sotto **Stato**.

   ![Attivare la diagnostica di Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Selezionare ora l'area di lavoro di OMS e i dati da usare per la registrazione, come indicato:

   1. Selezionare **Invia a Log Analytics**. 
   2. In **Log Analytics** scegliere **Configura**. 
   3. In **Aree di lavoro OMS** selezionare l'area di lavoro di OMS da usare per la registrazione.
   4. In **Log** selezionare la categoria **IntegrationAccountTrackingEvents**.
   5. Scegliere **Salva**.

   ![Impostare Log Analytics in modo da inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. [Impostare la registrazione per i messaggi B2B in OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Attivare la registrazione diagnostica con Monitoraggio di Azure

1. Nel menu principale di Azure del [portale di Azure](https://portal.azure.com) scegliere **Monitoraggio**, **Log di diagnostica**. Selezionare quindi il proprio account di integrazione come indicato di seguito:

   ![Scegliere "Monitoraggio", "Log di diagnostica", selezionare l'account di integrazione](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Dopo aver selezionato l'account di integrazione, i valori riportati di seguito vengono selezionati automaticamente. Se questi valori sono corretti, scegliere **Abilita diagnostica**. In caso contrario, selezionare valori specifici:

   1. In **Sottoscrizione** selezionare la sottoscrizione di Azure da usare con l'account di integrazione.
   2. In **Gruppo di risorse** selezionare il gruppo di risorse da usare con l'account di integrazione.
   3. In **Tipo di risorsa** selezionare **Account di integrazione**.
   4. In **Risorsa** selezionare il proprio account di integrazione.
   5. Scegliere **Abilita diagnostica**.

   ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. In **Impostazioni di diagnostica** scegliere **Sì**.

   ![Attivare la diagnostica di Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Selezionare ora l'area di lavoro di OMS e la categoria di eventi per la registrazione, come indicato:

   1. Selezionare **Invia a Log Analytics**. 
   2. In **Log Analytics** scegliere **Configura**. 
   3. In **Aree di lavoro OMS** selezionare l'area di lavoro di OMS da usare per la registrazione.
   4. In **Log** selezionare la categoria **IntegrationAccountTrackingEvents**.
   5. Al termine dell'operazione, scegliere **Salva**.

   ![Impostare Log Analytics in modo da inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. [Impostare la registrazione per i messaggi B2B in OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Estendere l'uso dei dati di diagnostica con altri servizi

Con Azure Log Analytics, è possibile usare in modo diverso i dati di diagnostica dell'app per la logica con altri servizi di Azure, ad esempio: 

* [Archiviare i log di diagnostica di Azure in Archiviazione di Microsoft Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Trasmettere i log di diagnostica di Azure a Hub eventi di Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

È quindi possibile eseguire il monitoraggio in tempo reale usando i dati di telemetria e l'analisi da altri servizi, ad esempio [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../log-analytics/log-analytics-powerbi.md), ad esempio:

* [Trasmettere i dati da Hub eventi ad Analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizzare i dati di streaming con Analisi di flusso e creare un dashboard di analisi in tempo reale in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

In base alle opzioni che si vuole configurare, assicurarsi prima di tutto di [creare un account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) o di [creare un hub eventi di Azure](../event-hubs/event-hubs-create.md). Selezionare quindi le opzioni per la posizione a cui si vogliono inviare i dati di diagnostica:

![Inviare i dati all'hub di eventi o all'account di archiviazione di Azure](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> I periodi di conservazione si applicano solo quando si sceglie di usare un account di archiviazione.

## <a name="supported-tracking-schemas"></a>Schemi di rilevamento supportati

Azure supporta questi tipi di schemi di rilevamento, che sono tutti fissi ad eccezione del tipo personalizzato.

* [Schema di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schema di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schema di rilevamento personalizzato](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Tenere traccia dei messaggi B2B in OMS](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Tenere traccia dei messaggi B2B in OMS")
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni su Enterprise Integration Pack")


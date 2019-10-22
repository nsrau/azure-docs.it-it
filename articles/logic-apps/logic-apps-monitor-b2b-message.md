---
title: Monitorare i messaggi B2B con monitoraggio di Azure-app per la logica di Azure
description: Configurare la registrazione diagnostica per i messaggi AS2, X12 ed EDIFACT in app per la logica di Azure tramite monitoraggio di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: e5c8e5f3d42f4e85406fcc7dd5a2f6602045c8ed
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680195"
---
# <a name="set-up-diagnostics-logging-for-b2b-messages-in-azure-logic-apps-by-using-azure-monitor"></a>Configurare la registrazione diagnostica per i messaggi B2B in app per la logica di Azure tramite monitoraggio di Azure

Dopo aver configurato la comunicazione B2B tra partner commerciali nell'account di integrazione, i partner possono scambiare messaggi tra di loro. Per verificare che questa comunicazione funzioni come previsto, è possibile monitorare i messaggi AS2, X12 ed EDIFACT e configurare la registrazione diagnostica per l'account di integrazione con i [log di monitoraggio di Azure](../log-analytics/log-analytics-overview.md). Questo servizio consente di monitorare il cloud e gli ambienti locali, di gestirne la disponibilità e le prestazioni e di raccogliere dettagli ed eventi di runtime per ottimizzare il debug. È anche possibile usare questi dati con altri servizi, come ad esempio Archiviazione di Azure e Hub eventi di Azure.

> [!NOTE]
> Questa pagina potrebbe contenere ancora riferimenti a Microsoft Operations Management Suite (OMS), che verrà [ritirato a gennaio 2019](../azure-monitor/platform/oms-portal-transition.md), ma sostituisce quei passaggi con Azure Log Analytics laddove possibile. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

* Un'app per la logica configurata con la registrazione diagnostica. Informazioni su [come creare un'app per la logica](quickstart-create-first-logic-app-workflow.md) e [come configurare la registrazione per tale app per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Una volta soddisfatti i requisiti precedenti, è necessaria anche un'area di lavoro Log Analytics, che consente di monitorare e tenere traccia delle comunicazioni B2B tramite i log di monitoraggio di Azure. Se non si dispone di un'area di lavoro Log Analytics, vedere [Creare un'area di lavoro Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Un account di integrazione collegato all'app per la logica. Informazioni su [come creare un account di integrazione con un collegamento all'app per la logica](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Attivare la registrazione diagnostica

È possibile attivare la registrazione direttamente dall'account di integrazione o [usando il servizio Monitoraggio di Azure](#azure-monitor-service). Monitoraggio di Azure offre il monitoraggio di base con dati a livello di infrastruttura. Altre informazioni su [Monitoraggio di Azure](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Attivare la registrazione dall'account di integrazione

1. Nel [portale di Azure](https://portal.azure.com) trovare e selezionare l'account di integrazione. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.

   ![Trovare e selezionare l'account di integrazione, selezionare “Impostazioni diagnostica”](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Ora trovare e selezionare l'account di integrazione. Negli elenchi di filtri, selezionare i valori che si applicano all'account di integrazione.
Al termine, scegliere **Aggiungi l'impostazione di diagnostica**.

   | Proprietà | Value | Description | 
   |----------|-------|-------------|
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | La sottoscrizione di Azure associata all'account di integrazione | 
   | **Gruppo di risorse** | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure per l'account di integrazione | 
   | **Tipo di risorsa** | **Account di integrazione** | Il tipo per la risorsa di Azure in cui si desidera attivare la registrazione | 
   | **Risorsa** | <*integration-account-name*> | Il nome per la risorsa di Azure in cui si desidera attivare la registrazione | 
   ||||  

   ad esempio:

   ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Specificare un nome per la nuova impostazione di diagnostica e selezionare l'area di lavoro Log Analytics e i dati che si desiderano registrare.

   1. Selezionare **Invia a Log Analytics**. 

   1. In **Log Analytics** selezionare **Configura**. 

   1. In **Aree di lavoro OMS** selezionare l'area di lavoro Log Analytics che si desidera usare per la registrazione. 

      > [!NOTE]
      > Le aree di lavoro OMS vengono sostituite da aree di lavoro di Log Analytics. 

   1. In **Log** selezionare la categoria **IntegrationAccountTrackingEvents** e scegliere **Salva**.

   ad esempio: 

   ![Configurare i log di monitoraggio di Azure in modo da poter inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Configurare ora il rilevamento per i messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Attivare la registrazione tramite Monitoraggio di Azure

1. Selezionare [Monitoraggio](https://portal.azure.com) dal menu principale di Azure nel **portale di Azure**. Selezionare **Impostazioni di diagnostica** in **Impostazioni**. 

   ![Selezionare “Monitoraggio” > “Impostazioni di diagnostica” > account di integrazione](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Ora trovare e selezionare l'account di integrazione. Negli elenchi di filtri, selezionare i valori che si applicano all'account di integrazione.
Al termine, scegliere **Aggiungi l'impostazione di diagnostica**.

   | Proprietà | Value | Description | 
   |----------|-------|-------------|
   | **Sottoscrizione** | <*nome sottoscrizione di Azure*> | La sottoscrizione di Azure associata all'account di integrazione | 
   | **Gruppo di risorse** | <*Azure-resource-group-name*> | Il gruppo di risorse di Azure per l'account di integrazione | 
   | **Tipo di risorsa** | **Account di integrazione** | Il tipo per la risorsa di Azure in cui si desidera attivare la registrazione | 
   | **Risorsa** | <*integration-account-name*> | Il nome per la risorsa di Azure in cui si desidera attivare la registrazione | 
   ||||  

   ad esempio:

   ![Configurare la diagnostica per l'account di integrazione](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Specificare un nome per la nuova impostazione di diagnostica e selezionare l'area di lavoro Log Analytics e i dati che si desiderano registrare.

   1. Selezionare **Invia a Log Analytics**. 

   1. In **Log Analytics** selezionare **Configura**. 

   1. In **Aree di lavoro OMS** selezionare l'area di lavoro Log Analytics che si desidera usare per la registrazione. 

      > [!NOTE]
      > Le aree di lavoro OMS vengono sostituite da aree di lavoro di Log Analytics. 

   1. In **Log** selezionare la categoria **IntegrationAccountTrackingEvents** e scegliere **Salva**.

   ad esempio: 

   ![Configurare i log di monitoraggio di Azure in modo da poter inviare i dati di diagnostica a un log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. [Configurare ora il rilevamento per i messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Usare i dati di diagnostica con altri servizi

Insieme ai log di monitoraggio di Azure, è possibile estendere il modo in cui si usano i dati di diagnostica dell'app per la logica con altri servizi di Azure, ad esempio: 

* [Archiviare i log di diagnostica di Azure in Archiviazione di Microsoft Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Trasmettere i log di diagnostica di Azure a Hub eventi di Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

È quindi possibile eseguire il monitoraggio in tempo reale usando i dati di telemetria e l'analisi da altri servizi, ad esempio [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) e [Power BI](../azure-monitor/platform/powerbi.md), ad esempio:

* [Trasmettere i dati da Hub eventi ad Analisi di flusso](../stream-analytics/stream-analytics-define-inputs.md)
* [Analizzare i dati di streaming con Analisi di flusso e creare un dashboard di analisi in tempo reale in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

In base alle opzioni che si vuole configurare, assicurarsi prima di tutto di [creare un account di archiviazione di Azure](../storage/common/storage-create-storage-account.md) o di [creare un hub eventi di Azure](../event-hubs/event-hubs-create.md). È quindi possibile selezionare le destinazioni a cui si vogliono inviare i dati di diagnostica.
I periodi di conservazione si applicano solo quando si sceglie di usare un account di archiviazione.

![Inviare i dati all'hub di eventi o all'account di archiviazione di Azure](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Schemi di rilevamento supportati

Azure supporta questi tipi di schemi di rilevamento, che sono tutti fissi ad eccezione del tipo personalizzato.

* [Schema di rilevamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Schema di rilevamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Schema di rilevamento personalizzato](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Passaggi successivi

* [Tenere traccia dei messaggi B2B nei log di monitoraggio di Azure](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Tenere traccia dei messaggi B2B nei log di monitoraggio di Azure")
* [Altre informazioni su Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informazioni sulle Enterprise Integration Pack")


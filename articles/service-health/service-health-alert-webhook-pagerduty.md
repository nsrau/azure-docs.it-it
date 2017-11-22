---
title: "Configurare gli avvisi di integrità dei servizi di Azure con PagerDuty | Microsoft Docs"
description: "Ricevere notifiche personalizzate sugli eventi di integrità del servizio nell'istanza di PagerDuty."
author: shawntabrizi
manager: scotthit
editor: 
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 9edcb727b9f0af348cacd5533523c4f2e8214703
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Configurare gli avvisi di integrità dei servizi con PagerDuty

Questo articolo illustra come configurare le notifiche di integrità dei servizi di Azure tramite PagerDuty usando un webhook. Con il tipo di integrazione personalizzata Microsoft Azure di [PagerDuty](https://www.pagerduty.com/) è possibile aggiungere facilmente gli avvisi di integrità dei servizi ai servizi nuovi o esistenti di PagerDuty.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Creazione di un URL di integrazione dell'integrità dei servizi in PagerDuty
1.  Assicurarsi di avere eseguito la registrazione e l'accesso all'account a [PagerDuty](https://www.pagerduty.com/).

2.  Passare alla sezione **Services** (Servizi) in PagerDuty.

    ![La sezione relativa ai servizi in PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Selezionare **Add New Service** (Aggiungi nuovo servizio) o aprire un servizio esistente che è stato configurato.

4.  In **Integration Settings** (Impostazioni di integrazione) selezionare le opzioni seguenti:

    a. **Integration Type** (Tipo di integrazione): Microsoft Azure

    b. **Integration Name** (Nome integrazione): \<Nome\>

    ![Le impostazioni di integrazione in PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Compilare tutti gli altri campi necessari e selezionare **Add** (Aggiungi).

6.  Aprire questa nuova integrazione e copiare e salvare l'**URL di integrazione**.

    ![L'URL di integrazione in PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Creare un avviso usando PagerDuty nel portale di Azure
### <a name="for-a-new-action-group"></a>Per un nuovo gruppo di azioni:
1. Seguire i passaggi da 1 a 8 in [Creare un avviso per una notifica sull'integrità dei servizi per un nuovo gruppo di azioni usando il portale di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definire l'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli: l'** **URL di integrazione** di PagerDuty salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

3. Al termine fare clic su **Salva** per creare l'avviso.

### <a name="for-an-existing-action-group"></a>Per un gruppo di azioni esistente:
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Monitoraggio**.

2. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

3. Individuare e selezionare il gruppo di azioni da modificare.

4. Aggiungere all'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli: l'** **URL di integrazione** di PagerDuty salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

5. Al termine fare clic su **Salva** per aggiornare il gruppo di azioni.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. È possibile trovare un payload di esempio del webhook di integrità del servizio in [Webhook per gli avvisi del log attività di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Verrà visualizzato un codice `202 Accepted` con un messaggio contenente l'"ID evento".

4. Passare a [PagerDuty](https://www.pagerduty.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Altre informazioni sui [gruppi di azione](../monitoring-and-diagnostics/monitoring-action-groups.md).
---
title: "Configurare gli avvisi di integrità dei servizi di Azure con OpsGenie | Microsoft Docs"
description: "Ricevere notifiche personalizzate sugli eventi di integrità del servizio nell'istanza di OpsGenie."
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
ms.openlocfilehash: a2309a050225dd0d7ac8d5b3e4c762bc5bcb25c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Configurare gli avvisi di integrità dei servizi con OpsGenie

Questo articolo illustra come configurare gli avvisi di integrità dei servizi di Azure con OpsGenie usando un webhook. Usando l'integrazione dell'integrità dei servizi di Azure di [OpsGenie](https://www.opsgenie.com/) è possibile inoltrare gli avvisi sull'integrità dei servizi di Azure a OpsGenie. OpsGenie può determinare i destinatari delle notifiche in base alla pianificazione della reperibilità usando posta elettronica, SMS, chiamate telefoniche, notifiche push di iOS e Android ed eseguendo l'escalation degli avvisi finché non vengono confermati o chiusi.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Creazione di un URL di integrazione dell'integrità dei servizi in OpsGenie
1.  Assicurarsi di avere eseguito la registrazione e l'accesso all'account di [OpsGenie](https://www.opsgenie.com/).

2.  Passare alla sezione **Integrations** (Integrazioni) di OpsGenie.

    ![Sezione "Integrations" (Integrazioni) di OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Selezionare il pulsante di integrazione **Azure Service Health**.

    ![Pulsante "Azure Service Health" in OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  Nel campo **Name** (Nome) assegnare un nome all'avviso e specificare un valore nel campo **Assigned to Team** (Team assegnatario).

5.  Compilare gli altri campi come **Recipients** (Destinatari), **Enabled** (Abilitato) e **Suppress Notifications** (Elimina notifiche).

6.  Copiare e salvare l'**URL di integrazione**, che conterrà già il valore di `apiKey` aggiunto alla fine.

    ![URL di integrazione in OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Selezionare **Save Integration** (Salva integrazione)

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Creare un avviso usando OpsGenie nel portale di Azure
### <a name="for-a-new-action-group"></a>Per un nuovo gruppo di azioni:
1. Seguire i passaggi da 1 a 8 in [Creare un avviso per una notifica sull'integrità dei servizi per un nuovo gruppo di azioni usando il portale di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Definire l'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l'**URL di integrazione** di OpsGenie salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

3. Al termine fare clic su **Salva** per creare l'avviso.

### <a name="for-an-existing-action-group"></a>Per un gruppo di azioni esistente:
1. Nel [portale di Azure](https://portal.azure.com/) selezionare **Monitoraggio**.

2. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

3. Individuare e selezionare il gruppo di azioni da modificare.

4. Aggiungere all'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l'**URL di integrazione** di OpsGenie salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

5. Al termine fare clic su **Salva** per aggiornare il gruppo di azioni.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. È possibile trovare un payload di esempio del webhook di integrità del servizio in [Webhook per gli avvisi del log attività di Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Verrà visualizzata una risposta `200 OK` con un messaggio che indica lo stato di operazione riuscita.

4. Passare a [OpsGenie](https://www.opsgenie.com/) per verificare che l'integrazione sia stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Altre informazioni sui [gruppi di azione](../monitoring-and-diagnostics/monitoring-action-groups.md).
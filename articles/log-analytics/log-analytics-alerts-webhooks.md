---
title: Esempio di azione di allarme webhook in Log Analytics di OMS | Documentazione Microsoft
description: "Una delle azioni che è possibile eseguire in risposta a un avviso di Log Analytics è la creazione di un *webhook*, che consente di richiamare un processo esterno tramite una singola richiesta HTTP. Questo articolo illustra un esempio di creazione di un'azione webhook in un avviso di Log Analytics tramite Slack."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>Creare un'azione di avviso webhook in Log Analytics di OMS per inviare messaggi a Slack
Una delle azioni che è possibile eseguire in risposta a un [avviso di Log Analytics](log-analytics-alerts.md) è la creazione di un *webhook*, che consente di richiamare un processo esterno tramite una singola richiesta HTTP.  Per informazioni dettagliate su avvisi e webhook, vedere [Avvisi in Log Analytics](log-analytics-alerts.md)

Questo articolo illustra un esempio di creazione di un'azione webhook in un avviso di Log Analytics tramite il servizio di messaggistica Slack.

> [!NOTE]
> Per completare l'esempio è necessario disporre di un account Slack.  È possibile iscriversi per ottenere un account gratuito nel sito Web [slack.com](http://slack.com).
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>Passaggio 1: Abilitare gli webhook in Slack
1. Accedere al sito Web Slack all'indirizzo [slack.com](http://slack.com).
2. Selezionare un canale nella sezione **Channels** (Canali) del riquadro a sinistra,  che costituirà il canale a cui verrà inviato il messaggio.  È possibile selezionare uno dei canali predefiniti, ad esempio **general** o **random**.  In uno scenario di produzione sarà probabilmente necessario creare un canale speciale, ad esempio **criticalservicealerts**. <br>
   
   ![Canali Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. Fare clic su **Add an app or custom integration** (Aggiungi un'app o l'integrazione personalizzata) per aprire la directory dell'app.
4. Digitare *webhooks* nella casella di ricerca e quindi selezionare **Incoming WebHooks**(Webhook in ingresso). <br>
   
   ![Canali Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. Fare clic su **Install** (Installa) vicino al nome del proprio team.
6. Fare clic su **Add Configuration**(Aggiungi configurazione).
7. Selezionare il canale che si intende usare in questo esempio e quindi fare clic su **Add Incoming WebHooks integration**(Aggiungi integrazione webhook in ingresso).  
8. Copiare l'indirizzo contenuto in **Webhook URL** (URL webhook),  che dovrà essere incollato nella configurazione dell'avviso. <br>
   
    ![Canali Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Passaggio 2: Creare una regola di avviso in Log Analytics
1. [Creare una regola di avviso](log-analytics-alerts.md) con le impostazioni seguenti.
   * Query: ```    Type=Event EventLevelName=error ```
   * Check for this alert every: 5 minutes (Verifica la presenza di questo avviso ogni: 5 minuti)
   * The number of results is: greater than 10 (Il numero di risultati è: maggiore di 10)
   * Over this time window: 60 minutes (Finestra temporale: 60 minuti)
   * Selezionare **Yes** (Sì) per **Webhook** e **No** per le altre azioni.
2. Incollare l'URL del sito Slack nel campo **Webhook URL** (URL webhook).
3. Selezionare l'opzione che consente di **includere un payload JSON personalizzato**.
4. Slack prevede un payload in formato JSON con un parametro denominato *text*.  che costituisce il testo visualizzato nel messaggio che verrà creato.  Usando il simbolo *#* è possibile includere più di un parametro di avviso.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![payload JSON di esempio](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. Fare clic su **Save** (Salva) per salvare la regola di avviso.
6. Attendere il tempo necessario alla creazione dell'avviso. In Slack apparirà un messaggio simile al seguente.
   
   ![webhook di esempio in Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Payload di un webhook avanzato per Slack
Con Slack è possibile personalizzare in maniera significativa i messaggi in ingresso. Per altre informazioni, vedere la sezione [Incoming Webhooks](https://api.slack.com/incoming-webhooks) (Webhook in ingresso) del sito Web Slack. Di seguito è riportato un payload complesso che consente la creazione di un messaggio avanzato con formattazione:

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


In Slack viene visualizzato un messaggio simile al seguente.

![messaggio di esempio in Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Riepilogo
Con l'attivazione di questa regola, ogni volta che vengono soddisfatti determinati criteri viene inviato un messaggio a Slack.  

Questo è solo un esempio delle varie azioni che è possibile creare in risposta a un avviso.  È possibile ad esempio creare un'azione webhook per chiamare un altro servizio esterno, un'azione runbook per avviare un runbook in Automazione di Azure o un'azione di posta elettronica per inviare un messaggio di posta a se stessi o ad altri destinatari.   

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [azioni di avviso in Log Analytics](log-analytics-alerts-actions.md) e su altre azioni.



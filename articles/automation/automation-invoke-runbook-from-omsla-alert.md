---
title: Chiamata di un runbook di Automazione di Azure da un avviso di Log Analytics | Documentazione Microsoft
description: Questo articolo fornisce una panoramica della procedura per richiamare un runbook di Automazione da un avviso di Microsoft OMS Log Analytics.
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 81cf490eae7f283c0180875cb3a2ed2ffe6333c8
ms.lasthandoff: 03/29/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Chiamata di un runbook di un Automazione di Azure da un avviso di OMS Log Analytics

Quando viene configurato un avviso in Log Analytics per creare un record di avviso in caso di corrispondenza dei risultati con un criterio specifico, ad esempio un picco prolungato nell'uso del processore o un errore di un processo specifico dell'applicazione essenziale per il funzionamento di un'applicazione aziendale e la conseguente scrittura dell'evento corrispondente nel registro eventi di Windows, tale avviso può eseguire automaticamente un runbook di Automazione per provare a risolvere automaticamente il problema.  

Sono disponibili due opzioni per chiamare un runbook durante la configurazione di un avviso,  ovvero:

1. Uso di un webhook.
   * Questa è l'unica opzione disponibile se l'area di lavoro di OMS non è collegata a un account di Automazione.
   * Se si ha già un account di Automazione collegato a un'area di lavoro di OMS, questa opzione è disponibile.  

2. Selezione diretta di un runbook.
   * Questa opzione è disponibile solo quando l'area di lavoro di OMS è collegata a un account di Automazione.  

## <a name="calling-a-runbook-using-a-webhook"></a>Chiamata di un runbook mediante un webhook

Un webhook consente di avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP.  Prima di configurare l'[avviso di Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) per chiamare il runbook usando un webhook come azione di avviso, sarà necessario creare un webhook per il runbook che verrà chiamato usando questo metodo.  Esaminare e seguire la procedura illustrata nell'articolo [Creare un webhook](automation-webhooks.md#creating-a-webhook) e annotare l'URL del webhook, in modo che sia possibile farvi riferimento durante la configurazione della regola di avviso.   

## <a name="calling-a-runbook-directly"></a>Chiamata diretta di un runbook

Se nell'area di lavoro di OMS è stata installata e configurata l'offerta di automazione e controllo, durante la configurazione dell'opzione relativa alle azioni del runbook per l'avviso è possibile visualizzare tutti i runbook dall'elenco a discesa **Selezionare un runbook**, quindi selezionare il runbook specifico da eseguire in risposta all'avviso.  Il runbook selezionato può essere eseguito in un'area di lavoro nel cloud di Azure o in un ruolo di lavoro ibrido per runbook.  Quando l'avviso viene creato usando l'opzione relativa al runbook, verrà creato un webhook per il runbook.  Per visualizzare il webhook, passare all'account di Automazione e quindi al pannello del webhook del runbook selezionato.  Se si elimina l'avviso, il webhook non viene eliminato, ma l'utente può eliminarlo manualmente.  Se la mancata eliminazione di un webhook non costituisce un problema, il webhook è semplicemente un elemento orfano che dovrà essere eliminato per un'organizzazione ottimale dell'account di Automazione.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Caratteristiche di un runbook (per entrambe le opzioni)

Entrambi i metodi per chiamare il runbook dall'avviso di Log Analytics presentano caratteristiche comportamentali diverse, che devono essere comprese prima di configurare le regole dell'avviso.  

* È necessario avere un parametro di input del runbook denominato **WebhookData**, di tipo **Object**.  Questo parametro può essere obbligatorio o facoltativo.  L'avviso passa i risultati della ricerca al runbook usando il parametro di input.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  È necessario avere codice per la conversione di WebhookData in un oggetto di PowerShell.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    *$SearchResults* sarà una matrice di oggetti. Ogni oggetto contiene i campi con i valori di un risultato della ricerca.

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>Incoerenze di WebhookData tra l'opzione webhook e l'opzione runbook

* Durante la configurazione di un avviso per chiamare un webhook, immettere un URL di webhook creato per un runbook, quindi fare clic sul pulsante **Test del webhook**.  L'oggetto WebhookData risultante inviato al runbook non contiene *.SearchResult* o *.SearchResults*.

*  Se si salva l'avviso, quando l'avviso viene attivato e chiama il webhook, l'oggetto WebhookData inviato al runbook contiene *.SearchResult*.
* Se si crea un avviso e lo si configura per chiamare un runbook, operazione che a sua volta crea un webhook, all'attivazione dell'avviso viene inviato un oggetto WebhookData al runbook che contiene *.SearchResults*.

Nell'esempio di codice precedente è quindi necessario ottenere *.SearchResult* se l'avviso chiama un webhook e ottenere *.SearchResults* se l'avviso chiama direttamente un runbook.

## <a name="example-walkthrough"></a>Procedura dettagliata di esempio

Il funzionamento di questa procedura verrà illustrato tramite il runbook grafico di esempio seguente, che avvia un servizio di Windows.<br><br> ![Avviare il runbook grafico per il servizio di Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Il runbook ha un parametro di input di tipo **Object** denominato **WebhookData** e include i dati del webhook passati dall'avviso che contiene *.SearchResults*.<br><br> ![Parametri di input dei runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Per questo esempio, in Log Analytics sono stati creati due campi personalizzati, *SvcDisplayName_CF* e *SvcState_CF*, per estrarre il nome visualizzato del servizio e lo stato del servizio, ad esempio in esecuzione o arrestato, dall'evento scritto nel registro eventi di sistema.  Viene quindi creata una regola di avviso con la query di ricerca seguente `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`, in modo che sia possibile rilevare l'arresto del servizio Spooler di stampa nel sistema Windows.  Può trattarsi di un servizio qualsiasi, ma per questo esempio si fa riferimento a uno dei servizi preesistenti inclusi nel sistema operativo Windows.  L'azione di avviso viene configurata per eseguire il runbook usato in questo esempio e per l'esecuzione nel ruolo di lavoro ibrido per runbook, abilitato nei sistemi di destinazione.   

L'attività di codice del runbook **Get Service Name from LA** convertirà la stringa con formattazione JSON in un tipo Object e applicherà un filtro all'elemento *SvcDisplayName_CF* per estrarre il nome visualizzato del servizio Windows e passarlo all'attività successiva, che verificherà se il servizio è stato arrestato prima di provare a riavviarlo.  *SvcDisplayName_CF* è un [campo personalizzato](../log-analytics/log-analytics-custom-fields.md) creato in Log Analytics per illustrare questo esempio.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

Quando il servizio viene arrestato, la regola di avviso in Log Analytics rileverà una corrispondenza, attiverà il runbook e invierà il contesto dell'avviso al runbook. Il runbook verificherà se il servizio è stato arrestato e, in tale caso, proverà a riavviarlo, quindi ne verificherà il riavvio corretto e restituirà i risultati.     

In alternativa, se l'account di Automazione è collegato all'area di lavoro di OMS, è possibile configurare la regola di avviso con un'azione di webhook per attivare il runbook, configurarlo per convertire la stringa in formato JSON e applicare il filtro a *.SearchResult* in base alle indicazioni precedenti.    

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli avvisi in Log Analytics e su come crearne uno, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).

* Per informazioni su come attivare i runbook usando un webhook, vedere [Webhook di Automazione di Azure](automation-webhooks.md).


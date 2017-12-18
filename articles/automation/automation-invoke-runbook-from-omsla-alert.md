---
title: Chiamata di un runbook di Automazione di Azure da un avviso di Log Analytics | Documentazione Microsoft
description: Questo articolo fornisce una panoramica della procedura per richiamare un runbook di Automazione da un avviso di Microsoft OMS Log Analytics.
services: automation
documentationcenter: 
author: georgewallace
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
ms.openlocfilehash: 0c0b15f33a177afc70a3662c5bd008eb236ed0d6
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Chiamata di un runbook di un Automazione di Azure da un avviso di OMS Log Analytics

Se un avviso è configurato in Log Analytics per creare un record di avviso quando i risultati corrispondono a criteri specifici, ad esempio un picco prolungato nell'utilizzo del processore o un errore di un processo dell'applicazione cruciale per il funzionamento di un'applicazione aziendale e la conseguente scrittura dell'evento corrispondente nel registro eventi di Windows, tale avviso può eseguire automaticamente un runbook di Automazione per provare a risolvere automaticamente il problema.  

Sono disponibili due opzioni per chiamare un runbook durante la configurazione di un avviso:

1. Uso di un webhook.
   * Questa è l'unica opzione disponibile se l'area di lavoro OMS non è collegata a un account di Automazione.
   * Se si ha già un account di Automazione collegato a un'area di lavoro di OMS, questa opzione è disponibile.  

2. Selezione diretta di un runbook.
   * Questa opzione è disponibile solo se l'area di lavoro di OMS è collegata a un account di Automazione.

## <a name="calling-a-runbook-using-a-webhook"></a>Chiamata di un runbook mediante un webhook

Un webhook consente di avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. Prima di configurare l'[avviso di Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) per chiamare il runbook usando un webhook come azione di avviso, è necessario creare prima di tutto un webhook per il runbook che deve essere chiamato usando questo metodo. Eseguire i passaggi indicati nell'articolo [Creare un webhook](automation-webhooks.md#creating-a-webhook) e annotare l'URL del webhook, in modo che sia possibile farvi riferimento durante la configurazione della regola di avviso.   

## <a name="calling-a-runbook-directly"></a>Chiamata diretta di un runbook

Se nell'area di lavoro OMS è stata installata e configurata l'offerta Automazione e controllo, durante la configurazione dell'opzione relativa alle azioni del runbook per l'avviso è possibile visualizzare tutti i runbook nell'elenco a discesa **Seleziona un runbook** e quindi selezionare il runbook specifico da eseguire in risposta all'avviso. Il runbook selezionato può essere eseguito in un'area di lavoro nel cloud di Azure o in un ruolo di lavoro ibrido per runbook. Quando l'avviso viene creato usando l'opzione relativa al runbook, viene creato un webhook per il runbook. Per visualizzare il webhook, passare all'account di Automazione e quindi al pannello del webhook per il runbook selezionato. Se si elimina l'avviso, il webhook non viene eliminato, ma l'utente può eliminarlo manualmente. Se la mancata eliminazione di un webhook non è un problema, il webhook è semplicemente un elemento orfano che dovrà essere eliminato per un'organizzazione ottimale dell'account di Automazione.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>Caratteristiche di un runbook (per entrambe le opzioni)

Entrambi i metodi per chiamare il runbook dall'avviso di Log Analytics hanno caratteristiche che è necessario comprendere prima di configurare le regole di avviso. I dati dell'avviso sono disponibili in formato JSON in una singola proprietà denominata **SearchResult**. Questo formato è per le azioni di runbook e webhook con un payload standard. Per le azioni webhook con payload personalizzati che includono **IncludeSearchResults:True** nell'elemento **RequestBody**, la proprietà è **SearchResults**.

* È necessario avere un parametro di input del runbook denominato **WebhookData**, di tipo **Object**. Questo parametro può essere obbligatorio o facoltativo. L'avviso passa i risultati della ricerca al runbook usando il parametro di input.

    ```powershell
    param  
        (  
        [Parameter (Mandatory=$true)]  
        [object] $WebhookData  
        )
    ```
*  È necessario avere codice per la conversione di WebhookData in un oggetto di PowerShell.

    ```powershell
    $SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
    ```

    *$SearchResult* è una matrice di oggetti, in cui ogni oggetto contiene i campi con i valori di un risultato della ricerca


## <a name="example-walkthrough"></a>Procedura dettagliata di esempio

Il funzionamento di questo processo viene illustrato tramite il runbook grafico di esempio seguente, che avvia un servizio di Windows.<br><br> ![Avviare il runbook grafico per il servizio di Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

Il runbook ha un parametro di input di tipo **Object** denominato **WebhookData** e include i dati del webhook passati dall'avviso che contiene \*.SearchResult\*.<br><br> ![Parametri di input dei runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Per questo esempio, in Log Analytics sono stati creati due campi personalizzati, \*SvcDisplayName\_CF\* e \*SvcState\_CF\*, per estrarre il nome visualizzato del servizio e lo stato del servizio, ad esempio in esecuzione o arrestato, dall'evento scritto nel log eventi di sistema. Viene quindi creata una regola di avviso con la query di ricerca seguente `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"`, in modo che sia possibile rilevare l'arresto del servizio Spooler di stampa nel sistema Windows. Può trattarsi di un servizio qualsiasi, ma per questo esempio si fa riferimento a uno dei servizi preesistenti inclusi nel sistema operativo Windows. L'azione di avviso viene configurata per eseguire il runbook usato in questo esempio e per l'esecuzione nel ruolo di lavoro ibrido per runbook, abilitato nel sistema di destinazione.   

L'attività di codice del runbook **Get Service Name from LA** converte la stringa in formato JSON in un tipo di oggetto e applica un filtro all'elemento \*SvcDisplayName\_CF\* per estrarre il nome visualizzato del servizio di Windows e passare il valore all'attività successiva, che verifica se il servizio è stato arrestato prima di provare a riavviarlo. *SvcDisplayName_CF* è un [campo personalizzato](../log-analytics/log-analytics-custom-fields.md) creato in Log Analytics per illustrare questo esempio.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Quando il servizio viene arrestato, la regola di avviso in Log Analytics rileva una corrispondenza, attiva il runbook e invia il contesto dell'avviso al runbook. Il runbook interviene per verificare se il servizio è stato arrestato e, in questo caso, prova a riavviarlo, quindi ne verifica il riavvio corretto e ne restituisce i risultati.     

In alternativa, se l'account di Automazione è collegato all'area di lavoro di OMS, è possibile configurare la regola di avviso con un'azione di webhook per attivare il runbook, configurarlo per convertire la stringa in formato JSON e applicare il filtro a \*.SearchResult\* in base alle indicazioni precedenti.    

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), il payload del webhook è stato modificato.  Vedere [API REST di Azure Log Analytics](https://aka.ms/loganalyticsapiresponse) per informazioni dettagliate sul formato.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli avvisi in Log Analytics e su come crearne uno, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).

* Per informazioni su come attivare i runbook usando un webhook, vedere [Webhook di Automazione di Azure](automation-webhooks.md).

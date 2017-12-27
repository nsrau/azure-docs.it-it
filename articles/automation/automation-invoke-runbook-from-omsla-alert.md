---
title: Chiamare un runbook di Automazione di Azure da un avviso di Log Analytics | Microsoft Docs
description: Questo articolo offre una panoramica della procedura per richiamare un runbook di Automazione da un avviso di Log Analytics in Operations Management Suite.
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
ms.openlocfilehash: 76d5ab23ef1962733ccb3b36640facdba9ab8acb
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="call-an-azure-automation-runbook-from-a-log-analytics-alert"></a>Chiamare un runbook di Automazione di Azure da un avviso di Log Analytics

È possibile configurare un avviso in Azure Log Analytics per creare un record di avviso quando i risultati corrispondono ai criteri. L'avviso può quindi eseguire automaticamente un runbook di Automazione di Azure per provare a risolvere automaticamente il problema. 

Un avviso potrebbe ad esempio indicare un picco prolungato nell'utilizzo del processore o un errore in un processo dell'applicazione fondamentale per il funzionamento di un'applicazione aziendale. Un runbook può quindi scrivere un evento corrispondente nel registro eventi di Windows.  

Per chiamare un runbook nella configurazione di un avviso sono disponibili due opzioni:

* Usare un webhook.
   * Questa è l'unica opzione disponibile se l'area di lavoro di Operations Management Suite non è collegata a un account di Automazione.
   * Se si ha già un account di Automazione collegato a un'area di lavoro di Operations Management Suite, questa opzione è comunque disponibile.  

* Selezione diretta di un runbook.
   * Questa opzione è disponibile solo se l'area di lavoro di Operations Management Suite è collegata a un account di Automazione.

## <a name="calling-a-runbook-by-using-a-webhook"></a>Chiamata di un runbook con un webhook

È possibile usare un webhook per avviare un runbook specifico in Automazione di Azure tramite una singola richiesta HTTP. Prima di configurare l'[avviso di Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules) per chiamare il runbook usando un webhook come azione di avviso, è necessario [creare un webhook](automation-webhooks.md#creating-a-webhook) per il runbook chiamato con questo metodo. Ricordarsi di annotare l'URL del webhook per potervi fare riferimento durante la configurazione della regola di avviso.   

## <a name="calling-a-runbook-directly"></a>Chiamata diretta di un runbook

È possibile installare e configurare l'offerta Automation & Control nell'area di lavoro di Operations Management Suite. Durante la configurazione dell'opzione relativa alle azioni del runbook per l'avviso è possibile visualizzare tutti i runbook nell'elenco a discesa **Seleziona un runbook** e quindi selezionare il runbook specifico da eseguire in risposta all'avviso. Il runbook selezionato può essere eseguito in un'area di lavoro di Azure o in un ruolo di lavoro ibrido per runbook. 

Dopo la creazione dell'avviso con l'opzione relativa al runbook, viene creato un webhook per il runbook. Per visualizzare il webhook, passare all'account di Automazione e aprire il riquadro del webhook per il runbook selezionato. 

Il webhook non viene eliminato in caso di eliminazione dell'avviso, ma ciò non costituisce un problema. Il webhook diventa semplicemente un elemento orfano che dovrà essere eliminato manualmente per mantenere organizzato l'account di Automazione.  

## <a name="characteristics-of-a-runbook"></a>Caratteristiche di un runbook

Entrambi i metodi per chiamare il runbook dall'avviso di Log Analytics hanno caratteristiche che è necessario comprendere prima di configurare le regole di avviso. 

I dati dell'avviso sono contenuti in formato JSON in una singola proprietà denominata **SearchResult**. Questo formato è per le azioni di runbook e webhook con un payload standard. Per le azioni webhook con payload personalizzati, che includono **IncludeSearchResults:True** in **RequestBody**, la proprietà è **SearchResults**.

È necessario avere un parametro di input del runbook denominato **WebhookData** di tipo **Object**. Questo parametro può essere obbligatorio o facoltativo. L'avviso passa i risultati della ricerca al runbook usando questo parametro di input.

```powershell
param  
    (  
    [Parameter (Mandatory=$true)]  
    [object] $WebhookData  
    )
```
È necessario includere anche il codice per convertire **WebhookData** in un oggetto di PowerShell.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
```

**$SearchResult** è una matrice di oggetti, ognuno dei quali contiene i campi con i valori di un risultato della ricerca.


## <a name="example-walkthrough"></a>Procedura dettagliata di esempio

L'esempio seguente di un runbook grafico illustra il funzionamento del processo. Il runbook avvia un servizio di Windows.

![Runbook grafico per l'avvio di un servizio di Windows](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)

Il runbook ha un parametro di input di tipo **Object** denominato **WebhookData**, che include i dati del webhook passati dall'avviso contenente **SearchResult**.

![Parametri di input dei runbook](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)

Per questo esempio, in Log Analytics sono stati creati due campi personalizzati: **SvcDisplayName_CF** e **SvcState_CF**. In questi campi vengono estratti il nome visualizzato e lo stato del servizio (che può essere in esecuzione o arrestato) dall'evento scritto nel registro eventi di sistema. Viene quindi creata una regola di avviso con la query di ricerca seguente, per poter rilevare l'arresto del servizio Spooler di stampa nel sistema Windows:

`Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` 

Può trattarsi di qualsiasi servizio a cui si è interessati. Per questo esempio si fa riferimento a uno dei servizi preesistenti inclusi nel sistema operativo Windows. L'azione di avviso viene configurata per eseguire il runbook usato in questo esempio nel ruolo di lavoro ibrido per runbook, abilitato nel sistema di destinazione.   

L'attività di codice del runbook **Get Service Name from LA** converte la stringa in formato JSON in un tipo Object e applica un filtro per l'elemento **SvcDisplayName_CF**. Estrae quindi il nome visualizzato del servizio di Windows e passa questo valore all'attività successiva, che verifica che il servizio sia stato arrestato prima di tentarne il riavvio. **SvcDisplayName_CF** è un [campo personalizzato](../log-analytics/log-analytics-custom-fields.md) che è stato creato in Log Analytics per illustrare questo esempio.

```powershell
$SearchResult = (ConvertFrom-Json $WebhookData.RequestBody).SearchResult.value
$SearchResult.SvcDisplayName_CF  
```

Quando il servizio viene arrestato, la regola di avviso in Log Analytics rileva una corrispondenza, attiva il runbook e invia il contesto dell'avviso al runbook. Il runbook prova a verificare che il servizio sia stato arrestato. In questo caso, tenta di riavviare il servizio, verificare che sia stato avviato correttamente e visualizzare i risultati.     

In alternativa, se l'account di Automazione non è collegato all'area di lavoro di Operations Management Suite, è possibile configurare la regola di avviso con un'azione webhook. L'azione webhook attiva il runbook e lo configura anche per la conversione della stringa in formato JSON e l'applicazione di un filtro per **SearchResult** in base alle indicazioni precedenti.    

>[!NOTE]
> Se l'area di lavoro è stata aggiornata al [nuovo linguaggio di query di Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md), il payload del webhook è stato modificato. Per informazioni dettagliate sul formato, vedere la documentazione relativa all'[API REST di Azure Log Analytics](https://aka.ms/loganalyticsapiresponse).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sugli avvisi in Log Analytics e su come crearne uno, vedere [Avvisi in Log Analytics](../log-analytics/log-analytics-alerts.md).

* Per informazioni su come attivare i runbook usando un webhook, vedere l'articolo relativo ai [webhook di automazione di Azure](automation-webhooks.md).

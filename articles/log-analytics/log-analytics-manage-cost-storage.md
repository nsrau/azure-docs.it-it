---
title: Gestire il costo dei dati in Azure Log Analytics | Microsoft Docs
description: Informazioni su come modificare il piano tariffario e gestire il volume e i criteri di conservazione dei dati per l'area di lavoro di Log Analytics in Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 179d5c71a309fd79c23519544d1da6bcc07cda0c
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045001"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Gestire i costi controllando i volumi e la conservazione di dati in Log Analytics

> [!NOTE]
> Questo articolo descrive come controllare i costi in Log Analytics impostando il periodo di conservazione dei dati.  Per le informazioni correlate, vedere gli articoli seguenti.
> - [Analizzare l'utilizzo dei dati in Log Analytics](log-analytics-manage-cost-storage.md) descrive l'analisi e gli avvisi per l'utilizzo dei dati.
> - [Monitoraggio dell'utilizzo e dei costi stimati](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per diversi modelli di determinazione dei prezzi. Illustra inoltre come modificare il modello di prezzi.

Log Analytics è un servizio progettato per offrire scalabilità e supportare la raccolta, l'indicizzazione e l'archiviazione di grandi quantità di dati al giorno da qualsiasi origine aziendale o distribuita in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tale scopo, è importante comprendere che il costo di un'area di lavoro di Log Analytics non si basa solo sul volume dei dati raccolti, ma dipende anche dal piano selezionato e dal periodo di tempo di archiviazione dei dati generati dalle origini connesse.  

In questo articolo viene esaminato come monitorare in modo proattivo il volume dei dati e l'aumento dello spazio di archiviazione e definire i limiti per controllare i costi associati. 

Il costo dei dati può essere notevole, a seconda dei fattori seguenti: 

- Numero di sistemi, componenti dell'infrastruttura, risorse cloud e così via, da cui avviene la raccolta 
- Tipo di dati creati dall'origine, ad esempio code di messaggi, log, eventi, dati sulla sicurezza o metriche sulle prestazioni 
- Volume di dati generati da tali origini e inseriti nell'area di lavoro 
- Periodo di conservazione dei dati nell'area di lavoro  
- Numero di soluzioni di gestione abilitate, origine dati e frequenza della raccolta 

> [!NOTE]
> Consultare la documentazione per ogni soluzione per avere una stima della quantità di dati raccolti.   

I clienti con un contratto Enterprise sottoscritto prima del 1 luglio 2018 o che hanno già creato un'area di lavoro di Log Analytics in una sottoscrizione potranno comunque accedere al piano *Gratuito*. Se la sottoscrizione non è associata a una registrazione EA esistente, il livello *Gratuito* non è disponibile quando si crea un'area di lavoro in una nuova sottoscrizione dopo il 2 aprile 2018.  I dati possono essere conservati per un massimo di 7 giorni per il livello *Gratuito*.  Per il livello *Autonomo* o *A pagamento*, i dati raccolti sono disponibili per 31 giorni. Il livello *Gratuito* prevede un limite di inserimento giornaliero di 500 MB. Se si supera costantemente il volume consentito, è possibile convertire l'area di lavoro in un piano a pagamento per raccogliere dati oltre questo limite. 

> [!NOTE]
> Se si sceglie di selezionare un periodo di conservazione più lungo per il livello a pagamento, vengono addebitati dei costi. È possibile modificare il tipo di piano in qualsiasi momento. Per altre informazioni sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/log-analytics/). 

Ci sono due modi per limitare il volume dei dati e controllare i costi, ovvero l'impostazione di un limite giornaliero e della conservazione dati.  

## <a name="review-estimated-cost"></a>Esaminare il costo stimato
Log Analytics aiuta a capire quali sono i costi probabili in base ai modelli di utilizzo recenti.  A questo scopo, eseguire i passaggi seguenti.  

1. Accedere al [portale di Azure](http://portal.azure.com). 
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.<br><br> ![portale di Azure](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. Nel riquadro delle sottoscrizioni di Log Analytics selezionare l'area di lavoro e quindi fare clic su **Utilizzo e costi stimati** nel riquadro a sinistra.<br><br> ![Pagina Utilizzo e costi stimati](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Da qui è possibile esaminare il volume di dati per il mese. Sono inclusi tutti i dati ricevuti e conservati nell'area di lavoro di Log Analytics.  Fare clic su **Dettagli di utilizzo** nella parte superiore della pagina per visualizzare il dashboard di utilizzo con informazioni sulle tendenze del volume di dati in base a origine, computer e offerta. Per visualizzare e impostare un limite giornaliero o per modificare il periodo di conservazione, fare clic su **Gestione del volume dati**.
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite giornaliero
Quando si crea un'area di lavoro di Log Analytics dal portale di Azure e si sceglie il piano *Gratuito*, viene impostato un limite di 500 MB al giorno. Gli altri piani tariffari non prevedono limiti. È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero è finalizzato a gestire l'aumento imprevisto nel volume di dati dalle risorse gestite e rimanere entro il limite prefissato oppure quando si vuole semplicemente limitare gli addebiti non pianificati per l'area di lavoro.  

Quando viene raggiunto il limite giornaliero, la raccolta di tipi di dati fatturabili viene arrestata per il resto del giorno. Nella parte superiore della pagina per l'area di lavoro di Log Analytics selezionata viene visualizzato un banner di avviso e viene inviato un evento di tipo operazione alla tabella *Operazione* nella categoria **LogManagement**. La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento operazione, per l'invio di una notifica quando viene raggiunta la soglia dei dati giornaliera. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire 
Vedere [Utilizzo e costi stimati di Log Analytics](log-analytics-usage.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gestire il volume di dati giornaliero massimo 
Di seguito viene descritto come configurare un limite per gestire il volume di dati che verranno inseriti da Log Analytics al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** per l'area di lavoro selezionata fare clic su **Gestione del volume dati** nella parte superiore. 
5. Per impostazione predefinita, il limite giornaliero è impostato su **DISATTIVA**. Fare clic su **ATTIVA** per abilitarlo e quindi impostare il volume di dati in GB/giorno.<br><br> ![Configurazione della soglia dei dati in Log Analytics](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Inviare un avviso al raggiungimento del limite
Anche se nel portale di Azure viene visualizzato un segnale visivo quando viene raggiunta la soglia dei dati, questo comportamento potrebbe non soddisfare le esigenze aziendali per la gestione di problemi operativi che richiedono attenzione immediata.  Per ricevere una notifica di avviso, è possibile creare una nuova regola di avviso in Monitoraggio di Azure.  Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Per iniziare, ecco le impostazioni consigliate per l'avviso:

* Destinazione: Selezionare la risorsa di Log Analytics
* Criteri: 
   * Nome segnale: Ricerca log personalizzata
   * Query di ricerca: operazione, seguita da | con valore "OverQuota" per Detail
   * In base a: Numero di risultati
   * Condizione: Maggiore di
   * Soglia: 0
   * Periodo: 5 (minuti)
   * Frequenza: 5 (minuti)
* Nome regola di avviso: Soglia dei dati giornaliera raggiunta
* Gravità: Avviso (Gravità 1)

Una volta definito l'avviso e raggiunto il limite, viene attivato un avviso e viene eseguita la risposta definita nel gruppo di azioni. È possibile informare il team tramite posta elettronica e SMS oppure automatizzare le operazioni usando webhook o runbook di Automazione oppure tramite l'[integrazione con una soluzione di Gestione dei servizi IT esterna](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati 
La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro.
 
1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Gestione del volume dati** nella parte superiore.
5. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.<br><br> ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>risoluzione dei problemi
**Domanda**: Cosa è possibile fare se Log Analytics smette di raccogliere dati? 
**Risposta**: Se si sta usando il piano tariffario Gratuito e sono stati inviati più di 500 MB di dati in un giorno, la raccolta dati viene interrotta per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  
Log Analytics crea un evento di tipo operazione quando la raccolta dati si avvia e si arresta.  
Eseguire la query seguente per verificare se si sta raggiungendo il limite giornaliero e si stanno perdendo dati: operazione, seguita da | con OperationCategory == 'Data Collection Status'   
Quando la raccolta dati si interrompe, OperationStatus è Avviso. Quando la raccolta dati si avvia, OperationStatus è Riuscito.  
La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:  

|Motivo dell'arresto della raccolta| Soluzione| 
|-----------------------|---------|
|Limite giornaliero di dati gratuiti raggiunto<sup>1</sup>|Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure passare a un piano tariffario a pagamento.|
|Limite giornaliero definito nella gestione dei volumi di dati raggiunto|Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure aumentare il limite giornaliero per il volume di dati, come descritto in [Gestire il volume di dati giornaliero massimo](#manage-the-maximum-daily-volume)|
|La sottoscrizione di Azure è in sospeso perché:<br> la versione di prova gratuita è terminata<br> Azure Pass è scaduto<br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio|Passare a una sottoscrizione a pagamento<br> Rimuovere il limite oppure attendere fino ripristino del limite|

<sup>1</sup> Se l'area di lavoro ha un piano tariffario gratuito, è possibile inviare solo 500 MB di dati al giorno al servizio. Quando si raggiunge il limite giornaliero, si interrompe la raccolta dei dati fino al giorno successivo. I dati inviati quando la raccolta dati è sospesa non vengono indicizzati e non sono disponibili per la ricerca. Quando la raccolta dati riprende, l'elaborazione avviene solo per i nuovi dati inviati. 

Log Analytics usa l'ora UTC. L'ora di ripristino varia tra le aree di lavoro, per impedire che tutte le aree di lavoro per cui è impostato un limite inizino a inserire i dati nello stesso momento. Se l'area di lavoro raggiunge il limite giornaliero, l'elaborazione riprende dopo l'ora di ripristino definita in **Daily limit will be set at** (Ora impostazione limite giornaliero).<br><br> ![Fuso orario UTC per l'impostazione del limite di Log Analytics](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Domanda**: Come è possibile ricevere una notifica per l'arresto della raccolta dati? 
**Risposta**: Usare la procedura per la *creazione di un avviso relativo al limite di dati giornaliero* per ricevere una notifica quando la raccolta dati si arresta e seguire i passaggi per aggiungere azioni alle regole di avviso per configurare un messaggio di posta elettronica, un webhook o un runbook per la regola di avviso. 

## <a name="next-steps"></a>Passaggi successivi  

Per determinare la quantità di dati raccolti, le origini che inviano i dati e i diversi tipi di dati inviati per gestire il consumo e i costi, vedere [Analizzare l'utilizzo dei dati in Log Analytics](log-analytics-usage.md).
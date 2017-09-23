---
title: Creazione di avvisi in Log Analytics di OMS | Documentazione Microsoft
description: Gli avvisi in Log Analytics identificano le informazioni importanti nel repository OMS e possono notificare i problemi all'utente in modo proattivo o richiamare le azioni per tentare di correggerle.  Questo articolo descrive come creare una regola di avviso e include i dettagli relativi alle diverse azioni che possono attivare.
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: c34fb7295e8f386f0e7cf2c1db6b26a3e49eae98
ms.contentlocale: it-it
ms.lasthandoff: 05/25/2017

---
# <a name="working-with-alert-rules-in-log-analytics"></a>Utilizzo delle regole di avviso in Log Analytics
Gli avvisi vengono creati da regole di avviso che eseguono automaticamente ricerche log a intervalli regolari.  Viene quindi creato un record di avviso se i risultati corrispondono a particolari criteri.  La regola può quindi eseguire automaticamente una o più azioni per notificare l'avviso all'utente in modo proattivo o richiamare un altro processo.   

Questo articolo descrive come creare e modificare le regole di avviso tramite il portale di OMS.  Per informazioni dettagliate sulle diverse impostazioni e su come implementare la logica necessaria, vedere [Informazioni sugli avvisi in Log Analytics](log-analytics-alerts.md).

>[!NOTE]
> Al momento non è possibile creare o modificare una regola di avviso tramite il portale di Azure. 

## <a name="create-an-alert-rule"></a>Creare una regola di avviso

Per creare una regola di avviso usando il portale di OMS, è necessario creare prima di tutto una ricerca log per i record che dovranno richiamare l'avviso.  Il pulsante **Avviso** diventerà disponibile per poter creare e configurare la regola di avviso.

>[!NOTE]
> Attualmente, è possibile creare un massimo di 250 regole di avviso in un'area di lavoro OMS. 

1. Dalla pagina di panoramica di OMS fare clic su **Ricerca log**.
2. Creare una nuova query di ricerca nei log o selezionarne una salvata. 
3. Fare clic su **Avviso** nella parte superiore della pagina per aprire la schermata **Aggiungi regola di avviso**.
4. Configurare la regola di avviso con le informazioni della sezione [Dettagli delle regole di avviso](#details-of-alert-rules) seguente.
6. Fare clic su **Salva** per salvare la regola di avviso.  Verrà avviata immediatamente l'esecuzione.


## <a name="edit-an-alert-rule"></a>Modificare una regola di avviso
È possibile ottenere un elenco di tutte le regole di avviso nel menu **Avvisi** delle **Impostazioni** di Log Analytics.  

![Gestisci avvisi](./media/log-analytics-alerts/configure.png)

1. Nella console di OMS selezionare il riquadro **Impostazioni** .
2. Selezionare **Avvisi**.

Da questa visualizzazione è possibile eseguire più azioni.

* Disabilitare una regola selezionando **No** accanto alla regola desiderata.
* Modificare una regola di avviso facendo clic sull'icona a forma di matita accanto alla regola.
* Rimuovere una regola di avviso facendo clic sull'icona a forma di **X** accanto alla regola. 

## <a name="details-of-alert-rules"></a>Dettagli delle regole di avviso
Quando si crea o modifica una regola di avviso nel portale di OMS, si lavora con le pagine **Aggiungi regola di avviso** o **Modifica regola di avviso**.  Nelle tabelle seguenti vengono descritti i campi di questa schermata.

![Regola di avviso](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>Informazioni sull'avviso
Si tratta di impostazioni di base per la regola di avviso e gli avvisi che questa crea.

| Proprietà | Descrizione |
|:--- |:---|
| Nome | Nome univoco per identificare la regola di avviso. Questo nome è incluso in tutti gli avvisi creati dalla regola.  |
| Descrizione | Descrizione facoltativa della regola di avviso. |
| Severity |Livello di gravità di tutti gli avvisi creati da questa regola. |

### <a name="search-query-and-time-window"></a>Finestra della query di ricerca e dell'intervallo di tempo
La finestra della query di ricerca e dell'intervallo di tempo che restituisce i record da valutare per determinare se è necessario creare avvisi.

| Proprietà | Descrizione |
|:--- |:---|
| Query di ricerca | La query che viene eseguita.  I record restituiti da questa query verranno usati per determinare se viene creato un avviso.<br><br>Selezionare **Usa query di ricerca corrente** per usare la query corrente o selezionare una ricerca salvata dall'elenco.  La sintassi della query è disponibile nella casella di testo dove può essere modificata, se necessario. |
| Intervallo di tempo |Specifica l'intervallo di tempo per la query.  La query restituisce solo i record creati in questo intervallo dell'ora corrente.  Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore.  Deve essere maggiore o uguale alla frequenza degli avvisi.  <br><br> Ad esempio, se l'intervallo di tempo è impostato su 60 minuti e la query viene eseguita alle 13.15 , verranno restituiti solo i record creati tra 12.15 e le 13.15. |

Quando si specifica l'intervallo di tempo per la regola di avviso, verrà visualizzato il numero di record esistenti che corrispondono ai criteri di ricerca per tale intervallo di tempo.  Ciò consente di determinare la frequenza con cui verrà visualizzato il numero di risultati previsti.

### <a name="schedule"></a>Pianificazione
Definisce la frequenza con cui viene eseguita la query di ricerca.

| Proprietà | Descrizione |
|:--- |:---|
| Frequenza di avviso | Specifica la frequenza con cui deve essere eseguita la query. Può essere un valore qualsiasi compreso tra 5 minuti e 24 ore. Deve essere uguale o minore dell'intervallo di tempo.  Se il valore è maggiore dell'intervallo di tempo, si rischia di omettere il record.<br><br>Si considerino ad esempio un intervallo di tempo di 30 minuti e una frequenza pari a 60 minuti.  Una query eseguita alle 13:00 restituirà i record compresi tra le 12:30 e le 13:00.  La volta successiva, la query verrà eseguita alle 14:00 e restituirà i record compresi tra le 13:30 e le 14:00.  Qualsiasi record creato tra le 13:00 e 13:30 non verrà mai valutato. |


### <a name="generate-alert-based-on"></a>Genera l'avviso in base a
Definisce i criteri che verranno valutati rispetto ai risultati della query di ricerca per determinare se è necessario creare un avviso.  Questi dettagli saranno diversi a seconda del tipo di regola di avviso selezionato.  È possibile ottenere i dettagli per i differenti tipi di regole di avviso da [Informazioni sugli avvisi in Log Analytics](log-analytics-alerts.md).

| Proprietà | Descrizione |
|:--- |:---|
| Elimina avvisi | Quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, creerà i record di avviso. In questo modo, l'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate. |

#### <a name="number-of-results-alert-rules"></a>Regole di avviso Numero di risultati

| Proprietà | Descrizione |
|:--- |:---|
| Numero di risultati |Viene creato un avviso se il numero di record restituiti dalla query è **superiore** o **inferiore** al valore fornito.  |

#### <a name="metric-measurement-alert-rules"></a>Regole di avviso Unità di misura della metrica

| Proprietà | Descrizione |
|:--- |:---|
| Valore di aggregazione | Valore soglia che ogni valore di aggregazione nei risultati deve superare per essere considerato una violazione. |
| Attiva l'avviso in base a | Il numero di violazioni della sicurezza per creare un avviso.  È possibile specificare le **violazioni totali** per qualsiasi combinazione di violazioni nei set di risultati o le **violazioni consecutive** necessarie che devono verificarsi in campioni consecutivi. |

### <a name="actions"></a>Azioni
Le regole di avviso creano sempre un [record di avviso](#alert-records) quando viene raggiunta la soglia.  È anche possibile definire una o più risposte da eseguire, ad esempio l'invio di un messaggio di posta elettronica o l'avvio di un runbook.



#### <a name="email-actions"></a>Azioni di posta elettronica
Le azioni di posta elettronica inviano un messaggio di posta elettronica con i dettagli dell'avviso a uno o più destinatari.

| Proprietà | Descrizione |
|:--- |:---|
| Notifica tramite posta elettronica |Specificare **Sì** se si vuole inviare un messaggio di posta elettronica quando viene generato l'avviso. |
| Oggetto |Oggetto nel messaggio di posta elettronica.  È possibile modificare il corpo del messaggio. |
| Destinatari |Indirizzi di tutti i destinatari di posta elettronica.  Se si specifica più di un indirizzo, separare ognuno con un punto e virgola (;). |

#### <a name="webhook-actions"></a>Azioni webhook
Le azioni Webhook consentono di richiamare un processo esterno tramite una singola richiesta HTTP POST.

| Proprietà | Descrizione |
|:--- |:---|
| webhook |Specificare **Sì** se si vuole chiamare un webhook quando viene generato l'avviso. |
| URL webhook |URL del webhook. |
| Includi payload JSON personalizzato |Selezionare questa opzione se si vuole sostituire il payload predefinito con un payload personalizzato. |
| Specificare il payload JSON personalizzato |Payload personalizzato per il webhook.  Per informazioni dettagliate, vedere la sezione precedente. |

#### <a name="runbook-actions"></a>Azioni runbook
Le azioni runbook avviano un runbook in Automazione di Azure. 

>[!NOTE]
> È necessario che la soluzione Automazione sia installata nell'area di lavoro per poter abilitare questa azione. 


| Proprietà | Descrizione |
|:--- |:---|
| Runbook | Specificare **Sì** se si vuole avviare un runbook di Automazione di Azure quando viene generato l'avviso.  |
| Account di Automazione | Specifica l'account di Automazione da cui vengono selezionati i runbook.  Si tratta dell'account Azione collegato all'area di lavoro. |
| Selezionare un runbook | Selezionare il runbook da avviare quando viene creato un avviso. |
| Esegui in | Selezionare **Azure** per eseguire il runbook nel cloud.  Selezionare **Ruolo di lavoro ibrido** per eseguire il runbook in un agente in cui è installato un [ruolo di lavoro ibrido per runbook](../automation/automation-hybrid-runbook-worker.md ).  |




## <a name="next-steps"></a>Passaggi successivi
* Installare la [soluzione Gestione avvisi](log-analytics-solution-alert-management.md) per analizzare gli avvisi creati in Log Analytics con gli avvisi raccolti da System Center Operations Manager (SCOM).
* Altre informazioni sulle [ricerche nei log](log-analytics-log-searches.md) che possono generare avvisi.
* Completare una procedura dettagliata per la [configurazione di un webhook](log-analytics-alerts-webhooks.md) con una regola di avviso.  
* Informazioni su come scrivere [runbook in Automazione di Azure](https://azure.microsoft.com/documentation/services/automation) per la risoluzione dei problemi identificati dagli avvisi.



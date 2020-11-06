---
title: Configurare gli avvisi di Advisor metriche
titleSuffix: Azure Cognitive Services
description: Come configurare gli avvisi di Advisor per le metriche usando hook per la posta elettronica, il Web e Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420921"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Procedura: configurare gli avvisi e ricevere notifiche tramite un hook

Una volta rilevata un'anomalia da metrica Advisor, viene attivata una notifica di avviso in base alle impostazioni degli avvisi, usando un hook. Un'impostazione di avviso può essere usata con più configurazioni di rilevamento, per personalizzare la regola di avviso sono disponibili vari parametri.

## <a name="create-a-hook"></a>Creare un hook

Metrica Advisor supporta tre tipi diversi di hook: hook di posta elettronica, Hook Web e Azure DevOps. È possibile scegliere quello che funziona per uno scenario specifico.       

### <a name="email-hook"></a>Hook di posta elettronica

> [!Note]
> Gli amministratori di risorse Advisor di metrica devono configurare le impostazioni di posta elettronica e immettere le informazioni relative a SMTP in metrica Advisor prima di poter inviare avvisi di anomalie. L'amministratore del gruppo di risorse o l'amministratore della sottoscrizione deve assegnare almeno un ruolo di *amministratore di metrica di servizi cognitivi* nella scheda controllo di accesso della risorsa Advisor di metrica. [Altre informazioni sulla configurazione delle impostazioni di posta elettronica](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Per creare un hook di posta elettronica, sono disponibili i parametri seguenti: 

Un hook di posta elettronica è il canale per gli avvisi di anomalie da inviare agli indirizzi di posta elettronica specificati nella sezione **posta elettronica** . Verranno inviati due tipi di messaggi di posta elettronica di avviso: *feed di dati non disponibili* e *report sugli eventi imprevisti* che contengono una o più anomalie. 

|Parametro |Description  |
|---------|---------|
| Nome | Nome dell'hook di posta elettronica |
| Invia un messaggio di posta elettronica a| Indirizzi di posta elettronica che inviano avvisi a|
| Collegamento esterno | Campo facoltativo che consente un reindirizzamento personalizzato, ad esempio per la risoluzione dei problemi relativi alle note. |
| Titolo avviso anomalie personalizzato | Il modello di titolo supporta `${severity}` ,, `${alertSettingName}` `${datafeedName}` , `${metricName}` , `${detectConfigName}` , `${timestamp}` , `${topDimension}` , `${incidentCount}` , `${anomalyCount}`

Dopo aver fatto clic su **OK** , verrà creato un hook di posta elettronica. È possibile usarlo in qualsiasi impostazione di avviso per ricevere avvisi di anomalie. 

### <a name="web-hook"></a>Hook Web

> [!Note]
> * Usare il metodo **post** request.
> * Il corpo della richiesta sarà simile al seguente:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Quando si crea o si modifica un webhook, l'API verrà chiamata come test con un corpo della richiesta vuoto. L'API deve restituire un codice HTTP 200.

Un webhook è il punto di ingresso per tutte le informazioni disponibili nel servizio di Advisor delle metriche e chiama un'API fornita dall'utente quando viene attivato un avviso. Tutti gli avvisi possono essere inviati tramite un webhook.

Per creare un webhook, è necessario aggiungere le seguenti informazioni:

|Parametro |Description  |
|---------|---------|
|Endpoint     | Indirizzo API da chiamare quando viene generato un avviso.        |
|Nome utente/password | Per l'autenticazione all'indirizzo dell'API. Lasciare questo nero se l'autenticazione non è necessaria.         |
|Intestazione     | Intestazioni personalizzate nella chiamata API.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="finestra di creazione dell'hook Web.":::

Quando viene eseguito il push di una notifica tramite un webhook, è possibile usare le API seguenti per ottenere i dettagli dell'avviso. Impostare *timestamp* e *ALERTSETTINGGUID* nel servizio API in cui viene eseguito il push, quindi usare le query seguenti: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Metrica Advisor supporta anche la creazione automatica di un elemento di lavoro in Azure DevOps per tenere traccia di problemi/bug quando viene rilevata un'anomalia. Tutti gli avvisi possono essere inviati tramite hook DevOps di Azure.

Per creare un hook di Azure DevOps, è necessario aggiungere le informazioni seguenti

|Parametro |Description  |
|---------|---------|
| Nome | Nome per l'hook |
| Organization | L'organizzazione a cui appartiene la DevOps |
| Project | Progetto specifico in DevOps. |
| Token di accesso |  Token per l'autenticazione a DevOps. | 

> [!Note]
> È necessario concedere autorizzazioni di scrittura se si vuole che l'Advisor di metrica crei elementi di lavoro in base a avvisi di anomalie. Dopo aver creato gli hook, è possibile usarli in tutte le impostazioni di avviso. Gestire i hook nella pagina **Impostazioni Hook** .

## <a name="add-or-edit-alert-settings"></a>Aggiungere o modificare le impostazioni degli avvisi

Passare alla pagina dei dettagli delle metriche per trovare la sezione **Impostazioni avvisi** nell'angolo in basso a sinistra della pagina dei dettagli della metrica. Elenca tutte le impostazioni di avviso che si applicano alla configurazione di rilevamento selezionata. Quando viene creata una nuova configurazione di rilevamento, non viene impostata alcuna impostazione di avviso e non verrà inviato alcun avviso.  
Per modificare le impostazioni degli avvisi, è possibile usare le icone **Aggiungi** , **modifica** ed **Elimina** .

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Voce di menu impostazioni avvisi.":::

Fare clic sui pulsanti **Aggiungi** o **modifica** per ottenere una finestra per aggiungere o modificare le impostazioni di avviso.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Aggiungere o modificare le impostazioni degli avvisi":::

**Nome impostazione avviso** : il nome dell'impostazione di avviso. Verrà visualizzato nel titolo del messaggio di posta elettronica di avviso.

**Hook** : elenco di hook a cui inviare avvisi.

La sezione contrassegnata nella schermata precedente sono le impostazioni per una configurazione di rilevamento. È possibile impostare impostazioni di avviso diverse per configurazioni di rilevamento diverse. Scegliere la configurazione di destinazione usando il terzo elenco a discesa in questa finestra. 

### <a name="filter-settings"></a>Impostazioni dei filtri 

Di seguito sono riportate le impostazioni di filtro per una configurazione di rilevamento.

**Avviso per** dispone di 4 opzioni per filtrare le anomalie:

* **Anomalie in tutte le serie** : tutte le anomalie verranno incluse nell'avviso.         
* **Anomalie nel gruppo serie** : filtrare le serie in base ai valori delle dimensioni. Impostare valori specifici per alcune dimensioni. Le anomalie verranno incluse nell'avviso solo quando la serie corrisponde al valore specificato.       
* **Anomalie nella serie preferiti** : solo le serie contrassegnate come Preferiti verranno incluse nell'avviso.        |
* **Anomalie nelle prime n di tutte le serie** : questo filtro è per il caso in cui si è interessati solo alla serie il cui valore è nelle prime n. Verranno esaminati alcuni timestamp e verrà verificato se il valore della serie in questo timestamp si trova in primi N. Se il conteggio "in primi n" è maggiore del numero specificato, l'anomalia verrà inclusa in un avviso.        |

Le **Opzioni di filtro anomalie** sono un filtro aggiuntivo con le opzioni seguenti:

- **gravità** : l'anomalia verrà inclusa solo quando la gravità dell'anomalia rientra nell'intervallo specificato.
- **Snooze** : interrompe temporaneamente gli avvisi per le anomalie nei successivi N punti (periodo), quando viene attivato in un avviso.
    - **tipo Snooze** : se impostata su **serie** , un'anomalia attivata rileverà solo la serie. Per la **metrica** , un'anomalia attivata rileverà tutte le serie in questa metrica.
    - **numero di snooze** : numero di punti (punto) da posporre.
    - **Reimposta per non consecutivo** : quando selezionato, un'anomalia attivata rileverà solo le successive anomalie successive. Se uno dei punti dati seguenti non è un'anomalia, il Snooze verrà reimpostato da tale punto; Una volta deselezionata, un'anomalia attivata rileverà i successivi n punti (periodo), anche se i punti dati successivi non sono anomalie.
- **valore** (facoltativo): filtrare in base al valore. Verranno inclusi solo i valori dei punti che soddisfano la condizione. Se si usa il valore corrispondente di un'altra metrica, i nomi delle dimensioni delle due metriche devono essere coerenti.

Le anomalie non filtrate verranno inviate in un avviso.

### <a name="add-cross-metric-settings"></a>Aggiungere le impostazioni della metrica

Per aggiungere un'altra sezione, fare clic su **+ Aggiungi impostazioni tra metriche** nella pagina Impostazioni avviso.

Il selettore dell' **operatore** è la relazione logica di ogni sezione per determinare se inviano un avviso.


|Operatore  |Descrizione  |
|---------|---------|
|AND     | Invia un avviso solo se una serie corrisponde a ogni sezione di avviso e tutti i punti dati sono anomalie. Se le metriche hanno nomi di dimensione diversi, un avviso non verrà mai attivato.         |
|OR     | Inviare l'avviso se almeno una sezione contiene anomalie.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="Operatore per la sezione più impostazioni di avviso":::

## <a name="next-steps"></a>Passaggi successivi

- [Regolare il rilevamento anomalie usando il feedback](anomaly-feedback.md)
- [Diagnosticare un evento imprevisto](diagnose-incident.md).
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](configure-metrics.md)
---
title: Risoluzione dei problemi relativi a avvisi e notifiche di monitoraggio di Azure
description: Problemi comuni con gli avvisi di monitoraggio di Azure e le possibili soluzioni.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: d86c6d8b67546aede86a3fc3c905f6feb40d92db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91403416"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Risoluzione dei problemi negli avvisi di monitoraggio di Azure

Questo articolo illustra i problemi comuni negli avvisi e nelle notifiche di monitoraggio di Azure.

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](alerts-overview.md).

Se si verifica un problema con la generazione di un avviso o se non viene attivato quando previsto, vedere gli articoli seguenti. È possibile visualizzare gli avvisi "attivato" nel portale di Azure.

- [Risoluzione dei problemi relativi agli avvisi delle metriche di monitoraggio di Azure in Microsoft Azure](alerts-troubleshoot-metric.md)  
- [Risoluzione dei problemi relativi agli avvisi del log di monitoraggio di Azure in Microsoft Azure](alerts-troubleshoot-metric.md)

Se l'avviso viene attivato come previsto in base al portale di Azure ma non si verificano le notifiche appropriate, usare le informazioni riportate nella parte restante di questo articolo per risolvere il problema.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>L'azione o la notifica nell'avviso non funziona come previsto

Se è possibile visualizzare un avviso attivato nell'portale di Azure, ma si verifica un problema con alcune delle azioni o notifiche, vedere le sezioni seguenti.

## <a name="did-not-receive-expected-email"></a>Non è stato ricevuto un messaggio di posta elettronica previsto

Se un avviso attivato è visibile nel portale di Azure ma non si è ricevuto il messaggio di posta elettronica così come configurato, seguire questa procedura:

1. **Il messaggio di posta elettronica è stato eliminato da una [regola di azione](alerts-action-rules.md)**?

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda della cronologia relativa ai [gruppi di azioni](action-groups.md) eliminati:

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

1. **Il tipo di azione "email Azure Resource Manager role"?**

    Questa azione esamina solo le assegnazioni di ruolo Azure Resource Manager che si trovano nell'ambito della sottoscrizione e di tipo *User*.  Assicurarsi di aver assegnato il ruolo a livello di sottoscrizione e non a livello di risorsa o di gruppo di risorse.

1. **Il server di posta elettronica e la cassetta postale accettano messaggi di posta elettronica esterni?**

    Verificare che i messaggi di posta elettronica da questi tre indirizzi non siano bloccati:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    È comune che le liste di distribuzione interne blocchino i messaggi di posta elettronica da indirizzi di posta elettronica esterni. È necessario consentire la posta degli indirizzi di posta elettronica precedenti.  
    Per testare, aggiungere un normale indirizzo di posta elettronica aziendale, non una lista di distribuzione, al gruppo di azioni e verificare che gli avvisi arrivino a tale indirizzo di posta elettronica.

1. **Il messaggio di posta elettronica è stato elaborato dalle regole della posta in arrivo o da un filtro antispam**

    Verificare che non siano presenti regole Posta in arrivo che eliminano i messaggi di posta elettronica o che li spostano in una cartella vicina. Ad esempio, le regole della posta in arrivo potrebbero rilevare mittenti specifici o parole specifiche nell'oggetto.

    Verificare anche:

   - le impostazioni della posta indesiderata del client di posta elettronica, ad esempio Outlook, Gmail
      - impostazioni di quarantena/impostazioni della posta indesiderata per il server di posta elettronica (ad esempio Exchange, Microsoft 365, G-Suite)
      - impostazioni dell'appliance di sicurezza della posta elettronica, se presente (ad esempio Barracuda, Cisco).

1. **Si è accidentalmente annullata la sottoscrizione al gruppo di azioni?**

    I messaggi di posta elettronica di avviso contengono un collegamento per annullare la sottoscrizione al gruppo di azioni. Per verificare se la sottoscrizione a questo gruppo di azione è stata annullata per errore, eseguire una delle operazioni seguenti:

    1. Aprire il gruppo di azioni nel portale e controllare la colonna Stato:

    ![colonna Stato del gruppo di azioni](media/alerts-troubleshoot/action-group-status.png)

    2. Cercare il messaggio di posta elettronica per confermare l'annullamento della sottoscrizione:

    ![annullamento della sottoscrizione dal gruppo di azioni di avviso riuscito](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Per eseguire di nuovo la sottoscrizione, usare il collegamento nel messaggio di posta elettronica di conferma dell'annullamento della sottoscrizione ricevuto oppure rimuovere l'indirizzo di posta elettronica dal gruppo di azioni e poi aggiungerlo di nuovo. 
 
1. **Il voto è limitato a causa di numerosi messaggi di posta elettronica che passano a un unico indirizzo di posta elettronica?**

    È previsto un [limite](alerts-rate-limiting.md) di 100 messaggi di posta elettronica all'ora per ogni indirizzo. Se si supera questa soglia, verranno eliminate altre notifiche di posta elettronica.  Controllare di aver ricevuto un messaggio che indica che è stato raggiunto il limite temporaneo per l'indirizzo di posta elettronica: 
 
   ![Posta elettronica limitata](media/alerts-troubleshoot/email-paused.png)

   Se si vuole ricevere un volume elevato di notifiche senza limiti di frequenza, è consigliabile usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o runbook di automazione, nessuna delle quali prevede un limite di frequenza. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Non è stato ricevuto un SMS, una chiamata vocale o una notifica push prevista

Se un avviso attivato è visibile nel portale ma non si è ricevuto il relativo SMS, la chiamata vocale o la notifica così come configurato, seguire questa procedura: 

1. **L'azione è stata eliminata da una [regola di azione](alerts-action-rules.md)?**

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda della cronologia relativa ai [gruppi di azioni](action-groups.md) eliminati: 

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

   Se non era intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione.
 
1. **SMS/Voice: il numero di telefono è corretto?**

   Controllare l'azione SMS per rilevare eventuali errori di digitazione nel codice paese o nel numero di telefono.
 
1. **SMS/Voice: è stata limitata la tariffa?**

    Per SMS e chiamate vocali è prevista una frequenza limitata a non più di una notifica ogni cinque minuti per numero di telefono. Se si supera questa soglia, le notifiche verranno eliminate.

      - Chiamata vocale: controllare la cronologia delle chiamate e verificare se è stata ricevuta una chiamata diversa da Azure nei cinque minuti precedenti.
      - SMS: controllare la cronologia degli SMS per verificare se è presente un messaggio indicante che il numero di telefono è soggetto a limite di frequenza.

    Se si vuole ricevere un volume elevato di notifiche senza limiti di frequenza, è consigliabile usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o runbook di automazione, nessuna delle quali prevede un limite di frequenza. 
 
1. **SMS: è stata annullata una sottoscrizione accidentale dal gruppo di azioni?**

    Aprire la cronologia SMS e verificare se è stato escluso il recapito SMS da questo gruppo di azione specifico (tramite la DISABILITAzione della risposta action_group_short_name) o da tutti i gruppi di azioni (usando la risposta di arresto). Per eseguire di nuovo la sottoscrizione, inviare il comando SMS pertinente (ENABLE action_group_short_name o START) oppure rimuovere l'azione SMS dal gruppo di azioni e quindi aggiungerlo di nuovo.  Per ulteriori informazioni, vedere [Comportamento degli avvisi SMS nei gruppi di azioni](alerts-sms-behavior.md).

1. **Sono state bloccate accidentalmente le notifiche sul telefono?**

   La maggior parte dei cellulari consente di bloccare le chiamate o gli SMS provenienti da specifici numeri di telefono o codici brevi oppure di bloccare le notifiche push di specifiche app, ad esempio l'app per dispositivi mobili di Azure. Per verificare se le notifiche sono state bloccate per errore nel telefono, vedere la documentazione specifica del modello o del sistema operativo dell'apparecchio oppure provare a usare un telefono e un numero di telefono differenti.

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>È previsto un altro tipo di azione da attivare, ma non è stato 
   
Se un avviso attivato è visibile nel portale, ma la relativa azione configurata non è stata attivata, seguire questa procedura:

1. **L'azione è stata eliminata da una regola di azione?**

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda della cronologia relativa ai [gruppi di azioni](action-groups.md) eliminati:

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)
 
    Se non era intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione.

1. **Un webhook non è stato attivato?**

    1. **Gli indirizzi IP di origine sono stati bloccati?**
    
       Aggiungere gli [indirizzi IP](action-groups.md#action-specific-information) da cui viene chiamato il webhook all'elenco Consenti.

    1. **L'endpoint del webhook funziona correttamente?**

       Verificare che l'endpoint del webhook configurato sia corretto e che funzioni correttamente. Controllare i log del webhook o instrumentarne il codice in modo che sia possibile analizzarlo (ad esempio, registrare il payload in ingresso).

    1. **Si sta chiamando Slack o Microsoft Teams?**  
    Ognuno di questi endpoint prevede un formato JSON specifico. Seguire [queste istruzioni](action-groups-logic-app.md) per configurare in alternativa un'azione dell'app per la logica.

    1. **Il webhook non è più attivo o ha restituito errori?** 

        Il periodo di timeout per una risposta del webhook è di 10 secondi. La chiamata al webhook verrà riprovata per un massimo di altre due volte quando vengono restituiti i codici di stato HTTP seguenti: 408, 429, 503, 504 oppure quando l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. Il secondo e ultimo tentativo si verifica dopo 100 secondi. Se il secondo tentativo ha esito negativo, l'endpoint non viene richiamato per 30 minuti per tutti i gruppi di azioni.

## <a name="action-or-notification-happened-more-than-once"></a>L'azione o la notifica è stata eseguita più di una volta 

Se si riceve più volte una notifica per un avviso (ad esempio un messaggio di posta elettronica o un SMS) oppure se l'azione dell'avviso (ad esempio un webhook o una funzione di avviso) viene attivata più volte, seguire questa procedura: 

1. **Si tratta di un vero e proprio avviso?** 

    In alcuni casi, vengono attivati più avvisi simili quasi contemporaneamente. Quindi, potrebbe sembrare che lo stesso avviso abbia attivato più volte le azioni. Una regola di avviso per i log attività, ad esempio, potrebbe essere configurata in modo da essere attivata quando un evento viene avviato e quando viene completato (correttamente o con errore) non filtrando in base al campo dello stato dell'evento. 

    Per verificare se queste azioni o notifiche provengono da avvisi diversi, esaminare i dettagli dell'avviso, ad esempio il timestamp e l'ID di avviso o l'ID di correlazione. In alternativa, controllare l'elenco degli avvisi attivati nel portale. In tal caso, è necessario adattare la logica della regola di avviso o configurare l'origine dell'avviso. 

1. **L'azione viene ripetuta in più gruppi di azioni?** 

    Quando viene generato un avviso, ognuno dei relativi gruppi di azioni viene elaborato in modo indipendente. Se quindi un'azione (ad esempio un indirizzo di posta elettronica) viene visualizzata in più gruppi di azioni attivati, viene chiamata una volta per ogni gruppo di azioni. 

    Per verificare quali gruppi di azione sono stati attivati, controllare la scheda della cronologia degli avvisi. Qui verranno visualizzati sia i gruppi di azioni definiti nella regola di avviso che i gruppi di azioni aggiunti all'avviso dalle regole di azione: 

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Azione o notifica con contenuto imprevisto

Se l'utente ha ricevuto l'avviso, ma ritiene che alcuni campi siano mancanti o errati, attenersi alla procedura seguente: 

1. **È stato scelto il formato corretto per l'azione?** 

    Ogni tipo di azione (posta elettronica, webhook e così via) ha due formati: il formato predefinito, quello legacy e il [formato dello schema comune più recente](alerts-common-schema.md). Quando si crea un gruppo di azioni, è necessario specificare il formato desiderato per ogni azione: le diverse azioni nei gruppi di azioni possono avere formati diversi. 

    Ad esempio, per l'azione webhook: 

    ![opzione dello schema azione webhook](media/alerts-troubleshoot/webhook.png)

    Verificare che il formato specificato a livello di azione sia quello previsto. Ad esempio, è possibile che sia stato sviluppato un codice che risponde agli avvisi (webhook, funzione, app per la logica e così via), in cui è previsto un formato specifico, ma in un secondo momento, nell'azione l'utente o un altra persona ha specificato un formato diverso.  

    Controllare anche il formato del payload (JSON) per gli [avvisi del log attività](activity-log-alerts-webhook.md), per gli [avvisi di ricerca log](alerts-log-webhook.md) (sia Application Insights che Log Analytics), per gli [avvisi di metrica](alerts-metric-near-real-time.md#payload-schema), per lo [schema di avviso comune](alerts-common-schema-definitions.md) e per gli [avvisi della metrica classica](alerts-webhooks.md) deprecati.

 
1. **Avvisi del log attività: le informazioni sono disponibili nel log attività?** 

    Gli [avvisi del log attività](activity-log-alerts.md) sono avvisi basati sugli eventi scritti nel log attività di Azure, ad esempio gli eventi sulla creazione, l'aggiornamento o l'eliminazione di risorse di Azure, l'integrità del servizio e gli eventi di integrità delle risorse o i risultati di Azure Advisor e criteri di Azure. Se è stato ricevuto un avviso basato sul log attività, ma alcuni campi necessari mancano o non sono corretti, controllare innanzitutto gli eventi nel log attività. Se la risorsa di Azure non ha scritto i campi che si sta cercando nell'evento del log attività, non verranno inclusi nell'avviso corrispondente. 

## <a name="action-rule-is-not-working-as-expected"></a>La regola di azione non funziona come previsto 

Se un avviso attivato è visibile nel portale, ma la relativa regola di azione non ha funzionato come previsto, seguire questa procedura: 

1. **La regola di azione è abilitata?** 

    Controllare la colonna dello stato delle regole di azione per verificare che la regola di azione correlata sia abilitata. 

    ![grafici](media/alerts-troubleshoot/action-rule-status.png) 

    Se non è abilitata, è possibile abilitarla selezionandola e facendo clic su Abilita. 

1. **Si tratta di un avviso di integrità del servizio?** 

    Gli avvisi sull'integrità dei servizi (servizio di monitoraggio = "Integrità dei servizi") non sono interessati dalle regole di azione. 

1. **La regola di azione ha agito sull'avviso?** 

    Controllare se la regola di azione ha elaborato l'avviso facendo clic sull'avviso attivato nel portale, quindi esaminare la scheda della cronologia.

    Ecco un esempio di regola di azione che elimina tutti i gruppi di azioni: 
 
     ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

    Ecco un esempio di regola di azione che aggiunge un altro gruppo di azioni:

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **L'ambito e il filtro della regola di azione corrispondono all'avviso attivato?** 

    Se si ritiene che la regola di azione avrebbe dovuto essere attivata ma non lo è stata o viceversa, esaminare con attenzione l'ambito e le condizioni di filtro della regola di azione rispetto alle proprietà dell'avviso attivato. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Come trovare l'ID avviso di un avviso attivato

Quando si apre un caso relativo a un avviso attivato specifico (ad esempio, se non si riceve la notifica di posta elettronica), è necessario fornire l'ID avviso. 

Per individuarlo, attenersi alla seguente procedura:

1. Nella portale di Azure passare all'elenco degli avvisi attivati e individuare l'avviso specifico. È possibile utilizzare i filtri per individuarli. 

1. Fare clic sull'avviso per aprire i dettagli dell'avviso. 

1. Scorrere verso il basso nei campi avviso della prima scheda (scheda Riepilogo) fino a quando non viene individuata e copiarla. Il campo include anche un pulsante Helper "copia negli Appunti" che è possibile usare.  

    ![trova ID avviso](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problemi durante la creazione, l'aggiornamento o l'eliminazione di regole di azione nella portale di Azure

Se si è ricevuto un errore durante il tentativo di creare, aggiornare o eliminare una [regola di azione](alerts-action-rules.md), attenersi alla procedura seguente: 

1. **Si è ricevuto un errore di autorizzazione?**  

    È necessario avere il [ruolo predefinito collaboratore monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor)o le autorizzazioni specifiche relative alle regole di azione e agli avvisi.

1. **Si verificano i parametri della regola di azione?**  

    Controllare la [documentazione della regola di azione](alerts-action-rules.md)o la [regola azione PowerShell set-AzActionRule](/powershell/module/az.alertsmanagement/set-azactionrule?view=azps-3.5.0) comando. 


## <a name="next-steps"></a>Passaggi successivi
- Se si usa un avviso di log, vedere anche [risoluzione dei problemi relativi agli avvisi del log](./alerts-troubleshoot-log.md).
- Tornare alla [portale di Azure](https://portal.azure.com) per verificare se il problema è stato risolto con le linee guida precedenti 

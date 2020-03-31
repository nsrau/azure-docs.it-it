---
title: Risoluzione dei problemi relativi ad avvisi e notifiche di Monitoraggio di AzureTroubleshooting Azure Monitor alerts and notifications
description: Problemi comuni con gli avvisi di Monitoraggio di Azure e le possibili soluzioni.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132335"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Risoluzione dei problemi negli avvisi di Monitoraggio di AzureTroubleshooting problems in Azure Monitor alerts 

Questo articolo illustra i problemi comuni negli avvisi di Monitoraggio di Azure.This article discusses common problems in Azure Monitor alerting.

Gli avvisi di Monitoraggio di Azure inviano una notifica proattiva quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per altre informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure.For](alerts-overview.md)more information on alerting, see Overview of alerts in Microsoft Azure.

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Azione o notifica sull'avviso non ha funzionato come previsto

Se è possibile visualizzare un avviso generato nel portale di Azure, ma si verifica un problema con alcune delle relative azioni o notifiche, vedere le sezioni seguenti.

## <a name="did-not-receive-expected-email"></a>Non ha ricevuto l'e-mail prevista

Se è possibile visualizzare un avviso generato nel portale di Azure, ma non si è ricevuto il messaggio di posta elettronica configurato, eseguire la procedura seguente:If you can see a fired alert in the Azure portal, but did not receive the email that you have configured on it, follow these steps: 

1. **L'e-mail è stata soppressa da una regola di [azione?](alerts-action-rules.md)** 

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda Cronologia per i [gruppi di azioni soppresse](action-groups.md): 

    ![Cronologia di eliminazione delle regole di azione di avviso](media/alerts-troubleshoot/history-action-rule.png)

1. **Il tipo di azione "Email Azure Resource Manager Role"?**

    Questa azione esamina solo le assegnazioni di ruolo di Azure Resource Manager nell'ambito della sottoscrizione e di tipo *Utente*.  Assicurarsi di aver assegnato il ruolo a livello di sottoscrizione e non a livello di risorsa o di gruppo di risorse.

1. **Il server di posta elettronica e la cassetta postale accettano messaggi di posta elettronica esterni?**

    Verificare che le e-mail provenienti da questi tre indirizzi non siano bloccate:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    È comune che le liste di distribuzione interne o le liste di distribuzione blocchino i messaggi di posta elettronica provenienti da indirizzi di posta elettronica esterni. È necessario whitelist gli indirizzi e-mail di cui sopra.  
    Per eseguire il test, aggiungere un normale indirizzo di posta elettronica di lavoro (non una lista di distribuzione) al gruppo di azioni e verificare se gli avvisi arrivano a tale messaggio. 

1. **L'e-mail è stata elaborata dalle regole della posta in arrivo o da un filtro antispam?** 

    Verificare che non siano presenti regole di posta in arrivo che eliminino tali messaggi di posta elettronica o li spostino in una cartella laterale. Ad esempio, le regole della posta in arrivo potrebbero intercettare mittenti specifici o parole specifiche nell'oggetto.

    Inoltre, controllare:
    
      - le impostazioni di spam del client di posta elettronica (come Outlook, Gmail)
      - i limiti del mittente / impostazioni di spam / impostazioni di quarantena del server di posta elettronica (come Exchange, Office 365, G-suite)
      - le impostazioni dell'appliance di sicurezza e-mail, se presente (come Barracuda, Cisco). 

1. **Hai accidentalmente annullato l'iscrizione al gruppo di azioni?** 

    Le e-mail di avviso forniscono un collegamento per annullare l'iscrizione al gruppo di azioni. Per verificare se hai accidentalmente annullato l'iscrizione a questo gruppo di azioni:

    1. Aprire il gruppo di azioni nel portale e controllare la colonna Stato:

    ![colonna Stato gruppo di azioni](media/alerts-troubleshoot/action-group-status.png)

    2. Cerca la tua email per la conferma di annullamento dell'iscrizione:

    ![annulla la sottoscrizione del gruppo di azioni di avviso](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Per iscriverti di nuovo: utilizza il link nell'e-mail di conferma dell'annullamento dell'iscrizione che hai ricevuto oppure rimuovi l'indirizzo e-mail dal gruppo di azioni, quindi aggiungilo di nuovo. 
 
1. **Sei stato valutato limitato a causa di molte e-mail che vanno a un singolo indirizzo e-mail?** 

    L'e-mail è [limitata](alerts-rate-limiting.md) a non più di 100 e-mail ogni ora per ogni indirizzo e-mail. Se superi questa soglia, le notifiche email aggiuntive vengono eliminate.  Controlla se hai ricevuto un messaggio che indica che il tuo indirizzo email è stato temporaneamente limitato: 
 
   ![Frequenza e-mail limitata](media/alerts-troubleshoot/email-paused.png)

   Se si vuole ricevere un volume elevato di notifiche senza limiti di frequenza, è consigliabile usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o runbook di automazione, nessuna delle quali è limitata alla frequenza. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Non ha ricevuto SMS, chiamate vocali o notifiche push previste

Se è possibile visualizzare un avviso generato nel portale, ma non ha ricevuto l'SMS, la chiamata vocale o la notifica push configurata, attenersi alla seguente procedura: 

1. **L'azione è stata soppressa da una regola di [azione?](alerts-action-rules.md)** 

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda Cronologia per i [gruppi di azioni soppresse](action-groups.md): 

    ![Cronologia di eliminazione delle regole di azione di avviso](media/alerts-troubleshoot/history-action-rule.png)

   Se ciò non è stato intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione.
 
1. **SMS / voce: Il tuo numero di telefono è corretto?**

   Controlla l'azione SMS per gli errori di battitura nel codice del paese o nel numero di telefono. 
 
1. **SMS / voce: sei stato tasso limitato?** 

    Gli SMS e le chiamate vocali sono limitati a non più di una notifica ogni cinque minuti per numero di telefono. Se superi questa soglia, le notifiche verranno interrotte. 

      - Chiamata vocale: controllare la cronologia delle chiamate e verificare se si è stata una chiamata diversa da Azure nei cinque minuti precedenti. 
      - SMS - controlla la cronologia DEGLI SMS per un messaggio che indica che il tuo numero di telefono è stato limitato. 

    Se si vuole ricevere un volume elevato di notifiche senza limiti di frequenza, è consigliabile usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o runbook di automazione, nessuna delle quali è limitata alla frequenza. 
 
1. **SMS: Hai accidentalmente annullato l'iscrizione al gruppo di azioni?**

    Apri la cronologia SMS e verifica se hai scelto di non recapitare SMS da questo gruppo di azioni specifico (utilizzando la risposta action_group_short_name DISABLE) o da tutti i gruppi di azioni (utilizzando la risposta STOP). Per eseguire nuovamente la sottoscrizione, inviare il comando SMS pertinente (ENABLE action_group_short_name o START) oppure rimuovere l'azione SMS dal gruppo di azioni, quindi aggiungerla di nuovo.  Per ulteriori informazioni, consultate Comportamento degli [avvisi SMS nei gruppi di azioni.](alerts-sms-behavior.md)

1. **Hai accidentalmente bloccato le notifiche sul tuo telefono?**

   La maggior parte dei telefoni cellulari consente di bloccare le chiamate o gli SMS da numeri di telefono o codici brevi specifici o di bloccare le notifiche push da app specifiche ( ad esempio l'app per dispositivi mobili di Azure). Per verificare se hai accidentalmente bloccato le notifiche sul telefono, cerca la documentazione specifica per il sistema operativo e il modello del telefono o prova con un numero di telefono e di un numero di telefono diverso. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Previsto un altro tipo di azione per l'attivazione, ma non 

Se è possibile visualizzare un avviso attivato nel portale, ma l'azione configurata non è stata attivata, attenersi alla seguente procedura: 

1. **L'azione è stata soppressa da una regola di azione?** 

    Controllare facendo clic sull'avviso attivato nel portale ed esaminare la scheda Cronologia per i [gruppi di azioni soppresse](action-groups.md): 

    ![Cronologia di eliminazione delle regole di azione di avviso](media/alerts-troubleshoot/history-action-rule.png)
 
    Se ciò non è stato intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione. 

1. **Un webhook non si è attivato?**

    1. **Gli indirizzi IP di origine sono stati bloccati?**
    
       Whitelist gli [indirizzi IP](action-groups.md#action-specific-information) che il webhook viene chiamato da.

    1. **L'endpoint webhook funziona correttamente?**

       Verificare che l'endpoint webhook configurato sia corretto e che l'endpoint funzioni correttamente. Controllare i log webhook o instrumentare il relativo codice in modo da poter analizzare (ad esempio, registrare il payload in ingresso). 

    1. **Stai chiamando Slack o Microsoft Teams?**  
    Ognuno di questi endpoint prevede un formato JSON specifico. Seguire [queste istruzioni](action-groups-logic-app.md) per configurare invece un'azione dell'app per la logica.

    1. **Il webhook non ha risposto o ha restituito errori?** 

        Il periodo di timeout per una risposta webhook è di 10 secondi. La chiamata webhook verrà ritentata fino a due volte aggiuntive quando vengono restituiti i seguenti codici di stato HTTP: 408, 429, 503, 504 o quando l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. Il secondo e ultimo tentativo viene eseguito dopo 100 secondi. Se il secondo tentativo ha esito negativo, l'endpoint non viene richiamato per 30 minuti per tutti i gruppi di azioni.

## <a name="action-or-notification-happened-more-than-once"></a>Azione o notifica eseguita più di una volta 

Se è stata ricevuta una notifica per un avviso (ad esempio un messaggio di posta elettronica o un SMS) più di una volta o l'azione dell'avviso (ad esempio webhook o funzione di Azure) è stata attivata più volte, attenersi alla seguente procedura: 

1. **È davvero lo stesso avviso?** 

    In alcuni casi, più avvisi simili vengono generati all'incirca nello stesso momento. Così, potrebbe sembrare come lo stesso avviso attivato le sue azioni più volte. Ad esempio, una regola di avviso del log attività potrebbe essere configurata per essere attivata sia quando un evento è stato avviato, sia quando è terminato (operazione completata o non riuscita), non filtrando il campo dello stato dell'evento. 

    Per verificare se queste azioni o notifiche provengono da avvisi diversi, esaminare i dettagli dell'avviso, ad esempio il timestamp e l'ID avviso o l'ID di correlazione. In alternativa, controllare l'elenco degli avvisi attivati nel portale. In tal caso, è necessario adattare la logica della regola di avviso o configurare in altro modo l'origine dell'avviso. 

1. **L'azione si ripete in più gruppi di azioni?** 

    Quando viene generato un avviso, ognuno dei relativi gruppi di azioni viene elaborato in modo indipendente. Pertanto, se un'azione (ad esempio un indirizzo di posta elettronica) viene visualizzata in più gruppi di azioni attivati, verrà chiamata una volta per ogni gruppo di azioni. 

    Per verificare quali gruppi di azioni sono stati attivati, controllare la scheda Cronologia avvisi. Verranno visualizzati sia i gruppi di azioni definiti nella regola di avviso che i gruppi di azioni aggiunti all'avviso dalle regole di azione:You would see there a action groups defined in the alert rule, and action groups added to the alert by action rules: 

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>L'azione o la notifica ha un contenuto imprevisto

Se hai ricevuto l'avviso, ma ritieni che alcuni dei suoi campi siano mancanti o errati, segui questi passaggi: 

1. **È stato scelto il formato corretto per l'azione?** 

    Ogni tipo di azione (e-mail, webhook e così via) ha due formati: il formato predefinito, legacy e il [formato dello schema comune più recente.](alerts-common-schema.md) Quando si crea un gruppo di azioni, si specifica il formato desiderato per ogni azione: azioni diverse nei gruppi di azioni possono avere formati diversi. 

    Ad esempio, per l'azione webhook: 

    ![Opzione schema azione webhook](media/alerts-troubleshoot/webhook.png)

    Verificare se il formato specificato a livello di azione è quello previsto. Ad esempio, potresti aver sviluppato codice che risponde agli avvisi (webhook, funzione, app per la logica e così via), in attesa di un formato, ma in un secondo momento nell'azione specificata dall'utente o da un'altra persona, è stato specificato un formato diverso.  

    Controllare inoltre il formato del payload (JSON) per [gli avvisi del log](activity-log-alerts-webhook.md)attività , per gli avvisi di ricerca nei [log](alerts-log-webhook.md) (sia Application Insights che analisi dei log), per gli avvisi [di metrica](alerts-metric-near-real-time.md#payload-schema), per lo schema degli [avvisi comuni](alerts-common-schema-definitions.md)e per gli avvisi [di metrica classica](alerts-webhooks.md)deprecati.

 
1. **Avvisi del log attività: le informazioni sono disponibili nel log attività?** 

    [Gli avvisi del log attività](activity-log-alerts.md) sono avvisi basati su eventi scritti nel log attività di Azure, ad esempio eventi relativi alla creazione, all'aggiornamento o all'eliminazione di risorse di Azure, all'integrità del servizio e agli eventi di integrità delle risorse o ai risultati di Azure Advisor e Criteri di Azure.Activity log alerts are alerts that are based on events written to the Azure Activity Log, such as events about creating, updating or deleting Azure resources, service health and resource health events, or findings from Azure Advisor and Azure Policy. Se è stato ricevuto un avviso basato sul registro attività ma alcuni campi necessari sono mancanti o non corretti, controllare innanzitutto gli eventi nel registro attività stesso. Se la risorsa di Azure non ha scritto i campi che si stanno cercando nel relativo evento del log attività, tali campi non verranno inclusi nell'avviso corrispondente. 

## <a name="action-rule-is-not-working-as-expected"></a>La regola dell'azione non funziona come previsto 

Se è possibile visualizzare un avviso attivato nel portale, ma una regola di azione correlata non ha funzionato come previsto, attenersi alla seguente procedura: 

1. **La regola di azione è abilitata?** 

    Controllare la colonna Stato regola azione per verificare che il ruolo azione correlato sia abilitato. 

    ![grafici](media/alerts-troubleshoot/action-rule-status.png) 

    Se non è abilitata, è possibile abilitare la regola di azione selezionandola e facendo clic su Abilita. 

1. **Si tratta di un avviso di integrità del servizio?** 

    Gli avvisi di integrità del servizio (servizio di monitoraggio - "Integrità dei servizi") non sono interessati dalle regole di azione. 

1. **La regola di azione ha agito in base all'avviso?** 

    Verificare se la regola di azione ha elaborato l'avviso facendo clic sull'avviso attivato nel portale ed esaminare la scheda Cronologia.

    Di seguito è riportato un esempio di regola di azione che sopprime tutti i gruppi di azioni:Here is an example of action rule suppressing all action groups: 
 
     ![Cronologia di eliminazione delle regole di azione di avviso](media/alerts-troubleshoot/history-action-rule.png)

    Di seguito è riportato un esempio di una regola di azione che aggiunge un altro gruppo di azioni:Here is an example of an action rule adding another action group:

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **L'ambito e il filtro della regola di azione corrispondono all'avviso attivato?** 

    Se si ritiene che la regola di azione avrebbe dovuto essere attivata ma non l'ha fatto o che non avrebbe dovuto essere attivata, ma lo ha fatto, esaminare attentamente l'ambito della regola di azione e le condizioni di filtro rispetto alle proprietà dell'avviso generato. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Come trovare l'ID di avviso di un avviso generatoHow to find the alert id of a fired alert

Quando si apre un caso relativo a un avviso generato specifico (ad esempio, se non si riceve la notifica tramite posta elettronica), è necessario fornire l'ID avviso. 

Per individuarlo, attenersi alla seguente procedura:

1. Nel portale di Azure passare all'elenco degli avvisi generati e individuare l'avviso specifico. È possibile utilizzare i filtri per individuare. 

1. Fare clic sull'avviso per aprire i dettagli dell'avviso. 

1. Scorrere verso il basso nei campi di avviso della prima scheda (la scheda Di riepilogo) fino a individuare e copiarlo. Questo campo include anche un pulsante helper "Copia negli Appunti" che è possibile utilizzare.  

    ![trovare l'ID avviso](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problema durante la creazione, l'aggiornamento o l'eliminazione di regole di azione nel portale di Azure

Se si è visualizzato un errore durante il tentativo di creare, aggiornare o eliminare una regola di [azione,](alerts-action-rules.md)attenersi alla seguente procedura: 

1. **Hai ricevuto un errore di autorizzazione?**  

    È necessario disporre del [ruolo predefinito Collaboratore monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)o delle autorizzazioni specifiche relative alle regole di azione e agli avvisi.

1. **Sono stati verificare i parametri della regola di azione?**  

    Controllare la [documentazione](alerts-action-rules.md)della regola di azione o il comando [PowerShell Set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) della regola di azione. 


## <a name="next-steps"></a>Passaggi successivi
- Se si utilizza un avviso di log, vedere anche [Risoluzione dei problemi relativi agli avvisi di registro](alert-log-troubleshoot.md).
- Tornare al [portale](https://portal.azure.com) di Azure per verificare se il problema è stato risolto con le indicazioni precedenti 

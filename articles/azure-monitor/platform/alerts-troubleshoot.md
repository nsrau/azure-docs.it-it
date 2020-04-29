---
title: Risoluzione dei problemi relativi a avvisi e notifiche di monitoraggio di Azure
description: Problemi comuni con gli avvisi di monitoraggio di Azure e le possibili soluzioni.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80132335"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Risoluzione dei problemi negli avvisi di monitoraggio di Azure 

Questo articolo illustra i problemi comuni negli avvisi di monitoraggio di Azure.

Gli avvisi di monitoraggio di Azure notificano in modo proattivo quando vengono rilevate condizioni importanti nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Per ulteriori informazioni sugli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>L'azione o la notifica nell'avviso non funziona come previsto

Se è possibile visualizzare un avviso attivato nell'portale di Azure, ma si verifica un problema con alcune delle azioni o notifiche, vedere le sezioni seguenti.

## <a name="did-not-receive-expected-email"></a>Non è stato ricevuto un messaggio di posta elettronica previsto

Se è possibile visualizzare un avviso attivato nel portale di Azure, ma non è stato ricevuto il messaggio di posta elettronica configurato, attenersi alla procedura seguente: 

1. **Il messaggio di posta elettronica è stato eliminato da una [regola di azione](alerts-action-rules.md)**? 

    Controllare facendo clic sull'avviso generato nel portale ed esaminare la scheda cronologia per i [gruppi di azioni](action-groups.md)eliminati: 

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

1. **Il tipo di azione "email Azure Resource Manager role"?**

    Questa azione esamina solo le assegnazioni di ruolo Azure Resource Manager che si trovano nell'ambito della sottoscrizione e di tipo *User*.  Assicurarsi di aver assegnato il ruolo a livello di sottoscrizione e non a livello di risorse o di gruppo di risorse.

1. **Il server di posta elettronica e la cassetta postale accettano messaggi di posta elettronica esterni?**

    Verificare che i messaggi di posta elettronica da questi tre indirizzi non siano bloccati:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    È comune che elenchi di distribuzione o liste di distribuzione interne blocchino i messaggi di posta elettronica da indirizzi esterni. È necessario inserire nell'elenco elementi consentiti gli indirizzi di posta elettronica precedenti.  
    Per eseguire il test, aggiungere un indirizzo di posta elettronica di lavoro normale (non una lista di distribuzione) al gruppo di azioni e verificare se gli avvisi arrivano al messaggio. 

1. **Il messaggio di posta elettronica è stato elaborato dalle regole della posta in arrivo o da un filtro antispam** 

    Verificare che non siano presenti regole della posta in arrivo che eliminino i messaggi di posta elettronica o li sposti in una cartella laterale. Ad esempio, le regole della posta in arrivo potrebbero rilevare mittenti specifici o parole specifiche nell'oggetto.

    Controllare anche:
    
      - impostazioni della posta indesiderata del client di posta elettronica (ad esempio Outlook, Gmail)
      - impostazioni di quarantena/impostazioni della posta indesiderata per il server di posta elettronica (ad esempio Exchange, Office 365, G-Suite)
      - impostazioni dell'appliance di sicurezza della posta elettronica, se presente (ad esempio Barracuda, Cisco). 

1. **Si è accidentalmente annullata la sottoscrizione al gruppo di azioni?** 

    I messaggi di posta elettronica di avviso forniscono un collegamento per annullare la sottoscrizione al gruppo di azioni. Per verificare se è stata annullata la sottoscrizione da questo gruppo di azione, eseguire una delle operazioni seguenti:

    1. Aprire il gruppo di azioni nel portale e controllare la colonna stato:

    ![colonna stato gruppo di azione](media/alerts-troubleshoot/action-group-status.png)

    2. Cercare il messaggio di posta elettronica per la conferma dell'annullamento della sottoscrizione:

    ![annullamento della sottoscrizione dal gruppo di azioni di avviso](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Per eseguire di nuovo la sottoscrizione, usare il collegamento nel messaggio di posta elettronica di conferma dell'annullamento della sottoscrizione ricevuto oppure rimuovere l'indirizzo di posta elettronica dal gruppo di azioni e quindi aggiungerlo di nuovo. 
 
1. **Il voto è limitato a causa di numerosi messaggi di posta elettronica che passano a un unico indirizzo di posta elettronica?** 

    La frequenza di posta elettronica è [limitata](alerts-rate-limiting.md) a non più di 100 di messaggi di posta elettronica ogni ora per ogni indirizzo di posta elettronica. Se si supera questa soglia, verranno eliminate ulteriori notifiche tramite posta elettronica.  Controllare se è stato ricevuto un messaggio che indica che l'indirizzo di posta elettronica è stato temporaneamente limitato: 
 
   ![Frequenza posta elettronica limitata](media/alerts-troubleshoot/email-paused.png)

   Se si vuole ricevere un volume elevato di notifiche senza limitazione della frequenza, provare a usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o manuali operativi di automazione, nessuna delle quali con frequenza limitata. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Non è stato ricevuto un SMS, una chiamata vocale o una notifica push prevista

Se è possibile visualizzare un avviso generato nel portale, ma non si riceve l'SMS, la chiamata vocale o la notifica push configurata, seguire questa procedura: 

1. **L'azione è stata eliminata da una [regola di azione](alerts-action-rules.md)?** 

    Controllare facendo clic sull'avviso generato nel portale ed esaminare la scheda cronologia per i [gruppi di azioni](action-groups.md)eliminati: 

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

   Se non era intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione.
 
1. **SMS/Voice: il numero di telefono è corretto?**

   Controllare l'azione SMS per errori di digitazione nel codice paese o nel numero di telefono. 
 
1. **SMS/Voice: è stata limitata la tariffa?** 

    Gli SMS e le chiamate vocali sono limitati a non più di una notifica ogni cinque minuti per numero di telefono. Se si passa questa soglia, le notifiche verranno eliminate. 

      - Chiamata vocale: controllare la cronologia delle chiamate e verificare se è stata rilevata una chiamata diversa da Azure nei cinque minuti precedenti. 
      - SMS: controllare la cronologia SMS per un messaggio che indica che il numero di telefono è stato limitato. 

    Se si vuole ricevere un volume elevato di notifiche senza limitazione della frequenza, provare a usare un'azione diversa, ad esempio webhook, app per la logica, funzione di Azure o manuali operativi di automazione, nessuna delle quali con frequenza limitata. 
 
1. **SMS: è stata annullata una sottoscrizione accidentale dal gruppo di azioni?**

    Aprire la cronologia SMS e verificare se è stato escluso il recapito SMS da questo gruppo di azione specifico (tramite la DISABILITAzione della risposta action_group_short_name) o da tutti i gruppi di azioni (usando la risposta di arresto). Per eseguire di nuovo la sottoscrizione, inviare il comando SMS pertinente (abilitare action_group_short_name o avviare) oppure rimuovere l'azione SMS dal gruppo di azioni e quindi aggiungerlo di nuovo.  Per ulteriori informazioni, vedere il [comportamento degli avvisi SMS nei gruppi di azioni](alerts-sms-behavior.md).

1. **Sono state bloccate accidentalmente le notifiche sul telefono?**

   La maggior parte dei telefoni cellulari consente di bloccare chiamate o SMS da numeri di telefono specifici o codici brevi oppure di bloccare le notifiche push da app specifiche, ad esempio la app per dispositivi mobili di Azure. Per verificare se le notifiche sono state bloccate accidentalmente sul telefono, eseguire una ricerca nella documentazione specifica del sistema operativo e del modello per il telefono oppure eseguire il test con un telefono e un numero di telefono diversi. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>È previsto un altro tipo di azione da attivare, ma non è stato 

Se è possibile visualizzare un avviso attivato nel portale, ma l'azione configurata non è stata attivata, attenersi alla procedura seguente: 

1. **L'azione è stata eliminata da una regola di azione?** 

    Controllare facendo clic sull'avviso generato nel portale ed esaminare la scheda cronologia per i [gruppi di azioni](action-groups.md)eliminati: 

    ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)
 
    Se non era intenzionale, è possibile modificare, disabilitare o eliminare la regola di azione. 

1. **Un webhook non è stato attivato?**

    1. **Gli indirizzi IP di origine sono stati bloccati?**
    
       Whitelist degli [indirizzi IP](action-groups.md#action-specific-information) da cui viene chiamato il webhook.

    1. **L'endpoint del webhook funziona correttamente?**

       Verificare che l'endpoint del webhook configurato sia corretto e che l'endpoint funzioni correttamente. Controllare i log del webhook o instrumentare il codice in modo che sia possibile analizzare (ad esempio, registrare il payload in ingresso). 

    1. **Si sta chiamando Slack o Microsoft Teams?**  
    Ognuno di questi endpoint prevede un formato JSON specifico. Seguire [queste istruzioni](action-groups-logic-app.md) per configurare un'azione dell'app per la logica.

    1. **Il webhook non è più attivo o ha restituito errori?** 

        Il periodo di timeout per una risposta webhook è di 10 secondi. La chiamata al webhook verrà ritentata fino a due volte quando vengono restituiti i codici di stato HTTP seguenti: 408, 429, 503, 504 o quando l'endpoint HTTP non risponde. La prima ripetizione del tentativo avviene dopo 10 secondi. Il secondo e l'ultimo tentativo si verificano dopo 100 secondi. Se il secondo tentativo ha esito negativo, l'endpoint non viene richiamato per 30 minuti per tutti i gruppi di azioni.

## <a name="action-or-notification-happened-more-than-once"></a>L'azione o la notifica è stata eseguita più di una volta 

Se è stata ricevuta una notifica per un avviso, ad esempio un messaggio di posta elettronica o un SMS, più di una volta, oppure l'azione dell'avviso, ad esempio webhook o funzione di Azure, è stata attivata più volte, attenersi alla procedura seguente: 

1. **Si tratta di un vero e proprio avviso?** 

    In alcuni casi, più avvisi simili vengono generati intorno allo stesso tempo. Quindi, potrebbe sembrare che lo stesso avviso abbia attivato più volte le azioni. Una regola di avviso del log attività, ad esempio, può essere configurata in modo da essere attivata sia quando è stato avviato un evento che dopo il completamento (esito positivo o negativo), non filtrando il campo stato evento. 

    Per verificare se queste azioni o notifiche provengono da avvisi diversi, esaminare i dettagli dell'avviso, ad esempio il timestamp e l'ID di avviso o l'ID di correlazione. In alternativa, controllare l'elenco degli avvisi attivati nel portale. In tal caso, è necessario adattare la logica della regola di avviso o configurare l'origine dell'avviso. 

1. **L'azione viene ripetuta in più gruppi di azioni?** 

    Quando viene generato un avviso, ognuno dei relativi gruppi di azioni viene elaborato in modo indipendente. Se quindi un'azione (ad esempio un indirizzo di posta elettronica) viene visualizzata in più gruppi di azioni attivati, viene chiamata una volta per ogni gruppo di azioni. 

    Per verificare quali gruppi di azione sono stati attivati, controllare la scheda cronologia avviso. Verranno visualizzati sia i gruppi di azioni definiti nella regola di avviso che i gruppi di azioni aggiunti all'avviso dalle regole di azione: 

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Azione o notifica con contenuto imprevisto

Se l'avviso è stato ricevuto, ma si ritiene che alcuni dei relativi campi siano mancanti o errati, attenersi alla procedura seguente: 

1. **È stato scelto il formato corretto per l'azione?** 

    Ogni tipo di azione (posta elettronica, webhook e così via) ha due formati: il formato predefinito, quello legacy e il [formato dello schema comune più recente](alerts-common-schema.md). Quando si crea un gruppo di azioni, è necessario specificare il formato desiderato per azione. le diverse azioni nei gruppi di azioni possono avere formati diversi. 

    Ad esempio, per l'azione webhook: 

    ![opzione dello schema azione webhook](media/alerts-troubleshoot/webhook.png)

    Controllare se il formato specificato a livello di azione è quello previsto. Ad esempio, è possibile che sia stato sviluppato codice che risponde agli avvisi (webhook, funzione, app per la logica e così via), in cui è previsto un formato, ma in un secondo momento nell'azione l'utente o un altro utente ha specificato un formato diverso.  

    Controllare anche il formato del payload (JSON) per [gli avvisi del log attività](activity-log-alerts-webhook.md), per gli avvisi di [ricerca nei log](alerts-log-webhook.md) (sia Application Insights che log Analytics), per gli avvisi delle [metriche](alerts-metric-near-real-time.md#payload-schema), per lo [schema di avviso comune](alerts-common-schema-definitions.md)e per gli [avvisi delle metriche classiche](alerts-webhooks.md)deprecate.

 
1. **Avvisi del log attività: le informazioni sono disponibili nel log attività?** 

    Gli [avvisi del log attività](activity-log-alerts.md) sono avvisi basati sugli eventi scritti nel log attività di Azure, ad esempio eventi sulla creazione, l'aggiornamento o l'eliminazione di risorse di Azure, l'integrità dei servizi e gli eventi di integrità delle risorse o i risultati di Azure Advisor e criteri di Azure. Se è stato ricevuto un avviso basato sul log attività, ma alcuni campi necessari sono mancanti o non corretti, controllare innanzitutto gli eventi nel log attività. Se la risorsa di Azure non ha scritto i campi che si sta cercando nell'evento del log attività, questi campi non verranno inclusi nell'avviso corrispondente. 

## <a name="action-rule-is-not-working-as-expected"></a>La regola azione non funziona come previsto 

Se è possibile visualizzare un avviso attivato nel portale, ma una regola di azione correlata non funziona come previsto, attenersi alla procedura seguente: 

1. **La regola di azione è abilitata?** 

    Controllare la colonna stato regola azione per verificare che il ruolo azione correlato sia abilitato. 

    ![grafici](media/alerts-troubleshoot/action-rule-status.png) 

    Se non è abilitato, è possibile abilitare la regola di azione selezionandola e facendo clic su Abilita. 

1. **Si tratta di un avviso di integrità del servizio?** 

    Gli avvisi di integrità dei servizi (Monitor Service = "integrità del servizio") non sono interessati dalle regole di azione. 

1. **È stata eseguita la regola di azione per l'avviso?** 

    Controllare se la regola di azione ha elaborato l'avviso facendo clic sull'avviso generato nel portale ed esaminare la scheda cronologia.

    Di seguito è riportato un esempio di regola azione che elimina tutti i gruppi di azioni: 
 
     ![Cronologia eliminazione regola azione avviso](media/alerts-troubleshoot/history-action-rule.png)

    Di seguito è riportato un esempio di una regola di azione che aggiunge un altro gruppo di azione:

    ![Azione ripetuta in più gruppi di azioni](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **L'ambito e il filtro della regola di azione corrispondono all'avviso attivato?** 

    Se si ritiene che la regola di azione debba essere stata attivata ma che non sia stata attivata ma che non sia stata attivata, esaminare attentamente l'ambito della regola di azione e le condizioni di filtro rispetto alle proprietà dell'avviso attivato. 


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

    È necessario avere il [ruolo predefinito collaboratore monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)o le autorizzazioni specifiche relative alle regole di azione e agli avvisi.

1. **Si verificano i parametri della regola di azione?**  

    Controllare la [documentazione della regola di azione](alerts-action-rules.md)o la [regola azione PowerShell set-AzActionRule](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) comando. 


## <a name="next-steps"></a>Passaggi successivi
- Se si usa un avviso di log, vedere anche [risoluzione dei problemi relativi agli avvisi del log](alert-log-troubleshoot.md).
- Tornare alla [portale di Azure](https://portal.azure.com) per verificare se il problema è stato risolto con le linee guida precedenti 

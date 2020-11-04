---
title: Gestire i criteri di arresto automatico in Azure DevTest Labs e macchine virtuali di calcolo | Microsoft Docs
description: Informazioni su come impostare i criteri di arresto automatico per un Lab in modo che le macchine virtuali vengano arrestate automaticamente quando non sono in uso.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cd7974580ea30c9d0591c88380a4e626711bad1e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318980"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configurare l'arresto automatico per le macchine virtuali Lab e di calcolo in Azure DevTest Labs

Questo articolo illustra come configurare le impostazioni di arresto automatico per le macchine virtuali del Lab in DevTest Labs e nelle macchine virtuali di calcolo.

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configurare l'arresto automatico per le macchine virtuali del Lab (DevTest Labs)

Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. Questo articolo illustra come configurare i criteri di arresto automatico per un Lab.  Viene inoltre illustrato come configurare le impostazioni di arresto automatico per una macchina virtuale Lab. Per vedere come impostare ogni criterio del lab, vedere [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-autoshutdown-policy-for-a-lab"></a>Impostare i criteri di arresto automatico per un Lab

I proprietari del lab possono configurare una pianificazione di arresto per tutte le macchine virtuali nel lab. In questo modo, è possibile ridurre i costi dovuti all'esecuzione di macchine virtuali che non vengono usate (inattive). È possibile applicare un criterio di arresto in tutte le macchine virtuali del Lab in modo centralizzato e salvare gli utenti del Lab per la configurazione di una pianificazione per i singoli computer. Questa funzionalità consente di impostare i criteri per la pianificazione del Lab, da consentire agli utenti del Lab di avere il controllo completo sulla pianificazione di arresto della macchina virtuale senza controllare la chiusura della macchina virtuale. I proprietari del lab possono configurare questo criterio seguendo questa procedura:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **Criteri di arresto automatico** nella sezione **Pianificazioni** del menu a sinistra.
3. Selezionare una delle opzioni disponibili. Le sezioni seguenti forniscono ulteriori dettagli su queste opzioni:

    ![Opzioni dei criteri di arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

> [!IMPORTANT]
> Le modifiche ai criteri di arresto si applicano solo alle nuove macchine virtuali create nel Lab e non alle macchine virtuali già esistenti.

### <a name="configure-autoshutdown-settings"></a>Configurare le impostazioni di arresto automatico

I criteri di arresto automatico consentono di ridurre al minimo gli sprechi del Lab consentendo di specificare l'ora di arresto delle macchine virtuali del Lab.

Per visualizzare o modificare i criteri per un Lab, attenersi alla procedura seguente:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **arresto automatico** nella sezione **pianificazioni** del menu a sinistra.
3. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
     ![Dettagli arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
4. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.
5. Specificare **Sì** o **No** per l'opzione per inviare una notifica 30 minuti prima del tempo di arresto automatico specificato. Se si sceglie **Sì** , immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per ulteriori informazioni, vedere la sezione [notifiche](#notifications) .
6. Selezionare **Salva**.

    Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una macchina virtuale specifica, aprire il riquadro di gestione della macchina virtuale e modificare l'impostazione **Arresto automatico**.

> [!NOTE]
> Se si aggiorna la pianificazione di AutoShutdown per il Lab o una macchina virtuale lab specifica entro 30 minuti dall'ora pianificata corrente, l'ora di arresto aggiornata verrà applicata alla pianificazione del giorno successivo.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utente imposta una pianificazione e può rifiutare esplicitamente

Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione concede agli utenti del lab il controllo completo sulla pianificazione dell'arresto automatico delle macchine virtuali. Gli utenti del lab non notano alcuna modifica nella pagina della pianificazione dell'arresto automatico della macchina virtuale.

![Opzione per l'arresto automatico dei criteri-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utente imposta una pianificazione e non può rifiutare esplicitamente

Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override della pianificazione del lab. Tuttavia, non possono rifiutare esplicitamente i criteri di arresto automatico. Questa opzione assicura che ogni computer nel Lab sia sottoposto a una pianificazione di arresto automatico. Gli utenti del Lab possono aggiornare la pianificazione dell'arresto automatico delle macchine virtuali e configurare le notifiche di arresto.

![Opzione per l'arresto automatico del criterio-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utente non ha alcun controllo sulla pianificazione impostata dall'amministratore del lab

Se si imposta il lab su questo criterio, gli utenti del lab non possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione offre all'amministratore del lab il controllo completo sulla pianificazione per ogni computer del lab. Gli utenti del lab possono solo configurare le notifiche di arresto automatico per le macchine virtuali.

![Opzione per l'arresto automatico dei criteri-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configurare l'arresto automatico per le macchine virtuali di calcolo

1. Nella pagina **macchina virtuale** selezionare **arresto automatico** nel menu a sinistra nella sezione **operazioni** .
2. Nella pagina **arresto automatico** **selezionare attivato** per abilitare questo criterio e **disattivarlo** per disabilitarlo.
3. Se si Abilita questo criterio, specificare l' **ora** e il **fuso orario** in cui deve essere arrestata la macchina virtuale.
4. Scegliere **Sì** o **No** per l'opzione Invia una notifica 30 minuti prima del tempo di arresto automatico specificato. Se si sceglie **Sì** , immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per ulteriori informazioni, vedere la sezione [notifiche](#notifications) .
5. Selezionare **Salva**.

    ![Configurare l'arresto automatico per una macchina virtuale di calcolo](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visualizzare i log attività per gli aggiornamenti di arresto automatico

Quando si aggiorna l'impostazione di AutoShutdown, viene visualizzata l'attività registrata nel log attività per la macchina virtuale.

1. Nella [portale di Azure](https://portal.azure.com)passare al Home page per la macchina virtuale.
2. Selezionare **log attività** dal menu a sinistra.
3. Rimuovere la **risorsa: mycomputevm** dai filtri.
4. Verificare che sia visualizzata l'operazione **Aggiungi o modifica pianificazioni** nel log attività. Se non viene visualizzato, attendere qualche minuto e aggiornare il log attività.

    ![Voce del log attività](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
5. Selezionare l'operazione **Aggiungi o modifica pianificazioni** per visualizzare le informazioni seguenti nella pagina **Riepilogo** :

    - Nome operazione (Aggiungi o modifica pianificazioni)
    - Data e ora in cui è stata aggiornata l'impostazione di arresto automatico.
    - Indirizzo di posta elettronica dell'utente che ha aggiornato l'impostazione.

        ![Riepilogo delle voci del log attività](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
6. Passare alla scheda **cronologia modifiche** nella pagina **Aggiungi o modifica pianificazioni** . viene visualizzata la cronologia delle modifiche per l'impostazione. Nell'esempio seguente, il tempo di arresto è stato modificato da 7 a 6 PM il 10 aprile 2020 alle 15:18:47 EST. L'impostazione è stata disabilitata al 15:25:09 EST.

    ![Log attività-cronologia modifiche](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
7. Per visualizzare altri dettagli sull'operazione, passare alla scheda **JSON** nella pagina **Aggiungi o modifica pianificazioni** .

## <a name="notifications"></a>Notifiche

Una volta configurata l'AutoShutdown, le notifiche verranno inviate agli utenti del Lab 30 minuti prima che l'arresto automatico venga attivato se le macchine virtuali saranno interessate. Questa opzione offre agli utenti del Lab la possibilità di salvare il proprio lavoro prima dell'arresto. La notifica fornisce anche collegamenti per ogni macchina virtuale per le azioni seguenti, nel caso in cui qualcuno debba lavorare sulla propria VM.

- Ignora l'arresto automatico per questa ora
- Snooze l'AutoShutdown per un'ora
- Snooze l'arresto automatico per 2 ore

La notifica viene inviata all'URL del webhook se è stato specificato un webhook.  Se nelle impostazioni di AutoShutdown è stato specificato un indirizzo di posta elettronica, verrà inviato un messaggio di posta elettronica all'indirizzo di posta elettronica. I webhook consentono di compilare o configurare integrazioni che sottoscrivono eventi specifici. Quando viene attivato uno di questi eventi, DevTest Labs invierà un payload HTTP POST all'URL configurato del webhook. Per altre informazioni sulla risposta ai webhook, vedere [Panoramica di trigger e associazioni HTTP di funzioni di Azure](../azure-functions/functions-bindings-http-webhook.md) o aggiunta di [un trigger http per app per la logica di Azure](../connectors/connectors-native-http.md#add-an-http-trigger).

È consigliabile usare i webhook perché sono ampiamente supportati da diverse app, ad esempio app per la logica di Azure e slack.  I webhook consentono di implementare un metodo personalizzato per l'invio di notifiche. Ad esempio, questo articolo illustra come configurare la notifica di arresto automatico per inviare un messaggio di posta elettronica al proprietario della macchina virtuale usando app per la logica di Azure. Per prima cosa, è possibile eseguire i passaggi di base per abilitare la notifica di arresto automatico nel Lab.

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Creare un'app per la logica che riceve le notifiche tramite posta elettronica

[App](../logic-apps/logic-apps-overview.md) per la logica di Azure offre molti connettori che semplificano l'integrazione di un servizio con altri client, ad esempio Office 365 e Twitter. Al livello elevato, i passaggi per configurare un'app per la logica per la notifica tramite posta elettronica possono essere divisi in quattro fasi:

- Creare un'app per la logica.
- Configurare il modello predefinito.
- Integrazione con il client di posta elettronica
- Ottenere l'URL del webhook.

### <a name="create-a-logic-app"></a>Creare un'app per la logica

Per iniziare, creare un'app per la logica nella sottoscrizione di Azure attenendosi alla procedura seguente:

1. Selezionare **+ Crea una risorsa** nel menu a sinistra, selezionare **integrazione** e selezionare app per la **logica**.

    ![Menu nuovo app per la logica](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Nella pagina **app per la logica-crea** seguire questa procedura:
    1. Immettere un **nome** per l'app per la logica.
    2. Selezionare la **sottoscrizione** di Azure.
    3. Creare un nuovo **gruppo di risorse** o selezionarne uno esistente.
    4. Selezionare un **percorso** per l'app per la logica.

        ![Nuova app per la logica-impostazioni](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nelle **notifiche** selezionare **Vai alla risorsa** nella notifica.

    ![Vai alla risorsa](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selezionare **progettazione app** per la logica nella categoria **strumenti di distribuzione** .

    ![Selezione richiesta/risposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Nella pagina **richiesta-risposta http** selezionare **Usa questo modello**.

    ![Selezionare l'opzione Usa questo modello](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copiare il codice JSON seguente nella sezione **dello schema JSON del corpo della richiesta** :

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            },
            "vmUrl": {
                "type": "string"
            },
            "minutesUntilShutdown": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName",
            "vmUrl",
            "minutesUntilShutdown"
        ],
        "type": "object"
    }
    ```

    ![Screenshot che mostra lo "schema JSON del corpo della richiesta".](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selezionare **+ nuovo passaggio** nella finestra di progettazione e attenersi alla procedura seguente:
    1. Cercare **Office 365 Outlook-inviare un messaggio di posta elettronica**.
    2. Selezionare **Invia un messaggio di posta elettronica** da **azioni**.

        ![Opzione Invia messaggio di posta elettronica](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selezionare **Sign in (accedi** ) per accedere all'account di posta elettronica.
    4. Selezionare **il** campo e scegliere proprietario.
    5. Selezionare **oggetto** e immettere un oggetto della notifica di posta elettronica. Ad esempio: "Shutdown of machine vmName for Lab: labName".
    6. Selezionare **corpo** e definire il contenuto del corpo per la notifica tramite posta elettronica. Ad esempio: "vmName è pianificato per l'arresto in 15 minuti. Per ignorare questa chiusura, fare clic su: URL. Ritardo dell'arresto per un'ora: delayUrl60. Ritardo dell'arresto per 2 ore: delayUrl120. "

        ![Corpo della richiesta: Schema JSON](./media/devtest-lab-auto-shutdown/email-options.png)
8. Sulla barra degli strumenti selezionare **Salva**. A questo punto, è possibile copiare l' **URL http post**. Selezionare il pulsante copia per copiare l'URL negli Appunti.

    ![URL webhook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come impostare tutti i criteri, vedere [definire i criteri di Lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).

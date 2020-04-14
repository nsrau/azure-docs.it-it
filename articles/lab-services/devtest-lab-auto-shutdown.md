---
title: Gestire i criteri di arresto automatico in Azure DevTest Labs Documenti Microsoft
description: Informazioni su come impostare i criteri di arresto automatico per un lab in modo che le macchine virtuali vengano arrestate automaticamente quando non sono in uso.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 7cdc9f9a4503c786065b6d514f61fe17eae4ce5e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270911"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>Configurare l'arresto automatico per le macchine virtuali lab e di calcolo nei laboratori DevTest di AzureConfigure autoshutdown for lab and compute virtual machines in Azure DevTest Labs

In questo articolo viene illustrato come configurare le impostazioni di arresto automatico per le macchine virtuali lab in DevTest Labs e calcolare le macchine virtuali. 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a>Configurare l'arresto automatico per le macchine virtuali lab (DevTest Labs)Configure autoshutdown for lab VMs (DevTest Labs)
Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. Questo articolo illustra come configurare i criteri di arresto automatico per un account lab e configurare le impostazioni di arresto automatico per un lab nell'account lab. Per vedere come impostare ogni criterio del lab, vedere [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>Impostare i criteri di arresto automatico per un labSet auto shutdown policy for a lab
I proprietari del lab possono configurare una pianificazione di arresto per tutte le macchine virtuali nel lab. In questo modo, è possibile ridurre i costi dovuti all'esecuzione di macchine virtuali che non vengono usate (inattive). È possibile applicare un criterio di arresto a tutte le macchine virtuali del lab a livello centrale, ma anche evitare agli utenti di dover configurare una pianificazione per i propri computer. Questa funzionalità consente di impostare il criterio per la pianificazione del lab, offrendo agli utenti del lab il livello di controllo desiderato. I proprietari del lab possono configurare questo criterio seguendo questa procedura:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **Criteri di arresto automatico** nella sezione **Pianificazioni** del menu a sinistra.
3. Selezionare una delle opzioni disponibili. Le sezioni seguenti forniscono altri dettagli su queste opzioni: il criterio impostato si applica solo alle nuove macchine virtuali create nel lab e non alle macchine virtuali già esistenti. 

    ![Opzioni dei criteri di arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>Configurare le impostazioni di arresto automatico
Il criterio di arresto automatico consente di ridurre al minimo gli sprechi di laboratorio consentendo di specificare l'ora di arresto delle macchine virtuali del lab.

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi**e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Selezionare **Configuration and policies** (Configurazione e criteri).

    ![Riquadro Impostazioni criteri](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Nel riquadro **Configurazione e criteri** del lab selezionare **Arresto automatico** in **Pianificazioni**.
   
    ![Arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
7. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.
8. Specificare **Sì** o **No** per l'opzione per inviare una notifica 30 minuti prima dell'ora di arresto automatico specificata. Se si sceglie **Sì**, immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per altre informazioni, vedere la sezione [Notifiche.For](#notifications) more information, see the Notifications section. 
9. Selezionare **Salva**.

    Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una macchina virtuale specifica, aprire il riquadro di gestione della macchina virtuale e modificarne **l'impostazione di arresto automatico.**
    
> [!NOTE]
> Se si aggiorna la pianificazione di arresto automatico per il lab o una macchina virtuale lab specifica entro 30 minuti dall'ora pianificata corrente, l'ora di arresto aggiornato verrà applicata alla pianificazione del giorno successivo. 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utente imposta una pianificazione e può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione concede agli utenti del lab il controllo completo sulla pianificazione dell'arresto automatico delle macchine virtuali. Gli utenti del lab non notano alcuna modifica nella pagina della pianificazione dell'arresto automatico della macchina virtuale.

![Opzione criterio di arresto automatico - 1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utente imposta una pianificazione e non può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override della pianificazione del lab. Non possono tuttavia rifiutare esplicitamente il criterio di arresto automatico. Questa opzione assicura che ogni computer del lab sia soggetto a una pianificazione di arresto automatico. Gli utenti del lab possono aggiornare la pianificazione dell'arresto automatico delle macchine virtuali e configurare le notifiche di arresto.

![Opzione criterio di arresto automatico - 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utente non ha alcun controllo sulla pianificazione impostata dall'amministratore del lab
Se si imposta il lab su questo criterio, gli utenti del lab non possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione offre all'amministratore del lab il controllo completo sulla pianificazione per ogni computer del lab. Gli utenti del lab possono solo configurare le notifiche di arresto automatico per le macchine virtuali.

![Opzione criterio di arresto automatico - 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>Notifiche
Una volta che l'arresto automatico è stato impostato dal proprietario del lab, le notifiche verranno inviate agli utenti del lab 30 minuti prima dell'arresto automatico attivato se una delle loro macchine virtuali sarà interessata. Questa opzione offre agli utenti del lab la possibilità di salvare il proprio lavoro prima dell'arresto. La notifica fornisce anche collegamenti per ogni macchina virtuale per le azioni seguenti:The notification also provides links for each VM for the following actions:

- Salta l'arresto automatico per questo tempo
- Posticipare l'arresto automatico per un'ora o 2 ore, in modo che possano continuare a lavorare sulla macchina virtuale.

La notifica viene inviata tramite l'endpoint dell'hook Web configurato o un indirizzo di posta elettronica specificato dai proprietari dei lab nelle impostazioni di arresto automatico. I webhook consentono di creare o configurare integrazioni che sottoscrivono determinati eventi. Quando viene attivato uno di questi eventi, DevTest Labs invierà un payload HTTP POST all'URL configurato del webhook. Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

È consigliabile usare gli hook Web perché sono ampiamente supportati da varie app (ad esempio, Slack, App per la logica di Azure e così via) e consente di implementare il proprio modo per l'invio di notifiche. Ad esempio, questo articolo illustra come ottenere la notifica di arresto automatico dai messaggi di posta elettronica usando app per la logica di Azure.As an example, this article walks you through how to get autoshutdown notification from emails by using Azure Logic Apps. In primo luogo, esaminiamo rapidamente i passaggi di base per abilitare la notifica di arresto automatico nel lab.   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>Creare un'app per la logica che riceve notifiche tramite posta elettronicaCreate a logic app that receives email notifications
[App per](../logic-apps/logic-apps-overview.md) la logica di Azure offre molti connettori out-of-the-box che semplifica l'integrazione di un servizio con altri client, ad esempio Office 365 e Twitter. A livello generale, i passaggi per configurare un App per la logica per la notifica e-mail possono essere suddivisi in quattro fasi:At the high level, the steps to set up a Logic App for email notification can be divided into four phases: 

- Creare un'app per la logica. 
- Configurare il modello incorporato.
- Integrazione con il client di posta elettronica
- Ottenere l'URL Webhook.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
Per iniziare, creare un'app per la logica nella sottoscrizione di Azure usando i passaggi seguenti:To get started, create a logic app in your Azure subscription by using the following steps:

1. Nel **+ Create a resource** menu a sinistra, selezionare **Integrazione**e quindi **App per**la logica . 

    ![Nuovo menu dell'app per la logica](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Nella pagina App per la logica - Crea eseguire la procedura seguente:On the **Logic App - Create** page, follow these steps: 
    1. Immettere un **nome** per l'app per la logica.
    2. Selezionare la **sottoscrizione**di Azure.
    3. Creare un nuovo **gruppo di risorse** o selezionarne uno esistente. 
    4. Selezionare un **percorso** per l'app per la logica. 

        ![Nuova app per la logica - impostazioni](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. In **Notifiche**selezionare **Vai alla risorsa** nella notifica. 

    ![Vai alla risorsa](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selezionare **Progettazione app per la logica** nella categoria Strumenti di **distribuzione.**

    ![Seleziona richiesta/risposta HTTP](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Nella pagina **Richiesta-risposta HTTP** selezionare **Usa questo modello.** 

    ![Selezionare l'opzione Usa questo modello](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copiare il codice JSON seguente nella sezione **Schema JSON corpo richiesta:Copy** the following JSON into the Request Body JSON Schema section: 

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
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![Schema JSON del corpo della richiestaRequest Body JSON Schema](./media/devtest-lab-auto-shutdown/request-json.png)
7. Nella finestra di progettazione, **selezionare Nuovo passaggio** e attenersi alla seguente procedura:
    1. Cercare **Office 365 Outlook - Inviare un messaggio di posta elettronica**. 
    2. Selezionare **Invia un messaggio di posta elettronica** da **Azioni**. 
    
        ![Opzione Invia messaggio di posta elettronica](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selezionare **Accedi** per accedere all'account di posta elettronica. 
    4. Selezionare il campo **A** e scegliere proprietario.
    5. Selezionare **SUBJECT**e immettere un oggetto della notifica e-mail. Ad esempio: "Arresto del computer vmName per Lab: labName".
    6. Selezionare **BODY**e definire il contenuto del corpo per la notifica tramite posta elettronica. Ad esempio: "vmName è pianificato per l'arresto in 15 minuti. Salta questo arresto facendo clic su: URL. Arresto ritardato per un'ora: delayUrl60. Arresto ritardato per 2 ore: delayUrl120."

        ![Schema JSON del corpo della richiestaRequest Body JSON Schema](./media/devtest-lab-auto-shutdown/email-options.png)
1. Sulla barra degli strumenti selezionare **Salva**. A questo punto, è possibile copiare **l'URL HTTP POST**. Selezionare il pulsante Copia per copiare l'URL negli Appunti. 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>Configurare l'arresto automatico per le macchine virtuali di calcoloConfigure autoshutdown for compute VMs

1. Nella pagina **Macchina virtuale** selezionare **Arresto automatico** nel menu a sinistra nella sezione **Operazioni.** 
2. Nella pagina **Arresto automatico** selezionare **Attivato** per abilitare questo criterio e **Disattivato** per disabilitarlo.
3. Se si abilita questo criterio, specificare **l'ora** (e il **fuso orario)** in cui la macchina virtuale deve essere arrestata.
4. Specificare **Sì** o **No** per l'opzione per inviare una notifica 30 minuti prima dell'ora di arresto automatico specificata. Se si sceglie **Sì**, immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per altre informazioni, vedere la sezione [Notifiche.For](#notifications) more information, see the Notifications section. 
9. Selezionare **Salva**.

    ![Configurare l'arresto automatico per una macchina virtuale di calcoloConfigure autoshutdown for a compute VM](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>Visualizzare i log attività per gli aggiornamenti di arresto automaticoView activity logs for auto shutdown updates
Quando si aggiorna l'impostazione di arresto automatico, verrà visualizzata l'attività registrata nel log attività per la macchina virtuale. 

1. Nel [portale di Azure](https://portal.azure.com)passare alla home page della macchina virtuale.
2. Selezionare **Registro attività** dal menu a sinistra. 
3. Rimuovi **risorsa: mycomputevm** dai filtri.
3. Verificare che nel registro attività sia visualizzata l'operazione **Aggiungi o modifica pianificazioni.** Se non viene visualizzato, attendere qualche volta e aggiornare il log attività.

    ![Voce del log attività](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. Selezionare l'operazione **Aggiungi o modifica pianificazioni** per visualizzare le seguenti informazioni nella pagina **Riepilogo:**

    - Nome dell'operazione (Aggiungere o modificare pianificazioni)
    - Data e ora in cui è stata aggiornata l'impostazione di arresto automatico.
    - Indirizzo di posta elettronica dell'utente che ha aggiornato l'impostazione. 

        ![Riepilogo delle voci del log attività](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. Passare alla scheda **Cronologia modifiche** nella pagina Aggiungi o **modifica pianificazioni** viene visualizzata la cronologia delle modifiche per l'impostazione. Nell'esempio seguente, l'ora di arresto è stata modificata dalle 19:00 alle 18:00 del 10 aprile 2020 alle 15:18:47 EST. E, l'impostazione è stata disabilitata a 15:25:09 EST. 

    ![Registro attività - cronologia delle modifiche](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. Per visualizzare ulteriori dettagli sull'operazione, passare alla scheda **JSON** nella pagina **Aggiungi o modifica pianificazioni.**

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come impostare tutti i criteri, vedere [Definire i criteri lab in Azure DevTest Labs.](devtest-lab-set-lab-policy.md)


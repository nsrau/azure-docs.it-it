---
title: Gestire i criteri di arresto automatico in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come impostare criteri di arresto automatico per un lab in modo che le macchine virtuali vengono arrestate automaticamente quando non sono in uso.
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
ms.date: 05/17/2019
ms.author: spelluru
ms.openlocfilehash: 9adf8dd4a5a3c469ed130b29308a0d828aee40bf
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873991"
---
# <a name="manage-autoshutdown-policies-for-a-lab-in-azure-devtest-labs"></a>Gestire i criteri di arresto automatico per un lab in Azure DevTest Labs
Azure DevTest Labs consente di gestire i criteri (impostazioni) in ogni lab, in modo da controllare i costi e ridurre al minimo gli sprechi. Questo articolo illustra come configurare i criteri di arresto automatico per un account del lab e configurare le impostazioni di arresto automatico per un lab nell'account del lab. Per vedere come impostare ogni criterio del lab, vedere [Definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="set-auto-shutdown-policy-for-a-lab"></a>Impostare i criteri di arresto automatico per un lab
I proprietari del lab possono configurare una pianificazione di arresto per tutte le macchine virtuali nel lab. In questo modo, è possibile ridurre i costi dovuti all'esecuzione di macchine virtuali che non vengono usate (inattive). È possibile applicare un criterio di arresto a tutte le macchine virtuali del lab a livello centrale, ma anche evitare agli utenti di dover configurare una pianificazione per i propri computer. Questa funzionalità consente di impostare il criterio per la pianificazione del lab, offrendo agli utenti del lab il livello di controllo desiderato. I proprietari del lab possono configurare questo criterio seguendo questa procedura:

1. Nella home page del lab selezionare **Configurazione e criteri**.
2. Selezionare **Criteri di arresto automatico** nella sezione **Pianificazioni** del menu a sinistra.
3. Selezionare una delle opzioni disponibili. Le sezioni seguenti offrono altri dettagli su queste opzioni: I criteri di gruppo si applica solo alle nuove macchine virtuali create nel lab e non per le macchine virtuali già esistenti. 

    ![Opzioni dei criteri di arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

## <a name="configure-auto-shutdown-settings"></a>Configurare le impostazioni di arresto automatico
I criteri di arresto automatico della consentono di ridurre al minimo gli sprechi nel lab permettendo di specificare l'ora di arresto delle macchine virtuali del lab.

Per visualizzare e modificare i criteri per un lab, seguire questi passaggi:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco.
3. Nell'elenco dei lab selezionare il lab desiderato.   
4. Selezionare **Configuration and policies** (Configurazione e criteri).

    ![Riquadro Impostazioni criteri](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. Del lab **configurazione e criteri** riquadro, selezionare **arresto automatico** sotto **pianificazioni**.
   
    ![Arresto automatico della](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. Selezionare **Attivo** per abilitare i criteri e **Non attivo** per disabilitarli.
7. Se si abilita questo criterio, specificare l'ora e il fuso orario per l'arresto di tutte le macchine virtuali nel lab attuale.
8. Specificare **Yes** oppure **No** per l'opzione inviare una notifica 15 minuti prima l'ora di arresto automatico della specificato. Se si sceglie **Sì**, immettere un endpoint dell'URL webhook o un indirizzo di posta elettronica che specifica dove inviare o registrare la notifica. L'utente riceve la notifica e ha la possibilità di ritardare l'arresto. Per altre informazioni, vedere la [notifiche](#notifications) sezione. 
9. Selezionare **Salva**.

    Per impostazione predefinita, dopo l'abilitazione questi criteri verranno applicati a tutte le macchine virtuali nel lab corrente. Per rimuovere questa impostazione da una VM specifica, aprire il riquadro di gestione della macchina virtuale e modificare relativi **arresto automatico della** impostazione.

### <a name="user-sets-a-schedule-and-can-opt-out"></a>L'utente imposta una pianificazione e può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione concede agli utenti del lab il controllo completo sulla pianificazione dell'arresto automatico delle macchine virtuali. Gli utenti del lab non notano alcuna modifica nella pagina della pianificazione dell'arresto automatico della macchina virtuale.

![Opzioni per criteri - 1 l'arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>L'utente imposta una pianificazione e non può rifiutare esplicitamente
Se si imposta il lab su questo criterio, gli utenti del lab possono eseguire l'override della pianificazione del lab. Non possono tuttavia rifiutare esplicitamente il criterio di arresto automatico. Questa opzione assicura che ogni computer del lab sia soggetto a una pianificazione di arresto automatico. Gli utenti del lab possono aggiornare la pianificazione dell'arresto automatico delle macchine virtuali e configurare le notifiche di arresto.

![Opzioni per criteri - 2 l'arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>L'utente non ha alcun controllo sulla pianificazione impostata dall'amministratore del lab
Se si imposta il lab su questo criterio, gli utenti del lab non possono eseguire l'override o rifiutare esplicitamente la pianificazione del lab. Questa opzione offre all'amministratore del lab il controllo completo sulla pianificazione per ogni computer del lab. Gli utenti del lab possono solo configurare le notifiche di arresto automatico per le macchine virtuali.

![Opzioni per criteri - 3 l'arresto automatico](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="notifications"></a>Notifiche
Dopo l'arresto automatico della impostato dal proprietario del lab, verranno inviate notifiche per gli utenti del lab 15 minuti prima di arresto automatico della attivata se tutte le macchine virtuali saranno interessate. Questa opzione consente a utenti del lab possibilità di salvare il lavoro prima dell'arresto. La notifica vengono inoltre forniti collegamenti per ogni macchina virtuale per le azioni seguenti:

- Ignorare l'arresto automatico per questo periodo di tempo
- Posponi l'arresto automatico per un'ora o due ore, in modo che è possibile continuare a lavorare nella macchina virtuale.

Notifica viene inviata tramite l'endpoint di hook web configurate o specificare un indirizzo di posta elettronica dai proprietari di lab nelle impostazioni di arresto automatico della. I Webhook consentono di creare o configurare le integrazioni per cui eseguire la sottoscrizione a determinati eventi. Quando viene attivato uno di questi eventi, DevTest Labs invierà un payload di richiesta HTTP POST all'URL configurato del webhook. Per altre informazioni sui webhook, vedere [Creare un webhook o una funzione API di Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

È consigliabile usare gli hook web perché è ampiamente supportati da app diverse (ad esempio, Slack, App per la logica di Azure e così via.) e consente di implementare in altro modo per l'invio di notifiche. Ad esempio, questo articolo illustra come ottenere l'arresto automatico della notifica da messaggi di posta elettronica usando le App per la logica di Azure. In primo luogo, è possibile scorrere rapidamente i passaggi di base per abilitare la notifica di arresto automatico nell'ambiente lab.   

## <a name="create-a-logic-app-that-receives-email-notifications"></a>Creare un'app per la logica che riceve notifiche di posta elettronica
[Le App per la logica di Azure](../logic-apps/logic-apps-overview.md) fornisce numerosi connettori out-of-the-box che rende più semplice per integrano un servizio con altri client, come Office 365 e twitter. A livello generale, i passaggi per configurare un'App per la logica per la notifica tramite posta elettronica possono essere suddivisi in quattro fasi: 

- Creare un'app per la logica. 
- Configurare il modello predefinito.
- Integrare con client di posta elettronica
- Ottenere l'URL del Webhook.

### <a name="create-a-logic-app"></a>Creare un'app per la logica
Per iniziare, creare un'app per la logica nella sottoscrizione di Azure usando la procedura seguente:

1. Selezionare **+ crea una risorsa** nel menu a sinistra, selezionare **Integration**e selezionare **App per la logica**. 

    ![Nuovo menu dell'app per la logica](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. Nel **App per la logica - creare** pagina, seguire questa procedura: 
    1. Immettere un **nome** per l'app per la logica.
    2. Selezionare la **sottoscrizione**di Azure.
    3. Creare un nuovo **gruppo di risorse** o selezionarne uno esistente. 
    4. Selezionare una **posizione** per l'app per la logica. 

        ![Nuova app per la logica - impostazioni](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. Nel **notifiche**, selezionare **Vai alla risorsa** sulla notifica. 

    ![Vai alla risorsa](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. Selezionare **progettazione di app per la logica** sotto **strumenti di distribuzione** categoria.

    ![Selezionare HTTP richiesta/risposta](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. Nel **richiesta-risposta HTTP** pagina, selezionare **usare questo modello**. 

    ![Selezionare Usa questa opzione del modello](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. Copiare il codice JSON seguente nella **dello Schema JSON del corpo richiesta** sezione: 

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
    
    ![Schema JSON del corpo della richiesta](./media/devtest-lab-auto-shutdown/request-json.png)
7. Selezionare **+ nuovo passaggio** nella finestra di progettazione e seguire questa procedura:
    1. Cercare **Office 365 Outlook - invia un messaggio di posta elettronica**. 
    2. Selezionare **inviare un messaggio di posta elettronica** dalla **azioni**. 
    
        ![Invia messaggio di posta elettronica opzione](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. Selezionare **Accedi** per accedere all'account di posta elettronica. 
    4. Selezionare **TO** campo e scegliere proprietario.
    5. Selezionare **soggetto**e un oggetto della notifica tramite posta elettronica di input. Ad esempio: "Arresto della macchina vmName per Lab: labName."
    6. Selezionare **corpo**e definire il contenuto del corpo per la notifica tramite posta elettronica. Ad esempio: "vmName è pianificata per spegnersi in 15 minuti. Ignorare questo spegnimento facendo clic: URL. Arresto di ritardo per un'ora: delayUrl60. Arresto di ritardo per 2 ore: delayUrl120. "

        ![Schema JSON del corpo della richiesta](./media/devtest-lab-auto-shutdown/email-options.png)
1. Sulla barra degli strumenti selezionare **Salva**. A questo punto, è possibile copiare il **URL POST HTTP**. Selezionare il pulsante Copia per copiare l'URL negli Appunti. 

    ![URL del WebHook](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come impostare tutti i criteri, vedere [definire i criteri di lab in Azure DevTest Labs](devtest-lab-set-lab-policy.md).

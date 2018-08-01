---
title: Scenario di budget per la gestione dei costi e la fatturazione di Azure | Microsoft Docs
description: Informazioni su come usare Automazione di Azure per arrestare le macchine virtuali in base a specifiche soglie di budget.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 7/25/2018
ms.author: erikre
ms.openlocfilehash: 19d8fbf82410136fd6a50a2088dd9b774dd6cb27
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259212"
---
# <a name="manage-costs-with-azure-budgets"></a>Gestire i costi con i budget di Azure

Il controllo dei costi è un componente essenziale per ottimizzare il valore del proprio investimento nel cloud. Esistono diversi scenari in cui la visibilità dei costi, la creazione di report e l'orchestrazione basata sui costi hanno un'importanza fondamentale per la continuità delle attività aziendali. Il set di [API di Gestione costi di Azure](https://docs.microsoft.com/rest/api/consumption/) offre supporto per ciascuno di questi scenari. Tramite queste API, gli utenti possono visualizzare i dati sull'utilizzo in modo da esaminare i costi in dettaglio a livello di singola istanza.

Come parte dell'attività di controllo dei costi vengono comunemente usati i budget. In Azure è possibile definire l'ambito dei budget, ad esempio limitandone la visualizzazione in base alla sottoscrizione, ai gruppi di risorse o a una raccolta di risorse. Oltre a usare l'API dei budget per ricevere una notifica tramite posta elettronica quando viene raggiunta una soglia, è possibile usare i [gruppi di azioni di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) per attivare un set orchestrato di azioni in risposta a un evento di budget.

Un comune scenario d'uso dei budget per un cliente con un carico di lavoro non critico può verificarsi quando il cliente vuole gestire i costi rispetto a un budget e anche ottenere una previsione dei costi in base all'analisi della fattura mensile. Questo scenario richiede un'orchestrazione basata sui costi delle risorse che fanno parte dell'ambiente di Azure. Nello scenario è definito un budget mensile di € 1000 per la sottoscrizione e sono impostate soglie di notifica per attivare alcune orchestrazioni. Lo scenario prevede una soglia iniziale pari all'80% dei costi, in corrispondenza della quale vengono arrestate tutte le macchine virtuali nel gruppo di risorse **Facoltativo**, e una soglia successiva pari al 100% dei costi, oltre la quale verranno arrestate tutte le istanze delle macchine virtuali.
Per configurare questo scenario, si completeranno le azioni elencate di seguito eseguendo i passaggi riportati nelle singole sezioni dell'esercitazione. 

Le azioni descritte in questa esercitazione consentono di:

- Creare un runbook di Automazione di Azure per arrestare le macchine virtuali usando webhook.
- Creare un'app per la logica di Azure da attivare in base al valore soglia del budget e chiamare il runbook con i parametri corretti.
- Creare un gruppo di azioni di Monitoraggio di Azure che verrà configurato per attivare l'app per la logica di Azure quando viene raggiunta la soglia di budget.
- Creare il budget di Azure con le soglie desiderate e collegarlo al gruppo di azioni.

## <a name="create-an-azure-automation-runbook"></a>Creare un runbook di Automazione di Azure

[Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) è un servizio che consente di creare script per eseguire la maggior parte delle attività di gestione delle risorse in base a una pianificazione o su richiesta. In questo scenario si creerà un [runbook di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types) che verrà usato per arrestare le macchine virtuali. Per creare lo scenario, si userà il runbook grafico [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) disponibile nella [raccolta di runbook](https://docs.microsoft.com/azure/automation/automation-runbook-gallery). Importando questo runbook nell'account Azure e pubblicandolo, si potranno arrestare le macchine virtuali quando viene raggiunta una determinata soglia di budget. 

### <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

1.  Accedere al [portale di Azure](https://portal.azure.com/) con le credenziali dell'account Azure.
2.  Fare clic sul pulsante **Crea una risorsa** nell'angolo in alto a sinistra della schermata di Azure.
3.  Selezionare **Strumenti di gestione** > **Automazione**.
    > [!NOTE]
    > Se non si ha un account Azure, è possibile [creare un account gratuito](https://azure.microsoft.com/free/).
4.  Immettere le informazioni dell'account. Per **Crea un account RunAs di Azure** scegliere **Sì** per abilitare automaticamente le impostazioni necessarie per semplificare l'autenticazione ad Azure.
5.  Al termine fare clic su **Crea**, per avviare la distribuzione dell'account di Automazione.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importare il runbook Stop Azure V2 VMs

Usando un [runbook di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types), importare il runbook grafico [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) dalla raccolta.

1.  Accedere al [portale di Azure](https://portal.azure.com/) con le credenziali dell'account Azure.
2.  Aprire l'account di Automazione selezionando **Tutti i servizi** > **Account di automazione**. Selezionare quindi l'account di Automazione.
3.  Fare clic su **Raccolta di runbook** dalla sezione **Automazione processi**.
4.  Impostare **Origine raccolta** su **Script Center** e selezionare **OK**.
5.  Individuare e selezionare l'elemento della raccolta [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) nel portale di Azure.
6.  Fare clic sul pulsante **Importa** per visualizzare il pannello **Importazione** e selezionare **OK**. Verrà visualizzato il pannello di panoramica del runbook.
7.  Al termine del processo di importazione del runbook, selezionare **Modifica** per visualizzare l'editor del runbook grafico e l'opzione di pubblicazione.
    
    ![Azure - Modifica runbook grafico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Selezionare **Pubblica** per pubblicare il runbook e quindi **Sì** quando richiesto. Quando si pubblica un runbook, si sovrascrive l'eventuale versione pubblicata esistente con la versione bozza. In questo caso non esiste ancora una versione pubblicata perché il runbook è stato appena creato. 

    Per altre informazioni sulla pubblicazione di un runbook, vedere [Creare un runbook grafico](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Creare webhook per il runbook

Usando il runbook grafico [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) si creeranno due webhook per avviare il runbook in Automazione di Azure tramite una singola richiesta HTTP. Il primo webhook richiamerà il runbook in corrispondenza di una soglia di budget dell'80%, con il nome del gruppo di risorse come parametro, in modo da arrestare le macchine virtuali facoltative. Il secondo webhook richiamerà il runbook senza parametri (alla soglia del 100%), arrestando così tutte le istanze di macchine virtuali rimanenti.

1.  Dalla pagina **Runbook** nel [portale di Azure](https://portal.azure.com/) fare clic sul runbook **StopAzureV2Vm** per visualizzare il relativo pannello di panoramica. 
2.  Fare clic su **Webhook** nella parte superiore della pagina per aprire il pannello **Aggiungi webhook**.
3.  Fare clic su **Creare un nuovo webhook** per aprire il pannello **Crea un nuovo webhook**.
4.  Impostare il campo **Nome** del webhook su **Facoltativo**. La proprietà **Abilitato** deve essere impostata su **Sì**. Il valore di **Scadenza** non deve essere modificato. Per altre informazioni sulle proprietà dei webhook, vedere [Informazioni dettagliate sui webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5.  Accanto al valore dell'URL fare clic sull'icona di copia per copiare l'URL del webhook. 
    > [!IMPORTANT]
    > Salvare l'URL del webhook denominato **Facoltativo** in un luogo sicuro. Questo valore verrà usato più avanti nell'esercitazione. Per motivi di sicurezza, dopo la creazione di un webhook, non è più possibile visualizzare o recuperare l'URL.
6.  Fare clic su **OK** per creare il nuovo webhook.
7.  Fare clic su **Configura i parametri e le impostazioni di esecuzione** per visualizzare i valori dei parametri relativi al runbook. 
    > [!NOTE]
    > Se il runbook contiene parametri obbligatori, non sarà possibile creare il webhook a meno che non vengano forniti i valori.
8.  Fare clic su **OK** per accettare i valori dei parametri del webhook.
9.  Fare clic su **Create** per creare il webhook.
10. Ripetere i passaggi precedenti per creare un secondo webhook denominato **Completo**. 
    > [!IMPORTANT]
    > Assicurarsi di salvare entrambi gli URL dei webhook poiché sarà necessario usarli più avanti in questa esercitazione. Per motivi di sicurezza, dopo la creazione di un webhook, non è più possibile visualizzare o recuperare l'URL.

Si avranno ora due webhook configurati, ciascuno disponibile tramite l'URL salvato. 

![Webhook - Facoltativo e Completo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

A questo punto la configurazione di Automazione di Azure è completata. È possibile testare i webhook con un semplice test Postman per verificarne il funzionamento. Il passaggio successivo consiste nel creare l'app per la logica per l'orchestrazione.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Creare un'app per la logica di Azure per l'orchestrazione

Il servizio App per la logica consente di creare, pianificare e automatizzare i processi sotto forma di flussi di lavoro in modo da integrare app, dati, sistemi e servizi in aziende o organizzazioni. In questo scenario si creerà un'[app per la logica](https://docs.microsoft.com/azure/logic-apps/) che non si limiterà a chiamare semplicemente il webhook di automazione creato. 

È possibile definire budget per attivare una notifica quando viene raggiunta una soglia specificata. Impostando più valori soglia per la ricezione di notifiche, l'app per la logica consentirà di eseguire azioni diverse in base alla soglia raggiunta. Nello scenario configurato in questo esempio si riceveranno due notifiche, la prima quando viene raggiunto l'80% del budget e la seconda quando viene toccata la soglia del 100%. L'app per la logica verrà usata per arrestare tutte le macchine virtuali nel gruppo di risorse. Prima verrà raggiunta la soglia denominata **Facoltativo** in corrispondenza dell'80% del budget e successivamente verrà raggiunta la seconda soglia, in cui tutte le macchine virtuali nella sottoscrizione verranno arrestate.

Le app per la logica consentono di definire uno schema di esempio per il trigger HTTP, ma richiedono che venga impostata l'intestazione **Content-Type**. Poiché il gruppo di azioni non ha intestazioni personalizzate per il webhook, è necessario analizzare il payload in un passaggio separato. Si userà quindi l'azione **Analizza** specificando un payload di esempio.

### <a name="create-the-logic-app"></a>Creare l'app per la logica

L'app per la logica eseguirà diverse azioni. Nell'elenco seguente presenta una sintesi delle azioni che verranno eseguite dall'app per la logica:
- Riconoscere quando viene ricevuta una richiesta HTTP.
- Analizzare i dati JSON passati per determinare il valore soglia raggiunto.
- Usare un'istruzione condizionale per controllare se l'importo soglia ha raggiunto o superato l'80% dell'intervallo di budget, ma non è maggiore o uguale al 100%.
    - Se la soglia è stata raggiunta, inviare una richiesta HTTP POST con il webhook denominato **Facoltativo**. Questa azione arresterà le macchine virtuali nel gruppo "Facoltativo".
- Usare un'istruzione condizionale per controllare se l'importo soglia ha raggiunto o superato il 100% del valore del budget.
    - Se la soglia è stata raggiunta, inviare una richiesta HTTP POST con il webhook denominato **Completo**. Questa azione arresterà tutte le macchine virtuali rimanenti.

Per creare l'app per la logica che eseguirà i passaggi appena descritti, seguire questa procedura:

1.  Nel [portale di Azure](https://portal.azure.com/) selezionare **Crea una risorsa** > **Integrazione** > **App per la logica**.
    
    ![Azure - Selezionare la risorsa App per la logica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Nel pannello **Crea app per la logica** specificare i dettagli necessari per creare l'app, selezionare **Aggiungi al dashboard** e fare clic su **Crea**. 
    
    ![Azure - Creare un'app per la logica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Dopo che Azure ha distribuito l'app per la logica, viene visualizzata la schermata **Progettazione app per la logica** contenente un pannello con un video introduttivo e i trigger di uso comune. 

### <a name="add-a-trigger"></a>Aggiungere un trigger

Ogni app per la logica deve essere avviata con un trigger, che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica che si avvia ed esegue il flusso di lavoro. Le azioni corrispondono a tutti i passaggi eseguiti dopo l'attivazione del trigger. 

1.  Nell'area **Modelli** di **Progettazione app per la logica** scegliere **App per la logica vuota**.
2.  Aggiungere un [trigger](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) specificando "richiesta http" nella casella di ricerca di **Progettazione app per la logica** per trovare e selezionare il trigger denominato **Richiesta - Alla ricezione di una richiesta HTTP**.
    
    ![Azure - App per la logica - Trigger HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png) 
3.  Selezionare **Nuovo passaggio** > **Aggiungi un'azione**. 
    
    ![Azure - Nuovo passaggio - Aggiungere un'azione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png) 
4.  Nella casella di ricerca di **Progettazione app per la logica** specificare "analizza JSON" per trovare e selezionare l'[azione](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) **Operazioni dati - Analizza JSON**. 
    
    ![Azure - App per la logica - Aggiungere l'azione Analizza JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png) 
5.  Immettere "Payload" come **Contenuto** per il payload di Analizza JSON.
6.  Selezionare l'opzione **Usare il payload di esempio per generare lo schema** nella casella **Analizza JSON**.
    
    ![Azure - App per la logica - Usare i dati JSON di esempio per generare lo schema](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png) 
7.  Incollare il payload JSON di esempio seguente nella casella di testo: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
    
    Verrà visualizzata la casella di testo come indicato di seguito:
        
    ![Azure - App per la logica - Payload JSON di esempio](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png) 
8.  Fare clic su **Fine**.

### <a name="add-the-first-conditional-action"></a>Aggiungere la prima azione condizionale

Usare un'istruzione condizionale per controllare se l'importo soglia ha raggiunto o superato l'80% dell'intervallo di budget, ma non è maggiore o uguale al 100%. Se la soglia è stata raggiunta, inviare una richiesta HTTP POST con il webhook denominato **Facoltativo**. Questa azione arresterà le macchine virtuali nel gruppo **Facoltativo**.

1.  Selezionare **Nuovo passaggio** > **Aggiungi una condizione**.
    
    ![Azure - App per la logica - Aggiungere una condizione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png) 
2.  Nella finestra **Condizione** fare clic sulla casella di testo contenente **Scegliere un valore** per visualizzare un elenco di valori disponibili.
    
    ![Azure - App per la logica - Finestra Condizione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png) 
        
3.  Fare clic su **Espressione** nella parte superiore dell'elenco e immettere l'espressione seguente nell'editor delle espressioni: `float()`
    
    ![Azure - App per la logica - Espressione float](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png) 
        
4.  Selezionare **Contenuto dinamico**, posizionare il cursore all'interno delle parentesi () e selezionare **NotificationThresholdAmount** dall'elenco per completare l'espressione. 
    
    Verrà generata l'espressione seguente:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
    
5.  Selezionare **OK** per impostare l'espressione. 
6.  Selezionare **è maggiore o uguale a** dalla casella a discesa della finestra **Condizione**.
7.  Nella casella **Scegliere un valore** della condizione immettere `.8`.
    
    ![Azure - App per la logica - Espressione float con un valore](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png) 
        
8.  Fare clic su **Aggiungi** > **Aggiungi riga** all'interno della finestra Condizione per aggiungere un altro elemento della condizione.
9.  Nella finestra **Condizione** fare clic sulla casella di testo contenente **Scegliere un valore**.
10. Fare clic su **Espressione** nella parte superiore dell'elenco e immettere l'espressione seguente nell'editor delle espressioni: `float()`
11. Selezionare **Contenuto dinamico**, posizionare il cursore all'interno delle parentesi () e selezionare **NotificationThresholdAmount** dall'elenco per completare l'espressione. 
12. Selezionare **OK** per impostare l'espressione. 
13. Selezionare **è minore di** dalla casella a discesa della finestra **Condizione**.
14. Nella casella **Scegliere un valore** della condizione immettere `1`.
    
    ![Azure - App per la logica - Espressione float con un valore](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png) 
        
15. Nella finestra **È true** selezionare **Aggiungi un'azione**. Si aggiungerà un'azione HTTP POST che arresterà le macchine virtuali facoltative.
    
    ![Azure - App per la logica - Aggiungere un'azione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png) 
        
16. Immettere **HTTP** per cercare l'azione HTTP e selezionare l'azione **HTTP - HTTP**.
    
    ![Azure - App per la logica - Aggiungere un'azione HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png) 
        
17. Selezionare **Post** come valore di **Metodo**.
18. Immettere l'URL del webhook **Facoltativo** creato in precedenza in questa esercitazione come valore di **Uri**.
    
    ![Azure - App per la logica - URI dell'azione HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png) 
        
19. Selezionare **Aggiungi un'azione** nella finestra **È true**. Si aggiungerà un'azione che invierà un messaggio di posta elettronica per informare il destinatario che sono state arrestate le macchine virtuali facoltative.
20. Cercare "invia messaggio di posta elettronica" e selezionare l'azione *Invia messaggio di posta elettronica* in base al servizio di posta elettronica usato.
        
    ![Azure - App per la logica - Azione Invia messaggio di posta elettronica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png) 
    
    Per gli account Microsoft personali, selezionare **Outlook.com**. Per gli account aziendali o dell'istituto di istruzione di Azure, selezionare **Office 365 Outlook**. Se non si ha già una connessione, viene chiesto di accedere al proprio account di posta elettronica. App per la logica crea una connessione all'account di posta elettronica.
        
    È necessario consentire all'app per la logica di accedere alle informazioni della posta elettronica.
    
    ![Azure - App per la logica - Notifica di accesso](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png) 
        
21. Impostare i campi **A**, **Oggetto** e **Corpo** per il messaggio di posta elettronica che verrà inviato per comunicare al destinatario l'arresto delle macchine virtuali facoltative. Usare il contenuto dinamico **BudgetName** e **NotificationThresholdAmount** per inserire i dati nei campi relativi all'oggetto e al corpo.
    
    ![Azure - App per la logica - Dettagli del messaggio di posta elettronica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png) 
 
### <a name="add-the-second-conditional-action"></a>Aggiungere la seconda azione condizionale

Usare un'istruzione condizionale per controllare se l'importo soglia ha raggiunto o superato il 100% del valore del budget. Se la soglia è stata raggiunta, inviare una richiesta HTTP POST con il webhook denominato **Completo**. Questa azione arresterà tutte le macchine virtuali rimanenti.

1.  Selezionare **Nuovo passaggio** > **Aggiungi una condizione**.
    
    ![Azure - App per la logica - Aggiungere un'azione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png) 
        
2.  Nella finestra **Condizione** fare clic sulla casella di testo contenente **Scegliere un valore** per visualizzare un elenco di valori disponibili.
3.  Fare clic su **Espressione** nella parte superiore dell'elenco e immettere l'espressione seguente nell'editor delle espressioni: `float()`
4.  Selezionare **Contenuto dinamico**, posizionare il cursore all'interno delle parentesi () e selezionare **NotificationThresholdAmount** dall'elenco per completare l'espressione. 
    
    Verrà generata l'espressione seguente:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
        
5.  Selezionare **OK** per impostare l'espressione. 
6.  Selezionare **è maggiore o uguale a** dalla casella a discesa della finestra **Condizione**.
7.  Nella casella **Scegliere un valore** della condizione immettere `1`.
    
    ![Azure - App per la logica - Impostare il valore della condizione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png) 
        
8.  Nella finestra **È true** selezionare **Aggiungi un'azione**. Si aggiungerà un'azione HTTP POST che arresterà tutte le macchine virtuali rimanenti.
    
    ![Azure - App per la logica - Aggiungere un'azione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png) 
    
9.  Immettere **HTTP** per cercare l'azione HTTP e selezionare l'azione **HTTP - HTTP**.
10. Selezionare **Post** come valore di **Metodo**.
11. Immettere l'URL del webhook **Completo** creato in precedenza in questa esercitazione come valore di **Uri**.
    
    ![Azure - App per la logica - Aggiungere un'azione](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png) 
        
12. Selezionare **Aggiungi un'azione** nella finestra **È true**. Si aggiungerà un'azione che invierà un messaggio di posta elettronica per informare il destinatario che sono state arrestate le macchine virtuali rimanenti.
13. Cercare "invia messaggio di posta elettronica" e selezionare l'azione *Invia messaggio di posta elettronica* in base al servizio di posta elettronica usato.
14. Impostare i campi **A**, **Oggetto** e **Corpo** per il messaggio di posta elettronica che verrà inviato per comunicare al destinatario l'arresto delle macchine virtuali facoltative. Usare il contenuto dinamico **BudgetName** e **NotificationThresholdAmount** per inserire i dati nei campi relativi all'oggetto e al corpo.
    
    ![Azure - App per la logica - Dettagli del messaggio di posta elettronica](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png) 
        
15. Fare clic su **Salva** nella parte superiore del pannello **Progettazione app per la logica**.

### <a name="logic-app-summary"></a>Riepilogo dell'app per la logica

Al termine delle operazioni, l'app per la logica avrà un aspetto simile al seguente: Negli scenari più semplici, in cui non è necessaria un'orchestrazione basata su soglie, è possibile chiamare direttamente lo script di automazione da **Monitoraggio** e ignorare il passaggio di **App per la logica**.

   ![Azure - App per la logica - Visualizzazione completa](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png) 
 
Quando è stata salvata l'app per la logica, è stato generato un URL che sarà possibile chiamare. Questo URL verrà usato nella sezione successiva di questa esercitazione. 

## <a name="create-an-azure-monitor-action-group"></a>Creare un gruppo di azioni di Monitoraggio di Azure

Un gruppo di azioni è una raccolta di preferenze di notifica definite dall'utente. Quando viene attivato un avviso, uno specifico gruppo di azioni può ricevere l'avviso tramite una notifica. Un avviso di Azure genera una notifica in modo proattivo in base a condizioni specifiche e offre la possibilità di eseguire un'azione. Un avviso può usare dati provenienti da più origini, tra cui metriche e log.

I gruppi di azioni sono l'unico endpoint che si integrerà con il budget. È possibile configurare le notifiche in vari canali, ma questo scenario sarà incentrato sull'app per la logica creata in precedenza in questa esercitazione. 

### <a name="create-an-action-group-in-azure-monitor"></a>Creare un gruppo di azioni in Monitoraggio di Azure

Quando si crea il gruppo di azioni, è necessario definire un puntatore all'app per la logica creata in precedenza in questa esercitazione. 

1.  Se non si è già eseguito l'accesso al [portale di Azure](https://portal.azure.com/), accedere e selezionare **Tutti i servizi** > **Monitoraggio**. 
2.  Selezionare **Gruppi di azioni** dalla sezione **Impostazioni**.
3.  Selezionare **Aggiungi gruppo di azioni** dal pannello **Gruppi di azioni**.
4.  Aggiungere e verificare quanto segue:
    - Nome gruppo di azione
    - Nome breve
    - Sottoscrizione
    - Gruppo di risorse
    
    ![Azure - App per la logica - Aggiungere un gruppo di azioni](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png) 
        
5.  All'interno del pannello **Aggiungi gruppo di azioni** aggiungere un'azione di tipo App per la logica. Assegnare il nome **Budget-BudgetLA** all'azione. Nel riquadro **App per la logica** selezionare i valori per **Sottoscrizione** e **Gruppo di risorse**. Selezionare quindi l'**app per la logica** creata in precedenza in questa esercitazione.
6.  Fare clic su **OK** per impostare l'app per la logica. Selezionare quindi **OK** nel riquadro **Aggiungi gruppo di azioni** per creare il gruppo di azioni.
    
A questo punto sono stati creati tutti i componenti di supporto necessari per orchestrare il budget in modo efficiente. È ora sufficiente creare il budget e configurarlo per l'uso del gruppo di azioni creato.

## <a name="create-the-azure-budget"></a>Creare il budget di Azure 

Per i budget non è attualmente disponibile un'esperienza del portale in Azure. È tuttavia possibile chiamare le API REST o i cmdlet di PowerShell oppure usare l'interfaccia della riga di comando. Nella procedura seguente si userà l'API REST. Prima di chiamare l'API REST, è necessario un token di autorizzazione. Per creare un token di autorizzazione, è possibile usare il progetto [ARMClient](https://github.com/projectkudu/ARMClient). **ARMClient** consente di eseguire l'autenticazione ad Azure Resource Manager e ottenere un token per chiamare le API. 

### <a name="create-an-authentication-token"></a>Ottenere un token di autenticazione

1.  Passare al progetto [ARMClient](https://github.com/projectkudu/ARMClient) in GitHub.
2.  Clonare il repository per ottenere una copia locale.
3.  Aprire il progetto in Visual Studio e compilarlo.
4.  Al termine della compilazione, il file eseguibile dovrebbe trovarsi nella cartella *\bin\debug*.
5.  Eseguire ARMClient. Aprire un prompt dei comandi e passare alla cartella *\bin\debug* dalla radice del progetto.
6.  Per eseguire l'accesso e l'autenticazione, immettere il comando seguente al prompt dei comandi:<br>
    `ARMClient login prod`
7.  Copiare il **GUID della sottoscrizione** dall'output.
8.  Per copiare un token di autorizzazione negli Appunti, immettere il comando seguente al prompt dei comandi, ma assicurarsi di usare l'ID della sottoscrizione copiato nel passaggio precedente: <br>
    `ARMClient token <subscription GUID from previous step>`
    
    Al termine di questo passaggio, verrà visualizzato un messaggio simile al seguente:<br>
    **Copia del token negli Appunti completata.**
9.  Salvare il token da usare per i passaggi nella sezione successiva di questa esercitazione.

### <a name="create-the-budget"></a>Creare il budget

In questa procedura si configurerà **Postman** per creare un budget chiamando le API REST per l'utilizzo di Azure. Postman è un ambiente per lo sviluppo di API. Si importeranno i file dell'ambiente e della raccolta in Postman. La raccolta contiene definizioni raggruppate delle richieste HTTP che chiamano le API REST per l'utilizzo di Azure. Il file dell'ambiente contiene le variabili usate dalla raccolta.

1.  Scaricare e aprire il [client REST di Postman](https://www.getpostman.com/) per eseguire le API REST.
2.  In Postman creare una nuova richiesta.
    
    ![Postman - Creare una nuova richiesta](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png) 
        
3.  Salvare la nuova richiesta come raccolta, in modo che non contenga informazioni.
    
    ![Postman - Salvare la nuova richiesta](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png) 
        
4.  Modificare la richiesta sostituendo l'azione `Get` con `Put`.
5.  Modificare l'URL seguente sostituendo `{subscriptionId}` con l'**ID sottoscrizione** usato nella sezione precedente di questa esercitazione. Modificare inoltre l'URL in modo da includere "SampleBudget" come valore per `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Selezionare la scheda **Headers** (Intestazioni) in Postman.
7.  In **Key** (Chiave) aggiungere una nuova chiave denominata "Authorization".
8.  In **Value** (Valore) impostare il token creato usando ArmClient alla fine della sezione precedente. 
9.  Selezionare la scheda **Body** (Corpo) all'interno di Postman. 
10. Selezionare il pulsante **Raw** (Non elaborato).
11. Nella casella di testo incollare la seguente definizione di budget di esempio, sostituendo però i parametri **subscriptionid**, **budgetname** e **actiongroupname** rispettivamente con l'ID della sottoscrizione, un nome univoco per il budget e il nome del gruppo di azioni creato sia nell'URL che nel corpo della richiesta:
    
    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                }, 
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Fare clic su **Send** (Invia) per inviare la richiesta.

A questo punto sono state completate tutte le operazioni necessarie per chiamare l'[API Budget](https://docs.microsoft.com/rest/api/consumption/budgets). Nelle informazioni di riferimento sull'API Budget sono inclusi altri dettagli sulle richieste specifiche, tra cui le seguenti:
    - **budgetName**: sono supportati più budget.  I nomi dei budget devono essere univoci.
    - **category**: deve essere **Cost** o **Usage**. L'API supporta i budget relativi ai costi e all'utilizzo.
    - **timeGrain**: il budget può essere mensile, trimestrale o annuale. L'importo viene azzerato alla fine del periodo.
    - **filters**: i filtri consentono di limitare il budget a un set specifico di risorse all'interno dell'ambito selezionato. Un filtro può ad esempio essere costituito da una raccolta di gruppi di risorse per un budget definito a livello di sottoscrizione.
    - **notifications**: determina i dettagli e le soglie di notifica. È possibile impostare più soglie e specificare un indirizzo di posta elettronica o un gruppo di azioni per ricevere una notifica.

## <a name="summary"></a>Riepilogo

In questa esercitazione si è appreso come:
- Creare un runbook di Automazione di Azure per arrestare le macchine virtuali.
- Creare un'app per la logica di Azure da attivare in base ai valori soglia del budget e chiamare il runbook correlato con i parametri corretti.
- Creare un gruppo di azioni di Monitoraggio di Azure da configurare per attivare l'app per la logica di Azure quando viene raggiunta la soglia di budget.
- Creare il budget di Azure con le soglie desiderate e collegarlo al gruppo di azioni.

Per la sottoscrizione è ora disponibile un budget perfettamente operativo che determina l'arresto delle macchine virtuali quando vengono raggiunte le soglie di budget configurate. 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sugli scenari di fatturazione di Azure, vedere [Scenari di automazione della fatturazione e della gestione dei costi](billing-cost-management-automation-scenarios.md).

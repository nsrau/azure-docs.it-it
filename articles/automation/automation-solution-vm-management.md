---
title: Macchine virtuali di avvio/arresto durante soluzione orario di lavoro (anteprima) | Documenti Microsoft
description: Questa soluzione di gestione della macchina virtuale avvia e arresta le macchine virtuali di Azure Resource Manager in una pianificazione e consente di monitorare in modo proattivo dal Log Analitica.
services: automation
documentationCenter: 
authors: eslesar
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: magoedte
ms.openlocfilehash: 4424cbb83bdb31c60e15d62f9387b4050611a98d
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Macchine virtuali di avvio/arresto durante la soluzione orario di lavoro (anteprima) in automazione di Azure

Le macchine virtuali di avviare o arrestare l'orario di ufficio soluzione avvia e arresta le macchine virtuali di Azure in una pianificazione definita dall'utente, fornisce informazioni dettagliate tramite Azure Log Analitica e invia messaggi di posta elettronica facoltativi utilizzando [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. 

Questa soluzione fornisce un'opzione di automazione decentralizzata per gli utenti che desiderano ridurre i costi utilizzando le risorse senza server e a basso costo. Con questa soluzione, è possibile:

* Pianificare le macchine virtuali per avviare e arrestare.
* Pianificare le macchine virtuali per avviare e arrestare in ordine crescente usando i tag di Azure (non supportato per le macchine virtuali classiche).
* Auto-interruzione di macchine virtuali in base all'utilizzo della CPU bassa.

## <a name="prerequisites"></a>Prerequisiti

- I runbook funzionano con un [account RunAs di Azure](automation-offering-get-started.md#authentication-methods).  L'account RunAs è il metodo di autenticazione preferito perché utilizza l'autenticazione del certificato anziché una password che potrebbe scadere o modificati di frequente.  

- Questa soluzione gestisce solo le macchine virtuali che si trovano nella stessa sottoscrizione dell'account di automazione di Azure.  

- Questa soluzione viene distribuita solo per le seguenti aree di Azure: Australia sudorientale, Canada centrale, India centrale, Stati Uniti orientali, Giappone orientale, Asia sudorientale, Regno Unito meridionale ed Europa occidentale.  
    
    > [!NOTE]
    > I runbook che gestiscono la pianificazione delle macchine virtuali possono essere usati in qualsiasi area.  

- Per inviare notifiche tramite posta elettronica quando la fine di avviare e arrestare i runbook VM, durante il caricamento da Azure Marketplace, selezionare **Sì** per la distribuzione di SendGrid. 

    > [!IMPORTANT]
    > SendGrid è un servizio di terze parti. Per assistenza, contattare [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Limitazioni con SendGrid sono:

    * Un massimo di un account di SendGrid per utente per ogni sottoscrizione.
    * Un massimo di due account di SendGrid per ogni sottoscrizione.

Se è stata distribuita una versione precedente di questa soluzione, è necessario eliminarlo prima dall'account di prima di distribuire questa versione.  

## <a name="solution-components"></a>Componenti della soluzione

Questa soluzione include runbook preconfigurato, pianificazioni e integrazione con Analitica di Log è possibile personalizzare l'avvio e arresto delle macchine virtuali in base alle esigenze aziendali. 

### <a name="runbooks"></a>Runbook

Nella tabella seguente sono elencati i runbook distribuiti al proprio account di automazione.  È consigliabile non apportare modifiche al codice di runbook. Invece di scrivere propri runbook per la nuova funzionalità.

> [!IMPORTANT]
> Non viene eseguito direttamente qualsiasi runbook con "figlio" aggiunto al nome.
>

Tutti i runbook padre includono la *WhatIf* parametro. Se impostato su **True**, *WhatIf* supporta che riporta in dettaglio il comportamento esatto esegue il runbook quando viene eseguito senza il *WhatIf* parametro e convalida la corretta da macchine virtuali destinazione.  Un runbook esegue solo le azioni definite quando il *WhatIf* parametro è impostato su **False**. 

|**Runbook** | **Parameters** | **Descrizione**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato solo dal runbook padre. Crea gli avvisi con cadenza risorse per /AUTOSTOP scenario.| 
|AutoStop_CreateAlert_Parent | WhatIf: True o False <br> VMList | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> VMList: Elenco delimitato da virgole delle macchine virtuali.  Ad esempio, *vm1, vm2, vm3*.| 
|AutoStop_Disable | Nessuno | Disattiva avvisi /AUTOSTOP e la pianificazione predefinita.| 
|AutoStop_StopVM_Child | WebHookData | Chiamato solo dal runbook padre. Le regole di avviso richiamare questo runbook per arrestare la macchina virtuale.|  
|Bootstrap_Main | Nessuno | Utilizzare una volta per impostare le configurazioni di avvio, ad esempio webhookURI, che sono in genere non è accessibile da Gestione risorse di Azure. Questo runbook viene rimosso automaticamente durante la distribuzione ha esito positivo.|  
|ScheduledStartStop_Child | VMName <br> Azione: Arresto o Avvio <br> ResourceGroupName | Chiamato solo dal runbook padre. Esegue arresta o Avvia per l'arresto pianificato.|  
|ScheduledStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | Ciò influisce su tutte le macchine virtuali nella sottoscrizione. Modificare il **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** per essere eseguiti solo su questi gruppi di risorse di destinazione. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile **External_ExcludeVMNames**. *WhatIf* si comporta esattamente come altri runbook.|  
|SequencedStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | Creare tag denominato **sequenza** e **SequenceStop** in ogni macchina virtuale per cui desidera eseguire attività di avvio/arresto sequence. Il valore del tag deve essere un numero intero positivo (1, 2, 3) che corrisponde all'ordine in cui si desidera avviare o arrestare. *WhatIf* si comporta esattamente come altri runbook. <br> **Nota**: le macchine virtuali devono essere all'interno di gruppi di risorse definiti come External_Start_ResourceGroupNames External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di automazione di Azure. È necessario che i tag appropriati per le azioni effettive.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di automazione.  È necessario modificare solo le variabili con precedute **esterno**. Modifica delle variabili con prefisso **interno** causa effetti indesiderati.  

|**Variabile** | **Descrizione**|
---------|------------|
|External_AutoStop_Condition | L'operatore condizionale necessaria per configurare la condizione prima che venga attivato un avviso. I valori accettabili sono **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, e **LessThanOrEqual**.|  
|External_AutoStop_Description | Arrestare la macchina virtuale se la percentuale di CPU supera la soglia di avviso.|  
|External_AutoStop_MetricName | Il nome della misurazione delle prestazioni per il quale è necessario configurare la regola di avviso di Azure.| 
|External_AutoStop_Threshold | La soglia per la regola di avviso di Azure specificata nella variabile *External_AutoStop_MetricName*. I valori di percentuale possono variare da 1 a 100.|  
|External_AutoStop_TimeAggregationOperator | L'operatore di aggregazione ora, che viene applicato alle dimensioni della finestra selezionati per valutare la condizione. I valori accettabili sono **Media**, **minimo**, **massimo**, **totale**, e **ultimo**.|  
|External_AutoStop_TimeWindow | Le dimensioni della finestra durante i quali Azure consente di analizzare le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|  
|External_EmailFromAddress | Specifica il mittente del messaggio di posta elettronica.|  
|External_EmailSubject | Specifica il testo per la riga dell'oggetto del messaggio di posta elettronica.|  
|External_EmailToAddress | Specifica i destinatari del messaggio di posta elettronica. Separare i nomi con una virgola.|  
|External_ExcludeVMNames | Immettere i nomi di macchina virtuale da escludere, separando i nomi con una virgola senza spazi.|  
|External_IsSendEmail | Specifica l'opzione per inviare la notifica tramite posta elettronica al termine dell'operazione.  Specificare **Sì** o **No** per non inviare il messaggio di posta elettronica.  Questa opzione deve essere **n** se le notifiche di posta elettronica non è stato abilitato durante la distribuzione iniziale.|  
|External_Start_ResourceGroupNames | Specifica uno o più gruppi di risorse, separare i valori con una virgola, di destinazione per le azioni di avvio.|  
|External_Stop_ResourceGroupNames | Specifica uno o più gruppi di risorse, separare i valori con una virgola, di destinazione per le azioni di arresto.|  
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|  
|Internal_AutoSnooze_WebhookUri | Specifica l'URI del Webhook chiamato per lo scenario AutoStop.|  
|Internal_AzureSubscriptionId | Specifica l'ID sottoscrizione di Azure.|  
|Internal_ResourceGroupName | Specifica il nome del gruppo di risorse di account di automazione.|  
|Internal_SendGridAccountName | Specifica il nome dell'account di SendGrid.|  
|Internal_SendGridPassword | Specifica la password dell'account di SendGrid.|  

<br>

In tutti gli scenari, il **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, e **External_ExcludeVMNames** le variabili sono necessarie Per specificare come destinazione le macchine virtuali, ad eccezione che fornisce un elenco delimitato da virgole di macchine virtuali per il **AutoStop_CreateAlert_Parent** runbook. Le macchine virtuali devono si trovano in gruppi di risorse di destinazione per l'avvio e arresto delle azioni. La logica di un bit funziona come criterio di Azure, in quanto è possibile il gruppo di sottoscrizione o la risorsa di destinazione e azioni ereditata dalle macchine virtuali appena create. Questo approccio evita di dover gestire una pianificazione separata per ogni macchina virtuale e gestione avvia e arresta nella scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.  È possibile modificarle o creare le proprie pianificazioni personalizzate.  Per impostazione predefinita, ognuna di queste sono disabilitati, ad eccezione di **Scheduled_StartVM** e **Scheduled_StopVM**.

Non è necessario abilitare tutte le pianificazioni, perché ciò potrebbe creare azioni pianificazione sovrapposti. È consigliabile determinare quali si desidera eseguire e modificare di conseguenza le ottimizzazioni.  Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.   

|**Nome della pianificazione** | **Frequenza** | **Descrizione**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook AutoStop_CreateAlert_Parent ogni 8 ore, che a sua volta interrompe i valori basati su VM External_Start_ResourceGroupNames External_Stop_ResourceGroupNames e External_ExcludeVMNames nelle variabili di automazione di Azure.  In alternativa, è possibile specificare un elenco delimitato da virgole delle macchine virtuali utilizzando il parametro VMList.|  
|Scheduled_StopVM | Ogni giorno definito dall'utente | Esegue il runbook Scheduled_Parent con un parametro di *arrestare* ogni giorno all'ora specificata.  Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili di asset. È consigliabile abilitare la pianificazione correlata, **pianificati-StartVM**.|  
|Scheduled_StartVM | Ogni giorno definito dall'utente | Esegue il runbook Scheduled_Parent con un parametro di *avviare* ogni giorno all'ora specificata.  Viene avviato automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili appropriate.  È consigliabile abilitare la pianificazione correlata, **pianificato StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), venerdì | Esegue il runbook Sequenced_Parent con un parametro di *arrestare* ogni venerdì all'ora specificata.  In modo sequenziale (crescente) arresta tutte le macchine virtuali con un tag di **SequenceStop** definito dalle variabili di tipo appropriate.  Vedere la sezione di runbook per ulteriori informazioni sui valori di tag e le variabili di asset.  È consigliabile abilitare la pianificazione correlata, **Sequenced StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), ogni lunedì | Esegue il runbook Sequenced_Parent con un parametro *Start* ogni lunedì a una determinata ora. In modo sequenziale (decrescente) avvia tutte le macchine virtuali con un tag di **sequenza** definito dalle variabili di tipo appropriate.  Vedere la sezione di runbook per ulteriori informazioni sui valori di tag e le variabili di asset.  È consigliabile abilitare la pianificazione correlata, **Sequenced StopVM**.|

<br>

## <a name="configuration"></a>Configurazione

Eseguire la procedura seguente per aggiungere le macchine virtuali di avvio/arresto durante la soluzione di orario di lavoro per l'account di automazione e quindi configurare le variabili per personalizzare la soluzione.

1. Nel portale di Azure fare clic su **Nuovo**.<br> ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Nel riquadro Marketplace, digitare una parola chiave, ad esempio **avviare** o **Start/Stop**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare uno o più parole chiave dal nome completo della soluzione e quindi premere INVIO.  Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** dai risultati della ricerca.  
3. Nel riquadro **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** per la soluzione selezionata, esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.  
4. Il **Aggiungi soluzione** verrà visualizzato il riquadro. Richiesto di configurare la soluzione prima di poter importare alla sottoscrizione di automazione.<br><br> ![Pannello Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Nel **Aggiungi soluzione** riquadro, selezionare **dell'area di lavoro**. Selezionare un'area di lavoro OMS che è collegato alla stessa sottoscrizione Azure che l'account di automazione. Se non si dispone di un'area di lavoro, selezionare **Crea nuova area di lavoro**. Nel **area di lavoro OMS** riquadro, eseguire le operazioni seguenti: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare un **sottoscrizione** a cui collegarsi selezionando dall'elenco a discesa, se il valore predefinito selezionato non è appropriato.
   - Per **gruppo di risorse**, è possibile creare un nuovo gruppo di risorse o selezionarne uno esistente.  
   - Selezionare un **percorso**.  Non sono attualmente i soli percorsi disponibili **Australia sudorientale**, **Canada centrale**, **India centrale**, **Stati Uniti orientali**, **Giappone orientale**, **Asia sudorientale**, **Regno Unito meridionale**, e **Europa occidentale**.
   - Selezionare un **Piano tariffario**.  La soluzione offre due livelli: gratuita e a pagamento di OMS.  Il piano gratuito prevede un limite sulla quantità di dati raccolti ogni giorno, il periodo di memorizzazione e i minuti di runtime processo runbook.  Il livello OMS a pagamento non ha limiti per la quantità di dati raccolti al giorno.  

        > [!NOTE]
        > Anche se il livello per ogni GB (Standalone) a pagamento viene visualizzato come un'opzione, non è applicabile.  Se si seleziona e procedere con la creazione di questa soluzione nella sottoscrizione, l'esito negativo.  Questo problema sarà risolto al momento del rilascio ufficiale della soluzione.<br>In questa soluzione solo utilizza automazione minuti di esecuzione processo e l'inserimento di log.  Non aggiunge i nodi aggiuntivi OMS al proprio ambiente.  

6. Dopo aver specificato le informazioni necessarie sul **area di lavoro OMS** riquadro, fare clic su **crea**.  È possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu, è possibile che restituisce il **Aggiungi soluzione** riquadro al termine.  
7. Nel **Aggiungi soluzione** riquadro, selezionare **account di automazione**.  Se si sta creando una nuova area di lavoro OMS, è necessario inoltre creare un nuovo account di automazione per essere associato ad esso.  Selezionare **creare un account di automazione**e scegliere il **account di automazione aggiungere** riquadro fornisce gli elementi seguenti: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolati automaticamente nell'area di lavoro OMS selezionato. Queste opzioni non possono essere modificate.  Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure.  Dopo aver fatto clic **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, è possibile selezionare un account RunAs di Automazione esistente.  Si noti che l'account selezionato non può già essere collegato a un'altra area di lavoro OMS. Se è già collegato, si riceve un messaggio ed è necessario selezionare un account RunAs automazione diverso o crearne uno nuovo.<br><br> ![Account di Automazione già collegato all'area di lavoro OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Infine, sul **Aggiungi soluzione** riquadro, selezionare **configurazione**. Il **parametri** verrà visualizzato il riquadro.<br><br> ![Riquadro dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  In questo caso, viene chiesto di:  
   - Specificare il **i nomi di gruppo di risorse di destinazione**. Questi sono i nomi dei gruppi di risorse che contengono le macchine virtuali deve essere gestito da questa soluzione.  È possibile immettere più di un nome e, è necessario separarli con una virgola (valori non sono tra maiuscole e minuscole).  Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly.
   - Specificare il **elenco esclusioni VM (string)**. Questo è il nome di uno o più macchine virtuali dal gruppo di risorse di destinazione.  È possibile immettere più di un nome e, è necessario separarli con una virgola (valori non sono tra maiuscole e minuscole).  I caratteri jolly sono supportati.
   - Selezionare un **pianificazione**. Si tratta di una ricorrenza di data e ora per avviare e arrestare le macchine virtuali nei gruppi di risorse di destinazione.  Per impostazione predefinita, la pianificazione è configurata per il fuso orario UTC. Selezionare un'altra area non è disponibile.  Per configurare la pianificazione per il fuso orario specifico dopo la configurazione della soluzione, vedere [modificando la pianificazione di avvio e arresto](#modifying-the-startup-and-shutdown-schedule).
   - Per ricevere **notifiche tramite posta elettronica** da SendGrid, accettare il valore predefinito **Sì** e fornire un indirizzo di posta elettronica valido. Se si seleziona **n** ma decidere in un secondo momento, che si desidera ricevere notifiche tramite posta elettronica, è possibile aggiornare il **External_EmailToAddress** variabile con indirizzi di posta elettronica validi separati da una virgola, quindi modificare la variabile **External_IsSendEmail** con il valore **Sì**.  

9. Dopo aver configurato le impostazioni iniziali necessari per la soluzione, selezionare **crea**.  Dopo tutte le impostazioni di convalida, la soluzione viene distribuita per la sottoscrizione.  Questo processo può richiedere diversi secondi, ed è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu. 

## <a name="collection-frequency"></a>Frequenza della raccolta

Automazione del log e processo di flusso dati del processo vengono acquisiti al repository Analitica Log ogni 5 minuti.  

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione di gestione di macchine Virtuali, il **StartStopVMView** riquadro viene aggiunto al dashboard nell'area di lavoro Analitica Log dal portale di Azure.  Questo riquadro Visualizza un numero e una rappresentazione grafica dei processi di runbook per la soluzione che è stato avviato e completato correttamente.<br><br> ![Riquadro StartStopVM View (Visualizzazione di avvio e arresto di VM) di Virtual Machine Management](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Nell'account di automazione, è possibile accedere e gestire la soluzione selezionando il **dell'area di lavoro** opzione. Nella pagina Log Analitica, selezionare **soluzioni** dal riquadro di sinistra.  Nel **soluzioni** pagina, selezionare la soluzione **Start-Stop-VM [area]** dall'elenco.<br><br> ![Elenco delle soluzioni in Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

La scelta della soluzione consente di visualizzare il **Start-Stop-VM [area]** riquadro soluzione. Qui è possibile rivedere le informazioni importanti, ad esempio il **StartStopVM** riquadro. Come l'area di lavoro Log Analitica, questo riquadro Visualizza un conteggio e una rappresentazione grafica dei processi di runbook che sono stati avviati e avere è stata completata per la soluzione.<br><br> ![Pagina della soluzione della gestione di aggiornamenti di automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Da qui è possibile eseguire un'ulteriore analisi dei record di processo facendo clic sul riquadro ad anello. Il dashboard di soluzione Mostra la cronologia processo e di query di ricerca log. Passare al portale di Log Analitica avanzate per la ricerca basata su query di ricerca.  

Tutti i runbook padre includono la *WhatIf* parametro. Se impostato su **True**, questa supporta che riporta in dettaglio il comportamento esatto esegue il runbook quando viene eseguito senza il *WhatIf* parametro e verifica che le macchine virtuali di corrette sono di destinazione. I runbook eseguono solo le azioni definite quando il *WhatIf* parametro è impostato su **False**.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>Scenario 1: Giornaliera avviare o arrestare le macchine virtuali in una sottoscrizione o gruppi di risorse di destinazione 

Questa è la configurazione predefinita al momento della prima distribuzione della soluzione.  Ad esempio, è possibile configurare in modo da interrompere tutte le macchine virtuali in una sottoscrizione quando si lascia lavoro nelle ore serali e avviarli del mattino quando si è in ufficio. Quando si configurano le pianificazioni **pianificati-StartVM** e **pianificato StopVM** durante la distribuzione, avviare e arrestare le macchine virtuali di destinazione.
>[!NOTE]
>Quando si configura il parametro tempo di pianificazione, il fuso orario è il fuso orario corrente. Tuttavia, archiviato in formato UTC in automazione di Azure.  Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione.

È possibile controllare le macchine virtuali presenti nell'ambito configurando le seguenti variabili: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, e **External_ ExcludeVMNames**.  

>[!NOTE]
> Il valore per **nomi di gruppo di risorse di destinazione** viene archiviato come valore per entrambi **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse.  Per l'azione di avvio, utilizzare **External_Start_ResourceGroupNames**e per l'azione di interruzione, utilizzare **External_Stop_ResourceGroupNames**. Macchine virtuali vengono aggiunte automaticamente all'inizio e di arrestare le pianificazioni.

Per testare e convalidare la configurazione, avviare manualmente il **ScheduledStartStop_Parent** runbook e impostare il parametro ACTION **avviare** o **arrestare** e il WHATIF parametro **True**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 L'azione pianificata di anteprima e apportare le modifiche necessarie prima dell'implementazione in produzione, le macchine virtuali.  È possibile eseguire manualmente il runbook con il parametro impostato su **False**, oppure è possibile consentire la pianificazione di automazione **pianificazione-StartVM** e **pianificazione StopVM** eseguire automaticamente dopo la pianificazione prescritta.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>Scenario 2: Sequenza di avviare o arrestare le macchine virtuali in una sottoscrizione utilizzando i tag
In un ambiente che include due o più componenti su più macchine virtuali che supporta un carico di lavoro distribuita, è importante che supporta la sequenza in cui i componenti avviati e arrestati in ordine.  Questo scopo, è possibile eseguire la procedura seguente:

1. Aggiungere un **sequenza** e **SequenceStop** tag con un valore intero positivo per le macchine virtuali che sono destinati in **External_Start_ResourceGroupNames** e  **External_Stop_ResourceGroupNames** variabili.  Le azioni start e stop vengono eseguite in ordine crescente.  Per informazioni su come contrassegnare una macchina virtuale, vedere [Tag di una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Tag di una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).
2. Modificare le pianificazioni **Sequenced StartVM** e **Sequenced StopVM** alla data e ora di soddisfare i requisiti e abilitare la pianificazione.  

Per testare e convalidare la configurazione, avviare manualmente il **SequencedStartStop_Parent** runbook. Impostare il parametro ACTION **avviare** o **arrestare** e il parametro WHATIF per **True**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> L'azione di anteprima e apportare le modifiche necessarie prima dell'implementazione in produzione, le macchine virtuali.  Quando pronto, manualmente esegue il runbook con il parametro impostato su **False**, o si lascia la pianificazione di automazione **Sequenced StartVM** e **Sequenced StopVM** eseguire automaticamente dopo la pianificazione prescritta.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>Scenario 3: Automatizzare avviare o arrestare le macchine virtuali in una sottoscrizione basata sull'utilizzo della CPU
Questa soluzione consente di gestire il costo delle macchine virtuali in esecuzione nella sottoscrizione valutando macchine virtuali di Azure che non sono utilizzati durante i periodi di non di punta, ad esempio dopo l'orario, e l'arresto automatico li se l'utilizzo del processore è minore di 0x %.  

Per impostazione predefinita, la soluzione è preconfigurato per valutare la metrica di percentuale della CPU per vedere se l'utilizzo medio è 5% o meno.  Questo è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

È possibile abilitare l'azione su una sottoscrizione e il gruppo di risorse di destinazione, oppure come destinazione di un elenco specifico di macchine virtuali, ma non entrambi.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destinare l'azione di arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.  
2. Abilitare e aggiornare la pianificazione **Schedule_AutoStop_CreateAlert_Parent**.
3. Eseguire il **AutoStop_CreateAlert_Parent** runbook con il set di parametri azione **avviare** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

#### <a name="target-the-stop-action-by-vm-list"></a>L'azione arresta di destinazione dall'elenco VM

1. Eseguire il **AutoStop_CreateAlert_Parent** runbook con il set di parametri azione **avviare**, aggiungere un elenco delimitato da virgole delle macchine virtuali nel *VMList* parametro e quindi impostare il Il parametro WHATIF per **True**. Visualizzare in anteprima le modifiche.  
2. Configurare il **External_ExcludeVMNames** parametro con un elenco delimitato da virgole delle macchine virtuali (VM1, VM2, VM3).
3. Questo scenario non rispetta il **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames** variabili.  Per questo scenario, è necessario creare una pianificazione di automazione. Per informazioni dettagliate, vedere [pianificazione di un runbook in automazione di Azure](../automation/automation-schedules.md).

Dopo aver creato una pianificazione per l'arresto delle macchine virtuali in base a utilizzo della CPU, è necessario abilitare una delle seguenti pianificazioni per avviarli.

* Azione di avvio di destinazione dal gruppo di risorse e di sottoscrizione.  Vedere i passaggi descritti in [lo Scenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) per test e l'abilitazione **pianificati-StartVM** pianificazioni.
* Destinazione azione dalla sottoscrizione, gruppo di risorse e i tag di avvio.  Vedere i passaggi descritti in [nello Scenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) per test e l'abilitazione **Sequenced StartVM** pianificazioni.


### <a name="configuring-email-notifications"></a>Configurazione delle notifiche di posta elettronica

Per configurare notifiche tramite posta elettronica dopo la distribuzione della soluzione, modificare le seguenti tre variabili:

* External_EmailFromAddress: Specificare l'indirizzo di posta elettronica del mittente.
* External_EmailToAddress: Specificare un elenco delimitato da virgole di indirizzi di posta elettronica (user@hotmail.com, user@outlook.com) per ricevere notifiche tramite posta elettronica.
* External_IsSendEmail: Impostare **Sì** per ricevere messaggi di posta elettronica. Per disabilitare le notifiche di posta elettronica, impostare il valore **n**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modifica delle pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Record di Log Analytics

Automazione crea due tipi di record nel repository OMS: processo di log e i flussi del processo.

### <a name="job-logs"></a>Log di processo

Proprietà | DESCRIZIONE|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobLogs.|
CorrelationId | Il GUID che rappresenta l'ID di correlazione del processo di runbook.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore è Job.|
ResourceId | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Lo stato del processo di runbook.  I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato|
resultDescription | Descrive lo stato del risultato del processo di runbook.  I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato|
RunbookName | Specifica il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per l'automazione, il valore è Operations Manager|
StreamType | Specifica il tipo di evento. I valori possibili sono:<br>- Dettagliato<br>- Output<br>- Errore<br>- Avviso|
SubscriptionId | Specifica l'ID sottoscrizione del processo.
Tempo | Data e ora di esecuzione del processo del runbook.|


### <a name="job-streams"></a>Flussi di processo

Proprietà | DESCRIZIONE|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobStreams.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore è Job.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
ResourceId | Specifica l'ID risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento. È un valore possibile:<br>- InProgress|
resultDescription | Include il flusso di output dal runbook.|
RunbookName | Il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per l'automazione, il valore è Operations Manager.|
StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>-Stato di avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
Tempo | Data e ora di esecuzione del processo del runbook.|

Quando si eseguono tutte le ricerche log che restituisce i record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare il **JobLogs** o **JobStreams**visualizzazione, che visualizza una serie di sezioni di riepilogo gli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da questa soluzione. 

Query | DESCRIZIONE|
----------|----------|
Individuazione dei processi per il runbook ScheduledStartStop_Parent quali è stata completata correttamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Individuazione dei processi per il runbook SequencedStartStop_Parent quali è stata completata correttamente | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>Rimuovere la soluzione

Se si decide di che non è più necessario utilizzare la soluzione, è possibile eliminarlo dall'account di automazione.  La soluzione di eliminazione rimuove solo i runbook. Non elimina le pianificazioni o variabili che sono state create quando la soluzione è stata aggiunta.  Tali risorse che è necessario eliminare manualmente se non vengono utilizzati con altri runbook.  

Per eliminare la soluzione, attenersi alla procedura seguente:

1.  Dall'account di Automazione selezionare **Area di lavoro** dal riquadro di sinistra.  
2.  Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).  Nel **VMManagementSolution [area]** pagina, dal menu, seleziona **eliminare**.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Nel **Elimina soluzione** finestra, confermare che si desidera eliminare la soluzione.
4.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu.  Verrà visualizzata di nuovo il **soluzioni** pagina dopo l'avvio del processo per rimuovere la soluzione.  

L'account di Automazione e l'area di lavoro di Log Analytics non vengono eliminati come parte del processo.  Se si desidera conservare l'area di lavoro Log Analitica, è necessario eliminarlo manualmente.  Questa operazione può essere eseguita dal portale di Azure:

1.    La schermata iniziale del portale Azure, selezionare **Analitica Log**.
2. Nel **Log Analitica** riquadro, selezionare l'area di lavoro.
3. Selezionare **eliminare** dal menu nel riquadro area di lavoro impostazioni.  
      
## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su come creare query di ricerca diverso ed esaminare i registri del processo di automazione con Log Analitica, vedere [Accedi ricerche Log Analitica](../log-analytics/log-analytics-log-searches.md).
- Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
- Per ulteriori informazioni sui Log Analitica e origini di raccolta dati, vedere [dati di archiviazione di Azure la raccolta in panoramica Analitica Log](../log-analytics/log-analytics-azure-storage.md).






   


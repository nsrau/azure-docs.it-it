---
title: "Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività | Microsoft Docs"
description: La soluzione Virtual Machine Management permette di avviare e arrestare le macchine virtuali di Azure Resource Manager in base a una pianificazione e di monitorare in modo attivo tramite Log Analytics.
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
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: 2ff2208f62c24c460c9d17533e28fd007549828b
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività avvia e arresta le macchine virtuali di Azure nelle pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite Log Analytics e invia messaggi di posta elettronica facoltativi sfruttando [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. 

Questa soluzione fornisce una funzionalità di automazione decentralizzata per i clienti che vogliono ridurre i costi di utilizzo delle risorse senza server a basso costo. Funzionalità incluse:

* Pianificazione delle macchine virtuali per l'avvio o l'arresto
* Pianificazione delle macchine virtuali per l'avvio o l'arresto in ordine crescente usando i tag di Azure (non supportati per le macchine virtuali classiche)
* Arresto automatico delle macchine virtuali in base al basso utilizzo della CPU

## <a name="prerequisites"></a>Prerequisiti

- I runbook funzionano con un [account RunAs di Azure](automation-offering-get-started.md#authentication-methods).  L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che può scadere o essere modificata di frequente.  

- Questa soluzione può gestire solo le macchine virtuali nella stessa sottoscrizione in cui risiede l'account di Automazione.  

- È possibile eseguire la distribuzione solo nelle aree di Azure seguenti: Australia sud-orientale, Canada centrale, India centrale, Stati Uniti orientali, Giappone orientale, Asia sud-orientale, Regno Unito meridionale ed Europa occidentale.  
    
    > [!NOTE]
    > I runbook che gestiscono la pianificazione delle macchine virtuali possono essere usati in qualsiasi area.  

- Per inviare notifiche tramite posta elettronica quando l'avvio e l'arresto dei runbook delle macchine virtuali vengono completati, durante il caricamento da Azure Marketplace è necessario selezionare **Sì** per la distribuzione di SendGrid. 

    > [!IMPORTANT]
    > SendGrid è un servizio di terze parti. Per il supporto con SendGrid contattare [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Le limitazioni con SendGrid sono le seguenti:

    * Numero massimo di un account di SendGrid per utente per ogni sottoscrizione
    * Numero massimo di due account di SendGrid per ogni sottoscrizione

Se è stata distribuita una versione precedente di questa soluzione, è necessario innanzitutto eliminarla dall'account prima di distribuire questa versione.  

## <a name="solution-components"></a>Componenti della soluzione

Questa soluzione include runbook preconfigurati, pianificazioni e l'integrazione con Log Analytics che consente di personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali. 

### <a name="runbooks"></a>Runbook

Nella tabella seguente sono elencati i runbook distribuiti al proprio account di Automazione.  È consigliabile non apportare modifiche al codice del runbook, ma scrivere il proprio runbook per la nuova funzionalità.

> [!NOTE]
> Non eseguire direttamente i runbook con il nome "Child" aggiunto alla fine del nome.
>

Tutti i runbook padre includono il parametro *WhatIf* che, quando impostato su **True**, riporta in dettaglio il comportamento esatto del runbook quando viene eseguito senza il parametro *WhatIf* e convalida che vengano selezionate come destinazione le macchine virtuali corrette.  Quando il parametro *WhatIf* è impostato su **False** i runbook eseguono solo le loro azioni definite. 

|**Runbook** | **Parametri** | **Descrizione**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato soltanto dal runbook padre. Crea gli avvisi in base alle risorse per lo scenario Autostop.| 
|AutoStop_CreateAlert_Parent | WhatIf: True o False <br> VMList | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> VMList: Elenco delimitato da virgole delle macchine virtuali.  Ad esempio, *vm1, vm2, vm3*| 
|AutoStop_Disable | Nessuno | Disabilitare avvisi AutoStop e la pianificazione predefinita.| 
|AutoStop_StopVM_Child | WebHookData | Chiamato soltanto dal runbook padre. Le regole di avviso chiamano questo runbook e questo esegue l'operazione di arresto della macchina virtuale.|  
|Bootstrap_Main | Nessuno | Usato una sola volta per impostare le configurazioni di bootstrap come webhookURI, che in genere non è accessibile da Azure Resource Manager. Questo runbook viene rimosso automaticamente se la distribuzione è andata a buon fine.|  
|ScheduledStartStop_Child | VMName <br> Azione: Arresto o Avvio <br> ResourceGroupName | Chiamato soltanto dal runbook padre. Effettua l'esecuzione di arresto o avvio per l'arresto pianificato.|  
|ScheduledStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | L'operazione avrà effetto su tutte le macchine virtuali nella sottoscrizione a meno che non si modifichi **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** che la limiteranno a effettuare l'esecuzione solo su questi gruppi di risorse di destinazione. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile **External_ExcludeVMNames**. WhatIf si comporta come negli altri runbook.|  
|SequencedStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | Creare un tag denominato **SequenceStart** e un altro tag denominato **SequenceStop** in ogni macchina virtuale per la quale si intende ordinare in sequenza l'attività di avvio\\arresto. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine crescente in cui si vuole eseguire l'avvio\\l'arresto. WhatIf si comporta come negli altri runbook. <br> **Nota: le macchine virtuali devono trovarsi all'interno di gruppi di risorse definiti External_Start_ResourceGroupNames External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure e disporre dei tag appropriati affinché le azioni abbiano effetto.**|

### <a name="variables"></a>variables

Nella tabella seguente sono elencate le variabili create nell'account di Automazione.  Si consiglia di modificare solo le variabili con prefisso **External**. La modifica delle variabili con prefisso **Internal** causa effetti indesiderati.  

|**Variabile** | **Descrizione**|
---------|------------|
|External_AutoStop_Condition | Questo è l'operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori possibili sono: **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**.|  
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|  
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.| 
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile *External_AutoStop_MetricName*. I valori di percentuale possono variare da 1 a 100.|  
|External_AutoStop_TimeAggregationOperator | L'operatore dell'aggregazione temporale che verrà applicato alle dimensioni della finestra selezionata per valutare la condizione. I valori possibili sono: **Average**, **Minimum**, **Maximum**, **Total**, **Last**.|  
|External_AutoStop_TimeWindow | Le dimensioni della finestra sulle quali Azure analizzerà la metrica selezionata per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra cinque minuti e sei ore.|  
|External_EmailFromAddress | Specifica il mittente del messaggio di posta elettronica.|  
|External_EmailSubject | Specifica il testo per la riga dell'oggetto del messaggio di posta elettronica.|  
|External_EmailToAddress | Specifica i destinatari del messaggio di posta elettronica. Separare i nomi con una virgola.|  
|External_ExcludeVMNames | Inserire i nomi delle macchine virtuali da escludere, separandoli usando una virgola senza spazi.|  
|External_IsSendEmail | Specifica l'opzione per inviare la notifica tramite posta elettronica al termine dell'operazione.  Specificare **Sì** o **No** per non inviare il messaggio di posta elettronica.  Questa opzione deve essere **No** se non è stato creato un SendGrid durante la distribuzione iniziale.|  
|External_Start_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di avvio.|  
|External_Stop_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di arresto.|  
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|  
|Internal_AutoSnooze_WebhookUri | Specifica l'URI del Webhook chiamato per lo scenario AutoStop.|  
|Internal_AzureSubscriptionId | Specifica l'ID sottoscrizione di Azure.|  
|Internal_ResourceGroupName | Specifica il nome del gruppo di risorse dell'account di Automazione di Azure.|  
|Internal_SendGridAccountName | Specifica il nome dell'account di SendGrid.|  
|Internal_SendGridPassword | Specifica la password dell'account di SendGrid.|  

<br>

In tutti gli scenari, le variabili **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** sono necessarie per le macchine virtuali di destinazione con l'eccezione di fornire un elenco separato da virgole delle macchine virtuali per il runbook **AutoStop_CreateAlert_Parent**. In altre parole, le macchine virtuali devono risiedere nei gruppi di risorse di destinazione affinché si verifichino le azioni di avvio/arresto. La logica funziona un po' come i criteri di Azure, in quanto è possibile scegliere come destinazione la sottoscrizione o il gruppo di risorse e disporre di azioni ereditate dalle macchine virtuali appena create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire l'avvio/arresto in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.  È possibile modificarle o creare le proprie pianificazioni personalizzate.  Ognuna di queste pianificazioni viene disabilitata per impostazione predefinita, ad eccezione di **Scheduled_StartVM** e **Scheduled-StopVM**.

Si consiglia di non abilitare tutte le pianificazioni, in quanto ciò potrebbe creare una sovrapposizione in base alla pianificazione che esegue un'azione.  Piuttosto, sarebbe opportuno determinare quali ottimizzazioni si desidera apportare e modificare di conseguenza.  Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.   

|**ScheduleName** | **Frequenza** | **Descrizione**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Viene eseguito ogni otto ore | Esegue il runbook AutoStop_CreateAlert_Parent ogni 8 ore, che a sua volta arresta i valori basati sulla macchina virtuale nelle variabili "External_Start_ResourceGroupNames", "External_Stop_ResourceGroupNames" e "External_ExcludeVMNames" di Automazione di Azure.  In alternativa, è possibile specificare un elenco separato da virgole delle macchine virtuali usando il parametro "VMList".|  
|Scheduled_StopVM | Definita dall'utente, ogni giorno | Esegue il runbook Scheduled_Parent con un parametro “Stop” ogni giorno a una determinata ora.  Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite tramite le variabili di asset.  Consiglia di abilitare la pianificazione sorella, Scheduled-StartVM.|  
|Scheduled_StartVM | Definita dall'utente, ogni giorno | Esegue il runbook Scheduled_Parent con un parametro *Start* ogni giorno a una determinata ora.  Avvierà automaticamente tutte le macchine virtuali che soddisfano le regole definite e una parte delle variabili appropriate.  Si consiglia di abilitare la pianificazione sorella, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 AM (UTC), ogni venerdì | Esegue il runbook Sequenced_Parent con un parametro *Stop* ogni venerdì a una determinata ora.  Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag di **SequenceStop** definito e una parte delle variabili appropriate.  Vedere la sezione Runbook per altre informazioni sui valori di tag e sulle variabili di asset.  Si consiglia di abilitare la pianificazione sorella, **Sequenced-StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC), ogni lunedì | Esegue il runbook Sequenced_Parent con un parametro *Start* ogni lunedì a una determinata ora.  Avvierà in sequenza (decrescente) tutte le macchine virtuali con un tag di **SequenceStart** definito e una parte delle variabili appropriate.  Vedere la sezione Runbook per altre informazioni sui valori di tag e sulle variabili di asset.  Si consiglia di abilitare la pianificazione sorella, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configurazione

Seguire questa procedura per aggiungere la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) all'account di Automazione e quindi configurare le variabili per personalizzarla.

1. Nel portale di Azure fare clic su **Nuovo**.<br> ![Portale di Azure](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Nel riquadro Marketplace digitare **Avvia macchina virtuale**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** dai risultati della ricerca.  
3. Nel riquadro **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** per la soluzione selezionata, esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.  
4. Verrà visualizzato il pannello **Aggiungi soluzione**, in cui viene richiesto di configurare la soluzione prima di importarla nella sottoscrizione di Automazione.<br><br> ![Pannello Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Nel pannello **Aggiungi soluzione** selezionare **Area di lavoro**. Qui è possibile selezionare un'area di lavoro OMS collegata alla stessa sottoscrizione di Azure in cui si trova l'account di Automazione o creare una nuova area di lavoro.  Se non è disponibile un'area di lavoro, è possibile selezionare **Crea una nuova area di lavoro** e seguire questa procedura nel riquadro **Area di lavoro OMS**: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.  
   - Selezionare un **percorso**.  Attualmente le uniche posizioni disponibili per la selezione sono: **Australia sud-orientale**, **Canada centrale**, **India centrale**, **Stati Uniti orientali**, **Giappone orientale**, **Asia sud-orientale**, **Regno Unito meridionale** ed **Europa occidentale**.
   - Selezionare un **Piano tariffario**.  Per la soluzione sono disponibili due livelli, quello gratuito e il livello OMS a pagamento.  Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di memorizzazione e i minuti di esecuzione dei processi dei runbook.  Il livello OMS a pagamento non ha limiti per la quantità di dati raccolti al giorno.  

        > [!NOTE]
        > Nonostante sia visualizzato come opzione, il livello a pagamento Per GB (autonomo) non è applicabile.  Se si seleziona e si procede con la creazione della soluzione nella sottoscrizione, l'operazione avrà esito negativo.  Questo problema sarà risolto al momento del rilascio ufficiale della soluzione.<br>Se si usa la soluzione, questa usa solo l'inserimento di log e i minuti di esecuzione dei processi di automazione.  Non aggiunge altri nodi OMS all'ambiente in uso.  

6. Dopo aver specificato le informazioni necessarie nel pannello **Area di lavoro OMS**, fare clic su **Crea**.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della creazione dell'area di lavoro, è possibile usare la voce **Notifiche** nel menu.  Verrà visualizzato nuovamente il pannello **Aggiungi soluzione**.  
7. Nel pannello **Aggiungi soluzione** selezionare **Account di Automazione**.  Se si sta creando una nuova area di lavoro OMS, viene richiesto di creare anche un nuovo account di Automazione da associare alla nuova area di lavoro OMS specificata in precedenza, nonché l'area, il gruppo di risorse e la sottoscrizione di Azure.  È possibile selezionare **Crea un account di Automazione** e specificare le informazioni seguenti nel pannello **Aggiungi account di Automazione**: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata e non è possibile modificarle.  Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure.  Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, è possibile selezionare un account RunAs di Automazione esistente.  Si noti che l'account selezionato non può essere già collegato a un'altra area di lavoro OMS, in caso contrario nel pannello verrà visualizzato un messaggio informativo.  Se è già collegato, è necessario selezionare un account RunAs di Automazione diverso o crearne uno nuovo.<br><br> ![Account di Automazione già collegato all'area di lavoro OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Infine, nel pannello **Aggiungi soluzione** selezionare **Configurazione**. Verrà visualizzato il pannello **Parametri**.<br><br> ![Riquadro dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  Nel pannello **Parametri** viene richiesto di:  
   - Specificare i **nomi dei gruppi di risorse di destinazione** contenenti le macchine virtuali che devono essere gestite dalla soluzione.  È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole.  Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly.
   - Specificare l'**elenco di esclusioni delle macchine virtuali (stringa)**, ovvero il nome di una o più macchine virtuali dal gruppo di risorse di destinazione.  È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole.  I caratteri jolly sono supportati.
   - Selezionare una **pianificazione**, ovvero una data e un'ora ricorrenti per l'avvio e l'arresto delle macchine virtuali nei gruppi di risorse di destinazione.  Per impostazione predefinita, la pianificazione è configurata per il fuso orario UTC e non è prevista la selezione di un'area diversa.  Se si desidera configurare la pianificazione per il fuso orario specifico dopo aver configurato la soluzione, vedere la sezione seguente [Modifica della pianificazione di avvio e arresto](#modifying-the-startup-and-shutdown-schedule).
   - Per ricevere **notifiche tramite posta elettronica** da SendGrid, accettare il valore predefinito **Sì** e fornire un indirizzo di posta elettronica valido.  Se si seleziona **No** e successivamente si decide che si intende ricevere notifiche tramite posta elettronica, sarà necessario ridistribuire la soluzione nuovamente da Azure Marketplace.  

10. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, selezionare **Crea**.  Tutte le impostazioni verranno convalidate e verrà eseguito un tentativo di distribuzione della soluzione nella sottoscrizione.  Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

## <a name="collection-frequency"></a>Frequenza della raccolta

Il log del processo di Automazione e i dati del flusso del processo vengono inseriti nel repository di Log Analytics ogni cinque minuti.  

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione Gestione macchine virtuali all'area di lavoro di Log Analytics dal portale di Azure, il riquadro **StartStopVM View** (Visualizzazione di avvio e arresto di VM) viene aggiunto al dashboard.  Questo riquadro mostra un conteggio e la rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Riquadro StartStopVM View (Visualizzazione di avvio e arresto di VM) di Virtual Machine Management](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Nell'account di Automazione, è possibile accedere e gestire la soluzione selezionando l'opzione **Area di lavoro** e nella pagina Log Analytics selezionare **Soluzioni** dal riquadro di sinistra.  Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]) dall'elenco.<br><br> ![Elenco delle soluzioni in Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Verrà visualizzato il pannello della soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM [Area di lavoro]), in cui è possibile rivedere informazioni importanti come il riquadro **StartStopVM** (Avvio e arresto di VM) nell'area di lavoro di Log Analytics, contenente un conteggio e la rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Pagina della soluzione della gestione di aggiornamenti di automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Da qui è possibile anche eseguire un'ulteriore analisi dei record di processo facendo clic sul riquadro ad anello e dal dashboard della soluzione si visualizza la cronologia processo, le query di ricerca log predefinite e si passa al portale avanzato di Log Analytics per effettuare una ricerca basata sulle query di ricerca.  

Tutti i runbook padre includono il parametro *WhatIf* che, quando impostato su **True**, riporta in dettaglio il comportamento esatto del runbook quando viene eseguito senza il parametro *WhatIf* e convalida che vengano selezionate come destinazione le macchine virtuali corrette.  Quando il parametro *WhatIf* è impostato su **False** i runbook eseguono solo le loro azioni definite.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Scenario 1: Avvio/arresto giornaliero delle macchine virtuali in una sottoscrizione o nei gruppi di risorse di destinazione 

Questa è la configurazione predefinita al momento della prima distribuzione della soluzione.  Ad esempio, è possibile configurarla in modo da interrompere tutte le macchine virtuali in una sottoscrizione nelle ore serali quando si lascia il luogo di lavoro e avviarle al mattino quando si ritorna in ufficio. Quando si configurano le pianificazioni **Scheduled-StartVM" e **Scheduled-StopVM** durante la distribuzione, queste avviano e arrestano le macchine virtuali di destinazione.
>[!NOTE]
>Il fuso orario è il fuso orario corrente quando si configura il parametro dell'orario di pianificazione. Viene tuttavia archiviato in formato UTC in Automazione di Azure.  Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione.

È possibile controllare quali macchine virtuali rientrano nell'ambito configurando le due variabili - **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames e **External_ExcludeVMNames**.  

>[!NOTE]
> Il valore per la variabile **Target ResourceGroup Names**" viene archiviato come valore per entrambe le variabili **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames**. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse.  Per l'azione di avvio usare **External_Start_ResourceGroupNames**, mentre per l'azione di arresto usare **External_Stop_ResourceGroupNames**. Nuove macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

Per testare e convalidare la configurazione, avviare manualmente il runbook **ScheduledStartStop_Parent** e impostare il parametro *ACTION* su **start** o **stop** e il parametro *WhatIf* su **true**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Ciò consente di visualizzare in anteprima l'azione che si verifica e apportare le modifiche necessarie prima dell'implementazione nelle macchine virtuali di produzione.  Una volta che si ha dimestichezza, è possibile eseguire manualmente il runbook con il parametro impostato su **false** o lasciare che le pianificazioni di Automazione **Schedule-StartVM** e **Schedule-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Scenario 2: Sequenza di avvio/arresto delle macchie virtuali in una sottoscrizione usando i tag
In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare/arrestare in ordine.  A questo scopo, seguire questa procedura:

1. Aggiunta di un tag **SequenceStart** e **SequenceStop** con un valore intero positivo per le macchine virtuali nella sottoscrizione che sono assegnate nelle variabili **External_Start_ResourceGroupNames** e **External_Stop*ResourceGroupNames**.  Le azioni di avvio e arresto verranno eseguite in ordine crescente.  Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come contrassegnare una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md)
2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** alla data e ora per soddisfare i requisiti e abilitare la pianificazione.  

Per testare e convalidare la configurazione, avviare manualmente il runbook **SequencedStartStop_Parent** e impostare il parametro *ACTION* su **start** o **stop** e il parametro *WhatIf* su **True**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Ciò consente di visualizzare in anteprima l'azione che si verifica e apportare le modifiche necessarie prima dell'implementazione nelle macchine virtuali di produzione.  Una volta che si ha dimestichezza, è possibile eseguire manualmente il runbook con il parametro impostato su **false** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Scenario 3: Avviare/arrestare automaticamente le macchine virtuali in una sottoscrizione basata sull'utilizzo della CPU
Per agevolare la gestione dei costi di esecuzione delle macchine virtuali nella sottoscrizione, questa soluzione può essere utile perché valuta le macchine virtuali di Azure non utilizzate durante i periodi non di punta, ad esempio dopo ore e le arresta automaticamente se l'uso del processore è inferiore a x%.  

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica Percentuale CPU e se l'utilizzo medio è pari o inferiore al 5%.  Questo è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

È possibile abilitare la selezione della destinazione dell'azione solo su una sottoscrizione e un gruppo di risorse o su un elenco specifico di macchine virtuali, ma non su entrambi.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destinare l'azione di arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.  
2. Abilitare e aggiornare la pianificazione **Schedule_AutoStop_CreateAlert_Parent**.
3. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro *ACTION* impostato su **start** e il parametro *WhatIf* impostato su **True** per visualizzare in anteprima le modifiche.

#### <a name="target-stop-action-by-vm-list"></a>Destinare l'azione di arresto con l'elenco delle macchine virtuali

1. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro *ACTION* impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e il parametro *WhatIf* impostato su **True** per visualizzare in anteprima le modifiche.  
2. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).
3. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupnames**.  Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

Dopo aver creato una pianificazione per arrestare le macchine virtuali in base all'utilizzo della CPU, è necessario abilitare una delle pianificazioni seguenti per avviarle.

* Destinare l'azione di avvio con una sottoscrizione e un gruppo di risorse.  Vedere i passaggi descritti nello [Scenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) per testare e abilitare la pianificazione **Scheduled-StartVM**.
* Destinare l'azione di avvio con una sottoscrizione, un gruppo di risorse e un tag.  Vedere i passaggi descritti nello [Scenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) per testare e abilitare la pianificazione "Sequenced-StartVM".


### <a name="configuring-e-mail-notifications"></a>Configurazione delle notifiche tramite posta elettronica

Per configurare le notifiche tramite posta elettronica dopo la distribuzione della soluzione, è possibile modificare le tre variabili seguenti:

* External_EmailFromAddress: specificare l'indirizzo di posta elettronica del mittente
* External_EmailToAddress: elenco separato da virgole di indirizzi di posta elettronica (user@hotmail.com, user@outlook.com) per ricevere notifiche tramite posta elettronica
* External_IsSendEmail: se impostato su **Sì** si riceveranno messaggi di posta elettronica, mentre per disabilitare le notifiche di posta elettronica impostare il valore su **No**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modifica delle pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Record di Log Analytics

Automazione crea due tipi di record nel repository OMS.

### <a name="job-logs"></a>Log di processo

Proprietà | Descrizione|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobLogs.|
CorrelationId | Il GUID che rappresenta l'ID di correlazione del processo di runbook.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore sarà Job.|
resourceId | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Lo stato del processo di runbook.  I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato|
resultDescription | Descrive lo stato del risultato del processo di runbook.  I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato|
RunbookName | Specifica il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager.|
StreamType | Specifica il tipo di evento. I valori possibili sono:<br>- Dettagliato<br>- Output<br>- Errore<br>- Avviso|
SubscriptionId | Specifica l'ID sottoscrizione del processo.
Time | Data e ora di esecuzione del processo del runbook.|


### <a name="job-streams"></a>Flussi di processo

Proprietà | Descrizione|
----------|----------|
Chiamante |  Chi ha avviato l'operazione.  I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
Categoria | La classificazione del tipo di dati.  Per Automazione, il valore è JobStreams.|
JobId | Il GUID che rappresenta l'ID del processo del runbook.|
operationName | Specifica il tipo di operazione eseguita in Azure.  Per Automazione, il valore sarà Job.|
ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
resourceId | Specifica l'ID risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire.  Per Automazione, il valore è Automazione di Azure.|
ResourceType | Specifica il tipo di risorsa in Azure.  Per Automazione, il valore è l'account di Automazione associato al runbook.|
resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento.  I valori possibili sono:<br>- InProgress|
resultDescription | Include il flusso di output dal runbook.|
RunbookName | Il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager.|
StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
Time | Data e ora di esecuzione del processo del runbook.|

Quando si esegue una ricerca di log che restituisce record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare la visualizzazione **JobLogs** o **JobStreams** che presenta una serie di riquadri di riepilogo degli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da questa soluzione. 

Query | Descrizione|
----------|----------|
Trova i processi completati per il runbook ScheduledStartStop_Parent | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Trova i processi completati per il runbook SequencedStartStop_Parent | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>Rimuovere la soluzione

Se non c'è più bisogno di usare la soluzione, è possibile eliminarla dall'account di Automazione.  Eliminando la soluzione verranno rimossi soltanto i runbook, mentre le pianificazioni e le variabili create quando è stata aggiunta la soluzione non saranno eliminate.  Tali asset dovranno essere eliminati manualmente se non vengono usati con altri runbook.  

Per eliminare la soluzione, attenersi alla procedura seguente:

1.  Dall'account di Automazione selezionare **Area di lavoro** dal riquadro di sinistra.  
2.  Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).  Nella pagina **VMManagementSolution[Workspace]** fare clic su **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Nella finestra **Elimina soluzione** confermare di voler eliminare la soluzione.
4.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu.  Quando l'operazione di rimozione della soluzione inizia, l'utente viene rimandato alla pagina **Soluzioni**.  

L'account di Automazione e l'area di lavoro di Log Analytics non vengono eliminati come parte del processo.  Se non si vuole conservare l'area di lavoro di Log Analytics, è necessario eliminarla manualmente.  Tale operazione può essere eseguita anche dal portale di Azure.   Nella schermata iniziale del portale di Azure selezionare **Log Analytics** per aprire il pannello **Log Analytics** in cui selezionare l'area di lavoro e fare clic su **Elimina** nel menu del pannello impostazioni dell'area di lavoro.  
      
## <a name="next-steps"></a>Passaggi successivi

- Per alte informazioni su come creare query di ricerca diverso ed esaminare i log del processo di automazione con Log Analytics, vedere [Ricerche nei log in Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
- Per altre informazioni su Log Analytics e sulle origini di raccolta di dati, vedere [Raccolta di log e metriche per i servizi di Azure da usare in Log Analytics](../log-analytics/log-analytics-azure-storage.md)






   


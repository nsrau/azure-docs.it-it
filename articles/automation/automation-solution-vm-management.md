---
title: "Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) | Microsoft Docs"
description: Questa soluzione di gestione di VM avvia e arresta le macchine virtuali di Azure Resource Manager in base a una pianificazione e le monitora in modo attivo da Log Analytics.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima) in Automazione di Azure

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività avvia e arresta le macchine virtuali di Azure in base a pianificazioni definite dall'utente, fornisce informazioni dettagliate tramite Azure Log Analytics e invia messaggi di posta elettronica facoltativi usando [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. 

Questa soluzione fornisce un'opzione di automazione decentralizzata per gli utenti che vogliono ridurre i costi usando risorse senza server a basso costo. Con questa soluzione, è possibile:

* Pianificare le macchine virtuali per l'avvio e l'arresto.
* Pianificare le macchine virtuali per l'avvio e l'arresto in ordine crescente usando i tag di Azure (non supportati per le macchine virtuali classiche).
* Arrestare automaticamente le macchine virtuali in caso di basso utilizzo della CPU.

## <a name="prerequisites"></a>prerequisiti

- I runbook funzionano con un [account RunAs di Azure](automation-offering-get-started.md#authentication-methods).  L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che potrebbe scadere o essere modificata di frequente.  

- Questa soluzione gestisce solo le macchine virtuali nella stessa sottoscrizione dell'account di Automazione di Azure.  

- È possibile eseguire la distribuzione solo nelle aree di Azure seguenti: Australia sud-orientale, Canada centrale, India centrale, Stati Uniti orientali, Giappone orientale, Asia sud-orientale, Regno Unito meridionale ed Europa occidentale.  
    
    > [!NOTE]
    > I runbook che gestiscono la pianificazione delle macchine virtuali possono essere usati in qualsiasi area.  

- Per inviare notifiche tramite posta elettronica quando l'avvio e l'arresto dei runbook delle macchine virtuali finiscono, durante l'onboarding da Azure Marketplace selezionare **Sì** per la distribuzione di SendGrid. 

    > [!IMPORTANT]
    > SendGrid è un servizio di terze parti. Per il supporto, contattare [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Le limitazioni con SendGrid sono:

    * Un massimo di un account SendGrid per utente per ogni sottoscrizione.
    * Un massimo di due account SendGrid per ogni sottoscrizione.

Se è stata distribuita una versione precedente di questa soluzione, è necessario prima di tutto eliminarla dall'account prima di distribuire questa versione.  

## <a name="solution-components"></a>Componenti della soluzione

Questa soluzione include runbook preconfigurati, pianificazioni e integrazione con Log Analytics per poter personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali. 

### <a name="runbooks"></a>Runbook

La tabella seguente elenca i runbook distribuiti nell'account di Automazione.  Non è consigliabile apportare modifiche al codice del runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente i runbook con "child" accodato al nome.
>

Tutti i runbook padre includono il parametro *WhatIf*. Se impostato su **True**, *WhatIf* supporta i dettagli relativi al comportamento esatto del runbook quando viene eseguito senza il parametro *WhatIf* e verifica che vengano specificate come destinazione le macchine virtuali corrette.  Quando il parametro *WhatIf* è impostato su **False**, un runbook esegue solo le azioni definite. 

|**Runbook** | **Parameters** | **Descrizione**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato soltanto dal runbook padre. Crea gli avvisi in base alle risorse per lo scenario AutoStop.| 
|AutoStop_CreateAlert_Parent | WhatIf: True o False <br> VMList | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> VMList: Elenco delimitato da virgole delle macchine virtuali.  Ad esempio, *vm1,vm2,vm3*.| 
|AutoStop_Disable | Nessuno | Disabilita gli avvisi di AutoStop e la pianificazione predefinita.| 
|AutoStop_StopVM_Child | WebHookData | Chiamato soltanto dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare la VM.|  
|Bootstrap_Main | Nessuno | Usato una sola volta per impostare le configurazioni di bootstrap come webhookURI, che in genere non sono accessibili da Azure Resource Manager. Questo runbook viene rimosso automaticamente dopo il completamento della distribuzione.|  
|ScheduledStartStop_Child | VMName <br> Azione: Arresto o Avvio <br> ResourceGroupName | Chiamato soltanto dal runbook padre. Esegue l'arresto o l'avvio per l'arresto pianificato.|  
|ScheduledStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | Ha effetto su tutte le VM della sottoscrizione. Modificare **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupNames** in modo che vengano eseguiti solo in questi gruppi di risorse di destinazione. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile **External_ExcludeVMNames**. *WhatIf* si comporta come negli altri runbook.|  
|SequencedStartStop_Parent | Azione: Arresto o Avvio <br> WhatIf: True o False | Crea i tag denominati **SequenceStart** e **SequenceStop** in ogni VM per cui si vuole mettere in sequenza l'attività di avvio/arresto. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine in cui si vuole eseguire l'avvio o l'arresto. *WhatIf* si comporta come negli altri runbook. <br> **Nota**: le macchine virtuali devono trovarsi all'interno di gruppi di risorse definiti come External_Start_ResourceGroupNames External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>variables

La tabella seguente elenca le variabili create nell'account di Automazione.  È consigliabile modificare solo le variabili con il prefisso **External**. La modifica delle variabili con il prefisso **Internal** provoca effetti indesiderati.  

|**Variabile** | **Descrizione**|
---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori possibili sono: **GreaterThan**, **GreaterThanOrEqual**, **LessThan** e **LessThanOrEqual**.|  
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|  
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.| 
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile *External_AutoStop_MetricName*. I valori di percentuale possono variare da 1 a 100.|  
|External_AutoStop_TimeAggregationOperator | Operatore dell'aggregazione temporale che viene applicato alle dimensioni della finestra selezionata per valutare la condizione. I valori possibili sono: **Average**, **Minimum**, **Maximum**, **Total** e **Last**.|  
|External_AutoStop_TimeWindow | Dimensioni della finestra durante le quali Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|  
|External_EmailFromAddress | Specifica il mittente del messaggio di posta elettronica.|  
|External_EmailSubject | Specifica il testo per la riga dell'oggetto del messaggio di posta elettronica.|  
|External_EmailToAddress | Specifica i destinatari del messaggio di posta elettronica. Separare i nomi usando una virgola.|  
|External_ExcludeVMNames | Immettere i nomi delle macchine virtuali da escludere, separandoli usando una virgola senza spazi.|  
|External_IsSendEmail | Specifica l'opzione per inviare la notifica tramite posta elettronica al termine dell'operazione.  Specificare **Sì** o **No** per non inviare il messaggio di posta elettronica.  Questa opzione deve essere **No** se non sono state abilitate le notifiche tramite posta elettronica durante la distribuzione iniziale.|  
|External_Start_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di avvio.|  
|External_Stop_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di arresto.|  
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|  
|Internal_AutoSnooze_WebhookUri | Specifica l'URI del Webhook chiamato per lo scenario AutoStop.|  
|Internal_AzureSubscriptionId | Specifica l'ID sottoscrizione di Azure.|  
|Internal_ResourceGroupName | Specifica il nome del gruppo di risorse dell'account di Automazione.|  
|Internal_SendGridAccountName | Specifica il nome dell'account di SendGrid.|  
|Internal_SendGridPassword | Specifica la password dell'account di SendGrid.|  

<br>

In tutti gli scenari le variabili **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames** sono necessarie per le macchine virtuali di destinazione, con l'eccezione di fornire un elenco separato da virgole delle macchine virtuali per il runbook **AutoStop_CreateAlert_Parent**. In altre parole, le macchine virtuali devono risiedere nei gruppi di risorse di destinazione perché si verifichino le azioni di avvio e arresto. La logica funziona un po' come i criteri di Azure, perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e avere azioni ereditate dalle macchine virtuali appena create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione.  È possibile modificarle o creare le proprie pianificazioni personalizzate.  Ognuna è disabilitata per impostazione predefinita, a eccezione di **Scheduled_StartVM** e **Scheduled-StopVM**.

Non è consigliabile abilitare tutte le pianificazioni, perché potrebbe verificarsi una sovrapposizione delle azioni di pianificazione. È meglio determinare quali ottimizzazioni si vogliono eseguire e apportare le modifiche di conseguenza.  Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.   

|**Nome pianificazione** | **Frequenza** | **Descrizione**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook AutoStop_CreateAlert_Parent ogni 8 ore, che a sua volta arresta i valori basati sulla macchina virtuale in External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure.  In alternativa, è possibile specificare un elenco separato da virgole delle macchine virtuali usando il parametro VMList.|  
|Scheduled_StopVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro *Stop* ogni giorno all'ora specificata.  Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili di asset. È consigliabile abilitare la pianificazione correlata, **Scheduled-StartVM**.|  
|Scheduled_StartVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro *Start* ogni giorno all'ora specificata.  Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili appropriate.  È consigliabile abilitare la pianificazione correlata, **Scheduled-StopVM**.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue il runbook Sequenced_Parent con un parametro *Stop* ogni venerdì all'ora specificata.  Arresterà in sequenza (crescente) tutte le macchine virtuali con un tag **SequenceStop** definito dalle variabili appropriate.  Vedere la sezione Runbook per altre informazioni sui valori di tag e sulle variabili di asset.  È consigliabile abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue il runbook Sequenced_Parent con un parametro *Start* ogni lunedì a una determinata ora. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate.  Vedere la sezione Runbook per altre informazioni sui valori di tag e sulle variabili di asset.  È consigliabile abilitare la pianificazione correlata, **Sequenced-StopVM**.|

<br>

## <a name="configuration"></a>Configurazione

Seguire questa procedura per aggiungere la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività all'account di Automazione e quindi configurare le variabili per personalizzarla.

1. Nel portale di Azure fare clic su **Nuovo**.<br> ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. Nel riquadro Marketplace digitare una parola chiave, ad esempio **Avvio** o **Avvio/Arresto**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare una o più parole chiave contenute nel nome completo della soluzione e quindi premere INVIO.  Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** dai risultati della ricerca.  
3. Nel riquadro **Avvio/Arresto di macchine virtuali durante gli orari di minore attività (anteprima)** per la soluzione selezionata, esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.  
4. Viene visualizzato il riquadro **Aggiungi soluzione**. Viene richiesto di configurare la soluzione prima di importarla nella sottoscrizione di Automazione.<br><br> ![Pannello Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  Nel riquadro **Aggiungi soluzione** selezionare **Area di lavoro**. Selezionare un'area di lavoro di OMS collegata alla stessa sottoscrizione di Azure in cui è incluso l'account di Automazione. Se non è disponibile un'area di lavoro, selezionare **Crea una nuova area di lavoro**. Nel riquadro **Area di lavoro di OMS** seguire questa procedura: 
   - Specificare un nome per la nuova **area di lavoro OMS**.
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.  
   - Selezionare un **percorso**.  Attualmente le uniche località disponibili sono: **Australia sud-orientale**, **Canada centrale**, **India centrale**, **Stati Uniti orientali**, **Giappone orientale**, **Asia sud-orientale**, **Regno Unito meridionale** ed **Europa occidentale**.
   - Selezionare un **Piano tariffario**.  La soluzione offre due livelli: gratuito e OMS a pagamento.  Il livello gratuito presenta un limite per la quantità di dati raccolti al giorno, il periodo di conservazione e i minuti di esecuzione dei processi dei runbook.  Il livello OMS a pagamento non ha limiti per la quantità di dati raccolti al giorno.  

        > [!NOTE]
        > Anche se è visualizzato come opzione, il livello a pagamento Per GB (autonomo) non è applicabile.  Se si seleziona e si procede con la creazione della soluzione nella sottoscrizione, l'operazione ha esito negativo.  Questo problema sarà risolto al momento del rilascio ufficiale della soluzione.<br>Questa soluzione usa solo l'inserimento di log e i minuti di esecuzione dei processi di automazione.  Non aggiunge altri nodi OMS all'ambiente in uso.  

6. Dopo aver specificato le informazioni necessarie nel riquadro **Area di lavoro di OMS**, fare clic su **Crea**.  È possibile monitorarne lo stato scegliendo **Notifiche** dal menu. Al termine, si tornerà al riquadro **Aggiungi soluzione**.  
7. Nel riquadro **Aggiungi soluzione** selezionare **Account di Automazione**.  Se si sta creando una nuova area di lavoro di OMS, è anche necessario creare un nuovo account di Automazione da associare.  Selezionare **Crea un account di Automazione** e specificare le informazioni seguenti nel riquadro **Aggiungi account di Automazione**: 
  - Nel campo **Nome** immettere il nome dell'account di Automazione.

    Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro OMS selezionata Queste opzioni non possono essere modificate.  Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure.  Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione.  Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

    In alternativa, è possibile selezionare un account RunAs di Automazione esistente.  Si noti che l'account selezionato non può essere già collegato a un'altra area di lavoro OMS. Se è già collegato, si riceve un messaggio ed è necessario selezionare un account RunAs di Automazione diverso o crearne uno nuovo.<br><br> ![Account di Automazione già collegato all'area di lavoro OMS](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Nel riquadro **Aggiungi soluzione** selezionare infine **Configurazione**. Viene visualizzato il riquadro **Parametri**.<br><br> ![Riquadro dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  In questo caso, viene chiesto di:  
   - Specificare i **nomi dei gruppi di risorse di destinazione**. Si tratta di nomi di gruppi di risorse contenenti le macchine virtuali che devono essere gestite da questa soluzione.  È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole.  Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly.
   - Specificare l'**elenco di esclusione delle VM (stringa)**. Si tratta del nome di una o più macchine virtuali dal gruppo di risorse di destinazione.  È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole.  I caratteri jolly sono supportati.
   - Selezionare una **pianificazione**. Si tratta di una data e un'ora ricorrenti per l'avvio e l'arresto delle macchine virtuali nei gruppi di risorse di destinazione.  Per impostazione predefinita, la pianificazione è configurata per il fuso orario UTC. Non è possibile selezionare un'altra area.  Per configurare la pianificazione per il fuso orario specifico dopo aver configurato la soluzione, vedere [Modifica della pianificazione di avvio e arresto](#modifying-the-startup-and-shutdown-schedule).
   - Per ricevere **notifiche tramite posta elettronica** da SendGrid, accettare il valore predefinito **Sì** e fornire un indirizzo di posta elettronica valido. Se si seleziona **No**, ma in seguito si decide di voler ricevere le notifiche tramite posta elettronica, è possibile aggiornare la variabile **External_EmailToAddress** con gli indirizzi di posta elettronica validi separati da virgola e quindi modificare la variabile **External_IsSendEmail** con il valore **Sì**.  

9. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, selezionare **Crea**.  Dopo la convalida di tutte le impostazioni, la soluzione viene distribuita nella sottoscrizione.  Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu. 

## <a name="collection-frequency"></a>Frequenza della raccolta

Il log del processo di Automazione e i dati del flusso del processo vengono inseriti nel repository di Log Analytics ogni 5 minuti.  

## <a name="using-the-solution"></a>Uso della soluzione

Quando si aggiunge la soluzione di gestione delle VM, il riquadro **StartStopVMView** viene aggiunto al dashboard nell'area di lavoro di Log Analytics dal portale di Azure.  Questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Riquadro StartStopVM View (Visualizzazione di avvio e arresto di VM) di Virtual Machine Management](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

Nell'account di Automazione, è possibile accedere e gestire la soluzione selezionando l'opzione **Area di lavoro**. Nella pagina Log Analytics selezionare **Soluzioni** nel riquadro a sinistra.  Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]) dall'elenco.<br><br> ![Elenco delle soluzioni in Log Analytics](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Selezionando la soluzione, viene visualizzato il riquadro della soluzione **Start-Stop-VM[workspace]** (Avvio-Arresto-VM[Area di lavoro]), dove è possibile esaminare importanti dettagli, ad esempio il riquadro **StartStopVM** (Avvio e arresto di VM). Come nell'area di lavoro di Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.<br><br> ![Pagina della soluzione della gestione di aggiornamenti di automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Da qui è possibile eseguire un'analisi aggiuntiva dei record dei processi facendo clic sul riquadro ad anello. Il dashboard della soluzione mostra la cronologia processo e le query di ricerca log predefinite. Passare al portale avanzato di Log Analytics per eseguire una ricerca in base alle query di ricerca.  

Tutti i runbook padre includono il parametro *WhatIf*. Se impostato su **True**, supporta i dettagli relativi al comportamento esatto del runbook quando viene eseguito senza il parametro *WhatIf* e verifica che vengano specificate come destinazione le macchine virtuali corrette. Quando il parametro *WhatIf* è impostato su **False** i runbook eseguono solo le azioni definite.  


### <a name="scenario-1-daily-startstop-vms-across-a-subscription-or-target-resource-groups"></a>Scenario 1: Avvio/Arresto giornaliero delle macchine virtuali in una sottoscrizione o nei gruppi di risorse di destinazione 

Questa è la configurazione predefinita al momento della prima distribuzione della soluzione.  Ad esempio, è possibile configurarla per poter arrestare tutte le macchine virtuali in una sottoscrizione quando si lascia il luogo di lavoro la sera e avviarle al mattino quando si ritorna in ufficio. Quando si configurano le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM** durante la distribuzione, queste avviano e arrestano le macchine virtuali di destinazione.
>[!NOTE]
>Il fuso orario è il fuso orario corrente quando si configura il parametro dell'orario di pianificazione. Viene tuttavia archiviato nel formato UTC in Automazione di Azure.  Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione.

È possibile controllare quali macchine virtuali rientrano nell'ambito configurando le variabili seguenti: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames**.  

>[!NOTE]
> Il valore per **Target ResourceGroup Names** (Nomi dei gruppi di risorse di destinazione) viene archiviato come valore sia per **External_Start_ResourceGroupNames** che per **External_Stop_ResourceGroupNames**. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse.  Per l'azione di avvio, usare **External_Start_ResourceGroupNames** e, per l'azione di arresto, usare **External_Stop_ResourceGroupNames**. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

Per testare e convalidare la configurazione, avviare manualmente il runbook **ScheduledStartStop_Parent** e impostare il parametro ACTION su **start** o **stop** e il parametro WHATIF su **true**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)


 Visualizzare in anteprima l'azione pianificata e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione.  È possibile eseguire manualmente il runbook con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Schedule-StartVM** e **Schedule-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

### <a name="scenario-2-sequence-the-startstop-vms-across-a-subscription-by-using-tags"></a>Scenario 2: Sequenza di avvio/arresto delle macchie virtuali in una sottoscrizione tramite i tag
In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine.  A tale scopo, seguire questa procedura:

1. Aggiungere un tag **SequenceStart** e un tag **SequenceStop** con un valore intero positivo alle VM specificate come destinazione nelle variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupNames**.  Le azioni di avvio e arresto vengono eseguite in ordine crescente.  Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).
2. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.  

Per testare e convalidare la configurazione, avviare manualmente il runbook **SequencedStartStop_Parent**. Impostare il parametro ACTION su **start** o su **stop** e il parametro WHATIF su **True**.<br><br> ![Configurare i parametri per il runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione.  Quando si è pronti, è possibile eseguire manualmente il runbook con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.  

### <a name="scenario-3-automate-startstop-vms-across-a-subscription-based-on-cpu-utilization"></a>Scenario 3: Automatizzare l'avvio/arresto delle macchine virtuali in una sottoscrizione in base all'utilizzo della CPU
Questa soluzione consente di gestire i costi di esecuzione delle macchine virtuali nella sottoscrizione valutando le macchine virtuali di Azure non usate durante i periodi non di punta, ad esempio dopo l'orario di lavoro, e arrestandole automaticamente se l'utilizzo del processore è inferiore a x%.  

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%.  Questo è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

È possibile consentire di specificare come destinazione dell'azione una sottoscrizione e un gruppo di risorse oppure un elenco specifico di macchine virtuali, ma non entrambi.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destinare l'azione di arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.  
2. Abilitare e aggiornare la pianificazione **Schedule_AutoStop_CreateAlert_Parent**.
3. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

#### <a name="target-the-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di arresto in base a un elenco di macchine virtuali

1. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.  
2. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1,VM2,VM3).
3. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**.  Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

Dopo aver creato una pianificazione per arrestare le macchine virtuali in base all'utilizzo della CPU, è necessario abilitare una delle pianificazioni seguenti per avviarle.

* Specificare la destinazione dell'azione di avvio in base a una sottoscrizione e un gruppo di risorse.  Vedere i passaggi descritti nello [Scenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) per testare e abilitare la pianificazione **Scheduled-StartVM**.
* Specificare la destinazione dell'azione di avvio in base a una sottoscrizione, un gruppo di risorse e un tag.  Vedere i passaggi descritti nello [Scenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) per testare e abilitare la pianificazione **Sequenced-StartVM**.


### <a name="configuring-email-notifications"></a>Configurazione delle notifiche tramite posta elettronica

Per configurare le notifiche tramite posta elettronica dopo la distribuzione della soluzione, modificare le tre variabili seguenti:

* External_EmailFromAddress: specificare l'indirizzo di posta elettronica del mittente.
* External_EmailToAddress: specificare un elenco separato da virgole di indirizzi di posta elettronica (user@hotmail.com, user@outlook.com) per ricevere notifiche tramite posta elettronica.
* External_IsSendEmail: impostare su **Sì** per ricevere i messaggi di posta elettronica. Per disabilitare le notifiche tramite posta elettronica, impostare il valore su **No**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Modifica delle pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md).     

## <a name="log-analytics-records"></a>Record di Log Analytics

Automazione crea due tipi di record nel repository OMS: log di processo e flussi di processo.

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
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager|
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
resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento. Un valore possibile è:<br>- InProgress|
resultDescription | Include il flusso di output dal runbook.|
RunbookName | Il nome del runbook.|
SourceSystem | Specifica il sistema di origine per i dati inviati.  Per Automazione, il valore è OpsManager.|
StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
Tempo | Data e ora di esecuzione del processo del runbook.|

Quando si esegue una ricerca log che restituisce record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare la visualizzazione **JobLogs** o **JobStreams** che presenta una serie di riquadri di riepilogo degli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da questa soluzione. 

Query | DESCRIZIONE|
----------|----------|
Trova i processi completati per il runbook ScheduledStartStop_Parent | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Trova i processi completati per il runbook SequencedStartStop_Parent | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="removing-the-solution"></a>Rimuovere la soluzione

Se non è più necessario usare la soluzione, è possibile eliminarla dall'account di Automazione.  Eliminando la soluzione, vengono rimossi solo i runbook. Non vengono eliminate le pianificazioni o le variabili create quando la soluzione è stata aggiunta.  Tali asset devono essere eliminati manualmente se non vengono usati con altri runbook.  

Per eliminare la soluzione, attenersi alla procedura seguente:

1.  Dall'account di Automazione selezionare **Area di lavoro** dal riquadro di sinistra.  
2.  Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]).  Nella pagina **VMManagementSolution[area di lavoro]** scegliere **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  Nella finestra **Elimina soluzione** confermare di voler eliminare la soluzione.
4.  Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu.  Quando il processo di rimozione della soluzione inizia, l'utente viene rimandato alla pagina **Soluzioni**.  

L'account di Automazione e l'area di lavoro di Log Analytics non vengono eliminati come parte del processo.  Se non si vuole conservare l'area di lavoro di Log Analytics, è necessario eliminarla manualmente.  Tale operazione può essere eseguita dal portale di Azure:

1.    Nella schermata iniziale del portale di Azure selezionare **Log Analytics**.
2. Nel riquadro **Log Analytics** selezionare l'area di lavoro.
3. Scegliere **Elimina** dal menu nel riquadro delle impostazioni dell'area di lavoro.  
      
## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come creare query di ricerca diverse ed esaminare i log di processo di Automazione con Log Analytics, vedere [Ricerche log in Log Analytics](../log-analytics/log-analytics-log-searches.md).
- Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
- Per altre informazioni su Log Analytics e sulle origini di raccolta dati, vedere [Panoramica della raccolta di dati di archiviazione di Azure in Log Analytics](../log-analytics/log-analytics-azure-storage.md).






   


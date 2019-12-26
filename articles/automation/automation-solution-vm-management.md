---
title: Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività
description: Questa soluzione di gestione delle VM avvia e arresta le macchine virtuali Azure Resource Manager in base a una pianificazione e monitora in modo proattivo i log di monitoraggio di Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0b022ed759837fc6d922386dd48a2f3a109527a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951497"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività in Automazione di Azure

La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività avvia e arresta le macchine virtuali di Azure in base alle pianificazioni definite dall'utente, fornisce informazioni dettagliate sui log di monitoraggio di Azure e invia messaggi di posta elettronica facoltativi tramite i [gruppi di azioni](../azure-monitor/platform/action-groups.md). Supporta sia Azure Resource Manager che le macchine virtuali classiche per la maggior parte degli scenari. Per usare questa soluzione con le macchine virtuali classiche, è necessario un account RunAs classico, che non viene creato per impostazione predefinita. Per istruzioni sulla creazione di un account RunAs classico, vedere account RunAs [classici](automation-create-standalone-account.md#classic-run-as-accounts).

> [!NOTE]
> La soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività è stata testata con i moduli di Azure che sono stati importati nell'account di automazione quando si distribuisce la soluzione. La soluzione attualmente non funziona con le versioni più recenti del modulo Azure. Questa operazione ha effetto solo sull'account di automazione usato per eseguire la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività. È comunque possibile usare le versioni più recenti del modulo Azure negli altri account di automazione, come descritto in [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md)

Questa soluzione fornisce un'opzione di automazione decentralizzata a basso costo per gli utenti che vogliono ottimizzare i costi delle macchine virtuali. Con questa soluzione, è possibile:

- Pianificare le macchine virtuali per l'avvio e l'arresto.
- Pianificare le macchine virtuali per l'avvio e l'arresto in ordine crescente usando i tag di Azure (non supportati per le macchine virtuali classiche).
- Arrestare automaticamente le macchine virtuali in caso di basso utilizzo della CPU.

Di seguito sono riportate le limitazioni della soluzione corrente:

- Questa soluzione gestisce macchine virtuali in qualsiasi area, ma può essere usata solo nella stessa sottoscrizione dell'account di Automazione di Azure.
- Questa soluzione è disponibile in Azure e Azure per enti pubblici per tutte le aree che supportano un'area di lavoro Log Analytics, un account di Automazione di Azure e Avvisi. Le aree di Azure per enti pubblici attualmente non supportano funzionalità di posta elettronica.

> [!NOTE]
> Se si usa la soluzione per le macchine virtuali classiche, tutte le macchine virtuali verranno elaborate in sequenza per ogni servizio cloud. Le macchine virtuali vengono comunque elaborate in parallelo tra servizi cloud diversi. Se sono presenti più di 20 macchine virtuali per ogni servizio cloud, è consigliabile creare più pianificazioni con la **ScheduledStartStop_Parent** padre Runbook e specificare 20 VM per ogni pianificazione. Nelle proprietà della pianificazione specificare come elenco delimitato da virgole i nomi delle macchine virtuali nel parametro **VMList** . In caso contrario, se il processo di automazione per questa soluzione viene eseguito più di tre ore, viene temporaneamente scaricato o arrestato in base al limite di [condivisione equa](automation-runbook-execution.md#fair-share) .
>
> Le sottoscrizioni Cloud Solution Provider di Azure (Azure CSP) supportano solo il modello Azure Resource Manager, i servizi non Azure Resource Manager non sono disponibili nel programma. Quando è in esecuzione la soluzione Avvio/Arresto, è possibile che vengano visualizzati degli errori perché le risorse classiche sono gestite dai cmdlet. Per altre informazioni su CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments). Se si usa una sottoscrizione di CSP, è necessario modificare la variabile [**External_EnableClassicVMs** ](#variables) su **False** dopo la distribuzione.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

I runbook per questa soluzione funzionano con un [account RunAs di Azure](automation-create-runas-account.md). L'account RunAs è il metodo di autenticazione preferito perché usa l'autenticazione del certificato anziché una password, che potrebbe scadere o essere modificata di frequente.

Si consiglia di usare un account di automazione separato per la soluzione di avvio/arresto della macchina virtuale. Questo perché le versioni dei moduli di Azure vengono aggiornate di frequente e i relativi parametri possono cambiare. La soluzione di avvio/arresto della macchina virtuale non viene aggiornata con la stessa cadenza, quindi potrebbe non funzionare con le versioni più recenti dei cmdlet usati. Si consiglia anche di testare gli aggiornamenti dei moduli in un account di automazione di test prima di importarli negli account di automazione di produzione.

### <a name="permissions-needed-to-deploy"></a>Autorizzazioni necessarie per la distribuzione

Esistono determinate autorizzazioni che un utente deve avere per distribuire la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità. Queste autorizzazioni sono diverse se si usa un account di automazione creato in precedenza e si Log Analytics area di lavoro o se ne creano di nuovi durante la distribuzione. Se si è un collaboratore per la sottoscrizione e un amministratore globale nel tenant di Azure Active Directory, non è necessario configurare le autorizzazioni seguenti. Se non si dispone di questi diritti o se è necessario configurare un ruolo personalizzato, vedere le autorizzazioni richieste di seguito.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>Account di automazione preesistente e area di lavoro Log Analytics

Per distribuire la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità in un account di automazione esistente e in Log Analytics area di lavoro, l'utente che distribuisce la soluzione richiede le autorizzazioni seguenti per il **gruppo di risorse**. Per altre informazioni sui ruoli, vedere [ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md).

| Autorizzazione | Scope|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/variables/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/schedules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/runbooks/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/connections/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/modules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/modules/read | Gruppo di risorse |
| Microsoft.automation/automationAccounts/jobSchedules/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/write | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/jobs/read | Gruppo di risorse |
| Microsoft.OperationsManagement/solutions/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/* | Gruppo di risorse |
| Microsoft.Insights/diagnosticSettings/write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/Write | Gruppo di risorse |
| Microsoft.Insights/ActionGroups/read | Gruppo di risorse |
| Microsoft.Resources/subscriptions/resourceGroups/read | Gruppo di risorse |
| Microsoft.Resources/deployments/* | Gruppo di risorse |

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>Nuovo account di automazione e una nuova area di lavoro Log Analytics

Per distribuire la soluzione avvio/arresto di macchine virtuali durante gli orari di indisponibilità in un nuovo account di automazione e in Log Analytics area di lavoro, l'utente che distribuisce la soluzione deve avere le autorizzazioni definite nella sezione precedente, nonché le autorizzazioni seguenti:

- Coamministratore nella sottoscrizione: è necessario solo per creare l'account RunAs classico se si intende gestire le VM classiche. Per impostazione predefinita, gli [account RunAs classici](automation-create-standalone-account.md#classic-run-as-accounts) non vengono più creati.
- Membro del [ruolo](../active-directory/users-groups-roles/directory-assign-admin-roles.md) di **sviluppatore dell'applicazione** Azure Active Directory. Per ulteriori informazioni sulla configurazione di account RunAs, vedere [autorizzazioni per la configurazione di account RunAs](manage-runas-account.md#permissions).
- Collaboratore per la sottoscrizione o per le autorizzazioni seguenti.

| Autorizzazione |Scope|
| --- | --- |
| Microsoft.Authorization/Operations/read | Sottoscrizione|
| Microsoft.Authorization/permissions/read |Sottoscrizione|
| Microsoft.Authorization/roleAssignments/read | Sottoscrizione |
| Microsoft.Authorization/roleAssignments/write | Sottoscrizione |
| Microsoft.Authorization/roleAssignments/delete | Sottoscrizione |
| Microsoft.Automation/automationAccounts/connections/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/certificates/read | Gruppo di risorse |
| Microsoft.Automation/automationAccounts/write | Gruppo di risorse |
| Microsoft.OperationalInsights/workspaces/write | Gruppo di risorse |

## <a name="deploy-the-solution"></a>Distribuire la soluzione

Seguire questa procedura per aggiungere la soluzione Avvio/Arresto di macchine virtuali durante gli orari di minore attività all'account di Automazione e quindi configurare le variabili per personalizzarla.

1. Da un account di Automazione selezionare **Avvio/Arresto di macchine virtuali** in **Risorse correlate**. Da qui è possibile fare clic su **Learn more about and enable the solution** (Scopri di più e abilita la soluzione). Se si è già distribuita una soluzione Avvio/Arresto di macchine virtuali, è possibile selezionarla facendo clic su **Gestisci la soluzione** e cercarla nell'elenco.

   ![Abilitare dall'account di automazione](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > È possibile anche crearla da qualsiasi posizione nel portale di Azure, facendo clic su **Crea una risorsa**. Nella pagina del Marketplace digitare una parola chiave, ad esempio **Avvio** o **Avvio/Arresto**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. In alternativa, è possibile digitare una o più parole chiave contenute nel nome completo della soluzione e quindi premere INVIO. Selezionare **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** dai risultati della ricerca.

2. Nella pagina **Avvio/Arresto di macchine virtuali durante gli orari di minore attività** per la soluzione selezionata esaminare le informazioni di riepilogo e quindi fare clic su **Crea**.

   ![Portale di Azure](media/automation-solution-vm-management/azure-portal-01.png)

3. Viene visualizzata la pagina **Aggiungi soluzione**. Viene richiesto di configurare la soluzione prima di importarla nella sottoscrizione di Automazione.

   ![Pagina Aggiungi soluzione di Virtual Machine Management](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

4. Nella pagina **Aggiungi soluzione** selezionare **Area di lavoro**. Selezionare un'area di lavoro Log Analytics collegata alla stessa sottoscrizione di Azure in cui è incluso l'account di Automazione. Se non è disponibile un'area di lavoro, selezionare **Crea una nuova area di lavoro**. Nella pagina **area di lavoro log Analytics** seguire questa procedura:
   - Specificare un nome per la nuova **area di lavoro log Analytics**, ad esempio "ContosoLAWorkspace".
   - Selezionare una **sottoscrizione** a cui collegarsi. Se la sottoscrizione selezionata per impostazione predefinita non è appropriata, è possibile sceglierne una dall'elenco a discesa.
   - Per il **gruppo di risorse**, è possibile selezionare un gruppo di risorse esistente o crearne uno nuovo.
   - Selezionare un **percorso**.
   - Selezionare un **Piano tariffario**. Scegliere l'opzione **Per GB (autonomo)** . I log di monitoraggio di Azure hanno aggiornato i [prezzi](https://azure.microsoft.com/pricing/details/log-analytics/) e il livello per GB è l'unica opzione.

   > [!NOTE]
   > Quando si abilitano soluzioni, sono supportate solo determinate aree per il collegamento a un'area di lavoro Log Analytics e un account di Automazione.
   >
   > Per un elenco delle coppie di mapping supportate, vedere [mapping delle aree per l'account di automazione e l'area di lavoro log Analytics](how-to/region-mappings.md).

5. Dopo aver specificato le informazioni necessarie nella pagina **area di lavoro Log Analytics**, fare clic su **Crea**. È possibile monitorarne lo stato scegliendo **Notifiche** dal menu. Al termine, si tornerà alla pagina **Aggiungi soluzione**.
6. Nella pagina **Aggiungi soluzione** selezionare **Account di Automazione**. Se si sta creando una nuova area di lavoro Log Analytics, è possibile creare un nuovo account di Automazione da associarvi oppure selezionare un Account di automazione esistente che non sia già collegato a un'area di lavoro Log Analytics. Selezionare un account di Automazione esistente o fare clic su **Crea un account di Automazione** e specificare le informazioni seguenti nella pagina **Aggiungi account di Automazione**:
   - Nel campo **Nome** immettere il nome dell'account di Automazione.

     Tutte le altre opzioni vengono popolate automaticamente in base all'area di lavoro Log Analytics selezionata. Queste opzioni non possono essere modificate. Il metodo di autenticazione predefinito per i runbook inclusi nella soluzione è un account RunAs di Azure. Dopo aver fatto clic su **OK**, le opzioni di configurazione vengono convalidate e viene creato l'account di Automazione. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

7. Nella pagina **Aggiungi soluzione** selezionare infine **Configurazione**. Viene visualizzata la pagina **Parametri**.

   ![Pagina dei parametri per la soluzione](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   In questo caso, viene chiesto di:
   - Specificare i **nomi dei gruppi di risorse di destinazione**. Questi valori sono nomi di gruppi di risorse contenenti le macchine virtuali che devono essere gestite da questa soluzione. È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole. Per specificare come destinazione le macchine virtuali in tutti i gruppi di risorse della sottoscrizione, è possibile usare un carattere jolly. Questo valore viene archiviato nelle variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**.
   - Specificare l'**elenco di esclusione delle VM (stringa)** . Questo valore è il nome di una o più macchine virtuali del gruppo di risorse di destinazione. È possibile immettere più nomi, separati da una virgola. I valori non fanno distinzione tra maiuscole e minuscole. I caratteri jolly sono supportati. Questo valore viene archiviato nella variabile **External_ExcludeVMNames**.
   - Selezionare una **pianificazione**. Selezionare una data e un'ora per la pianificazione. Verrà creata una pianificazione giornaliera ricorrente a partire dall'ora selezionata. Non è possibile selezionare un'altra area. Per configurare la pianificazione per il fuso orario specifico dopo aver configurato la soluzione, vedere [Modifica della pianificazione di avvio e arresto](#modify-the-startup-and-shutdown-schedules).
   - Per ricevere **notifiche tramite posta elettronica** da un gruppo di azione, accettare il valore predefinito **Sì** e fornire un indirizzo di posta elettronica valido. Se si seleziona **No** ma in un secondo momento si decide che si desidera ricevere notifiche tramite posta elettronica, è possibile aggiornare il [gruppo di azioni](../azure-monitor/platform/action-groups.md) che viene creato con gli indirizzi di posta elettronica validi separati da una virgola. È anche necessario abilitare le regole di avviso seguenti:

     - AutoStop_VM_Child
     - Scheduled_StartStop_Parent
     - Sequenced_StartStop_Parent

     > [!IMPORTANT]
     > Il valore predefinito per i **nomi dei gruppi di risorse di destinazione** è **&ast;** , destinato a tutte le macchine virtuali in una sottoscrizione. Se non si vuole che la soluzione interessi tutte le macchine virtuali nella sottoscrizione, questo valore deve essere aggiornato con un elenco di nomi di gruppi di risorse prima di abilitare le pianificazioni.

8. Dopo aver configurato le impostazioni iniziali necessarie per la soluzione, fare clic su **OK** per chiudere la pagina **Parametri** e selezionare **Crea**. Dopo la convalida di tutte le impostazioni, la soluzione viene distribuita nella sottoscrizione. Questo processo può richiedere alcuni secondi. Per tenere traccia dello stato di avanzamento, è possibile usare la voce **Notifiche** nel menu.

> [!NOTE]
> Se si dispone di una sottoscrizione di Azure Cloud Solution Provider (Azure CSP), al termine della distribuzione, nell'account di automazione passare a **variabili** in **risorse condivise** e impostare la variabile [**External_EnableClassicVMs**](#variables) su **false**. In questo modo, si impedisce alla soluzione di cercare risorse della macchina virtuale classica.

## <a name="scenarios"></a>Scenari

La soluzione contiene tre diversi scenari. Questi scenari sono:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: avviare/arrestare una macchina virtuale in base a una pianificazione

Questo scenario è la configurazione predefinita al momento della prima distribuzione della soluzione. Ad esempio, è possibile configurarla per poter arrestare tutte le macchine virtuali in una sottoscrizione quando si lascia il luogo di lavoro la sera e avviarle al mattino quando si ritorna in ufficio. Quando si configurano le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM** durante la distribuzione, queste avviano e arrestano le macchine virtuali di destinazione. È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali. Per informazioni su come configurare una pianificazione personalizzata, vedere [Modificare le pianificazioni di avvio e arresto](#modify-the-startup-and-shutdown-schedules).

> [!NOTE]
> Il fuso orario è il fuso orario corrente quando si configura il parametro dell'orario di pianificazione. Viene tuttavia archiviato nel formato UTC in Automazione di Azure. Non è necessario eseguire alcuna conversione del fuso orario, perché questa operazione viene gestita durante la distribuzione.

È possibile controllare quali macchine virtuali rientrano nell'ambito configurando le variabili seguenti: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames**.

È possibile consentire di specificare come destinazione dell'azione una sottoscrizione e un gruppo di risorse oppure un elenco specifico di macchine virtuali, ma non entrambi.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.
2. Abilitare e aggiornare le pianificazioni **Scheduled-StartVM** e **Scheduled-StopVM**.
3. Eseguire il runbook **ScheduledStartStop_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il runbook **ScheduledStartStop_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.
1. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).
1. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

> [!NOTE]
> Il valore per **Target ResourceGroup Names** (Nomi dei gruppi di risorse di destinazione) viene archiviato come valore sia per **External_Start_ResourceGroupNames** che per **External_Stop_ResourceGroupNames**. Per un'ulteriore granularità, è possibile modificare ognuna di queste variabili per diversi gruppi di risorse. Per l'azione di avvio, usare **External_Start_ResourceGroupNames** e, per l'azione di arresto, usare **External_Stop_ResourceGroupNames**. Le macchine virtuali vengono aggiunte automaticamente alle pianificazioni di avvio e di arresto.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: avviare/arrestare le macchine virtuali in sequenza usando i tag

In un ambiente che include due o più componenti su più macchine virtuali che supportano un carico di lavoro distribuito, è importante supportare la sequenza dei componenti da avviare e arrestare in ordine. A tale scopo, seguire questa procedura:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Destinare le azioni di avvio e arresto a una sottoscrizione e un gruppo di risorse

1. Aggiungere un tag **sequencestart** e un tag **sequencestart** con un valore intero positivo alle macchine virtuali specificate come destinazione nelle variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupNames**. Le azioni di avvio e arresto vengono eseguite in ordine crescente. Per informazioni su come assegnare un tag a una macchina virtuale, vedere [Come assegnare un tag a una macchina virtuale Windows in Azure](../virtual-machines/windows/tag.md) e [Come assegnare un tag a una macchina virtuale Linux in Azure](../virtual-machines/linux/tag.md).
1. Modificare le pianificazioni **Sequenced-StartVM** e **Sequenced-StopVM** impostandole sulla data e ora che soddisfano i requisiti e abilitano la pianificazione.
1. Eseguire il runbook **SequencedStartStop_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.
1. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, è possibile eseguire manualmente il runbook con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Aggiungere un tag **sequencestart** e un tag **sequencestop** con un valore intero positivo alle macchine virtuali che si intende aggiungere al parametro **VMList**.
1. Eseguire il runbook **SequencedStartStop_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.
1. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).
1. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).
1. Visualizzare in anteprima l'azione e apportare le modifiche necessarie prima dell'implementazione nelle VM di produzione. Quando si è pronti, è possibile eseguire manualmente il runbook monitoring-and-diagnostics/monitoring-action-groups con il parametro impostato su **False** o lasciare che le pianificazioni di Automazione **Sequenced-StartVM** e **Sequenced-StopVM** vengano eseguite automaticamente dopo la pianificazione prescritta.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenario 3: automatizzare l'avvio/arresto in base all'utilizzo della CPU

Questa soluzione consente di gestire i costi di esecuzione delle macchine virtuali nella sottoscrizione valutando le macchine virtuali di Azure non usate durante i periodi non di punta, ad esempio dopo l'orario di lavoro, e arrestandole automaticamente se l'utilizzo del processore è inferiore a x%.

Per impostazione predefinita, la soluzione è stata preconfigurata per valutare la metrica relativa alla percentuale CPU per verificare se l'utilizzo medio è pari o inferiore al 5%. Questo scenario è controllato dalle variabili seguenti e può essere modificato se i valori predefiniti non soddisfano i requisiti:

- External_AutoStop_MetricName
- External_AutoStop_Threshold
- External_AutoStop_TimeAggregationOperator
- External_AutoStop_TimeWindow

È possibile consentire di specificare come destinazione dell'azione una sottoscrizione e un gruppo di risorse oppure un elenco specifico di macchine virtuali, ma non entrambi.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Destinare l'azione di arresto a una sottoscrizione e un gruppo di risorse

1. Configurare le variabili **External_Stop_ResourceGroupNames** e **External_ExcludeVMNames** per specificare le macchine virtuali di destinazione.
1. Abilitare e aggiornare la pianificazione **Schedule_AutoStop_CreateAlert_Parent**.
1. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro ACTION impostato su **start** e il parametro WHATIF impostato su **True** per visualizzare in anteprima le modifiche.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Specificare la destinazione dell'azione di avvio e arresto in base a un elenco di macchine virtuali

1. Eseguire il runbook **AutoStop_CreateAlert_Parent** con il parametro ACTION impostato su **start**, aggiungere un elenco separato da virgole delle macchine virtuali nel parametro *VMList* e quindi impostare il parametro WHATIF su **True**. Visualizzare in anteprima le modifiche.
1. Configurare il parametro **External_ExcludeVMNames** con un elenco separato da virgole delle macchine virtuali (VM1, VM2, VM3).
1. Questo scenario non rispetta le variabili **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupnames**. Per questo scenario, è necessario creare la propria pianificazione di Automazione. Per informazioni dettagliate, vedere [Pianificazione di un runbook in Automazione di Azure](../automation/automation-schedules.md).

Dopo aver creato una pianificazione per arrestare le macchine virtuali in base all'utilizzo della CPU, è necessario abilitare una delle pianificazioni seguenti per avviarle.

- Specificare la destinazione dell'azione di avvio in base a una sottoscrizione e un gruppo di risorse. Vedere i passaggi descritti nello [Scenario 1](#scenario-1-startstop-vms-on-a-schedule) per testare e abilitare la pianificazione **Scheduled-StartVM**.
- Specificare la destinazione dell'azione di avvio in base a una sottoscrizione, un gruppo di risorse e un tag. Vedere i passaggi descritti nello [Scenario 2](#scenario-2-startstop-vms-in-sequence-by-using-tags) per testare e abilitare la pianificazione **Sequenced-StartVM**.

## <a name="solution-components"></a>Componenti della soluzione

Questa soluzione include manuali operativi preconfigurati, pianificazioni e integrazione con i log di monitoraggio di Azure, in modo da poter personalizzare l'avvio e l'arresto delle macchine virtuali in base alle esigenze aziendali.

### <a name="runbooks"></a>Runbook

La tabella seguente elenca i runbook distribuiti nell'account di Automazione da questa soluzione. Non apportare modifiche al codice del runbook. Scrivere invece il proprio runbook per le nuove funzionalità.

> [!IMPORTANT]
> Non eseguire direttamente i runbook con "child" accodato al nome.

Tutti i runbook padre includono il parametro _WhatIf_. Se impostato su **True**, _WhatIf_ supporta i dettagli relativi al comportamento esatto del runbook quando viene eseguito senza il parametro _WhatIf_ e verifica che vengano specificate come destinazione le macchine virtuali corrette. Quando il parametro _WhatIf_ è impostato su **False**, un runbook esegue solo le azioni definite.

|Runbook | parameters | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chiamato dal runbook padre. Questo runbook crea gli avvisi in base alle risorse per lo scenario AutoStop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True o False  | Crea o aggiorna le regole di avviso di Azure nelle macchine virtuali nella sottoscrizione o nei gruppi di risorse selezionati. <br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_.<br> *WhatIf* convalida la logica del runbook senza eseguirlo.|
|AutoStop_Disable | None | Disabilita gli avvisi di AutoStop e la pianificazione predefinita.|
|AutoStop_StopVM_Child | WebHookData | Chiamato dal runbook padre. Le regole di avviso chiamano questo runbook per arrestare la VM.|
|Bootstrap_Main | None | Usato una sola volta per impostare le configurazioni di bootstrap come webhookURI, che in genere non sono accessibili da Azure Resource Manager. Questo runbook viene rimosso automaticamente dopo il completamento della distribuzione.|
|ScheduledStartStop_Child | VMName <br> Azione: avvio o arresto <br> ResourceGroupName | Chiamato dal runbook padre. Esegue un'azione di avvio o arresto per l'arresto pianificato.|
|ScheduledStartStop_Parent | Azione: avvio o arresto <br>VMList <br> WhatIf: True o False | Questa impostazione ha effetto su tutte le macchine virtuali della sottoscrizione. Modificare **External_Start_ResourceGroupNames** ed **External_Stop_ResourceGroupNames** in modo che vengano eseguiti solo in questi gruppi di risorse di destinazione. È inoltre possibile escludere macchine virtuali specifiche aggiornando la variabile **External_ExcludeVMNames**.<br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_.<br> _WhatIf_ convalida la logica del runbook senza eseguirlo.|
|SequencedStartStop_Parent | Azione: avvio o arresto <br> WhatIf: True o False<br>VMList| Crea i tag denominati **sequencestart** e **sequencestop** in ogni macchina virtuale per cui si vuole mettere in sequenza l'attività di avvio/arresto. I nomi dei tag distinguono tra maiuscole e minuscole. Il valore del tag deve essere un numero intero positivo (1, 2, 3) corrispondente all'ordine in cui si vuole eseguire l'avvio o l'arresto. <br> VMList: Elenco delimitato da virgole delle macchine virtuali. Ad esempio, _vm1, vm2, vm3_. <br> _WhatIf_ convalida la logica del runbook senza eseguirlo. <br> **Nota**: le macchine virtuali devono trovarsi all'interno di gruppi di risorse definiti come External_Start_ResourceGroupNames External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure. Devono avere i tag appropriati perché le azioni abbiano effetto.|

### <a name="variables"></a>Variabili

La tabella seguente elenca le variabili create nell'account di Automazione. Modificare solo le variabili con il prefisso **External**. La modifica delle variabili con il prefisso **Internal** provoca effetti indesiderati.

|Variabile | Description|
|---------|------------|
|External_AutoStop_Condition | Operatore condizionale necessario per configurare la condizione prima che venga attivato un avviso. I valori possibili sono: **GreaterThan**, **GreaterThanOrEqual**, **LessThan** e **LessThanOrEqual**.|
|External_AutoStop_Description | Avviso per l'arresto della macchina virtuale se la percentuale della CPU supera la soglia di avviso.|
|External_AutoStop_MetricName | Nome della metrica delle prestazioni per cui deve essere configurata la regola di avviso di Azure.|
|External_AutoStop_Threshold | Soglia per la regola di avviso di Azure specificata nella variabile _External_AutoStop_MetricName_. I valori di percentuale possono variare da 1 a 100.|
|External_AutoStop_TimeAggregationOperator | Operatore dell'aggregazione temporale che viene applicato alle dimensioni della finestra selezionata per valutare la condizione. I valori possibili sono: **Average**, **Minimum**, **Maximum**, **Total** e **Last**.|
|External_AutoStop_TimeWindow | Dimensioni della finestra durante le quali Azure analizza le metriche selezionate per l'attivazione di un avviso. Questo parametro accetta l'input nel formato timespan. I valori possibili sono compresi tra 5 minuti e 6 ore.|
|External_EnableClassicVMs| Specifica se le macchine virtuali classiche sono considerate come destinazione della soluzione. Il valore predefinito è True. Il valore deve essere impostato su False per le sottoscrizioni CSP. Le macchine virtuali classiche richiedono un [account RunAs classico](automation-create-standalone-account.md#classic-run-as-accounts).|
|External_ExcludeVMNames | Immettere i nomi delle macchine virtuali da escludere, separandoli usando una virgola senza spazi. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco delimitato da virgole, le macchine virtuali che sono impostate per essere escluse potrebbero essere avviate o arrestate inavvertitamente.|
|External_Start_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di avvio.|
|External_Stop_ResourceGroupNames | Specifica uno o più gruppi di risorse, separando i valori con una virgola, destinati alle azioni di arresto.|
|Internal_AutomationAccountName | Specifica il nome dell'account di Automazione.|
|Internal_AutoSnooze_WebhookUri | Specifica l'URI del Webhook chiamato per lo scenario AutoStop.|
|Internal_AzureSubscriptionId | Specifica l'ID sottoscrizione di Azure.|
|Internal_ResourceGroupName | Specifica il nome del gruppo di risorse dell'account di Automazione.|

In tutti gli scenari le variabili **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** ed **External_ExcludeVMNames** sono necessarie per definire la destinazione delle macchine virtuali, con l'eccezione di fornire un elenco separato da virgole delle macchine virtuali per i runbook **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** e **ScheduledStartStop_Parent**. In altre parole, le macchine virtuali devono risiedere nei gruppi di risorse di destinazione perché si verifichino le azioni di avvio e arresto. La logica funziona in modo simile ai criteri di Azure perché è possibile specificare come destinazione la sottoscrizione o il gruppo di risorse e lasciare che le azioni vengano ereditate dalle nuove macchine virtuali create. Questo approccio evita di dover mantenere una pianificazione separata per ogni macchina virtuale e di gestire gli avvii e gli arresti in scala.

### <a name="schedules"></a>Pianificazioni

Nella tabella seguente sono elencate le pianificazioni create nell'account di Automazione. È possibile modificarli o creare pianificazioni personalizzate. Per impostazione predefinita, tutte le pianificazioni sono disabilitate ad eccezione di **Scheduled_StartVM** e **Scheduled_StopVM**.

Non è consigliabile abilitare tutte le pianificazioni, perché potrebbe verificarsi una sovrapposizione delle azioni di pianificazione. È meglio determinare quali ottimizzazioni si vogliono eseguire e apportare le modifiche di conseguenza. Per altre informazioni, vedere gli scenari di esempio nella sezione Panoramica.

|Nome pianificazione | Frequenza | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Ogni 8 ore | Esegue il runbook AutoStop_CreateAlert_Parent ogni 8 ore, che a sua volta arresta i valori basati sulla macchina virtuale in External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames ed External_ExcludeVMNames nelle variabili di Automazione di Azure. In alternativa, è possibile specificare un elenco separato da virgole delle macchine virtuali usando il parametro VMList.|
|Scheduled_StopVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro _Stop_ ogni giorno all'ora specificata. Arresta automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili di asset. Abilitare la pianificazione correlata, Scheduled **-StartVM**.|
|Scheduled_StartVM | Definita dall'utente, giornalmente | Esegue il runbook Scheduled_Parent con un parametro _Start_ ogni giorno all'ora specificata. Avvia automaticamente tutte le macchine virtuali che soddisfano le regole definite dalle variabili appropriate. Abilitare la pianificazione correlata, Scheduled **-StopVM**.|
|Sequenced-StopVM | 1:00 (UTC), ogni venerdì | Esegue il runbook Sequenced_Parent con un parametro _Stop_ ogni venerdì all'ora specificata. In sequenza (crescente) arresta tutte le VM con un tag di **SequenceStop** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere la sezione Runbook. Abilitare la pianificazione correlata, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13:00 (UTC), ogni lunedì | Esegue il runbook Sequenced_Parent con un parametro _Start_ ogni lunedì a una determinata ora. Avvia in sequenza (decrescente) tutte le macchine virtuali con un tag **SequenceStart** definito dalle variabili appropriate. Per altre informazioni sui valori di tag e sulle variabili di asset, vedere la sezione Runbook. Abilitare la pianificazione correlata, **Sequenced-StopVM**.|

## <a name="azure-monitor-logs-records"></a>Record di log di monitoraggio di Azure

Automazione crea due tipi di record nell'area di lavoro Log Analytics: log di processo e flussi di processo.

### <a name="job-logs"></a>Log di processo

|Proprietà | Description|
|----------|----------|
|Chiamante |  Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
|Categoria | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs.|
|CorrelationId | GUID che rappresenta l'ID di correlazione del processo del runbook.|
|JobId | GUID che rappresenta l'ID del processo del runbook.|
|operationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job.|
|ResourceId | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
|ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire. Per Automazione, il valore è Automazione di Azure.|
|ResourceType | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|resultType | Lo stato del processo di runbook. I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato|
|resultDescription | Descrive lo stato del risultato del processo di runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato|
|RunbookName | Specifica il nome del runbook.|
|SourceSystem | Specifica il sistema di origine per i dati inviati. Per Automazione, il valore è OpsManager|
|StreamType | Specifica il tipo di evento. I valori possibili sono:<br>- Dettagliato<br>- Output<br>- Errore<br>- Avviso|
|SubscriptionId | Specifica l'ID sottoscrizione del processo.
|Durata | Data e ora di esecuzione del processo del runbook.|

### <a name="job-streams"></a>Flussi di processo

|Proprietà | Description|
|----------|----------|
|Chiamante |  Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
|Categoria | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams.|
|JobId | GUID che rappresenta l'ID del processo del runbook.|
|operationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job.|
|ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
|ResourceId | Specifica l'ID risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire. Per Automazione, il valore è Automazione di Azure.|
|ResourceType | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento. Un valore possibile è:<br>- InProgress|
|resultDescription | Include il flusso di output dal runbook.|
|RunbookName | Il nome del runbook.|
|SourceSystem | Specifica il sistema di origine per i dati inviati. Per Automazione, il valore è OpsManager.|
|StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
|Durata | Data e ora di esecuzione del processo del runbook.|

Quando si esegue una ricerca log che restituisce record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare la visualizzazione **JobLogs** o **JobStreams** che presenta una serie di riquadri di riepilogo degli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da questa soluzione.

|Query | Description|
|----------|----------|
|Trova i processi completati per il runbook ScheduledStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Trova i processi completati per il runbook SequencedStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="viewing-the-solution"></a>Visualizzazione della soluzione

Per accedere alla soluzione, passare all'account di Automazione e selezionare **Area di lavoro** in **RISORSE CORRELATE**. Nella pagina log Analytics selezionare **soluzioni** in **generale**. Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]) dall'elenco.

Selezionando la soluzione, viene visualizzata la pagina della soluzione **Start-Stop-VM[workspace]** (Avvio-Arresto-VM[Area di lavoro]), dove è possibile esaminare importanti dettagli, ad esempio il riquadro **StartStopVM** (Avvio e arresto di VM). Come nell'area di lavoro Log Analytics, questo riquadro mostra un conteggio e una rappresentazione grafica dei processi di runbook della soluzione che sono stati avviati e completati.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Da qui è possibile eseguire un'analisi aggiuntiva dei record dei processi facendo clic sul riquadro ad anello. Il dashboard della soluzione mostra la cronologia processo e le query di ricerca log predefinite. Passare al portale avanzato di log Analytics per eseguire la ricerca in base alle query di ricerca.

## <a name="configure-email-notifications"></a>Configurare le notifiche di posta elettronica

Per modificare le notifiche di posta elettronica dopo la distribuzione della soluzione, modificare il gruppo di azioni che è stato creato durante la distribuzione.  

> [!NOTE]
> Le sottoscrizioni nel cloud di Azure per enti pubblici non supportano la funzionalità di posta elettronica di questa soluzione.

Nel portale di Azure, passare a monitoraggio -> gruppi di azioni. Selezionare il gruppo di azione intitolato **StartStop_VM_Notication**.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/azure-monitor.png)

Nella pagina **StartStop_VM_Notification**, fare clic su **Modifica dettagli** in **Dettagli**. Questo apre la pagina **Email/SMS/Push/Voice** (Posta elettronica/SMS/Push/Voce). Aggiornare l'indirizzo di posta elettronica e fare clic su **OK** per salvare le modifiche.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/change-email.png)

In alternativa è possibile aggiungere azioni aggiuntive al gruppo di azione, per altre informazioni sui gruppi di azioni, vedere [gruppi di azioni](../azure-monitor/platform/action-groups.md)

Di seguito è riportato un messaggio di posta elettronica di esempio che viene inviato quando la soluzione arresta macchine virtuali.

![Pagina della soluzione Gestione aggiornamenti di Automazione](media/automation-solution-vm-management/email.png)

## <a name="add-exclude-vms"></a>Aggiungere o escludere macchine virtuali

È possibile aggiungere macchine virtuali a cui applicare la soluzione oppure escludere determinate macchine dalla soluzione.

### <a name="add-a-vm"></a>Aggiungere una macchina virtuale

Per assicurarsi che una macchina virtuale sia inclusa nella soluzione di avvio/arresto al momento dell'esecuzione sono disponibili due opzioni.

* Ogni [manuali operativi](#runbooks) padre della soluzione ha un parametro **VMList** . È possibile passare un elenco delimitato da virgole di nomi di VM a questo parametro quando si pianificano i Runbook padre appropriati per la situazione e queste VM verranno incluse durante l'esecuzione della soluzione.

* Per selezionare più macchine virtuali, impostare **External_Start_ResourceGroupNames** e **External_Stop_ResourceGroupNames** con i nomi dei gruppi di risorse che contengono le macchine virtuali da avviare o arrestare. È anche possibile impostare questo valore su `*` per fare in modo che la soluzione venga applicata a tutti i gruppi di risorse nella sottoscrizione.

### <a name="exclude-a-vm"></a>Escludere una macchina virtuale

Per escludere una macchina virtuale dalla soluzione, è possibile aggiungerla alla variabile **External_ExcludeVMNames**. Questa variabile è un elenco delimitato da virgole di VM specifiche da escludere dalla soluzione Start/Stop. Il limite dell'elenco è 140 macchine virtuali. Se si aggiungono più di 140 macchine virtuali a questo elenco delimitato da virgole, le macchine virtuali che sono impostate per essere escluse potrebbero essere avviate o arrestate inavvertitamente.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificare le pianificazioni di avvio e arresto

Per gestire le pianificazioni relative all'avvio e all'arresto in questa soluzione viene seguita la stessa procedura illustrata in [Pianificazione di un runbook in Automazione di Azure](automation-schedules.md). Per l'avvio e l'arresto di macchine virtuali devono essere definite due pianificazioni separate.

È possibile configurare la soluzione per eseguire solamente l'arresto delle macchine virtuali a una data ora. In questo scenario è sufficiente definire una pianificazione di **arresto**, senza la pianificazione di **avvio** corrispondente. A questo scopo, è necessario:

1. Verificare di aver aggiunto i gruppi di risorse per le macchine virtuali da arrestare nella variabile **External_Stop_ResourceGroupNames**.
2. Creare una pianificazione personalizzata per l'ora di arresto delle macchine virtuali.
3. Passare al runbook **ScheduledStartStop_Parent** e fare clic su **Pianificazione**. In questo modo è possibile selezionare la pianificazione creata nel passaggio precedente.
4. Selezionare **Parametri e impostazioni di esecuzione** e impostare il parametro ACTION su "Stop".
5. Fare clic su **OK** per salvare le modifiche.

## <a name="update-the-solution"></a>Aggiornare la soluzione

Se è stata distribuita una versione precedente di questa soluzione, è necessario prima di tutto eliminarla dall'account prima di distribuire una versione aggiornata. Seguire la procedura per [rimuovere la soluzione](#remove-the-solution) e quindi eseguire i passaggi precedenti per [distribuire la soluzione](#deploy-the-solution).

## <a name="remove-the-solution"></a>Rimuovere la soluzione

Se non è più necessario usare la soluzione, è possibile eliminarla dall'account di Automazione. Eliminando la soluzione, vengono rimossi solo i runbook. Non vengono eliminate le pianificazioni o le variabili create quando la soluzione è stata aggiunta. Tali asset devono essere eliminati manualmente se non vengono usati con altri runbook.

Per eliminare la soluzione, attenersi alla procedura seguente:

1. Nell'account di automazione in **risorse correlate**selezionare area di **lavoro collegata**.
1. Selezionare **Vai all'area di lavoro**.
1. In **generale**selezionare **soluzioni**. 
1. Nella pagina **Soluzioni** selezionare la soluzione **Start-Stop-VM[Workspace]** (Avvio-Arresto-VM[Area di lavoro]). Nella pagina **VMManagementSolution[area di lavoro]** scegliere **Elimina** dal menu.<br><br> ![Eliminare una soluzione di gestione di macchine virtuali](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. Nella finestra **Elimina soluzione** confermare di voler eliminare la soluzione.
1. Per tenere traccia dello stato di avanzamento della verifica delle informazioni e della rimozione della soluzione, è possibile usare la voce **Notifiche** nel menu. Quando il processo di rimozione della soluzione inizia, l'utente viene rimandato alla pagina **Soluzioni**.

L'account di Automazione e l'area di lavoro Log Analytics non vengono eliminati come parte del processo. Se non si vuole conservare l'area di lavoro Log Analytics, è necessario eliminarla manualmente. Tale operazione può essere eseguita dal portale di Azure:

1. In portale di Azure cercare e selezionare **log Analytics aree di lavoro**.
1. Nella pagina **aree di lavoro log Analytics** selezionare l'area di lavoro.
1. Scegliere **Elimina** dal menu nella pagina delle impostazioni dell'area di lavoro.

Se non si vogliono mantenere i componenti dell'account di Automazione di Azure, è possibile eliminarli manualmente. Per l'elenco dei runbook, delle variabili e delle pianificazioni create dalla soluzione, vedere [Componenti della soluzione](#solution-components).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su come creare query di ricerca diverse ed esaminare i log dei processi di automazione con i log di monitoraggio di Azure, vedere [ricerche nei log nei log di monitoraggio di Azure](../log-analytics/log-analytics-log-searches.md).
- Per altre informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere [Verifica di un processo di runbook](automation-runbook-execution.md).
- Per altre informazioni sui log di monitoraggio di Azure e sulle origini di raccolta dati, vedere [raccolta di dati di archiviazione di Azure in log di monitoraggio di Azure](../azure-monitor/platform/collect-azure-metrics-logs.md).

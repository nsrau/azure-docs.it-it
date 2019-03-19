---
title: Uso dei criteri di sicurezza | Microsoft Docs
description: Questo articolo descrive come usare i criteri di sicurezza nel Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/14/2019
ms.author: monhaber
ms.openlocfilehash: 98fffbc7a3b287dd59cfc681beec2107a5fd4ed6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085049"
---
# <a name="working-with-security-policies"></a>Utilizzo dei criteri di sicurezza

Questo articolo illustra come vengono configurati i criteri di sicurezza e come visualizzarli nel Centro sicurezza. Il Centro sicurezza di Azure assegna automaticamente i [criteri di sicurezza predefiniti](security-center-policy-definitions.md) in ogni sottoscrizione caricata. È possibile configurarli in [Criteri di Azure](../governance/policy/overview.md), che consente anche di impostare i criteri nei gruppi di gestione e in più sottoscrizioni.

Per istruzioni su come impostare i criteri con PowerShell, vedere [Guida introduttiva: Creare un'assegnazione di criteri per identificare le risorse non conformi con il modulo di Azure PowerShell](../governance/policy/assign-policy-powershell.md).

>[!NOTE]
> Il Centro sicurezza ha avviato l'integrazione con i Criteri di Azure. I clienti esistenti verranno automaticamente migrati alla nuova iniziativa incorporata in Criteri di Azure, anziché ai precedenti criteri di sicurezza in Centro sicurezza. Questa modifica non influirà su risorse o ambiente, ad eccezione della presenza di una nuova iniziativa in Criteri di Azure.

## <a name="what-are-security-policies"></a>Informazioni sui criteri di sicurezza
Un criterio di sicurezza definisce la configurazione specifica dei carichi di lavoro e contribuisce ad assicurare la conformità ai requisiti aziendali o normativi per la sicurezza. In Criteri di Azure è possibile definire criteri per le sottoscrizioni di Azure e adattarli al tipo di carico di lavoro o alla riservatezza dei dati. Ad esempio, le applicazioni che usano dati regolamentati come le informazioni personali possono richiedere un livello di sicurezza maggiore di quello degli altri carichi di lavoro. Per impostare un criterio nelle sottoscrizioni o nei gruppi di gestione, impostarle nei [Criteri di Azure](../governance/policy/overview.md).

I criteri di sicurezza determinano i suggerimenti per la sicurezza ottenuti nel Centro sicurezza di Azure. È possibile monitorare la conformità a tali criteri per identificare potenziali vulnerabilità e attenuare le minacce. Per altre informazioni su come determinare l'opzione più appropriata, vedere l'elenco di [criteri di sicurezza predefiniti](security-center-policy-definitions.md).

Se si abilita Centro sicurezza, i criteri di sicurezza incorporati al suo interno si riflettono in Criteri di Azure come iniziativa predefinita nella categoria Centro sicurezza. L'iniziativa predefinita viene automaticamente assegnata a tutte le sottoscrizioni registrate di Centro sicurezza (livello Gratuito o Standard). L'iniziativa predefinita contiene solo criteri di controllo.


### <a name="management-groups"></a>Gruppi di gestione
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicati i criteri di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente i criteri applicati al gruppo di gestione. A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Per configurare i gruppi di gestione per l'uso con Centro sicurezza di Azure, seguire le istruzioni nell'articolo [Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure](security-center-management-groups.md).

> [!NOTE]
> È importante comprendere la gerarchia dei gruppi di gestione e delle sottoscrizioni. Vedere [Organizzare le risorse con i gruppi di gestione di Azure](../governance/management-groups/index.md#root-management-group-for-each-directory) per altre informazioni sui gruppi di gestione, la gestione radice e l'accesso ai gruppi di gestione.
>

## <a name="how-security-policies-work"></a>Funzionamento dei criteri di sicurezza
Il Centro sicurezza crea automaticamente un criterio di sicurezza predefinito per ogni sottoscrizione di Azure. È possibile modificare i criteri in Criteri di Azure per eseguire le operazioni seguenti:
- Creare nuove definizioni dei criteri.
- Assegnare i criteri ai gruppi di gestione e alle sottoscrizioni, che possono rappresentare un'intera organizzazione o una business unit all'interno dell'organizzazione.
- Monitorare la conformità ai criteri.

Per altre informazioni su Criteri di Azure, vedere [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

Un criterio di Azure è costituito dai componenti seguenti:

- Un **criterio** è una regola.
- Un'**iniziativa** è una raccolta di criteri.
- Un'**assegnazione** è l'applicazione di un'iniziativa o di un criterio a un ambito specifico (gruppo di gestione, sottoscrizione o gruppo di risorse).

## <a name="view-security-policies"></a>Visualizzare i criteri di sicurezza

Per visualizzare i criteri di sicurezza nel Centro sicurezza:

1. Nel dashboard del **Centro sicurezza** selezionare **Criteri di sicurezza**.

    ![Riquadro Gestione dei criteri](./media/security-center-policies/security-center-policy-mgt.png)

   Nella schermata **Gestione dei criteri** è possibile visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

   > [!NOTE]
   > - Il dashboard Centro sicurezza potrebbe mostrare in **Copertura della sottoscrizione** un numero di sottoscrizioni maggiore rispetto a quello indicato in **Gestione dei criteri**. In Copertura della sottoscrizione è riportato il numero di sottoscrizioni di tipo Standard, Gratuito e “senza copertura”. Le sottoscrizioni "senza copertura" non hanno abilitato il Centro sicurezza e non vengono visualizzate in **Gestione dei criteri**.
   >

   Le colonne nella tabella mostrano:

   - **Assegnazione dell'iniziativa relativa ai criteri**: iniziative e [criteri predefiniti](security-center-policy-definitions.md) del Centro sicurezza assegnati a una sottoscrizione o a un gruppo di gestione.
   - **Copertura**: identifica il piano tariffario, Gratuito o Standard, in cui viene eseguito il gruppo di gestione, la sottoscrizione o l'area di lavoro.  Per altre informazioni sui piani tariffari di Centro sicurezza, vedere [Prezzi](security-center-pricing.md).
   - **Impostazioni**: per le sottoscrizioni è disponibile il collegamento **Modifica impostazioni**. Selezionando **Modifica impostazioni**, è possibile aggiornare le [impostazioni del Centro sicurezza](security-center-policies-overview.md) per ogni sottoscrizione o gruppo di gestione.
   - **Punteggio di sicurezza**: il [Punteggio di sicurezza](security-center-secure-score.md) fornisce un'indicazione sul comportamento di sicurezza del carico di lavoro e aiuta a dare priorità alle raccomandazioni per il miglioramento.

2. Selezionare la sottoscrizione o il gruppo di gestione di cui si vogliono visualizzare i criteri.

   - La schermata **Criteri di sicurezza** rispecchia l'azione eseguita dai criteri assegnati nella sottoscrizione o nel gruppo di gestione selezionato.
   - Nella parte superiore usare i collegamenti forniti per aprire ogni **assegnazione** dei criteri che si applica alla sottoscrizione o al gruppo di gestione. È possibile usare i collegamenti per accedere all'assegnazione e modificare o disabilitare i criteri. Se ad esempio si nota che l'assegnazione di un determinato criterio rifiuta di fatto la protezione di un endpoint, è possibile usare il collegamento per accedere al criterio e modificarlo o disabilitarlo.
   - Nell'elenco dei criteri è possibile visualizzare l'applicazione effettiva del criterio nella sottoscrizione o nel gruppo di gestione. Ciò significa che vengono prese in esame le impostazioni di ogni criterio che si applicano all'ambito e viene fornito il risultato cumulativo dell'azione che viene eseguita dal criterio. Se ad esempio in un'assegnazione il criterio è disabilitato, ma in un'altra è impostato su AuditIfNotExist, l'effetto cumulativo applica AuditIfNotExist. L'effetto più attivo ha sempre la precedenza.
   - L'effetto dei criteri può essere uno dei seguenti, ovvero Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Per altre informazioni su come vengono applicati gli effetti, vedere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).

   ![Schermata dei criteri](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Quando si visualizzano i criteri assegnati, è possibile visualizzare più assegnazioni e anche come è configurata ogni singola assegnazione.

## <a name="edit-security-policies"></a>Modificare i criteri di sicurezza
È possibile modificare i criteri di sicurezza predefiniti per ogni sottoscrizione e gruppo di gestione di Azure in [Criteri di Azure](../governance/policy/tutorials/create-and-manage.md). Per modificare i criteri di sicurezza, è necessario essere un proprietario, un collaboratore o un amministratore della sicurezza della sottoscrizione o del gruppo di gestione che la include.

Per istruzioni su come modificare un criterio di sicurezza in Criteri di Azure, vedere [Creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

È possibile modificare i criteri di sicurezza tramite il portale Criteri di Azure, l'API REST o Windows PowerShell. L'esempio seguente contiene istruzioni per la modifica tramite l'API REST.


## <a name="disable-security-policies"></a>Disabilitare i criteri di sicurezza
Se i criteri di sicurezza predefinito genera un'indicazione che non è rilevante per l'ambiente, è possibile arrestarlo disabilitando la definizione dei criteri che invia la raccomandazione.
Per altre informazioni sulle raccomandazioni, vedere [gestione delle raccomandazioni di sicurezza](security-center-recommendations.md).

1. Nel Centro sicurezza, dal **criteri e conformità** fare clic su **criteri di sicurezza**.

   ![Gestione dei criteri](./media/tutorial-security-policy/policy-management.png)

2. Fare clic sulla sottoscrizione per cui si desidera disabilitare la raccomandazione.

1. Fare clic sul criterio assegnato.

   ![disabilitare i criteri](./media/tutorial-security-policy/security-policy.png)

1. Nel **parametri** sezione ricerca per il criterio che richiama l'indicazione che si desidera disabilitare e nell'elenco a discesa, selezionare **disabilitato**

   ![disabilitare i criteri](./media/tutorial-security-policy/disable-policy.png)
1. Fare clic su **Save**.
> [!Note]
> Le modifiche al criterio disabilitazione possono richiedere fino a 12 ore per diventare effettive.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Configurare criteri di sicurezza usando l'API REST

Nell'ambito dell'integrazione nativa con Criteri di Azure, Centro sicurezza di Azure consente di sfruttare l'API REST di Criteri di Azure per creare le assegnazioni dei criteri. Le istruzioni seguenti descrivono la creazione delle assegnazioni dei criteri, nonché la personalizzazione delle assegnazioni esistenti. 

Concetti importanti in Criteri di Azure 

- Una  **definizione di criteri** è una regola 

- Un' **iniziativa** è una raccolta di definizioni di criteri (regole) 

- Un' **assegnazione** è un'applicazione di un'iniziativa o di criteri a un ambito specifico (gruppo di gestione, sottoscrizione e così via) 

In Centro sicurezza è presente un'iniziativa integrata che include tutti i criteri di sicurezza relativi. Per valutare i criteri del Centro sicurezza nelle risorse di Azure, è necessario creare un'assegnazione nel gruppo di gestione o la sottoscrizione da valutare.  

Nell'iniziativa integrata sono presenti tutti i criteri del Centro sicurezza abilitati per impostazione predefinita. È possibile scegliere di disabilitare determinati criteri dall'iniziativa integrata, ad esempio è possibile applicare tutti i criteri del Centro sicurezza, ad eccezione del **firewall delle applicazioni Web**, modificando il valore del parametro di effetto dei criteri su **Disabled**. 

### <a name="api-examples"></a>Esempi di API

Negli esempi seguenti sostituire queste variabili:

- **{scope}**: immettere il nome del gruppo di gestione o la sottoscrizione a cui si applicano i criteri.
- **{policyAssignmentName}**: immettere il [nome dell'assegnazione di criteri pertinente](#policy-names).
- **{name}**: immettere il proprio nome o il nome dell'amministratore che ha approvato la modifica dei criteri.

Questo esempio illustra come assegnare l'iniziativa del Centro sicurezza integrata in una sottoscrizione oppure in un gruppo di gestione:
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

Questo esempio illustra come assegnare l'iniziativa del Centro sicurezza integrata in una sottoscrizione, con i criteri seguenti disabilitati: 

- Aggiornamenti del sistema ("systemUpdatesMonitoringEffect") 

- Configurazione di sicurezza ("systemConfigurationsMonitoringEffect") 

- Protezione di endpoint ("endpointProtectionMonitoringEffect") 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Corpo della richiesta (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName": "Abilita monitoraggio nel Centro sicurezza di Azure", 
    
    "metadati": { 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters": { 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect": {"value": "Disabled"}, 
    
    }, 
    
     } 
    
    } 

Questo esempio illustra come rimuovere un'assegnazione:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### Riferimento per i  nomi dei criteri <a name="policy-names"></a>

|Nome del criterio in Centro sicurezza|Nome del criterio visualizzato in Criteri di Azure |Nome del parametro di effetto del criterio|
|----|----|----|
|Crittografia SQL |Monitora i database SQL non crittografati nel Centro sicurezza di Azure |sqlEncryptionMonitoringEffect| 
|Controllo SQL |Monitora i database SQL non controllati nel Centro sicurezza di Azure |sqlAuditingMonitoringEffect|
|Aggiornamenti del sistema |Monitora gli aggiornamenti di sistema mancanti nel Centro sicurezza di Azure |systemUpdatesMonitoringEffect|
|Crittografia di archiviazione |Controlla crittografia BLOB mancante per gli account di archiviazione |storageEncryptionMonitoringEffect|
|Accesso alla rete JIT |Monitora i possibili accessi JIT alla rete nel Centro sicurezza di Azure |jitNetworkAccessMonitoringEffect |
|Controlli delle applicazioni adattivi |Monitora il possibile elenco elementi consentiti dell'app nel Centro sicurezza di Azure |adaptiveApplicationControlsMonitoringEffect|
|Gruppi di sicurezza di rete |Monitora gli accessi di rete permissivi nel Centro sicurezza di Azure |networkSecurityGroupsMonitoringEffect| 
|Configurazioni di sicurezza |Monitora le vulnerabilità del sistema operativo nel Centro sicurezza di Azure |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Monitora server senza Endpoint Protection nel Centro sicurezza di Azure |endpointProtectionMonitoringEffect |
|Crittografia del disco |Monitora i dischi di macchine virtuali non crittografati nel Centro sicurezza di Azure |diskEncryptionMonitoringEffect|
|Valutazione della vulnerabilità |Monitorare le vulnerabilità delle macchine virtuali nel Centro sicurezza di Azure |vulnerabilityAssessmentMonitoringEffect|
|Web application firewall |Monitora le applicazioni Web non protette nel Centro sicurezza di Azure |webApplicationFirewallMonitoringEffect |
|Firewall di nuova generazione |Monitora gli endpoint rete non protetti nel Centro sicurezza di Azure| |


### <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza
Il Centro sicurezza usa il controllo degli accessi in base al ruolo, che fornisce ruoli predefiniti assegnabili a utenti, gruppi e servizi in Azure. Quando un utente apre il Centro sicurezza, visualizza solo informazioni correlate alle risorse a cui ha accesso. All'utente viene infatti assegnato il ruolo Proprietario, Collaboratore o Lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa. Oltre a questi ruoli, esistono due ruoli specifici del Centro sicurezza:

- Ruolo con autorizzazioni di lettura per la sicurezza: dispone dei diritti di visualizzazione per il Centro sicurezza, inclusi avvisi, criteri, raccomandazioni e integrità, ma non può apportare modifiche.
- Amministratore della sicurezza: ha gli stessi diritti di visualizzazione del ruolo con autorizzazioni di lettura per la sicurezza, ma può anche aggiornare i criteri di sicurezza e ignorare raccomandazioni e avvisi.



## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato descritto come modificare i criteri di sicurezza in Criteri di Azure. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](security-center-planning-and-operations-guide.md): Informazioni sulla pianificazione e considerazioni di progettazione sul Centro sicurezza di Azure.
* [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md): Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md): Informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md): Informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Ottenere visibilità a livello di tenant per il Centro sicurezza di Azure](security-center-management-groups.md). Informazioni su come configurare i gruppi di gestione per il Centro sicurezza di Azure.
* [Domande frequenti sul Centro sicurezza di Azure](security-center-faq.md). Risposte alle domande frequenti sull'uso del servizio.
* [Blog sulla sicurezza di Azure](https://blogs.msdn.com/b/azuresecurity/): Post di blog sulla sicurezza e sulla conformità di Azure.

Per altre informazioni su Criteri di Azure, vedere [Informazioni su Criteri di Azure](../governance/policy/overview.md).

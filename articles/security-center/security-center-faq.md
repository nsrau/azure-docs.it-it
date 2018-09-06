---
title: Domande frequenti sul Centro sicurezza di Azure | Microsoft Docs
description: Queste FAQ rispondono alle domande sul Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: b93ad6793bb8e431ba318772c780a06c792dcd9a
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126761"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Domande frequenti sul Centro sicurezza di Azure
Queste FAQ rispondono alle domande sul Centro sicurezza di Azure, un servizio che consente di prevenire, rilevare e rispondere alle minacce con visibilità e controllo maggiori sulla sicurezza delle risorse di Microsoft Azure.

> [!NOTE]
> A partire dall'inizio di giugno 2017, il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere e archiviare i dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Le informazioni contenute in questo articolo si riferiscono alle funzionalità del Centro sicurezza dopo la transizione a Microsoft Monitoring Agent.
>
>

## <a name="general-questions"></a>Domande generali
### <a name="what-is-azure-security-center"></a>Che cos'è il Centro sicurezza di Azure?
Il Centro sicurezza PC di Azure impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

### <a name="how-do-i-get-azure-security-center"></a>In che modo è possibile accedere al Centro sicurezza di Azure?
Il Centro sicurezza di Azure viene abilitato con la sottoscrizione di Microsoft Azure ed è accessibile dal [portale di Azure](https://azure.microsoft.com/features/azure-portal/). ([accedere al portale](https://portal.azure.com), selezionare **Sfoglia** e scorrere fino a **Centro sicurezza**).  

## <a name="billing"></a>Fatturazione
### <a name="how-does-billing-work-for-azure-security-center"></a>Come funziona la fatturazione per il Centro sicurezza di Azure?
Il Centro sicurezza è disponibile in due livelli:

Il **livello gratuito** permette di conoscere lo stato di protezione delle risorse di Azure, i criteri di sicurezza di base, i consigli sulla sicurezza e l'aspetto di integrazione con i prodotti e i servizi dei partner.

Il **livello standard** aggiunge funzionalità avanzate per il rilevamento delle minacce, tra cui intelligence per le minacce, analisi del comportamento, rilevamento delle anomalie, eventi imprevisti per la sicurezza e report di valutazione delle minacce. Il livello Standard è gratuito per i primi 60 giorni. Se scegli di continuare a usare il servizio dopo questi 60 giorni, l'uso del servizio verrà addebitato automaticamente.  Per eseguire l'aggiornamento, selezionare il [piano tariffario](https://docs.microsoft.com/azure/security-center/security-center-pricing) nei criteri di sicurezza.

## <a name="permissions"></a>Autorizzazioni
Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md), con [ruoli predefiniti](../role-based-access-control/built-in-roles.md) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](security-center-permissions.md) (Autorizzazioni in Centro sicurezza di Azure).

## <a name="data-collection-agents-and-workspaces"></a>Raccolta di dati, agenti e aree di lavoro
Il Centro sicurezza raccoglie i dati delle macchine virtuali di Azure e dei computer che non hanno Azure per monitorare le minacce e le vulnerabilità della sicurezza. I dati vengono raccolti tramite Microsoft Monitoring Agent, che legge diverse configurazioni correlate alla sicurezza oltre ai registri eventi del computer e copia i dati nell'area di lavoro per eseguire l'analisi.

### <a name="am-i-billed-for-log-analytics-on-the-workspaces-created-by-security-center"></a>Vengono addebitati costi per Log Analytics nelle aree di lavoro create dal Centro sicurezza?
No. Le aree di lavoro create dal Centro sicurezza non comportano addebiti di Log Analytics, benché siano configurate per la fatturazione di Log Analytics per nodo. La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Livello Gratuito**: il Centro sicurezza abilita la soluzione 'SecurityCenterFree' nell'area di lavoro predefinita. Non viene applicato alcun addebito per il livello Gratuito.
- **Livello Standard**: il Centro sicurezza abilita la soluzione 'Security' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/). La pagina relativa ai prezzi illustra le modifiche apportate alla fatturazione per l'archiviazione dei dati di sicurezza e alla fatturazione ripartita a partire da giugno 2017.

> [!NOTE]
> Il piano tariffario di Log Analytics per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Che cosa determina il diritto di una macchina virtuale al provisioning automatico dell'installazione di Microsoft Monitoring Agent?
Le macchine virtuali IaaS Windows o Linux hanno diritto al provisioning automatico se:

- L'estensione Microsoft Monitoring Agent non è attualmente installata nella macchina virtuale.
- La macchina virtuale è nello stato di esecuzione.
- L'agente di macchine virtuali Windows o Linux è installato.
- La macchina virtuale non viene usata come appliance, ad esempio web application firewall o firewall di nuova generazione.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>È possibile eliminare le aree di lavoro predefinite create dal Centro sicurezza?
**L'eliminazione dell'area di lavoro predefinita non è consigliata.** Il Centro sicurezza usa le aree di lavoro predefinite per archiviare i dati di sicurezza dalle macchine virtuali.  Se si elimina un'area di lavoro, il Centro sicurezza non potrà raccogliere tali dati e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili.

Per il ripristino, rimuovere Microsoft Monitoring Agent dalle VM connesse all'area di lavoro eliminata. Il Centro sicurezza reinstalla l'agente e crea nuove aree di lavoro predefinite.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Come usare l'area di lavoro di Log Analytics esistente

È possibile selezionare un'area di lavoro di Log Analytics esistente per archiviare i dati raccolti dal Centro sicurezza. Per usare l'area di lavoro di Log Analytics esistente:

- L'area di lavoro deve essere associata alla sottoscrizione di Azure selezionata.
- È necessario avere almeno le autorizzazioni di lettura per accedere all'area di lavoro.

Per selezionare l'area di lavoro di Log Analytics esistente:

1. In **Security policy – Data Collection** (Criteri di sicurezza - Raccolta dati) selezionare **Use another workspace** (Usare un'altra area di lavoro).

   ![Usare un'altra area di lavoro][5]

2. Nel menu a discesa selezionare un'area di lavoro dove archiviare i dati raccolti.

   > [!NOTE]
   > Nel menu a discesa vengono visualizzate solo le aree di lavoro a cui si ha accesso e che si trovano nella sottoscrizione di Azure.
   >
   >

3. Selezionare **Salva**.
4. Dopo aver selezionato **Salva**, verrà richiesto se si desidera riconfigurare le macchine virtuali monitorate.

   - Selezionare **No** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino solo alle nuove macchine virtuali**. Le nuove impostazioni dell'area di lavoro si applicano solo alle nuove installazioni dell'agente, ovvero alle macchine virtuali da poco rilevate che non hanno installato Microsoft Monitoring Agent.
   - Selezionare **Sì** se si desidera che le nuove impostazioni dell'area di lavoro si **applichino a tutte le macchine virtuali**. In aggiunta, ogni macchina virtuale connessa a un'area di lavoro creata dal Centro sicurezza viene ricollegata alla nuova area di lavoro di destinazione.

   > [!NOTE]
   > Se si seleziona Sì, non è necessario eliminare le aree di lavoro create dal Centro sicurezza fino a quando tutte le macchine virtuali vengono ricollegate alla nuova area di lavoro di destinazione. Questa operazione non riesce se un'area di lavoro viene eliminata troppo presto.
   >
   >

   - Selezionare **Annulla** per annullare l'operazione.

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Cosa accade se Microsoft Monitoring Agent è già stato installato come estensione nella VM?
Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivia i dati di sicurezza dalla VM nell'area di lavoro già connessa. Il Centro sicurezza aggiorna la versione dell'estensione in modo da includere l'ID risorsa di Azure della macchina virtuale per supportare l'uso del Centro sicurezza.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Cosa accade se Microsoft Monitoring Agent è installato nella macchina virtuale ma non come estensione?
Se Microsoft Monitoring Agent è installato direttamente sulla macchina virtuale, non come estensione di Azure, il Centro sicurezza non lo installerà e il monitoraggio della sicurezza sarà limitato.

Per altre informazioni, vedere la sezione successiva [Cosa accade se un agente diretto OMS o SCOM è già installato nella macchina virtuale?](#scomomsinstalled).

### Cosa accade se un agente diretto OMS o SCOM è già installato nella macchina virtuale?<a name="scomomsinstalled"></a>
Il Centro sicurezza non è in grado di identificare in anticipo se è installato un agente.  Il Centro sicurezza tenta di installare l'estensione Microsoft Monitoring Agent e non riesce a causa dell'agente installato esistente.  Questo errore impedisce l'override delle impostazioni di connessione dell'agente all'area di lavoro ed evita la creazione del multihosting.

> [!NOTE]
> La versione dell'agente viene aggiornata alla versione più recente dell'agente OMS.  Questo vale anche per gli utenti SCOM.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual è l'impatto della rimozione delle estensioni?
Se si rimuove l'estensione Microsoft Monitoring, il Centro sicurezza non potrà raccogliere i dati di sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Entro 24 ore il Centro sicurezza determina che nella VM non è presente l'estensione e la reinstalla.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?
È possibile disattivare il provisioning automatico per le sottoscrizioni nei criteri di sicurezza, ma questa opzione non è consigliata. La disattivazione del provisioning automatico limita le raccomandazioni e gli avvisi del Centro sicurezza. Il provisioning automatico è obbligatorio per le sottoscrizioni con piano tariffario Standard. Per disabilitare il provisioning automatico:

1. Se la sottoscrizione è configurata per il livello Standard, aprire i criteri di sicurezza per tale sottoscrizione e selezionare il livello **Gratuito**.

   ![Piano tariffario][1]

2. Disattivare quindi il provisioning automatico selezionando **Disattivato** nel pannello **Security policy – Data collection** (Criteri di sicurezza - Raccolta dati).
   ![Raccolta di dati][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>È consigliabile rifiutare esplicitamente l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?

> [!NOTE]
> Assicurarsi di aver letto con attenzione le sezioni [Quali sono le implicazioni del rifiuto esplicito?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se si sceglie di rifiutare esplicitamente il provisioning automatico.
>
>

È possibile rifiutare esplicitamente il provisioning automatico se si applicano le condizioni seguenti:

- L'installazione automatica dell'agente da parte del Centro sicurezza si applica all'intera sottoscrizione.  Non è possibile applicare l'installazione automatica a un sottoinsieme di macchine virtuali. Se sono presenti macchine virtuali critiche in cui Microsoft Monitoring Agent non può essere installato, sarà necessario rifiutare esplicitamente il provisioning automatico.
- L'installazione dell'estensione Microsoft Monitoring Agent aggiorna la versione dell'agente. Questa condizione si applica a un agente diretto e a un agente SCOM. Se la versione dell'agente SCOM installato è la 2012 e l'agente viene aggiornato, le capacità di gestibilità potrebbero andare perse se anche la versione del server SCOM è la 2012. È consigliabile prendere in considerazione di rifiutare esplicitamente il provisioning automatico se la versione dell'agente SCOM installato è la 2012.
- Se si dispone di un'area di lavoro personalizzata esterna alla sottoscrizione (un'area di lavoro centralizzata), è consigliabile rifiutare esplicitamente il provisioning automatico. È possibile installare manualmente l'estensione Microsoft Monitoring Agent e connetterla all'area di lavoro senza che il Centro sicurezza esegua l'override della connessione.
- Se si vuole evitare di creare più aree di lavoro per sottoscrizione e si dispone di un'area di lavoro personalizzata all'interno della sottoscrizione, sono disponibili due opzioni:

   1. È possibile rifiutare esplicitamente il provisioning automatico. Dopo la migrazione, configurare le impostazioni dell'area di lavoro predefinita come descritto in [Come usare l'area di lavoro di Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).
   2. In alternativa, è possibile consentire il completamento della migrazione, l'installazione di Microsoft Monitoring Agent nelle macchine virtuali e la connessione delle macchine virtuali all'area di lavoro creata. Quindi, selezionare l'area di lavoro personalizzata configurando l'impostazione dell'area di lavoro predefinita in modo da acconsentire esplicitamente alla riconfigurazione degli agenti già installati. Per altre informazioni, vedere [Come usare l'area di lavoro di Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quali sono le implicazioni del rifiuto esplicito del provisioning automatico?
Al termine della migrazione, il Centro sicurezza non potrà raccogliere i dati sulla sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Se si rifiuta esplicitamente, è consigliabile installare manualmente Microsoft Monitoring Agent. Vedere [Procedure consigliate quando si rifiuta esplicitamente](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quali sono le procedure consigliate quando si rifiuta esplicitamente il provisioning automatico?
È consigliabile installare manualmente Microsoft Monitoring Agent in modo che il Centro sicurezza possa raccogliere i dati sulla sicurezza dalle macchine virtuali e fornire raccomandazioni e avvisi. Vedere [Connettere computer Windows al servizio Log Analytics in Azure](../log-analytics/log-analytics-windows-agent.md) per indicazioni sull'installazione.

È possibile connettere l'agente a qualsiasi area di lavoro personalizzata esistente o all'area di lavoro creata dal Centro sicurezza. Se per un'area di lavoro personalizzata non è abilitata la soluzione ‘Security’ o 'SecurityCenterFree', sarà necessario applicare una soluzione. A tale scopo, selezionare l'area di lavoro personalizzata o la sottoscrizione e applicare un piano tariffario tramite il pannello **Criteri di sicurezza - Piano tariffario**.

   ![Piano tariffario][1]

Il Centro sicurezza abiliterà la corretta soluzione nell'area di lavoro in base al piano tariffario selezionato.

### Come si rimuovono le estensioni di OMS installate dal Centro sicurezza?<a name="remove-oms"></a>
È possibile rimuovere manualmente Microsoft Monitoring Agent. Questa operazione non è consigliata perché limita le raccomandazioni e gli avvisi del Centro sicurezza.

> [!NOTE]
> Se la raccolta di dati è abilitata, il Centro sicurezza reinstallerà l'agente dopo la rimozione.  È necessario disabilitare la raccolta di dati prima di rimuovere manualmente l'agente. Per istruzioni sulla disabilitazione della raccolta di dati, vedere [Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?).
>
>

Per rimuovere manualmente l'agente:

1.  Nel portale aprire **Log Analytics**.
2.  Nel pannello di Log Analytics selezionare un'area di lavoro:
3.  Selezionare ogni VM da escludere dal monitoraggio, quindi selezionare **Disconnetti**.

   ![Rimuovere l'agente][3]

> [!NOTE]
> Se una VM Linux include già un agente OMS non dell'estensione, la rimozione dell'estensione comporta anche la rimozione dell'agente e il cliente dovrà reinstallarlo.
>
>
### <a name="how-do-i-disable-data-collection"></a>Come si disabilita la raccolta dati?
Il provisioning automatico è disattivato per impostazione predefinita. È possibile disabilitare il provisioning automatico nelle risorse in qualsiasi momento disattivando questa impostazione nei criteri di sicurezza. Il provisioning automatico è fortemente consigliato per ottenere gli avvisi di sicurezza e i suggerimenti sugli aggiornamenti del sistema, le vulnerabilità del sistema operativo e la protezione degli endpoint.

Per disabilitare la raccolta di dati, [accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, **Centro sicurezza**, quindi selezionare**Selezione criteri**. Selezionare la sottoscrizione per cui si desidera disabilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **Off**.

### <a name="how-do-i-enable-data-collection"></a>Come si abilita la raccolta dati?
È possibile abilitare la raccolta dei dati per la sottoscrizione nei criteri di sicurezza. Per abilitare la raccolta di dati. [Accedere al portale di Azure](https://portal.azure.com), selezionare **Sfoglia**, quindi **Centro sicurezza** e infine **Criteri di sicurezza**. Selezionare la sottoscrizione per cui si desidera abilitare il provisioning automatico. Quando si seleziona una sottoscrizione, si apre **Criteri di sicurezza - Raccolta dati**. In **Auto provisioning**, (Provisioning automatico) selezionare **On**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Cosa accade quando si abilita la raccolta dati?
Dopo aver abilitato il provisioning automatico, il Centro sicurezza effettua il provisioning di Microsoft Monitoring Agent in tutte le macchine virtuali di Azure supportate e in quelle nuove che vengono create. Il provisioning automatico è fortemente consigliato, ma è disponibile anche l'installazione manuale dell'agente. [Informazioni sull'installazione dell'estensione Microsoft Monitoring Agent](../log-analytics/log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

L'agente abilita l'evento di creazione di processi 4688 e il campo *CommandLine* all'interno dell'evento 4688. I nuovi processi creati nella macchina virtuale vengono registrati da EventLog e monitorati dai servizi di rilevamento del Centro sicurezza. Per informazioni sui dettagli registrati per ogni nuovo processo, vedere i [campi descrizione in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). L'agente inoltre raccoglie gli eventi 4688 creati nella macchina virtuale e li archivia nella ricerca.

L'agente abilita anche la raccolta di dati per i [controlli applicazione adattivi](security-center-adaptive-application.md). Centro sicurezza consente di configurare criteri AppLocker locali in modalità di controllo per consentire tutte le applicazioni. In questo modo AppLocker genererà eventi che vengono poi raccolti e sfruttati dal Centro sicurezza. È importante notare che questi criteri non saranno configurati nei computer in cui è già configurato un criterio AppLocker. 

Quando il Centro sicurezza rileva attività sospette nella macchina virtuale, il cliente riceve una notifica tramite posta elettronica se sono state fornite le [informazioni sul contatto di sicurezza](security-center-provide-security-contact-details.md). Viene visualizzato un avviso anche nel dashboard degli avvisi di sicurezza del Centro sicurezza.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>L'agente di monitoraggio compromettere le prestazioni dei server?
L'agente e usa una quantità nominale delle risorse di sistema e dovrebbe avere un impatto minimo sulle prestazioni. Per altre informazioni sull'agente, sull'estensione e sull'impatto sulle prestazioni, vedere la [guida alla pianificazione e alla gestione](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Dove vengono archiviati i dati?
I dati raccolti dall'agente vengono archiviati in un'area di lavoro di Log Analytics esistente associata alla sottoscrizione o in una nuova area di lavoro. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md).

## Clienti di Log Analytics esistenti<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Il Centro sicurezza esegue l'override di eventuali connessioni esistenti tra le macchine virtuali e le aree di lavoro?
Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza non esegue l'override della connessione all'area di lavoro esistente. Il Centro sicurezza usa l'area di lavoro esistente.

Una soluzione del Centro sicurezza viene installata nell'area di lavoro, se non è già presente, e la soluzione viene applicata solo alle VM rilevanti. Quando viene aggiunta, la soluzione viene automaticamente distribuita per impostazione predefinita a tutti gli agenti di Windows e Linux connessi all'area di lavoro di Log Analytics. Il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md) consente di applicare un ambito alle soluzioni.

Se Microsoft Monitoring Agent è installato direttamente sulla macchina virtuale, non come estensione di Azure, il Centro sicurezza non lo installerà e il monitoraggio della sicurezza sarà limitato.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Il Centro sicurezza installa soluzioni nelle aree di lavoro di Log Analytics esistenti? Quali solo le implicazioni relative alla fatturazione?
Quando il Centro sicurezza rileva che una VM è già connessa a un'area di lavoro creata, il Centro sicurezza abilita soluzioni in questa area di lavoro in base al piano tariffario specifico. Le soluzioni vengono applicate solo alle macchine virtuali rilevanti di Azure tramite il [targeting della soluzione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), quindi la fatturazione rimane invariata.

- **Livello Gratuito**: il Centro sicurezza installa la soluzione 'SecurityCenterFree' nell'area di lavoro. Non viene applicato alcun addebito per il livello Gratuito.
- **Livello Standard**: il Centro sicurezza installa la soluzione 'Security' nell'area di lavoro.

   ![Soluzioni nell'area di lavoro predefinita][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Nell'ambiente sono già presenti aree di lavoro. È possibile usarle per raccogliere i dati di sicurezza?
Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza usa l'area di lavoro connessa esistente. Una soluzione del Centro sicurezza viene installata nell'area di lavoro, se non è già presente, e la soluzione viene applicata solo alle VM rilevanti tramite il [targeting della soluzione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Quando il Centro sicurezza installa Microsoft Monitoring Agent nelle VM, usa le aree di lavoro predefinite create dal Centro sicurezza.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Nelle aree di lavoro è già presente una soluzione di sicurezza. Quali solo le implicazioni relative alla fatturazione?
La soluzione Sicurezza e controllo viene usata per abilitare le funzionalità Standard del Centro sicurezza per le VM di Azure. Se la soluzione Sicurezza e controllo è già installata in un'area di lavoro, il Centro sicurezza usa la soluzione esistente. La fatturazione rimane invariata.

## <a name="using-azure-security-center"></a>Utilizzo del Centro sicurezza di Azure
### <a name="what-is-a-security-policy"></a>Cosa sono i criteri di sicurezza?
I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione specificata. Nel Centro sicurezza di Azure è possibile definire i criteri per le sottoscrizioni di Azure in base ai requisiti di sicurezza della società e al tipo di applicazione o al livello di riservatezza dei dati in ciascuna sottoscrizione.

I criteri di sicurezza abilitati nel Centro sicurezza di Azure determinano il monitoraggio e i suggerimenti per la sicurezza. Per ulteriori informazioni sui criteri di sicurezza, vedere [Monitoraggio dello stato di sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Chi può modificare i criteri di sicurezza?
Per modificare i criteri di sicurezza, è necessario essere Amministratore della protezione, proprietario o collaboratore di tale sottoscrizione.

Per informazioni su come configurare i criteri di sicurezza, vedere [Impostazione dei criteri di sicurezza nel Centro sicurezza di Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>Che cos'è un suggerimento per la sicurezza?
Il Centro sicurezza di Azure analizza lo stato di sicurezza delle risorse di Azure. Quando vengono identificate potenziali vulnerabilità di sicurezza, vengono creati i suggerimenti. Tali suggerimenti illustrano in dettaglio il processo di configurazione dei controlli necessari. Alcuni esempi:

* Provisioning di antimalware per identificare e rimuovere il software dannoso
* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md) e regole per controllare il traffico verso le macchine virtuali
* Provisioning di un Web application firewall per la difesa da attacchi diretti alle applicazioni Web
* Distribuzione degli aggiornamenti di sistema mancanti
* Risoluzione delle configurazioni del sistema operativo che non corrispondono alle baseline consigliate

Qui vengono visualizzati solo i suggerimenti abilitati in Criteri di sicurezza.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Come è possibile visualizzare lo stato di sicurezza corrente delle risorse Azure?
Il pannello **Security Center Overview** (Panoramica del Centro sicurezza) mostra la situazione generale relativamente alla sicurezza dell'ambiente ripartito per calcolo, rete, archiviazioni e dati e applicazioni. Ciascun tipo di risorsa presenta un indicatore che visualizza l'eventuale rilevamento di potenziali vulnerabilità di sicurezza. Facendo clic su ogni sezione viene visualizzato un elenco di problemi di sicurezza identificati dal Centro sicurezza, insieme a un inventario delle risorse nella sottoscrizione.

### <a name="what-triggers-a-security-alert"></a>Che cosa attiva un avviso di sicurezza?
Il Centro sicurezza di Azure raccoglie, analizza e unisce automaticamente i dati di log dalle risorse di Azure, dalla rete e dalle soluzioni dei partner, ad esempio antimalware e firewall. Quando vengono rilevate minacce, viene creato un avviso di sicurezza. Ad esempio, è compreso il rilevamento di:

* Macchine virtuali compromesse in comunicazione con indirizzi IP dannosi noti
* Malware avanzato rilevato mediante i report degli errori di Windows
* Attacchi di forza bruta contro le macchine virtuali
* Avvisi di sicurezza da soluzioni di sicurezza integrata dei partner, ad esempio antimalware o Web application firewall

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Qual è la differenza tra le minacce rilevate e le minacce segnalate da Microsoft Security Response Center e dal Centro sicurezza di Azure?
Microsoft Security Response Center (MSRC) esegue il monitoraggio selettivo della sicurezza della rete e dell'infrastruttura di Azure e riceve informazioni sulle minacce e segnalazioni di violazioni da terzi. Se MSRC rileva che un'entità illegale o non autorizzata ha ottenuto l'accesso a dati del cliente o che l'uso di Azure da parte del cliente non è conforme ai criteri d'uso, un responsabile della sicurezza segnala il problema al cliente. Come notifica viene in genere inviato un messaggio di posta elettronica ai contatti per la sicurezza specificati nel Centro sicurezza di Azure oppure al proprietario della sottoscrizione di Azure se non è specificato nessun contatto per la sicurezza.

Il Centro sicurezza PC è un servizio di Azure che esegue il monitoraggio continuo dell'ambiente Azure del cliente e applica metodi di analisi per rilevare un'ampia gamma di attività potenzialmente dannose. I rilevamenti vengono visualizzati come di avvisi di sicurezza nel dashboard del Centro sicurezza PC.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Quali risorse di Azure vengono monitorate dal Centro sicurezza di Azure?
Il Centro sicurezza di Azure monitora le risorse di Azure seguenti:

* Macchine virtuali (VM) (inclusi i [Servizi cloud](../cloud-services/cloud-services-choose-me.md))
* Reti virtuali di Azure
* Servizio di SQL Azure
* Account di archiviazione di Azure
* App Web di Azure in un [ambiente del servizio app](../app-service/environment/intro.md)
* Soluzioni partner integrate con la sottoscrizione di Azure, ad esempio un Web application firewall, nelle VM e nell'ambiente del servizio app

## <a name="virtual-machines"></a>Macchine virtuali
### <a name="what-types-of-virtual-machines-are-supported"></a>Quali tipi di macchine virtuali sono supportati?
Il monitoraggio e le indicazioni sono disponibili per le macchine virtuali create usando i [modelli di distribuzione classica e Resource Manager](../azure-classic-rm.md).

Per l'elenco delle piattaforme supportate vedere [Supported platforms in Azure Security Center](security-center-os-coverage.md) (Piattaforme supportate nel Centro sicurezza di Azure).

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Perché il Centro sicurezza di Azure non riconosce la soluzione antimalware in esecuzione nella VM Azure?
Il Centro sicurezza di Azure dispone di visibilità sull'antimalware installato tramite le estensioni di Azure. Ad esempio, il Centro sicurezza non è in grado di rilevare l'antimalware che è stato preinstallato in un'immagine fornita dall'utente o è stato installato nelle macchine virtuali mediante processi dell'utente (ad esempio sistemi di gestione della configurazione).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Perché viene visualizzato il messaggio "Dati di analisi mancanti" per la VM?
Questo messaggio viene visualizzato quando non sono presenti dati di analisi per una macchina virtuale. Dopo l'abilitazione della raccolta dei dati nel Centro sicurezza di Azure, l'analisi dei dati da popolare può richiedere tempo, in genere meno di un'ora. Dopo il popolamento iniziale dei dati di analisi, è possibile che si riceva questo messaggio perché non è presente alcun dato di analisi o non sono presenti dati di analisi recenti. Le analisi non saranno popolate per le macchine virtuali con stato arrestato. Questo messaggio potrebbe essere visualizzato anche se i dati di analisi non sono stati inseriti di recente, in conformità ai criteri di conservazione per l'agente Windows, che ha un valore predefinito di 30 giorni.

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Con quale frequenza il Centro sicurezza esegue l'analisi alla ricerca di vulnerabilità del sistema operativo, aggiornamenti del sistema e problemi di protezione degli endpoint?
La latenza con cui il Centro sicurezza esegue l'analisi alla ricerca di vulnerabilità, aggiornamenti e problemi è riportata di seguito:

- Configurazioni di sicurezza del sistema operativo: i dati vengono aggiornati entro 48 ore
- Aggiornamenti di sistema: i dati vengono aggiornati entro 24 ore
- Problemi di protezione degli endpoint: i dati vengono aggiornati entro 8 ore

In genere il Centro sicurezza esegue l'analisi alla ricerca di nuovi dati ogni ora. I valori di latenza sopra riportati fanno riferimento a uno scenario pessimistico, ovvero nel caso in cui non sia disponibile un'analisi recente o l'analisi abbia avuto esito negativo.

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Perché viene visualizzato il messaggio "L'agente di macchine virtuali non è presente?"
Per abilitare la raccolta dei dati, l'agente di macchine virtuali deve essere installato nelle VM. Per impostazione predefinita, l'agente di macchine virtuali è installato nelle macchine virtuali distribuite da Azure Marketplace. Per altre informazioni su come installare l'agente di maccine virtuali in altre VM, vedere il post di blog [Estensioni e agente di macchine virtuali](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png

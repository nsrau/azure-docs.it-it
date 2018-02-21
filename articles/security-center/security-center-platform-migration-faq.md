---
title: Domande frequenti sulla migrazione della piattaforma del Centro sicurezza | Microsoft Docs
description: Questa pagina fornisce risposte alle domande relative alla migrazione della piattaforma del Centro sicurezza.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: terrylan
ms.openlocfilehash: 6ccf104ea09dc1fbce1dd34a06168205d6f5fac8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="security-center-platform-migration-faq"></a>Domande frequenti sulla migrazione della piattaforma del Centro sicurezza
All'inizio di giugno 2017 il Centro sicurezza di Azure ha iniziato a usare Microsoft Monitoring Agent per la raccolta e l'archiviazione dei dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Questa pagina fornisce risposte alle domande relative alla migrazione della piattaforma.

## <a name="data-collection-agents-and-workspaces"></a>Raccolta di dati, agenti e aree di lavoro

### <a name="how-is-data-collected"></a>In che modo vengono raccolti i dati?
Il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere dati di sicurezza dalle VM, ad esempio informazioni su:

- configurazioni di sicurezza, usate per identificare le vulnerabilità
- eventi di sicurezza, usati per rilevare le minacce

I dati raccolti dall'agente vengono archiviati in un'area di lavoro esistente di Log Analytics connessa alla VM o in una nuova area di lavoro creata dal Centro sicurezza. Quando il Centro sicurezza crea una nuova area di lavoro, viene presa in considerazione l'area geografica della VM.

> [!NOTE]
> Microsoft Monitoring Agent è lo stesso agente usato da Operations Management Suite (OMS), dal servizio Log Analytics e da System Center Operations Manager (SCOM).
>
>

Quando il provisioning automatico (chiamato in precedenza Raccolta log) è abilitato o quando viene eseguita la migrazione delle sottoscrizioni, il Centro sicurezza verifica se Microsoft Monitoring Agent è già installato come estensione di Azure in ogni VM. Se Microsoft Monitoring Agent non è installato, il Centro sicurezza eseguirà queste operazioni per impostazione predefinita:

- Installazione dell'estensione Microsoft Monitoring Agent nella VM.

   - Se esiste già un'area di lavoro creata dal Centro sicurezza nella stessa area geografica della VM, l'agente viene connesso a tale area di lavoro.
   - Se non esiste alcuna area di lavoro, il Centro sicurezza crea un nuovo gruppo di risorse e un'area di lavoro predefinita in tale area geografica e quindi connette l'agente all'area di lavoro. Ecco le convenzioni di denominazione per l'area di lavoro e il gruppo di risorse:

       Area di lavoro: DefaultWorkspace-[subscription-ID]-[geo]

       Gruppo di risorse: DefaultResouceGroup-[geo]

- Abilitazione di una soluzione del Centro sicurezza nell'area di lavoro in base al piano tariffario associato alla VM nel Centro sicurezza. Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/).
- Solo per le sottoscrizioni di cui è stata eseguita la migrazione, il Centro sicurezza rimuoverà anche l'agente di monitoraggio di Azure precedente.

> [!NOTE]
> È possibile eseguire l'override dell'installazione automatica di Microsoft Monitoring Agent e usare la propria area di lavoro.  Vedere [Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) e [Come usare l'area di lavoro di Log Analytics esistente](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

La posizione dell'area di lavoro dipende dalla posizione della VM. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md). Se una sottoscrizione contiene macchine virtuali da più aree geografiche, il Centro sicurezza crea più aree di lavoro. Vengono create più aree di lavoro per gestire le regole sulla privacy dei dati.

> [!NOTE]
> Prima della migrazione della piattaforma, il Centro sicurezza raccoglie i dati di sicurezza dalle VM usando l'Agente di monitoraggio di Azure e archivia i dati nell'account di archiviazione. Dopo la migrazione della piattaforma, il Centro sicurezza usa Microsoft Monitoring Agent e l'area di lavoro per raccogliere e archiviare gli stessi dati. L'account di archiviazione può essere rimosso dopo la migrazione.  Il Centro sicurezza rimuove anche gli agenti di monitoraggio di Azure installati in precedenza dopo la migrazione della piattaforma.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Vengono addebitati costi per Log Analytics oppure OMS nelle aree di lavoro create dal Centro sicurezza?
di serie Le aree di lavoro create dal Centro sicurezza non comportano addebiti di OMS, benché siano configurate per OMS per la fatturazione per nodo. La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Livello Gratuito**: il Centro sicurezza abilita la soluzione 'SecurityCenterFree' nell'area di lavoro predefinita. Non viene applicato alcun addebito per il livello Gratuito.
- **Livello Standard**: il Centro sicurezza abilita la soluzione 'Security' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/). La pagina relativa ai prezzi illustra le modifiche apportate alla fatturazione per l'archiviazione dei dati di sicurezza e alla fatturazione ripartita a partire da giugno 2017.

> [!NOTE]
> Il piano tariffario di OMS per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza.
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

      ![Riconfigurare le macchine virtuali monitorate][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>Cosa accade se Microsoft Monitoring Agent è già stato installato come estensione nella VM?
Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivia i dati di sicurezza dalla VM nell'area di lavoro già connessa. Il Centro sicurezza aggiorna la versione dell'estensione in modo da includere l'ID risorsa di Azure della macchina virtuale per supportare l'uso del Centro sicurezza.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Cosa accade se Microsoft Monitoring Agent è installato nella macchina virtuale ma non come estensione?
Se Microsoft Monitoring Agent è installato direttamente sulla macchina virtuale, non come estensione di Azure, il Centro sicurezza non lo installerà e il monitoraggio della sicurezza sarà limitato.

Per altre informazioni, vedere la sezione successiva [Cosa accade se un agente diretto OMS o SCOM è già installato nella macchina virtuale?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm).

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>Cosa accade se un agente diretto OMS o SCOM è già installato nella macchina virtuale?
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

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Come si rimuovono le estensioni di OMS installate dal Centro sicurezza?
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

## <a name="existing-oms-customers"></a>Clienti di OMS esistenti

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Il Centro sicurezza esegue l'override di eventuali connessioni esistenti tra le macchine virtuali e le aree di lavoro?
Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza non esegue l'override della connessione all'area di lavoro esistente. Il Centro sicurezza usa l'area di lavoro esistente.

Una soluzione del Centro sicurezza viene installata nell'area di lavoro, se non è già presente, e la soluzione viene applicata solo alle VM rilevanti. Quando viene aggiunta, la soluzione viene automaticamente distribuita per impostazione predefinita a tutti gli agenti di Windows e Linux connessi all'area di lavoro di Log Analytics. Il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md), una funzionalità di OMS, consente di applicare un ambito alle soluzioni.

Se Microsoft Monitoring Agent è installato direttamente sulla macchina virtuale, non come estensione di Azure, il Centro sicurezza non lo installerà e il monitoraggio della sicurezza sarà limitato.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>Che cosa si deve fare se si sospetta che la migrazione della piattaforma di dati abbia interrotto la connessione tra una delle VM e l'area di lavoro?
Questo problema non si dovrebbe verificare. Nel caso in cui si verifichi, [creare una richiesta di supporto tecnico di Azure](../azure-supportability/how-to-create-azure-support-request.md) e includere i dettagli seguenti:

- ID della risorsa di Azure della VM interessata
- ID della risorsa di Azure dell'area di lavoro configurata nell'estensione prima dell'interruzione della connessione
- Agente e versione installata in precedenza

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Il Centro sicurezza installa soluzioni nelle aree di lavoro di OMS esistenti? Quali solo le implicazioni relative alla fatturazione?
Quando il Centro sicurezza rileva che una VM è già connessa a un'area di lavoro creata, il Centro sicurezza abilita soluzioni in questa area di lavoro in base al piano tariffario specifico. Le soluzioni vengono applicate solo alle macchine virtuali rilevanti di Azure tramite il [targeting della soluzione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), quindi la fatturazione rimane invariata.

- **Livello Gratuito**: il Centro sicurezza installa la soluzione 'SecurityCenterFree' nell'area di lavoro. Non viene applicato alcun addebito per il livello Gratuito.
- **Livello Standard**: il Centro sicurezza installa la soluzione 'Security' nell'area di lavoro.

   ![Soluzioni nell'area di lavoro predefinita][4]

> [!NOTE]
> La soluzione 'Security' in Log Analytics è la soluzione Sicurezza e controllo in OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Nell'ambiente sono già presenti aree di lavoro. È possibile usarle per raccogliere i dati di sicurezza?
Se in una VM è già installato Microsoft Monitoring Agent come estensione di Azure, il Centro sicurezza usa l'area di lavoro connessa esistente. Una soluzione del Centro sicurezza viene installata nell'area di lavoro, se non è già presente, e la soluzione viene applicata solo alle VM rilevanti tramite il [targeting della soluzione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Quando il Centro sicurezza installa Microsoft Monitoring Agent nelle VM, usa le aree di lavoro predefinite create dal Centro sicurezza. I clienti potranno presto configurare le aree di lavoro usate.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Nelle aree di lavoro è già presente una soluzione di sicurezza. Quali solo le implicazioni relative alla fatturazione?
La soluzione Sicurezza e controllo viene usata per abilitare le funzionalità Standard del Centro sicurezza per le VM di Azure. Se la soluzione Sicurezza e controllo è già installata in un'area di lavoro, il Centro sicurezza usa la soluzione esistente. La fatturazione rimane invariata.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla migrazione della piattaforma del Centro sicurezza, vedere:

- [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md)
- [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png

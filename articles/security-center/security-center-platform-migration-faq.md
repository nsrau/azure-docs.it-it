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
ms.date: 09/14/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="security-center-platform-migration-faq"></a>Domande frequenti sulla migrazione della piattaforma del Centro sicurezza
All'inizio di giugno 2017 il Centro sicurezza di Azure ha iniziato a usare Microsoft Monitoring Agent per la raccolta e l'archiviazione dei dati. Per altre informazioni, vedere [Migrazione della piattaforma del Centro sicurezza di Azure](security-center-platform-migration.md). Questa pagina fornisce risposte alle domande relative alla migrazione della piattaforma.

## <a name="data-collection-agents-and-workspaces"></a>Raccolta di dati, agenti e aree di lavoro

### <a name="how-is-data-collected"></a>In che modo vengono raccolti i dati?
Il Centro sicurezza usa Microsoft Monitoring Agent per raccogliere dati di sicurezza dalle VM, ad esempio informazioni sulle configurazioni di sicurezza, usate per identificare le vulnerabilità, ed eventi di sicurezza, usati per rilevare le minacce. I dati raccolti dall'agente vengono archiviati in un'area di lavoro esistente di Log Analytics connessa alla VM o in una nuova area di lavoro creata dal Centro sicurezza. Quando il Centro sicurezza crea una nuova area di lavoro, viene presa in considerazione l'area geografica della VM.

> [!NOTE]
> Microsoft Monitoring Agent è lo stesso agente usato da Operations Management Suite (OMS), dal servizio Log Analytics e da System Center Operations Manager (SCOM).
>
>

Quando la raccolta di dati viene abilitata per la prima volta o quando viene eseguita la migrazione delle sottoscrizioni, il Centro sicurezza verifica se Microsoft Monitoring Agent è già installato come estensione di Azure in ogni VM. Se Microsoft Monitoring Agent non è installato, il Centro sicurezza eseguirà queste operazioni:

- Installazione di Microsoft Monitoring Agent nella VM.
   - Se esiste già un'area di lavoro creata dal Centro sicurezza nella stessa area geografica della VM, l'agente viene connesso a tale area di lavoro.
   - Se non esiste alcuna area di lavoro, il Centro sicurezza crea un nuovo gruppo di risorse e un'area di lavoro predefinita in tale area geografica e quindi connette l'agente all'area di lavoro. Ecco le convenzioni di denominazione per l'area di lavoro e il gruppo di risorse:

       Area di lavoro: DefaultWorkspace-[subscription-ID]-[geo]

       Gruppo di risorse: DefaultResouceGroup-[geo]
- Installazione di una soluzione del Centro sicurezza nell'area di lavoro

La posizione dell'area di lavoro dipende dalla posizione della VM. Per altre informazioni, vedere [Sicurezza dei dati](security-center-data-security.md).

> [!NOTE]
> Prima della migrazione della piattaforma, il Centro sicurezza raccoglie i dati di sicurezza dalle VM usando l'Agente di monitoraggio di Azure e archivia i dati nell'account di archiviazione. Dopo la migrazione della piattaforma, il Centro sicurezza usa Microsoft Monitoring Agent e l'area di lavoro per raccogliere e archiviare gli stessi dati. L'account di archiviazione può essere rimosso dopo la migrazione.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Vengono addebitati costi per Log Analytics oppure OMS nelle aree di lavoro create dal Centro sicurezza?
No. Le aree di lavoro create dal Centro sicurezza non comportano addebiti di OMS, benché siano configurate per OMS per la fatturazione per nodo. La fatturazione del Centro sicurezza è sempre basata sui criteri di sicurezza del Centro sicurezza e sulle soluzioni installate in un'area di lavoro:

- **Livello Gratuito**: il Centro sicurezza installa la soluzione 'SecurityCenterFree' nell'area di lavoro predefinita. Non viene applicato alcun addebito per il livello Gratuito.
- **Livello Standard**: il Centro sicurezza installa le soluzioni 'SecurityCenterFree' e 'Security' nell'area di lavoro predefinita.

Per altre informazioni sui prezzi, vedere [Prezzi di Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/). La pagina relativa ai prezzi illustra le modifiche apportate alla fatturazione per l'archiviazione dei dati di sicurezza e alla fatturazione ripartita a partire da giugno 2017.

> [!NOTE]
> Il piano tariffario di OMS per le aree di lavoro create dal Centro sicurezza non influisce sulla fatturazione del Centro sicurezza.
>
>

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
   > Nel menu a discesa vengono visualizzati solo le aree di lavoro a cui si ha accesso e che si trovano nella sottoscrizione di Azure.
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
Il Centro sicurezza non esegue l'override delle connessioni esistenti alle aree di lavoro degli utenti. Il Centro sicurezza archivia i dati di sicurezza dalla VM nell'area di lavoro già connessa.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>Cosa accade se Microsoft Monitoring Agent è installato nella macchina virtuale ma non come estensione?
Se Microsoft Monitoring Agent è installato direttamente sulla macchina virtuale, non come estensione di Azure, il Centro sicurezza non lo installerà e il monitoraggio della sicurezza sarà limitato.

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Qual è l'impatto della rimozione delle estensioni?
Se si rimuove l'estensione Microsoft Monitoring, il Centro sicurezza non potrà raccogliere i dati di sicurezza dalla VM e alcune raccomandazioni e alcuni avvisi di sicurezza non saranno disponibili. Entro 24 ore il Centro sicurezza determina che nella VM non è presente l'estensione e la reinstalla.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Come si interrompono l'installazione automatica dell'agente e la creazione automatica dell'area di lavoro?
È possibile disattivare il provisioning automatico per le sottoscrizioni nei criteri di sicurezza, ma questa opzione non è consigliata. La disattivazione del provisioning automatico limita le raccomandazioni e gli avvisi del Centro sicurezza. Il provisioning automatico è obbligatorio per le sottoscrizioni con piano tariffario Standard. Per disabilitare il provisioning automatico:

1. Se la sottoscrizione è configurata per il livello Standard, aprire i criteri di sicurezza per tale sottoscrizione e selezionare il livello **Gratuito**.

   ![Piano tariffario ][1]

2. Disattivare quindi il provisioning automatico selezionando **Disattivato** nel pannello **Security policy – Data collection** (Criteri di sicurezza - Raccolta dati).
   ![Raccolta di dati][2]

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
- **Livello Standard**: il Centro sicurezza installa le soluzioni 'SecurityCenterFree' e 'Security' nell'area di lavoro.

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


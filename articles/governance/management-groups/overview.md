---
title: Organizzare le risorse con i gruppi di gestione - Governance di Azure
description: Informazioni sui gruppi di gestione, sul funzionamento delle autorizzazioni e sul relativo utilizzo.
ms.date: 04/15/2020
ms.topic: overview
ms.openlocfilehash: 43c8bb2bdb71b0b75d2fcc31451952214978093c
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773152"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizzare le risorse con i gruppi di gestione di Azure

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione. I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono.
Tutte le sottoscrizioni all'interno di un singolo gruppo di gestione devono considerare attendibile lo stesso tenant di Azure Active Directory.

Ad esempio, è possibile applicare a un gruppo di gestione criteri che limitano le aree disponibili per la creazione di macchine virtuali (VM). Questi criteri verranno applicati a tutti i gruppi di gestione, le sottoscrizioni e le risorse all'interno del gruppo, consentendo la creazione di macchine virtuali esclusivamente in tale area.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Gerarchia di gruppi di gestione e sottoscrizioni

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni, in modo da organizzare le risorse in una gerarchia per la gestione unificata di accesso e criteri. Il diagramma seguente mostra un esempio di creazione di una gerarchia per la governance tramite gruppi di gestione.

:::image type="content" source="./media/tree.png" alt-text="Esempio di un albero gerarchico dei gruppi di gestione" border="false":::

È possibile creare una gerarchia che applica un criterio, ad esempio che limita le posizioni delle VM all'area Stati Uniti occidentali nel gruppo denominato "Produzione". Questo criterio erediterà da tutte le sottoscrizioni del Contratto Enterprise (EA) discendenti del gruppo di gestione e verrà applicato a tutte le macchine virtuali all'interno delle sottoscrizioni. Questo criterio di sicurezza non potrà essere modificato dal proprietario della risorsa o della sottoscrizione e garantisce così una governance migliore.

Un altro scenario in cui è utile usare gruppi di gestione è per fornire agli utenti l'accesso a più sottoscrizioni. Spostando più sottoscrizioni all'interno del gruppo di gestione, è possibile creare un'assegnazione di [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) nel gruppo di gestione, con ereditarietà di tale accesso in tutte le sottoscrizioni. Una sola assegnazione nel gruppo di gestione può consentire agli utenti di accedere a tutte le risorse necessarie invece di eseguire script di controllo degli accessi in base al ruolo per diverse sottoscrizioni.

### <a name="important-facts-about-management-groups"></a>Informazioni importanti sui gruppi di gestione

- Una singola directory può supportare 10.000 gruppi di gestione.
- L'albero di un gruppo di gestione può supportare fino a sei livelli di profondità.
  - Questo limite non include il livello radice o il livello sottoscrizione.
- Ogni gruppo di gestione e sottoscrizione può supportare un solo elemento padre.
- Ogni gruppo di gestione può avere molti elementi figlio.
- Tutte le sottoscrizioni e i gruppi di gestione si trovano all'interno di una singola gerarchia in ogni directory. Vedere [Informazioni importanti sul gruppo di gestione radice](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Gruppo di gestione radice per ogni directory

A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Inizialmente l'[Amministratore globale di Azure AD deve elevare se stesso](../../role-based-access-control/elevate-access-global-admin.md) al ruolo Amministratore Accesso utenti di questo gruppo radice. Dopo aver elevato l'accesso, l'amministratore può assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo ad altri utenti della directory o gruppi per gestire la gerarchia. Gli amministratori possono assegnare il proprio account come proprietario del gruppo di gestione radice.

### <a name="important-facts-about-the-root-management-group"></a>Informazioni importanti sul gruppo di gestione radice

- Per impostazione predefinita, il nome visualizzato del gruppo di gestione radice è **Gruppo radice tenant**. L'ID corrisponde all'ID di Azure Active Directory.
- Per cambiare il nome visualizzato, all'account deve essere assegnato il ruolo Proprietario o Collaboratore per il gruppo di gestione radice. Vedere [Modificare il nome di un gruppo di gestione](manage.md#change-the-name-of-a-management-group) per aggiornare il nome del gruppo di gestione.
- A differenza degli altri gruppi di gestione, il gruppo di gestione radice non può essere spostato o eliminato.  
- Tutte le sottoscrizioni e i gruppi di gestione sono ricondotti all'unico gruppo di gestione radice all'interno della directory.
  - Tutte le risorse nella directory sono ricondotte al gruppo di gestione radice ai fini della gestione globale.
  - Le nuove sottoscrizioni vengono inserite automaticamente nel gruppo di gestione radice al momento della creazione.
- Tutti i clienti di Azure possono vedere il gruppo di gestione radice, ma non tutti i clienti dispongono dell'accesso per gestire il gruppo di gestione radice.
  - Chiunque abbia accesso a una sottoscrizione può vedere il contesto in cui tale sottoscrizione si trova nella gerarchia.  
  - A nessun utente viene assegnato l'accesso predefinito al gruppo di gestione radice. Gli amministratori globali di Azure AD sono gli unici utenti che possono elevare i propri privilegi per ottenere l'accesso. Dopo avere ottenuto l'accesso al gruppo di gestione radice, gli amministratori globali possono assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo agli altri utenti per la  
    gestione.
- In SDK il gruppo di gestione radice, o 'radice del tenant', funge da gruppo di gestione.

> [!IMPORTANT]
> Qualsiasi assegnazione di accesso utente o di criteri nel gruppo di gestione radice **viene applicata a tutte le risorse all'interno della directory**. Per questo motivo, tutti i clienti devono valutare la necessità di disporre di elementi definiti in questo ambito. Le assegnazioni di accesso utente e di criteri devono essere "obbligatori" solo in questo  
> ambito.

## <a name="initial-setup-of-management-groups"></a>Configurazione iniziale dei gruppi di gestione

Quando un utente inizia a usare i gruppi di gestione, si verifica un processo di configurazione iniziale. Il primo passaggio è la creazione del gruppo di gestione radice nella directory. Dopo avere creato questo gruppo, tutte le sottoscrizioni esistenti nella directory diventano elementi figlio del gruppo di gestione radice.
Lo scopo di questo processo è assicurarsi che esista un'unica gerarchia di gruppi di gestione all'interno di una directory. Un'unica gerarchia all'interno della directory consente ai clienti amministrativi di applicare i criteri e l'accesso globale per cui gli altri clienti all'interno della directory non possono eseguire il bypass. Un valore assegnato alla radice verrà applicato all'intera gerarchia, che include tutti i gruppi di gestione, le sottoscrizioni, i gruppi di risorse e le risorse all'interno di quel tenant di Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Difficoltà a visualizzare tutte le sottoscrizioni

In alcune directory che hanno iniziato a usare gruppi di gestione nelle prime fasi dell'anteprima antecedenti al 25 giugno 2018 potrebbe essere rilevato un problema per cui le sottoscrizioni non sono tutte incluse nella gerarchia. Il processo per includere le sottoscrizioni nella gerarchia è stato implementato dopo un'assegnazione di ruolo o di criteri nel gruppo di gestione radice della directory.

### <a name="how-to-resolve-the-issue"></a>Come risolvere il problema

Per risolvere il problema sono disponibili due opzioni.

- Rimuovere tutte le assegnazioni di ruoli e criteri dal gruppo di gestione radice
  - Rimuovendo tutte le assegnazioni di ruoli e criteri dal gruppo di gestione radice, questo servizio inserirà tutte le sottoscrizioni nella gerarchia nel successivo ciclo notturno. Questo processo garantisce che non vengano concesse accidentalmente assegnazioni di accesso o di criteri a tutte le sottoscrizioni dei tenant.
  - Il modo migliore per eseguire questo processo senza compromettere i servizi è applicare l'assegnazione del ruolo o dei criteri un livello sotto il gruppo di gestione radice. Successivamente è possibile rimuovere tutte le assegnazioni dall'ambito radice.
- Chiamare direttamente l'API per avviare il processo di recupero delle sottoscrizioni
  - Qualsiasi cliente nella directory può chiamare le API _TenantBackfillStatusRequest_ o _StartTenantBackfillRequest_. Quando viene chiamata l'API StartTenantBackfillRequest, si avvia il processo di configurazione iniziale che prevede il trasferimento di tutte le sottoscrizioni nella gerarchia. Questo processo avvia anche l'applicazione di tutte le nuove sottoscrizioni come elementi figlio del gruppo di gestione radice.
    Questo processo può essere eseguito senza modificare le assegnazioni nel livello radice. Chiamando l'API, si conferma che qualsiasi assegnazione di accesso o di criteri nella radice può essere applicata a tutte le sottoscrizioni.

Per eventuali domande su questo processo di backfill, contattare `managementgroups@microsoft.com`
  
## <a name="management-group-access"></a>Accesso ai gruppi di gestione

I gruppi di gestione di Azure supportano il [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) per tutte le definizioni di ruoli e gli accessi alle risorse.
Queste autorizzazioni vengono ereditate dalle risorse figlio presenti nella gerarchia. È possibile assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo a un gruppo di gestione che verrà ereditato fino alle risorse. Ad esempio, il ruolo Controllo degli accessi in base al ruolo Collaboratore Macchina virtuale può essere assegnato a un gruppo di gestione. Questo ruolo non dispone di alcuna azione sul gruppo di gestione, ma verrà ereditato da tutte le macchine virtuali in tale gruppo.

Il grafico seguente mostra l'elenco dei ruoli e delle azioni supportate per i gruppi di gestione.

| Nome del ruolo Controllo degli accessi in base al ruolo             | Create | Rinominare | Spostamento\*\* | Delete | Assegnare l'accesso | Assegnare un criterio | Lettura  |
|:-------------------------- |:------:|:------:|:--------:|:------:|:-------------:| :------------:|:-----:|
|Proprietario                       | X      | X      | X        | X      | X             | X             | X     |
|Collaboratore                 | X      | X      | X        | X      |               |               | X     |
|Collaboratore gruppo di gestione\*            | X      | X      | X        | X      |               |               | X     |
|Reader                      |        |        |          |        |               |               | X     |
|Lettore gruppo di gestione\*                 |        |        |          |        |               |               | X     |
|Collaboratore per i criteri delle risorse |        |        |          |        |               | X             |       |
|Amministratore accessi utente   |        |        |          |        | X             | X             |       |

\*: i ruoli Collaboratore gruppo di gestione e Lettore gruppo di gestione consentono agli utenti di eseguire le azioni solo nell'ambito del gruppo di gestione.  
\*\*: per spostare una sottoscrizione o un gruppo di gestione all'interno o all'esterno di un gruppo di gestione radice, non sono necessarie assegnazioni di ruolo. Per informazioni su come spostare elementi all'interno della gerarchia, vedere [Gestire le risorse con i gruppi di gestione](manage.md).

## <a name="custom-rbac-role-definition-and-assignment"></a>Definizione e assegnazione del ruolo Controllo degli accessi in base al ruolo personalizzato

Il supporto del ruolo Controllo degli accessi in base al ruolo personalizzato per i gruppi di gestione è attualmente disponibile in anteprima con alcune [limitazioni](#limitations). È possibile definire l'ambito del gruppo di gestione nell'ambito assegnabile della definizione del ruolo. Il ruolo Controllo degli accessi in base al ruolo personalizzato sarà quindi disponibile per l'assegnazione in tale gruppo di gestione e in qualsiasi gruppo di gestione, sottoscrizione, gruppo di risorse o risorsa in esso contenuti. Questo ruolo personalizzato erediterà la gerarchia in modo analogo a qualsiasi ruolo predefinito.  

### <a name="example-definition"></a>Definizione di esempio

La [definizione e la creazione di un ruolo personalizzato](../../role-based-access-control/custom-roles.md) non cambiano con l'inclusione di gruppi di gestione. Usare il percorso completo per definire il gruppo di gestione **/providers/Microsoft.Management/managementgroups/{groupId}** .

Usare l'ID del gruppo di gestione e non il nome visualizzato del gruppo di gestione. Questo errore comune si verifica poiché entrambi sono campi definiti personalizzati quando si crea un gruppo di gestione.

```json
...
{
  "Name": "MG Test Custom Role",
  "Id": "id", 
  "IsCustom": true,
  "Description": "This role provides members understand custom roles.",
  "Actions": [
    "Microsoft.Management/managementgroups/delete",
    "Microsoft.Management/managementgroups/read",
    "Microsoft.Management/managementgroup/write",
    "Microsoft.Management/managementgroup/subscriptions/delete",
    "Microsoft.Management/managementgroup/subscriptions/write",
    "Microsoft.resources/subscriptions/read",
    "Microsoft.Authorization/policyAssignments/*",
    "Microsoft.Authorization/policyDefinitions/*",
    "Microsoft.Authorization/policySetDefinitions/*",
    "Microsoft.PolicyInsights/*",
    "Microsoft.Authorization/roleAssignments/*",
    "Microsoft.Authorization/roledefinitions/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
        "/providers/microsoft.management/managementGroups/ContosoCorporate"
  ]
}
...
```

### <a name="issues-with-breaking-the-role-definition-and-assignment-hierarchy-path"></a>Problemi di interruzione della definizione del ruolo e del percorso della gerarchia di assegnazione

Le definizioni del ruolo sono con ambito assegnabile in qualsiasi punto all'interno della gerarchia del gruppo di gestione. È possibile definire una definizione del ruolo in un gruppo di gestione padre mentre l'assegnazione di ruolo effettiva è presente nella sottoscrizione figlio. Poiché esiste una relazione tra i due elementi, viene visualizzato un errore durante il tentativo di separare l'assegnazione dalla relativa definizione.

Esaminiamo, ad esempio, una piccola sezione di una gerarchia per un oggetto visivo.

:::image type="content" source="./media/subtree.png" alt-text="sottoalbero" border="false":::

Supponiamo che nel gruppo di gestione Marketing sia definito un ruolo personalizzato. Il ruolo personalizzato viene quindi assegnato alle due sottoscrizioni di valutazione gratuite.  

Se proviamo a spostare una di queste sottoscrizioni in modo da diventare un elemento figlio del gruppo di gestione Production, questo spostamento interrompe il percorso dall'assegnazione del ruolo di sottoscrizione alla definizione del ruolo del gruppo di gestione Marketing. In questo scenario verrà visualizzato un errore che informa che lo spostamento non è consentito poiché interrompe questa relazione.  

Sono disponibili alcune opzioni diverse per correggere questo scenario:
- Rimuovere l'assegnazione di ruolo dalla sottoscrizione prima di spostare la sottoscrizione in un nuovo gruppo di gestione padre.
- Aggiungere la sottoscrizione all'ambito assegnabile della definizione del ruolo.
- Modificare l'ambito assegnabile all'interno della definizione del ruolo. Nell'esempio precedente, è possibile aggiornare gli ambiti assegnabili da Marketing a Root Management Group in modo che la definizione possa essere raggiunta da entrambi i rami della gerarchia.  
- Creare un ruolo personalizzato aggiuntivo che verrà definito nell'altro ramo. Per questo nuovo ruolo sarà necessario modificare anche l'assegnazione di ruolo nella sottoscrizione.  

### <a name="limitations"></a>Limitazioni  

Esistono alcune limitazioni quando si usano i ruoli personalizzati nei gruppi di gestione. 

 - È possibile definire un solo gruppo di gestione negli ambiti assegnabili di un nuovo ruolo. Questa limitazione è prevista per ridurre il numero di situazioni in cui le definizioni del ruolo e le assegnazioni di ruolo sono disconnesse. Questa situazione si verifica quando una sottoscrizione o un gruppo di gestione con un'assegnazione di ruolo viene spostato in un elemento padre diverso che non contiene la definizione del ruolo.  
 - Le azioni del piano dati di Controllo degli accessi in base al ruolo non possono essere definite nei ruoli personalizzati del gruppo di gestione. Questa restrizione è prevista perché si verifica un problema di latenza con le azioni di Controllo degli accessi in base al ruolo che aggiornano i provider di risorse del piano dati.
   Questo problema di latenza è in fase di analisi e queste azioni verranno disabilitate dalla definizione del ruolo per ridurre eventuali rischi.
 - Azure Resource Manager non convalida l'esistenza del gruppo di gestione nell'ambito assegnabile della definizione del ruolo. Se è presente un errore di digitazione o un ID gruppo di gestione non corretto, la definizione del ruolo verrà comunque creata.  

## <a name="moving-management-groups-and-subscriptions"></a>Spostamento di gruppi di gestione e sottoscrizioni 

Affinché un gruppo di gestione o una sottoscrizione possa essere spostato in modo da essere un elemento figlio di un altro gruppo di gestione, è necessario che siano soddisfatte tre regole.

Per eseguire l'azione di spostamento, è necessario avere: 

- Autorizzazioni di scrittura per il gruppo di gestione e l'assegnazione di ruolo nel gruppo di gestione o nella sottoscrizione figlio.
  - Esempio di ruolo predefinito: **Proprietario**
- Accesso in scrittura del gruppo di gestione nel gruppo di gestione padre di destinazione.
  - Esempio di ruolo predefinito: **Proprietario**, **Collaboratore**, **Collaboratore gruppo di gestione**
- Accesso in scrittura del gruppo di gestione nel gruppo di gestione padre esistente.
  - Esempio di ruolo predefinito: **Proprietario**, **Collaboratore**, **Collaboratore gruppo di gestione**

**Eccezione**: se il gruppo di gestione padre di destinazione o esistente è il gruppo di gestione radice, i requisiti delle autorizzazioni non sono applicabili. Poiché il gruppo di gestione radice è il punto di destinazione predefinito per tutti i nuovi gruppi di gestione e le sottoscrizioni, non è necessario avere le autorizzazioni per spostare un elemento.

Se il ruolo Proprietario nella sottoscrizione viene ereditato dal gruppo di gestione corrente, le destinazioni di spostamento sono limitate. È possibile spostare la sottoscrizione solo in un altro gruppo di gestione in cui si ha il ruolo Proprietario. Non è possibile spostarla in un gruppo di gestione in cui si ha il ruolo Collaboratore perché si perderebbe la proprietà della sottoscrizione. Se il ruolo Proprietario per la sottoscrizione è stato assegnato direttamente (non ereditato dal gruppo di gestione), è possibile eseguire lo spostamento in qualsiasi gruppo di gestione in cui si ha il ruolo Collaboratore.

## <a name="audit-management-groups-using-activity-logs"></a>Controllare i gruppi di gestione con i log attività

I gruppi di gestione sono supportati all'interno del [log attività di Azure](../../azure-monitor/platform/platform-logs-overview.md). È possibile cercare tutti gli eventi che si verificano per un gruppo di gestione nella stessa posizione centrale delle altre risorse di Azure. È ad esempio possibile vedere tutte le modifiche delle assegnazioni di ruoli o di criteri apportate a uno specifico gruppo di gestione.

:::image type="content" source="./media/al-mg.png" alt-text="Log attività con i gruppi di gestione" border="false":::

Quando si esegue una query sui gruppi di gestione all'esterno del portale di Azure, l'ambito di destinazione per tali gruppi sarà simile a **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](./create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](./manage.md)
- [Gruppi di gestione nel modulo Resources di Azure PowerShell](/powershell/module/az.resources#resources)
- [Gruppi di gestione nell'API REST](/rest/api/resources/managementgroups)
- [Gruppi di gestione nell'interfaccia della riga di comando di Azure](/cli/azure/account/management-group)

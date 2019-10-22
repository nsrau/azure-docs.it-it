---
title: Organizzare le risorse con i gruppi di gestione - Governance di Azure
description: Informazioni sui gruppi di gestione, sul funzionamento delle autorizzazioni e sul relativo utilizzo.
author: rthorn17
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: governance
ms.date: 04/22/2019
ms.author: rithorn
ms.topic: overview
ms.openlocfilehash: aa4c5a7bfe7333c02fe79612b828df5680b83b26
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254724"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizzare le risorse con i gruppi di gestione di Azure

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione. I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono. Tutte le sottoscrizioni all'interno di un singolo gruppo di gestione devono considerare attendibile lo stesso tenant di Azure Active Directory.

Ad esempio, è possibile applicare a un gruppo di gestione criteri che limitano le aree disponibili per la creazione di macchine virtuali (VM). Questi criteri verranno applicati a tutti i gruppi di gestione, le sottoscrizioni e le risorse all'interno del gruppo, consentendo la creazione di macchine virtuali esclusivamente in tale area.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Gerarchia di gruppi di gestione e sottoscrizioni

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni, in modo da organizzare le risorse in una gerarchia per la gestione unificata di accesso e criteri. Il diagramma seguente mostra un esempio di creazione di una gerarchia per la governance tramite gruppi di gestione.

![Esempio di un albero gerarchico dei gruppi di gestione](./media/tree.png)

È possibile creare una gerarchia che applica un criterio, ad esempio che limita le posizioni delle VM all'area Stati Uniti occidentali nel gruppo denominato "Produzione". Questo criterio erediterà da entrambe le sottoscrizioni EA all'interno del gruppo di gestione e verrà applicato a tutte le macchine virtuali all'interno delle sottoscrizioni. Questo criterio di sicurezza non potrà essere modificato dal proprietario della risorsa o della sottoscrizione e garantisce così una governance migliore.

Un altro scenario in cui è utile usare gruppi di gestione è per fornire agli utenti l'accesso a più sottoscrizioni. Spostando più sottoscrizioni all'interno del gruppo di gestione, è possibile creare un'assegnazione di [controllo degli accessi in base al ruolo](../../role-based-access-control/overview.md) nel gruppo di gestione, con ereditarietà di tale accesso in tutte le sottoscrizioni.
Una sola assegnazione nel gruppo di gestione può consentire agli utenti di accedere a tutte le risorse necessarie invece di eseguire script di controllo degli accessi in base al ruolo per diverse sottoscrizioni.

### <a name="important-facts-about-management-groups"></a>Informazioni importanti sui gruppi di gestione

- Una singola directory può supportare 10.000 gruppi di gestione.
- L'albero di un gruppo di gestione può supportare fino a sei livelli di profondità.
  - Questo limite non include il livello radice o il livello sottoscrizione.
- Ogni gruppo di gestione e sottoscrizione può supportare un solo elemento padre.
- Ogni gruppo di gestione può avere molti elementi figlio.
- Tutte le sottoscrizioni e i gruppi di gestione si trovano all'interno di una singola gerarchia in ogni directory. Vedere [Informazioni importanti sul gruppo di gestione radice](#important-facts-about-the-root-management-group).

## <a name="root-management-group-for-each-directory"></a>Gruppo di gestione radice per ogni directory

A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice".
Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice permette l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Inizialmente l'[Amministratore globale di Azure AD deve elevare se stesso](../../role-based-access-control/elevate-access-global-admin.md) al ruolo Amministratore Accesso utenti di questo gruppo radice. Dopo aver elevato l'accesso, l'amministratore può assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo ad altri utenti della directory o gruppi per gestire la gerarchia. Gli amministratori possono assegnare il proprio account come proprietario del gruppo di gestione radice.

### <a name="important-facts-about-the-root-management-group"></a>Informazioni importanti sul gruppo di gestione radice

- Per impostazione predefinita, il nome visualizzato del gruppo di gestione radice è **Gruppo radice tenant**. L'ID corrisponde all'ID di Azure Active Directory.
- Per cambiare il nome visualizzato, all'account deve essere assegnato il ruolo Proprietario o Collaboratore per il gruppo di gestione radice. Per la procedura, vedere [Modificare il nome di un gruppo di gestione](manage.md#change-the-name-of-a-management-group).
- A differenza degli altri gruppi di gestione, il gruppo di gestione radice non può essere spostato o eliminato.  
- Tutte le sottoscrizioni e i gruppi di gestione sono ricondotti all'unico gruppo di gestione radice all'interno della directory.
  - Tutte le risorse nella directory sono ricondotte al gruppo di gestione radice ai fini della gestione globale.
  - Le nuove sottoscrizioni vengono inserite automaticamente nel gruppo di gestione radice al momento della creazione.
- Tutti i clienti di Azure possono vedere il gruppo di gestione radice, ma non tutti i clienti dispongono dell'accesso per gestire il gruppo di gestione radice.
  - Chiunque abbia accesso a una sottoscrizione può vedere il contesto in cui tale sottoscrizione si trova nella gerarchia.  
  - A nessun utente viene assegnato l'accesso predefinito al gruppo di gestione radice. Gli amministratori globali di Azure AD sono gli unici utenti che possono elevare i propri privilegi per ottenere l'accesso.  Dopo avere ottenuto l'accesso al gruppo di gestione radice, gli amministratori globali possono assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo agli altri utenti per la gestione.  

> [!IMPORTANT]
> Qualsiasi assegnazione di accesso utente o di criteri nel gruppo di gestione radice **viene applicata a tutte le risorse all'interno della directory**.
> Per questo motivo, tutti i clienti devono valutare la necessità di disporre di elementi definiti in questo ambito.
> Le assegnazioni di accesso utente e di criteri devono essere "obbligatori" solo in questo ambito.  

## <a name="initial-setup-of-management-groups"></a>Configurazione iniziale dei gruppi di gestione

Quando un utente inizia a usare i gruppi di gestione, si verifica un processo di configurazione iniziale. Il primo passaggio è la creazione del gruppo di gestione radice nella directory. Dopo avere creato questo gruppo, tutte le sottoscrizioni esistenti nella directory diventano elementi figlio del gruppo di gestione radice. Lo scopo di questo processo è assicurarsi che esista un'unica gerarchia di gruppi di gestione all'interno di una directory. Un'unica gerarchia all'interno della directory consente ai clienti amministrativi di applicare i criteri e l'accesso globale per cui gli altri clienti all'interno della directory non possono eseguire il bypass. Un valore assegnato alla radice verrà applicato all'intera gerarchia, che include tutti i gruppi di gestione, le sottoscrizioni, i gruppi di risorse e le risorse all'interno di quel tenant di Azure AD.

## <a name="trouble-seeing-all-subscriptions"></a>Difficoltà a visualizzare tutte le sottoscrizioni

In alcune directory che hanno iniziato a usare gruppi di gestione nelle prime fasi dell'anteprima antecedenti al 25 giugno 2018 potrebbe essere rilevato un problema per cui le sottoscrizioni non sono tutte incluse nella gerarchia. Il processo per includere le sottoscrizioni nella gerarchia è stato implementato dopo un'assegnazione di ruolo o di criteri nel gruppo di gestione radice della directory. 

### <a name="how-to-resolve-the-issue"></a>Come risolvere il problema

Per risolvere il problema sono disponibili due opzioni.

1. Rimuovere tutte le assegnazioni di ruoli e criteri dal gruppo di gestione radice
   1. Rimuovendo tutte le assegnazioni di ruoli e criteri dal gruppo di gestione radice, questo servizio inserirà tutte le sottoscrizioni nella gerarchia nel successivo ciclo notturno.  Questo processo garantisce che non vengano concesse accidentalmente assegnazioni di accesso o di criteri a tutte le sottoscrizioni dei tenant.
   1. Il modo migliore per eseguire questo processo senza compromettere i servizi è applicare l'assegnazione del ruolo o dei criteri un livello sotto il gruppo di gestione radice. Successivamente è possibile rimuovere tutte le assegnazioni dall'ambito radice.
1. Chiamare direttamente l'API per avviare il processo di recupero delle sottoscrizioni
   1. Qualsiasi cliente nella directory può chiamare le API *TenantBackfillStatusRequest* o *StartTenantBackfillRequest*. Quando viene chiamata l'API StartTenantBackfillRequest, si avvia il processo di configurazione iniziale che prevede il trasferimento di tutte le sottoscrizioni nella gerarchia. Questo processo avvia anche l'applicazione di tutte le nuove sottoscrizioni come elementi figlio del gruppo di gestione radice. Questo processo può essere eseguito senza modificare le assegnazioni nel livello radice. Chiamando l'API, si conferma che qualsiasi assegnazione di accesso o di criteri nella radice può essere applicata a tutte le sottoscrizioni.

Per eventuali domande su questo processo di backfill, contattare managementgroups@microsoft.com  
  
## <a name="management-group-access"></a>Accesso ai gruppi di gestione

I gruppi di gestione di Azure supportano il [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) per tutte le definizioni di ruoli e gli accessi alle risorse.
Queste autorizzazioni vengono ereditate dalle risorse figlio presenti nella gerarchia. È possibile assegnare qualsiasi ruolo predefinito Controllo degli accessi in base al ruolo a un gruppo di gestione che verrà ereditato fino alle risorse.
Ad esempio, il ruolo Controllo degli accessi in base al ruolo Collaboratore Macchina virtuale può essere assegnato a un gruppo di gestione. Questo ruolo non dispone di alcuna azione sul gruppo di gestione, ma verrà ereditato da tutte le macchine virtuali in tale gruppo.

Il grafico seguente mostra l'elenco dei ruoli e delle azioni supportate per i gruppi di gestione.

| Nome del ruolo Controllo degli accessi in base al ruolo             | Create | Rinominare | Spostamento** | Delete | Assegnare l'accesso | Assegnare un criterio | Lettura  |
|:-------------------------- |:------:|:------:|:------:|:------:|:-------------:| :------------:|:-----:|
|Proprietario                       | X      | X      | X      | X      | X             | X             | X     |
|Collaboratore                 | X      | X      | X      | X      |               |               | X     |
|Collaboratore gruppo di gestione*             | X      | X      | X      | X      |               |               | X     |
|Reader                      |        |        |        |        |               |               | X     |
|Lettore gruppo di gestione*                  |        |        |        |        |               |               | X     |
|Collaboratore per i criteri delle risorse |        |        |        |        |               | X             |       |
|Amministratore accessi utente   |        |        |        |        | X             | X             |       |

*: i ruoli Collaboratore gruppo di gestione e Lettore gruppo di gestione consentono agli utenti di eseguire le azioni solo nell'ambito del gruppo di gestione.  
**: per spostare una sottoscrizione o un gruppo di gestione all'interno o all'esterno di un gruppo di gestione radice, non sono necessarie assegnazioni di ruolo.  Per informazioni su come spostare elementi all'interno della gerarchia, vedere [Gestire le risorse con i gruppi di gestione](manage.md).

### <a name="custom-rbac-role-definition-and-assignment"></a>Definizione e assegnazione di un ruolo personalizzato Controllo degli accessi in base al ruolo

Attualmente i ruoli personalizzati Controllo degli accessi in base al ruolo non sono supportati per i gruppi di gestione. Per visualizzare lo stato di questo elemento, vedere il [forum dei commenti sui gruppi di gestione](https://aka.ms/mgfeedback).

## <a name="audit-management-groups-using-activity-logs"></a>Controllare i gruppi di gestione con i log attività

I gruppi di gestione sono supportati all'interno del [log attività di Azure](../../azure-monitor/platform/activity-logs-overview.md). È possibile cercare tutti gli eventi che si verificano per un gruppo di gestione nella stessa posizione centrale delle altre risorse di Azure.  È ad esempio possibile vedere tutte le modifiche delle assegnazioni di ruoli o di criteri apportate a uno specifico gruppo di gestione.

![Log attività con i gruppi di gestione](media/al-mg.png)

Quando si esegue una query sui gruppi di gestione all'esterno del portale di Azure, l'ambito di destinazione per tali gruppi sarà simile a **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](manage.md)
- [Gruppi di gestione nel modulo Resources di Azure PowerShell](/powershell/module/az.resources#resources)
- [Gruppi di gestione nell'API REST](/rest/api/resources/managementgroups)
- [Gruppi di gestione nell'interfaccia della riga di comando di Azure](/cli/azure/account/management-group)

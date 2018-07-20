---
title: Organizzare le risorse con i gruppi di gestione di Azure | Microsoft Docs
description: Informazioni sui gruppi di gestione e sul loro uso.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/09/2018
ms.author: rithorn
ms.openlocfilehash: c8152a6c12c776806d9a17c5e434d825d6c91165
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38466644"
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Organizzare le risorse con i gruppi di gestione di Azure

Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I gruppi di gestione di Azure forniscono un livello di ambito oltre le sottoscrizioni. Le sottoscrizioni sono organizzate in contenitori chiamati "gruppi di gestione" a cui vengono applicate le condizioni di governance. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione. I gruppi di gestione offrono gestione di livello aziendale su larga scala, indipendentemente dal tipo di sottoscrizioni che si posseggono.

La funzionalità dei gruppi di gestione è disponibile come anteprima pubblica. Per iniziare a usare i gruppi di gestione, accedere al [portale di Azure](https://portal.azure.com) e cercare **Gruppi di gestione** nella sezione **Tutti i servizi**.

Ad esempio, è possibile applicare a un gruppo di gestione criteri che limitano le aree disponibili per la creazione di macchine virtuali (VM). Questi criteri verranno applicati a tutti i gruppi di gestione, le sottoscrizioni e le risorse all'interno del gruppo, consentendo la creazione di macchine virtuali esclusivamente in tale area.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>Gerarchia di gruppi di gestione e sottoscrizioni

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni, in modo da organizzare le risorse in una gerarchia per la gestione unificata di accesso e criteri.
Il diagramma seguente mostra una gerarchia di esempio costituita da gruppi di gestione e sottoscrizioni organizzati in base ai reparti.

![albero](media/management-groups/MG_overview.png)

Tramite la creazione di una gerarchia raggruppata per reparti, è possibile assegnare ruoli [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) che vengono *ereditati* dai reparti sottostanti al gruppo di gestione. Usando i gruppi di gestione è possibile ridurre il carico di lavoro e il rischio di errori, grazie alla possibilità di assegnare il ruolo una sola volta.

### <a name="important-facts-about-management-groups"></a>Informazioni importanti sui gruppi di gestione

- Una singola directory può supportare 10.000 gruppi di gestione.
- L'albero di un gruppo di gestione può supportare fino a sei livelli di profondità.
  - Questo limite non include il livello radice o il livello sottoscrizione.
- Ogni gruppo di gestione e sottoscrizione può supportare un solo elemento padre.
- Ogni gruppo di gestione può avere più elementi figlio.
- Tutte le sottoscrizioni e i gruppi di gestione sono contenuti all'interno di una singola gerarchia in ogni directory. Per le eccezioni durante l'anteprima, vedere [Informazioni importanti sul gruppo di gestione radice](#important-facts-about-the-root-management-group).

### <a name="preview-subscription-visibility-limitation"></a>Limitazione della visibilità delle sottoscrizioni nell'anteprima

Attualmente esiste una limitazione nell'anteprima che non consente di visualizzare le sottoscrizioni per cui è stato ereditato l'accesso. L'accesso alla sottoscrizione è stato ereditato, ma Azure Resource Manager non è ancora in grado di riconoscere questo tipo di accesso.  

Se si usa l'API REST per ottenere informazioni sulla sottoscrizione, vengono restituiti i dettagli poiché in realtà si dispone dell'accesso, ma nel portale di Azure e in Azure PowerShell le sottoscrizioni non vengono visualizzate.

Questa limitazione è attualmente in fase di analisi e verrà risolta prima dell'annuncio della "Disponibilità generale" dei gruppi di gestione.  

### <a name="cloud-solution-provider-csp-limitation-during-preview"></a>Limitazione per Provider di soluzioni cloud (CSP) nell'anteprima

Attualmente esiste una limitazione per i partner Provider di soluzioni cloud (CSP) a causa della quale non sono in grado di creare o gestire gruppi di gestione dei clienti all'interno della directory dei clienti.  
Questa limitazione è attualmente in fase di analisi e verrà risolta prima dell'annuncio della "Disponibilità generale" dei gruppi di gestione.

## <a name="root-management-group-for-each-directory"></a>Gruppo di gestione radice per ogni directory

A ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione "radice". Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente l'applicazione di criteri globali e assegnazioni di Controllo degli accessi in base al ruolo a livello di directory. Inizialmente l'[Amministratore directory deve elevare se stesso](../role-based-access-control/elevate-access-global-admin.md) al ruolo di proprietario del gruppo radice. Una diventato proprietario del gruppo, l'amministratore può assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo ad altri utenti della directory o gruppi per gestire la gerarchia.  

### <a name="important-facts-about-the-root-management-group"></a>Informazioni importanti sul gruppo di gestione radice

- Il nome e l'ID del gruppo di gestione radice sono assegnati per impostazione predefinita. Il nome visualizzato può essere aggiornato in qualsiasi momento in modo che appaia in modo diverso all'interno del portale di Azure.
  - Il nome sarà "Gruppo radice tenant".
  - L'ID sarà l'ID di Azure Active Directory.
- A differenza degli altri gruppi di gestione, il gruppo di gestione radice non può essere spostato o eliminato.  
- Tutte le sottoscrizioni e i gruppi di gestione sono ricondotti all'unico gruppo di gestione radice all'interno della directory.
  - Tutte le risorse nella directory sono ricondotte al gruppo di gestione radice ai fini della gestione globale.
  - Le nuove sottoscrizioni vengono inserite automaticamente nel gruppo di gestione radice al momento della creazione.
- Tutti i clienti di Azure possono vedere il gruppo di gestione radice, ma non tutti i clienti dispongono dell'accesso per gestire il gruppo di gestione radice.
  - Chiunque abbia accesso a una sottoscrizione può vedere il contesto in cui tale sottoscrizione si trova nella gerarchia.  
  - A nessun utente viene assegnato l'accesso predefinito al gruppo di gestione radice. Gli amministratori globali di directory sono gli unici utenti che possono elevare i propri privilegi per ottenere l'accesso.  Dopo avere ottenuto l'accesso, gli amministratori di directory possono assegnare qualsiasi ruolo Controllo degli accessi in base al ruolo agli altri utenti per la gestione.  

>[!NOTE]
>Se la directory in uso ha iniziato a usare il servizio dei gruppi di gestione prima del 25/6/2018, la directory potrebbe non essere configurata con tutte le sottoscrizioni nella gerarchia. Il team del gruppo di gestione sta aggiornando retroattivamente ogni directory che ha iniziato a usare i gruppi di gestione nell'anteprima pubblica prima di tale data entro luglio 2018. Tutte le sottoscrizioni nelle directory saranno elementi figlio sotto il gruppo di gestione radice precedente.  
>
>Per domande su questo processo retroattivo, contattare: managementgroups@microsoft.com  
  
## <a name="initial-setup-of-management-groups"></a>Configurazione iniziale dei gruppi di gestione

Quando un utente inizia a usare i gruppi di gestione, si verifica un processo di configurazione iniziale. Il primo passaggio è la creazione del gruppo di gestione radice nella directory. Dopo avere creato questo gruppo, tutte le sottoscrizioni esistenti nella directory diventano elementi figlio del gruppo di gestione radice.  Lo scopo di questo processo è assicurarsi che esista un'unica gerarchia di gruppi di gestione all'interno di una directory.  Un'unica gerarchia all'interno della directory consente ai clienti amministrativi di applicare i criteri e l'accesso globale per cui gli altri clienti all'interno della directory non possono eseguire il bypass. Un valore assegnato alla radice verrà applicato a tutti i gruppi di gestione, le sottoscrizioni, i gruppi di risorse e le risorse all'interno della directory avendo una gerarchia all'interno della directory.  

> [!IMPORTANT]
> Qualsiasi assegnazione di accesso utente o di criteri nel gruppo di gestione radice **viene applicata a tutte le risorse all'interno della directory**. Per questo motivo, tutti i clienti devono valutare la necessità di disporre di elementi definiti in questo ambito.  Le assegnazioni di accesso utente e di criteri devono essere "obbligatori" solo in questo ambito.  
  
## <a name="management-group-access"></a>Accesso ai gruppi di gestione

I gruppi di gestione di Azure supportano il [Controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md) per tutte le definizioni di ruoli e gli accessi alle risorse. Queste autorizzazioni vengono ereditate dalle risorse figlio presenti nella gerarchia. È possibile assegnare qualsiasi ruolo predefinito Controllo degli accessi in base al ruolo a un gruppo di gestione che verrà ereditato fino alle risorse.  Ad esempio, il ruolo Controllo degli accessi in base al ruolo Collaboratore Macchina virtuale può essere assegnato a un gruppo di gestione. Questo ruolo non dispone di alcuna azione sul gruppo di gestione, ma verrà ereditato da tutte le macchine virtuali in tale gruppo.  

Il grafico seguente mostra l'elenco dei ruoli e delle azioni supportate per i gruppi di gestione.

| Nome del ruolo Controllo degli accessi in base al ruolo             | Create | Rinominare | Spostamento | Delete | Assegnare l'accesso | Assegnare un criterio | Lettura  |
|:-------------------------- |:------:|:------:|:----:|:------:|:-------------:| :------------:|:-----:|
|Proprietario                       | X      | X      | X    | X      | X             |               | X     |
|Collaboratore                 | X      | X      | X    | X      |               |               | X     |
|Reader                      |        |        |      |        |               |               | X     |
|Collaboratore per i criteri delle risorse |        |        |      |        |               | X             |       |
|Amministratore accessi utente   |        |        |      |        | X             |               |       |

### <a name="custom-rbac-role-definition-and-assignment"></a>Definizione e assegnazione di un ruolo personalizzato Controllo degli accessi in base al ruolo

Attualmente i ruoli personalizzati Controllo degli accessi in base al ruolo non sono supportati per i gruppi di gestione.  Per visualizzare lo stato di questo elemento, vedere il [forum dei commenti sui gruppi di gestione](https://aka.ms/mgfeedback).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui gruppi di gestione, vedere:

- [Creare gruppi di gestione per organizzare le risorse di Azure](management-groups-create.md)
- [Come modificare, eliminare o gestire i gruppi di gestione](management-groups-manage.md)
- [Installare il modulo Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Esaminare la specifica di dell'API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Installare l'estensione dell'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

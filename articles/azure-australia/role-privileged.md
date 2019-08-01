---
title: Controllo degli accessi in base al ruolo e Privileged Identity Management in Azure Australia
description: Linee guida per l'implementazione del controllo degli accessi in base al ruolo e Privileged Identity Management all'interno delle aree australiane per soddisfare i requisiti specifici di criteri, normative e normative per il governo australiano.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a9451b5dcd572eee27809cf66df889f06da960ed
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571497"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Controllo degli accessi in base al ruolo (RBAC) e Privileged Identity Management (PIM)

La gestione dei privilegi amministrativi è un passaggio fondamentale per garantire la protezione in qualsiasi ambiente IT. La limitazione dei privilegi amministrativi tramite l'utilizzo della sicurezza con privilegi minimi è un requisito del [ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm) e fa parte dell'elenco [ACSC Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) di consigli sulla sicurezza.

Microsoft fornisce un gruppo di controlli per implementare l'accesso just-in-time e just-enough all'interno Microsoft Azure. La comprensione di questi controlli è essenziale per un comportamento di sicurezza efficace nel cloud. In questa guida viene fornita una panoramica dei controlli stessi e delle principali considerazioni di progettazione per l'implementazione.

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo è fondamentale per la gestione dell'accesso a tutte le risorse all'interno Microsoft Azure e la gestione di Azure Active Directory (Azure AD). Il controllo degli accessi in base al ruolo può essere implementato insieme a numerose funzionalità complementari disponibili in Azure. Questo articolo è incentrato sull'implementazione di un controllo degli accessi in base al ruolo con Azure Gruppi di gestione, gruppi di Azure Active Directory e Azure Privileged Identity Management (PIM).

A un livello elevato, l'implementazione di RBAC richiede tre componenti:

![RBAC-Panoramica](media/rbac-overview.png)

* **Entità di sicurezza**: Un'entità di sicurezza può essere una delle seguenti: un utente, un gruppo, [entità servizio o un' [identità gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Alle entità di sicurezza devono essere assegnati privilegi utilizzando gruppi Azure Active Directory.

* **Definizioni di ruolo**: Una definizione di ruolo, detta anche ruolo, è una raccolta di autorizzazioni. Queste autorizzazioni definiscono le operazioni che possono essere eseguite dalle entità di sicurezza assegnate alla definizione di ruolo. Questa funzionalità viene fornita dai ruoli delle risorse di Azure e dai ruoli di amministratore Azure Active Directory. Azure viene fornita con un set di ruoli predefiniti (collegamento) che possono essere ampliati con ruoli personalizzati.

* **Ambito**: L'ambito è il set di risorse di Azure a cui viene applicata una definizione di ruolo. I ruoli di Azure possono essere assegnati alle risorse di Azure tramite Gruppi di gestione di Azure.

Questi tre componenti si combinano per concedere alle entità di sicurezza l'accesso definito nelle definizioni di ruolo a tutte le risorse che rientrano nell'ambito del Gruppi di gestione di Azure. questa operazione viene definita assegnazione di ruolo. È possibile assegnare più definizioni di ruolo a un'entità di sicurezza e più entità di sicurezza possono essere assegnate a un singolo ambito.

### <a name="azure-active-directory-groups"></a>Gruppi di Azure Active Directory

Quando si assegnano privilegi a singoli utenti o team, quando possibile l'assegnazione deve essere collegata a un gruppo di Azure Active Directory e non assegnata direttamente all'utente in questione. Si tratta della stessa procedura consigliata ereditata dalle implementazioni Active Directory locali. Laddove possibile, è necessario creare gruppi di Azure Active Directory per ogni team, complementari alla struttura logica del Gruppi di gestione di Azure creato.

In uno scenario di cloud ibrido, i gruppi di sicurezza di Windows Server Active Directory locali possono essere sincronizzati con l'istanza di Azure Active Directory. Se è già stato implementato il controllo degli accessi in base al ruolo in locale usando questi gruppi di sicurezza di Windows Server Active Directory, questi gruppi, una volta sincronizzati, potranno quindi essere usati per implementare RBAC per le risorse di Azure. In caso contrario, l'ambiente cloud può essere considerato uno Slate pulito per progettare e implementare un piano di gestione dei privilegi affidabile basato sull'implementazione di Azure Active Directory.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Ruoli delle risorse di Azure rispetto ai ruoli di amministratore Azure Active Directory

Microsoft Azure offre un'ampia gamma di ruoli predefiniti per [le risorse di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) e l' [amministrazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Entrambi i tipi di ruolo forniscono un accesso granulare specifico alle risorse di Azure o agli amministratori Azure AD. È importante notare che i ruoli delle risorse di Azure non possono essere usati per fornire l'accesso amministrativo a Azure AD e i ruoli di Azure AD non forniscono accesso specifico alle risorse di Azure.

Di seguito sono riportati alcuni esempi dei tipi di accesso che possono essere assegnati a una risorsa di Azure usando un ruolo predefinito:

* Consentire a un utente di gestire le macchine virtuali in una sottoscrizione e un altro utente a gestire le reti virtuali
* Consentire a un gruppo di amministratori di database di gestire database SQL all'interno di una sottoscrizione
* Consentire a un utente di gestire tutte le risorse in un gruppo di risorse, ad esempio le macchine virtuali, i siti Web e le subnet
* Consentire a un'applicazione di accedere a tutte le risorse in un gruppo di risorse

Di seguito sono riportati alcuni esempi dei tipi di accesso che è possibile assegnare per Azure AD amministrazione:

* Consenti al personale del supporto tecnico di reimpostare le password utente
* Consentire al personale di invitare gli utenti esterni a un'istanza di Azure AD per la collaborazione B2B
* Consenti l'accesso in lettura al personale amministrativo per l'accesso e i report di controllo
* Consentire al personale di gestire tutti gli utenti e i gruppi, inclusa la reimpostazione delle password.

È importante comprendere l'elenco completo delle azioni consentite fornite da un ruolo predefinito per garantire che l'accesso non autorizzato a non venga concesso. L'elenco dei ruoli predefiniti e l'accesso fornito sono in continua evoluzione, l'elenco completo dei ruoli e le relative definizioni possono essere visualizzati esaminando la documentazione collegata sopra o usando il cmdlet Azure PowerShell:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

oppure il comando dell'interfaccia della riga di comando di Azure:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

È anche possibile creare ruoli di risorse di Azure personalizzati come richiesto. Questi ruoli personalizzati possono essere creati nella portale di Azure, tramite PowerShell o l'interfaccia della riga di comando di Azure. Quando si creano ruoli personalizzati, è fondamentale assicurarsi che lo scopo del ruolo sia univoco e che la funzione non sia già fornita da un ruolo di risorsa di Azure esistente. In questo modo si riduce la complessità della gestione in corso e si riduce il rischio che le entità di sicurezza ricevano privilegi non necessari. Un esempio potrebbe essere la creazione di un ruolo di risorsa di Azure personalizzato che risiede tra i ruoli predefiniti delle risorse di Azure, "collaboratore macchina virtuale" e "accesso amministratore macchina virtuale".

Il ruolo personalizzato può essere basato sul ruolo Collaboratore esistente, che concede l'accesso seguente:

| Risorsa di Azure | Livello di accesso |
| --- | --- |
| Macchine virtuali | Può gestire ma non può accedere a |
| Rete virtuale collegata alla macchina virtuale | Impossibile accedere |
| Archiviazione collegata alla macchina virtuale | Impossibile accedere |
|

Il ruolo personalizzato potrebbe mantenere questo accesso di base, ma consentire agli utenti designati alcuni privilegi aggiuntivi di base per modificare la configurazione di rete delle macchine virtuali.

I ruoli delle risorse di Azure hanno anche il vantaggio di poter essere assegnati alle risorse tramite Azure Gruppi di gestione.

### <a name="azure-management-groups"></a>Gruppi di gestione di Azure

Azure Gruppi di gestione può essere usato da un'organizzazione per gestire l'assegnazione dei ruoli a tutte le sottoscrizioni e alle relative risorse in un tenant di Azure. Azure Gruppi di gestione è progettato per consentire la creazione di gerarchie di gestione, inclusa la possibilità di eseguire il mapping gerarchico della struttura organizzativa in Azure. La creazione di unità organizzative aziendali come entità logiche separate consente di applicare le autorizzazioni all'interno di un'organizzazione in base ai requisiti specifici di ogni team. Azure Gruppi di gestione può essere usato per definire una gerarchia di gestione fino a sei livelli di profondità.

![Gruppi di gestione](media/management-groups.png)

Azure Gruppi di gestione viene mappato alle sottoscrizioni di Azure in un tenant di Azure. Questo consente a un'organizzazione di separare le risorse di Azure appartenenti a specifiche unità aziendali e di fornire un livello di controllo granulare sia per la gestione dei costi che per l'assegnazione dei privilegi.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft ha implementato JIT (just-in-Time) e accesso just-enough (JEA) tramite Azure Privileged Identity Management. Questo servizio permette al personale amministrativo di controllare, gestire e monitorare l'accesso con privilegi alle risorse di Azure. PIM consente alle entità di sicurezza di essere rese "idonee" per un ruolo da parte del personale amministrativo, consentendo agli utenti di richiedere l'attivazione del ruolo tramite il portale di Azure o tramite i cmdlet di PowerShell. Per impostazione predefinita, l'assegnazione di ruolo può essere attivata per un periodo compreso tra 1 e 72 ore. Se necessario, l'utente può richiedere un'estensione per l'assegnazione di ruolo e l'opzione per rendere permanente l'assegnazione di ruolo esiste. Facoltativamente, è possibile applicare il requisito per l'autenticazione a più fattori quando gli utenti richiedono l'attivazione dei propri ruoli idonei. Una volta che il periodo allocato dell'attivazione del ruolo scade, l'entità di sicurezza non dispone più dell'accesso con privilegi concesso dal ruolo.

L'uso di PIM impedisce i problemi comuni di assegnazione dei privilegi che possono verificarsi in ambienti che non utilizzano l'accesso JIT o non eseguono controlli di routine dell'assegnazione dei privilegi. Un problema comune è l'assegnazione di privilegi elevati dimenticati e rimanenti sul posto a lungo dopo che l'attività che richiede privilegi elevati è stata completata. Un altro problema è la proliferazione di privilegi elevati all'interno di un ambiente attraverso la clonazione dell'accesso assegnato a un'entità di sicurezza durante la configurazione di altre entità di sicurezza simili.

## <a name="key-design-considerations"></a>Considerazioni di progettazione principali

Quando si progetta una strategia di controllo degli accessi in base al ruolo con l'intenzione di applicare la sicurezza con privilegi minimi, è necessario considerare i requisiti di sicurezza seguenti:

* Richieste di accesso con privilegi convalidate
* I privilegi amministrativi sono limitati all'accesso minimo necessario per l'esecuzione di mansioni specifiche
* I privilegi amministrativi sono limitati al periodo di tempo minimo necessario per l'esecuzione di mansioni specifiche
* Revisioni regolari dei privilegi amministrativi concessi

Il processo di progettazione di una strategia di controllo degli accessi in base al ruolo richiederà una revisione dettagliata delle funzioni di business per comprendere la differenza tra i diversi ruoli aziendali e il tipo e la frequenza di lavoro che richiedono privilegi elevati. La differenza nella funzione tra un operatore di backup, un amministratore della sicurezza e un revisore richiede diversi livelli di accesso in momenti diversi con diversi livelli di revisione continua.

## <a name="validate-requests-for-access"></a>Convalidare le richieste di accesso

I privilegi elevati devono essere approvati in modo esplicito. Per supportare questa operazione, è necessario sviluppare un processo di approvazione e il personale appropriato responsabile della convalida che tutte le richieste di privilegi aggiuntivi siano legittime. Privileged Identity Management offre più opzioni per l'approvazione dell'assegnazione di ruolo. Una richiesta di attivazione del ruolo può essere configurata in modo da consentire l'approvazione automatica o essere gestita e richiedere agli approvatori nominati di rivedere e approvare manualmente tutte le richieste di attivazione dei ruoli. Le richieste di attivazione possono anche essere configurate in modo da richiedere ulteriori informazioni di supporto sono incluse nella richiesta di attivazione, ad esempio i numeri di ticket.

### <a name="restrict-privilege-based-on-duties"></a>Limitazione dei privilegi in base ai compiti

La limitazione del livello di privilegio concesso alle entità di sicurezza è fondamentale, poiché l'assegnazione dei privilegi è un vettore di attacco di sicurezza IT comune. È necessario valutare i tipi di risorse gestiti e i team responsabili, in modo che sia possibile assegnare il livello minimo di privilegi necessari per i compiti giornalieri. I privilegi aggiuntivi che vanno oltre quelli necessari per i compiti giornalieri devono essere concessi solo per il periodo di tempo necessario per eseguire un'attività specifica. Un esempio di questo argomento è fornire l'accesso "collaboratore" all'amministratore di un cliente, ma consentire loro di richiedere autorizzazioni "proprietario" per una risorsa di Azure per un'attività specifica che richiede l'accesso temporaneo di alto livello.

In questo modo, ogni singolo amministratore dispone di accesso con privilegi elevati per il periodo di tempo più breve. L'adesione a queste procedure riduce la superficie di attacco complessiva per tutte le organizzazioni IT Infrastructure.

### <a name="regular-evaluation-of-administrative-privilege"></a>Valutazione regolare dei privilegi amministrativi

È fondamentale che le entità di sicurezza all'interno di un ambiente siano periodicamente controllate per garantire che sia attualmente assegnato il livello di privilegio corretto. Microsoft Azure offre un numero di mezzi per controllare e valutare i privilegi assegnati alle entità di sicurezza di Azure. Privileged Identity Management consente al personale amministrativo di eseguire periodicamente "verifiche di accesso" dei ruoli concessi alle entità di sicurezza. È possibile eseguire una verifica di accesso per controllare l'assegnazione di ruolo delle risorse di Azure e Azure Active Directory assegnazione di ruolo amministrativo. Una verifica di accesso può essere configurata con le proprietà seguenti:

* **Verificare il nome e le date di inizio e di fine**della verifica: Le verifiche devono essere configurate in modo da essere sufficientemente lunghe da consentire agli utenti designati di completarle.

* **Ruolo da rivedere**: Ogni verifica di accesso è incentrata su un singolo ruolo di Azure.

* Revisori nominati: Sono disponibili tre opzioni per l'esecuzione di una verifica. La revisione può essere assegnata a un altro utente, essere eseguita personalmente oppure ogni utente può verificare il proprio accesso.

* **Richiedi agli utenti di fornire un motivo per l'accesso**: È possibile richiedere agli utenti di immettere un motivo per mantenere il livello di privilegio durante il completamento della verifica di accesso.

Lo stato di avanzamento delle verifiche di accesso in sospeso può essere monitorato in qualsiasi momento tramite un dashboard nella portale di Azure. L'accesso al ruolo da rivedere rimarrà invariato fino al completamento della verifica di accesso. È anche possibile [controllare](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) tutte le assegnazioni utente e le attivazioni PIM entro un periodo di tempo specificato.

## <a name="next-steps"></a>Passaggi successivi

Vedere l'articolo sul [monitoraggio del sistema in Azure in Australia](system-monitor.md).

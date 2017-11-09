---
title: "Proteggere i dati personali usando l'identità e i controlli di accesso di Azure | Microsoft Docs"
description: "Uso dell'identità e dei controlli di accesso di Azure per proteggere i dati personali"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 7c66a95d5a056f59e0f28dba4e0880e72e74dc3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory e Multi-Factor Authentication: proteggere i dati personali usando l'identità e i controlli di accesso

Questo articolo fornisce informazioni e procedure utili per proteggere i dati personali usando i servizi e le funzionalità di sicurezza di Azure Active Directory e Multi-Factor Authentication.

## <a name="scenario"></a>Scenario

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito. 

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud. Questi includono informazioni personali come nomi, indirizzi, numeri di telefono e dati delle carte di credito della base clienti globale, nonché informazioni tradizionali del reparto risorse umane come indirizzi, numeri di telefono, codici fiscali e altri dati dei dipendenti aziendali in tutte le sedi. La linea di crociere gestisce anche un database di grandi dimensioni dei membri dei programmi fedeltà e premi, che include informazioni personali per tenere traccia delle relazioni con i clienti attuali e del passato.

I dipendenti aziendali accedono alla rete dagli uffici remoti della società, mentre agenti di viaggio in tutto il mondo hanno accesso ad alcune risorse aziendali.

## <a name="problem-statement"></a>Presentazione del problema

L'azienda deve proteggere la privacy dei dati personali dei clienti e dei dipendenti da utenti malintenzionati che cercano di usare le identità compromesse per ottenere l'accesso. Deve anche assicurare che l'accesso ai dati personali da parte di utenti legittimi sia limitato solo a coloro che necessitano di tale accesso per svolgere le proprie mansioni.

## <a name="company-goal"></a>Obiettivo dell'azienda

L'obiettivo dell'azienda è garantire che l'accesso ai dati personali sia strettamente controllato. È essenziale che le identità degli utenti con accesso ai dati personali siano protette da autenticazione avanzata. È necessario applicare un criterio di [privilegi minimi] (https://it.wikipedia.org/wiki/Principio_del_privilegio_minimo) in modo che gli utenti legittimi abbiano esclusivamente il livello di accesso di cui necessitano e non oltre.

## <a name="solutions"></a>Soluzioni

Microsoft Azure offre gli strumenti di gestione di identità e accessi per consentire alle aziende di determinare chi può accedere a risorse che contengono dati personali.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) gestisce le identità e controlla l'accesso ad Azure e ad altre risorse, applicazioni e dati locali o cloud. [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) consente agli amministratori di Azure di ridurre al minimo il numero di utenti che hanno accesso a determinate informazioni, ad esempio i dati personali. Consente di individuare, limitare e monitorare le identità con privilegi e il loro accesso alle risorse e di assegnare diritti amministrativi temporanei Just-In-Time (JIT) agli utenti idonei. Consente anche di ottenere informazioni su coloro che hanno privilegi amministrativi di AAD.

Le attività correlate all'uso di Azure Active Directory Privileged Identity Management includono:

- Abilitazione di Privileged Identity Management per la directory

- Uso del dashboard di amministrazione di Privileged Identity Management per visualizzare rapidamente informazioni importanti

- Gestione delle identità con privilegi (amministratori) tramite l'aggiunta o la rimozione di amministratori permanenti o idonei a ogni ruolo

- Configurazione delle impostazioni di attivazione del ruolo

- Attivazione dei ruoli

- Verifica dell'attività dei ruoli

#### <a name="how-do-i-enable-aad-pim"></a>Come abilitare Azure Active Directory Privileged Identity Management

Per iniziare a usare Privileged Identity Management per la directory, seguire questa procedura:

1. Accedere al portale di Azure come amministratore globale della directory.

2. Se l'organizzazione ha più directory, selezionare il proprio nome utente nell'angolo superiore destro del portale di Azure. Selezionare la directory in cui si userà Azure AD Privileged Identity Management.

3. Selezionare **Altri servizi** e usare la casella di testo **Filtro** per cercare Azure AD Privileged Identity Management.

4. Selezionare **Aggiungi al dashboard** e quindi fare clic su **Crea**. Verrà aperta l'applicazione Privileged Identity Management.

Dopo la configurazione di Azure AD Privileged Identity Management, a ogni apertura dell'applicazione viene visualizzato il pannello di spostamento.

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

Per altre informazioni e istruzioni su come iniziare a usare Azure Active Directory Privileged Identity Management, vedere [Iniziare ad usare Azure AD Privileged Identity Management](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started).

### <a name="azure-role-based-access-control"></a>Controllo degli accessi in base al ruolo di Azure

Il [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) consente agli amministratori di Azure di gestire l'accesso alle risorse di Azure, concedendo l'accesso in base al ruolo assegnato all'utente. È possibile separare i compiti all'interno di un team e concedere a utenti, gruppi e applicazioni solo il livello di accesso necessario per svolgere le proprie attività.

L'accesso in base al ruolo può essere concesso agli utenti tramite il portale di Azure, gli strumenti da riga di comando di Azure o le API di gestione di Azure.

Per altre informazioni sul controllo degli accessi in base al ruolo di Azure, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](https://docs.microsoft.com/active-directory/role-based-access-control-what-is).

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>Come gestire il controllo degli accessi in base al ruolo con PowerShell

È possibile usare i cmdlet di PowerShell per gestire il controllo degli accessi in base al ruolo di Azure, incluse le attività di gestione seguenti:

- Elenco dei ruoli

- Assegnazioni di accesso

- Concedere l'accesso

- Rimuovere un accesso

- Creare un ruolo personalizzato

- Ottenere le azioni per un provider di risorse

- Modificare un ruolo personalizzato

- Eliminare un ruolo personalizzato

- Elencare ruoli personalizzati

Per istruzioni su come gestire il controllo degli accessi in base al ruolo di Azure con PowerShell, vedere [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/), o MFA, è una soluzione per la verifica in due passaggi che consente di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre autenticazione avanzata tramite una gamma di metodi di verifica, fra cui una telefonata, un SMS o una verifica dell'app per dispositivi mobili.

Per distribuire Multi-Factor Authentication nel cloud di Azure, è prima necessario abilitare il servizio e quindi attivare la verifica in due passaggi per gli utenti.

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>Come abilitare Azure per l'uso di Multi-Factor Authentication

Se gli utenti hanno licenze che comprendono Azure Multi-Factor Authentication, non è necessario eseguire operazioni per attivare Azure MFA. In caso contrario sarà necessario creare un provider di Multi-Factor Authentication nella directory. A questo scopo, seguire questa procedura:

1. Accedere al portale di Azure classico come amministratore e selezionare **Active Directory**.

2. Selezionare **Provider di Multi-Factor Authentication**.

3. Selezionare **Nuovo** e quindi **Provider di Multi-Factor Authentication** in **Servizi app**.

4. Selezionare **Creazione rapida**.

5. Compilare il campo del nome e selezionare un modello di utilizzo, ovvero per autenticazione o per utente abilitato.

6. Designare una directory a cui associare il provider di Multi-Factor Authentication.

7. Selezionare il pulsante **Create** .

![](media/protect-personal-data-identity-access-controls/quick-create.png)

Per altre istruzioni su come gestire i provider di Multi-Factor Authentication, vedere [Introduzione all'uso di un provider di Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider).

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>Come attivare la verifica in due passaggi per gli utenti

È possibile applicare la verifica in due passaggi per tutti gli accessi o creare criteri di accesso condizionale per richiedere la verifica in due passaggi solo in presenza di condizioni specifiche.

L'abilitazione di Azure MFA modificando gli stati utente è l'approccio tradizionalmente usato per richiedere la verifica in due passaggi. Tutti gli utenti abilitati devono effettuare la verifica in due passaggi ogni volta che accedono. L'abilitazione di un utente sostituisce eventuali criteri di accesso condizionale in vigore per l'utente.

L'abilitazione di Azure MFA con criteri di accesso condizionale è un approccio più flessibile per richiedere la verifica in due passaggi. È possibile creare criteri di accesso condizionale da applicare sia a gruppi che a singoli utenti. È possibile, ad esempio, assegnare ai gruppi ad alto rischio più restrizioni rispetto ai gruppi a basso rischio oppure richiedere la verifica in due passaggi solo per le app cloud ad alto rischio e non per quelle a basso rischio. L'accesso condizionale è tuttavia una funzionalità a pagamento di Azure Active Directory.

Per abilitare MFA modificando lo stato utente, seguire questa procedura:

1. Accedre al portale di Azure come amministratore.
2. Passare ad **Azure Active Directory \> Utenti e gruppi \> Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
4. Trovare l'utente che si vuole abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
5. Selezionare la casella accanto al nome dell'utente.
6. A destra scegliere **Abilita** sotto Azioni rapide.

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. Confermare la selezione nella finestra popup che viene visualizzata.  Gli utenti per i quali è stata abilitata la funzionalità Multi-Factor Authentication devono eseguire la registrazione all'accesso successivo.

Per abilitare Azure MFA con criteri di accesso condizionale, seguire questa procedura:

1. Accedre al portale di Azure come amministratore.

2. Passare ad **Azure Active Directory \> Accesso condizionale**.

3. Selezionare **Nuovi criteri**.

4. In **Assegnazioni** selezionare **Utenti e gruppi**. Usare le schede **Includi** ed **Escludi** per specificare quali utenti e i gruppi dovranno essere gestiti dai criteri.

5. In **Assegnazioni** selezionare **App cloud**. Scegliere di includere **Tutte le app cloud**.
6.  In **Controlli di accesso** selezionare **Concedi**. Selezionare **Richiedi autenticazione a più fattori**.
7.  Impostare **Abilita criterio** su **On** e quindi selezionare **Salva**.

Per informazioni su come configurare le impostazioni di Azure MFA per definire gli avvisi di illecito, creare un bypass monouso, usare messaggi vocali personalizzati, configurare il caching, specificare indirizzi IP attendibili, creare password per le app, abilitare la memorizzazione dell'autenticazione per i dispositivi attendibili degli utenti e selezionare metodi di verifica, vedere [Configurare le impostazioni di Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next).

## <a name="next-steps"></a>Passaggi successivi

- [Protezione dell'accesso con privilegi in Azure AD](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [Domande frequenti su Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [Risoluzione dei problemi del controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

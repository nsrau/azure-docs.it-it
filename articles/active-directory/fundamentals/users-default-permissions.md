---
title: Autorizzazioni utente predefinite - Azure Active Directory | Microsoft Docs
description: Informazioni sulle diverse autorizzazioni utente disponibili in Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18, contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d585ac57a369c994df9871bf731157de7b23212
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707629"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Autorizzazioni utente predefinite in Azure Active Directory
In Azure Active Directory (Azure AD) a tutti gli utenti viene concesso un set di autorizzazioni predefinite. L'accesso di un utente è costituito dal tipo di utente, dalle [assegnazioni di ruolo](active-directory-users-assign-role-azure-portal.md) e dalla proprietà di singoli oggetti. Questo articolo descrive tali autorizzazioni predefinite e contiene un confronto delle impostazioni predefinite degli utenti membro e guest. Le autorizzazioni utente predefinite possono essere modificate solo nelle impostazioni utente in Azure AD.

## <a name="member-and-guest-users"></a>Utenti guest e membro
Il set di autorizzazioni predefinite ricevuto varia a seconda che l'utente sia un membro nativo del tenant (utente membro) o provenga da un'altra directory come guest di Collaborazione B2B (utente guest). Per altre informazioni sull'aggiunta di utenti guest, vedere [Che cos'è Collaborazione B2B in Azure Active Directory?](../external-identities/what-is-b2b.md)
* Gli utenti membro possono registrare le applicazioni, gestire il proprio numero di cellulare e la propria foto profilo, modificare la propria password e invitare guest B2B. Gli utenti possono anche leggere tutte le informazioni della directory (con alcune eccezioni). 
* Gli utenti guest hanno autorizzazioni di directory limitate. Possono gestire il proprio profilo, modificare la propria password e recuperare alcune informazioni su altri utenti, gruppi e app, ma non sono in grado di leggere tutte le informazioni sulla directory. Ad esempio, gli utenti guest non possono enumerare utenti, gruppi e altri oggetti directory. Gli utenti guest possono essere aggiunti ai ruoli di amministratore, che concedono le autorizzazioni complete di lettura e scrittura contenute nel ruolo. I guest possono invitare anche altri guest.

## <a name="compare-member-and-guest-default-permissions"></a>Confrontare le autorizzazioni predefinite di membri e guest

**Area** | **Autorizzazioni utente membro** | **Autorizzazioni utente Guest predefinite** | **Autorizzazioni utente Guest limitate (anteprima)**
------------ | --------- | ---------- | ----------
Utenti e contatti | <ul><li>Lettura di tutte le proprietà pubbliche di utenti e contatti</li><li>Invito di utenti guest<li>Modifica della propria password<li>Gestione del proprio numero di cellulare<li>Gestione della propria foto<li>Invalidare i propri token di aggiornamento</li></ul> | <ul><li>Lettura delle proprietà personali<li>Leggi nome visualizzato, indirizzo di posta elettronica, nome di accesso, foto, nome dell'entità utente e proprietà dei tipi di utente di altri utenti e contatti<li>Modifica della propria password<li>Cerca un altro utente in base al nome visualizzato, al nome dell'entità utente o al valore ObjectId (se consentito)<li>Leggere le informazioni di gestione e di report dirette di altri utenti</li></ul> | <ul><li>Lettura delle proprietà personali<li>Modifica della propria password</li></ul>
Gruppi | <ul><li>Creazione di gruppi di sicurezza<li>Creare gruppi di Microsoft 365<li>Lettura di tutte le proprietà dei gruppi<li>Lettura delle appartenenze a gruppi non nascoste<li>Lettura delle appartenenze a gruppi Microsoft 365 nascoste per il gruppo Unito<li>Gestione delle proprietà, della titolarità e dell'appartenenza a gruppi dell'utente<li>Aggiunta di utenti guest ai gruppi con proprietario<li>Gestire delle impostazioni di appartenenza dinamica<li>Eliminazione dei gruppi con proprietario<li>Ripristinare i gruppi di Microsoft 365 di proprietà</li></ul> | <ul><li>Leggere le proprietà di tutti i gruppi non nascosti, incluse l'appartenenza e la proprietà (anche gruppi non aggiunti)<li>Lettura delle appartenenze ai gruppi Microsoft 365 nascoste per i gruppi aggiunti<li>Cerca i gruppi in base al nome visualizzato o ObjectId (se consentito)</li></ul> | Nessuna autorizzazione
Applicazioni | <ul><li>Registrazione (creazione) di una nuova applicazione<li>Lettura delle proprietà delle applicazioni aziendali e registrate<li>Gestione delle proprietà, delle assegnazioni e delle credenziali per le applicazioni con proprietario<li>Creazione o eliminazione della password applicazione per un utente<li>Eliminazione delle applicazioni di proprietà<li>Ripristino delle applicazioni di proprietà</li></ul> | <ul><li>Lettura delle proprietà delle applicazioni aziendali e registrate</li></ul> | <ul><li>Lettura delle proprietà delle applicazioni aziendali e registrate
Dispositivi</li></ul> | <ul><li>Lettura di tutte le proprietà dei dispositivi<li>Gestione di tutte le proprietà dei dispositivi con proprietario</li></ul> | Nessuna autorizzazione | Nessuna autorizzazione
Directory | <ul><li>Lettura di tutte le informazioni aziendali<li>Lettura di tutti i domini<li>Lettura di tutti i contratti dei partner</li></ul> | <ul><li>Leggi nome visualizzato dell'azienda<li>Lettura di tutti i domini</li></ul> | <ul><li>Leggi nome visualizzato dell'azienda<li>Lettura di tutti i domini</li></ul>
Ruoli e ambiti | <ul><li>Lettura di tutti i ruoli amministrativi e delle appartenenze<li>Lettura di tutte le proprietà e dell'appartenenza delle unità amministrative</li></ul> | Nessuna autorizzazione | Nessuna autorizzazione
Sottoscrizioni | <ul><li>Lettura di tutte le sottoscrizioni<li>Abilitazione del membro del piano di servizio</li></ul> | Nessuna autorizzazione | Nessuna autorizzazione
Criteri | <ul><li>Lettura di tutte le proprietà dei criteri<li>Gestione di tutte le proprietà dei criteri con proprietario</li></ul> | Nessuna autorizzazione | Nessuna autorizzazione

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Per limitare le autorizzazioni predefinite per gli utenti membro

Le autorizzazioni predefinite per gli utenti membri possono essere limitate nei modi seguenti:

Autorizzazione | Spiegazione dell'impostazione
---------- | ------------
Gli utenti possono registrare l'applicazione | L'impostazione di questa opzione su No impedisce agli utenti di creare registrazioni di applicazioni. La possibilità può essere quindi concessa a utenti specifici aggiungendoli al ruolo di sviluppatore di applicazioni.
Consenti agli utenti di connettere un account aziendale o dell'Istituto di istruzione a LinkedIn | Se si imposta questa opzione su No, gli utenti non possono connettere il proprio account aziendale o dell'Istituto di istruzione con il proprio account LinkedIn. Per altre informazioni, vedere la pagina relativa al [consenso e alla condivisione di dati delle connessioni dell'account LinkedIn](../users-groups-roles/linkedin-user-consent.md).
Possibilità di creare i gruppi di sicurezza | Impostando questa opzione su No, gli utenti non possono creare gruppi di sicurezza. Gli amministratori globali e gli amministratori utenti possono comunque creare gruppi di sicurezza. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-cmdlets.md).
Possibilità di creare gruppi di Microsoft 365 | Se si imposta questa opzione su No, gli utenti non possono creare gruppi Microsoft 365. L'impostazione di questa opzione su alcune consente a un set di utenti selezionato di creare Microsoft 365 gruppi. Gli amministratori globali e gli amministratori utenti potranno comunque creare Microsoft 365 gruppi. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-cmdlets.md).
Limita l'accesso al portale di amministrazione di Azure AD | Impostando questa opzione su No, non è possibile usare il portale di amministrazione di Azure AD per leggere e gestire le risorse Azure AD. Sì, impedisce a tutti gli amministratori non di accedere ai dati Azure AD nel portale di amministrazione.<p>**Nota**: questa impostazione non limita l'accesso ai dati Azure ad tramite PowerShell o altri client, ad esempio Visual Studio. Se impostato su Sì, per concedere a un utente non amministratore specifico la possibilità di usare il portale di amministrazione di Azure ad assegnare qualsiasi ruolo amministrativo, ad esempio il ruolo dei lettori di directory.<p>Questo ruolo consente di leggere le informazioni sulla directory di base, quali utenti membri hanno per impostazione predefinita (Guest e entità servizio non).
Possibilità di leggere altri utenti | Questa impostazione è disponibile solo in PowerShell. Impostando questo flag su $false si impedisce a tutti gli amministratori non di leggere le informazioni utente dalla directory. Questo flag non impedisce la lettura delle informazioni utente in altri servizi Microsoft come Exchange Online. Questa impostazione è destinata a circostanze speciali e l'impostazione di questo flag su $false non è consigliata.


## <a name="to-restrict-the-default-permissions-for-guest-users"></a>Per limitare le autorizzazioni predefinite per gli utenti Guest

Le autorizzazioni predefinite per gli utenti guest possono essere limitate nei modi seguenti:

>[!NOTE] 
>L'impostazione limitazioni di accesso utente Guest sostituite per le **autorizzazioni utenti Guest è limitata** . Per istruzioni sull'uso di questa funzionalità, vedere [limitare le autorizzazioni di accesso Guest (anteprima) in Azure Active Directory](../users-groups-roles/users-restrict-guest-permissions.md).

Autorizzazione | Spiegazione dell'impostazione
---------- | ------------
Limitazioni di accesso utente Guest (anteprima) | Per impostazione predefinita, l'impostazione di questa opzione su utenti Guest ha lo stesso accesso ai membri che concede a tutti gli utenti Guest le autorizzazioni per l'utente Guest.<p>L'impostazione di questa opzione sull'accesso utente Guest è limitata alle proprietà e le appartenenze dei propri oggetti directory limitano l'accesso guest solo al proprio profilo utente per impostazione predefinita. L'accesso ad altri utenti non è più consentito anche durante la ricerca in base al nome dell'entità utente o a objectId. Non è più consentito accedere a gruppi che includono appartenenze a gruppi. Questa impostazione non impedisce l'accesso ai gruppi in altri servizi Microsoft come Microsoft teams. Per ulteriori informazioni, vedere [Microsoft teams Guest Access]() .<p>Gli utenti guest possono comunque essere aggiunti ai ruoli di amministratore indipendentemente dalle impostazioni di autorizzazione.
Gli utenti guest possono invitare | Se si imposta questa opzione su Sì, i Guest potranno invitare altri guest. Per altre informazioni, vedere [delegare gli inviti per la collaborazione B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
I membri possono invitare | I membri possono invitare l'impostazione di questa opzione su Sì consente ai membri non amministratori della directory di invitare i guest. Per altre informazioni, vedere [delegare gli inviti per la collaborazione B2B](../external-identities/delegate-invitations.md#configure-b2b-external-collaboration-settings) .
Amministratori e utenti nel ruolo mittente dell'invito guest possono invitare | Se si imposta questa opzione su Sì, gli amministratori e gli utenti del ruolo "invitato Guest" possono invitare i guest. Se l'opzione è impostata su Sì, gli utenti del ruolo di invito Guest saranno comunque in grado di invitare i guest, indipendentemente dal fatto che i membri possano invitare l'impostazione. Per altre informazioni, vedere [delegare gli inviti per la collaborazione B2B](../external-identities/delegate-invitations.md#assign-the-guest-inviter-role-to-a-user) .

## <a name="object-ownership"></a>Proprietà di un oggetto

### <a name="application-registration-owner-permissions"></a>Autorizzazioni di proprietario della registrazione dell'applicazione
Quando un utente registra un'applicazione, viene aggiunto automaticamente come proprietario dell'applicazione. In qualità di proprietario può gestire i metadati dell'applicazione, ad esempio il nome e le autorizzazioni richieste all'app. Può anche gestire la configurazione specifica del tenant dell'applicazione, ad esempio le assegnazioni utente e la configurazione SSO. Un proprietario può anche aggiungere o rimuovere altri proprietari. A differenza degli amministratori globali, i proprietari possono gestire solo le applicazioni di cui sono proprietari.

### <a name="enterprise-application-owner-permissions"></a>Autorizzazioni del proprietario dell'applicazione aziendale
Quando un utente aggiunge una nuova applicazione aziendale, viene aggiunto automaticamente come proprietario. I proprietari possono gestire la configurazione specifica del tenant dell'applicazione, ad esempio la configurazione SSO, il provisioning e le assegnazioni utente. Un proprietario può anche aggiungere o rimuovere altri proprietari. Diversamente dagli amministratori globali, i proprietari possono gestire solo le applicazioni di cui sono proprietari.

### <a name="group-owner-permissions"></a>Autorizzazioni di proprietario del gruppo
Quando un utente crea un gruppo, viene aggiunto automaticamente come proprietario di tale gruppo. In qualità di proprietario può gestire le proprietà del gruppo, ad esempio il nome, oltre a gestire l'appartenenza al gruppo. Un proprietario può anche aggiungere o rimuovere altri proprietari. A differenza degli amministratori globali e degli amministratori utenti, i proprietari possono gestire solo i gruppi di cui sono proprietari. Per assegnare un proprietario del gruppo, vedere [Gestione dei proprietari di un gruppo](active-directory-accessmanagement-managing-group-owners.md).

### <a name="ownership-permissions"></a>Autorizzazioni di proprietà
Le tabelle seguenti descrivono le autorizzazioni specifiche in Azure Active Directory utenti membro dispongono di oggetti di proprietà. L'utente dispone solo di queste autorizzazioni per gli oggetti di cui è proprietario.

#### <a name="owned-application-registrations"></a>Registrazioni di applicazioni di proprietà
Gli utenti possono eseguire le azioni seguenti per le registrazioni di applicazioni di proprietà.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| Microsoft. directory/applicazioni/ripristino | Ripristina le applicazioni in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Applicazioni aziendali di proprietà
Gli utenti possono eseguire le azioni seguenti nelle applicazioni aziendali di proprietà. Un'applicazione aziendale è costituita da un'entità servizio, uno o più criteri di applicazione e talvolta da un oggetto applicazione nello stesso tenant dell'entità servizio.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivi di proprietà
Gli utenti possono eseguire le azioni seguenti nei dispositivi di proprietà.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/disable | Disabilitare i dispositivi in Azure Active Directory. |

#### <a name="owned-groups"></a>Gruppi di proprietà
Gli utenti possono eseguire le azioni seguenti nei gruppi di proprietà.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| Microsoft. directory/groups/dynamicMembershipRule/Update | Aggiornare la proprietà groups.dynamicMembershipRule in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle restrizioni di accesso degli utenti guest, vedere [limitare le autorizzazioni di accesso Guest (anteprima) in Azure Active Directory](../users-groups-roles/users-restrict-guest-permissions.md).
* Per altre informazioni su come assegnare i ruoli di amministratore di Azure AD, vedere [Assegnare un utente ai ruoli di amministratore in Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestire gli utenti](add-users-azure-active-directory.md)
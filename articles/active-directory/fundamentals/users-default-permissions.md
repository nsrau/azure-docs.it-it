---
title: Autorizzazioni utente predefinite - Azure Active Directory | Microsoft Docs
description: Informazioni sulle diverse autorizzazioni utente disponibili in Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561615"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Autorizzazioni utente predefinite in Azure Active Directory
In Azure Active Directory (Azure AD) a tutti gli utenti viene concesso un set di autorizzazioni predefinite. L'accesso di un utente è costituito dal tipo di utente, dalle [assegnazioni di ruolo](active-directory-users-assign-role-azure-portal.md) e dalla proprietà di singoli oggetti. Questo articolo descrive tali autorizzazioni predefinite e contiene un confronto delle impostazioni predefinite degli utenti membro e guest. Le autorizzazioni utente predefinite possono essere modificate solo nelle impostazioni utente in Azure AD.

## <a name="member-and-guest-users"></a>Utenti guest e membro
Il set di autorizzazioni predefinite ricevuto varia a seconda che l'utente sia un membro nativo del tenant (utente membro) o provenga da un'altra directory come guest di Collaborazione B2B (utente guest). Per altre informazioni sull'aggiunta di utenti guest, vedere [Che cos'è Collaborazione B2B in Azure Active Directory?](../b2b/what-is-b2b.md)
* Gli utenti membro possono registrare le applicazioni, gestire il proprio numero di cellulare e la propria foto profilo, modificare la propria password e invitare guest B2B. Gli utenti possono anche leggere tutte le informazioni della directory (con alcune eccezioni). 
* Gli utenti guest hanno autorizzazioni di directory limitate. Gli utenti guest, ad esempio, non possono esplorare le informazioni dal tenant, se non quelle del proprio profilo. Un utente guest può tuttavia recuperare informazioni su un altro utente, fornendo il nome dell'entità utente o un ID oggetto. Un utente guest può leggere le proprietà dei propri gruppi, inclusa l'appartenenza, indipendentemente dall'impostazione di **Le autorizzazioni degli utenti guest sono limitate**. Un utente guest non può visualizzare informazioni su altri oggetti tenant.

Le autorizzazioni predefinite per gli utenti guest sono restrittive per impostazione predefinita. Gli utenti guest possono essere aggiunti ai ruoli di amministratore, che concedono le autorizzazioni complete di lettura e scrittura contenute nel ruolo. È disponibile una restrizione aggiuntiva, ovvero la possibilità per gli utenti guest di invitare altri utenti guest. Impostando **Gli utenti guest possono invitare** su **No**, gli utenti guest non possono invitarne altri. Per informazioni, vedere [Delegare gli inviti per Collaborazione B2B](../b2b/delegate-invitations.md). Per concedere agli utenti guest le stesse autorizzazioni degli utenti membro per impostazione predefinita, impostare **Le autorizzazioni degli utenti guest sono limitate** su **No**. Questa impostazione concede tutte le autorizzazioni degli utenti membro agli utenti guest per impostazione predefinita, oltre a consentire l'aggiunta degli utenti guest ai ruoli amministrativi.

## <a name="compare-member-and-guest-default-permissions"></a>Confrontare le autorizzazioni predefinite di membri e guest

**Area** | **Autorizzazioni utente membro** | **Autorizzazioni utente guest**
------------ | --------- | ----------
Utenti e contatti | Lettura di tutte le proprietà pubbliche di utenti e contatti<br>Invito di utenti guest<br>Modifica della propria password<br>Gestione del proprio numero di cellulare<br>Gestione della propria foto<br>Invalidare i propri token di aggiornamento | Lettura delle proprietà personali<br>Leggi nome visualizzato, indirizzo di posta elettronica, nome di accesso, foto, nome dell'entità utente e proprietà dei tipi di utente di altri utenti e contatti<br>Modifica della propria password
Gruppi | Creazione di gruppi di sicurezza<br>Creazione di gruppi di Office 365<br>Lettura di tutte le proprietà dei gruppi<br>Lettura delle appartenenze a gruppi non nascoste<br>Lettura delle appartenenze a gruppi di Office 365 nascoste per il gruppo aggiunto<br>Gestione delle proprietà, della titolarità e dell'appartenenza a gruppi dell'utente<br>Aggiunta di utenti guest ai gruppi con proprietario<br>Gestire delle impostazioni di appartenenza dinamica<br>Eliminazione dei gruppi con proprietario<br>Ripristino dei gruppi di Office 365 con proprietario | Lettura di tutte le proprietà dei gruppi<br>Lettura delle appartenenze a gruppi non nascoste<br>Lettura delle appartenenze a gruppi di Office 365 nascoste per i gruppi aggiunti<br>Gestione dei gruppi con proprietario<br>Aggiunta di utenti guest ai gruppi con proprietario (se consentita)<br>Eliminazione dei gruppi con proprietario<br>Ripristino dei gruppi di Office 365 con proprietario<br>Lettura delle proprietà dei propri gruppi, inclusa l'appartenenza.
Applicazioni | Registrazione (creazione) di una nuova applicazione<br>Lettura delle proprietà delle applicazioni aziendali e registrate<br>Gestione delle proprietà, delle assegnazioni e delle credenziali per le applicazioni con proprietario<br>Creazione o eliminazione della password applicazione per un utente<br>Eliminazione delle applicazioni di proprietà<br>Ripristino delle applicazioni di proprietà | Lettura delle proprietà delle applicazioni aziendali e registrate<br>Gestione delle proprietà, delle assegnazioni e delle credenziali per le applicazioni con proprietario<br>Eliminazione delle applicazioni di proprietà<br>Ripristino delle applicazioni di proprietà
Dispositivi | Lettura di tutte le proprietà dei dispositivi<br>Gestione di tutte le proprietà dei dispositivi con proprietario<br> | Nessuna autorizzazione<br>Eliminazione dei dispositivi con proprietario<br>
Directory | Lettura di tutte le informazioni aziendali<br>Lettura di tutti i domini<br>Lettura di tutti i contratti dei partner | Lettura del nome visualizzato e dei domini verificati
Ruoli e ambiti | Lettura di tutti i ruoli amministrativi e delle appartenenze<br>Lettura di tutte le proprietà e dell'appartenenza delle unità amministrative | Nessuna autorizzazione 
Abbonamenti | Lettura di tutte le sottoscrizioni<br>Abilitazione del membro del piano di servizio | Nessuna autorizzazione
Criteri | Lettura di tutte le proprietà dei criteri<br>Gestione di tutte le proprietà dei criteri con proprietario | Nessuna autorizzazione

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Per limitare le autorizzazioni predefinite per gli utenti membro

Le autorizzazioni predefinite per gli utenti membro possono essere limitate nei modi seguenti.

Autorizzazioni | Spiegazione dell'impostazione
---------- | ------------
Gli utenti possono registrare l'applicazione | L'impostazione di questa opzione su No impedisce agli utenti di creare registrazioni di applicazioni. La possibilità può essere quindi concessa a utenti specifici aggiungendoli al ruolo di sviluppatore di applicazioni.
Consenti agli utenti di connettere l'account aziendale o dell'istituto di istruzione a LinkedIn | Se si imposta questa opzione su No, gli utenti non possono connettere il proprio account aziendale o dell'Istituto di istruzione con il proprio account LinkedIn. Per altre informazioni, vedere la pagina relativa al [consenso e alla condivisione di dati delle connessioni dell'account LinkedIn](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
Possibilità di creare i gruppi di sicurezza | Impostando questa opzione su No, gli utenti non possono creare gruppi di sicurezza. Gli amministratori globali e gli amministratori utenti possono comunque creare gruppi di sicurezza. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-cmdlets.md).
Possibilità di creare gruppi di Office 365 | Impostando questa opzione su No, gli utenti non possono creare gruppi di Office 365. Impostando questa opzione su In parte, un set selezionato di utenti può creare gruppi di Office 365. Gli amministratori globali e gli amministratori utenti saranno comunque in grado di creare gruppi di Office 365. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](../users-groups-roles/groups-settings-cmdlets.md).
Limita l'accesso al portale di amministrazione di Azure AD | Se si imposta questa opzione su Sì, si impedisce agli utenti di accedere a Azure Active Directory solo tramite portale di Azure.
Possibilità di leggere altri utenti | Questa impostazione è disponibile solo in PowerShell. Impostando questo flag su $false si impedisce a tutti gli amministratori non di leggere le informazioni utente dalla directory. Questo flag non impedisce la lettura delle informazioni utente in altri servizi Microsoft come Exchange Online. Questa impostazione è destinata a circostanze speciali e l'impostazione di questo flag su $false non è consigliata.

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
| microsoft.directory/applications/basic/update | Aggiornare le proprietà di base nelle applicazioni in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.directory/applications/restore | Ripristinare applicazioni in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Applicazioni aziendali di proprietà
Gli utenti possono eseguire le azioni seguenti nelle applicazioni aziendali di proprietà. Un'applicazione aziendale è costituita da un'entità servizio, uno o più criteri di applicazione e talvolta da un oggetto applicazione nello stesso tenant dell'entità servizio.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Leggere tutte le proprietà, incluse le proprietà con privilegi, in auditLogs in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Aggiornare le proprietà di base nei criteri in Azure Active Directory. |
| microsoft.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base nelle entità servizio in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Eliminare entità servizio in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Leggere tutte le proprietà, incluse le proprietà con privilegi, in signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Dispositivi di proprietà
Gli utenti possono eseguire le azioni seguenti nei dispositivi di proprietà.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/disable | Disabilitare dispositivi in Azure Active Directory. |

#### <a name="owned-groups"></a>Gruppi di proprietà
Gli utenti possono eseguire le azioni seguenti nei gruppi di proprietà.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Aggiornare le proprietà di base nei gruppi in Azure Active Directory. |
| microsoft.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Aggiornare la proprietà groups.dynamicMembershipRule in Azure Active Directory. |
| microsoft.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come assegnare i ruoli di amministratore di Azure AD, vedere [Assegnare un utente ai ruoli di amministratore in Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestire gli utenti](add-users-azure-active-directory.md)

---
title: Confrontare le autorizzazioni utente predefinite in Azure Active Directory | Microsoft Docs
description: Confrontare le autorizzazioni di membri, guest, proprietario dell'app e proprietario del gruppo
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/29/2018
ms.author: curtand
ms.reviewer: vincesm
ms.openlocfilehash: 880eaedcba2c0cdfe057ddb2460cf6a19bf8298e
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Autorizzazioni utente predefinite in Azure Active Directory

In Azure Active Directory (Azure AD) a tutti gli utenti viene concesso un set di autorizzazioni predefinite. L'accesso di un utente è costituito dal tipo di utente, dalle [appartenenze a ruoli](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-assign-role-azure-portal) e dalla proprietà di singoli oggetti. Questo articolo descrive tali autorizzazioni predefinite e contiene un confronto delle impostazioni predefinite degli utenti membro e guest.

## <a name="member-and-guest-users"></a>Utenti guest e membro
Il set di autorizzazioni predefinite ricevuto varia a seconda che l'utente sia un membro nativo del tenant (utente membro) o un guest di Collaborazione B2B (utente guest). Per altre informazioni su Collaborazione B2B, vedere [Informazioni su Collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md) (per gli utenti guest). 
* Gli utenti membro possono registrare le applicazioni, gestire il proprio numero di cellulare e la propria foto profilo, modificare la propria password e invitare guest B2B. Gli utenti possono anche leggere tutte le informazioni della directory (con alcune eccezioni). 
* Gli utenti guest di B2B di Azure AD hanno autorizzazioni directory limitate. Gli utenti guest, ad esempio, non possono esplorare le informazioni dal tenant, se non quelle del proprio profilo. Un utente guest può tuttavia recuperare informazioni su un altro utente, fornendo il nome dell'entità utente o un ID oggetto. Un guest non può visualizzare informazioni su altri oggetti del tenant, ad esempio i gruppi e le applicazioni.

Le autorizzazioni predefinite per gli utenti guest sono restrittive per impostazione predefinita. Gli utenti guest possono essere aggiunti ai ruoli di amministratore, che concedono le autorizzazioni complete di lettura e scrittura contenute nel ruolo. È disponibile una restrizione aggiuntiva, ovvero la possibilità per gli utenti guest di invitare altri utenti guest. Impostando **Gli utenti guest possono invitare** su **No**, gli utenti guest non possono invitarne altri. Per informazioni, vedere [Delegare gli inviti per Collaborazione B2B](active-directory-b2b-delegate-invitations.md). Per concedere agli utenti guest le stesse autorizzazioni degli utenti membro per impostazione predefinita, impostare **Le autorizzazioni degli utenti guest sono limitate** su **No**. Questa impostazione concede tutte le autorizzazioni degli utenti membro agli utenti guest per impostazione predefinita, oltre a consentire l'aggiunta degli utenti guest ai ruoli amministrativi.

## <a name="compare-member-and-guest-default-permissions"></a>Confrontare le autorizzazioni predefinite di membri e guest

**Area** | **Autorizzazioni utente membro** | **Autorizzazioni utente guest**
------------ | --------- | ----------
Utenti e contatti | Lettura di tutte le proprietà pubbliche di utenti e contatti<br>Invito di utenti guest<br>Modifica della propria password<br>Gestione del proprio numero di cellulare<br>Gestione della propria foto<br>Invalidare i propri token di aggiornamento | Lettura delle proprietà personali<br>Lettura di nome visualizzato, indirizzo di posta elettronica, nome di accesso, foto, nome dell'entità utente e proprietà di tipo utente di altri utenti e contatti<br>Modifica della propria password
Gruppi   | Creazione di gruppi di sicurezza<br>Creazione di gruppi di Office 365<br>Lettura di tutte le proprietà dei gruppi<br>Lettura delle appartenenze a gruppi non nascoste<br>Lettura delle appartenenze a gruppi di Office 365 nascoste per il gruppo aggiunto<br>Gestione delle proprietà, della titolarità e dell'appartenenza dei gruppi con proprietario<br>Aggiunta di utenti guest ai gruppi con proprietario<br>Gestire delle impostazioni di appartenenza dinamica<br>Eliminazione dei gruppi con proprietario<br>Ripristino dei gruppi di Office 365 con proprietario | Lettura di tutte le proprietà dei gruppi<br>Lettura delle appartenenze a gruppi non nascoste<br>Lettura delle appartenenze a gruppi di Office 365 nascoste per i gruppi aggiunti<br>Gestione dei gruppi con proprietario<br>Aggiunta di utenti guest ai gruppi con proprietario (se consentita)<br>Eliminazione dei gruppi con proprietario<br>Ripristino dei gruppi di Office 365 con proprietario           
APPLICAZIONI | Registrazione (creazione) di una nuova applicazione<br>Lettura delle proprietà delle applicazioni aziendali e registrate<br>Gestione delle proprietà, delle assegnazioni e delle credenziali per le applicazioni con proprietario<br>Creazione o eliminazione della password applicazione per un utente<br>Eliminazione delle applicazioni di proprietà<br>Ripristino delle applicazioni di proprietà | Lettura delle proprietà delle applicazioni aziendali e registrate<br>Gestione delle proprietà, delle assegnazioni e delle credenziali per le applicazioni con proprietario<br>Eliminazione delle applicazioni di proprietà<br>Ripristino delle applicazioni di proprietà
Dispositivi | Lettura di tutte le proprietà dei dispositivi<br>Gestione di tutte le proprietà dei dispositivi con proprietario<br> | Nessuna autorizzazione<br>Eliminazione dei dispositivi con proprietario<br>
Directory | Lettura di tutte le informazioni aziendali<br>Lettura di tutti i domini<br>Lettura di tutti i contratti dei partner | Lettura del nome visualizzato e dei domini verificati
Ruoli e ambiti | Lettura di tutti i ruoli amministrativi e delle appartenenze<br>Lettura di tutte le proprietà e dell'appartenenza delle unità amministrative | Nessuna autorizzazione              
Sottoscrizioni | Lettura di tutte le sottoscrizioni<br>Abilitazione del membro del piano di servizio | Nessuna autorizzazione
Criteri | Lettura di tutte le proprietà dei criteri<br>Gestione di tutte le proprietà dei criteri con proprietario | Nessuna autorizzazione

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Per limitare le autorizzazioni predefinite per gli utenti membro


Le autorizzazioni predefinite per gli utenti membro possono essere limitate nei modi seguenti. Per altre informazioni, vedere [App, autorizzazioni e consenso in Azure Active Directory](active-directory-apps-permissions-consent.md).

Autorizzazione | Spiegazione dell'impostazione
---------- | ------------
Possibilità di creare i gruppi di sicurezza | Impostando questa opzione su No, gli utenti non possono creare gruppi di sicurezza. Gli amministratori globali e gli amministratori account utente possono tuttavia creare i gruppi di sicurezza. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](active-directory-accessmanagement-groups-settings-cmdlets.md).
Possibilità di creare gruppi di Office 365 | Impostando questa opzione su No, gli utenti non possono creare gruppi di Office 365. Impostando questa opzione su In parte, un set selezionato di utenti può creare gruppi di Office 365. Gli amministratori globali e gli amministratori account utente potranno tuttavia creare gruppi di Office 365. Per informazioni, vedere [Cmdlet di Azure Active Directory per la configurazione delle impostazioni di gruppo](active-directory-accessmanagement-groups-settings-cmdlets.md).
Possibilità di dare il consenso alle applicazioni | Impostando questa opzione su No, gli utenti non possono dare il consenso alle applicazioni. Gli amministratori globali potranno tuttavia dare il consenso alle applicazioni. Per altre informazioni, vedere [App, autorizzazioni e consenso in Azure Active Directory](active-directory-apps-permissions-consent.md).
Possibilità di aggiungere app della raccolta al Pannello di accesso | Impostando questa opzione su No, gli utenti non possono aggiungere app della raccolta al Pannello di accesso.
Possibilità di registrare (creare) applicazioni | Impostando questa opzione su No, i non amministratori non possono creare applicazioni. Gli amministratori globali potranno tuttavia creare applicazioni. Per altre informazioni, vedere [App, autorizzazioni e consenso in Azure Active Directory](active-directory-apps-permissions-consent.md).
Gli amministratori e gli utenti nel ruolo Mittente dell'invito guest possono inviare inviti agli utenti guest | Impostando questa opzione su No, nessun utente può inviare inviti agli utenti guest. Vedere Configurazione delle autorizzazioni predefinite per gli utenti membro. Per altre informazioni, vedere [App, autorizzazioni e consenso in Azure Active Directory](active-directory-apps-permissions-consent.md).
I membri possono invitare gli utenti guest | Impostando questa opzione su No, gli utenti non possono invitare gli utenti guest. Gli amministratori globali, gli amministratori account utente e i mittenti dell'invito guest potranno tuttavia invitare gli utenti guest. Per altre informazioni, vedere [App, autorizzazioni e consenso in Azure Active Directory](active-directory-apps-permissions-consent.md).
Limita l'accesso al portale di amministrazione di Azure AD | Impostando questa opzione su No, gli utenti non possono accedere al portale di Azure Active Directory.
Possibilità di leggere altri utenti | Questa impostazione è disponibile solo in PowerShell. Impostandola su $false si impedisce a tutti gli amministratori di leggere le informazioni utente dalla directory, ma non si impedisce la lettura delle informazioni utente in altri servizi Microsoft come Exchange Online. Questa impostazione deve essere usata in casi speciali e impostarla su $false è sconsigliato.

## <a name="object-ownership"></a>Proprietà di un oggetto

### <a name="application-registration-owner-permissions"></a>Autorizzazioni di proprietario della registrazione dell'applicazione
Quando un utente registra un'applicazione, viene aggiunto automaticamente come proprietario dell'applicazione. In qualità di proprietario può gestire i metadati dell'applicazione, ad esempio il nome e le autorizzazioni richieste all'app. Può anche gestire la configurazione specifica del tenant dell'applicazione, ad esempio le assegnazioni utente e la configurazione SSO. Un proprietario può anche aggiungere o rimuovere altri proprietari. A differenza degli amministratori globali, i proprietari possono gestire solo le applicazioni di cui sono proprietari. Per assegnare un proprietario della registrazione dell'applicazione, vedere [Registrazione di app di Azure Active Directory](active-directory-app-registration.md).

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Autorizzazioni di proprietario del gruppo

Quando un utente crea un gruppo, viene aggiunto automaticamente come proprietario di tale gruppo. In qualità di proprietario può gestire le proprietà del gruppo, ad esempio il nome, oltre a gestire l'appartenenza. Un proprietario può anche aggiungere o rimuovere altri proprietari. A differenza degli amministratori globali e degli amministratori account utente, i proprietari possono gestire solo i gruppi di cui sono proprietari. Per assegnare un proprietario del gruppo, vedere [Gestione dei proprietari di un gruppo](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestire gli utenti](active-directory-create-users.md)

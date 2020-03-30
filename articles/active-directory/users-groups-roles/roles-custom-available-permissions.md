---
title: Autorizzazioni disponibili per i ruoli di amministratore personalizzati - Azure AD Documenti Microsoft
description: Autorizzazioni del ruolo di amministratore personalizzato per la delega della gestione delle identità.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025160"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Sottotipi e autorizzazioni di registrazione delle applicazioni in Azure Active DirectoryApplication registration subtypes and permissions in Azure Active Directory

Questo articolo contiene le autorizzazioni di registrazione delle app attualmente disponibili per le definizioni di ruolo personalizzate in Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Autorizzazioni per la gestione di applicazioni a directory singola

Quando si scelgono le autorizzazioni per il ruolo personalizzato, è possibile concedere l'accesso per gestire solo le applicazioni a directory singola. Le applicazioni a directory singola sono disponibili solo per gli utenti dell'organizzazione di Azure AD in cui è registrata l'applicazione. Le applicazioni a directory singola sono definite come con **tipi di account supportati** impostati su "Solo account in questa directory dell'organizzazione". Nell'API Graph, le applicazioni a directory singola hanno la proprietà signInAudience impostata su "AzureADMyOrg".

Per concedere l'accesso per gestire solo le applicazioni a directory singola, utilizzare le autorizzazioni riportate di seguito con il sottotipo **applications.myOrganization**. Ad esempio, microsoft.directory/applications.myOrganization/basic/update.

Vedere la [panoramica](roles-custom-overview.md) dei ruoli personalizzati per una spiegazione del significato dei termini generali di sottotipo, autorizzazione e set di proprietà. Le seguenti informazioni sono specifiche per le registrazioni delle applicazioni.

### <a name="create-and-delete"></a>Creare ed eliminare

Sono disponibili due autorizzazioni per concedere la possibilità di creare registrazioni di applicazioni, ognuna con un comportamento diverso:There are two permissions available for granting the ability to create application registrations, each with different behavior:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

L'assegnazione di questa autorizzazione comporta l'aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata verrà conteggiata rispetto alla quota di 250 oggetti creati del creatore.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applicazioni/creare

L'assegnazione di questa autorizzazione comporta che il creatore non venga aggiunto come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata non verrà conteggiata rispetto alla quota di 250 oggetti creati del creatore. Utilizzare questa autorizzazione con attenzione, perché non vi è nulla che impedisce all'assegnatario di creare registrazioni di app fino a quando non viene raggiunta la quota a livello di directory. Se vengono assegnate entrambe le autorizzazioni, questa autorizzazione ha la precedenza.

Se vengono assegnate entrambe le autorizzazioni, l'autorizzazione /create avrà la precedenza. Anche se l'autorizzazione /createAsOwner non aggiunge automaticamente il creatore come primo proprietario, i proprietari possono essere specificati durante la creazione della registrazione dell'app quando si usano le API Graph o i cmdlet di PowerShell.

Le autorizzazioni di creazione concedono l'accesso al comando **Nuova registrazione.**

[Queste autorizzazioni concedono l'accesso al comando Nuovo portale di registrazione](./media/roles-create-custom/new-custom-role.png)

Sono disponibili due autorizzazioni per concedere la possibilità di eliminare le registrazioni delle app:There are two permissions available for granting the ability to delete app registrations:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applicazioni/eliminazione

Concede la possibilità di eliminare le registrazioni dell'app indipendentemente dal sottotipo; vale a dire, sia applicazioni single-tenant e multi-tenant.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Concede la possibilità di eliminare le registrazioni delle app limitate a quelle accessibili solo agli account dell'organizzazione o delle applicazioni con un singolo tenant (sottotipo myOrganization).

![Queste autorizzazioni concedono l'accesso al comando Elimina registrazione app](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Quando si assegna un ruolo che contiene autorizzazioni di creazione, l'assegnazione di ruolo deve essere effettuata nell'ambito della directory. Un'autorizzazione di creazione assegnata a un ambito di risorsa non concede la possibilità di creare registrazioni di app.

### <a name="read"></a>Lettura

Tutti gli utenti membri dell'organizzazione possono leggere le informazioni di registrazione dell'app per impostazione predefinita. Tuttavia, gli utenti guest e le entità servizio dell'applicazione non possono. Se si prevede di assegnare un ruolo a un utente guest o a un'applicazione, è necessario includere le autorizzazioni di lettura appropriate.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Possibilità di leggere tutte le proprietà delle applicazioni single-tenant e multi-tenant al di fuori di proprietà che non possono essere lette in qualsiasi situazione come le credenziali.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Concede le stesse autorizzazioni di microsoft.directory/applications/allProperties/read, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applicazioni/proprietari/lettura

Offre la possibilità di leggere le proprietà dei proprietari in applicazioni single-tenant e multi-tenant. Concede l'accesso a tutti i campi nella pagina dei proprietari della registrazione dell'applicazione:

![Questa autorizzazione concede l'accesso alla pagina dei proprietari di registrazione app](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Concede l'accesso alle proprietà di registrazione dell'applicazione standard di lettura. Sono incluse le proprietà tra le pagine di registrazione dell'applicazione.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Concede le stesse autorizzazioni di microsoft.directory/applications/standard/read, ma solo per le applicazioni single-tenant.

### <a name="update"></a>Aggiornamento

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Possibilità di aggiornare tutte le proprietà su applicazioni a directory singola e multidirectory.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Concede le stesse autorizzazioni di microsoft.directory/applications/allProperties/update, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Possibilità di aggiornare la proprietà del tipo di account supportato (signInAudience) in applicazioni a directory singola e a più directory.

![Questa autorizzazione concede l'accesso alla proprietà del tipo di account supportata per la registrazione dell'app nella pagina di autenticazione](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Concede le stesse autorizzazioni di microsoft.directory/applications/audience/update, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Possibilità di aggiornare l'URL di risposta, l'URL di disconnessione, il flusso implicito e le proprietà del dominio dell'editore nelle applicazioni single-tenant e multi-tenant. Concede l'accesso a tutti i campi della pagina di autenticazione della registrazione dell'applicazione, ad eccezione dei tipi di account supportati:

![Concede l'accesso all'autenticazione di registrazione dell'app ma ai tipi di account non supportati](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Concede le stesse autorizzazioni di microsoft.directory/applications/authentication/update, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Possibilità di aggiornare il nome, il logo, l'URL della home page, l'URL dei termini del servizio e le proprietà dell'URL dell'informativa sulla privacy nelle applicazioni single-tenant e multi-tenant. Concede l'accesso a tutti i campi nella pagina di personalizzazione della registrazione dell'applicazione:

![Questa autorizzazione concede l'accesso alla pagina di branding per la registrazione dell'app](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Concede le stesse autorizzazioni di microsoft.directory/applications/basic/update, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applicazioni/credenziali/aggiornamento

Possibilità di aggiornare i certificati e le proprietà dei segreti client nelle applicazioni single-tenant e multi-tenant. Concede l'accesso a tutti i campi della pagina dei certificati di registrazione dell'applicazione & dei segreti:

![Questa autorizzazione concede l'accesso ai certificati di registrazione dell'app & pagina dei segreti](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Concede le stesse autorizzazioni di microsoft.directory/applications/credentials/update, ma solo per le applicazioni a directory singola.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applicazioni/proprietari/aggiornamento

Possibilità di aggiornare la proprietà del proprietario in single-tenant e multi-tenant. Concede l'accesso a tutti i campi nella pagina dei proprietari della registrazione dell'applicazione:

![Questa autorizzazione concede l'accesso alla pagina dei proprietari di registrazione app](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Concede le stesse autorizzazioni di microsoft.directory/applications/owners/update, ma solo per le applicazioni single-tenant.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applicazioni/autorizzazioni/aggiornamento

Possibilità di aggiornare le autorizzazioni delegate, le autorizzazioni delle applicazioni, le applicazioni client autorizzate, le autorizzazioni necessarie e concedere le proprietà di consenso nelle applicazioni single-tenant e multi-tenant. Non concede la possibilità di eseguire il consenso. Concede l'accesso a tutti i campi nelle autorizzazioni API di registrazione dell'applicazione ed esporre un'API pagine:Grants access to all fields on the application registration API permissions and Expose an API pages:

![Questa autorizzazione concede l'accesso alla pagina delle autorizzazioni dell'API di registrazione dell'app](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Questa autorizzazione concede l'accesso alla registrazione dell'app Esporre una pagina API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Concede le stesse autorizzazioni di microsoft.directory/applications/permissions/update, ma solo per le applicazioni single-tenant.

## <a name="required-license-plan"></a>Piano di licenza richiesto

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare ruoli personalizzati usando [il portale di Azure, Azure AD PowerShell e l'API GraphCreate](roles-create-custom.md) custom roles using the Azure portal, Azure AD PowerShell, and Graph API
- [Visualizzare le assegnazioni per un ruolo personalizzatoView the assignments for a custom role](roles-view-assignments.md)

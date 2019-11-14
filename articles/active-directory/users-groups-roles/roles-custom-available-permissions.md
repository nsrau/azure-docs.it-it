---
title: Autorizzazioni del ruolo amministratore personalizzato disponibili-Azure AD | Microsoft Docs
description: Autorizzazioni del ruolo amministratore personalizzato per la delega della gestione delle identità.
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025160"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Sottotipi e autorizzazioni per la registrazione dell'applicazione in Azure Active Directory

Questo articolo contiene le autorizzazioni di registrazione delle app attualmente disponibili per le definizioni di ruolo personalizzate in Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Autorizzazioni per la gestione di applicazioni a directory singola

Quando si scelgono le autorizzazioni per il ruolo personalizzato, è possibile concedere l'accesso per gestire solo le applicazioni con una sola directory. Le applicazioni a directory singola sono disponibili solo per gli utenti dell'organizzazione Azure AD in cui l'applicazione è registrata. Le applicazioni a directory singola sono definite con **tipi di account supportati** impostati su "account solo in questa directory organizzativa". Nella API Graph le applicazioni a directory singola hanno la proprietà signInAudience impostata su "AzureADMyOrg".

Per concedere l'accesso per gestire solo le applicazioni a directory singola, utilizzare le autorizzazioni riportate di seguito con il sottotipo **Applications. Organization**. Ad esempio, Microsoft. directory/Applications. organizzazione/Basic/Update.

Vedere [Cenni preliminari sui ruoli personalizzati](roles-custom-overview.md) per una spiegazione del significato del sottotipo, dell'autorizzazione e del set di proprietà dei termini generali. Le informazioni seguenti sono specifiche per le registrazioni di applicazioni.

### <a name="create-and-delete"></a>Creazione ed eliminazione

Sono disponibili due autorizzazioni per concedere la possibilità di creare registrazioni di applicazioni, ognuna con un comportamento diverso:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>Microsoft. directory/applicazioni/createAsOwner

L'assegnazione di questa autorizzazione comporta l'aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata verrà conteggiata in base alla quota degli oggetti creati 250 dell'autore.

#### <a name="microsoftdirectoryapplicationscreate"></a>Microsoft. directory/applicazioni/crea

L'assegnazione di questa autorizzazione comporta la mancata aggiunta del creatore come primo proprietario della registrazione dell'app creata e la registrazione dell'app creata non verrà conteggiata con la quota degli oggetti creati 250 dell'autore. Usare questa autorizzazione con cautela, perché non ci sono elementi che impediscono all'assegnatario di creare registrazioni di app fino a quando non viene raggiunta la quota a livello di directory. Se entrambe le autorizzazioni sono assegnate, questa autorizzazione avrà la precedenza.

Se vengono assegnate entrambe le autorizzazioni, l'autorizzazione/Create avrà la precedenza. Sebbene l'autorizzazione/createAsOwner non aggiunga automaticamente il creatore come primo proprietario, i proprietari possono essere specificati durante la creazione della registrazione dell'app quando si usano le API Graph o i cmdlet di PowerShell.

Le autorizzazioni create consentono di concedere l'accesso al nuovo comando di **registrazione** .

[Queste autorizzazioni concedono l'accesso al nuovo comando del portale di registrazione](./media/roles-create-custom/new-custom-role.png)

Sono disponibili due autorizzazioni per concedere la possibilità di eliminare le registrazioni dell'app:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Concede la possibilità di eliminare registrazioni per l'app indipendentemente dal sottotipo; ovvero applicazioni a tenant singolo e multi-tenant.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. directory/Applications. organizzazione/eliminazione

Consente di eliminare le registrazioni di app limitate a quelle accessibili solo agli account dell'organizzazione o alle applicazioni a tenant singolo (sottotipo di organizzazione).

![Queste autorizzazioni concedono l'accesso al comando Delete app Registration](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Quando si assegna un ruolo che contiene le autorizzazioni di creazione, l'assegnazione di ruolo deve essere eseguita nell'ambito di directory. Un'autorizzazione Create assegnata a un ambito di risorse non concede la possibilità di creare registrazioni per l'app.

### <a name="read"></a>Read

Per impostazione predefinita, tutti gli utenti membri dell'organizzazione possono leggere le informazioni di registrazione dell'app. Tuttavia, gli utenti guest e le entità servizio dell'applicazione non possono. Se si prevede di assegnare un ruolo a un utente Guest o a un'applicazione, è necessario includere le autorizzazioni di lettura appropriate.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. directory/Applications/allProperties/Read

Possibilità di leggere tutte le proprietà di applicazioni a tenant singolo e multi-tenant esterne a proprietà che non possono essere lette in alcuna situazione come le credenziali.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. directory/Applications. organizzazione/allProperties/lettura

Concede le stesse autorizzazioni di Microsoft. directory/Applications/allProperties/Read, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. directory/applicazioni/proprietari/lettura

Concede la possibilità di leggere la proprietà owners nelle applicazioni a tenant singolo e multi-tenant. Concede l'accesso a tutti i campi nella pagina proprietari registrazione applicazione:

![Questa autorizzazione concede l'accesso alla pagina dei proprietari di registrazione delle app](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. directory/applicazioni/standard/lettura

Concede l'accesso per la lettura delle proprietà standard di registrazione dell'applicazione. Sono incluse le proprietà nelle pagine di registrazione dell'applicazione.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. directory/Applications. organizzazione/standard/lettura

Concede le stesse autorizzazioni di Microsoft. directory/Applications/standard/Read, ma solo per le applicazioni a tenant singolo.

### <a name="update"></a>Update

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. directory/Applications/allProperties/Update

Possibilità di aggiornare tutte le proprietà nelle applicazioni a directory singola e multidirectory.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. directory/Applications. organizzazione/allProperties/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/allProperties/Update, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Possibilità di aggiornare la proprietà tipo di account supportato (signInAudience) nelle applicazioni a directory singola e multidirectory.

![Questa autorizzazione concede l'accesso alla proprietà tipo di account supportato per la registrazione dell'app nella pagina di autenticazione](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. directory/Applications. l'organizzazione/destinatari/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/audience/Update, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Possibilità di aggiornare l'URL di risposta, l'URL di disconnessione, il flusso implicito e le proprietà del dominio dell'editore nelle applicazioni a tenant singolo e multi-tenant. Concede l'accesso a tutti i campi nella pagina di autenticazione per la registrazione dell'applicazione ad eccezione dei tipi di account supportati:

![Concede l'accesso all'autenticazione per la registrazione dell'app, ma non ai tipi di conto supportati](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. directory/Applications. organizzazione/autenticazione/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/Authentication/Update, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Possibilità di aggiornare il nome, il logo, l'URL della Home page, l'URL delle condizioni di servizio e le proprietà dell'URL dell'informativa sulla privacy per le applicazioni a tenant singolo e multi-tenant. Concede l'accesso a tutti i campi nella pagina Personalizzazione registrazione applicazione:

![Questa autorizzazione concede l'accesso alla pagina Personalizzazione registrazione app](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. directory/Applications. organizzazione/base/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/Basic/Update, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Possibilità di aggiornare le proprietà dei certificati e dei segreti client nelle applicazioni a tenant singolo e multi-tenant. Concede l'accesso a tutti i campi nella pagina dei certificati di registrazione dell'applicazione & Secrets:

![Questa autorizzazione concede l'accesso ai certificati di registrazione dell'app & pagina dei segreti](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. directory/Applications. organizzazione/credenziali/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/Credentials/Update, ma solo per le applicazioni a directory singola.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Possibilità di aggiornare la proprietà Owner in un tenant singolo e multi-tenant. Concede l'accesso a tutti i campi nella pagina proprietari registrazione applicazione:

![Questa autorizzazione concede l'accesso alla pagina dei proprietari di registrazione delle app](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. directory/Applications. organizzazione/proprietari/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/owners/Update, ma solo per le applicazioni a tenant singolo.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Possibilità di aggiornare le autorizzazioni delegate, le autorizzazioni dell'applicazione, le applicazioni client autorizzate, le autorizzazioni necessarie e concedere le proprietà di consenso per le applicazioni a tenant singolo e multi-tenant. Non concede la possibilità di eseguire il consenso. Concede l'accesso a tutti i campi nelle autorizzazioni dell'API di registrazione dell'applicazione ed esporre le pagine dell'API:

![Questa autorizzazione concede l'accesso alla pagina autorizzazioni API registrazione app](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Questa autorizzazione concede l'accesso alla registrazione dell'app esporre una pagina API](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. directory/Applications. organizzazione/autorizzazioni/aggiornamento

Concede le stesse autorizzazioni di Microsoft. directory/Applications/Permissions/Update, ma solo per le applicazioni a tenant singolo.

## <a name="required-license-plan"></a>Piano di licenza obbligatorio

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Passaggi successivi

- Creare ruoli personalizzati usando [il portale di Azure, Azure ad PowerShell e API Graph](roles-create-custom.md)
- [Visualizzare le assegnazioni per un ruolo personalizzato](roles-view-assignments.md)

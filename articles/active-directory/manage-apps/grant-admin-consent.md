---
title: Concedere il consenso dell'amministratore a livello di tenant a un'applicazione-Azure AD
description: Informazioni su come concedere il consenso a livello di tenant a un'applicazione in modo che agli utenti finali non venga richiesto il consenso per l'accesso a un'applicazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68e40696c935adf6bef78636770b1add81255fa8
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90060918"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Concedere a un'applicazione il consenso amministratore a livello di tenant

Informazioni su come semplificare l'esperienza utente concedendo il consenso dell'amministratore a livello di tenant a un'applicazione. Questo articolo fornisce le diverse modalità per ottenere questo risultato. Questi metodi si applicano a tutti gli utenti finali nel tenant di Azure Active Directory (Azure AD).

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per concedere il consenso dell'amministratore a livello di tenant, è necessario accedere come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore di applicazioni cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).

> [!IMPORTANT]
> Quando a un'applicazione viene concesso il consenso dell'amministratore a livello di tenant, tutti gli utenti saranno in grado di accedere all'app a meno che non sia stata configurata per richiedere l'assegnazione dell'utente. Per limitare gli utenti che possono accedere a un'applicazione, richiedere l'assegnazione dell'utente e quindi assegnare utenti o gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).
>
> Il ruolo di amministratore globale è necessario per fornire il consenso dell'amministratore per le autorizzazioni dell'applicazione per l'API Microsoft Graph.

> [!WARNING]
> La concessione del consenso dell'amministratore a livello di tenant a un'applicazione consentirà all'app e all'autore dell'app di accedere ai dati dell'organizzazione. Esaminare attentamente le autorizzazioni richieste dall'applicazione prima di concedere il consenso.
>
> Il ruolo di amministratore globale è necessario per fornire il consenso dell'amministratore per le autorizzazioni dell'applicazione per l'API Microsoft Graph.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Concedere il consenso dell'amministratore dal portale di Azure

### <a name="grant-admin-consent-in-enterprise-apps"></a>Concedi il consenso dell'amministratore nelle app aziendali

È possibile concedere il consenso dell'amministratore a livello di tenant tramite *le applicazioni aziendali* se è già stato effettuato il provisioning dell'applicazione nel tenant. Ad esempio, è possibile eseguire il provisioning di un'app nel tenant se almeno un utente ha già acconsentito all'applicazione. Per ulteriori informazioni, vedere [come e perché le applicazioni vengono aggiunte a Azure Active Directory](../develop/active-directory-how-applications-are-added.md).

Per concedere il consenso dell'amministratore a livello di tenant a un'app elencata in **applicazioni aziendali**:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore di applicazioni cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selezionare **Azure Active Directory** quindi **applicazioni aziendali**.
3. Selezionare l'applicazione a cui si vuole concedere il consenso dell'amministratore a livello di tenant.
4. Selezionare **autorizzazioni** e quindi fare clic su **concedi il consenso dell'amministratore**.
5. Esaminare attentamente le autorizzazioni richieste dall'applicazione.
6. Se si accettano le autorizzazioni richieste dall'applicazione, concedere il consenso. In caso contrario, fare clic su **Annulla** o chiudere la finestra.

> [!WARNING]
> Se si concede il consenso dell'amministratore a livello di tenant tramite **app aziendali** , le autorizzazioni concesse in precedenza a livello di tenant vengono revocate. Le autorizzazioni concesse in precedenza agli utenti per loro conto non saranno interessate. 

### <a name="grant-admin-consent-in-app-registrations"></a>Concedi il consenso dell'amministratore in Registrazioni app

Per le applicazioni sviluppate dall'organizzazione o registrate direttamente nel tenant di Azure AD, è anche possibile concedere il consenso dell'amministratore a livello di tenant da **registrazioni app** nel portale di Azure.

Per concedere il consenso dell'amministratore a livello di tenant da **registrazioni app**:

1. Accedere al [portale di Azure](https://portal.azure.com) come [amministratore globale](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore di applicazioni cloud](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selezionare **Azure Active Directory** quindi **registrazioni app**.
3. Selezionare l'applicazione a cui si vuole concedere il consenso dell'amministratore a livello di tenant.
4. Selezionare **autorizzazioni API** , quindi fare clic su **concedi il consenso dell'amministratore**.
5. Esaminare attentamente le autorizzazioni richieste dall'applicazione.
6. Se si accettano le autorizzazioni richieste dall'applicazione, concedere il consenso. In caso contrario, fare clic su **Annulla** o chiudere la finestra.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Costruire l'URL per concedere il consenso dell'amministratore a livello di tenant

Quando si concede il consenso dell'amministratore a livello di tenant usando uno dei metodi descritti in precedenza, viene visualizzata una finestra dal portale di Azure per richiedere il consenso dell'amministratore a livello di tenant. Se si conosce l'ID client (noto anche come ID applicazione) dell'applicazione, è possibile creare lo stesso URL per concedere il consenso dell'amministratore a livello di tenant.

L'URL di consenso dell'amministratore a livello di tenant segue il formato seguente:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

dove:

* `{client-id}` è l'ID client dell'applicazione (noto anche come ID app).
* `{tenant-id}` è l'ID tenant dell'organizzazione o qualsiasi nome di dominio verificato.

Come sempre, esaminare attentamente le autorizzazioni richieste da un'applicazione prima di concedere il consenso.

> [!WARNING]
> Se si concede il consenso dell'amministratore a livello di tenant tramite questo URL, le autorizzazioni concesse in precedenza a livello di tenant vengono revocate. Le autorizzazioni concesse in precedenza agli utenti per loro conto non saranno interessate. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare la modalità con cui gli utenti finali consentono le applicazioni](configure-user-consent.md)

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Autorizzazioni e consenso in Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

[Azure AD in StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

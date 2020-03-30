---
title: Concedere il consenso di amministratore a livello di tenant a un'applicazione - Azure ADGrant tenant-wide admin consent to an application - Azure AD
description: Informazioni su come concedere il consenso a livello di tenant a un'applicazione in modo che agli utenti finali non venga richiesto il consenso quando si accede a un'applicazione.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480918"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Concedere il consenso di amministratore a livello di tenant a un'applicazioneGrant tenant-wide admin consent to an application

Informazioni su come semplificare l'esperienza utente concedendo il consenso di amministratore a livello di tenant per un'applicazione. Questo articolo fornisce i diversi modi per raggiungere questo obiettivo. Questi metodi si applicano a tutti gli utenti finali nel tenant di Azure Active Directory (Azure AD).

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

## <a name="prerequisites"></a>Prerequisiti

Per concedere il consenso di amministratore a livello di tenant, è necessario accedere come [amministratore globale,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore dell'applicazione cloud.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)

> [!IMPORTANT]
> Quando a un'applicazione è stato concesso il consenso di amministratore a livello di tenant, tutti gli utenti potranno accedere all'app a meno che non sia stata configurata per richiedere l'assegnazione dell'utente. Per limitare gli utenti che possono accedere a un'applicazione, richiedere l'assegnazione dell'utente e quindi assegnare utenti o gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> La concessione del consenso di amministratore a livello di tenant a un'applicazione concederà all'app e all'editore dell'app l'accesso ai dati dell'organizzazione. Esaminare attentamente le autorizzazioni richieste dall'applicazione prima di concedere il consenso.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Concedere il consenso dell'amministratore dal portale di AzureGrant admin consent from the Azure portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Concedere il consenso dell'amministratore nelle app EnterpriseGrant admin consent in Enterprise apps

È possibile concedere il consenso di amministratore a livello di tenant tramite *le applicazioni Enterprise* se è già stato eseguito il provisioning dell'applicazione nel tenant. Ad esempio, è possibile eseguire il provisioning di un'app nel tenant se almeno un utente ha già acconsentito all'applicazione. Per altre informazioni, vedere [Come e perché le applicazioni vengono aggiunte ad Azure Active Directory.](../develop/active-directory-how-applications-are-added.md)

Per concedere il consenso di amministratore a livello di tenant a un'app elencata nelle **applicazioni enterprise:**

1. Accedere al [portale](https://portal.azure.com) di Azure come [amministratore globale,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore dell'applicazione cloud.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selezionare **Azure Active Directory,** quindi **Applicazioni aziendali**.
3. Selezionare l'applicazione a cui si desidera concedere il consenso di amministratore a livello di tenant.
4. Selezionare **Autorizzazioni** e quindi fare clic su **Concedi il consenso dell'amministratore**.
5. Esaminare attentamente le autorizzazioni necessarie per l'applicazione.
6. Se si accettano le autorizzazioni necessarie per l'applicazione, concedere il consenso. In caso contrario, fare clic su **Annulla** o chiudere la finestra.

### <a name="grant-admin-consent-in-app-registrations"></a>Concedere il consenso dell'amministratore nelle registrazioni delle appGrant admin consent in App registrations

Per le applicazioni sviluppate dall'organizzazione o registrate direttamente nel tenant di Azure AD, è anche possibile concedere il consenso di amministratore a livello di tenant da Registrazioni di app nel portale di Azure.For applications your organization has developed, or which are registered directly in your Azure AD tenant, you can also grant tenant-wide admin consent from **App registrations** in the Azure portal.

Per concedere il consenso di amministratore a livello di tenant dalle **registrazioni delle app:**

1. Accedere al [portale](https://portal.azure.com) di Azure come [amministratore globale,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [amministratore dell'applicazione](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)o [amministratore dell'applicazione cloud.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Selezionare **Azure Active Directory,** quindi **Registrazioni app**.
3. Selezionare l'applicazione a cui si desidera concedere il consenso di amministratore a livello di tenant.
4. Selezionare **Autorizzazioni API** e quindi fare clic su **Concedi il consenso dell'amministratore**.
5. Esaminare attentamente le autorizzazioni necessarie per l'applicazione.
6. Se si accettano le autorizzazioni necessarie per l'applicazione, concedere il consenso. In caso contrario, fare clic su **Annulla** o chiudere la finestra.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Costruire l'URL per la concessione del consenso di amministratore a livello di tenant

Quando si concede il consenso di amministratore a livello di tenant usando uno dei metodi descritti in precedenza, dal portale di Azure viene aperta una finestra per richiedere il consenso dell'amministratore a livello di tenant. Se si conosce l'ID client (noto anche come ID applicazione) dell'applicazione, è possibile creare lo stesso ID per concedere il consenso di amministratore a livello di tenant.

L'URL di consenso dell'amministratore a livello di tenant segue il formato seguente:The tenant-wide admin consent URL follows the following format:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

dove:

* `{client-id}`è l'ID client dell'applicazione (noto anche come ID applicazione).
* `{tenant-id}`è l'ID tenant dell'organizzazione o qualsiasi nome di dominio verificato.

Come sempre, esaminare attentamente le autorizzazioni richieste da un'applicazione prima di concedere il consenso.

## <a name="next-steps"></a>Passaggi successivi

[Configurare il modo in cui gli utenti finali consentono di concedere il consenso alle applicazioni](configure-user-consent.md)

[Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)

[Autorizzazioni e consenso nella piattaforma di identità Microsoft](../develop/active-directory-v2-scopes.md)

[Azure AD on StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

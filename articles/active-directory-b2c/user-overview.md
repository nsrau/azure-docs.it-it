---
title: Panoramica degli account utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni sugli account utente in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f517754a8076fc18ed29f7db49f6ff9bfc6c7b3d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725761"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Panoramica degli account utente in Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C consente l'utilizzo di diversi tipi di account. Azure AD, Azure AD B2B e Azure AD B2C condividono i tipi di account utente che possono essere usati.

Sono disponibili i tipi di account seguenti:

- **Account aziendale**: un account aziendale può accedere alle risorse in un tenant; se dispone di un ruolo di amministratore, può gestire i tenant.
- **Account guest**: un account guest può essere solo un account Microsoft o un utente di Azure AD che può essere usato per accedere alle applicazioni o gestire i tenant. 
- **Account consumer**: un account consumer viene creato tramite un flusso utente di iscrizione in un'applicazione di Azure AD B2C o usando l'API Graph di Azure AD. Viene usato dagli utenti delle applicazioni registrate in Azure AD B2C. 

## <a name="work-account"></a>Account aziendale

La procedura per la creazione di un account aziendale è la stessa per tutti i tenant basati su Azure AD. Per creare un account aziendale, usare le informazioni contenute in [Avvio rapido: Aggiungere o modificare utenti in Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Per creare un account aziendale usare l'opzione **Nuovo utente** nel portale di Azure.

Quando si aggiunge un nuovo account aziendale, è necessario prendere in considerazione le impostazioni di configurazione seguenti:

- **Nome** e **Nome utente** - La proprietà **Nome** contiene il nome e il cognome dell'utente. Il **Nome utente** è l'identificatore che l'utente immette per accedere e include il dominio completo. La parte del nome di dominio del nome utente deve essere il nome di dominio predefinito iniziale *dominio.onmicrosoft.com* o un nome di dominio verificato, non federato, [personalizzato](../active-directory/fundamentals/add-custom-domain.md), ad esempio *contoso.com*.
- **Profilo** - L'account è configurato con un profilo di dati utente. È possibile immettere nome, cognome, posizione e nome del reparto. Dopo aver creato l'account, è possibile modificare il profilo.
- **Gruppi** - Usare un gruppo per eseguire attività di gestione come l'assegnazione di licenze o autorizzazioni per diversi utenti o dispositivi contemporaneamente. È possibile inserire il nuovo account in un [gruppo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) esistente del tenant. 
- **Ruolo della directory** - È necessario specificare il livello di accesso dell'account utente alle risorse del tenant. Sono disponibili i seguenti livelli di autorizzazione:

    - **Utente** - Gli utenti possono accedere alle risorse assegnate, ma non possono gestire la maggior parte delle risorse del tenant.
    - **Amministratore globale**- Gli amministratori globali hanno il controllo completo su tutte le risorse del tenant.
    - **Amministratore con limitazioni** - Selezionare il ruolo o i ruoli amministrativi per l'utente. Per altre informazioni sui ruoli che è possibile selezionare, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md). 

### <a name="create-a-work-account"></a>Creare un account aziendale

Per creare un nuovo account aziendale è possibile usare le informazioni seguenti:

- [Portale di Azure](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_post_users)

### <a name="update-a-user-profile"></a>Aggiornare un profilo utente

Per aggiornare un profilo utente è possibile usare le informazioni seguenti:

- [Portale di Azure](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

### <a name="reset-a-password-for-a-user"></a>Reimpostare la password di un utente

Per reimpostare la password di un utente è possibile usare le informazioni seguenti: 

- [Portale di Azure](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/user_update)

## <a name="guest-user"></a>Utente guest

È possibile invitare utenti esterni al tenant assegnando loro il ruolo di utente guest. Uno scenario tipico per questo tipo di invito al tenant di Azure AD B2C è quello in cui è necessario condividere le responsabilità di amministrazione. Per un esempio di utilizzo di un account guest, vedere [Proprietà di un utente di Collaborazione B2B di Azure Active Directory](../active-directory/b2b/user-properties.md).

Quando si invita un utente guest al tenant, viene fornito l'indirizzo di posta elettronica del destinatario e un messaggio che descrive l'invito. Il collegamento di invito indirizza l'utente alla pagina di consenso, dove è possibile selezionare il pulsante **Introduzione** e accettare il riepilogo delle autorizzazioni. Se all'indirizzo di posta elettronica non è collegata una casella di posta in arrivo, l'utente può aprire la pagina di consenso da una pagina Microsoft, usando le credenziali fornite nell'invito. L'utente deve quindi riscattare l'invito con la stessa procedura che avrebbe eseguito usando il collegamento presente nel messaggio di posta elettronica. Ad esempio: `https://myapps.microsoft.com/B2CTENANTNAME`.

Per invitare un utente guest è anche possibile usare l'[API Graph di Microsoft](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/api/invitation_post).

## <a name="consumer-user"></a>Utente consumer

L'utente consumer può accedere ad applicazioni protette da Azure AD B2C, ma non alle risorse di Azure, ad esempio il portale di Azure.  Può usare un account locale o account federati come ad esempio Facebook o Twitter. L'account consumer viene creato usando un [flusso utente di iscrizione o di accesso](../active-directory-b2c/active-directory-b2c-reference-policies.md).

È possibile specificare i dati raccolti quando viene creato un account consumer usando attributi utente personalizzati. Per altre informazioni, vedere [Definire attributi personalizzati in Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Per creare un account consumer di Azure AD B2C è possibile usare le informazioni contenute nella sezione **Creare account utente consumer** di [Usare l'API Graph di Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md). Per gestire le proprietà dell'account è possibile usare le informazioni contenute nella sezione **Aggiornare gli account utente consumer** dello stesso articolo.

### <a name="migrate-consumer-user-accounts"></a>Migrare gli account utente consumer

Può essere necessario eseguire la migrazione degli account utente consumer esistenti da qualsiasi provider di identità ad Azure AD B2C. Per altre informazioni, vedere [Migrazione degli utenti](active-directory-b2c-user-migration.md) oppure [Eseguire la migrazione di utenti con identità dei social network](active-directory-b2c-social-migration.md).
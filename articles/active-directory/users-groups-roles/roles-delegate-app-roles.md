---
title: Delegare i ruoli di amministratore dell'applicazione - Azure Active Directory | Microsoft Docs
description: Delega dei ruoli di gestione dell'accesso all'applicazione per concedere le autorizzazioni in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60469129"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegare i ruoli di amministratore dell'app in Azure Active Directory

 Azure AD consente di delegare la gestione dell'accesso all'applicazione per un set di ruoli amministrativi predefiniti. Oltre a ridurre il sovraccarico amministrativo globale, la delega di privilegi speciali per gestire le attività di accesso all'applicazione può migliorare il comportamento di sicurezza e ridurre i rischi di accesso non autorizzato. Per indicazioni generali e informazioni sui problemi relativi alla delega, vedere [Delegare i ruoli di amministratore in Azure Active Directory](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Delegare l'amministrazione di app

I ruoli descritti di seguito concedono le autorizzazioni per gestire le registrazioni delle applicazioni, le impostazioni del Single Sign-On e le assegnazioni di utenti e gruppi, oltre che per dare il consenso per le autorizzazioni delegate e le autorizzazioni dell'applicazione (a esclusione di Microsoft Graph e Azure AD Graph). L'unica differenza è che il ruolo di amministratore dell'applicazione concede anche le autorizzazioni di gestione delle impostazioni del proxy dell'applicazione. Nessuno dei ruoli consente di gestire le impostazioni di accesso condizionale.
> [!IMPORTANT]
> Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. La rappresentazione dell'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le altre assegnazioni dei ruoli in Azure AD. Un utente assegnato a questo ruolo potrebbe potenzialmente creare o aggiornare utenti o altri oggetti mentre rappresenta l'applicazione.

Per concedere l'autorizzazione di gestire l'accesso all'applicazione nel portale di Azure:

1. Accedere al proprio [tenant di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account idoneo per il ruolo di amministratore globale nel tenant.
2. Quando si hanno autorizzazioni sufficienti, aprire la [pagina Ruoli e amministratori](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Aprire uno dei ruoli seguenti per visualizzare le assegnazioni dei membri:
   * **Amministratore dell'applicazione**
   * **Amministratore applicazione cloud**
4. Nella pagina **Membri** del ruolo selezionare **Aggiungi membro**.
5. Selezionare uno o più membri da aggiungere al ruolo. <!--Members can be users or groups.-->

È possibile visualizzare la descrizione di questi ruoli in [Ruoli disponibili](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegare la registrazione di app

Per impostazione predefinita, tutti gli utenti possono creare registrazioni dell'applicazione, ma è possibile concedere in modo selettivo l'autorizzazione per creare registrazioni dell'applicazione o acconsentire ad autorizzare un'app.

1. Accedere al proprio [tenant di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account idoneo per il ruolo di amministratore globale nel tenant.
2. Una volta ottenute autorizzazioni sufficienti, impostare una o entrambe le opzioni seguenti:
   * Nella [pagina Impostazioni utente del tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) impostare **Gli utenti possono registrare applicazioni** su No.
   * In [Applicazioni aziendali - Impostazioni utente](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) impostare **Gli utenti possono fornire il consenso alle app che accedono ai dati aziendali per loro conto** su No.
3. Aggiungere quindi gli utenti che hanno bisogno di questa autorizzazione come membri del ruolo Sviluppatore applicazioni.

Quando un utente registra un'applicazione, viene aggiunto automaticamente come primo proprietario dell'applicazione.

## <a name="delegate-app-ownership"></a>Delegare la proprietà delle app

Proprietari di App e di registrazione app possibile ognuno gestire solo le applicazioni o le registrazioni di app che sono proprietari. Ad esempio, se si aggiunge un proprietario per l'applicazione Salesforce, tale proprietario può gestire l'accesso e la configurazione per Salesforce ma non per altre applicazioni. Un'app può avere diversi proprietari e un utente può essere proprietario di diverse app.

Un proprietario dell'applicazione può:

* Cambiare le proprietà dell'applicazione, ad esempio il nome e le autorizzazioni richieste dall'app
* Gestire le credenziali
* Configura accesso Single Sign-On
* Assegnare l'accesso utente
* Aggiungere o rimuovere altri proprietari
* Modificare il manifesto dell'applicazione
* Pubblicare l'app nella raccolta di app

> [!IMPORTANT]
> Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. La rappresentazione dell'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le altre assegnazioni dei ruoli in Azure AD. Un utente assegnato a questo ruolo potrebbe potenzialmente creare o aggiornare utenti o altri oggetti mentre rappresenta l'applicazione.

Il proprietario di una registrazione app può visualizzare e modificare la registrazione stessa.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>Per assegnare un proprietario a un'applicazione

1. Accedere al [tenant di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account idoneo per il ruolo Amministratore dell'applicazione o Amministratore applicazione cloud per il tenant.
2. Nella [pagina Registrazioni per l'app](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) del tenant selezionare un'app per aprire la pagina **Panoramica** dell'app.
3. Selezionare **Proprietari** per visualizzare l'elenco dei proprietari dell'app.
4. Selezionare **Aggiungi** per selezionare uno o più proprietari da aggiungere all'app.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Per assegnare un proprietario a una registrazione dell'applicazione

1. Accedere al [tenant di Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con un account idoneo per il ruolo Amministratore dell'applicazione o Amministratore applicazione cloud nel tenant.
2. Quando si hanno autorizzazioni sufficienti, nella [pagina Applicazioni aziendali](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) del tenant selezionare una registrazione dell'app per aprirla.
3. Selezionare **Impostazioni**.
4. Selezionare **Proprietari** nella pagina **Impostazioni** per visualizzare l'elenco dei proprietari dell'app.
5. Selezionare **Aggiungi proprietario** per selezionare uno o più proprietari da aggiungere all'app.

## <a name="next-steps"></a>Passaggi successivi

* [Autorizzazioni del ruolo di amministratore in Azure Active Directory](directory-assign-admin-roles.md)

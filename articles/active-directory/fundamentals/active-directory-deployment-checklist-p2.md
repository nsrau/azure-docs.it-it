---
title: Elenco di controllo delle licenze Premium P2 - Azure Active Directory | Microsoft Docs
description: Elenco di controllo della distribuzione delle funzionalità di Azure Active Directory Premium P2 in 30, 90 e più giorni.
services: active-directory
ms.service: active-directory
ms.component: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.custom: it-pro, seodec18
ms.openlocfilehash: 61cefe762b639dcc58c2e10fc845dd9729cb2cb2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078237"
---
# <a name="azure-active-directory-premium-p2-licensing-feature-checklist"></a>Elenco di controllo delle licenze di Azure Active Directory Premium P2

La distribuzione e la protezione di Azure Active Directory (Azure AD) per l'organizzazione possono sembrare attività molto impegnative. Questo articolo identifica alcune attività comuni considerate utili dagli utenti. I clienti completano in genere queste attività nel corso di 30, 90 o più giorni per migliorare la sicurezza del proprio ambiente. Anche le organizzazioni che hanno già distribuito Azure AD possono usare questo elenco di controllo per assicurarsi di ottenere il massimo dall'investimento.

Un'infrastruttura delle identità ben pianificata ed eseguita pone le basi per un accesso più sicuro ai carichi di lavoro e ai dati di produttività, consentito solo a utenti e dispositivi autenticati.

## <a name="prerequisites"></a>Prerequisiti

Questa guida presuppone che si possiedano licenze di Azure AD Premium P2, Enterprise Mobility + Security E5, Microsoft 365 E5 o combinazioni di licenze equivalenti.

[Licenze di Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)

[Microsoft 365 Enterprise](https://www.microsoft.com/en-us/licensing/product-licensing/microsoft-365-enterprise)

[Enterprise Mobility + Security](https://www.microsoft.com/en-us/licensing/product-licensing/enterprise-mobility-security)

## <a name="plan-and-deploy-day-1-30"></a>Pianificare e distribuire: Giorni 1-30

- Designare più di un amministratore globale (account di emergenza)
   - [Gestire gli account amministrativi di accesso di emergenza in Azure AD](../users-groups-roles/directory-emergency-access.md)
- Attivare Azure AD Privileged Identity Management (PIM) per visualizzare i report
   - [Iniziare a usare PIM](../privileged-identity-management/pim-getting-started.md)
- Dove possibile, usare ruoli amministrativi non globali.
   - [Assegnazione dei ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- Authentication
   - [Roll out self-service password reset](../authentication/howto-sspr-deployment.md) (Implementare la reimpostazione della password self-service)
   - Distribuire la funzione della password di protezione di Azure AD disponibile in anteprima
      - [Eliminare le password non appropriate nell'organizzazione](../authentication/concept-password-ban-bad.md)
      - [Anteprima: Applicare la funzione della password di protezione di Azure AD per Windows Server Active Directory](../authentication/concept-password-ban-bad-on-premises.md)
   - Configurare criteri di blocco degli account
      - [Blocco intelligente di Azure Active Directory](../authentication/howto-password-smart-lockout.md)
      - [AD FS Extranet Lockout and Extranet Smart Lockout](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (Blocco Extranet e blocco intelligente Extranet di AD FS)
   - [Implementare Azure Multi-Factor Authentication basato su cloud](../authentication/howto-mfa-getstarted.md)
   - [Registrazione convergente per la reimpostazione password self-service e Azure Multi-Factor Authentication (anteprima pubblica)](../authentication/concept-registration-mfa-sspr-converged.md)
   - [Abilitazione di Azure Active Directory Identity Protection](../identity-protection/enable.md)
      - [Esercitazione: Usare eventi di rischio per attivare Multi-Factor Authentication e modifiche delle password](../authentication/tutorial-risk-based-sspr-mfa.md)
   - [Password guidance](https://www.microsoft.com/research/publication/password-guidance/) (Materiale sussidiario sulle password)
      - Attenersi al requisito di lunghezza minima di otto caratteri, una password più lunga non è necessariamente un vantaggio.
      - Eliminare i requisiti di composizione di caratteri.
      - [Impostare una password senza scadenza.](../authentication/concept-sspr-policy.md#set-a-password-to-never-expire)
- Sincronizzare gli utenti da Active Directory locale
   - [Installare Azure AD Connect](../connect/active-directory-aadconnect-select-installation.md)
   - [Implementare la sincronizzazione dell'hash delle password](../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)
   - [Procedura: Configurare il writeback delle password](../authentication/howto-sspr-writeback.md)
   - [Implementare Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)
- [Assegnare licenze agli utenti in base all'appartenenza ai gruppi in Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)

## <a name="plan-and-deploy-day-31-90"></a>Pianificare e distribuire: Giorni 31-90

- [Pianificare l'accesso degli utenti guest](../b2b/what-is-b2b.md)
   - [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../b2b/add-users-administrator.md)
   - [Consentire o bloccare gli inviti agli utenti B2B da organizzazioni specifiche](../b2b/allow-deny-list.md)
   - [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali](../b2b/hybrid-cloud-to-on-premises.md)
- Prendere decisioni sulla strategia di gestione del ciclo di vita degli utenti
- [Stabilire la strategia di gestione dei dispositivi](../devices/overview.md)
   - [Scenari di utilizzo e considerazioni sulla distribuzione per Aggiunta di Azure AD](../devices/azureadjoin-plan.md)
- [Gestire Windows Hello for Business nell'organizzazione](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

## <a name="plan-and-deploy-day-90-and-beyond"></a>Pianificare e distribuire: Giorni da 90 in avanti

- [Gestione identità con privilegi di Azure AD](../privileged-identity-management/pim-configure.md)
   - [Configurare le impostazioni dei ruoli della directory di Azure AD in PIM](../privileged-identity-management/pim-how-to-change-default-settings.md)
   - [Assegnare ruoli della directory di Azure AD in PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md)
- [Completare una verifica di accesso per i ruoli della directory di Azure AD in PIM](../privileged-identity-management/pim-how-to-start-security-review.md)
- Gestire il ciclo di vita degli utenti in modo completo
   - Azure AD ha un approccio specifico alla gestione del ciclo di vita delle identità
   - Rimuovere i passaggi manuali dal ciclo di vita degli account dei dipendenti per evitare accessi non autorizzati:
      - Sincronizzare le identità dall'origine dei dati (sistema HR) ad Azure AD. (Collegamento ai sistemi HR supportati)
      - [Usare i gruppi dinamici per assegnare automaticamente gli utenti ai gruppi in base agli attributi HR (o altra origine dei dati), ad esempio reparto, posizione, area geografica e altri attributi.](../users-groups-roles/groups-dynamic-membership.md)
      - [Usare il provisioning di gestione dell'accesso basato sui gruppi per eseguire automaticamente il provisioning degli utenti per le applicazioni SaaS.](../manage-apps/what-is-access-management.md)

## <a name="next-steps"></a>Passaggi successivi

[Configurazioni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

[Criteri comuni di identità e accesso dei dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/identity-access-policies)

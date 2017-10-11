---
title: Aggiungere un utente di Collaborazione B2B di Azure Active Directory a un ruolo | Microsoft Docs
description: Aggiungere un utente guest a un ruolo in Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e816349ea971c997f655b4d51672dba666bc3e89
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Concedere autorizzazioni agli utenti di organizzazioni partner nel tenant di Azure Active Directory

Gli utenti di Collaborazione B2B di Azure Active Directory (Azure AD) vengono aggiunti come utenti guest alla directory e le autorizzazioni guest nella directory sono limitate per impostazione predefinita. Può essere necessario definire alcuni utenti guest per assegnare ruoli con privilegi elevati nell'organizzazione. Per supportare la definizione di ruoli con privilegi più elevati, gli utenti guest possono essere aggiunti a qualsiasi ruolo, in base alle esigenze dell'organizzazione.

## <a name="default-role"></a>Ruolo predefinito

![ruolo predefinito](./media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Ruolo Amministratore globale

![ruolo Amministratore globale](./media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Ruolo Amministratore con limitazioni

![ruolo Amministratore con limitazioni](./media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente in Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)

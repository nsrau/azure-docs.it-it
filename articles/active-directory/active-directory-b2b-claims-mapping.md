---
title: Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory | Microsoft Docs
description: Informazioni sul mapping delle attestazioni per Collaborazione B2B in Azure Active Directory
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
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 5f8559450b24effd40a38879aeae3a8dd03944a3
ms.lasthandoff: 03/17/2017


---

# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory

Azure Active Directory (Azure AD) supporta la personalizzazione delle attestazioni rilasciate nel token SAML per gli utenti di Collaborazione B2B. Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita, sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Le attestazioni inviate all'applicazione nel token SAML possono essere visualizzate o modificate nella scheda Attributi .

Due sono i possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML.

1. L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso

2. L'applicazione richiede che l'attestazione NameIdentifier sia diversa dal nome dell'entità utente archiviato in Azure Active Directory.

  ![Visualizzare le attestazioni nel token SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Per informazioni su come aggiungere e modificare le attestazioni, vedere l'articolo sulla personalizzazione delle attestazioni, [Personalizzazione delle attestazioni rilasciate nel token SAML per le app preintegrate in Azure Active Directory](develop/active-directory-saml-claims-customization.md). Per gli utenti di Collaborazione B2B, il mapping tra NameID e UPN tra tenant non è consentito per motivi di sicurezza.


## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)


---
title: Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory | Documentazione Microsoft
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 7fa5fc5e85268c908eb8b8c055837f41b36c4260


---

# <a name="b2b-collaboration-user-claims-mapping-in-azure-active-directory"></a>Mapping delle attestazioni utente per Collaborazione B2B in Azure Active Directory

Azure AD supporta la personalizzazione delle attestazioni rilasciate nel token SAML per gli utenti di Collaborazione B2B. Quando un utente esegue l'autenticazione all'applicazione, Azure AD rilascia un token SAML all'applicazione contenente informazioni (o attestazioni) sull'utente, che lo identificano in modo univoco. Per impostazione predefinita, sono inclusi il nome utente, l'indirizzo di posta elettronica, il nome e il cognome dell'utente. Le attestazioni inviate all'applicazione nel token SAML possono essere visualizzate o modificate nella scheda Attributi .

Due sono i possibili motivi per cui potrebbe essere necessario modificare le attestazioni rilasciate nel token SAML.

1. L'applicazione è stata scritta per richiedere un set di URI attestazione o di valori attestazione diverso

2. L'applicazione è stata distribuita avendo come requisito il fatto che l'attestazione NameIdentifier sia diversa dal nome utente (nome dell'entità utente) archiviato in Azure Active Directory.

  ![Visualizzare le attestazioni nel token SAML](media/active-directory-b2b-claims-mapping/view-claims-in-saml-token.png)

Per informazioni su come aggiungere e modificare le attestazioni per questi scenari, vedere l'articolo sulla personalizzazione delle attestazioni, [Personalizzazione delle attestazioni rilasciate nel token SAML per le app preintegrate in Azure Active Directory](develop/active-directory-saml-claims-customization.md). Agli utenti di B2B, per motivi di sicurezza, è impedito il mapping di NameID e di UPN tra tenant.


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



<!--HONumber=Feb17_HO1-->



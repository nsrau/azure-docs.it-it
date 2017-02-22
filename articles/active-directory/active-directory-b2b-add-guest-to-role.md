---
title: Aggiunta di un utente di Collaborazione B2B di Azure Active Directory a un ruolo | Documentazione Microsoft
description: "Le proprietà di un utente di Collaborazione B2B di Azure Active Directory sono configurabili"
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
ms.date: 02/03/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: 9ba46fe5ce6f06a69b057ebcb4af072689ac8ce9


---

# <a name="adding-an-azure-active-directory-b2b-collaboration-user-to-a-role"></a>Aggiunta di un utente di Collaborazione B2B di Azure Active Directory a un ruolo

Benché gli utenti di Collaborazione B2B di Azure Active Directory (Azure AD) vengano aggiunti come utenti guest alla directory e le autorizzazioni nella directory siano limitate per impostazione predefinita, potrebbe essere necessario aggiungere alcuni utenti guest da associare ad altri ruoli con privilegi dell'organizzazione. Per supportare questo scenario, gli utenti guest possono essere aggiunti a qualsiasi ruolo, in base alle esigenze dell'organizzazione.

## <a name="default-role"></a>Ruolo predefinito

![ruolo predefinito](media/active-directory-b2b-add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Ruolo Amministratore globale

![ruolo Amministratore globale](media/active-directory-b2b-add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Ruolo Amministratore con limitazioni

![ruolo Amministratore con limitazioni](media/active-directory-b2b-add-guest-to-role/limited-admin-role.png)

## <a name="auditing-and-reporting"></a>Controllo e creazione di report
Con gli utenti guest è ora possibile ottenere funzionalità di controllo analoghe a quelle degli utenti membri. Ecco un esempio della cronologia di invito e riscatto relativa all'utente Sam Oogle appena invitato:

![log di controllo](media/active-directory-b2b-add-guest-to-role/audit-log.png)

È possibile selezionare ogni evento per visualizzare i dettagli. È ad esempio possibile esaminare i dettagli relativi all'accettazione.

![dettagli dell'attività](media/active-directory-b2b-add-guest-to-role/activity-details.png)

È anche possibile esportare i log da Azure AD e usare lo strumento di creazione di report per ottenere report personalizzati.

## <a name="resending-invitations"></a>Nuovo invio degli inviti
È ora possibile passare alla pagina del profilo di un utente di Collaborazione B2B e inviare di nuovo gli inviti a utenti guest non ancora riscattati per qualsiasi motivo:

![nuovo invio dell'invito](media/active-directory-b2b-add-guest-to-role/resend-invitation.png)

> ![NOTE] L'invito verrà inviato di nuovo dall'utente connesso e l'utente verrà invitato alla directory, anche se l'invito originale è destinato a un'app o a un gruppo specifico.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



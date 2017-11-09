---
title: Gruppi dinamici e Collaborazione B2B di Azure Active Directory | Microsoft Docs
description: "Collaborazione B2B di Azure Active Directory può essere usato con i gruppi dinamici di Azure AD"
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
ms.date: 06/27/2017
ms.author: curtand
ms.reviewer: sasubram
ms.openlocfilehash: 5818c41610c8c5df89abcb0dcd058bcbe9579ce7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Gruppi dinamici e Collaborazione B2B di Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Che cosa sono i gruppi dinamici
La configurazione dinamica dell'appartenenza a gruppi di sicurezza per Azure Active Directory (Azure AD) è disponibile nel [portale di Azure](https://portal.azure.com). Gli amministratori possono impostare regole per popolare i gruppi creati in Azure Active Directory in base agli attributi utente, ad esempio tipo di utente, reparto o paese. I membri possono essere automaticamente aggiunti o rimossi in un gruppo di sicurezza in base agli attributi. Questi gruppi possono fornire accesso ad applicazioni o a risorse cloud, (documenti, siti di SharePoint) e per assegnare licenze ai membri. Per altre informazioni sui gruppi dinamici, vedere [Gruppi dedicati in Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

La [licenza di Azure AD Premium P1 o P2](https://azure.microsoft.com/pricing/details/active-directory/) appropriata è necessaria per creare e usare gruppi dinamici. Maggiori informazioni sono riportate nell’articolo [Creare regole basate su attributi per l'appartenenza dinamica ai gruppi in Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Che cosa sono i gruppi dinamici predefiniti
Il gruppo dinamico **Tutti gli utenti** consente agli amministratori tenant di creare un gruppo contenente tutti gli utenti nel tenant con un solo clic. Per impostazione predefinita, il gruppo **Tutti gli utenti** include tutti gli utenti della directory, compresi guest e membri.
Nel nuovo portale di amministrazione di Azure Active Directory è possibile scegliere di abilitare il gruppo **Tutti gli utenti** nella visualizzazione Impostazioni gruppo.

![Gruppi predefiniti](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Applicando la protezione avanzata al gruppo dinamico Tutti gli utenti
Per impostazione predefinita, il gruppo **Tutti gli utenti** contiene anche gli utenti (guest) di Collaborazione B2B. È possibile proteggere ulteriormente il gruppo **Tutti gli utenti** tramite una regola di rimozione degli utenti guest. La figura seguente illustra il gruppo **Tutti gli utenti** modificato per escludere i guest.

![Abilitare il gruppo Tutti gli utenti](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Potrebbe inoltre essere utile creare un nuovo gruppo dinamico che contiene solo gli utenti guest, in modo che sia possibile applicare a essi criteri quali, ad esempio, criteri di accesso condizionale di Azure AD.
Un gruppo può avere questo aspetto:

![Escludere gli utenti guest](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente in Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)

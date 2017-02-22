---
title: Gruppi dinamici e Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
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
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: d3f68340592d9032999ecb5cc16ac1fedcce4c33
ms.openlocfilehash: a483e219c1e730e174baa0ffa8ba471b0724f0da


---

# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Gruppi dinamici e Collaborazione B2B di Azure Active Directory

## <a name="what-are-dynamic-groups"></a>Che cosa sono i gruppi dinamici
La configurazione dinamica dell'appartenenza a gruppi di sicurezza per Azure Active Directory (Azure AD) è disponibile in anteprima pubblica nel [portale di Azure](https://portal.azure.com). Gli amministratori possono impostare regole per popolare i gruppi creati in Azure Active Directory in base agli attributi utente, ad esempio tipo di utente, reparto o paese. In questo modo i membri possono essere automaticamente aggiunti o rimossi in un gruppo di sicurezza in base alle modifiche apportate agli attributi. Questi gruppi possono essere usati per fornire accesso ad applicazioni o a risorse cloud, come documenti e siti di SharePoint, e per assegnare licenze ai membri. Per altre informazioni sui gruppi dinamici, vedere [Gruppi dedicati in Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md).

Con una sottoscrizione Premium P1 o P2 di AAD, il portale di Azure ora offre la possibilità di creare regole avanzate per consentire appartenenze dinamiche basate su attributi più complesse per i gruppi in anteprima di Azure Active Directory. Per altre informazioni sulla creazione di regole avanzate, vedere [Uso degli attributi per creare regole avanzate per l'appartenenza al gruppo in anteprima di Azure Active Directory](active-directory-groups-dynamic-membership-azure-portal.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Che cosa sono i gruppi dinamici predefiniti
Il gruppo dinamico **Tutti gli utenti** consente agli amministratori tenant di creare un gruppo contenente tutti gli utenti nel tenant con un solo clic. Per impostazione predefinita, il gruppo **Tutti gli utenti** include tutti gli utenti della directory, compresi guest e membri.
Nel nuovo portale di amministrazione di Azure Active Directory è possibile scegliere di abilitare il gruppo **Tutti gli utenti** nella visualizzazione Impostazioni gruppo.

![Gruppi predefiniti](media/active-directory-b2b-dynamic-groups/built-in-groups.png)

Applicando la protezione avanzata al gruppo dinamico Tutti gli utenti per impostazione predefinita, il gruppo **Tutti gli utenti** contiene anche gli utenti (guest) di Collaborazione B2B. Per proteggere ulteriormente il gruppo **Tutti gli utenti** rimuovendo gli utenti guest, è sufficiente modificare la regola basata sugli attributi del gruppo **Tutti gli utenti**. La figura seguente illustra il gruppo **Tutti gli utenti** modificato per escludere i guest.

![Abilitare il gruppo Tutti gli utenti](media/active-directory-b2b-dynamic-groups/enable-all-users-group.png)

Potrebbe anche essere utile creare un nuovo gruppo dinamico che contiene solo utenti guest, ad esempio per specificare come destinazione criteri (come i criteri di accesso condizionale) per gli utenti guest.
La figura seguente illustra come si presenta tale gruppo:

![Escludere gli utenti guest](media/active-directory-b2b-dynamic-groups/exclude-guest-users.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)
* [Limitazioni correnti di Collaborazione B2B](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->



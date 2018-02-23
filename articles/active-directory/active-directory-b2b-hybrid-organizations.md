---
title: Collaborazione B2B di Azure Active Directory per organizzazioni ibride | Microsoft Docs
description: Fornire ai partner l'accesso alle risorse locali e cloud con la collaborazione B2B di Azure AD
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 12/15/2017
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 2e690eeea6a9f7e1cc10830a913774daa3c66689
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Collaborazione B2B di Azure Active Directory per organizzazioni ibride

In un'organizzazione ibrida, in cui si dispone sia di risorse cloud che locali, è possibile consentire ai partner esterni di accedere a tali risorse. Per l'accesso alle risorse locali, è possibile gestire gli account dei partner in locale nell'ambiente Active Directory locale. Per accedere alle risorse dell'organizzazione, i partner si registrano con le credenziali del loro account. Per consentire ai partner di accedere alle risorse cloud con le stesse credenziali, è ora possibile usare Azure Active Directory (Azure AD) Connect per sincronizzare gli account dei partner nel cloud come utenti B2B di Azure AD (ovvero, come utenti con UserType = Guest).

## <a name="identify-unique-attributes-for-usertype"></a>Identificare gli attributi univoci per UserType

Per abilitare la sincronizzazione dell'attributo UserType, è prima necessario decidere in che modo tale attributo verrà ricavato da Active Directory locale. In altre parole, quali parametri nell'ambiente locale sono univoci per i collaboratori esterni? Definire un parametro che distingua i collaboratori esterni dai membri dell'organizzazione.

A questo scopo, due approcci comuni sono:

- Individuare un attributo di Active Directory locale non usato, ad esempio extensionAttribute1, e usarlo come attributo di origine. 
- In alternativa, è possibile ricavare il valore dell'attributo UserType da altre proprietà. Ad esempio si vogliono sincronizzare tutti gli utenti come Guest se il relativo attributo UserPrincipalName di Active Directory locale termina con il dominio *@partners.fabrikam123.org*.
 
Per altre informazioni sui requisiti per gli attributi, vedere [Abilitare la sincronizzazione di UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurare Azure AD Connect per sincronizzare gli utenti nel cloud

Dopo aver identificato l'attributo univoco, è possibile configurare Azure Active Directory (Azure AD) Connect per sincronizzare questi utenti nel cloud come utenti B2B di Azure AD (ovvero, come utenti con UserType = Guest). Dal punto di vista delle autorizzazioni, questi utenti non sono distinguibili dagli utenti B2B creati attraverso il procedimento di invito a Collaborazione B2B di Azure AD.

Per istruzioni sull'implementazione, vedere [Abilitare la sincronizzazione di UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di Collaborazione B2B di Azure AD, vedere [Informazioni su Collaborazione B2B di Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)
- Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](connect/active-directory-aadconnect.md).


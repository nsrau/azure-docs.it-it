---
title: Collaborazione B2B di Active Directory di Azure per le organizzazioni ibrida | Documenti Microsoft
description: Assegnare i partner ai sia locali e risorse cloud con la collaborazione B2B di Azure AD
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Collaborazione B2B di Active Directory di Azure per le organizzazioni ibrida

In un ambiente ibrido organizzazione, in cui sono disponibili sia in locale e nel cloud di risorse, è possibile consentire l'accesso partner esterni al sia locali e le risorse cloud. Per l'accesso alle risorse locali, è possibile gestire gli account di partner in locale nell'ambiente Active Directory locale. Partner di accedere con le credenziali dell'account partner di accedere alle risorse dell'organizzazione. Per fornire i partner di accesso alle risorse di cloud usando le stesse credenziali, è ora possibile utilizzare Connect di Azure Active Directory (Azure AD) per sincronizzare gli account per il partner nel cloud come Azure AD B2B utenti (ovvero, gli utenti con UserType = Guest).

## <a name="identify-unique-attributes-for-usertype"></a>Identificare gli attributi univoci per UserType

Prima di abilitare la sincronizzazione dell'attributo UserType, è innanzitutto necessario decidere come derivare l'attributo UserType da Active Directory locale. In altre parole, i parametri nell'ambiente locale sono univoci per i collaboratori esterni? Determinare un parametro che distingue i collaboratori esterni dai membri dell'organizzazione.

Due approcci comuni per questo sono:

- Definire un attributo di Active Directory locale non usato (ad esempio, extensionAttribute1) da utilizzare come attributo di origine. 
- In alternativa, è possibile derivare il valore di attributo UserType da altre proprietà. Ad esempio, si desidera sincronizzare tutti gli utenti come Guest se termina con l'attributo UserPrincipalName di Active Directory locale con il dominio  *@partners.fabrikam123.org* .
 
Per i requisiti di attributo dettagliate, vedere [consentono la sincronizzazione di UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurare Azure AD Connect, agli utenti di sincronizzazione per il cloud

Dopo aver identificato l'attributo univoco, è possibile configurare Azure AD Connect per sincronizzare gli utenti nel cloud come utenti di Azure AD B2B (vale a dire, gli utenti con UserType = Guest). Da un punto di vista di autorizzazione, questi utenti non sono distinguibili da utenti B2B creati tramite il processo di invito di collaborazione B2B di Azure Active Directory.

Per istruzioni di implementazione, vedere [consentono la sincronizzazione di UserType](connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica di collaborazione B2B di Azure Active Directory, vedere [cosa è collaborazione B2B di Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- Per una panoramica di Azure AD Connect, vedere [integrare le directory locali con Azure Active Directory](connect/active-directory-aadconnect.md).


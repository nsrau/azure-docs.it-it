---
title: Sincronizzare gli account dei partner locali al cloud come utenti B2B - Azure Active Directory | Microsoft Docs
description: Concedere ai partner esterni gestiti in locale l'accesso alle risorse locali e cloud usando le stesse credenziali con Collaborazione B2B di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ae75311ab61449f37ccea15a0bcb88fed80c3ed
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767347"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Concedere agli account partner gestiti in locale l'accesso alle risorse cloud usando Collaborazione B2B di Azure AD

Prima di Azure Active Directory (Azure AD), le organizzazioni con sistemi di identità locali gestivano gli account partner nella propria directory locale. In un'organizzazione di questo tipo, quando si inizia a spostare le app in Azure AD, è necessario assicurarsi che i partner possano accedere alle risorse necessarie. Non è importante se le risorse si trovano in locale o nel cloud. Si vuole inoltre che gli utenti partner possano usare le stesse credenziali di accesso per le risorse locali e quelle di Azure AD. 

Se si creano account per i partner esterni nella directory locale (ad esempio si crea un account con un nome di accesso "wmoran" per un utente esterno di nome Wendy Moran nel dominio partners.contoso.com), è ora possibile eseguire la sincronizzazione di questi account nel cloud. Nello specifico, è possibile usare Azure AD Connect per sincronizzare gli account partner nel cloud come utenti di Azure AD B2B (ovvero utenti con UserType = Guest). In questo modo, si consente agli utenti partner di accedere alle risorse cloud con le stesse credenziali degli account locali, senza concedere loro un accesso più esteso di quanto necessario. 

## <a name="identify-unique-attributes-for-usertype"></a>Identificare gli attributi univoci per UserType

Per abilitare la sincronizzazione dell'attributo UserType, è prima necessario decidere in che modo tale attributo verrà ricavato da Active Directory locale. In altre parole, quali parametri nell'ambiente locale sono univoci per i collaboratori esterni? Definire un parametro che distingua i collaboratori esterni dai membri dell'organizzazione.

A questo scopo, due approcci comuni sono:

- Individuare un attributo di Active Directory locale non usato, ad esempio extensionAttribute1, e usarlo come attributo di origine. 
- In alternativa, è possibile ricavare il valore dell'attributo UserType da altre proprietà. Ad esempio, si desidera sincronizzare tutti gli utenti come Guest se il relativo attributo UserPrincipalName di Active Directory locale termina con il dominio  *\@partners.contoso.com*.
 
Per altre informazioni sui requisiti per gli attributi, vedere [Abilitare la sincronizzazione di UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Configurare Azure AD Connect per sincronizzare gli utenti nel cloud

Dopo aver identificato l'attributo univoco, è possibile configurare Azure Active Directory (Azure AD) Connect per sincronizzare questi utenti nel cloud come utenti B2B di Azure AD (ovvero, come utenti con UserType = Guest). Dal punto di vista delle autorizzazioni, questi utenti non sono distinguibili dagli utenti B2B creati attraverso il procedimento di invito a Collaborazione B2B di Azure AD.

Per istruzioni sull'implementazione, vedere [Abilitare la sincronizzazione di UserType](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Passaggi successivi

- [Collaborazione B2B di Azure Active Directory per organizzazioni ibride](hybrid-organizations.md)
- [Concedere agli utenti B2B in Azure AD l'accesso alle applicazioni locali](hybrid-cloud-to-on-premises.md)
- Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).


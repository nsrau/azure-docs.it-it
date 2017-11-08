---
title: 'Licenze: reimpostazione password self-service di Azure AD | Microsoft Docs'
description: Requisiti di licenza per la reimpostazione password self-service di Azure AD
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: db849cd1e9da634064f79fbc041098542580ad02
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione password self-service di Azure AD

Per consentire il funzionamento della reimpostazione password di Azure AD, **è necessario che nell'organizzazione sia presente almeno una licenza assegnata**. La reimpostazione password non prevede l'applicazione delle licenze per utente. Per mantenere la conformità con il contratto di licenza Microsoft, è necessario assegnare le licenze a tutti gli utenti che usano le funzionalità Premium.

* **Utenti solo cloud** - Office 365 (O365) e SKU a pagamento o Azure AD Basic
* **Utenti cloud** e/o **utenti locali** - Azure AD P1 Premium o Azure AD P2 Premium, Enterprise Mobility + Security (EMS) o Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licenze richieste per il writeback delle password

Per usare il writeback delle password, è necessario disporre una delle licenze seguenti assegnate nel tenant.

* Azure AD P1 Premium
* Azure AD P2 Premium
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!WARNING]
> I piani di licenza Office 365 autonomi **non supportano il writeback delle password** e richiedono uno dei piani precedenti per l'uso della funzionalità.

Informazioni aggiuntive sulle licenze, ad esempio i costi, sono disponibili nelle pagine seguenti.

* [Sito sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>Abilitare le licenze per gruppi o per utente

Azure AD supporta ora licenze per gruppi che consentono agli amministratori di assegnare le licenze in blocco a un gruppo di utenti, anziché assegnarle loro singolarmente. [Assegnare, verificare e risolvere i problemi relativi alle licenze](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

Alcuni servizi Microsoft non sono disponibili in tutte le posizioni. Per assegnare una licenza a un utente, l'amministratore deve prima specificare la proprietà relativa alla località di uso per l'utente. L'assegnazione delle licenze può essere eseguita nella sezione Utente > Profilo > Impostazioni nel portale di Azure. **Quando si usa l'assegnazione di licenze ai gruppi, tutti gli utenti per cui non è specificata un percorso d'uso ereditano il percorso della directory**

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](active-directory-passwords-best-practices.md)
* [Reimpostare o modificare la password](active-directory-passwords-update-your-own-password.md).
* [Registrarsi per la reimpostazione della password self-service](active-directory-passwords-reset-register.md).
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](active-directory-passwords-data.md)
* [Metodi di autenticazione disponibili per gli utenti](active-directory-passwords-how-it-works.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](active-directory-passwords-policy.md)
* [Panoramica del writeback delle password](active-directory-passwords-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](active-directory-passwords-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](active-directory-passwords-how-it-works.md)
* [Come risolvere i problemi di reimpostazione della password self-service](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

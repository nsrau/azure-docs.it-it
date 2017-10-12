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
ms.date: 08/30/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 5445a479fd6893048eb8ff356fa829a3dcd5f7d3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione password self-service di Azure AD

Per consentire il funzionamento della reimpostazione password di Azure AD, **è necessario che nell'organizzazione sia presente almeno una licenza assegnata**. La reimpostazione password non prevede l'applicazione delle licenze per utente. Per mantenere la conformità con il contratto di licenza Microsoft, è necessario assegnare le licenze a tutti gli utenti che usano le funzionalità Premium.

* **Solo utenti del cloud** - Office 365 (O365) e SKU a pagamento o Azure AD Basic
* **Utenti cloud** e/o **utenti locali** - Azure AD P1 Premium o Azure AD P2 Premium, Enterprise Mobility + Security (EMS) o Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>Licenze richieste per il writeback delle password

Per usare il writeback delle password, è necessario disporre una delle licenze seguenti assegnate nel tenant.

* Azure AD P1 Premium
* Azure AD P2 Premium
* Enterprise Mobility + Security E3
* Enterprise Mobility + Security E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!NOTE]
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

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md): iniziare a usare la gestione self-service delle password di Azure AD 
* [**Dati** ](active-directory-passwords-data.md): informazioni sui dati necessari e su come vengono usati per la gestione delle password
* [**Implementazione**](active-directory-passwords-best-practices.md): pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md): personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Reporting**](active-directory-passwords-reporting.md): verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service

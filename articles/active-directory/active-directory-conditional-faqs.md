---
title: Domande frequenti sull'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Ottenere risposte alle domande frequenti sull'accesso condizionale in Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: e89550763209bc2d56dbc5dd374239ff404ccb11
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Domande frequenti sull'accesso condizionale di Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quali applicazioni funzionano con i criteri di accesso condizionale?

Per informazioni sulle applicazioni che usano i criteri di accesso condizionale, vedere [Applicazioni e browser che usano regole di accesso condizionale in Azure Active Directory](active-directory-conditional-access-supported-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>I criteri di accesso condizionale vengono applicati a Collaborazione B2B e agli utenti guest?

I criteri vengono applicati agli utenti di Collaborazione business-to-business (B2B). In alcuni casi, tuttavia, è possibile che un utente non sia in grado di soddisfare i requisiti dei criteri. È possibile, ad esempio, che l'organizzazione di un utente guest non supporti l'autenticazione a più fattori. 



## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>I criteri di SharePoint Online si applicano anche a OneDrive for Business?

Sì. I criteri di SharePoint Online si applicano anche a OneDrive for Business.


## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Perché non è possibile impostare criteri nelle app client, ad esempio Word o Outlook?

I criteri di accesso condizionale impostano i requisiti per l'accesso a un servizio e vengono applicati nel momento in cui viene eseguita l'autenticazione al servizio. I criteri non vengono impostati direttamente su un'applicazione client, ma vengono applicati quando un client chiama un servizio. I criteri impostati in SharePoint, ad esempio, vengono applicati ai client che chiamano SharePoint e i criteri impostati in Exchange vengono applicati ad Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Un criterio di accesso condizionale è applicabile agli account del servizio?

I criteri di accesso condizionale vengono applicati a tutti gli account utente, inclusi gli account utente usati come account del servizio. In molti casi, un account del servizio in esecuzione automatica non è in grado di soddisfare i criteri di accesso condizionale. È possibile, ad esempio, che sia richiesta l'autenticazione a più fattori. Gli account del servizio possono essere esclusi dai criteri usando le impostazioni di gestione dei criteri di accesso condizionale. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Sono disponibili API Graph per configurare i criteri di accesso condizionale?

Attualmente no. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Quali sono i criteri di esclusione predefiniti per le piattaforme per dispositivi non supportate?

Attualmente i criteri di accesso condizionale sono applicati in modo selettivo per gli utenti che usano dispositivi iOS e Android. Per impostazione predefinita, i criteri di accesso condizionale per dispositivi iOS e Android non incidono sulle applicazioni in altre piattaforme per dispositivi. Un amministratore tenant può decidere di ignorare i criteri globali per disabilitare l'accesso per gli utenti su piattaforme non supportate.


## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Come funzionano i criteri di accesso condizionale per Microsoft Teams?

Microsoft Teams si basa principalmente su Exchange Online e SharePoint Online per gli scenari di produttività di base, come riunioni, calendari e condivisione di file. I criteri di accesso condizionale impostati per queste app cloud si applicano a Microsoft Teams quando un utente esegue l'accesso direttamente a Microsoft Teams.

Microsoft Teams è supportato anche separatamente come app cloud nei criteri di accesso condizionale di Azure Active Directory. I criteri di accesso condizionale impostati per un'app cloud si applicano a Microsoft Teams quando un utente esegue l'accesso. Senza i criteri corretti in altre app come Exchange Online e SharePoint Online, gli utenti possono tuttavia essere comunque in grado di accedere direttamente a tali risorse.

I client desktop di Microsoft Teams per Windows e Mac supportano l'autenticazione moderna. Con l'autenticazione moderna, l'accesso basato su Azure Active Directory Authentication Library (ADAL) viene integrato in applicazioni client Microsoft Office su più piattaforme.
---
title: Domande frequenti sull'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Risposte alle domande frequenti sull'accesso condizionale in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad0494868c494b488a238a81e504c58552813907
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508981"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Domande frequenti sull'accesso condizionale di Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quali applicazioni funzionano con i criteri di accesso condizionale?

Per informazioni sulle applicazioni che funzionano con i criteri di accesso condizionale, vedere [applicazioni e browser che usano regole di accesso condizionale in Azure Active Directory](technical-reference.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>Criteri di accesso condizionale vengono applicati per collaborazione B2B e gli utenti guest?

I criteri vengono applicati agli utenti di Collaborazione business-to-business (B2B). In alcuni casi, tuttavia, è possibile che un utente non sia in grado di soddisfare i requisiti dei criteri. È possibile, ad esempio, che l'organizzazione di un utente guest non supporti l'autenticazione a più fattori. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>I criteri di SharePoint Online si applicano anche a OneDrive for Business?

Sì. I criteri di SharePoint Online si applicano anche a OneDrive for Business.

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Perché non è possibile impostare criteri nelle app client, ad esempio Word o Outlook?

Criteri di accesso condizionale impostano i requisiti per l'accesso a un servizio. e vengono applicati nel momento in cui viene eseguita l'autenticazione al servizio. I criteri non vengono impostati direttamente su un'applicazione client, ma vengono applicati quando un client chiama un servizio. I criteri impostati in SharePoint, ad esempio, vengono applicati ai client che chiamano SharePoint e i criteri impostati in Exchange vengono applicati ad Outlook.

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Criteri di accesso condizionale si applica agli account del servizio?

Criteri di accesso condizionale si applicano a tutti gli account utente. inclusi gli account utente usati come account del servizio. Spesso, un account del servizio esecuzione automatico non riesce a soddisfare i requisiti dei criteri di accesso condizionale. È possibile, ad esempio, che sia richiesta l'autenticazione a più fattori. Gli account del servizio possono essere esclusi dai criteri usando le impostazioni di gestione dei criteri di accesso condizionale. 

## <a name="are-graph-apis-available-for-configuring-conditional-access-policies"></a>Sono disponibili per la configurazione dei criteri di accesso condizionale API Graph?

Attualmente no. 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Quali sono i criteri di esclusione predefiniti per le piattaforme per dispositivi non supportate?

Attualmente, i criteri di accesso condizionale vengono applicati in modo selettivo gli utenti di dispositivi iOS e Android. Le applicazioni in altre piattaforme, per impostazione predefinita, non sono interessate dal criterio di accesso condizionale per dispositivi iOS e Android. Un amministratore tenant può decidere di ignorare i criteri globali per disabilitare l'accesso per gli utenti su piattaforme non supportate.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Come funzionano i criteri di accesso condizionale per Microsoft Teams?

Microsoft Teams si basa principalmente su Exchange Online e SharePoint Online per gli scenari di produttività di base, come riunioni, calendari e condivisione di file. Criteri di accesso condizionale impostati per queste App cloud si applicano a Microsoft Teams quando un utente accede direttamente in Microsoft Teams.

Microsoft Teams è supportato anche separatamente come app cloud nei criteri di Active Directory accesso condizionale di Azure. Criteri di accesso condizionale impostati per un'app cloud si applicano a Microsoft Teams quando un utente esegue l'accesso. Senza i criteri corretti in altre app come Exchange Online e SharePoint Online, gli utenti possono tuttavia essere comunque in grado di accedere direttamente a tali risorse.

I client desktop di Microsoft Teams per Windows e Mac supportano l'autenticazione moderna. Con l'autenticazione moderna, l'accesso basato su Azure Active Directory Authentication Library (ADAL) viene integrato in applicazioni client Microsoft Office su più piattaforme.

## <a name="next-steps"></a>Passaggi successivi

- Per configurare i criteri di accesso condizionale per l'ambiente, vedere la [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 

---
title: Domande frequenti sull'accesso condizionale Azure Active Directory | Microsoft Docs
description: Risposte alle domande frequenti sull'accesso condizionale in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5e75f7214e3a9aa5b44a763cc3448583b47185f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253240"
---
# <a name="azure-active-directory-conditional-access-faqs"></a>Domande frequenti sull'accesso condizionale Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quali applicazioni funzionano con i criteri di accesso condizionale?

Per informazioni sulle applicazioni che funzionano con i criteri di accesso condizionale, vedere [applicazioni e browser che usano regole di accesso condizionale in Azure Active Directory](concept-conditional-access-cloud-apps.md).

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>I criteri di accesso condizionale sono applicati per la collaborazione B2B e gli utenti Guest?

I criteri vengono applicati agli utenti di Collaborazione business-to-business (B2B). In alcuni casi, tuttavia, è possibile che un utente non sia in grado di soddisfare i requisiti dei criteri. È possibile, ad esempio, che l'organizzazione di un utente guest non supporti l'autenticazione a più fattori. 

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>I criteri di SharePoint Online si applicano anche a OneDrive for Business?

Sì. I criteri di SharePoint Online si applicano anche a OneDrive for Business. Per altre informazioni, vedere l'articolo relativo [alle dipendenze del servizio di accesso condizionale](service-dependencies.md) e prendere in considerazione i criteri di destinazione per l' [app Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="why-cant-i-set-a-policy-directly-on-client-apps-like-word-or-outlook"></a>Perché non è possibile impostare un criterio direttamente sulle app client, ad esempio Word o Outlook?

Un criterio di accesso condizionale imposta i requisiti per l'accesso a un servizio. e vengono applicati nel momento in cui viene eseguita l'autenticazione al servizio. I criteri non vengono impostati direttamente su un'applicazione client, ma vengono applicati quando un client chiama un servizio. I criteri impostati in SharePoint, ad esempio, vengono applicati ai client che chiamano SharePoint e i criteri impostati in Exchange vengono applicati ad Outlook. Per altre informazioni, vedere l'articolo relativo [alle dipendenze del servizio di accesso condizionale](service-dependencies.md) e prendere in considerazione i criteri di destinazione per l' [app Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>I criteri di accesso condizionale si applicano agli account del servizio?

I criteri di accesso condizionale si applicano a tutti gli account utente. inclusi gli account utente usati come account del servizio. Spesso, un account del servizio che viene eseguito in modo automatico non è in grado di soddisfare i requisiti di un criterio di accesso condizionale. È possibile, ad esempio, che sia richiesta l'autenticazione a più fattori. Gli account del servizio possono essere esclusi da un criterio usando l'esclusione di un [utente o un gruppo](concept-conditional-access-users-groups.md#exclude-users). 

## <a name="what-is-the-default-exclusion-policy-for-unsupported-device-platforms"></a>Quali sono i criteri di esclusione predefiniti per le piattaforme per dispositivi non supportate?

Attualmente, i criteri di accesso condizionale vengono applicati in modo selettivo per gli utenti di dispositivi iOS e Android. Per impostazione predefinita, le applicazioni su altre piattaforme del dispositivo non sono interessate dai criteri di accesso condizionale per i dispositivi iOS e Android. Un amministratore tenant può decidere di ignorare i criteri globali per disabilitare l'accesso per gli utenti su piattaforme non supportate.

## <a name="how-do-conditional-access-policies-work-for-microsoft-teams"></a>Come funzionano i criteri di accesso condizionale per i team Microsoft?

Microsoft Teams si basa principalmente su Exchange Online e SharePoint Online per gli scenari di produttività di base, come riunioni, calendari e condivisione di file. I criteri di accesso condizionale impostati per queste app Cloud si applicano a Microsoft teams quando un utente accede direttamente a Microsoft teams.

Microsoft teams è inoltre supportato separatamente come app cloud nei criteri di accesso condizionale. I criteri di accesso condizionale impostati per un'app Cloud si applicano a Microsoft teams quando un utente accede. Senza i criteri corretti in altre app come Exchange Online e SharePoint Online, gli utenti possono tuttavia essere comunque in grado di accedere direttamente a tali risorse.

I client desktop di Microsoft Teams per Windows e Mac supportano l'autenticazione moderna. Con l'autenticazione moderna, l'accesso basato su Azure Active Directory Authentication Library (ADAL) viene integrato in applicazioni client Microsoft Office su più piattaforme.

Per altre informazioni, vedere l'articolo relativo [alle dipendenze del servizio di accesso condizionale](service-dependencies.md) e prendere in considerazione i criteri di destinazione per l' [app Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) .

## <a name="why-are-some-tabs-not-working-in-microsoft-teams-after-enabling-conditional-access-policies"></a>Perché alcune schede non funzionano in Microsoft teams dopo aver abilitato i criteri di accesso condizionale?

Dopo aver abilitato alcuni criteri di accesso condizionale nel tenant in Microsoft teams, alcune schede potrebbero non funzionare più nel client desktop come previsto. Tuttavia, le schede interessate funzionano quando si usa il client Web Microsoft teams. Le schede interessate possono includere Power BI, form, VSTS, PowerApps e l'elenco di SharePoint.

Per visualizzare le schede interessate, è necessario usare il client Web teams in Edge, Internet Explorer o Chrome con l'estensione accounts di Windows 10 installata. Alcune schede dipendono dall'autenticazione Web, che non funziona in Microsoft teams desktop client quando è abilitato l'accesso condizionale. Microsoft collabora con i partner per abilitare questi scenari. Fino a oggi, abbiamo abilitato scenrarios che coinvolgono Planner, OneNote e Stream.

## <a name="next-steps"></a>Passaggi successivi

- Per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere le [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md). 

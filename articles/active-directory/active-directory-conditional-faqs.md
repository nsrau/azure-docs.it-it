---
title: Domande frequenti sull&quot;accesso condizionale di Azure Active Directory | Microsoft Docs
description: 'Domande frequenti sull&quot;accesso condizionale  '
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: dee29df176389f39907d302cedc6143c0d3e3322
ms.lasthandoff: 04/12/2017


---
# <a name="azure-active-directory-conditional-access-faq"></a>Domande frequenti sull'accesso condizionale di Azure Active Directory

## <a name="which-applications-work-with-conditional-access-policies"></a>Quali applicazioni funzionano con i criteri di accesso condizionale?

**A:** vedere [Applicazioni e browser che usano regole di accesso condizionale in Azure Active Directory](active-directory-conditional-access-supported-apps.md).

---

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>I criteri di accesso condizionale vengono applicati a Collaborazione B2B e agli utenti guest?
**R:** I criteri vengono applicati agli utenti di Collaborazione B2B. In alcuni casi tuttavia l'utente potrebbe non soddisfare i criteri richiesti, ad esempio se l'organizzazione non supporta l'autenticazione a più fattori. I criteri non sono attualmente applicati agli utenti guest di SharePoint. La relazione guest viene gestita in SharePoint. Gli account utente guest non sono soggetti ai criteri di accesso nel server di autenticazione. L'accesso guest può essere gestito in SharePoint.

---

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>I criteri di SharePoint Online si applicano anche a OneDrive for Business?
**R:** Sì.

---

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Perché non è possibile impostare criteri nelle app client, ad esempio Word o Outlook?
**R:** I criteri di accesso condizionale stabiliscono requisiti per l'accesso a un servizio e vengono applicati al momento dell'autenticazione al servizio in questione. I criteri non sono quindi impostati direttamente su un'applicazione client, ma vengono applicati quando quest'ultima effettua una chiamata a un servizio. Ad esempio, i criteri impostati in SharePoint verranno applicati ai client che chiamano SharePoint e i criteri impostati in Exchange verranno applicati ad Outlook.

--- 

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>Un criterio di accesso condizionale è applicabile agli account del servizio?
**R:** I criteri di accesso condizionale vengono applicati a tutti gli account utente, inclusi gli account utente usati come account del servizio. In molti casi un account del servizio in esecuzione automatica non è in grado di rispettare un criterio, ad esempio nel caso in cui sia necessaria l'autenticazione MFA. In questi casi gli account del servizio possono essere esclusi da un criterio mediante le impostazione di gestione dei criteri di accesso condizionale. Per altre informazioni sull'applicazione di un criterio agli utenti, vedere qui.

---

## <a name="are-graph-apis-available-to-configure-configure-conditional-access-policies"></a>Sono disponibili API Graph per configurare i criteri di accesso condizionale?
**A:** per il momento no. 

---

---
title: Limitazioni della collaborazione B2B-Azure Active Directory | Microsoft Docs
description: Limitazioni correnti di Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4f960819aa208dcc8d3e476fc45a766452b612c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168951"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitazioni di Collaborazione B2B di Azure AD
Collaborazione B2B di Azure Active Directory (Azure AD) è soggetta alle limitazioni descritte in questo articolo.

## <a name="possible-double-multi-factor-authentication"></a>Possibile autenticazione a più fattori doppia
Con B2B di Azure AD è possibile imporre l'autenticazione a più fattori dell'organizzazione delle risorse (l'organizzazione che invita). I motivi di questo approccio sono descritti in dettaglio in [accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md). Se un partner ha già configurato e applicato l'autenticazione a più fattori, è possibile che i rispettivi utenti debbano eseguire l'autenticazione una volta nella propria organizzazione e di nuovo nell'organizzazione di destinazione.

## <a name="instant-on"></a>Immediatezza
Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura vengono eseguite generalmente in un'istanza della directory e devono essere replicate in tutte le istanze. La replica viene completata quando tutte le istanze sono state aggiornate. In alcuni casi, quando un oggetto viene scritto o aggiornato in un'istanza e la chiamata per il recupero dell'oggetto viene effettuata a un'altra istanza, è possibile che si verifichino latenze della replica. In tal caso, aggiornare o riprovare. Se si sta scrivendo un'app usando l'API, è consigliabile riprovare, interrompendo temporaneamente, per risolvere il problema.

## <a name="azure-ad-directories"></a>Directory di Azure AD
Azure AD B2B è soggetta ai limiti del servizio directory di Azure AD. Per informazioni dettagliate sul numero di directory che un utente può creare e il numero di directory a cui un utente o un utente guest può appartenere, vedere [Restrizioni e limiti del servizio Azure AD](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="national-clouds"></a>Cloud nazionali
I [cloud nazionali](../develop/authentication-national-cloud.md) sono istanze fisicamente isolate di Azure. La collaborazione B2B non è supportata nei limiti del cloud nazionale. Ad esempio, se il tenant di Azure si trova in un cloud globale pubblico, non è possibile invitare un utente il cui account si trova in un cloud nazionale. Per collaborare con l'utente, richiedere un altro indirizzo di posta elettronica o creare un account utente membro nella directory.

## <a name="azure-us-government-clouds"></a>Cloud del governo degli Stati Uniti di Azure
Nel cloud del governo degli Stati Uniti di Azure è supportata la collaborazione B2B tra i tenant che si trovano all'interno del cloud di Azure per il governo degli Stati Uniti e che supportano la collaborazione B2B. I tenant di Azure per enti pubblici statunitensi che supportano la collaborazione B2B possono anche collaborare con gli utenti di Social Networking usando gli account Microsoft o Google. Se si invita un utente al di fuori di questi gruppi, ad esempio se l'utente si trova in un tenant che non fa parte del cloud del governo degli Stati Uniti di Azure o non supporta ancora la collaborazione B2B, l'invito non riuscirà o l'utente non sarà in grado di riscattare l'invito. Per informazioni dettagliate su altre limitazioni, vedere [Azure Active Directory Premium le varianti P1 e P2](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure per il governo degli Stati Uniti?
Per sapere se il tenant cloud del governo degli Stati Uniti di Azure supporta la collaborazione B2B, seguire questa procedura:

1. In un browser passare all'URL seguente, sostituendo il nome del tenant per *&lt; TenantName &gt;*:

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Trovare `"tenant_region_scope"` nella risposta JSON:

   - Se `"tenant_region_scope":"USGOV”` viene visualizzato, B2B è supportato.
   - Se `"tenant_region_scope":"USG"` viene visualizzato, B2B non è supportato.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Delegare gli inviti a Collaborazione B2B](delegate-invitations.md)
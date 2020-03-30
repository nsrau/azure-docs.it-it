---
title: Limitazioni della collaborazione B2B - Azure Active Directory Documenti Microsoft
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
ms.openlocfilehash: ffee01488ecf658ce02a20a41252aca19288667c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263361"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitazioni di Collaborazione B2B di Azure AD
Collaborazione B2B di Azure Active Directory (Azure AD) è soggetta alle limitazioni descritte in questo articolo.

## <a name="possible-double-multi-factor-authentication"></a>Possibile autenticazione a più fattori doppia
Con B2B di Azure AD è possibile imporre l'autenticazione a più fattori dell'organizzazione delle risorse (l'organizzazione che invita). I motivi di questo approccio sono descritti in dettaglio in [Accesso condizionale per gli utenti di Collaborazione B2B.](conditional-access.md) Se un partner ha già configurato e applicato l'autenticazione a più fattori, è possibile che i rispettivi utenti debbano eseguire l'autenticazione una volta nella propria organizzazione e di nuovo nell'organizzazione di destinazione.

## <a name="instant-on"></a>Immediatezza
Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura vengono eseguite generalmente in un'istanza della directory e devono essere replicate in tutte le istanze. La replica viene completata quando tutte le istanze sono state aggiornate. In alcuni casi, quando un oggetto viene scritto o aggiornato in un'istanza e la chiamata per il recupero dell'oggetto viene effettuata a un'altra istanza, è possibile che si verifichino latenze della replica. In tal caso, aggiornare o riprovare. Se si sta scrivendo un'app usando l'API, è consigliabile riprovare, interrompendo temporaneamente, per risolvere il problema.

## <a name="azure-ad-directories"></a>Directory di Azure AD
Azure AD B2B è soggetta ai limiti del servizio directory di Azure AD. Per informazioni dettagliate sul numero di directory che un utente può creare e il numero di directory a cui un utente o un utente guest può appartenere, vedere [Restrizioni e limiti del servizio Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Nubi nazionali
Le nuvole nazionali sono istanze fisicamente isolate di [Azure.National clouds](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) are physically isolated instances of Azure. La collaborazione B2B non è supportata oltre i limiti del cloud nazionale. Ad esempio, se il tenant di Azure si trova nel cloud globale pubblico, non è possibile invitare un utente il cui account si trova in un cloud nazionale. Per collaborare con l'utente, chiedere loro un altro indirizzo di posta elettronica o creare un account utente membro nella directory.

## <a name="azure-us-government-clouds"></a>Cloud di Azure per enti pubblici degli Stati UnitiAzure US Government clouds
All'interno del cloud di Azure per enti pubblici degli Stati Uniti, la collaborazione B2B è attualmente supportata solo tra tenant che si trovano entrambi all'interno del cloud di Azure per enti pubblici degli Stati Uniti e che supportano entrambi la collaborazione B2B. Se si invita un utente in un tenant che non fa parte del cloud di Azure per enti pubblici o che non supporta ancora la collaborazione B2B, l'invito avrà esito negativo o l'utente non sarà in grado di riscattare l'invito. Per informazioni dettagliate su altre limitazioni, vedere [Azure Active Directory Premium P1 e P2 Variations.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2)

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>Come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure per enti pubblici degli Stati Uniti?
Per scoprire se il tenant cloud di Azure US Government supporta la collaborazione B2B, eseguire le operazioni seguenti:To find out if your Azure US Government cloud tenant supports B2B collaboration, do the following:

1. In un browser passare all'URL seguente, sostituendo il nome del tenant con * &lt;nometenant:&gt;*

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. Trovare `"tenant_region_scope"` nella risposta JSON:Find in the JSON response:

   - Se `"tenant_region_scope":"USGOV”` visualizzato, B2B è supportato.
   - Se `"tenant_region_scope":"USG"` visualizzato, B2B non è supportato.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Delegare gli inviti a Collaborazione B2B](delegate-invitations.md)


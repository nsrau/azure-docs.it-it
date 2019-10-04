---
title: Limitazioni di collaborazione B2B - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: b45277c89193c51f70836bcef8a21636fc9c7973
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113116"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitazioni di Collaborazione B2B di Azure AD
Collaborazione B2B di Azure Active Directory (Azure AD) è soggetta alle limitazioni descritte in questo articolo.

## <a name="possible-double-multi-factor-authentication"></a>Possibile autenticazione a più fattori doppia
Con B2B di Azure AD è possibile imporre l'autenticazione a più fattori dell'organizzazione delle risorse (l'organizzazione che invita). I motivi per questo approccio sono descritti in dettaglio [accesso condizionale per gli utenti di collaborazione B2B](conditional-access.md). Se un partner ha già configurato e applicato l'autenticazione a più fattori, è possibile che i rispettivi utenti debbano eseguire l'autenticazione una volta nella propria organizzazione e di nuovo nell'organizzazione di destinazione.

## <a name="instant-on"></a>Immediatezza
Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura vengono eseguite generalmente in un'istanza della directory e devono essere replicate in tutte le istanze. La replica viene completata quando tutte le istanze sono state aggiornate. In alcuni casi, quando un oggetto viene scritto o aggiornato in un'istanza e la chiamata per il recupero dell'oggetto viene effettuata a un'altra istanza, è possibile che si verifichino latenze della replica. In tal caso, aggiornare o riprovare. Se si sta scrivendo un'app usando l'API, è consigliabile riprovare, interrompendo temporaneamente, per risolvere il problema.

## <a name="azure-ad-directories"></a>Directory di Azure AD
Azure AD B2B è soggetta ai limiti del servizio directory di Azure AD. Per informazioni dettagliate sul numero di directory che un utente può creare e il numero di directory a cui un utente o un utente guest può appartenere, vedere [Restrizioni e limiti del servizio Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="national-clouds"></a>Cloud nazionali
[I cloud nazionali](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) sono isolate fisicamente le istanze di Azure. Collaborazione B2B non è supportata nei limiti del cloud nazionale. Ad esempio, se il tenant di Azure si trova nel cloud pubblico e globale, è Impossibile invitare un utente il cui account si trova in un cloud nazionale. Per collaborare con l'utente, chiedere a un altro indirizzo di posta elettronica o creare un account utente membro relativa nella directory.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Delegare gli inviti a Collaborazione B2B](delegate-invitations.md)


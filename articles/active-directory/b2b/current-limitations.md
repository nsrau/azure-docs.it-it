---
title: Limitazioni di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Limitazioni correnti di Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 562076e9529ffeac4cb0f99c1ffd4d4866d0bd1a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267059"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitazioni di Collaborazione B2B di Azure AD
Collaborazione B2B di Azure Active Directory (Azure AD) è soggetta alle limitazioni descritte in questo articolo.

## <a name="possible-double-multi-factor-authentication"></a>Possibile autenticazione a più fattori doppia
Con B2B di Azure AD è possibile imporre l'autenticazione a più fattori dell'organizzazione delle risorse (l'organizzazione che invita). I motivi di questo approccio vengono descritti nel dettaglio in [Accesso condizionale per gli utenti di Collaborazione B2B](conditional-access.md). Se un partner ha già configurato e applicato l'autenticazione a più fattori, è possibile che i rispettivi utenti debbano eseguire l'autenticazione una volta nella propria organizzazione e di nuovo nell'organizzazione di destinazione.

## <a name="instant-on"></a>Immediatezza
Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura vengono eseguite generalmente in un'istanza della directory e devono essere replicate in tutte le istanze. La replica viene completata quando tutte le istanze sono state aggiornate. In alcuni casi, quando un oggetto viene scritto o aggiornato in un'istanza e la chiamata per il recupero dell'oggetto viene effettuata a un'altra istanza, è possibile che si verifichino latenze della replica. In tal caso, aggiornare o riprovare. Se si sta scrivendo un'app usando l'API, è consigliabile riprovare, interrompendo temporaneamente, per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Delegare gli inviti a Collaborazione B2B](delegate-invitations.md)


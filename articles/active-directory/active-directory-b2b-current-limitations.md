---
title: Limitazioni di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
description: Limitazioni correnti di Collaborazione B2B di Azure Active Directory
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0e71a840d4f503779131ee4a21fe6063d33185f1
ms.openlocfilehash: cbefca2d45a332cd57cfea49dfeaa300426d5502
ms.lasthandoff: 02/24/2017


---

# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Limitazioni di Collaborazione B2B di Azure AD
Collaborazione B2B di Azure Active Directory (Azure AD) è soggetta alle limitazioni descritte in questo articolo.

## <a name="invitation-apis-are-in-preview"></a>Le API di invito sono nella versione di anteprima.
La superficie dell'API è il nostro prossimo obiettivo. Tuttavia, come tutte le versioni non definitive, l'API è soggetta al contratto dello spazio dei nomi di anteprima. L'API verrà inserita in una versione numerata con la versione disponibile a livello generale.

## <a name="possible-double-multi-factor-authentication"></a>Possibile autenticazione Multi-Factor Authentication doppia
Questa ridondanza può verificarsi se il partner ha già applicato i criteri di Azure Multi-Factor Authentication. L'autenticazione Multi-Factor Authentication di Collaborazione B2B viene eseguita e gestita nell'organizzazione che ha inviato l'invito. Questo tipo di autenticazione è preferibile perché copre tutte le identità e consente il controllo sull'attendibilità dell'autenticazione degli invitati di Collaborazione B2B.

Se tuttavia un partner ha già configurato e applicato l'autenticazione Multi-Factor Authentication, è possibile che gli utenti del partner debbano eseguire l'autenticazione una volta nella propria organizzazione e di nuovo nell'organizzazione di destinazione.

Nelle versioni future verranno introdotti criteri con cui sarà possibile evitare l'autenticazione doppia scegliendo di considerare attendibile l'autenticazione Multi-Factor Authentication del partner.

## <a name="instant-on"></a>Immediatezza
Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura vengono eseguite generalmente in un'istanza della directory e devono essere replicate in tutte le istanze. È stato osservato che a causa del periodo di tempo limitato che può richiedere il completamento della replica, in alcuni casi possono verificarsi problemi di autorizzazione. L'obiettivo è ridurre o eliminare questi problemi prima della versione disponibile a livello generale. Nel frattempo questo problema non si dovrebbe verificare, ma, se così fosse, in genere è utile aggiornare o riprovare.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli altri articoli su Azure AD B2B Collaboration.

* [Che cos'è Azure AD B2B Collaboration?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Proprietà dell'utente di Collaborazione B2B](active-directory-b2b-user-properties.md)
* [Aggiunta di un utente di Collaborazione B2B a un ruolo](active-directory-b2b-add-guest-to-role.md)
* [Delegare gli inviti a Collaborazione B2B](active-directory-b2b-delegate-invitations.md)
* [Gruppi dinamici e Collaborazione B2B](active-directory-b2b-dynamic-groups.md)
* [Codici ed esempi di PowerShell per Collaborazione B2B](active-directory-b2b-code-samples.md)
* [Configurare app SaaS per Collaborazione B2B](active-directory-b2b-configure-saas-apps.md)
* [Token utente per Collaborazione B2B](active-directory-b2b-user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](active-directory-b2b-claims-mapping.md)
* [Condivisione esterna di Office 365](active-directory-b2b-o365-external-user.md)


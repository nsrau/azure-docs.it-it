---
title: Limitazioni correnti di Collaborazione B2B di Azure Active Directory | Documentazione Microsoft
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
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 4dae74740d8fd00d36492e5a9fad8848b5d03952
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-current-limitations"></a>Limitazioni correnti di Collaborazione B2B di Azure Active Directory

1. Le API di invito di Collaborazione B2B di Azure Active Directory (Azure AD) sono in versione beta testing. La superficie API è l'obiettivo, ma come tutte le versioni beta, è soggetta al contratto per lo spazio dei nomi beta. L'API verrà inserita in una versione numerata con la versione disponibile a livello generale.
2. Esiste la possibilità di autenticazione a più fattori doppia se il partner ha già adottato criteri di autenticazione a più fattori. L'autenticazione a più fattori di Collaborazione B2B verrà eseguita e gestita nell'organizzazione che invia l'invito. Ciò è preferibile perché considera tutte le identità e consente di avere il controllo sull'attendibilità dell'autenticazione degli invitati di Collaborazione B2B. Se tuttavia un partner ha già configurato l'autenticazione a più fattori e la applica, gli utenti dovranno eseguire l'autenticazione a più fattori nella propria organizzazione e di nuovo nell'organizzazione di destinazione. Nelle versioni future verranno introdotti criteri in cui sarà possibile scegliere di considerare attendibile l'autenticazione a più fattori di determinati partner per evitare il problema dell'autenticazione a più fattori doppia.
3. Nei flussi di Collaborazione B2B gli utenti vengono aggiunti alla directory e aggiornati in modo dinamico durante il riscatto dell'invito, l'assegnazione di app e così via. Le operazioni di aggiornamento e scrittura in genere vengono eseguite in un'istanza della directory e devono essere replicate in tutte le istanze. È stato osservato che in alcuni casi il periodo di tempo limitato che può richiedere il completamento della replica può causare problematiche che si rivelano problemi di autorizzazione. Questa serie di problemi verrà ridotta/eliminata prima della disponibilità generale. Nel frattempo, questo problema non si dovrebbe verificare, ma, se così fosse, in genere è utile aggiornare o riprovare.

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


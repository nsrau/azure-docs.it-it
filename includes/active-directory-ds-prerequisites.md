---
title: File di inclusione
description: File di inclusione con prerequisiti
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: 1fba8cc9ae40cf5539016bbd73de65f557a64136
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359373"
---
> [!IMPORTANT]
> **Abilitare la sincronizzazione dell'hash password ad Azure Active Directory Domain Services, prima di aver completato le attività in questo articolo.**
>
> Seguire le istruzioni seguenti, a seconda del tipo di utenti in Azure Active Directory. Se in Azure Active Directory è presente una combinazione di account utente solo cloud e sincronizzati, completare entrambi i set di istruzioni. Potrebbe non essere in grado di eseguire le operazioni seguenti nel caso in cui si sta tentando di usare un account Guest B2B (esempio di gmail o account del servizio gestito da un provider di identità diversi che è consentito) perché non è disponibile la password per gli utenti sincronizzati con il dominio gestito come queste sono gli account guest nella directory. Le informazioni complete su tali account tra cui la password sarebbe di fuori di Azure AD e come queste informazioni non sono in Azure AD di conseguenza non anche ottenere sincronizzati con il dominio gestito. 
> - [Istruzioni per account utente solo cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Istruzioni per account utente sincronizzati da una directory locale](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)

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
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426907"
---
> [!IMPORTANT]
> **Abilitare la sincronizzazione dell'hash password ad Azure Active Directory Domain Services, prima di aver completato le attività in questo articolo.**
>
> Seguire le istruzioni seguenti, a seconda del tipo di utenti in Azure Active Directory. Se in Azure Active Directory è presente una combinazione di account utente solo cloud e sincronizzati, completare entrambi i set di istruzioni. Potrebbe non essere possibile eseguire le operazioni seguenti se si sta provando a usare un account Guest B2B (ad esempio, Gmail o MSA da un provider di identità diverso) perché la password per questi utenti non è stata sincronizzata con il dominio gestito poiché si tratta di account guest nella directory. Le informazioni complete su questi account, incluse le relative password, non rientrano nell'Azure AD e, poiché queste informazioni non sono presenti in Azure AD, di conseguenza non vengono sincronizzate con il dominio gestito. 
> - [Istruzioni per account utente solo cloud](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Istruzioni per account utente sincronizzati da una directory locale](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)

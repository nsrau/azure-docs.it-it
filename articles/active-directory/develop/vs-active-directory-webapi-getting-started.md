---
title: Introduzione AD Azure nei progetti di Visual Studio WebApi | Microsoft Docs
description: Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio
services: active-directory
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: f44c60cc02ef93fb8bd08954d8b059889eb82df1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Introduzione ad Azure Active Directory (progetti WebAPI)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-active-directory-webapi-getting-started.md)
> - [Risultati](vs-active-directory-webapi-what-happened.md)

Questo articolo fornisce istruzioni aggiuntive da seguire dopo l'aggiunta di Active Directory a un progetto WebAPI ASP.NET tramite il comando **Progetto > Servizi connessi** di Visual Studio. Se il servizio non è ancora stato aggiunto al progetto, è possibile farlo in qualsiasi momento.

Vedere [Modifiche apportate al progetto WebAPI](vs-active-directory-webapi-what-happened.md) per informazioni sulle modifiche apportate al progetto quando si aggiunge il servizio connesso.

## <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso

Tutti i controller del progetto sono dotati dell'attributo `[Authorize]`. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](active-directory-authentication-scenarios.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)

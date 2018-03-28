---
title: Introduzione AD Azure nei progetti di Visual Studio WebApi | Microsoft Docs
description: Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio
services: active-directory
documentationcenter: ''
author: kraigb
manager: ghogen
editor: ''
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: a5953a8ae4ef3fc221c984cc7520422251ef2c82
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
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

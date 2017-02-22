---
title: Introduzione AD Azure nei progetti di Visual Studio WebApi | Microsoft Docs
description: Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f3f8292eb505c73b5fda86499581fe85ad3f8e47
ms.openlocfilehash: b42bd57c8a7dde854208c65f4477327fbf1108a4


---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Introduzione ad Azure Active Directory e ai servizi relativi a Visual Studio (progetti WebApi)
> [!div class="op_single_selector"]
> * [Per iniziare](vs-active-directory-webapi-getting-started.md)
> * [Risultati](vs-active-directory-webapi-what-happened.md)
> 
> 

### <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso
Tutti i controller del progetto sono dotati dell'attributo **Authorize** . Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)




<!--HONumber=Jan17_HO5-->



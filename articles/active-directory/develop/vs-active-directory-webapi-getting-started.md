---
title: Introduzione AD Azure nei progetti di Visual Studio WebApi | Microsoft Docs
description: Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio
services: active-directory
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/19/2017
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: a756316054dd3bb63f3b0a9f59621bb1345bc693
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="get-started-with-azure-active-directory-and-visual-studio-connected-services-webapi-projects"></a>Introduzione ad Azure Active Directory e ai servizi relativi a Visual Studio (progetti WebApi)
> [!div class="op_single_selector"]
> * [Per iniziare](vs-active-directory-webapi-getting-started.md)
> * [Risultati](vs-active-directory-webapi-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso
Tutti i controller del progetto sono dotati dell'attributo **Authorize** . Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni su Azure Active Directory](https://azure.microsoft.com/services/active-directory/)


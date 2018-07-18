---
title: Come concedere autorizzazioni a un'applicazione personalizzata | Microsoft Docs
description: Come concedere autorizzazioni all'applicazione personalizzata tramite il portale di Azure AD o un parametro URL
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 3310a08047700a577c5c6cbada90e575fcd12089
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333705"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Come concedere autorizzazioni a un'applicazione personalizzata

Se si vuole concedere in modo preventivo il consenso per l'app in uso o si verifica un errore che indica che non è stato fornito il consenso a un'app, seguire questa procedura.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Come fornire il consenso dell'amministratore per l'applicazione

Questa operazione consente di fornire all'applicazione il consenso per tutti gli utenti dell'organizzazione.

1. Passare al pannello **Registrazioni per l'app** in qualità di **amministratore globale** e quindi selezionare l'app.

2. Selezionare **Autorizzazioni necessarie** e infine scegliere il pulsante **Concedi autorizzazioni** nella parte superiore del pannello.

È possibile, in alternativa, creare una richiesta in *login.microsoftonline.com* con le configurazioni dell'app e aggiungerla in *&prompt=admin\_consent*. Dopo avere eseguito l'accesso con le credenziali di amministratore, all'app viene concesso il consenso per tutti gli utenti.

## <a name="how-to-force-user-consent-for-your-application"></a>Come forzare il consenso dell'utente per l'applicazione

* Eseguire l'aggiunta alle richieste di autenticazione *&prompt=consent* che richiedono il consenso degli utenti finali a ogni autenticazione.

## <a name="next-steps"></a>Passaggi successivi

[Consenso e integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Consenso e concessione delle autorizzazioni per le app con convergenza di Azure Active Directory v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD in Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)

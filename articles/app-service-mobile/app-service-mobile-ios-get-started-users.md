---
title: Aggiungere l&quot;autenticazione in iOS con le app per dispositivi mobili di Azure
description: "Informazioni su come usare le app per dispositivi mobili di Azure per autenticare gli utenti dell&quot;app iOS tramite vari provider di identità, tra cui AAD, Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>Aggiungere l'autenticazione all'app iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Questa esercitazione illustra come aggiungere l'autenticazione al progetto di [avvio rapido di iOS] tramite un provider di identità supportato. Questa esercitazione è basata sull'esercitazione relativa all' [avvio rapido di iOS] , che deve essere completata per prima.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrare l'app per l'autenticazione e configurare il servizio app
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Limitare le autorizzazioni agli utenti autenticati
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

In Xcode fare clic su **Run** (Esegui) per avviare l'app. Viene generata un'eccezione non gestita perché l'app prova ad accedere al back-end come utente non autenticato, mentre ora la tabella *TodoItem* richiede l'autenticazione.

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>Aggiungere l'autenticazione all'app
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[avvio rapido di iOS]: app-service-mobile-ios-get-started.md

[Portale di Azure]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->



---
title: Registrazione dell&quot;app v 2.0 | Microsoft Docs
description: Come registrare un&quot;app in Microsoft per abilitare l&quot;accesso ai servizi Microsoft tramite l&quot;endpoint v2.0
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: 0ca01c2ef66706502e19e5e30f878a509356c94c


---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Come registrare un'app con l'endpoint v2.0
Per creare un'app che consente di accedere tramite account Microsoft e Azure AD, è innanzitutto necessario registrare un'app con Microsoft.  Con gli account Azure AD o Microsoft non sarà possibile usare un'app esistente, ma è necessario crearne una nuova.

> [!NOTE]
> Non tutti gli scenari e le funzionalità di Azure Active Directory sono supportati dall'endpoint 2.0.  Per determinare se è necessario usare l'endpoint v2.0, leggere le informazioni sulle [limitazioni v2.0](active-directory-v2-limitations.md).
> 
> 

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitare il portale di registrazione delle app Microsoft
Per prima cosa, passare a [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Il nuovo portale di registrazione delle app consente di gestire le app Microsoft.

Accedere con un account Microsoft personale, aziendale o dell'istituto di istruzione.  Se non si dispone di alcun account, iscriversi per creare un nuovo account personale. La procedura richiederà pochi minuti.

Dopo aver completato l'operazione, è possibile esaminare l'elenco delle app Microsoft, che probabilmente sarà vuoto.  A tale scopo, seguire questa procedura.

Fare clic sul pulsante per **aggiungere un'app**e attribuirle un nome.  Il portale assegnerà all'app un ID applicazione univoco globale che verrà usato successivamente nel codice.  Se l’app include un componente sul lato server che necessità di token di accesso per chiamare le API (ad esempio, Office, Azure o un’API Web), è opportuno creare anche un **segreto dell’applicazione** .
<!-- TODO: Link for app secrets -->

Successivamente, aggiungere le piattaforme usate dall'app.

* Per le applicazioni basate sul Web, specificare un **URI di reindirizzamento** dove possono essere inviati messaggi di accesso.
* Per le app per dispositivi mobili, copiare l'URI di reindirizzamento predefinito creato automaticamente.

Facoltativamente, è possibile personalizzare l'aspetto della pagina di accesso nella sezione del profilo.  Assicurarsi di **salvare** prima di procedere.

> [!NOTE]
> Quando si crea un'applicazione usando [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l'applicazione verrà registrata nel tenant home dell'account usato per accedere al portale.  Ciò significa che non è possibile registrare un'applicazione nel tenant di Azure AD usando un account Microsoft personale.  Se si desidera registrare  in modo esplicito un'applicazione in un particolare tenant, accedere con un account creato nel tenant.
> 
> 

## <a name="build-a-quick-start-app"></a>Creare un'app di avvio rapido
Dopo aver creato un'app Microsoft, è possibile completare una delle esercitazioni rapide v2.0.  Di seguito sono elencati alcuni suggerimenti:

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]




<!--HONumber=Jan17_HO3-->



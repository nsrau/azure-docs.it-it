---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 2cb4895fc2f884d6da41b55faa91fbcb9e88f52f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978704"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Chiamare l'API Microsoft Graph da un'applicazione a singola pagina (SPA) di JavaScript

Questa guida dimostra come un'applicazione a pagina singola JavaScript possa accedere ad account personali, aziendali e dell'istituto di istruzione, ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.0.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Funzionamento dell'app di esempio generata da questa guida](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Altre informazioni

L'applicazione di esempio creata in questa guida consente a una SPA di Javascript di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2.0. Per questo scenario, dopo l'accesso di un utente, viene richiesto e aggiunto un token di accesso a richieste HTTP tramite l'intestazione dell'autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Authentication Library di Microsoft per l'anteprima di JavaScript|

> [!NOTE]
> *msal.js* specifica come destinazione l'*endpoint di Azure Active Directory v2.0*, che consente agli account personali, aziendali e dell'istituto di istruzione di eseguire l'accesso e di acquisire i token. L'*endpoint di Azure Active Directory v2.0* ha [alcune limitazioni](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Per conoscere le differenze tra gli endpoint v1.0 e v2.0, vedere la [guida al confronto tra gli endpoint](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

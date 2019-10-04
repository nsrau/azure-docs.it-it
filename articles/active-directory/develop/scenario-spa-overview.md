---
title: Panoramica dello scenario di applicazione a pagina singola JavaScript-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (panoramica dello scenario) che integra la piattaforma di identità Microsoft.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852508"
---
# <a name="scenario-single-page-application"></a>Scenario: Applicazione a pagina singola

Scopri tutto quello che ti serve per creare un'applicazione a singola pagina (SPA).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

È possibile creare la prima applicazione seguendo la Guida introduttiva a JavaScript SPA:

> [!div class="nextstepaction"]
> [Avvio rapido: Applicazione a pagina singola](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni Web moderne vengono create come applicazioni a pagina singola sul lato client scritte usando JavaScript o un apposito framework come Angular, Vue.js e React.js. Queste applicazioni vengono eseguite in un Web browser e presentano caratteristiche di autenticazione diverse rispetto alle applicazioni Web tradizionali sul lato server. La piattaforma Microsoft Identity consente alle applicazioni a singola pagina di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web usando il [flusso implicito OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Il flusso implicito consente all'applicazione di ottenere i token ID per rappresentare l'utente autenticato e anche i token di accesso necessari per chiamare le API protette.

![Applicazioni a pagina singola](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, ad esempio elettrone, React-native e così via. Poiché richiedono ulteriori funzionalità per l'interazione con le piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione, sono necessari gli aspetti seguenti:

* La registrazione dell'applicazione con Azure AD implica l'abilitazione del flusso implicito e l'impostazione di un URI di reindirizzamento a cui vengono restituiti i token.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrate, ad esempio l'ID applicazione.
* Uso della libreria MSAL per eseguire il flusso di autenticazione per l'accesso e l'acquisizione di token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-spa-app-registration.md)

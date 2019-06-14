---
title: Panoramica dello scenario di applicazione a singola pagina JavaScript - piattaforma delle identità Microsoft
description: Informazioni su come compilare un'applicazione a pagina singola (panoramica dello scenario) che si integra piattaforma delle identità Microsoft.
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076361"
---
# <a name="scenario-single-page-application"></a>Scenario: Applicazione a pagina singola

Scopri tutto quello che dovete compilare un'applicazione a pagina singola (SPA).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

È possibile creare la prima applicazione seguendo la Guida introduttiva JavaScript SPA:

> [!div class="nextstepaction"]
> [Avvio rapido: Applicazione a singola pagina](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni web moderne vengono compilate come applicazioni a pagina singola lato client scritte in JavaScript o un framework di applicazione a singola pagina, ad esempio VUE, Angular e React. js. Queste applicazioni eseguite in un web browser e dispongono di caratteristiche di autenticazione diverso rispetto alle applicazioni web tradizionali sul lato server. La piattaforma delle identità Microsoft consente alle applicazioni a pagina singola di accesso degli utenti e ottenere i token per accedere a servizi back-end o API web usando il [flusso implicito OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Il flusso implicito consente all'applicazione ottenere i token ID per rappresentare l'utente autenticato e anche i token necessari per chiamare le API protette di accesso.

![Applicazioni a pagina singola](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non sono inclusi gli scenari di applicazione usando i framework JavaScript più piattaforme, ad esempio Electron, React-Native e così via. poiché richiedono altre funzionalità per l'interazione con piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione sono necessari i seguenti aspetti:

* Registrazione dell'applicazione con Azure AD comporta abilitare il flusso implicito e impostazione di un URI di reindirizzamento a cui vengono restituiti i token.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrato, ad esempio l'ID applicazione.
* Utilizzo libreria MSAL per eseguire il flusso di autenticazione per accedere e acquisire i token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-spa-app-registration.md)

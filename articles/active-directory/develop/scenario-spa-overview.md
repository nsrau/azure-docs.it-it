---
title: Scenario di app a pagina singola JavaScript - Piattaforma di identità Microsoft Azure
description: Informazioni su come creare un'applicazione a pagina singola (panoramica dello scenario) usando la piattaforma di identità Microsoft.Learn how to build a single-page application (scenario overview) by using the Microsoft identity platform.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885175"
---
# <a name="scenario-single-page-application"></a>Scenario: applicazione a pagina singolaScenario: Single-page application

Scopri tutto ciò che ti serve per creare un'applicazione a pagina singola (SPA).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Introduzione

È possibile creare la prima applicazione seguendo la guida introduttiva di JavaScript SPA:

> [!div class="nextstepaction"]
> [Guida introduttiva: Applicazione a pagina singola](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni Web moderne vengono create come applicazioni a pagina singola sul lato client. Gli sviluppatori li scrivono utilizzando JavaScript o un framework SPA come Angular, Vue.js e React.js. Queste applicazioni vengono eseguite in un Web browser e hanno caratteristiche di autenticazione diverse rispetto alle tradizionali applicazioni Web lato server. 

La piattaforma di identità Microsoft consente alle applicazioni a pagina singola di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web utilizzando il [flusso implicito OAuth 2.0.](./v2-oauth2-implicit-grant-flow.md) Il flusso implicito consente all'applicazione di ottenere token ID per rappresentare l'utente autenticato e anche di accedere ai token necessari per chiamare LE API protette.

![Applicazioni a pagina singola](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazione che utilizzano framework JavaScript multipiattaforma, ad esempio Electron e React-Native.This authentication flow does not include application scenarios that use cross-platform JavaScript frameworks such as Electron and React-Native. Sono necessarie ulteriori funzionalità per l'interazione con le piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione, è necessario:To enable this scenario for your application, you need:

* Registrazione dell'applicazione con Azure Active Directory (Azure AD). Questa registrazione comporta l'abilitazione del flusso implicito e l'impostazione di un URI di reindirizzamento a cui vengono restituiti i token.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrate, ad esempio l'ID applicazione.
* Utilizzo di Microsoft Authentication Library (MSAL) per eseguire il flusso di autenticazione per accedere e acquisire token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-spa-app-registration.md)

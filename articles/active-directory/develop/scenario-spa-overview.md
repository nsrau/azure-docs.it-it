---
title: Panoramica dello scenario di applicazione a pagina singola JavaScript-piattaforma di identità Microsoft
description: Informazioni su come creare un'applicazione a singola pagina (panoramica dello scenario) usando la piattaforma di identità Microsoft.
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
ms.openlocfilehash: 77f59e8c628d7ba37aaf258541664e40e1d4a2dc
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74764679"
---
# <a name="scenario-single-page-application"></a>Scenario: applicazione a pagina singola

Scopri tutto quello che ti serve per creare un'applicazione a singola pagina (SPA).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Inizia ora

È possibile creare la prima applicazione seguendo la Guida introduttiva a JavaScript SPA:

> [!div class="nextstepaction"]
> [Guida introduttiva: applicazione a pagina singola](./quickstart-v2-javascript.md)

## <a name="overview"></a>Panoramica

Molte applicazioni Web moderne sono compilate come applicazioni a singola pagina sul lato client. Gli sviluppatori li scrivono usando JavaScript o un Framework di applicazione a singola pagina, ad esempio angolare, VME. js e React. js. Queste applicazioni vengono eseguite in un browser Web e presentano caratteristiche di autenticazione diverse rispetto alle applicazioni Web tradizionali sul lato server. 

La piattaforma Microsoft Identity consente alle applicazioni a singola pagina di accedere agli utenti e ottenere i token per accedere ai servizi back-end o alle API Web usando il [flusso implicito OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Il flusso implicito consente all'applicazione di ottenere i token ID per rappresentare l'utente autenticato e anche i token di accesso necessari per chiamare le API protette.

![Applicazioni a pagina singola](./media/scenarios/spa-app.svg)

Questo flusso di autenticazione non include scenari di applicazioni che usano framework JavaScript multipiattaforma, ad esempio Electron e React-native. Sono necessarie altre funzionalità per l'interazione con le piattaforme native.

## <a name="specifics"></a>Specifiche

Per abilitare questo scenario per l'applicazione, è necessario:

* Registrazione dell'applicazione con Azure Active Directory (Azure AD). Questa registrazione comporta l'abilitazione del flusso implicito e l'impostazione di un URI di reindirizzamento a cui vengono restituiti i token.
* Configurazione dell'applicazione con le proprietà dell'applicazione registrate, ad esempio l'ID applicazione.
* Usare Microsoft Authentication Library (MSAL) per eseguire il flusso di autenticazione per l'accesso e l'acquisizione di token.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione delle app](scenario-spa-app-registration.md)

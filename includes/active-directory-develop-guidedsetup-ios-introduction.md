---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 971ae8cd44f1b345d3a71b8fa4f256c8f25ef961
ms.sourcegitcommit: 807c318f5c034f8256f91c241e9d6f8f4d7de90a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64951334"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Chiamare l'API Microsoft Graph da un'applicazione iOS

Questa guida illustra in che modo un'applicazione iOS nativa (Swift) può chiamare API che richiedono token di accesso dall'endpoint Microsoft Identity Platform. La guida spiega come ottenere i token di accesso e usarli nelle chiamate all'API Microsoft Graph e ad altre API.

Dopo aver completato gli esercizi in questa guida, l'applicazione può chiamare un'API protetta da qualsiasi società o organizzazione che usa Azure AD. L'applicazione può eseguire chiamate alle API protette tramite account personali, come outlook.com, live.com e altri, oltre che tramite account aziendali o dell'istituto di istruzione.

## <a name="prerequisites"></a>Prerequisiti

- Per l'esempio creato in questa guida, è necessario XCode versione 10.x. È possibile scaricare XCode dal [sito Web iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di download di XCode").
- Per la gestione dei pacchetti, è necessario il gestore delle dipendenze [Carthage](https://github.com/Carthage/Carthage).

## <a name="how-this-guide-works"></a>Come interpretare questa guida

![Illustra come funziona l'app di esempio generata da queste esercitazioni](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

In questa guida l'applicazione di esempio consente a un'applicazione iOS di eseguire query sull'API Microsoft Graph o su un'API Web che accetta token dall'endpoint Microsoft Identity Platform. Per questo scenario, viene aggiunto un token alle richieste HTTP usando**l'intestazione dell'autorizzazione**. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Gestire l'acquisizione di token per l'accesso alle API Web protette

Dopo l'autenticazione dell'utente, l'applicazione di esempio riceve un token. Il token viene usato per eseguire query sull'API Microsoft Graph o su un'API Web protetta dall'endpoint Microsoft Identity Platform.

Le API come Microsoft Graph richiedono un token di accesso per consentire l'accesso a risorse specifiche. I token sono necessari per leggere il profilo di un utente, accedere al calendario di un utente, inviare posta elettronica e così via. L'applicazione può richiedere un token di accesso usando MSAL e specificando gli ambiti API. Il token di accesso viene aggiunto all'intestazione **dell'autorizzazione**HTTP per ogni chiamata effettuata per la risorsa protetta.

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti dalla libreria MSAL e non devono quindi essere effettuati dall'applicazione.

## <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Anteprima di Microsoft Authentication Library per iOS|

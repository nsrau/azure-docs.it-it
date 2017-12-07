---
title: iOS per Azure AD v2 - Introduzione | Microsoft Docs
description: "Informazioni sulle modalità per le applicazioni iOS (Swift) per chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.openlocfilehash: 5fcd78eaa0c5b09b70aa973466a34556fff56071
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="call-the-microsoft-graph-api-from-an-ios-app"></a>Chiamare l'API Microsoft Graph da un'app iOS

Questa guida dimostra come un'applicazione iOS nativa (Swift) possa ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

Al termine di questa guida, l'applicazione sarà in grado di chiamare un'API protetta usando sia account personali (ad esempio, outlook.com, live.com e altri) sia account aziendali o di istituti di istruzione di proprietà di aziende o organizzazioni con Azure Active Directory.

> ### <a name="pre-requisites"></a>Prerequisiti
> - XCode 8.x è obbligatorio per questa guida. È possibile scaricare XCode [da qui](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "URL di Download di XCode").
> - [Carthage](https://github.com/Carthage/Carthage) per la gestione pacchetti

### <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](media/active-directory-mobileanddesktopapp-ios-introduction/iosintro.png)

L'applicazione di esempio creata in questa guida consente a un'applicazione iOS di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo che l'utente ha eseguito l'autenticazione, l'applicazione di esempio riceve un token che può essere usato per eseguire query nell'API Microsoft Graph o in un'API Web protetta da Microsoft Azure Active Directory v2.

API come Microsoft Graph richiedono un token di accesso per consentire l'accesso a risorse specifiche, ad esempio per leggere un profilo utente, accedere al calendario dell'utente o inviare un messaggio di posta elettronica. L'applicazione può richiedere un token di accesso tramite la libreria MSAL specificando gli ambiti API. Il token di accesso ottenuto viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata alla risorsa protetta.

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti dalla libreria MSAL e non devono quindi essere effettuati dall'applicazione.


### <a name="libraries"></a>Librerie

Questa guida usa la libreria seguente:

|Libreria|Descrizione|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Anteprima di Microsoft Authentication Library per iOS|


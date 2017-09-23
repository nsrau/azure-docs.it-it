---
title: Introduzione a Windows Desktop per Azure AD v2 - Intro | Microsoft Docs
description: Come applicazioni .NET per Windows Desktop (XAML) possono chiamare un'API che richiede token di accesso dall'endpoint di Azure Active Directory v2
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 4a695c00fce4deb02261ba58ec95469746bb1486
ms.contentlocale: it-it

---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chiamare l'API Microsoft Graph da un'app Windows Desktop

Questa guida dimostra come un'applicazione .NET per Windows Desktop (XAML) nativa può ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

Al termine di questa guida, l'applicazione sarà in grado di chiamare un'API protetta usando sia account personali (ad esempio, outlook.com, live.com e altri) sia account aziendali o di istituti di istruzione di proprietà di aziende o organizzazioni con Azure Active Directory.  

> Questa guida richiede Visual Studio 2015 Update 3 o Visual Studio 2017.  Se non lo si ha, è possibile [scaricare Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/)

### <a name="how-this-guide-works"></a>Come interpretare questa guida

![Come interpretare questa guida](media/active-directory-mobileanddesktopapp-windowsdesktop-intro/windesktophowitworks.png)

L'applicazione di esempio creata in questa guida consente a un'applicazione per Windows Desktop di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).


### <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo che l'utente ha eseguito l'autenticazione, l'applicazione di esempio riceve un token che può essere usato per eseguire query nell'API Microsoft Graph o in un'API Web protetta da Microsoft Azure Active Directory v2.

API come Microsoft Graph richiedono un token di accesso per consentire l'accesso a risorse specifiche, ad esempio per leggere un profilo utente, accedere al calendario dell'utente o inviare un messaggio di posta elettronica. L'applicazione può richiedere un token di accesso usando la libreria MSAL per accedere alle risorse tramite la definizione di ambiti API. Il token di accesso ottenuto viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata alla risorsa protetta. 

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti dalla libreria MSAL e non devono quindi essere effettuati dall'applicazione.


### <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|Descrizione|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|



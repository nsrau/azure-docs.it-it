---
title: Introduzione ad Android per Azure AD v2 - Intro | Microsoft Docs
description: "Informazioni sul modo in cui un'app di Android può ottenere un token di accesso e chiamare l'API o le API Graph Microsoft che richiedono token di acceso dall'endpoint di Azure Active Directory v2"
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
ms.openlocfilehash: d933781713456f73aa76db557fdf35672dfb2a29
ms.contentlocale: it-it


---

# <a name="call-the-microsoft-graph-api-from-an-android-app"></a>Chiamare l'API Microsoft Graph da un'app Android

Questa guida dimostra come un'applicazione Android nativa può ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso dall'endpoint di Azure Active Directory v2.

Al termine di questa guida, l'applicazione sarà in grado di chiamare un'API protetta usando sia account personali (ad esempio, outlook.com, live.com e altri) sia account aziendali o di istituti di istruzione di proprietà di aziende o organizzazioni con Azure Active Directory.  

### <a name="how-this-sample-works"></a>Come interpretare questo esempio
![Come interpretare questo esempio](media/active-directory-mobileanddesktopapp-android-intro/android-intro.png)

L'esempio creato in questa guida si basa su uno scenario in cui viene usata un'applicazione Android per eseguire query su un'API Web che accetta token dall'endpoint di Azure Active Directory v2, in questo caso l'API Microsoft Graph. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

### <a name="pre-requisites"></a>Prerequisiti
* Questa installazione guidata è basata su Android Studio, ma è accettabile anche qualsiasi altro ambiente di sviluppo di applicazioni Android. 
* È necessario Android SDK 21 o versione successiva (è consigliato SDK 25).
* Per questa versione di Microsoft Authentication Library (MSAL) per Android è necessario Google Chrome o un Web browser che usa schede personalizzate.

> Nota: Google Chrome non è incluso in Visual Studio Emulator for Android. È consigliabile testare questo codice in un emulatore con API 25 o in un'immagine con API 21 o versione successiva in cui è installato Google Chrome.


### <a name="how-to-handle-token-acquisition-to-access-a-protected-web-api"></a>Come gestire l'acquisizione dei token per accedere a un'API Web protetta

Dopo che l'utente ha eseguito l'autenticazione, l'applicazione di esempio riceve un token che può essere usato per eseguire query nell'API Microsoft Graph o in un'API Web protetta da Microsoft Azure Active Directory v2.

API come Microsoft Graph richiedono un token di accesso per consentire l'accesso a risorse specifiche, ad esempio per leggere un profilo utente, accedere al calendario dell'utente o inviare un messaggio di posta elettronica. L'applicazione può richiedere un token di accesso usando la libreria MSAL per accedere alle risorse tramite la definizione di ambiti API. Il token di accesso ottenuto viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata alla risorsa protetta. 

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti dalla libreria MSAL e non devono quindi essere effettuati dall'applicazione.

### <a name="libraries"></a>Librerie

Questa guida usa le librerie seguenti:

|Libreria|Descrizione|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|


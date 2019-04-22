---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: f0cc888eaf3724737e9c868c69a641094a19348c
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59498386"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chiamare l'API Microsoft Graph da un'app Windows Desktop

Questa guida dimostra come un'applicazione .NET per Windows Desktop (XAML) nativa può ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso da un endpoint di Microsoft Identity Platform per sviluppatori v2.0 (in precedenza denominato Azure AD).

Dopo aver completato la Guida, l'applicazione sarà in grado di chiamare un'API protetta che usa account personali (inclusi outlook.com, live.com e altri). L'applicazione userà anche account di lavoro e di formazione da qualsiasi società o organizzazione che usa Azure Active Directory.  

> [!NOTE]
> La guida richiede Visual Studio 2015 Update 3 o Visual Studio 2017. Non si dispone di nessuna di queste versioni? [Scaricare Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come funziona l'app di esempio generata da queste esercitazioni](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

L'applicazione di esempio creata in questa guida consente a un'applicazione per Windows Desktop di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Microsoft Identity Platform. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo l'autenticazione dell'utente, l'applicazione di esempio riceve un token che può essere usato per eseguire query sull'API Microsoft Graph o su un'API Web protetta da Microsoft Identity Platform per sviluppatori.

Le API come Microsoft Graph richiedono un token per consentire l'accesso a specifiche risorse. È necessario un token, ad esempio, per leggere il profilo di un utente, accedere al calendario di un utente o inviare posta elettronica. L'applicazione può richiedere un token di accesso usando MSAL per accedere alle risorse tramite la specifica degli ambiti dell'API. Questo token di accesso viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata per la risorsa protetta.

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti da MSAL e non devono quindi essere eseguiti dall'applicazione.

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|

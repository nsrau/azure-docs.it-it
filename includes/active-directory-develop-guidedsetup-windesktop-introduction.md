---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 9f25734259ce34caa0f0d7e844fc985f953b2795
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843597"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Chiamare l'API Microsoft Graph da un'app Windows Desktop

Questa guida dimostra come un'applicazione .NET per Windows Desktop (XAML) nativa può ottenere un token di accesso e chiamare l'API Microsoft Graph o altre API che richiedono token di accesso da un endpoint di Azure Active Directory v2.

Dopo aver completato la Guida, l'applicazione sarà in grado di chiamare un'API protetta che usa account personali (inclusi outlook.com, live.com e altri). L'applicazione userà anche account di lavoro e di formazione da qualsiasi società o organizzazione che usa Azure Active Directory.  

> [!NOTE] 
> La guida richiede Visual Studio 2015 Update 3 o Visual Studio 2017.  Non si dispone di nessuna di queste versioni? [Scaricare Visual Studio 2017 gratuitamente](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Come interpretare questa guida](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

L'applicazione di esempio creata in questa guida consente a un'applicazione per Windows Desktop di eseguire query nell'API Microsoft Graph o in un'API Web che accetta token dall'endpoint di Azure Active Directory v2. Per questo scenario, viene aggiunto un token a richieste HTTP tramite l'intestazione di autorizzazione. L'acquisizione e il rinnovo del token vengono gestiti da Microsoft Authentication Library (MSAL).

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Gestione dell'acquisizione di token per l'accesso ad API Web protette

Dopo l'autenticazione dell'utente, l'applicazione di esempio riceve un token che può essere usato per eseguire query sull'API Microsoft Graph o su un'API Web protetta da Microsoft Azure Active Directory v2.

Le API come Microsoft Graph richiedono un token per consentire l'accesso a specifiche risorse. È necessario un token, ad esempio, per leggere il profilo di un utente, accedere al calendario di un utente o inviare posta elettronica. L'applicazione può richiedere un token di accesso usando MSAL per accedere alle risorse tramite la specifica degli ambiti dell'API. Questo token di accesso viene quindi aggiunto all'intestazione di autorizzazione HTTP per ogni chiamata effettuata per la risorsa protetta. 

La memorizzazione nella cache e l'aggiornamento dei token di accesso vengono gestiti da MSAL e non devono quindi essere eseguiti dall'applicazione.

## <a name="nuget-packages"></a>Pacchetti NuGet

Questa guida usa i pacchetti NuGet seguenti:

|Libreria|DESCRIZIONE|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL)|


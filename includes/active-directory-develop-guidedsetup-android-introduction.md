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
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 33e2ac136ae68ee0c0ce0109a6f6934727d3a6c5
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203702"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Accesso utenti e chiamata di Microsoft Graph da un'app Android

In questa esercitazione si apprenderà come compilare un'applicazione Android e integrarla in Microsoft Identity Platform. In particolare, questa app consentirà a un utente di accedere, otterrà un token di accesso per chiamare l'API Microsoft Graph ed effettuerà una richiesta di base all'API Microsoft Graph.  

Al termine della guida, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda o organizzazione che usa Azure Active Directory.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funzionamento dell'app di esempio generata da questa guida

![Illustra come l'app di esempio generato da questo funzionamento esercitazioni](media/active-directory-develop-guidedsetup-android-intro/android-intro-updated.png)

L'app in questo esempio consentirà agli utenti di accedere e otterrà i dati per loro conto.  Questi dati saranno accessibili tramite un'API remota (in questo caso l'API Microsoft Graph) che richiede l'autorizzazione ed è anche protetta da Microsoft Identity Platform.

Più in particolare:

* L'app avvierà una pagina Web per l'accesso dell'utente.
* All'app verrà fornito un token di accesso per l'API Microsoft Graph.
* Il token di accesso verrà incluso nella richiesta HTTP all'API Web.
* Elaborare la risposta di Microsoft Graph.

Questo esempio usa Microsoft Authentication Library (MSAL) per Android per il coordinamento e il supporto per l'autenticazione. MSAL rinnoverà automaticamente i token, distribuirà SSO tra le altre app sul dispositivo, consentirà di gestire gli account e gestirà la maggior parte dei casi di accesso condizionale.

## <a name="prerequisites"></a>Prerequisiti

* Questa configurazione guidata usa Android Studio 3.0.
* È necessario Android 21 o versione successiva (si consiglia la versione 25 o successiva).
* Per questa versione di MSAL per Android è necessario Google Chrome o un Web browser che usa schede personalizzate.

## <a name="library"></a>Libreria

Questa guida usa la libreria di autenticazione seguente:

|Libreria|DESCRIZIONE|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

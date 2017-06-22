---
title: Introduzione ad Android per Azure AD v2 - Configurazione | Microsoft Docs
description: "Informazioni sul modo in cui un&quot;app di Android può ottenere un token di accesso e chiamare l&quot;API o le API Graph Microsoft che richiedono token di acceso dall&quot;endpoint di Azure Active Directory v2"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b034bf99351c5b33c51d9a8401434160bddb15be
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario aggiungere l'ID client al progetto.

1.    Aprire `MainActivity` (in `app` > `java` > *`{host}.{namespace}`*)
2.    Sostituire la riga che inizia con `final static String CLIENT_ID` con:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Aprire: `app` > `manifests` > `AndroidManifest.xml`
4. Aggiungere l'attività seguente al nodo `manifest\application`. Verrà registrata un'attività `BrowserTabActivity` per consentire al sistema operativo di riavviare l'applicazione dopo il completamento dell'autenticazione:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>Passaggi successivi

[Test e convalida](active-directory-mobileanddesktopapp-android-test.md)


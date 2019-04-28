---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
origin.date: 09/13/2018
ms.date: 11/05/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: b8f961ad3fe4550b915253746d0f4f677c593a8c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300604"
---
## <a name="add-the-applications-registration-to-your-code"></a>Aggiungere le informazioni di registrazione dell'applicazione al codice

In questo passaggio è necessario aggiungere l'ID client/applicazione al progetto.

1. Aprire `MainActivity` (in `app` > `java` > *`{host}.{namespace}`*)
2. Sostituire la riga che inizia con `final static String CLIENT_ID` con:
    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
3. Aprire: `app` > `manifests` > `AndroidManifest.xml`
4. Aggiungere l'attività seguente a `manifest\application`. `BrowserTabActivity` consente a Microsoft di eseguire il callback all'applicazione dopo il completamento dell'autenticazione:

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

<!-- ms.date: 11/05/2018 -->

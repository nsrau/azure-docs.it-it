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
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 6e20ce083c415bced22231835cc616ede8f0dd04
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843336"
---
## <a name="register-your-application"></a>Registrare l'applicazione
È possibile registrare l'applicazione in uno dei due modi descritti nelle due sezioni successive.

### <a name="option-1-express"></a>Opzione 1: Modalità rapida
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  In **Application Name** (Nome applicazione) immettere un nome per l'applicazione.

3. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia selezionata e quindi selezionare **Create** (Crea).

4. Seguire le istruzioni per ottenere l'ID applicazione e incollarlo nel codice.

### <a name="option-2-advanced"></a>Opzione 2: Modalità avanzata 
1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Nella casella **Application Name** (Nome applicazione) immettere un nome per l'applicazione. 

3. Verificare che la casella di controllo **Guided Setup** (Configurazione guidata) sia deselezionata e quindi selezionare **Create** (Crea).

4. Selezionare **Aggiungi piattaforma**, **Applicazione nativa** e quindi **Salva**.

5. In **app** > **java** > **{host}.{spazionomi}** aprire `MainActivity`. 

6.  Sostituire *[Enter the application Id here]* con l'ID client/applicazione:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. In **app** > **manifests** aprire il file *AndroidManifest.xml*.

8. In `manifest\application` aggiungere l'attività seguente. L'attività `BrowserTabActivity` consente a Microsoft di eseguire il callback all'applicazione dopo il completamento dell'autenticazione:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
<!-- Workaround for Docs conversion bug -->
9. In `BrowserTabActivity` sostituire `[Enter the application Id here]` con l'ID client/applicazione.

---
title: 'Introduzione a iOS con Azure AD v2: configurazione | Microsoft Docs'
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
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 8a4f412102c0e75654030f5d4f8aab575fdcebba
ms.contentlocale: it-it

---
## <a name="create-an-application-express"></a>Creare un'applicazione (Rapida)
È ora necessario registrare l'applicazione nel *portale di registrazione delle applicazioni Microsoft*:
1. Registrare l'applicazione tramite il [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia selezionata
4.  Seguire le istruzioni per ottenere l'ID dell'applicazione e incollarlo nel codice

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Aggiungere le informazioni di registrazione dell'applicazione alla soluzione (Avanzata)

1.  Passare al [Portale di registrazione delle applicazioni di Microsoft](https://apps.dev.microsoft.com/portal/register-app)
2.  Immettere un nome per l'applicazione e l'indirizzo di posta elettronica
3.  Assicurarsi che l'opzione per l'installazione guidata sia deselezionata
4.  Fare clic su `Add Platform`, selezionare `Native Application` e quindi fare clic su `Save`
5.  Tornare a Xcode. In `ViewController.swift` sostituire la riga che inizia con "`let kClientID`" con l'ID dell'applicazione appena registrata:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
Digitale Control+ clic su <code>Info.plist</code> per visualizzare il menu di scelta rapida e quindi fare clic su: <code>Open As</code>> <code>Source Code</code>
</li>
<li>
Sotto il nodo radice <code>dict</code> aggiungere quanto segue:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Sostituire <i><code>[Your_Application_Id_Here]</code></i> con l'ID applicazione appena registrato
</li>
</ol>


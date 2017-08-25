---
title: 'Introduzione a iOS con Azure AD v2: configurazione (ARP) | Microsoft Docs'
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
ms.openlocfilehash: 5ce5d26943839d9fe53508d396825289d77cf2f8
ms.contentlocale: it-it
ms.lasthandoff: 08/15/2017

---
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario aggiungere l'ID applicazione al progetto:

1.  In `ViewController.swift` sostituire la riga che inizia con "`let kClientID`":
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
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
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```


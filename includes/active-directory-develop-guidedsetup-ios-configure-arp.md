---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: f6c812d8519e05a92cf07eed04e1c568db142577
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967648"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Aggiungere le informazioni di registrazione dell'applicazione all'app

In questo passaggio è necessario aggiungere l'ID applicazione al progetto:

1. In `ViewController.swift` sostituire la riga che inizia con "`let kClientID`":

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
        </array>
    </dict>
</array>
```

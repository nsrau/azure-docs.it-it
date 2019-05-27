---
title: Problemi noti in browser (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni su problemi noti quando si usa Microsoft Authentication Library per JavaScript (msal) con il browser Safari.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873886"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemi noti nel browser Safari con msal. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Rinnovo del token invisibile all'utente in Safari 12 e ITP 2.0

Apple iOS 12 e sistemi operativi MacOS 10.14 incluse una versione del [browser Safari 12](https://developer.apple.com/safari/whats-new/). Per motivi di sicurezza e privacy, Safari 12 include il [2.0 di prevenzione di rilevamento intelligente](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). In questo modo essenzialmente il browser eliminare i cookie di terze parti da impostare. ITP 2.0 considera anche cookie impostati dai provider di identità come cookie di terze parti.

### <a name="impact-on-msaljs"></a>Impatto sul msal. js

Msal. js Usa un Iframe nascosto per eseguire come parte dell'acquisizione di token invisibile all'utente e il rinnovo di `acquireTokenSilent` chiamate. Le richieste di token invisibile all'utente si basano sull'Iframe che possono accedere alla sessione utente autenticato rappresentata dal cookie impostati da Azure AD. 2.0 ITP impedendo l'accesso a questi cookie, msal. js ha esito negativo acquisire e rinnovare i token invisibile all'utente e di conseguenza `acquireTokenSilent` errori.

A questo punto non vi è alcuna soluzione per risolvere questo problema e si sta valutando opzioni con la community degli standard.

### <a name="work-around"></a>Soluzione alternativa

Per impostazione predefinita è abilitata l'impostazione ITP nel browser Safari. È possibile disabilitare questa impostazione, passare a **preferenze** -> **Privacy** e deselezionando i **evitare il cross-site rilevamento** opzione.

![impostazione di Safari](./media/msal-js-known-issue-safari-browser/safari.png)

È necessario gestire il `acquireTokenSilent` errori relativi a un oggetto interattivo acquisire token chiamata, il che richiede all'utente di accedere.
Per evitare accessi ripetuti, un approccio è possibile implementare consiste nel gestire la `acquireTokenSilent` errore e consentire all'utente un'opzione per disabilitare l'impostazione ITP in Safari prima di procedere con la chiamata interattiva. Dopo l'impostazione è disabilitata, i rinnovi successivi token invisibile all'utente dovrebbero avere esito positivo.

---
title: Problemi noti del browser Safari (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Scopri di più sui problemi relativi alle informazioni sull'uso della libreria di autenticazione Microsoft per JavaScript (MSAL.js) con il browser Safari.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696113"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Problemi noti sul browser Safari con MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Rinnovo silenzioso dei token su Safari 12 e ITP 2.0

I sistemi operativi Apple iOS 12 e MacOS 10.14 includevano una versione del [browser Safari 12.](https://developer.apple.com/safari/whats-new/) Per motivi di sicurezza e privacy, Safari 12 include [l'Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). Ciò causa essenzialmente il browser per eliminare i cookie di terze parti impostati. ITP 2.0 considera inoltre i cookie impostati dai provider di identità come cookie di terze parti.

### <a name="impact-on-msaljs"></a>Impatto su MSAL.js

MSAL.js utilizza un Iframe nascosto per eseguire l'acquisizione e il `acquireTokenSilent` rinnovo di token silenziosi come parte delle chiamate. Le richieste di token invisibile all'utente si basano sull'Iframe con accesso alla sessione utente autenticata rappresentata dai cookie impostati da Azure AD. Con ITP 2.0 che impedisce l'accesso a questi cookie, MSAL.js non `acquireTokenSilent` riesce ad acquisire e rinnovare in modo invisibile all'utente i token e ciò si traduce in errori.

Non esiste una soluzione per questo problema a questo punto e stiamo valutando le opzioni con la comunità degli standard.

### <a name="work-around"></a>Lavorare intorno

Per impostazione predefinita, l'impostazione ITP è abilitata sul browser Safari. È possibile disattivare questa impostazione passando a**Privacy** **preferenze** -> e deselezionando l'opzione **Impedisci monitoraggio intersito.**

![impostazione safari](./media/msal-js-known-issue-safari-browser/safari.png)

È necessario gestire `acquireTokenSilent` gli errori con una chiamata interattiva al token di acquisizione, che richiede all'utente di accedere.
Per evitare gli invii ripetuti, un `acquireTokenSilent` approccio che è possibile implementare consiste nel gestire l'errore e fornire all'utente un'opzione per disabilitare l'impostazione ITP in Safari prima di procedere con la chiamata interattiva. Una volta disattivata l'impostazione, i successivi rinnovi di token invisibile all'utente dovrebbero avere esito positivo.

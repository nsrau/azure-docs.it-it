---
title: Risoluzione dei problemi relativi a TLS/SSL (MSAL iOS/macOS) Azure
titleSuffix: Microsoft identity platform
description: Informazioni su cosa fare in merito a vari problemi relativi all'utilizzo di certificati TLS/SSL con MSAL. Libreria Obiettivo-C.
services: active-directory
documentationcenter: ''
author: mmacy
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 42bd016a9d0882cc97eaa800235226b2de9569a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369411"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Procedura: Risolvere i problemi relativi a MSAL per iOS e macOS TLS/SSLHow to: Troubleshoot MSAL for iOS and macOS TLS/SSL issues

In questo articolo vengono fornite informazioni che consentono di risolvere i problemi che possono verificarsi durante l'utilizzo di [Microsoft Authentication Library (MSAL) per iOS e macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problemi di rete

**Errore -1200:**"Si è verificato un errore SSL e non è possibile effettuare una connessione sicura al server".

Questo errore indica che la connessione non è sicura. Si verifica quando un certificato non è valido. Per ulteriori informazioni, incluso il server che `NSURLErrorFailingURLErrorKey` non `userInfo` supera il controllo TLS, fare riferimento a nel dizionario dell'oggetto errore.

Questo errore proviene dalla libreria di rete di Apple. Un elenco completo dei codici di errore NSURL è in NSURLError.h negli SDK di macOS e iOS. Per ulteriori informazioni su questo errore, vedere [URL Caricamento codici di errore](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc)di sistema .

## <a name="certificate-issues"></a>Problemi relativi ai certificati

Se l'URL che fornisce un certificato non valido si connette al server che si intende utilizzare come parte del flusso di autenticazione, è possibile verificare il problema consiste nel testare l'URL con un servizio di convalida SSL, ad esempio [SSL Server Test](https://www.ssllabs.com/ssltest/analyze.html). Testa il server con una vasta gamma di scenari e browser e verifica la presenza di molte vulnerabilità note.

Per impostazione predefinita, la nuova funzionalità [APP Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) di Apple applica criteri di sicurezza più rigorosi alle app che utilizzano certificati TLS/SSL. Alcuni sistemi operativi e browser web hanno iniziato ad applicare alcuni di questi criteri per impostazione predefinita. Per motivi di sicurezza, si consiglia di non disattivare ATS.

I certificati che utilizzano gli eporti SHA-1 presentano vulnerabilità note. La maggior parte dei browser Web moderni non consente certificati con gli hashe SHA-1.

## <a name="captive-portals"></a>Portali in cattività

Un portale captive presenta una pagina web a un utente quando accede per la prima volta a una rete Wi-Fi e non ha ancora ottenuto l'accesso a tale rete. Intercetta il traffico Internet fino a quando l'utente non soddisfa i requisiti del portale. Gli errori di rete perché l'utente non può connettersi alle risorse di rete sono previsti fino a quando l'utente non si connette tramite il portale.Network errors because the user can't connect to network resources are expected until the user connects through the portal.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui [portali captive](https://en.wikipedia.org/wiki/Captive_portal) e sulla nuova funzionalità [App Transport Security (ATS) di](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) Apple.

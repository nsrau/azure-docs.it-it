---
title: Autenticazione desktop virtuale di Windows-Azure
description: Metodi di autenticazione per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500301"
---
# <a name="supported-authentication-methods"></a>Metodi di autenticazione supportati

In questo articolo viene illustrata una breve panoramica dei tipi di autenticazione che è possibile usare nel desktop virtuale di Windows.

## <a name="session-host-authentication"></a>Autenticazione host sessione

Desktop virtuale Windows supporta sia NT LAN Manager (NTLM) che Kerberos per l'autenticazione dell'host sessione. Tuttavia, per utilizzare Kerberos, il client deve ottenere i ticket di sicurezza Kerberos da un servizio Centro distribuzione chiavi (KDC) in esecuzione in un controller di dominio. Per ottenere i ticket, il client deve avere una linea di visibilità diretta per il controller di dominio. È possibile ottenere una linea di visione diretta usando la rete aziendale. È anche possibile usare una connessione VPN alla rete aziendale.

Questi sono i metodi di accesso attualmente supportati:

- Client desktop di Windows
    - Nome utente e password
    - Smart card
    - Windows Hello
- Client Windows Store
    - Nome utente e password
- Client Web
    - Nome utente e password
- Android
    - Nome utente e password
- iOS
    - Nome utente e password
- macOS
    - Nome utente e password

>[!NOTE]
>Smart Card e Windows Hello possono usare solo Kerberos per accedere. L'accesso con Kerberos richiede una linea di visibilità al controller di dominio.

## <a name="hybrid-identity"></a>Identità ibrida

Il desktop virtuale Windows supporta le [identità ibride](../active-directory/hybrid/whatis-hybrid-identity.md) tramite Azure Active Directory (ad), incluse quelle federate con Active Directory Federation Services (ADFS). Poiché gli utenti devono essere individuabili tramite Azure AD, desktop virtuale Windows non supporta le distribuzioni Active Directory autonome con ADFS.

## <a name="single-sign-on-sso"></a>Single Sign-on (SSO)

Il desktop virtuale Windows non supporta attualmente Active Directory Federation Services (ADFS) per SSO.

L'unico modo per evitare che vengano richieste le credenziali per l'host della sessione consiste nel salvarle nel client. È consigliabile eseguire questa operazione solo con i dispositivi protetti per impedire ad altri utenti di accedere alle risorse.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri modi per proteggere la distribuzione Vedere le [procedure](security-guide.md)consigliate per la sicurezza.

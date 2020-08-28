---
title: Autenticazione desktop virtuale di Windows-Azure
description: Metodi di autenticazione per desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8328fa9c9e32a22e8e5093aa343f333cd1914ddc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89038671"
---
# <a name="supported-authentication-methods"></a>Metodi di autenticazione supportati

In questo articolo viene illustrata una breve panoramica dei tipi di autenticazione che è possibile usare nel desktop virtuale di Windows.

## <a name="session-host-authentication"></a>Autenticazione host sessione

Desktop virtuale Windows supporta sia NT LAN Manager (NTLM) che Kerberos per l'autenticazione dell'host sessione. Tuttavia, per utilizzare Kerberos, il client deve ottenere i ticket di sicurezza Kerberos da un servizio Centro distribuzione chiavi (KDC) in esecuzione in un controller di dominio. Per ottenere i ticket, il client deve avere una linea di visibilità diretta per il controller di dominio. È possibile ottenere una linea di visione diretta usando la rete aziendale. È anche possibile usare una connessione VPN alla rete aziendale.

Questi sono i metodi di accesso attualmente supportati:

- Client Windows Desktop
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

## <a name="single-sign-on-sso"></a>Single Sign-on (SSO)

Il desktop virtuale Windows non supporta attualmente Active Directory Federation Services (ADFS) per l'autenticazione o l'accesso SSO.

L'unico modo per evitare che vengano richieste le credenziali per l'host della sessione consiste nel salvarle nel client. È consigliabile eseguire questa operazione solo con i dispositivi protetti per impedire ad altri utenti di accedere alle risorse.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su altri modi per proteggere la distribuzione Vedere le [procedure](security-guide.md)consigliate per la sicurezza.

---
title: Disponibilità e limitazioni di Microsoft Authenticator per Android in Cina | Microsoft Docs
description: Informazioni su come ottenere l'app Microsoft Authenticator - Disponibilità in Cina
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/20/2020
ms.author: curtand
ms.openlocfilehash: 795c68fc063d98bdee6ccf59dba6ee718dc92d03
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84323026"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>Microsoft Authenticator per Android nel cloud pubblico in Cina

L'app Microsoft Authenticator per Android è disponibile per il download in Cina. Il Google Play Store non è disponibile in Cina, per cui l'app deve essere scaricata da altri marketplace di app cinesi. L'app Microsoft Authenticator per Android è attualmente disponibile presso gli store seguenti in Cina:

- [Baidu](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy Store](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

La build più recente dell'app è disponibile nel Google Play Store, ma l'app viene aggiornata in tutti gli altri store di app non appena possibile. Poiché negli store di app non vengono distribuiti pacchetti di applicazioni Android personalizzati, l'app può essere aggiornata semplicemente da una delle risorse seguenti:

- Lo store da cui è stata scaricata
- Il Google Play Store se l'utente si reca in altre aree geografiche

## <a name="limitations"></a>Limitazioni

L'app Microsoft Authenticator per Android usa il sistema Firebase Cloud Messaging di Google e Google Play Services per ricevere notifiche push. Poiché nessuno di questi servizi è disponibile in Cina, esistono alcune limitazioni nelle funzionalità dell'app:

- La registrazione dell'app Authenticator come metodo di autenticazione a più fattori tramite notifiche push non funziona.

- Non è possibile configurare l'[accesso tramite telefono](../authentication/howto-authentication-sms-signin.md). Richiede che l'utente configuri l'app come metodo di autenticazione a più fattori tramite notifiche push, il che attualmente non funziona.

Se un utente in precedenza è riuscito a configurare l'accesso tramite telefono o l'autenticazione a più fattori tramite l'app, può controllare manualmente la presenza di richieste di notifiche nell'app a scopo di verifica dell'identità.

## <a name="multi-factor-authentication-workaround"></a>Soluzione alternativa per l'autenticazione a più fattori

Invece di usare le notifiche push per l'autenticazione a più fattori, gli utenti possono [configurare l'app Authenticator in modo da ricevere codici di verifica](multi-factor-authentication-setup-auth-app.md#set-up-the-microsoft-authenticator-app-to-use-verification-codes) nei propri dispositivi e usare tali codici per la verifica dell'identità. Questi codici di verifica sono validi per 30 secondi e, per usarli, gli amministratori devono consentire al tenant di eseguire la verifica tramite codici TOTP (Time-Based One-Time Passwords).

## <a name="availability"></a>Disponibilità

Funzionalità di Microsoft Authenticator | Disponibilità in Cina
------------------------------- | ---------------------
Registrazione dell'autenticazione a più fattori tramite notifiche push | No
Account di autenticazione a più fattori preesistente per la verifica dell'identità tramite notifiche push | No
Account di autenticazione a più fattori preesistente per il controllo manuale della presenza di notifiche | Sì
Registrazione dell'autenticazione a più fattori/autenticazione solo tramite codici di verifica/TOTP | Sì
Registrazione dell'accesso tramite telefono | No
Accesso telefonico esistente tramite notifiche push | No
Verifica dell'accesso tramite telefono esistente con controllo manuale della presenza di richieste di autenticazione | Sì

## <a name="next-steps"></a>Passaggi successivi

- [Scaricare e installare l'app Microsoft Authenticator](user-help-auth-app-download-install.md)

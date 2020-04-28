---
title: Eseguire la migrazione a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra Microsoft Authentication Library (MSAL) e Autenticazione di Azure AD Library (ADAL) e su come eseguire la migrazione a MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/27/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3af18eb09fd9906a0caaebda0b786795400467f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164932"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Eseguire la migrazione di applicazioni a Microsoft Authentication Library (MSAL)

Microsoft Authentication Library (MSAL) e Autenticazione di Azure AD Library (ADAL) vengono usati per autenticare Azure AD entità e richiedere token da Azure AD. Finora la maggior parte degli sviluppatori ha lavorato con Azure AD per la piattaforma per sviluppatori (v1.0) per autenticare le identità di Azure AD (account aziendali e dell'istituto di istruzione) richiedendo token con Azure AD Authentication Library (ADAL). Uso di MSAL:

- È possibile autenticare un set più ampio di identità Microsoft (Azure AD identità e account Microsoft e account social e locali tramite Azure AD B2C), in quanto usa l'endpoint della piattaforma di identità Microsoft.
- Gli utenti otterranno la migliore esperienza Single Sign-on.
- L'applicazione può abilitare il consenso incrementale e il supporto dell'accesso condizionale è più semplice.
- È possibile trarre vantaggio dall'innovazione.

**MSAL è ora la libreria di autenticazione consigliata da usare con la piattaforma di identità Microsoft**. In ADAL non verranno implementate nuove funzionalità. Gli sforzi sono incentrati sul miglioramento di MSAL.

Gli articoli seguenti descrivono le differenze tra le librerie MSAL e ADAL e consentono di eseguire la migrazione a MSAL:
- [Eseguire la migrazione a MSAL.NET](msal-net-migration.md)
- [Eseguire la migrazione a MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Eseguire la migrazione a MSAL.Android](migrate-android-adal-msal.md)
- [Eseguire la migrazione a MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Eseguire la migrazione a MSAL Python](migrate-python-adal-msal.md)
- [Eseguire la migrazione a MSAL per Java](migrate-adal-msal-java.md)
- [Eseguire la migrazione di app Xamarin tramite broker a MSAL.NET](msal-net-migration-ios-broker.md)

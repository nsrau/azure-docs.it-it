---
title: Eseguire la migrazione a Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Informazioni sulle differenze tra Microsoft Authentication Library (MSAL) e Azure AD Authentication Library (ADAL) e su come eseguire la migrazione a MSAL.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164932"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Eseguire la migrazione delle applicazioni a Microsoft Authentication Library (MSAL)Migrate applications to Microsoft Authentication Library (MSAL)

Sia Microsoft Authentication Library (MSAL) che Azure AD Authentication Library (ADAL) vengono usati per autenticare le entità di Azure AD e i token di richiesta da Azure AD. Finora la maggior parte degli sviluppatori ha lavorato con Azure AD per la piattaforma per sviluppatori (v1.0) per autenticare le identità di Azure AD (account aziendali e dell'istituto di istruzione) richiedendo token con Azure AD Authentication Library (ADAL). Utilizzo di MSAL:

- È possibile autenticare un set più ampio di identità Microsoft (identità di Azure AD e account Microsoft e account social e locali tramite Azure AD B2C) quando usa l'endpoint della piattaforma di identità Microsoft.You can authenticate a broader set of Microsoft identities (Azure AD identities and Microsoft accounts, and social and local accounts through Azure AD B2C) as it uses the Microsoft identity platform endpoint.
- Gli utenti avranno la migliore esperienza Single Sign-On.
- L'applicazione può abilitare il consenso incrementale e il supporto dell'accesso condizionale è più semplice.
- Approfittate dell'innovazione.

**MSAL è ora la libreria di autenticazione consigliata da utilizzare con la piattaforma**di identità Microsoft . Nessuna nuova funzionalità sarà implementata su ADAL. Gli sforzi sono incentrati sul miglioramento della MSAL.

Gli articoli seguenti descrivono le differenze tra le librerie MSAL e ADAL e consentono di eseguire la migrazione a MSAL:
- [Eseguire la migrazione a MSAL.NET](msal-net-migration.md)
- [Eseguire la migrazione a MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Eseguire la migrazione a MSAL.Android](migrate-android-adal-msal.md)
- [Eseguire la migrazione a MSAL.iOS / macOS](migrate-objc-adal-msal.md)
- [Eseguire la migrazione a MSAL Python](migrate-python-adal-msal.md)
- [Eseguire la migrazione a MSAL per Java](migrate-adal-msal-java.md)
- [Eseguire la migrazione di app Xamarin tramite broker a MSAL.NET](msal-net-migration-ios-broker.md)

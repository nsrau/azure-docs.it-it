---
title: Plug-in Microsoft Enterprise SSO per dispositivi Apple
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sul plug-in Azure Active Directory SSO di Microsoft per i dispositivi iOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982583"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO per i dispositivi Apple (anteprima)

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il *plug-in Microsoft Enterprise SSO per i dispositivi Apple* fornisce Single Sign-on (SSO) per gli account Azure Active Directory (Azure ad) in tutte le applicazioni che supportano la funzionalità di [Enterprise Single Sign-on](https://developer.apple.com/documentation/authenticationservices) di Apple. Microsoft ha collaborato con Apple per sviluppare questo plug-in per aumentare l'usabilità dell'applicazione garantendo al contempo la migliore protezione fornita da Apple e da Microsoft.

In questa versione di anteprima pubblica, il plug-in Enterprise SSO è disponibile solo per i dispositivi iOS ed è distribuito in determinate applicazioni Microsoft.

Il primo uso del plug-in Enterprise SSO è costituito dalla nuova funzionalità di [modalità dispositivo condiviso](msal-ios-shared-devices.md) .

## <a name="features"></a>Funzionalità

Il plug-in Microsoft Enterprise SSO per i dispositivi Apple offre i vantaggi seguenti:

- Fornisce l'accesso SSO per gli account Azure AD in tutte le applicazioni che supportano la funzionalità Enterprise Single Sign-On di Apple.
- Viene recapitato automaticamente nel Microsoft Authenticator e può essere abilitato da qualsiasi soluzione di gestione di dispositivi mobili (MDM).

## <a name="requirements"></a>Requisiti

Per usare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- iOS 13,0 o versione successiva deve essere installato nel dispositivo.
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisce il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per la versione di anteprima pubblica, queste applicazioni includono l' [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Il dispositivo deve essere registrato con MDM, ad esempio con Microsoft Intune.
- È necessario effettuare il push della configurazione nel dispositivo per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple nel dispositivo. Questo vincolo di sicurezza è richiesto da Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Abilitare l'estensione SSO con la gestione dei dispositivi mobili (MDM)

Per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple, i dispositivi devono essere inviati tramite un servizio MDM. Poiché Microsoft include il plug-in Enterprise SSO nell' [app Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), usare MDM per configurare l'app per abilitare il plug-in Microsoft Enterprise SSO.

Per configurare il plug-in Microsoft Enterprise SSO per i dispositivi Apple, usare i parametri seguenti:

- **Tipo**: Reindirizzamento
- **ID estensione**:`com.microsoft.azureauthenticator.ssoextension`
- **ID team**:`SGGM6D27TK`
- **URL**:
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

È possibile usare Microsoft Intune come servizio MDM per semplificare la configurazione del plug-in Microsoft Enterprise SSO. Per ulteriori informazioni, vedere la [documentazione di configurazione di Intune](https://docs.microsoft.com/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-extension-in-your-application"></a>Utilizzo dell'estensione SSO nell'applicazione

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e versioni successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple.

Se si vuole supportare la modalità dispositivo condiviso fornita dal plug-in Microsoft Enterprise SSO per i dispositivi Apple, assicurarsi che le applicazioni usino la versione minima richiesta specificata di MSAL.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla modalità dispositivo condiviso in iOS, vedere [modalità dispositivo condiviso per dispositivi iOS](msal-ios-shared-devices.md).

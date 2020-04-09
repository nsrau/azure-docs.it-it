---
title: Plug-in Microsoft Enterprise SSO per dispositivi Apple
titleSuffix: Microsoft identity platform | Azure
description: Informazioni sul plug-in SSO di Azure Active Directory di Microsoft per dispositivi iOS e macOS.
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
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982583"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in Microsoft Enterprise SSO per dispositivi Apple (anteprima)

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il *plug-in Microsoft Enterprise SSO per i dispositivi Apple* fornisce l'accesso Single Sign-On (SSO) per gli account Azure Active Directory (Azure AD) in tutte le applicazioni che supportano la funzionalità Enterprise Single [Sign-On](https://developer.apple.com/documentation/authenticationservices) di Apple. Microsoft ha collaborato strettamente con Apple per sviluppare questo plug-in per aumentare l'usabilità dell'applicazione, fornendo al contempo la migliore protezione che Apple e Microsoft possono fornire.

In questa versione di anteprima pubblica, il plug-in Enterprise SSO è disponibile solo per i dispositivi iOS ed è distribuito in alcune applicazioni Microsoft.

Il nostro primo utilizzo del plug-in Enterprise SSO è con la nostra nuova funzione di [modalità dispositivo condiviso.](msal-ios-shared-devices.md)

## <a name="features"></a>Funzionalità

Il plug-in Microsoft Enterprise SSO per i dispositivi Apple offre i seguenti vantaggi:

- Fornisce gli account SSO per Azure AD in tutte le applicazioni che supportano la funzionalità Enterprise Single Sign-On di Apple.
- Consegnato automaticamente in Microsoft Authenticator e può essere abilitato da qualsiasi soluzione di gestione dei dispositivi mobili (MDM).

## <a name="requirements"></a>Requisiti

Per utilizzare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- iOS 13.0 o versione successiva deve essere installato nel dispositivo.
- Nel dispositivo deve essere installata un'applicazione Microsoft che fornisca il plug-in Microsoft Enterprise SSO per i dispositivi Apple. Per l'anteprima pubblica, queste applicazioni includono [l'app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- Il dispositivo deve essere registrato MDM (ad esempio, con Microsoft Intune).
- La configurazione deve essere inviata al dispositivo per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple nel dispositivo. Questo vincolo di sicurezza è richiesto da Apple.

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>Abilitare l'estensione SSO con la gestione dei dispositivi mobili (MDM)Enable the SSO extension with mobile device management (MDM)

Per abilitare il plug-in Microsoft Enterprise SSO per i dispositivi Apple, i dispositivi devono essere inviati un segnale tramite un servizio MDM. Poiché Microsoft include il plug-in Enterprise SSO [nell'app Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), utilizzare MDM per configurare l'app per abilitare il plug-in Microsoft Enterprise SSO.

Utilizzare i seguenti parametri per configurare il plug-in Microsoft Enterprise SSO per i dispositivi Apple:

- **Tipo**: Reindirizza
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

È possibile usare Microsoft Intune come servizio MDM per semplificare la configurazione del plug-in Microsoft Enterprise SSO. Per altre informazioni, vedere la [documentazione sulla configurazione](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)di Intune.For more information, see the Intune configuration documentation .

## <a name="using-the-sso-extension-in-your-application"></a>Utilizzo dell'estensione SSO nell'applicazione

[Microsoft Authentication Library (MSAL) per i dispositivi Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versione 1.1.0 e successive supporta il plug-in Microsoft Enterprise SSO per i dispositivi Apple.

Se si desidera supportare la modalità di dispositivo condiviso fornita dal plug-in Microsoft Enterprise SSO per i dispositivi Apple, verificare che le applicazioni utilizzino la versione minima richiesta di MSAL specificata.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla modalità dispositivo condiviso in iOS, consultate [Modalità dispositivo condiviso per dispositivi iOS](msal-ios-shared-devices.md).

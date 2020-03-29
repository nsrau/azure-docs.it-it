---
title: File di inclusione
description: includere un file per le pagine di destinazione con scenari client riservati (daemon, web app, API Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773371"
---
## <a name="register-secrets-or-certificates"></a>Registrare segreti o certificatiRegister secrets or certificates

Come per qualsiasi applicazione client riservata, è necessario registrare un segreto o un certificato. È possibile registrare i segreti dell'applicazione tramite l'esperienza interattiva nel portale di [Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) o usando gli strumenti della riga di comando (ad esempio PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrare i segreti client tramite il portale di registrazione dell'applicazioneRegister client secrets by using the application registration portal

La gestione delle credenziali client avviene nella pagina Certificati & segreti per un'applicazione:The management of client **credentials** happens on the Certificates & secrets page for an application:

![Pagina Certificati & segreti](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Il segreto dell'applicazione (denominato anche segreto client) viene generato da Azure AD durante la registrazione dell'applicazione client riservata. Questa generazione si verifica quando si seleziona **Nuovo segreto client**. A questo punto, è necessario copiare la stringa segreta negli Appunti per l'utilizzo nell'app, prima di selezionare **Salva**. Questa stringa non verrà più presentata.
- Durante la registrazione dell'applicazione, si utilizza il pulsante **Carica certificato** per caricare il certificato. Azure AD supporta solo i certificati registrati direttamente nell'applicazione e non seguono le catene di certificati.

Per informazioni dettagliate, vedere [Guida introduttiva: Configurare un'applicazione client per l'accesso alle API Web. Aggiungere credenziali all'applicazione.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="register-client-secrets-by-using-powershell"></a>Registrare i segreti client tramite PowerShellRegister client secrets by using PowerShell

In alternativa, è possibile registrare l'applicazione con Azure AD usando gli strumenti della riga di comando. Nell'esempio [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) viene illustrato come registrare un segreto dell'applicazione o un certificato con un'applicazione Azure AD:The active-directory-dotnetcore-daemon-v2 sample shows how to register an application secret or certificate with an Azure AD application:

- Per informazioni dettagliate su come registrare un segreto dell'applicazione, vedere [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Per informazioni dettagliate su come registrare un certificato con un'applicazione, vedere [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

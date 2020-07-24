---
title: includere file
description: file di inclusione per le pagine di destinazione dello scenario client riservato (daemon, app Web, API Web)
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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102757"
---
## <a name="register-secrets-or-certificates"></a>Registrare segreti o certificati

Per le applicazioni client riservate, è necessario registrare un segreto o un certificato. È possibile registrare i segreti dell'applicazione tramite l'esperienza interattiva nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) o usando gli strumenti da riga di comando (ad esempio PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrare i segreti client usando il portale di registrazione delle applicazioni

La gestione delle credenziali client viene eseguita nella pagina **certificati & segreti** di un'applicazione:

![Pagina certificati & segreti](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Per creare un *segreto client* , selezionare **nuovo segreto client** nella registrazione dell'app nel portale di Azure. Quando si crea un segreto client, è _necessario_ registrare la stringa del segreto prima di uscire dal riquadro **certificati & segreti** . La stringa del segreto non viene mai visualizzata di nuovo.
- Durante la registrazione dell'applicazione, usare il pulsante **Carica certificato** per caricare il certificato. Azure AD supporta solo i certificati che sono registrati direttamente nell'applicazione e non seguono le catene di certificati.

Per informazioni dettagliate, vedere [Guida introduttiva: configurare un'applicazione client per accedere alle API Web | Aggiungere le credenziali all'applicazione](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Registrare i segreti client usando PowerShell

In alternativa, è possibile registrare l'applicazione con Azure AD usando gli strumenti da riga di comando. L'esempio [Active-Directory-dotnetcore-Daemon-V2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) Mostra come registrare un segreto o un certificato dell'applicazione con un'applicazione Azure ad:

- Per informazioni dettagliate su come registrare un segreto dell'applicazione, vedere [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Per informazioni dettagliate su come registrare un certificato con un'applicazione, vedere [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76773371"
---
## <a name="register-secrets-or-certificates"></a>Registrare segreti o certificati

Per le applicazioni client riservate, è necessario registrare un segreto o un certificato. È possibile registrare i segreti dell'applicazione tramite l'esperienza interattiva nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) o usando gli strumenti da riga di comando (ad esempio PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrare i segreti client usando il portale di registrazione delle applicazioni

La gestione delle credenziali client viene eseguita nella pagina **certificati & segreti** di un'applicazione:

![Pagina certificati & segreti](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Il segreto dell'applicazione (denominato anche segreto client) viene generato da Azure AD durante la registrazione dell'applicazione client riservata. Questa generazione si verifica quando si seleziona **nuovo segreto client**. A questo punto, è necessario copiare la stringa segreta negli Appunti da usare nell'app, prima di selezionare **Salva**. Questa stringa non verrà più visualizzata.
- Durante la registrazione dell'applicazione, usare il pulsante **Carica certificato** per caricare il certificato. Azure AD supporta solo i certificati che sono registrati direttamente nell'applicazione e non seguono le catene di certificati.

Per informazioni dettagliate, vedere [Guida introduttiva: configurare un'applicazione client per accedere alle API Web | Aggiungere le credenziali all'applicazione](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Registrare i segreti client usando PowerShell

In alternativa, è possibile registrare l'applicazione con Azure AD usando gli strumenti da riga di comando. L'esempio [Active-Directory-dotnetcore-Daemon-V2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) Mostra come registrare un segreto o un certificato dell'applicazione con un'applicazione Azure ad:

- Per informazioni dettagliate su come registrare un segreto dell'applicazione, vedere [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Per informazioni dettagliate su come registrare un certificato con un'applicazione, vedere [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

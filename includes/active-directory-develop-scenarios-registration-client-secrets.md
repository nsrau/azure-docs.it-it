---
title: File di inclusione
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
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286286"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrazione di segreti o certificati

Come per le applicazioni client riservate, è necessario registrare un segreto o un certificato. È possibile registrare i segreti dell'applicazione tramite l'esperienza interattiva nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview)o usando gli strumenti da riga di comando (ad esempio PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrazione dei segreti client tramite il portale di registrazione delle applicazioni

La gestione delle credenziali client viene eseguita nella pagina **certificati & segreti** di un'applicazione:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- il segreto dell'applicazione (denominato anche segreto client) viene generato da Azure AD durante la registrazione dell'applicazione client riservata. Questa generazione si verifica quando si seleziona **nuovo segreto client**. A questo punto, è necessario copiare la stringa segreta negli Appunti da usare nell'app, prima di selezionare **Salva**. Questa stringa non verrà più visualizzata.
- il certificato viene caricato nella registrazione dell'applicazione usando il pulsante **Carica certificato** . Azure AD supporta solo i certificati che sono registrati direttamente nell'applicazione e non seguono le catene di certificati.

Per informazioni dettagliate, [vedere Guida introduttiva: Configurare un'applicazione client per accedere alle API Web | Aggiungere credenziali all'applicazione](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Registrazione dei segreti client tramite PowerShell

In alternativa, è possibile registrare l'applicazione con Azure AD usando gli strumenti da riga di comando. L'esempio [Active-Directory-dotnetcore-Daemon-V2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) Mostra come registrare un segreto dell'applicazione o un certificato con un'applicazione Azure ad:

- Per informazioni dettagliate su come registrare un segreto dell'applicazione, vedere [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Per informazioni dettagliate su come registrare un certificato con l'applicazione, vedere [AppCreationScripts-withCert/configure. ps1.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)

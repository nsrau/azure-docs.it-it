---
title: File di inclusione
description: file di inclusione per uno scenario client riservato pagine (daemon, l'app Web, API Web) di destinazione
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66121837"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrazione dei segreti o certificati

Come per qualsiasi applicazione client riservato, è necessario registrare un certificato o chiave privata. È possibile registrare i segreti dell'applicazione tramite l'esperienza interattiva nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), o usando gli strumenti da riga di comando (ad esempio, PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>La registrazione di segreti client tramite il portale di registrazione dell'applicazione

La gestione delle credenziali client avviene nella **certificati e i segreti** pagina per un'applicazione:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- il segreto dell'applicazione (anche segreto client denominato) viene generato da Azure AD, durante la registrazione dell'applicazione client riservato. Questa generazione si verifica quando si seleziona **nuovo segreto client**. A questo punto, è necessario copiare la stringa del segreto negli Appunti per l'uso nell'app per la prima di selezionare **salvare**. Questa stringa non verrà più visualizzata.
- il caricamento del certificato nella registrazione dell'applicazione utilizzando il **carica certificato** pulsante

Per informazioni dettagliate, vedere [Guida introduttiva: Configurare un'applicazione client per accedere alle API web | Aggiungere le credenziali per l'applicazione](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Registrare i segreti client usando PowerShell

In alternativa, è possibile registrare l'applicazione con Azure AD mediante gli strumenti da riga di comando. Il [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) esempio mostra come registrare un certificato o un segreto dell'applicazione con un'applicazione Azure AD:

- Per informazioni dettagliate su come registrare un segreto dell'applicazione, vedere [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Per informazioni dettagliate su come registrare un certificato con l'applicazione, vedere [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
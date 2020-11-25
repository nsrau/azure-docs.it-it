---
title: includere file
description: file di inclusione per le pagine di destinazione dello scenario client riservato (daemon, app Web, API Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996008"
---
## <a name="add-a-client-secret-or-certificate"></a>Aggiungere un certificato o un segreto client

Come per qualsiasi applicazione client riservata, è necessario aggiungere un segreto o un certificato che funga da *credenziali* dell'applicazione in modo che possa autenticarsi come se stesso, senza l'interazione dell'utente.

È possibile aggiungere credenziali alla registrazione dell'app client usando il [portale di Azure](#add-client-credentials-by-using-the-azure-portal) o usando uno strumento da riga di comando come [PowerShell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Aggiungere le credenziali client usando il portale di Azure

Per aggiungere credenziali alla registrazione dell'app riservata dell'applicazione client, seguire i passaggi in [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../articles/active-directory/develop/quickstart-register-app.md) per il tipo di credenziale da aggiungere:

* [Aggiungere un segreto client](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Aggiungere un certificato](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Aggiungere credenziali client tramite PowerShell

In alternativa, è possibile aggiungere le credenziali quando si registra l'applicazione con la piattaforma di identità Microsoft usando PowerShell.

L'esempio di codice [Active-Directory-dotnetcore-Daemon-V2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) in GitHub Mostra come aggiungere un segreto o un certificato dell'applicazione durante la registrazione di un'applicazione:

- Per informazioni dettagliate su come aggiungere un **segreto client** con PowerShell, vedere [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Per informazioni dettagliate su come aggiungere un **certificato** con PowerShell, vedere [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).

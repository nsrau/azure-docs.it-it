---
author: mmacy
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 07/27/2020
ms.author: marsma
ms.openlocfilehash: 99ca8e45da27f5bce5258d55f46bcfa25b358239
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87311543"
---
> [!IMPORTANT]
> MSAL.js 2.0 attualmente non supporta Azure AD B2C per l'uso con il flusso del codice di autorizzazione PKCE. Al momento, per Azure AD B2C è consigliabile usare il flusso implicito come descritto in [Esercitazione: Registrare un'applicazione][implicit-flow]. Per seguire lo stato di questo problema, vedere il [wiki di MSAL.js][msal-wiki].

[github-issue]: https://github.com/AzureAD/microsoft-authentication-library-for-js/issues/1795
[implicit-flow]: ../articles/active-directory-b2c/tutorial-register-applications.md
[msal-wiki]: https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-browser-B2C-CORS-issue

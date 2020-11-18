---
title: La pagina di accesso ad Azure AD accetta account Microsoft? | Microsoft Docs
description: In che modo i messaggi visualizzati riflettono la ricerca del nome utente durante l'accesso
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a3c2257311f208ae704a54f1369e10b2ff160b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646849"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opzioni di accesso per gli account Microsoft in Azure Active Directory

La pagina di accesso a Microsoft 365 per Azure Active Directory (Azure AD) supporta gli account aziendali o dell'istituto di istruzione e gli account Microsoft, ma a seconda della situazione dell'utente, può supportare o l'uno o l'altro tipo oppure entrambi. Ad esempio, la pagina di accesso ad Azure AD supporta:

* App che accettano accessi da entrambi i tipi di account
* Organizzazioni che accettano guest

## <a name="identification"></a>Identificazione
È possibile stabilire se la pagina di accesso usata dall'organizzazione supporta gli account Microsoft esaminando il testo del suggerimento nel campo Nome utente. Se il testo del suggerimento è "Posta elettronica, telefono o Skype", la pagina di accesso supporta gli account Microsoft.

![Differenza tra le pagine di accesso dell'account](./media/signin-account-support/ui-prompt.png)

[Altre opzioni di accesso funzionano solo per gli account Microsoft personali](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ), ma non possono essere usate per l'accesso alle risorse di account aziendali o dell'istituto di istruzione.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare l'accesso](../fundamentals/add-custom-domain.md)
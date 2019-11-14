---
title: La pagina di accesso Azure AD accetta account Microsoft | Microsoft Docs
description: Come la messaggistica su schermo riflette la ricerca del nome utente durante l'accesso
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024293"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opzioni di accesso per gli account Microsoft in Azure Active Directory

La pagina di accesso Microsoft 365 per Azure Active Directory (Azure AD) supporta gli account aziendali o dell'Istituto di istruzione e gli account Microsoft, ma a seconda della situazione dell'utente, può essere uno o l'altro o entrambi. Ad esempio, la pagina di accesso Azure AD supporta:

* App che accettano accessi da entrambi i tipi di account
* Organizzazioni che accettano Guest

## <a name="identification"></a>Identificazione
È possibile stabilire se la pagina di accesso utilizzata dall'organizzazione supporta gli account Microsoft esaminando il testo del suggerimento nel campo nome utente. Se il testo del suggerimento dice "posta elettronica, telefono o Skype", la pagina di accesso supporta gli account Microsoft.

![Differenza tra le pagine di accesso dell'account](./media/signin-account-support/ui-prompt.png)

Le [Opzioni di accesso aggiuntive funzionano solo per gli account Microsoft personali](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , ma non possono essere usate per l'accesso alle risorse dell'account aziendale o dell'Istituto di istruzione.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare la personalizzazione dell'accesso](../fundamentals/add-custom-domain.md)
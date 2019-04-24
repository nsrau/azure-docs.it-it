---
title: Come sapere se una pagina di accesso Azure AD accetta gli account Microsoft | Microsoft Docs
description: La modalità su schermo messaggistica riflette ricerca nome utente durante l'accesso
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60468224"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Opzioni di accesso per gli account Microsoft in Azure Active Directory

La pagina di accesso di Microsoft 365 per Azure Active Directory (Azure AD) supporta aziendale o dell'istituto di istruzione e gli account Microsoft, ma a seconda della situazione dell'utente, potrebbe essere uno o l'altro o entrambi. Ad esempio, la pagina di accesso Azure AD supporta:

* App che accettano accessi da entrambi i tipi di account
* Organizzazioni che accettano gli utenti guest

## <a name="identification"></a>Identificazione
È possibile indicare se la pagina di accesso usato dall'organizzazione supporta gli account Microsoft, esaminando il testo di suggerimento nel campo username. Se viene visualizzato il testo di suggerimento "Messaggio di posta elettronica, telefono o Skype", la pagina di accesso supporta gli account Microsoft.

![Differenza tra le pagine di accesso account](./media/signin-account-support/ui-prompt.png)

[Opzioni di accesso aggiuntive valide solo per gli account Microsoft personali](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) ma non può essere usato per l'accesso all'azienda o dell'istituto di istruzione le risorse dell'account.

## <a name="next-steps"></a>Passaggi successivi

[Personalizzazione di accesso](../fundamentals/add-custom-domain.md)
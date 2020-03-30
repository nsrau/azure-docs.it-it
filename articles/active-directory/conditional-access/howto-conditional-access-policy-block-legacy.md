---
title: Accesso condizionale - Blocca autenticazione legacy - Azure Active DirectoryConditional Access - Block legacy authentication - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per bloccare i protocolli di autenticazione legacyCreate a custom Conditional Access policy to block legacy authentication protocols
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295227"
---
# <a name="conditional-access-block-legacy-authentication"></a>Accesso condizionale: bloccare l'autenticazione legacyConditional Access: Block legacy authentication

A causa dell'aumento dei rischi associati ai protocolli di autenticazione legacy, Microsoft consiglia alle organizzazioni di bloccare le richieste di autenticazione utilizzando questi protocolli e di richiedere l'autenticazione moderna.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I passaggi seguenti consentono di creare criteri di accesso condizionale per bloccare le richieste di autenticazione legacy. Questo criterio viene messo in [modalità Solo report](howto-conditional-access-report-only.md) per avviare in modo che gli amministratori possano determinare l'impatto che avranno sugli utenti esistenti. Quando gli amministratori hanno familiarità con cui il criterio si applica come previsto, possono passare a **On** o stage la distribuzione aggiungendo gruppi specifici ed escludendo altri.

1. Accedere al **portale** di Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare ad Accesso**condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni**selezionare **Utenti e gruppi**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Utenti e gruppi** e scegliere gli account che devono mantenere la possibilità di utilizzare l'autenticazione legacy. Escludere almeno un account per evitare che si sia bloccato. Se non si esclude alcun account, non sarà possibile creare questo criterio.
   1. Selezionare **Fatto**.
1. In **Azioni o app cloud**selezionare Tutte le app **cloud**.
   1. Selezionare **Fatto**.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**.
   1. Selezionare solo le caselle **App per dispositivi mobili e client** > desktop**Altri client**.
   1. Selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**, selezionare **Blocca accesso**.
   1. Selezionare **Seleziona**.
1. Confermare le impostazioni e **impostare Abilita criterio** su **Solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)

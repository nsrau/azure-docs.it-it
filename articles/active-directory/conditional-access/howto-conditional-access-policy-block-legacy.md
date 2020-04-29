---
title: Accesso condizionale-blocca autenticazione legacy-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per bloccare i protocolli di autenticazione legacy
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295227"
---
# <a name="conditional-access-block-legacy-authentication"></a>Accesso condizionale: blocca autenticazione legacy

A causa dell'aumento dei rischi associati ai protocolli di autenticazione legacy, Microsoft consiglia alle organizzazioni di bloccare le richieste di autenticazione usando questi protocolli e di richiedere l'autenticazione moderna.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per bloccare le richieste di autenticazione legacy. Questo criterio viene inserito in [modalità di sola segnalazione](howto-conditional-access-report-only.md) per iniziare, in modo che gli amministratori possano determinare l'effetto che avranno sugli utenti esistenti. Quando gli amministratori hanno dimestichezza con il fatto che i criteri si applicano come previsto, possono passare alla **distribuzione o eseguire** il staging tramite l'aggiunta di gruppi specifici ed escluderne altri.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **Security** > **accesso condizionale**di sicurezza.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere tutti gli account che devono mantenere la possibilità di usare l'autenticazione legacy. Escludere almeno un account per impedire che venga bloccato. Se non si esclude alcun account, non sarà possibile creare questo criterio.
   1. Seleziona **Chiudi**.
1. In **app Cloud o azioni**selezionare **tutte le app Cloud**.
   1. Seleziona **Chiudi**.
1. In **condizioni** > **app client (anteprima)** impostare **Configura** su **Sì**.
   1. Controllare solo le caselle **app per dispositivi mobili e client** > Desktop**altri client**.
   1. Seleziona **Chiudi**.
1. In **Access controls** > **concessione**controlli di accesso selezionare **Blocca accesso**.
   1. Selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **solo report**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)

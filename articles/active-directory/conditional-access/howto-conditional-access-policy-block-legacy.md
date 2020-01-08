---
title: Accesso condizionale-blocca autenticazione legacy-Azure Active Directory
description: Creare un criterio di accesso condizionale personalizzato per bloccare i protocolli di autenticazione legacy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c40993df8033b9dbc49c81e8db2f9f01c6de37d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424911"
---
# <a name="conditional-access-block-legacy-authentication"></a>Accesso condizionale: blocca autenticazione legacy

A causa dell'aumento dei rischi associati ai protocolli di autenticazione legacy, Microsoft consiglia alle organizzazioni di bloccare le richieste di autenticazione usando questi protocolli e di richiedere l'autenticazione moderna.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per bloccare le richieste di autenticazione legacy.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** **sicurezza** >  > **l'accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **assegnazioni**selezionare **utenti e gruppi** .
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **utenti e gruppi** e scegliere tutti gli account che devono mantenere la possibilità di usare l'autenticazione legacy. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni** selezionare **tutte le app Cloud**.
   1. Selezionare **Operazione completata**.
1. In **condizioni** > **app client (anteprima)** impostare **Configura** su **Sì**.
   1. Controllare solo le caselle **app per dispositivi mobili e client desktop** > **altri client**.
   1. Selezionare **Operazione completata**.
1. In **controlli di accesso** > **concedere**Selezionare **Blocca accesso**.
   1. Scegliere **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Determinare l'effetto usando la modalità solo report di accesso condizionale](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)

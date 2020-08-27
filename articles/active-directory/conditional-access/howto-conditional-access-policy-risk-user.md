---
title: Accesso condizionale basato sul rischio utente-Azure Active Directory
description: Creare criteri di accesso condizionale usando il rischio utente di Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c890c8e9af7310338e8470c4d56b46d1e259c168
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948063"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>Accesso condizionale: accesso condizionale basato sul rischio utente

Microsoft collabora con i ricercatori, le forze legali, diversi team di sicurezza di Microsoft e altre fonti attendibili per individuare le coppie di nome utente e password perse. Le organizzazioni con Azure AD Premium licenze P2 possono creare criteri di accesso condizionale che incorporano [Azure ad Identity Protection rilevamenti dei rischi utente](../identity-protection/concept-identity-protection-risks.md#user-risk). 

Questo criterio può essere assegnato a due posizioni. Le organizzazioni devono scegliere una delle opzioni seguenti per abilitare un criterio di accesso condizionale basato sul rischio utente che richiede una modifica sicura della password.

## <a name="enable-with-conditional-access-policy"></a>Abilita con criteri di accesso condizionale

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri. Per altre informazioni, [impostare gli standard di denominazione per i criteri](./plan-conditional-access.md#set-naming-standards-for-your-policies).
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**  >  **Includi**selezionare **tutte le app Cloud**.
1. In **Conditions**  >  **User Risk**impostare **Configure** su **Yes**. In **configurare i livelli di rischio utente necessari per l'applicazione dei criteri** selezionare **alta**, quindi fare clic su **fine**.
1. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi modifica della password**e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per creare e abilitare il criterio.

## <a name="enable-through-identity-protection"></a>Abilita tramite Identity Protection

1. Accedere al **portale di Azure**.
1. Selezionare **tutti i servizi**, quindi passare a **Azure ad Identity Protection**.
1. Selezionare **criteri di rischio utente**.
1. In **assegnazioni**selezionare **utenti**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass e selezionare **Seleziona**.
   1. Selezionare **Fine**.
1. In **condizioni**selezionare **rischio utente**, quindi fare clic **su alto**.
   1. Selezionare **Seleziona**, quindi **fine**.
1. In **Controlla**  >  **accesso**scegliere **Consenti accesso**, quindi selezionare **Richiedi modifica della password**.
   1. Scegliere **Seleziona**.
1. Impostare **Applica criterio** **su on**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Accesso condizionale basato sul rischio per l'accesso](howto-conditional-access-policy-risk.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
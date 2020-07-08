---
title: Accesso condizionale basato sul rischio di accesso-Azure Active Directory
description: Creare criteri di accesso condizionale usando il rischio di accesso a Identity Protection
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
ms.openlocfilehash: ce687ae1f47b20bb5fff3827e7bcbd5d7edf2d83
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024360"
---
# <a name="conditional-access-sign-in-risk-based-conditional-access"></a>Accesso condizionale: accesso condizionale basato sui rischi di accesso

La maggior parte degli utenti ha un comportamento normale monitorabile. In condizioni che esulano dalla normalità, potrebbe essere rischioso consentire loro semplicemente di accedere. Potrebbe essere necessario bloccare tale utente o chiedere loro di eseguire l'autenticazione a più fattori per dimostrare che si tratta effettivamente di chi afferma. 

Un rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. Le organizzazioni con Azure AD Premium licenze P2 possono creare criteri di accesso condizionale che incorporano [Azure ad Identity Protection rilevamenti dei rischi di](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)accesso.

Questo criterio può essere assegnato a due posizioni. Le organizzazioni devono scegliere una delle opzioni seguenti per abilitare un criterio di accesso condizionale basato sul rischio di accesso che richiede una modifica sicura della password.

## <a name="enable-with-conditional-access-policy"></a>Abilita con criteri di accesso condizionale

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **app Cloud o azioni**  >  **Includi**selezionare **tutte le app Cloud**.
1. In **Conditions**  >  **User Risk**impostare **Configure** su **Yes**. In **selezionare il livello di rischio di accesso a cui verranno applicati i criteri** 
   1. Selezionare **alta** e **media**.
   1. Selezionare **Operazione completata**.
1. In **Controlli di accesso** > **Concedi**, selezionare **Concedi accesso**, **Richiedi autenticazione a più fattori** e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

## <a name="enable-through-identity-protection"></a>Abilita tramite Identity Protection

1. Accedere al **portale di Azure**.
1. Selezionare **tutti i servizi**, quindi passare a **Azure ad Identity Protection**.
1. Selezionare i **criteri di rischio di accesso**.
1. In **assegnazioni**selezionare **utenti**.
   1. In **Includi** selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass e selezionare **Seleziona**.
   1. Selezionare **Operazione completata**.
1. In **condizioni**selezionare **rischio di accesso**, quindi scegliere **media e versioni successive**.
   1. Selezionare **Seleziona**, quindi **fine**.
1. In **Controlla**  >  **accesso**scegliere **Consenti accesso**, quindi selezionare **Richiedi autenticazione**a più fattori.
   1. Scegliere **Seleziona**.
1. Impostare **Applica criterio** **su on**.
1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Accesso condizionale basato sul rischio utente](howto-conditional-access-policy-risk-user.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Informazioni su Azure Active Directory Identity Protection](../identity-protection/overview.md)

---
title: Conditional Access - Risk-based Conditional Access - Azure Active Directory
description: Creare criteri di accesso condizionale per abilitare i miglioramenti apportati a Identity Protection ai criteriCreate Conditional Access policies to enable Identity Protection enhancements to policies
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
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295148"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Conditional Access: Risk-based Conditional Access

Le organizzazioni con licenze di Azure AD Premium P2 possono creare criteri di accesso condizionale che incorporano i rilevamenti dei rischi di Azure AD Identity Protection.Organizations with Azure AD Premium P2 licenses can create Conditional Access policies incorporating Azure AD Identity Protection risk detections. Esistono tre criteri predefiniti che possono essere abilitati. 

* Richiedere a tutti gli utenti di registrarsi per Azure Multi-Factor Authentication.Require all users to register for Azure Multi-Factor Authentication.
* Richiedere una modifica della password per gli utenti ad alto rischio.
* Richiedere l'autenticazione a più fattori per gli utenti con rischio di accesso medio o alto.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Richiedere a tutti gli utenti di registrarsi per Azure Multi-Factor AuthenticationRequire all users to register for Azure Multi-Factor Authentication

L'abilitazione di questi criteri richiederà a tutti gli utenti di registrarsi per Azure Multi-Factor Authentication entro 14 giorni. 

1. Accedere al **portale**di Azure .
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **Registrazione MFA**.
1. In **Assegnazioni**selezionare **Utenti**.
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere gli account di accesso di emergenza o di vetro rotto dell'organizzazione e selezionare **Seleziona**. 
   1. Selezionare **Fatto**.
1. Impostare **Applica criterio** su **Attivato**.
1. Fare clic su **Salva**.

## <a name="require-a-password-change-high-risk-users"></a>Richiedere una modifica della password per gli utenti ad alto rischioRequire a password change high-risk users

Microsoft collabora con ricercatori, forze dell'ordine, diversi team di sicurezza di Microsoft e altre fonti attendibili per trovare coppie di nome utente e password. Quando una di queste coppie corrisponde a un account in un ambiente specifico, è possibile attivare una modifica della password basata sul rischio tramite i criteri seguenti.

1. Accedere al **portale**di Azure .
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **Criteri di rischio per l'utente**.
1. In **Assegnazioni**selezionare **Utenti**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere gli account di accesso di emergenza o di vetro rotto dell'organizzazione e selezionare **Seleziona**.
   1. Selezionare **Fatto**.
1. In **Condizioni**selezionare **Rischio utente**, quindi scegliere **Alta**.
   1. Fare clic su **Seleziona,** quindi **su Fine**.
1. In **Controlli** > **accesso**scegliere Consenti **accesso**e quindi selezionare Richiedi **modifica password**.
   1. Fare clic su **Seleziona**.
1. Impostare **Applica criterio** su **Attivato**.
1. Fare clic su **Salva**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Richiedere utenti a rischio di accesso elevato o di autenticazione a più fattoriRequire MFA medium or high sign-in users

La maggior parte degli utenti ha un comportamento normale monitorabile. In condizioni che esulano dalla normalità, potrebbe essere rischioso consentire loro semplicemente di accedere. Si consiglia di bloccare tale utente o forse solo chiedere loro di eseguire l'autenticazione a più fattori per dimostrare che sono davvero chi dicono di essere. Per abilitare criteri che richiedono MFA quando viene rilevato un accesso rischioso, procedere come segue.

1. Accedere al **portale**di Azure .
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **Criteri di rischio di accesso**
1. In **Assegnazioni**selezionare **Utenti**
   1. In **Includi**selezionare **Tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere gli account di accesso di emergenza o di vetro rotto dell'organizzazione e selezionare **Seleziona**.
   1. Selezionare **Fatto**.
1. In **Condizioni**selezionare **Rischio di accesso**, quindi scegliere Medio e **oltre**.
   1. Fare clic su **Seleziona,** quindi **su Fine**.
1. In **Accesso ai controlli** > **Access**scegliere **Consenti accesso**, quindi selezionare **Richiedi autenticazione a più fattori**.
   1. Fare clic su **Seleziona**.
1. Impostare **Applica criterio** su **Attivato**.
1. Fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)

[Funzionamento di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview.md)

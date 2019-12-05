---
title: Accesso condizionale-accesso condizionale basato sul rischio-Azure Active Directory
description: Creare criteri di accesso condizionale per abilitare i miglioramenti di Identity Protection per i criteri
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4130b05be1a4abb2166514b730c3d21bd5ece97
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803581"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Accesso condizionale: accesso condizionale basato sul rischio

Le organizzazioni con Azure AD Premium licenze P2 possono creare criteri di accesso condizionale che incorporano Azure AD Identity Protection rilevamento dei rischi. Sono disponibili tre criteri predefiniti che è possibile abilitare. 

* Richiedere a tutti gli utenti di registrarsi per Multi-Factor Authentication di Azure.
* Richiedere una modifica della password per gli utenti ad alto rischio.
* Richiedere l'autenticazione a più fattori per gli utenti con rischi di accesso medio o elevato.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Richiedi la registrazione di tutti gli utenti per Multi-Factor Authentication di Azure

L'abilitazione di questo criterio richiede che tutti gli utenti si registrino per Multi-Factor Authentication di Azure entro 14 giorni. 

1. Accedere al **portale di Azure**.
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **Registrazione MFA**.
1. In **assegnazioni**selezionare **utenti**.
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass e selezionare **Seleziona**. 
   1. Selezionare **Operazione completata**.
1. Impostare **Applica criterio** **su on**.
1. Fare clic su **Salva**

## <a name="require-a-password-change-high-risk-users"></a>Richiedere una modifica della password per gli utenti ad alto rischio

Microsoft collabora con ricercatori, forze dell'ordine, diversi team di sicurezza di Microsoft e altre fonti attendibili per trovare coppie di nome utente e password. Quando una di queste coppie corrisponde a un account in un ambiente specifico, è possibile attivare una modifica della password basata sul rischio tramite i criteri seguenti.

1. Accedere al **portale di Azure**.
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **criteri di rischio utente**.
1. In **assegnazioni**selezionare **utenti**
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass e selezionare **Seleziona**.
   1. Selezionare **Operazione completata**.
1. In **condizioni**selezionare **rischio utente**, quindi fare clic **su alto**.
   1. Fare clic su **Seleziona** e quindi su **fine**.
1. In **controls** > **Access**scegliere **Allow Access**e quindi selezionare **Richiedi modifica della password**.
   1. Fare clic su **Seleziona**.
1. Impostare **Applica criterio** **su on**.
1. Fare clic su **Salva**

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Richiedi utenti di rischio di accesso medio o alto per l'autenticazione a più fattori

La maggior parte degli utenti ha un comportamento normale monitorabile. In condizioni che esulano dalla normalità, potrebbe essere rischioso consentire loro semplicemente di accedere. Potrebbe essere necessario bloccare tale utente o chiedere loro di eseguire l'autenticazione a più fattori per dimostrare che si tratta effettivamente di chi afferma. Per abilitare criteri che richiedono MFA quando viene rilevato un accesso rischioso, procedere come segue.

1. Accedere al **portale di Azure**.
1. Fare clic su **Tutti i servizi** e quindi passare ad **Azure AD Identity Protection**.
1. Fare clic su **criteri di rischio di accesso**
1. In **assegnazioni**selezionare **utenti**
   1. In **Includi**selezionare **tutti gli utenti**.
   1. In **Escludi**selezionare **Seleziona utenti esclusi**, scegliere l'accesso di emergenza dell'organizzazione o gli account break-Glass e selezionare **Seleziona**.
   1. Selezionare **Operazione completata**.
1. In **condizioni**selezionare **rischio di accesso**, quindi scegliere **media e versioni successive**.
   1. Fare clic su **Seleziona** e quindi su **fine**.
1. In **controlli** > **accesso**scegliere **Consenti accesso**, quindi selezionare **Richiedi autenticazione**a più fattori.
   1. Fare clic su **Seleziona**.
1. Impostare **Applica criterio** **su on**.
1. Fare clic su **Salva**

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

[Simulare il comportamento di accesso usando lo strumento di What If dell'accesso condizionale](troubleshoot-conditional-access-what-if.md)

[Funzionamento di Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Cos'è Azure Active Directory Identity Protection?](../identity-protection/overview.md)

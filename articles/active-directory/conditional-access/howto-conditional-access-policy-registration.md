---
title: Accesso condizionale - Informazioni di sicurezza combinate - Azure Active DirectoryConditional Access - Combined security information - Azure Active Directory
description: Creare criteri di accesso condizionale personalizzati per la registrazione delle informazioni di sicurezzaCreate a custom Conditional Access policy for security info registration
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
ms.openlocfilehash: 4f69a94e17155ff93510d09f666bce12f628274f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295165"
---
# <a name="conditional-access-securing-security-info-registration"></a>Accesso condizionale: protezione della registrazione delle informazioni di sicurezzaConditional Access: Securing security info registration

Garantire quando e come gli utenti si registrano per Azure Multi-Factor Authentication e la reimpostazione della password self-service è ora possibile con le azioni dell'utente nei criteri di accesso condizionale. Questa funzione di anteprima è disponibile per le organizzazioni che hanno abilitato [l'anteprima di registrazione combinata.](../authentication/concept-registration-mfa-sspr-combined.md) Questa funzionalità può essere abilitata nelle organizzazioni in cui desiderano usare condizioni come il percorso di rete attendibile per limitare l'accesso per la registrazione per Azure Multi-Factor Authentication e la reimpostazione della password self-service (SSPR). Per ulteriori informazioni sulle condizioni utilizzabili, vedere l'articolo [Accesso condizionale: Condizioni](concept-conditional-access-conditions.md).

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Creare un criterio per richiedere la registrazione da un percorso attendibileCreate a policy to require registration from a trusted location

Il criterio seguente si applica a tutti gli utenti selezionati, che tentano di registrarsi utilizzando l'esperienza di registrazione combinata, e bloccano l'accesso a meno che non si connettano da una posizione contrassegnata come rete attendibile.

1. Nel **portale di Azure**passare ad**Accesso condizionale**alla**sicurezza** > di **Azure Active Directory** > .
1. Selezionare **Nuovi criteri**.
1. In Nome immettere un Nome per il criterio. Ad esempio, Registrazione combinata delle informazioni di **sicurezza su reti attendibili**.
1. In **Assegnazioni**selezionare **Utenti e gruppi**e selezionare gli utenti e i gruppi a cui si desidera applicare il criterio.

   > [!WARNING]
   > Gli utenti devono essere abilitati per [l'anteprima di registrazione combinata](../authentication/howto-registration-mfa-sspr-combined.md).

1. In **Azioni o app cloud**selezionare Azioni **utente**, selezionare Registra informazioni di **sicurezza (anteprima)**.
1. In **Condizioni** > **Posizioni**.
   1. Configura **Sì**.
   1. Includi **qualsiasi posizione**.
   1. Escludi **tutti i percorsi attendibili**.
   1. Selezionare **Fatto** nel pannello Posizioni.
   1. Selezionare Fatto nel pannello Condizioni.Select **Done** on the Conditions blade.
1. In **Condizioni** > **App client (anteprima)** impostare **Configura** su **Sì**e selezionare **Fatto**.
1. In **Controlli di** > accesso**Concedi**.
   1. Selezionare **Blocca accesso**.
   1. Quindi fare clic su **Seleziona**.
1. Impostare **Abilita criterio** su **Sì**.
1. Quindi selezionare **Salva**.

Al passaggio 6 di questo criterio, le organizzazioni hanno scelte che possono fare. Il criterio precedente richiede la registrazione da un percorso di rete attendibile. Le organizzazioni possono scegliere di utilizzare le condizioni disponibili al posto delle **sedi**. Tenere presente che questo criterio è un criterio di blocco, pertanto tutto ciò che è incluso viene bloccato e tutto ciò che non corrisponde all'inclusione è consentito. 

Alcuni possono scegliere di utilizzare lo stato del dispositivo anziché la posizione nel passaggio 6 precedente:Some may choose to use device state instead of location in step 6 above:

6. In **Condizioni** > **Stato dispositivo (anteprima)**.
   1. Configura **Sì**.
   1. Includi **tutto lo stato del dispositivo**.
   1. Escludi dispositivo ibrido Azure AD aggiunto e/o **dispositivo contrassegnato come conformeExclude** **Device Hybrid Azure AD joined** and/or Device marked as compliant
   1. Selezionare **Fatto** nel pannello Posizioni.
   1. Selezionare Fatto nel pannello Condizioni.Select **Done** on the Conditions blade.

> [!WARNING]
> Se si utilizza lo stato del dispositivo come condizione nei criteri, ciò potrebbe influire sugli utenti guest nella directory. [La modalità Solo report](concept-conditional-access-report-only.md) consente di determinare l'impatto delle decisioni relative ai criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

[Determinare l'impatto utilizzando la modalità solo report di accesso condizionaleDetermine impact using Conditional Access report-only](howto-conditional-access-report-only.md)

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)

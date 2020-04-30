---
title: Configurare Multi-Factor Authentication di Azure per desktop virtuale Windows-Azure
description: Come configurare Multi-Factor Authentication di Azure per una maggiore sicurezza nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 069d2a153e307ed94032ce1d980f26521969fc56
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508344"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Abilitare Multi-Factor Authentication di Azure per desktop virtuale Windows

Il desktop virtuale Windows client per Windows è un'ottima opzione per l'integrazione di desktop virtuali Windows con il computer locale. Tuttavia, quando si configura l'account desktop virtuale di Windows nel client Windows, è necessario adottare alcune misure per proteggere gli utenti.

Quando si accede per la prima volta, il client richiede il nome utente, la password e l'autenticazione a più fattori di Azure. Successivamente, al successivo accesso, il client memorizzerà il token dall'applicazione aziendale Azure Active Directory (AD). Quando si seleziona **memorizza**, gli utenti possono accedere dopo aver riavviato il client senza dover immettere nuovamente le credenziali.

Sebbene la memorizzazione delle credenziali sia comoda, può anche rendere le distribuzioni in scenari aziendali o dispositivi personali meno sicure. Per proteggere gli utenti, è necessario assicurarsi che il client continui a richiedere le credenziali di Azure Multi-Factor Authentication (multi-factor authentication). In questo articolo viene illustrato come configurare i criteri di accesso condizionale per desktop virtuale Windows per abilitare questa impostazione.

## <a name="prerequisites"></a>Prerequisiti

Ecco gli elementi necessari per iniziare:

- Assegnare agli utenti una licenza che includa Azure Active Directory Premium P1 o P2.
- Gruppo di Azure Active Directory con gli utenti assegnati come membri del gruppo.
- Abilitare l'autenticazione a più fattori di Azure per tutti gli utenti. Per ulteriori informazioni su come eseguire questa operazione, vedere [come richiedere la verifica in due passaggi per un utente](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> L'impostazione seguente si applica anche al [client Web desktop virtuale di Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

In questa sezione viene illustrato come creare un criterio di accesso condizionale che richiede l'autenticazione a più fattori quando ci si connette al desktop virtuale di Windows.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare a **Azure Active Directory** > **Security** > **accesso condizionale**di sicurezza.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome al criterio. È consigliabile che le organizzazioni creino uno standard significativo per i nomi dei propri criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi**selezionare **Seleziona utenti e gruppi** > **utenti e** gruppi > scegliere il gruppo creato nella fase prerequisiti.
   1. Seleziona **Chiudi**.
1. In **app Cloud o azioni** > **Includi**selezionare **Seleziona app**.
   1. Scegliere **desktop virtuale Windows** e **client desktop virtuale di Windows** **, quindi selezionare** **fine**.
   ![Screenshot della pagina app o azioni cloud. Il desktop virtuale Windows e le app client desktop virtuali Windows sono evidenziate in rosso.](media/cloud-apps-enterprise-selected.png)
1. In **controllo** > di accesso**concedere**selezionare **Concedi accesso**, **Richiedi autenticazione**a più fattori e quindi **selezionare**.
1. In **Access controls** > **sessione**controlli di accesso selezionare **frequenza**di accesso, impostare il valore su **1** e unità su **ore**, quindi **selezionare**.
1. Confermare le impostazioni e impostare **Abilita criterio** **su on**.
1. Selezionare **Crea** per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui criteri di accesso condizionale](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Altre informazioni sulla frequenza di accesso degli utenti](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

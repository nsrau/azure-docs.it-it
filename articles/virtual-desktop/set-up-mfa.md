---
title: Configurare Multi-Factor Authentication di Azure per desktop virtuale Windows-Azure
description: Come configurare Multi-Factor Authentication di Azure per una maggiore sicurezza nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e42ca0a0d0ff9d9df3dc42f1e165d1035d56d6a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009461"
---
# <a name="enable-azure-multi-factor-authentication-for-windows-virtual-desktop"></a>Abilitare l'autenticazione a più fattori di Azure per Desktop virtuale Windows

>[!IMPORTANT]
> Se si visita questa pagina dalla documentazione di Windows Virtual Desktop (classica), assicurarsi di [tornare alla documentazione di Windows Virtual Desktop (classica)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) al termine dell'operazione.

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

Ecco come creare un criterio di accesso condizionale che richiede l'autenticazione a più fattori quando ci si connette al desktop virtuale Windows:

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
2. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
3. Selezionare **Nuovi criteri**.
4. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
5. In **Assegnazioni** selezionare **Utenti e gruppi**.
6. In **Includi**selezionare **Seleziona utenti e gruppi**  >  **utenti e gruppi** > scegliere il gruppo creato nella fase dei [prerequisiti](#prerequisites) .
7. Selezionare **Fine**.
8. In **app Cloud o azioni**  >  **Includi**selezionare **Seleziona app**.
9. Selezionare uno dei seguenti gruppi di app in base alla versione di desktop virtuale di Windows in uso.
   - Se si usa desktop virtuale di Windows (versione classica), scegliere le due app seguenti:
       - **Desktop virtuale Windows** (ID app 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Client desktop virtuale Windows** (ID app fa4345a4-A730-4230-84a8-7d9651b86739)
   - Se si usa desktop virtuale di Windows, scegliere le due app seguenti:
       -  **Desktop virtuale Windows** (ID app 9cdead84-A844-4324-93f2-b2e6bb768d07)
       -  **Client desktop virtuale Windows** (ID app a85cf173-4192-42F8-81fa-777a763e6e2c)

   >[!IMPORTANT]
   > Per il client Web vengono usate le app client desktop virtuali di Windows. Tuttavia, non selezionare l'app denominata Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Questa app viene usata solo per recuperare il feed dell'utente e non deve avere l'autenticazione a più fattori.

1. Dopo aver selezionato l'app, scegliere **Seleziona**, quindi fare clic su **fine**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della pagina app o azioni cloud. Il desktop virtuale Windows e le app client desktop virtuali Windows sono evidenziate in rosso.](media/cloud-apps-enterprise.png)

   >[!NOTE]
   >Per trovare l'ID app dell'app che si vuole selezionare, passare ad **applicazioni aziendali** e selezionare **applicazioni Microsoft** dal menu a discesa tipo di applicazione.

10. In **controllo di accesso**  >  **concedere**selezionare **Concedi accesso**, **Richiedi autenticazione**a più fattori e quindi **selezionare**.
11. In **sessione controlli di accesso**  >  **Session**selezionare **frequenza**di accesso, impostare il valore su **1** e unità su **ore**, quindi selezionare **Seleziona**.
12. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
13. Selezionare **Crea** per abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sui criteri di accesso condizionale](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Altre informazioni sulla frequenza di accesso degli utenti](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

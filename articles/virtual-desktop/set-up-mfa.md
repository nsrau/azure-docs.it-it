---
title: Configurare Azure Multifactor Authentication per desktop virtuale Windows-Azure
description: Come configurare l'autenticazione a più fattori di Azure per una maggiore sicurezza nel desktop virtuale di Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80998482"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurare Azure Multi-Factor Authentication

Il desktop virtuale Windows client per Windows è un'ottima opzione per l'integrazione di desktop virtuali Windows con il computer locale. Tuttavia, quando si configura l'account desktop virtuale di Windows nel client Windows, è necessario adottare alcune misure per proteggere gli utenti.

Quando si accede per la prima volta, il client richiede il nome utente, la password e l'autenticazione a più fattori di Azure. Successivamente, al successivo accesso, il client memorizzerà il token dall'applicazione aziendale Azure Active Directory (AD). Quando si seleziona **memorizza**, gli utenti possono accedere dopo aver riavviato il client senza dover immettere nuovamente le credenziali.

Sebbene la memorizzazione delle credenziali sia comoda, può anche rendere le distribuzioni in scenari aziendali o dispositivi personali meno sicure. Per proteggere gli utenti, è necessario assicurarsi che il client continui a richiedere le credenziali di Azure Multi-Factor Authentication (multi-factor authentication). In questo articolo viene illustrato come configurare i criteri di accesso condizionale per desktop virtuale Windows per abilitare questa impostazione.

## <a name="prerequisites"></a>Prerequisiti

Ecco gli elementi necessari per iniziare:

- Assegnare a tutti gli utenti una delle licenze seguenti:
  - Microsoft 365 E3 o E5
  - Azure Active Directory Premium P1 o P2
  - Enterprise Mobility + Security E3 o E5
- Gruppo di Azure Active Directory con gli utenti assegnati come membri del gruppo.
- Abilitare l'autenticazione a più fattori di Azure per tutti gli utenti. Per ulteriori informazioni su come eseguire questa operazione, vedere [come richiedere la verifica in due passaggi per un utente](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

>[!NOTE]
>L'impostazione seguente si applica anche al [client Web desktop virtuale di Windows](https://rdweb.wvd.microsoft.com/webclient/index.html).

## <a name="opt-in-to-the-conditional-access-policy"></a>Acconsentire esplicitamente ai criteri di accesso condizionale

1. Aprire **Azure Active Directory**.

2. Passare alla scheda **tutte le applicazioni** . Nel menu a discesa "tipo di applicazione" selezionare **applicazioni aziendali**, quindi cercare **client desktop virtuale di Windows**.

    ![Screenshot della scheda tutte le applicazioni. L'utente ha immesso "Windows Virtual Desktop client" nella barra di ricerca e l'app è stata visualizzata nei risultati della ricerca.](media/all-applications-search.png)

3. Selezionare **accesso condizionale**.

    ![Screenshot che mostra l'utente che posiziona il cursore del mouse sulla scheda accesso condizionale.](media/conditional-access-location.png)

4. Selezionare **+ nuovo criterio**.

   ![Screenshot della pagina di accesso condizionale. L'utente sta posizionando il cursore del mouse sul pulsante nuovo criterio.](media/new-policy-button.png)

5. Immettere un **nome** per la **regola**, quindi **selezionare** il nome del **gruppo** creato nei prerequisiti.

6. Selezionare **Seleziona**, quindi fare clic su **fine**.

7. Aprire quindi le **app Cloud o le azioni**.

8. Nel pannello **Seleziona** selezionare l'app Enterprise **desktop virtuale di Windows** .

    ![Screenshot della pagina app o azioni cloud. L'utente ha selezionato l'app desktop virtuale Windows selezionando il segno di spunta accanto. L'app selezionata è evidenziata in rosso.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >È anche possibile visualizzare l'app client desktop virtuale di Windows selezionata sul lato sinistro dello schermo, come illustrato nella figura seguente. Per il corretto funzionamento del criterio sono necessarie sia il desktop virtuale Windows sia le app client di desktop virtuali Windows.
    >
    > ![Screenshot della pagina app o azioni cloud. Il desktop virtuale Windows e le app client desktop virtuali Windows sono evidenziate in rosso.](media/cloud-apps-enterprise-selected.png)

9. Selezionare **Seleziona**

10. Aprire quindi **Grant** 

11. Selezionare **Richiedi autenticazione**a più fattori e quindi selezionare **Richiedi uno dei controlli selezionati**.
   
    ![Screenshot della pagina di concessione. È selezionata l'opzione "Richiedi autenticazione a più fattori".](media/grant-page.png)

    >[!NOTE]
    >Se nell'organizzazione sono presenti dispositivi registrati in MDM e non si vuole che mostrino la richiesta di autenticazione a più fattori, è anche possibile selezionare Richiedi che il **dispositivo sia contrassegnato come conforme**.

12. Selezionare **sessione**.

13. Impostare la **frequenza di accesso** su **attivo**, quindi impostare il relativo valore su **1 ore**.

    ![Screenshot della pagina della sessione. Il menu della sessione Mostra che i menu a discesa della frequenza di accesso sono stati modificati in "1" e "ore".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Le sessioni attive nell'ambiente desktop virtuale Windows continueranno a funzionare quando si modificano i criteri. Tuttavia, se si esegue la disconnessione o la disconnessione, sarà necessario specificare nuovamente le credenziali dopo 60 minuti. Quando si modificano le impostazioni, è possibile estendere il periodo di timeout nel modo desiderato (purché sia allineato ai criteri di sicurezza dell'organizzazione).
    >
    >L'impostazione predefinita è una finestra in sequenza di 90 giorni, il che significa che il client chiederà agli utenti di eseguire di nuovo l'accesso quando tentano di accedere a una risorsa dopo che il computer è stato inattivo per più di 90 giorni.

14. Abilitare i criteri.

15. Selezionare **Crea** per confermare i criteri.

La procedura è terminata. È possibile testare i criteri per assicurarsi che l'elenco Consenti funzioni come previsto.

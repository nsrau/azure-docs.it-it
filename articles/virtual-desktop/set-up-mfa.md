---
title: Configurare l'autenticazione a più fattori di Azure per Windows Virtual Desktop - AzureSet up Azure multi-factor authentication for Windows Virtual Desktop - Azure
description: Come configurare l'autenticazione a più fattori di Azure per una maggiore sicurezza in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b3c47e1bbe5efdc5ee303305e52a785a49d0c00
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586873"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Configurare Azure Multi-Factor AuthenticationSet up Azure Multi-Factor Authentication

Il client Windows per Windows Virtual Desktop è un'opzione eccellente per l'integrazione di Windows Virtual Desktop con il computer locale. Tuttavia, quando si configura l'account desktop virtuale di Windows nel client Windows, è necessario adottare alcune misure per proteggere se stessi e gli utenti.

Quando si accede per la prima volta, il client richiede il nome utente, la password e l'autenticazione a più fattori di Azure.When you first sign in, the client asks for your username, password, and Azure MFA. Dopodiché, al successivo accesso, il client ricorderà il token dall'applicazione aziendale di Azure Active Directory (AD). Quando si seleziona **Memorizza ricordi**, gli utenti possono accedere dopo aver riavviato il client senza dover immettere nuovamente le credenziali.

Sebbene la memorizzazione delle credenziali sia utile, può anche rendere meno sicure le distribuzioni in scenari Aziendali o dispositivi personali. Per proteggere gli utenti, è necessario assicurarsi che il client continua a richiedere le credenziali di Azure Multi-Factor Authentication (MFA). In questo articolo verrà illustrato come configurare i criteri di accesso condizionale per Windows Virtual Desktop per abilitare questa impostazione.

## <a name="prerequisites"></a>Prerequisiti

Ecco cosa ti serve per iniziare:

- Assegnare a tutti gli utenti una delle seguenti licenze:
  - Microsoft 365 E3 o E5
  - Azure Active Directory Premium P1 o P2
  - Mobilità aziendale - Sicurezza E3 o E5
- Gruppo di Azure Active Directory con gli utenti assegnati come membri del gruppo.
- Abilitare Azure MFA per tutti gli utenti. Per ulteriori informazioni su come eseguire questa operazione, vedere Come richiedere la [verifica in due passaggi per un utente](/active-directory/authentication/howto-mfa-userstates).

>[!NOTE]
>L'impostazione seguente si applica anche al client Web desktop [virtuale di Windows.](https://rdweb.wvd.microsoft.com/webclient/index.html)

## <a name="opt-in-to-the-conditional-access-policy"></a>Attivare i criteri di accesso condizionaleOpt in to the Conditional Access policy

1. Aprire **Azure Active Directory**.

2. Passare alla scheda **Tutte le applicazioni.** Nel menu a discesa "Tipo di applicazione" selezionare **Applicazioni aziendali**, quindi cercare Client desktop **virtuale Windows**.

    ![Schermata della scheda Tutte le applicazioni. L'utente ha immesso "client desktop virtuale windows" nella barra di ricerca e l'app è stata visualizzata nei risultati della ricerca.](media/all-applications-search.png)

3. Selezionare **Accesso condizionale**.

    ![Schermata che mostra all'utente il cursore del mouse sulla scheda Accesso condizionale.](media/conditional-access-location.png)

4. Selezionare **: Nuovo criterio**.

   ![Schermata della pagina Accesso condizionale. L'utente passa il cursore del mouse sul pulsante del nuovo criterio.](media/new-policy-button.png)

5. Immettere un **nome** per la **regola**, quindi **selezionare** il nome del **gruppo** creato nei prerequisiti.

6. Selezionare **Seleziona**, quindi **Fatto**.

7. Successivamente, apri **App cloud o azioni**.

8. Nel pannello **Seleziona** selezionare l'app **Windows Virtual Desktop** Enterprise.

    ![Screenshot della pagina Delle app o delle azioni cloud. L'utente ha selezionato l'app Desktop virtuale di Windows selezionando il segno di spunta accanto ad essa. L'app selezionata viene evidenziata in rosso.](media/cloud-apps-select.png)
    
    >[!NOTE]
    >Dovresti anche vedere l'app Windows Virtual Desktop Client selezionata sul lato sinistro dello schermo, come mostrato nell'immagine seguente. Per il funzionamento dei criteri sono necessarie sia le app Desktop virtuale Windows che le app Client Enterprise di Windows Desktop virtuale.
    >
    > ![Screenshot della pagina Delle app o delle azioni cloud. Le app Desktop virtuale di Windows e Client desktop virtuale di Windows sono evidenziate in rosso.](media/cloud-apps-enterprise-selected.png)

9. Seleziona **Seleziona**

10. Successivamente, apri **Grant** 

11. Selezionare **Richiedi autenticazione a più fattori**, quindi Richiedi uno dei controlli **selezionati**.
   
    ![Cattura di schermata della pagina Concedi. "Richiedi autenticazione a più fattori" è selezionata.](media/grant-page.png)

    >[!NOTE]
    >Se nell'organizzazione sono presenti dispositivi registrati MDM e non si desidera che vengano visualizzati i prompt di autenticazione a più fattori, è anche possibile selezionare Richiedi che il **dispositivo venga contrassegnato come conforme.**

12. Selezionare **Sessione**.

13. Impostare la **frequenza di accesso** su **Attivo**, quindi modificarne il valore su **1 ora**.

    ![Cattura di schermata della pagina Sessione. Il menu della sessione mostra che i menu a discesa della frequenza di accesso sono stati modificati in "1" e "Ore".](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Le sessioni attive nell'ambiente Windows Virtual Desktop continueranno a funzionare quando si modificano i criteri. Tuttavia, se ti disconnetti o disconnetti, dovrai fornire nuovamente le tue credenziali dopo 60 minuti. Quando si modificano le impostazioni, è possibile estendere il periodo di timeout quanto si desidera, purché sia allineato ai criteri di sicurezza dell'organizzazione.
    >
    >L'impostazione predefinita è una finestra in sequenza di 90 giorni, il che significa che il client chiederà agli utenti di accedere nuovamente quando tentano di accedere a una risorsa dopo essere stati inattivi sul proprio computer per 90 giorni o più.

14. Abilitare il criterio.

15. Selezionare **Crea** per confermare il criterio.

La procedura è terminata. È possibile testare la politica per assicurarsi che l'elenco Consenti funzioni come previsto.

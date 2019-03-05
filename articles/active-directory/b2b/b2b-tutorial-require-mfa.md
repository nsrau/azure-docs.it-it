---
title: 'Esercitazione: Autenticazione a più fattori per B2B - Azure Active Directory | Microsoft Docs'
description: Informazioni su come richiedere l'autenticazione a più fattori (MFA) quando si usa Azure AD B2B per collaborare con utenti esterni e organizzazioni partner.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70f6718c13534e7f43b183400a1ccf25c3f8d1e1
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669008"
---
# <a name="tutorial-enforce-multi-factor-authentication-for-b2b-guest-users"></a>Esercitazione: Imporre l'autenticazione a più fattori per gli utenti guest B2B

Quando si collabora con utenti guest B2B esterni, è consigliabile proteggere le app con criteri di autenticazione a più fattori (MFA). Per gli utenti esterni non saranno sufficienti un nome utente e una password per accedere alle risorse. In Azure Active Directory (Azure AD) è possibile raggiungere questo obiettivo con i criteri di accesso condizionale che richiedono MFA per l'accesso. I criteri MFA possono essere applicati a livello di tenant, di app o di singolo utente, così come vengono abilitati per i membri dell'organizzazione.

Esempio:

![App B2B che richiede MFA](media/tutorial-mfa/aad-b2b-mfa-example.png)

1.  Un amministratore o un dipendente della Società A invita un utente guest a usare un'applicazione cloud o locale configurata per richiedere l'autenticazione MFA per l'accesso.
2.  L'utente guest accede con la propria identità aziendale, dell'istituto di istruzione o di social networking. 
3.  All'utente viene richiesto di completare una richiesta di autenticazione MFA. 
4.  L'utente configura l'autenticazione MFA con la Società A e ne sceglie l'opzione MFA. All'utente viene consentito l'accesso all'applicazione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Testare l'esperienza di accesso prima della configurazione di MFA.
> * Creare criteri di accesso condizionale che richiedono MFA per l'accesso a un'app cloud nell'ambiente. In questa esercitazione si userà l'app di gestione di Microsoft Azure per illustrare il processo.
> * Usare lo strumento What If per simulare l'accesso MFA.
> * Testare i criteri di accesso condizionale.
> * Eliminare l'utente e i criteri di test.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare lo scenario in questa esercitazione, sono necessari gli elementi seguenti:

 - **Accesso ad Azure AD Premium**, che include le funzionalità dei criteri di accesso condizionale. Per imporre MFA, è necessario creare un criterio di accesso condizionale di Azure AD. Si noti che i criteri MFA vengono sempre applicati nell'organizzazione, indipendentemente dal fatto che il partner abbia funzionalità MFA. Se si configura l'autenticazione MFA per l'organizzazione, sarà necessario assicurarsi di avere un numero sufficiente di licenze Azure AD Premium per gli utenti guest. 
 - **Un account di posta elettronica esterno valido** che è possibile aggiungere alla directory del tenant come utente guest e usarlo per accedere. Se non si sa come creare un account guest, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md).

## <a name="create-a-test-guest-user-in-azure-ad"></a>Creare un utente guest di test in Azure AD

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3.  In **Gestisci** selezionare **Utenti**.
4.  Selezionare **Nuovo utente guest**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-user-3.png)

5.  In **Nome utente** immettere l'indirizzo di posta elettronica dell'utente esterno. Se si vuole, includere un messaggio di benvenuto. 

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-user-4.png)

6.  Selezionare **Invita** per inviare automaticamente l'invito all'utente guest. Viene visualizzato il messaggio **L'utente è stato invitato**. 
7.  Dopo aver inviato l'invito, l'account utente viene automaticamente aggiunto alla directory come guest.

## <a name="test-the-sign-in-experience-before-mfa-setup"></a>Testare l'esperienza di accesso prima della configurazione di MFA
1.  Usare il nome utente e la password di test per accedere al [portale di Azure](https://portal.azure.com/).
2.  Si noti che è possibile accedere al portale di Azure usando solo le credenziali di accesso. Non è necessaria un'autenticazione aggiuntiva.
3.  Uscire,

## <a name="create-a-conditional-access-policy-that-requires-mfa"></a>Creare criteri di accesso condizionale che richiedono MFA
1.  Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
2.  Nel portale di Azure selezionare **Azure Active Directory**. 
3.  Nella sezione **Sicurezza** della pagina **Azure Active Directory** selezionare **Accesso condizionale**.
4.  Nella pagina **Accesso condizionale** selezionare **Nuovi criteri** nella barra degli strumenti in alto.
5.  Nella pagina **Nuovo** digitare **Richiedi MFA per l'accesso al portale B2B** nella casella di testo **Nome**.
6.  Nella sezione **Assegnazioni** selezionare **Utenti e gruppi**.
7.  Nella pagina **Utenti e gruppi** scegliere **Seleziona utenti e gruppi** e quindi selezionare **Tutti gli utenti guest (anteprima)**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-6.png)
9.  Selezionare **Operazione completata**.
10. Nella sezione **Assegnazioni** della pagina **Nuovo** selezionare **App cloud**.
11. Nella pagina **App cloud** scegliere **Selezionare le app** e quindi **Seleziona**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-10.png)

12. Nella pagina **Seleziona** scegliere **Gestione di Microsoft Azure** e quindi scegliere **Seleziona**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-11.png)

13. Nella pagina **App cloud** fare clic su **Fine**.
14. Nella sezione **Controlli di accesso** della pagina **Nuovo** selezionare **Concedi**.
15. Nella pagina **Concedi** scegliere **Concedi accesso**, selezionare la casella di controllo **Richiedi autenticazione a più fattori** e quindi scegliere **Seleziona**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-13.png)

16. In **Abilita criterio** selezionare **Sì**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-policy-14.png)

17. Selezionare **Create**.

## <a name="use-the-what-if-option-to-simulate-sign-in"></a>Usare l'opzione What If per simulare l'accesso

1.  Nella pagina **Accesso condizionale - Criteri** selezionare **What If**. 

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-1.png)

2.  Selezionare **Utente**, scegliere l'utente guest di test e quindi scegliere **Seleziona**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-2.png)

3.  Selezionare **App cloud**.
4.  Nella pagina **App cloud** scegliere **Selezionare le app** e quindi fare clic su **Seleziona**. Nell'elenco delle applicazioni selezionare **Gestione di Microsoft Azure** e quindi fare clic su **Seleziona**. 

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-3.png)

5.  Nella pagina **App cloud** fare clic su **Fine**.
6.  Selezionare **What If** e verificare che il nuovo criterio sia visualizzato in **Risultati valutazione** nella scheda **Criteri applicabili**.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/tutorial-mfa-whatif-4.png)

## <a name="test-your-conditional-access-policy"></a>Testare i criteri di accesso condizionale
1.  Usare il nome utente e la password di test per accedere al [portale di Azure](https://portal.azure.com/).
2.  Verrà visualizzata una richiesta per i metodi di autenticazione aggiuntivi. Si noti che potrebbe essere richiesto del tempo prima che il criterio diventi effettivo.

    ![Selezionare Azure Active Directory](media/tutorial-mfa/mfa-required.png)
 
3.  Uscire,

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non sono più necessari, eliminare l'utente e i criteri di accesso condizionale di test.
1.  Accedere al [portale di Azure](https://portal.azure.com/) come amministratore di Azure AD.
2.  Nel riquadro sinistro selezionare **Azure Active Directory**.
3.  In **Gestisci** selezionare **Utenti**.
4.  Selezionare l'utente di test e quindi selezionare **Elimina utente**.
5.  Nel riquadro sinistro selezionare **Azure Active Directory**.
6.  In **Sicurezza** selezionare **Accesso condizionale**.
7.  Nell'elenco **Nome criteri** selezionare il menu di scelta rapida (...) per i criteri di test e quindi scegliere **Elimina**. Selezionare **Sì** per confermare.
## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati creati criteri di accesso condizionale che richiedono agli utenti guest di usare MFA per l'accesso a una delle app cloud. Per altre informazioni sull'aggiunta di utenti guest per la collaborazione, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md).

---
title: Guida introduttiva - Richiedere Multi-Factor Authentication (MFA) per app specifiche con l'accesso condizionale di Azure Active Directory | Microsoft Docs
description: In questa guida introduttiva viene illustrato come è possibile collegare i requisiti di autenticazione al tipo di app cloud a cui si accede con l'accesso condizionale di Azure Active Directory (Azure AD).
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5ab513034d6e2946dcb31f3a31dbf86f14873e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895986"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Guida introduttiva: Richiedere MFA per app specifiche con l'accesso condizionale di Azure Active Directory

Per semplificare l'esperienza di accesso degli utenti, è possibile consentire di accedere alle app cloud usando un nome utente e una password. Molti ambienti hanno tuttavia almeno alcune applicazioni per cui è consigliabile richiedere una forma più affidabile di verifica dell'account, ad esempio Multi-Factor Authentication (MFA), come nel caso dell'accesso al sistema di posta elettronica dell'organizzazione o alle app per la gestione delle risorse umane. In Azure Active Directory (Azure AD) è possibile raggiungere questo obiettivo con i criteri di accesso condizionale.

Questa guida introduttiva illustra come configurare i [criteri di accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md) che richiedono Multi-Factor Authentication per un'app cloud selezionata dell'ambiente.

![Esempio di criteri di accesso condizionale nel portale di Azure](./media/app-based-mfa/32.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare lo scenario in questa guida introduttiva, sono necessari gli elementi seguenti:

- **Accesso a un'edizione Azure AD Premium**: l'accesso condizionale di Azure AD è una funzionalità di Azure AD Premium.

- **Account di test denominato Isabella Simonsen**: se non si conosce la procedura per creare un account di test, vedere [Aggiungere gli utenti basati su cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Per lo scenario di questo argomento di avvio rapido è necessario che il servizio Multi-Factor Authentication per utente non sia abilitato per l'account di test. Per altre informazioni, vedere [Come richiedere la verifica in due passaggi per un utente](../authentication/howto-mfa-userstates.md).

## <a name="test-your-sign-in"></a>Verificare il proprio accesso

L'obiettivo di questo passaggio è farsi un'idea dell'esperienza di accesso senza un criterio di accesso condizionale.

**Per inizializzare l'ambiente:**

1. Accedere al portale di Azure come Isabella Simonsen.
1. Uscire,

## <a name="create-your-conditional-access-policy"></a>Creare i criteri di accesso condizionale

Questa sezione illustra come creare i criteri di accesso condizionale necessari. Lo scenario di questa guida introduttiva usa:

- Portale di Azure come segnaposto per un'app cloud che richiede MFA. 
- Utente di esempio per testare i criteri di accesso condizionale.  

Nei criteri, impostare:

| Impostazione | Valore |
| --- | --- |
| Utenti e gruppi | Isabella Simonsen |
| App cloud | Gestione di Microsoft Azure |
| Concedere l'accesso | Richiedi autenticazione a più fattori |

![Criteri di accesso condizionale espansi](./media/app-based-mfa/31.png)

**Per configurare i criteri di accesso condizionale:**

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.

1. Sulla barra di spostamento a sinistra nel portale di Azure fare clic su **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Nella sezione **Sicurezza** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

   ![Accesso condizionale](./media/app-based-mfa/03.png)

1. Nella pagina **Accesso condizionale** fare clic su **Nuovi criteri** sulla barra degli strumenti in alto.

   ![Add](./media/app-based-mfa/04.png)

1. Nella pagina **Nuovo** digitare **Richiedi MFA per l'accesso al portale di Azure** nella casella di testo **Nome**.

   ![NOME](./media/app-based-mfa/05.png)

1. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

   ![Utenti e gruppi](./media/app-based-mfa/06.png)

1. Nella pagina **Utenti e gruppi** seguire questa procedura:

   ![Utenti e gruppi](./media/app-based-mfa/24.png)

   1. Fare clic su **Seleziona utenti e gruppi** e quindi selezionare **Utenti e gruppi**.

   1. Fare clic su **Seleziona**.

   1. Nella pagina **Seleziona** scegliere **Isabella Simonsen** e quindi fare clic su **Seleziona**.

   1. Nella pagina **Utenti e gruppi** fare clic su **Fatto**.

1. Fare clic su **App cloud**.

   ![App cloud](./media/app-based-mfa/08.png)

1. Nella pagina **App cloud** attenersi alla procedura seguente:

   ![Selezionare App cloud](./media/app-based-mfa/26.png)

   1. Fare clic su **Selezionare le app**.

   1. Fare clic su **Seleziona**.

   1. Nella pagina **Seleziona** selezionare **Gestione di Microsoft Azure** e quindi fare clic su **Seleziona**.

   1. Nella pagina **App cloud** fare clic su **Fatto**.

1. Nella sezione **Controlli di accesso** fare clic su **Concedi**.

   ![Controlli di accesso](./media/app-based-mfa/10.png)

1. Nella pagina **Concedi** seguire questa procedura:

   ![Concessione](./media/app-based-mfa/11.png)

   1. Selezionare **Concedi accesso**.

   1. Selezionare **Richiedi autenticazione a più fattori**.

   1. Fare clic su **Seleziona**.

1. Nella sezione **Attiva criterio** fare clic su **Sì**.

   ![Abilitare i criteri](./media/app-based-mfa/18.png)

1. Fare clic su **Create**(Crea).

## <a name="evaluate-a-simulated-sign-in"></a>Valutare un accesso simulato

Ora che sono stati configurati i criteri di accesso condizionale, è possibile sapere se funzionano come previsto. Come primo passaggio, usare lo strumento per i criteri What If dell'accesso condizionale per simulare un accesso dell'utente di test. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione.  

Per inizializzare lo strumento Valutazioni dei criteri di simulazione, impostare:

- **Isabella Simonsen** come utente
- **Gestione di Microsoft Azure** come app cloud

Fare clic su **What If** per creare un report di simulazione che indica:

- **Richiedi MFA per l'accesso al portale di Azure** sotto **Criteri applicabili**
- **Richiedi autenticazione a più fattori** in **Concedi controlli**.

![Strumento per i criteri What If](./media/app-based-mfa/23.png)

**Per valutare i criteri di accesso condizionale:**

1. Nella pagina [Accesso condizionale - Criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) fare clic su **What If** nel menu in alto.  

   ![What If](./media/app-based-mfa/14.png)

1. Fare clic su **Utenti**, scegliere **Isabella Simonsen** e quindi fare clic su **Seleziona**.

   ![Utente](./media/app-based-mfa/15.png)

1. Per selezionare un'app cloud, seguire questa procedura:

   ![App cloud](./media/app-based-mfa/16.png)

   1. Fare clic su **App cloud**.

   1. Nella pagina **App cloud** fare clic su **Selezionare le app**.

   1. Fare clic su **Seleziona**.

   1. Nella pagina **Seleziona** selezionare **Gestione di Microsoft Azure** e quindi fare clic su **Seleziona**.

   1. Nella pagina App cloud fare clic su **Fine**.

1. Fare clic su **What If**.

## <a name="test-your-conditional-access-policy"></a>Testare i criteri di accesso condizionale

Nella sezione precedente si è appreso come valutare un accesso simulato. Oltre a una simulazione, è anche consigliabile testare i criteri di accesso condizionale per assicurarsi che funzionino come previsto.

Per testare i criteri, provare ad accedere al [portale di Azure](https://portal.azure.com) usando l'account di test **Isabella Simonsen**. Verrà visualizzata una finestra di dialogo che richiede di impostare l'account per la verifica aggiuntiva della sicurezza.

![Autenticazione a più fattori](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare l'utente di test e i criteri di accesso condizionale:

- Se non si conosce la procedura per eliminare un utente di Azure AD, vedere [Eliminare gli utenti da Azure Active Directory](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Per eliminare i criteri, selezionarli e quindi fare clic su **Elimina** nella barra di accesso rapido.

    ![Autenticazione a più fattori](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Richiedere le condizioni d'uso per essere accettati](require-tou.md)
> [Bloccare l'accesso quando viene rilevato un rischio per la sessione](app-sign-in-risk.md)

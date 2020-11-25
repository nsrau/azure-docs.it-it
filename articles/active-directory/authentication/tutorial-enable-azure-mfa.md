---
title: Abilitare Azure AD Multi-Factor Authentication
description: Questa esercitazione illustra come abilitare Azure AD Multi-Factor Authentication per un gruppo di utenti e testare la richiesta del fattore secondario durante un evento di accesso.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62818ae5be079dc154e6d6faef4a8ebaae8fcd9d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837873"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Esercitazione: Proteggere gli eventi di accesso degli utenti con Azure AD Multi-Factor Authentication

L'autenticazione a più fattori è un processo che richiede all'utente di immettere ulteriori forme di identificazione durante un evento di accesso. La richiesta può prevedere l'immissione di un codice sul telefono cellulare o l'analisi dell'impronta digitale. Quando è necessaria una seconda forma di autenticazione, la sicurezza viene rafforzata, perché questo fattore aggiuntivo non è facile da ottenere o duplicare.

Azure AD Multi-Factor Authentication e i criteri di accesso condizionale offrono la flessibilità per abilitare l'autenticazione a più fattori per gli utenti durante specifici eventi di accesso.

> [!IMPORTANT]
> Questa esercitazione illustra come un amministratore può abilitare Azure AD Multi-Factor Authentication.
>
> Se il team IT non ha abilitato l'opzione per usare Azure AD Multi-Factor Authentication o se si verificano problemi durante l'accesso, rivolgersi al supporto tecnico per ulteriore assistenza.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un criterio di accesso condizionale per abilitare Azure AD Multi-Factor Authentication per un gruppo di utenti
> * Configurare le condizioni dei criteri che richiedono l'autenticazione a più fattori
> * Testare il processo di autenticazione a più fattori come utente

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant di Azure AD funzionante con almeno una licenza di valutazione o di Azure AD Premium P1 abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di *amministratore globale*.
* Un utente non amministratore con una password conosciuta, ad esempio *testuser*. In questa esercitazione sarà possibile testare l'esperienza di Azure AD Multi-Factor Authentication dell'utente finale usando questo account.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Un gruppo di cui l'utente non amministratore è membro, ad esempio, *MFA-Test-Group*. In questa esercitazione verrà abilitata l'esperienza di Azure AD Multi-Factor Authentication per questo gruppo.
    * Se è necessario creare un gruppo, vedere come [creare un gruppo e aggiungere membri in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

I criteri di accesso condizionale costituiscono il modo consigliato per abilitare e usare Azure AD Multi-Factor Authentication. L'accesso condizionale consente di creare e definire criteri che reagiscono agli eventi di accesso e richiedono azioni aggiuntive prima che a un utente venga concesso l'accesso a un'applicazione o a un servizio.

![Diagramma della panoramica del funzionamento dell'accesso condizionale per proteggere il processo di accesso](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

I criteri di accesso condizionale possono essere granulari e specifici, con l'obiettivo di consentire agli utenti di garantire la produttività ovunque e in qualsiasi momento, ma anche di proteggere l'organizzazione. Questa esercitazione illustra come creare criteri di accesso condizionale di base per richiedere l'autenticazione a più fattori quando un utente accede al portale di Azure. Un'esercitazione successiva di questa serie mostra come configurare Azure AD Multi-Factor Authentication usando criteri di accesso condizionale basati sul rischio.

Prima di tutto, creare un criterio di accesso condizionale e assegnare il gruppo di test degli utenti come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Sicurezza** dal menu a sinistra.
1. Selezionare **Accesso condizionale** e quindi scegliere **+ Nuovi criteri**.
1. Immettere un nome per il criterio, ad esempio *MFA Pilot*.
1. In **Assegnazioni** scegliere **Utenti e gruppi** e quindi il pulsante di opzione **Seleziona utenti e gruppi**.
1. Selezionare la casella per **Utenti e gruppi** e quindi **Seleziona** per visualizzare gli utenti e i gruppi di Azure AD disponibili.
1. Cercare e selezionare il gruppo di Azure AD, ad esempio *MFA-Test-Group*, quindi scegliere **Seleziona**.

    [ ![Selezionare il gruppo di Azure AD da usare con i criteri di accesso condizionale](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Per applicare il criterio di accesso condizionale per il gruppo, selezionare **Fine**.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Configurare le condizioni per l'autenticazione a più fattori

Dopo aver creato il criterio di accesso condizionale e aver assegnato un gruppo di test di utenti, è necessario definire le app cloud o le azioni che attivano il criterio. Le app cloud o le azioni sono gli scenari che dovranno richiedere un'elaborazione aggiuntiva, come richiedere l'autenticazione a più fattori. Ad esempio, è possibile decidere che l'accesso a un'applicazione finanziaria o l'uso di strumenti di gestione richiede una richiesta di verifica aggiuntiva.

Per questa esercitazione il criterio di accesso condizionale verrà configurato per richiedere l'autenticazione a più fattori quando un utente accede al portale di Azure.

1. Selezionare **Applicazioni cloud o azioni**. È possibile scegliere di applicare il criterio di accesso condizionale per *Tutte le app cloud* o *Seleziona le app*. Per garantire flessibilità, è anche possibile escludere determinate app dal criterio.

    Per questa esercitazione, nella pagina *Includi* scegliere il pulsante di opzione **Seleziona le app**.

1. Scegliere **Seleziona**, quindi esplorare l'elenco degli eventi di accesso disponibili che è possibile usare.

    Per questa esercitazione, scegliere **Gestione di Microsoft Azure** per applicare il criterio agli eventi di accesso al portale di Azure.

1. Per applicare le app selezionate, scegliere **Seleziona** e quindi **Fine**.

    ![Selezionare l'app di gestione Microsoft Azure da includere nel criterio di accesso condizionale](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

I controlli di accesso consentono di definire i requisiti per concedere l'accesso a un utente, ad esempio la necessità di un'app client approvata o l'uso di un dispositivo aggiunto ad Azure AD ibrido. In questa esercitazione verranno configurati i controlli di accesso per richiedere l'autenticazione a più fattori durante un evento di accesso al portale di Azure.

1. In *Controlli di accesso* scegliere **Concedi** e quindi assicurarsi che il pulsante di opzione **Concedi accesso** sia selezionato.
1. Selezionare la casella **Richiedi autenticazione a più fattori** e quindi scegliere **Seleziona**.

È possibile impostare i criteri di accesso condizionale su *Solo report* per visualizzare il modo in cui la configurazione influirà sugli utenti o su *No* se non si vuole usare il criterio in questo momento. Poiché questa esercitazione è destinata a un gruppo di utenti di test, ora verrà abilitato il criterio e si procederà al test di Azure AD Multi-Factor Authentication.

1. Impostare l'interruttore *Abilita criterio* su **Sì**.
1. Per applicare il criterio di accesso condizionale, selezionare **Crea**.

## <a name="test-azure-ad-multi-factor-authentication"></a>Testare Azure AD Multi-Factor Authentication

A questo punto, si testerà il funzionamento del criterio di accesso condizionale e di Azure AD Multi-Factor Authentication. Prima di tutto, accedere a una risorsa che non richiede l'autenticazione a più fattori:

1. Aprire una nuova finestra del browser in modalità InPrivate o anonima e passare a [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Accedere con l'utente test non amministratore, ad esempio *testuser*. Non viene visualizzata alcuna richiesta di autenticazione a più fattori.
1. Chiudere la finestra del browser.

Accedere quindi al portale di Azure. Poiché nel criterio di accesso condizionale il portale di Azure è stato configurato per richiedere una verifica aggiuntiva, verrà visualizzata una richiesta di Azure AD Multi-Factor Authentication.

1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a [https://portal.azure.com](https://portal.azure.com).
1. Accedere con l'utente test non amministratore, ad esempio *testuser*. È necessario registrarsi e usare Azure AD Multi-Factor Authentication. Seguire le istruzioni per completare il processo e verificare che sia possibile accedere al portale di Azure.

    ![Seguire le istruzioni visualizzate nel browser e quindi quelle nella richiesta di autenticazione a più fattori registrata per accedere](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Chiudere la finestra del browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende usare più il criterio di accesso condizionale per abilitare Azure AD Multi-Factor Authentication configurato come parte di questa esercitazione, seguire questa procedura per eliminarlo:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare **Azure Active Directory**, quindi scegliere **Sicurezza** dal menu a sinistra.
1. Selezionare **Accesso condizionale** e quindi scegliere il criterio creato, ad esempio *MFA Pilot*
1. Scegliere **Elimina**, quindi confermare che si vuole eliminare il criterio.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'esperienza di Azure AD Multi-Factor Authentication usando i criteri di accesso condizionale per un gruppo selezionato di utenti. Si è appreso come:

> [!div class="checklist"]
> * Creare un criterio di accesso condizionale per abilitare Azure AD Multi-Factor Authentication per un gruppo di utenti di Azure AD
> * Configurare le condizioni dei criteri che richiedono l'autenticazione a più fattori
> * Testare il processo di autenticazione a più fattori come utente

> [!div class="nextstepaction"]
> [Abilitare l'opzione di writeback delle password nella reimpostazione della password self-service](./tutorial-enable-sspr-writeback.md)
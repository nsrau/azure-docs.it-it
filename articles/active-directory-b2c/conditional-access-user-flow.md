---
title: Aggiungere l'accesso condizionale a un flusso utente in Azure AD B2C
description: Informazioni su come aggiungere l'accesso condizionale ai flussi utente di Azure AD B2C. Configurare le impostazioni di autenticazione a più fattori (MFA) e i criteri di accesso condizionale nei flussi utente per applicare i criteri e correggere gli accessi a rischio.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfac3b80e772e7b359b1e926d5fb84e447a8fb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270734"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Aggiungere l'accesso condizionale ai flussi utente in Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

È possibile aggiungere l'accesso condizionale ai flussi utente di Azure Active Directory B2C per gestire gli accessi a rischio alle applicazioni. L'integrazione di Identity Protection e dell'accesso condizionale in Azure AD B2C consente di configurare i criteri che identificano il comportamento di accesso a rischio e di applicare criteri che richiedono ulteriori azioni da parte dell'utente o dell'amministratore. Questi sono i componenti che abilitano l'accesso condizionale nei flussi utente di Azure AD B2C:

- **Flusso utente**. Creare un flusso utente per guidare l'utente nel processo di accesso e iscrizione. Nelle impostazioni del flusso utente indicare se attivare i criteri di accesso condizionale quando un utente segue il flusso utente.
- **Applicazione che indirizza gli utenti verso il flusso utente**. Configurare l'app in modo da indirizzare gli utenti verso il flusso utente appropriato di iscrizione e accesso specificando l'endpoint del flusso utente nell'app.
- **Criteri di accesso condizionale**. [Creare un criterio di accesso condizionale](conditional-access-identity-protection-setup.md) e specificare le app a cui si vuole applicare il criterio. Quando l'utente segue il flusso utente di accesso o iscrizione per l'app, i criteri di accesso condizionale usano i segnali di Identity Protection per identificare gli accessi a rischio e visualizzare, se necessario, l'azione correttiva appropriata.

L'accesso condizionale è supportato nelle versioni più recenti dei flussi utente. È possibile aggiungere criteri di accesso condizionale a flussi utente nuovi durante la creazione oppure a flussi utente esistenti, purché la versione supporti l'accesso condizionale. Quando si aggiunge l'accesso condizionale a un flusso utente esistente, è necessario esaminare due impostazioni:

- **Multi-Factor Authentication (MFA)** : gli utenti possono ora usare un codice monouso tramite SMS o voce oppure una password monouso tramite posta elettronica per l'autenticazione a più fattori. Le impostazioni dell'autenticazione a più fattori sono indipendenti dalle impostazioni di accesso condizionale. È possibile impostare l'autenticazione a più fattori su **Always On** (Sempre attiva) in modo che l'autenticazione a più fattori sia obbligatoria indipendentemente dalla configurazione dell'accesso condizionale. In alternativa, è possibile impostare l'autenticazione a più fattori su **Condizionale**, in modo che l'autenticazione a più fattori sia obbligatoria solo se richiesta da un criterio di accesso condizionale attivo.

- **Accesso condizionale**: questa impostazione deve sempre essere **Attivato**. Questa impostazione viene in genere **disattivata** durante la risoluzione dei problemi o la migrazione oppure per le implementazioni legacy.

Altre informazioni su [Identity Protection e sull'accesso condizionale](conditional-access-identity-protection-overview.md) in Azure AD B2C o su [come configurarlo](conditional-access-identity-protection-setup.md).

## <a name="add-conditional-access-to-a-new-user-flow"></a>Aggiungere l'accesso condizionale a un nuovo flusso utente

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il tipo di flusso utente.
1. In **Selezionare una versione**selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pagina Crea un flusso utente nel portale di Azure con proprietà evidenziate](./media/tutorial-create-user-flows/select-version.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Nella sezione **Provider di identità** selezionare i provider di identità da consentire per questo flusso utente.
2. Nella sezione **Multi-Factor Authentication** selezionare il **metodo MFA**  desiderato e in **Applicazione di MFA** selezionare **Condizionale (opzione consigliata)** .
 
   ![Configurare l'autenticazione a più fattori](media/conditional-access-user-flow/configure-mfa.png)

1. Nella sezione **Accesso condizionale** selezionare la casella di controllo **Applica i criteri di accesso condizionale**.

   ![Configurare le impostazioni di accesso condizionale](media/conditional-access-user-flow/configure-conditional-access.png)

1. Nella sezione **Attributi e attestazioni utente** scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica** e **Nome visualizzato**. Selezionare **OK**.

    ![Pagina di selezione di attributi e attestazioni utente con tre attestazioni selezionate](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Aggiungere l'accesso condizionale a un flusso utente esistente

> [!NOTE]
> Il flusso utente esistente deve essere di una versione che supporta l'accesso condizionale. Queste versioni del flusso utente sono contrassegnate come **Consigliata**.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.

1. In **Criteri** selezionare **Flussi utente**. Selezionare quindi il flusso utente.

1. Selezionare **Proprietà** e assicurarsi che il flusso utente supporti l'accesso condizionale. A questo scopo, selezionare **Proprietà** e cercare l'impostazione etichettata come **Accesso condizionale**.
 
   ![Configurare l'autenticazione a più fattori e l'accesso condizionale nelle proprietà](media/conditional-access-user-flow/add-conditional-access.png)

1. Nella sezione **Multi-Factor Authentication** selezionare il **metodo MFA**  desiderato e in **Applicazione di MFA** selezionare **Condizionale (opzione consigliata)** .
 
1. Nella sezione **Accesso condizionale** selezionare la casella di controllo **Applica i criteri di accesso condizionale**.

1. Selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

Per testare l'accesso condizionale nel flusso utente, [creare un criterio di accesso condizionale](conditional-access-identity-protection-setup.md) e abilitare l'accesso condizionale nel flusso utente come descritto in precedenza. 

### <a name="prerequisites"></a>Prerequisiti

- Per creare criteri di accesso a rischio, è richiesto Azure AD B2C Premium 2. I tenant del livello Premium P1 consentono di creare criteri di percorso, app o gruppo.
- A scopo di test, è possibile [registrare l'applicazione Web di test](tutorial-register-applications.md) `https://jwt.ms`, ovvero un'applicazione Web di proprietà di Microsoft che visualizza il contenuto decodificato di un token (il contenuto del token non lascia mai il browser). 
- Per simulare un accesso a rischio, scaricare Tor Browser e provare ad accedere all'endpoint del flusso utente.
- Usare le impostazioni seguenti per [creare un criterio di accesso condizionale](conditional-access-identity-protection-setup.md):
   
   - Per **Utenti e gruppi** selezionare l'utente di test. Non selezionare **Tutti gli utenti** altrimenti potrebbe essere bloccato anche il proprio account utente.
   - Per **Applicazioni cloud o azioni** scegliere **Selezionare le app** e quindi scegliere l'applicazione relying party.
   - Per Condizioni selezionare **Rischio di accesso** e i livelli di rischio **Elevato**, **Medio** e **Basso**.
   - Per **Concedi** scegliere **Blocca l'accesso**.

      ![Rilevamenti dei rischi](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

### <a name="run-the-user-flow"></a>Eseguire il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**. In **Applicazione** selezionare *webapp1*. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.

   ![Pagina Esegui il flusso utente nel portale con il pulsante Esegui il flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Copiare l'URL in **Esegui l'endpoint del flusso utente**.

1. Per simulare un accesso a rischio, aprire [Tor Browser](https://www.torproject.org/download/) e usare l'URL copiato nel passaggio dell'anteprima per accedere all'app registrata.

1. Immettere le informazioni richieste nella pagina di accesso e quindi provare ad accedere. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato. Nel token decodificato jwt.ms è indicato che l'accesso è stato bloccato:

   ![Testare un accesso bloccato](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="next-steps"></a>Passaggi successivi

[Personalizzare l'interfaccia utente in un flusso utente di Azure AD B2C](customize-ui-overview.md)

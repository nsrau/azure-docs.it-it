---
title: 'Esercitazione: Creare flussi utente - Azure Active Directory B2C | Microsoft Docs'
description: Informazioni su come creare flussi utente per le applicazioni in Azure Active Directory B2C mediante il portale di Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b76d56b17973c04992a8855917c814ced649bd0f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338259"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Esercitazione: Creare flussi utente in Azure Active Directory B2C

Nelle applicazioni possono essere presenti [flussi utente](active-directory-b2c-reference-policies.md) che consentono agli utenti di effettuare l'iscrizione, accedere o gestire il proprio profilo. È possibile creare più flussi utente di tipi diversi nel proprio tenant di Azure Active Directory (Azure AD) B2C e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Questa esercitazione illustra come creare alcuni flussi utente consigliati tramite il portale di Azure. Se occorrono informazioni su come configurare un flusso delle credenziali password del proprietario della risorsa (ROPC) nell'applicazione, vedere [Configurare il flusso delle credenziali password del proprietario della risorsa in Azure AD B2C](configure-ropc.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Registrare le applicazioni](tutorial-register-applications.md) che fanno parte dei flussi utente da creare. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.

    ![Passare alla directory della sottoscrizione](./media/tutorial-create-user-flows/switch-directories.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
4. Selezionare **Flussi utente** nel menu a sinistra e quindi selezionare **Nuovo flusso utente**.

    ![Selezionare Nuovo flusso utente](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. Nella scheda Consigliati selezionare il flusso utente **Iscrizione e accesso**.

    ![Selezionare il flusso utente di iscrizione e accesso](./media/tutorial-create-user-flows/signup-signin-type.png)

6. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
7. Per **Provider di identità** selezionare **Iscrizione posta elettronica**.

    ![Impostare le proprietà del flusso](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra altro**, quindi **Paese/Area**, **Nome visualizzato**, e **Codice postale**. Fare clic su **OK**.

    ![Selezionare attributi e attestazioni utente](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
2. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Eseguire il flusso utente](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. Immettere un indirizzo di posta elettronica valido, fare clic su **Invia codice di verifica**, quindi immettere il codice di verifica ricevuto.
5. Immettere una nuova password e confermarla.
6. Immettere il nome visualizzato desiderato, selezionare il paese e l'area, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
7. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate di nome, paese e codice postale.

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Selezionare **Flussi utente** nel menu a sinistra e quindi selezionare **Nuovo flusso utente**.
2. Selezionare il flusso utente di **modifica del profilo** nella scheda Consigliati.
3. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
4. Per **Provider di identità** selezionare **Accesso all'account locale**.
5. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra altro**, quindi **Nome visualizzato** e **Posizione**. Fare clic su **OK**.
6. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
2. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
4. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continue**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

È possibile abilitare l'utente dell'applicazione per la reimpostazione della password, se necessario. Per abilitare la reimpostazione della password, si usa un flusso utente di reimpostazione della password.

1. Selezionare **Flussi utente** nel menu a sinistra e quindi selezionare **Nuovo flusso utente**.
2. Selezionare il flusso utente **Reimpostazione password** nella scheda Consigliati.
3. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
4. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
5. In Attestazioni dell'applicazione fare clic su **Mostra altro** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
6. Fare clic su **OK**.
7. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Nella pagina Panoramica del flusso utente creato selezionare **Esegui il flusso utente**.
2. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
3. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
4. Ora è possibile modificare la password per l'utente. Fare clic su **Continue**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

> [!div class="nextstepaction"]
> [Aggiungere provider di identità alle applicazioni in Azure Active Directory B2C](tutorial-add-identity-providers.md)
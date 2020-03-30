---
title: Esercitazione - Creare flussi utente - Azure Active Directory B2CTutorial - Create user flows - Azure Active Directory B2C
description: Informazioni su come creare flussi utente nel portale di Azure per abilitare l'iscrizione, l'accesso e la modifica dei profili utente per le applicazioni in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51adbb74635f66ca86347b536dc2607566dcb725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264245"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Esercitazione: Creare flussi utente in Azure Active Directory B2CTutorial: Create user flows in Azure Active Directory B2C

Nelle applicazioni potrebbero essere disponibili [flussi utente](user-flow-overview.md) che consentono agli utenti di iscriversi, accedere o gestire il proprio profilo. È possibile creare più flussi utente di tipi diversi nel tenant B2C di Azure Active Directory (Azure AD B2C) e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Questa esercitazione illustra come creare alcuni flussi utente consigliati tramite il portale di Azure. Per informazioni su come configurare un flusso di credenziali per la password del proprietario della risorsa (ROPC) nell'applicazione, vedere Configurare il flusso delle credenziali della password del proprietario della [risorsa in Azure AD B2C.](configure-ropc.md)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[Registrare le applicazioni](tutorial-register-applications.md) che fanno parte dei flussi utente da creare.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.

    ![Tenant B2C, riquadro Directory e sottoscrizione, portale di Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri**selezionare **Flussi utente (criteri)** e quindi **Nuovo flusso utente.**

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Nella scheda **Consigliato** selezionare il flusso utente **Iscriviti e Accedi.**

    ![Selezionare una pagina del flusso utente con il flusso di iscrizione e accesso evidenziato](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per **Provider di identità** selezionare **Iscrizione posta elettronica**.

    ![Creare la pagina del flusso utente nel portale di Azure con le proprietà evidenziateCreate user flow page in Azure portal with properties highlighted](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra altro**, quindi scegliere gli attributi e le attestazioni per **Paese/area geografica**, Nome **visualizzato**e **Codice postale**. Fare clic su **OK**.

    ![Pagina di selezione di attributi e attestazioni con tre attestazioni selezionate](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Pagina Esegui flusso utente nel portale con il pulsante Esegui flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Inserisci un indirizzo email valido, fai clic su **Invia codice di verifica**, inserisci il codice di verifica che ricevi, quindi seleziona Verifica **codice**.
1. Immettere una nuova password e confermarla.
1. Selezionare il paese e l'area geografica, immettere il nome da visualizzare, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate di paese/area geografica, nome e codice postale.

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Nel menu della pagina Panoramica tenant di Azure AD B2C selezionare **Flussi utente (criteri)** e quindi **Nuovo flusso utente**.
1. Selezionare il flusso utente di **modifica del profilo** nella scheda **Consigliato.**
1. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
1. Per **Provider di identità** selezionare **Accesso all'account locale**.
1. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra altro**, quindi scegliere sia gli attributi che le attestazioni per **Nome visualizzato** e Titolo **processo**. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
1. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per consentire agli utenti dell'applicazione di reimpostare la password, utilizzare un flusso utente di reimpostazione della password.

1. Nel menu Panoramica tenant B2C di Azure AD selezionare **Flussi utente (criteri)** e quindi **Nuovo flusso utente**.
1. Selezionare il flusso utente **di reimpostazione della password** nella scheda **Consigliato.**
1. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
1. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
1. In Attestazioni dell'applicazione fare clic su **Mostra altro** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
1. Fare clic su **OK**.
1. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui flusso utente**, verificare l'indirizzo di posta elettronica dell'account creato in precedenza e selezionare **Continua**.
1. Ora è possibile modificare la password per l'utente. Modificare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Successivamente, scopri come aggiungere provider di identità alle tue applicazioni per abilitare l'accesso degli utenti con provider come Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft o Twitter.

> [!div class="nextstepaction"]
> [Aggiungere provider di identità alle applicazioni >](tutorial-add-identity-providers.md)
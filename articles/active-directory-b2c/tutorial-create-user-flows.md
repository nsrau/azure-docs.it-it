---
title: 'Esercitazione: Creare flussi utente - Azure Active Directory B2C'
description: Seguire questa esercitazione per informazioni su come creare flussi utente nel portale di Azure per consentire l'iscrizione, l'accesso e la modifica dei profili utente per le applicazioni in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041b1766ae6a64f51d922de128ef316cc0ed8260
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922170"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Esercitazione: Creare flussi utente in Azure Active Directory B2C

Nelle applicazioni possono essere presenti [flussi utente](user-flow-overview.md) che consentono agli utenti di effettuare l'iscrizione, accedere o gestire il proprio profilo. È possibile creare più flussi utente di tipi diversi nel proprio tenant di Azure Active Directory B2C (Azure AD B2C) e usarli nelle applicazioni in base alle esigenze. I flussi utente possono essere usati per più applicazioni.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Questa esercitazione illustra come creare alcuni flussi utente consigliati tramite il portale di Azure. Se servono informazioni su come configurare un flusso delle credenziali password del proprietario della risorsa (ROPC) nell'applicazione, vedere [Configurare il flusso delle credenziali password del proprietario della risorsa in Azure AD B2C](configure-ropc.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

> [!IMPORTANT]
> Il modo in cui si fa riferimento alle versioni dei flussi utente è stato cambiato. In precedenza, venivano offerte le versioni V1 (pronte per l'ambiente di produzione) e le versioni V 1.1 e V2 (anteprima). Ora questi flussi utente sono stati consolidati nelle versioni **Consigliata** (anteprima di nuova generazione) e **Standard** (disponibile a livello generale). Tutti i flussi utente delle anteprime legacy V 1.1 e V2 verranno deprecati entro il **1° agosto 2021**. Per informazioni dettagliate, vedere [Versioni dei flussi utente in Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Prerequisiti

[Registrare le applicazioni](tutorial-register-applications.md) che fanno parte dei flussi utente da creare.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Creare un flusso utente di iscrizione e accesso

Il flusso utente di iscrizione e accesso consente di gestire le esperienze di iscrizione e accesso tramite una singola configurazione. Gli utenti dell'applicazione vengono indirizzati sul percorso corretto a seconda del contesto.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

    ![Tenant B2C, riquadro Directory e sottoscrizione, portale di Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Iscrizione e accesso**.

    ![Pagina Selezionare un tipo di flusso utente con il flusso Iscrizione e accesso evidenziato](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. In **Selezionare una versione**selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pagina Crea un flusso utente nel portale di Azure con proprietà evidenziate](./media/tutorial-create-user-flows/select-version.png)

1. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
1. Per **Provider di identità** selezionare **Iscrizione posta elettronica**.
1. Per **Attributi e attestazioni utente**, scegliere le attestazioni e gli attributi che si vogliono raccogliere e inviare all'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Fare clic su **OK**.

    ![Pagina di selezione di attributi e attestazioni utente con tre attestazioni selezionate](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Fare clic su **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi selezionare **Iscriversi adesso**.

    ![Pagina Esegui il flusso utente nel portale con il pulsante Esegui il flusso utente evidenziato](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Immettere un indirizzo di posta elettronica valido, fare clic su **Invia codice di verifica**, immettere il codice di verifica ricevuto e quindi selezionare **Verifica codice**.
1. Immettere una nuova password e confermarla.
1. Selezionare il paese e l'area geografica, immettere il nome visualizzato desiderato, immettere un codice postale e quindi fare clic su **Crea**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.
1. Ora si può eseguire di nuovo il flusso utente e dovrebbe essere possibile accedere con l'account creato. Il token restituito include le attestazioni selezionate di paese/area geografica, nome e codice postale.

## <a name="create-a-profile-editing-user-flow"></a>Creare un flusso utente di modifica del profilo

Se si vuole abilitare nell'applicazione la modifica del profilo da parte degli utenti, usare un flusso utente di modifica del profilo.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Modifica del profilo**. 
1. In **Selezionare una versione**selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *profileediting1*.
1. Per **Provider di identità** selezionare **Accesso all'account locale**.
2. Per **Attributi utente** scegliere gli attributi che vuole che il cliente possa modificate nel proprio profilo. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Nome visualizzato** e **Posizione**. Fare clic su **OK**.
3. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, quindi accedere con l'account creato in precedenza.
1. A questo punto si ha la possibilità di modificare il nome visualizzato e la posizione dell'utente. Fare clic su **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="create-a-password-reset-user-flow"></a>Creare un flusso utente di reimpostazione delle password

Per consentire agli utenti dell'applicazione di reimpostare la propria password, si può usare il flusso utente Reimpostazione password.

1. Nel menu della pagina di panoramica del tenant Azure AD B2C selezionare **Flussi utente** e quindi **Nuovo flusso utente**.
1. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Reimpostazione password**. 
1. In **Selezionare una versione**selezionare **Consigliata**, quindi selezionare **Crea**.
1. Immettere un **nome** per il flusso utente. Ad esempio, *passwordreset1*.
1. Per **Provider di identità** abilitare **Ripristinare la password usando la verifica tramite posta elettronica**.
2. In Attestazioni dell'applicazione fare clic su **Mostra altro** e scegliere le attestazioni che devono essere restituite nei token di autorizzazione inviati all'applicazione. Selezionare ad esempio **ID oggetto dell'utente**.
3. Fare clic su **OK**.
4. Fare clic su **Crea** per aggiungere il flusso utente. Un prefisso *B2C_1* viene automaticamente aggiunto al nome.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Selezionare il flusso utente creato per aprire la relativa pagina di panoramica, quindi selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Fare clic su **Esegui il flusso utente**, verificare l'indirizzo di posta elettronica dell'account creato in precedenza e selezionare **Continua**.
1. Ora è possibile modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come:

> [!div class="checklist"]
> * Creare un flusso utente di iscrizione e accesso
> * Creare un flusso utente di modifica del profilo
> * Creare un flusso utente di reimpostazione delle password

Nelle esercitazioni successive si apprenderà come aggiungere provider di identità alle applicazioni per consentire agli utenti di accedere con provider come Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft o Twitter.

> [!div class="nextstepaction"]
> [Aggiungere provider di identità alle applicazioni >](tutorial-add-identity-providers.md)

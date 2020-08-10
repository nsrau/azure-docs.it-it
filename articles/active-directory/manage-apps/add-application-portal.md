---
title: "Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)"
description: Questa guida introduttiva usa il portale di Azure per aggiungere un'applicazione della raccolta al tenant di Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 0818ab782710e6a102d2034790ff8d997cd54f8e
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2020
ms.locfileid: "87808440"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>Avvio rapido: Aggiungere un'applicazione al tenant di Azure Active Directory (Azure AD)

Azure Active Directory (Azure AD) offre una raccolta contenente migliaia di applicazioni preintegrate. Molte delle applicazioni usate dall'organizzazione sono probabilmente già presenti nella raccolta.

Dopo l'aggiunta di un'applicazione al tenant di Azure AD, è possibile:

- Configurare le proprietà per l'app.
- Gestire l'accesso degli utenti all'app con criteri di accesso condizionale.
- Configurare l'accesso Single Sign-On in modo che gli utenti possano accedere all'app con le credenziali Azure AD personali.

## <a name="prerequisites"></a>Prerequisiti

Per aggiungere un'applicazione al tenant di Azure AD, sono necessari:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno dei ruoli seguenti: amministratore globale, amministratore di applicazioni cloud o amministratore di applicazioni oppure proprietario dell'entità servizio.
- (Facoltativo: completamento di [Visualizzare le app](view-applications-portal.md)).

>[!IMPORTANT]
>Per testare i passaggi di questo argomento di avvio rapido, è consigliabile usare un ambiente non di produzione.

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Aggiungere un'app al tenant di Azure AD

Per aggiungere un'applicazione al tenant di Azure AD:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.
2. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali**. Verrà aperto il riquadro **Tutte le applicazioni**, in cui è visualizzato un campione casuale delle applicazioni presenti nel tenant di Azure AD.
3. Nel riquadro **Applicazioni aziendali** selezionare **Nuova applicazione**. 
    ![Selezionare Nuova applicazione per aggiungere un'app della raccolta al tenant](media/add-application-portal/new-application.png)
4. Passare alla nuova esperienza di anteprima della raccolta: Nel banner nella parte superiore della **pagina Aggiungi applicazione** selezionare il collegamento **Fare clic qui per provare la nuova raccolta di app migliorata**.
5. Viene visualizzato il riquadro **Esplora la raccolta di Azure AD (anteprima)** , contenente sezioni per le piattaforme cloud, le applicazioni locali e le applicazioni in primo piano. Per le applicazioni elencate nella sezione **Applicazioni in primo piano** vengono visualizzate icone che indicano se l'accesso Single Sign-On (SSO) federato e il provisioning sono supportati.
    ![Cercare un'app per nome o categoria](media/add-application-portal/browse-gallery.png)
6. È possibile sfogliare la raccolta per individuare l'applicazione da aggiungere oppure cercare l'applicazione immettendone il nome nella casella di ricerca. Selezionare quindi l'applicazione nei risultati. Nel modulo è possibile modificare il nome dell'applicazione in base alle esigenze dell'organizzazione. In questo esempio è stato selezionato GitHub e il nome è stato cambiato in **GitHub-test**.
    ![Mostra come aggiungere un'applicazione dalla raccolta](media/add-application-portal/create-application.png)
    >[!TIP]
    >Se l'applicazione che si sta cercando non è presente nella raccolta, è possibile fare clic sul collegamento **Crea un'applicazione personalizzata** e quindi in **Che cosa si vuole fare con l'applicazione?** scegliere **Integrare qualsiasi altra applicazione non trovata nella raccolta**. Microsoft ha collaborato con molti sviluppatori di applicazioni per preconfigurarle per l'uso con Azure AD. Queste sono le app visualizzate nella raccolta. Ma se l'app da aggiungere non è presente nell'elenco, è possibile crearne una nuova generica e quindi configurarla autonomamente o con le indicazioni dello sviluppatore che l'ha creata.
7. Selezionare **Crea**. Verrà visualizzata una pagina Attività iniziali con le opzioni per configurare l'applicazione per l'organizzazione.

L'aggiunta di un'applicazione è stata completata. L'argomento di avvio rapido successivo illustra come modificare il logo e altre proprietà dell'applicazione.

> [!TIP]
> È possibile automatizzare la gestione delle app usando l'API Graph. Vedere [Automatizzare la gestione delle app con l'API Microsoft Graph](https://docs.microsoft.com/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare con questa serie di argomenti di avvio rapido, è consigliabile eliminare l'app per pulire il tenant di test. La procedura di eliminazione dell'app è illustrata nell'ultimo argomento di avvio rapido di questa serie. Vedere [Eliminare un'app](delete-application-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come configurare un'app, passare all'articolo successivo.
> [!div class="nextstepaction"]
> [Configurare un'app](add-application-portal-configure.md)

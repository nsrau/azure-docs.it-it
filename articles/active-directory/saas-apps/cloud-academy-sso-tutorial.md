---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On di Azure Active Directory con Cloud Academy - SSO"
description: Questa esercitazione descrive come configurare l'accesso Single Sign-On tra Azure Active Directory e Cloud Academy - SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 37ed9bb09b6b15af0c32f489cbc3c02ec27c2827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461953"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con Cloud Academy - SSO

Questa esercitazione descrive come integrare Cloud Academy - SSO con Azure Active Directory (Azure AD). Integrando Cloud Academy - SSO con Azure AD, è possibile:

* Usare Azure AD per controllare chi può accedere a Cloud Academy - SSO.
* Abilitare gli utenti per l'accesso automatico a Cloud Academy - SSO con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Cloud Academy - SSO abilitata per l'accesso Single Sign-On (SSO).

## <a name="tutorial-description"></a>Descrizione dell'esercitazione

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

Cloud Academy - SSO supporta l'accesso SSO avviato da SP.

Dopo aver configurato Cloud Academy - SSO, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Aggiungere Cloud Academy - SSO dalla raccolta

Per configurare l'integrazione di Cloud Academy - SSO in Azure AD, è necessario aggiungere Cloud Academy - SSO dalla raccolta all'elenco di app SaaS gestite:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Cloud Academy - SSO** nella casella di ricerca.
1. Selezionare **Cloud Academy - SSO** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Configurare e testare l'accesso SSO di Azure AD per Cloud Academy - SSO

Configurare e testare l'accesso SSO di Azure AD con Cloud Academy - SSO usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Cloud Academy - SSO.

Per configurare e testare l'accesso SSO di Azure AD con Cloud Academy - SSO, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare la funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** per testare l'accesso Single Sign-On di Azure AD.
    1. **[Concedere l'accesso all'utente di test](#grant-access-to-the-test-user)** per consentire all'utente di usare l'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On per Cloud Academy-SSO](#configure-single-sign-on-for-cloud-academy)** sul lato applicazione.
    1. **[Creare un utente di test di Cloud Academy-SSO](#create-a-cloud-academy-test-user)** come controparte per la rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Cloud Academy - SSO** del [portale di Azure](https://portal.azure.com/) nella sezione **Gestione** selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante a forma di matita relativo a **Configurazione SAML di base** per modificare le impostazioni:

   ![Screenshot che mostra l'icona della matita per la modifica della configurazione SAML di base.](common/edit-urls.png)

1. Nella casella **URL di accesso** della sezione **Configurazione SAML di base** immettere `https://cloudacademy.com/login/enterprise/`.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare il pulsante Copia per copiare l'**URL dei metadati di federazione dell'app**. Salvare l'URL.

    ![Screenshot che mostra il pulsante Copia per l'URL dei metadati di federazione dell'app.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**. Selezionare **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** completare questa procedura:
   1. Nella casella **Nome** immettere **B.Simon**.  
   1. Nella casella **Nome utente** immettere \<username>@\<companydomain>.\<extension>. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Selezionare **Crea**.

### <a name="grant-access-to-the-test-user"></a>Concedere l'accesso all'utente di test

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Cloud Academy - SSO.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Cloud Academy - SSO**.
1. Nella sezione **Gestione** della pagina di panoramica dell'app selezionare **Utenti e gruppi**:

   ![Screenshot che mostra l'opzione Utenti e gruppi.](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**:

    ![Screenshot che mostra il pulsante Aggiungi utente.](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Configurare l'accesso Single Sign-On per Cloud Academy

1. In un'altra finestra del browser accedere al sito aziendale di Cloud Academy - SSO come amministratore.

1. Selezionare il nome della società e quindi selezionare **Settings & Integrations** (Impostazioni e integrazioni) nel menu visualizzato:

    ![Screenshot che mostra l'opzione Settings & Integrations.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Nella pagina **Settings & Integrations** (Impostazioni e integrazioni) passare alla scheda **Integrations** (Integrazioni) e selezionare **SSO**:

    ![Screenshot che mostra l'opzione SSO nella scheda Integrations.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Completare i passaggi seguenti in questa pagina:

    ![Screenshot che mostra la pagina Integrations > SSO.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Nella casella **Entity ID URL** (URL dell'ID entità) immettere il valore dell'ID entità copiato dal portale di Azure.

    b. Nella casella **SSO URL** (URL SSO) incollare il valore dell'URL di accesso copiato dal portale di Azure.

    c. Aprire il certificato Base64 scaricato dal portale di Azure nel Blocco note. Incollare il contenuto nella casella **Certificate** (Certificato).

    d. Nella casella di testo **Name ID Format** (Formato ID nome) digitare il valore predefinito: `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

1. Selezionare **Salva**.

    > [!NOTE]
    > Per altre informazioni su come configurare Cloud Academy - SSO, vedere l'articolo sulla [configurazione dell'accesso Single Sign-On](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Creare un utente di test di Cloud Academy

1. Accedere a Cloud Academy - SSO.

1. Selezionare il nome della società e quindi selezionare **Members** (Membri) nel menu visualizzato:

    ![Screenshot che mostra l'opzione Members.](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Selezionare **Invite Members** (Invita membri) e quindi selezionare **Invite a Single Member** (Invita un singolo membro):

    ![Screenshot che mostra l'opzione Invite a Single Member.](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Immettere i valori nei campi obbligatori e quindi selezionare **Invite** (Invita):

    ![Screenshot che mostra la finestra di dialogo Invite a Member.](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testare l'accesso SSO 

Testare ora la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di Cloud Academy - SSO nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di Cloud Academy - SSO per cui si è configurato l'accesso SSO. Per altre informazioni, vedere l'[introduzione al pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Esercitazioni su come integrare le app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare Cloud Academy - SSO con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere Cloud Academy - SSO con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
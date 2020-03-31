---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con iProva | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98458f8be162d0903f5ea0d1f7d4651d46f78e8e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048443"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con iProva

Questa esercitazione descrive come integrare iProva con Azure Active Directory (Azure AD). Integrando iProva con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a iProva.
* Abilitare gli utenti per l'accesso automatico a iProva con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di iProva abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* iProva supporta l'accesso SSO avviato da **provider di servizi**

* Dopo aver configurato iProva, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Aggiunta di iProva dalla raccolta

Per configurare l'integrazione di iProva in Azure AD, è necessario aggiungere iProva dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **iProva** nella casella di ricerca.
1. Selezionare **iProva** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per iProva

Configurare e testare l'accesso SSO di Azure AD con iProva usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iProva.

Per configurare e testare l'accesso SSO di Azure AD con iProva, completare le procedure di base seguenti:

1. **[Recuperare le informazioni di configurazione da iProva](#retrieve-configuration-information-from-iprova)** : come preparazione per i passaggi successivi.
1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Creare l'utente di test di iProva](#create-iprova-test-user)** : per avere una controparte di B.Simon in iProva collegata alla rappresentazione dell'utente in Azure AD.
1. **[Configurare l'accesso Single Sign-On di iProva](#configure-iprova-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="retrieve-configuration-information-from-iprova"></a>Recuperare le informazioni di configurazione da iProva

In questa sezione si recuperano da iProva le informazioni per configurare l'accesso Single Sign-On di Azure AD.

1. Aprire un Web browser e passare alla pagina **SAML2 info** (Informazioni su SAML2) in iProva usando l'URL nel formato seguente:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Visualizzare la pagina delle informazioni SAML2 in iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Lasciare aperta la scheda del browser mentre si procede con i passaggi successivi in un'altra scheda del browser.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **iProva** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Compilare la casella **URL di accesso** con il valore visualizzato con l'etichetta **Sign-on URL** (URL di accesso) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    b. Compilare la casella **Identificatore** con il valore visualizzato con l'etichetta **EntityID** (ID entità) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    c. Compilare la casella **URL di risposta** con il valore visualizzato con l'etichetta **Reply URL** (URL di risposta) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

1. L'applicazione iProva prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione iProva prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | Nome | Attributo di origine| Spazio dei nomi  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

## <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iProva.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **iProva**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="create-iprova-test-user"></a>Creare l'utente di test di iProva

1. Accedere a iProva usando l'account **Administrator**.

2. Aprire il menu **Go to** (Vai a).

3. Selezionare **Application management** (Gestione applicazioni).

4. Selezionare **Users** (Utenti) nel pannello **Users and user groups** (Utenti e gruppi di utenti).

5. Selezionare **Aggiungi**.

6. Nella casella **Username** (Nome utente) digitare il nome dell'utente, ad esempio `B.Simon@contoso.com`.

7. Nella casella **Full name** (Nome completo) immettere nome e cognome dell'utente, ad esempio **B.Simon**.

8. Selezionare l'opzione **No password (use single sign-on)** (Nessuna password - usa Single Sign-On).

9. Nella casella **Email address** (Indirizzo di posta elettronica) immettere l'indirizzo di posta elettronica dell'utente, ad esempio `B.Simon@contoso.com`.

10. Scorrere fino alla fine della pagina e selezionare **Finish** (Fine).

## <a name="configure-iprova-sso"></a>Configurare l'accesso Single Sign-On di iProva

1. Accedere a iProva usando l'account **Administrator**.

2. Aprire il menu **Go to** (Vai a).

3. Selezionare **Application management** (Gestione applicazioni).

4. Selezionare **General** (Generale) nel pannello **System settings** (Impostazioni di sistema).

5. Selezionare **Modifica**.

6. Scorrere verso il basso fino a **Access control** (Controllo di accesso).

    ![Impostazioni di controllo di accesso di iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Individuare l'impostazione **Users are automatically logged on with their network accounts** (Gli utenti vengono connessi automaticamente con i propri account di rete) e modificarla in **Yes, authentication via SAML** (Sì, autenticazione tramite SAML). Vengono ora visualizzate le opzioni aggiuntive.

8. Selezionare **Set up** (Configura).

9. Selezionare **Avanti**.

10. iProva chiede se si vogliono scaricare i dati della federazione da un URL oppure caricarli da un file. Selezionare l'opzione **From URL** (Da URL).

    ![Scaricare i metadati di Azure AD](media/iprova-tutorial/iprova-download-metadata.png)

11. Incollare l'URL dei metadati salvato nell'ultimo passaggio della sezione "Configurare l'accesso Single Sign-On di Azure AD".

12. Selezionare il pulsante a forma di freccia per scaricare i metadati da Azure AD.

13. Al termine del download verrà visualizzato il messaggio di conferma **Valid Federation Data file downloaded** (File di dati di federazione valido scaricato).

14. Selezionare **Avanti**.

15. Ignorare per il momento l'opzione **Test login** (Accesso di test) e selezionare **Next** (Avanti).

16. Nell'elenco a discesa **Claim to use** (Attestazione da usare) selezionare **windowsaccountname**.

17. Selezionare **Fine**.

18. Tornare alla schermata **Edit general settings** (Modifica impostazioni generali). Scorrere verso il basso fino alla parte inferiore della pagina e fare clic su **OK** per salvare la configurazione.

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di iProva nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di iProva per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare iProva con Azure AD](https://aad.portal.azure.com/)

- [Informazioni sul controllo sessioni in Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Come proteggere iProva con visibilità e controlli avanzati](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
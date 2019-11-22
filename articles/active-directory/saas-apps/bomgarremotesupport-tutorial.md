---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con BeyondTrust Remote Support | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e BeyondTrust Remote Support.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 193b163f-bdee-4974-b16d-777c51b991df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff21c3ee7721c82232e668ddb9645895080cf79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con BeyondTrust Remote Support

Questa esercitazione descrive come integrare BeyondTrust Remote Support con Azure Active Directory (Azure AD). Integrando BeyondTrust Remote Support con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a BeyondTrust Remote Support.
* Abilitare gli utenti per l'accesso automatico a BeyondTrust Remote Support con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di BeyondTrust Remote Support abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* BeyondTrust Remote Support supporta l'accesso SSO avviato da **SP**
* BeyondTrust Remote Support supporta il provisioning utenti **JIT**

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Aggiunta di BeyondTrust Remote Support dalla raccolta

Per configurare l'integrazione di BeyondTrust Remote Support in Azure AD è necessario aggiungere BeyondTrust Remote Support dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **BeyondTrust Remote Support** nella casella di ricerca.
1. Selezionare **BeyondTrust Remote Support** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per BeyondTrust Remote Support

Configurare e testare l'accesso SSO di Azure AD con BeyondTrust Remote Support usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in BeyondTrust Remote Support.

Per configurare e testare l'accesso SSO di Azure AD con BeyondTrust Remote Support, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di BeyondTrust Remote Support](#configure-beyondtrust-remote-support-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di BeyondTrust Remote Support](#create-beyondtrust-remote-support-test-user)** : per avere una controparte di B.Simon in BeyondTrust Remote Support collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **BeyondTrust Remote Support** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://<HOSTNAME>.bomgar.com/saml`

    b. Nella casella di testo **Identificatore** digitare un URL nel formato seguente: `https://<HOSTNAME>.bomgar.com`

    c. Nella casella di testo **URL di risposta** digitare un URL nel formato seguente: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di accesso effettivi. Questi valori verranno spiegati più avanti nell'esercitazione.

1. L'applicazione BeyondTrust Remote Support prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre quelli elencati in precedenza, l'applicazione BeyondTrust Remote Support prevede il passaggio di altri attributi nella risposta SAML. Tali attributi sono indicati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.

    | NOME |  Attributo di origine|
    | ---------------| ----------|
    | Givenname | user.givenname |
    | Emailaddress | user.mail |
    | NOME | user.userprincipalname |
    | Username | user.userprincipalname |
    | Gruppi | user.groups |
    | Identificatore univoco dell'utente | user.userprincipalname |

    > [!NOTE]
    > Quando si assegnano gruppi di Azure AD per l'applicazione BeyondTrust Remote Support, è necessario modificare il valore dell'opzione Gruppi restituiti nell'attestazione da Nessuno a Gruppo di sicurezza. I gruppi verranno importati nell'applicazione come ID oggetto. Per trovare l'ID oggetto del gruppo di Azure AD, controllare le proprietà nell'interfaccia di Azure Active Directory. Tale valore sarà necessario per fare riferimento ai gruppi Azure AD e assegnarli ai criteri di gruppo corretti.

1. Quando si imposta Identificatore univoco dell'utente, questo valore deve essere impostato sul formato NameID **Persistente**. L'identificatore deve essere persistente per identificare e associare correttamente l'utente nei criteri di gruppo corretti per le autorizzazioni. Fare clic sull'icona di modifica per aprire la finestra di dialogo **Attributi utente e attestazioni** per modificare il valore di Identificatore univoco dell'utente.

1. Nella sezione **Gestisci l'attestazione** fare clic su **Scegliere il formato per l'identificatore del nome** e impostare il valore su **Persistente**, quindi fare clic su **Salva**.

    ![Attributi e attestazioni utente](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura BeyondTrust Remote Support** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a BeyondTrust Remote Support.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **BeyondTrust Remote Support**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Configurare l'accesso Single Sign-On di BeyondTrust Remote Support

1. In un'altra finestra del Web browser accedere al sito aziendale di BeyondTrust Remote Support come amministratore.

1. Fare clic sul menu **STATUS** (STATO) e copiare i valori di **Identifier** (Identificatore), **Reply URL** (URL di risposta) e **Sign on URL** (URL di accesso), quindi usare questi valori nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Configurare BeyondTrust Remote Support](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Passare all'interfaccia /login di BeyondTrust Remote Support all'indirizzo `https://support.example.com/login`, in cui **support.example.com** è il nome host primario dell'appliance, ed eseguire l'autenticazione usando le credenziali amministrative.

1. Passare a **Users & Security** (Utenti e sicurezza)  > **Security Providers** (Provider di sicurezza).

1. Nel menu a discesa selezionare **SAML** e fare clic sul pulsante **Create Provider** (Crea provider).

1. Nella sezione delle impostazioni del provider di identità è disponibile un'opzione per caricare i metadati del provider di identità. Individuare il file XML dei metadati scaricato dal portale di Azure e fare clic sul pulsante **Upload** (Carica). I valori di **Entity ID** (ID entità), **Single Sign-On Service URL** (URL del servizio SSO) e il certificato verranno caricati automaticamente e sarà necessario impostare **Protocol Binding** (Binding protocollo) su **HTTP POST**. Vedere lo screenshot seguente:

    ![Configurare BeyondTrust Remote Support](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Creare l'utente di test di BeyondTrust Remote Support

In questa sezione verranno configurate le impostazioni di provisioning utenti. Ai valori usati in questa sezione verrà fatto riferimento nella sezione **Attributi utente e attestazioni** del portale di Azure. La sezione è stata configurata in modo da usare i valori predefiniti già importati al momento della creazione, ma, se necessario, i valori sono personalizzabili.

![Creazione dell'utente](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Gli attributi groups ed e-mail non sono necessari per questa implementazione. Se si usano gruppi di Azure AD e li si assegna ai criteri di gruppo di BeyondTrust Remote Support per le autorizzazioni, è necessario fare riferimento all'ID oggetto del gruppo tramite le relative proprietà nel portale di Azure e inserirlo nella sezione Available Groups (Gruppi disponibili). Dopo aver completato questa operazione, l'ID oggetto o il gruppo di Azure AD sarà disponibile per l'assegnazione ai criteri di gruppo per le autorizzazioni.

![Creazione dell'utente](./media/bomgarremotesupport-tutorial/config-user2.png)

![Creazione dell'utente](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> In alternativa, è possibile impostare criteri di gruppo predefiniti per il provider di sicurezza SAML2. Definendo questa opzione, a tutti gli utenti che eseguono l'autenticazione tramite SAML verranno assegnate le autorizzazioni specificate nei criteri di gruppo. I criteri per membri generali sono inclusi in BeyondTrust Remote Support/Privileged Remote Access con autorizzazioni limitate e possono essere usati per testare l'autenticazione e assegnare gli utenti ai criteri corretti. Gli utenti non vengono inseriti nell'elenco di utenti di SAML2 tramite /login > Users & Security (Utenti e sicurezza) fino al primo tentativo di autenticazione riuscito. Per altre informazioni sui criteri di gruppo, vedere la pagina all'indirizzo seguente: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di BeyondTrust Remote Support nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di BeyondTrust Remote Support per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare BeyondTrust Remote Support con Azure AD](https://aad.portal.azure.com/)

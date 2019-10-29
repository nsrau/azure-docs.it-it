---
title: "Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con NetSuite | Microsoft Docs"
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d578b5d08fecde733bb7b257057e480fef83c4e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754423"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Esercitazione: Integrare l'accesso Single Sign-On (SSO) di Azure Active Directory con NetSuite

Questa esercitazione descrive come integrare NetSuite con Azure Active Directory (Azure AD). Integrando NetSuite con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a NetSuite.
* Abilitare gli utenti per l'accesso automatico a NetSuite con gli account Azure AD personali.
* Gestire gli account in una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Una sottoscrizione di NetSuite abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. 

NetSuite supporta:

* Accesso Single Sign-On avviato da IDP.
* Provisioning utenti JIT.
* [Provisioning utenti automatizzato](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-netsuite-from-the-gallery"></a>Aggiungere NetSuite dalla raccolta

Per configurare l'integrazione di NetSuite in Azure AD, aggiungere NetSuite dalla raccolta all'elenco di app SaaS gestite come descritto di seguito:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **NetSuite** nella casella di ricerca.
1. Selezionare **NetSuite** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per NetSuite

Configurare e testare l'accesso SSO di Azure AD con NetSuite by usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetSuite.

Per configurare e testare l'accesso SSO di Azure AD con NetSuite, completare le procedure di base seguenti:

1. [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso): per consentire agli utenti di usare questa funzionalità.

    a. [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user): per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.  
    b. [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user): per consentire all'utente B.Simon di usare l'accesso Single Sign-On di Azure AD.
1. [Configurare l'accesso Single Sign-On di NetSuite](#configure-netsuite-sso): per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * [Creare l'utente di test di NetSuite](#create-the-netsuite-test-user): per avere una controparte dell'utente B.Simon in NetSuite collegata alla rappresentazione dell'utente in Azure AD.
1. [Testare l'accesso SSO](#test-sso) per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **NetSuite** del [portale di Azure](https://portal.azure.com/) cercare la sezione **Gestione** e quindi selezionare **Single Sign-On**.
1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nel riquadro **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** (la matita) accanto a **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella casella di testo **URL di risposta** della sezione **Configurazione SAML di base** digitare un URL in uno dei formati seguenti:

    ```
    https://<tenant-name>.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.NetSuite.com/saml2/acs
    https://<tenant-name>.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs
    https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    > [!NOTE]
    > I valori negli URL precedenti non sono reali. Aggiornarli con l'URL di risposta effettivo. Per ottenere il valore, contattare il [team di supporto clienti di NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). È anche possibile fare riferimento ai formati illustrati nella sezione **Configurazione SAML di base** del portale di Azure.

    L'applicazione NetSuite prevede che le asserzioni SAML siano visualizzate in un formato specifico. Sarà necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. 
    
1. Per aprire il riquadro **Attributi utente**, selezionare l'icona **Modifica** (la matita). Il riquadro visualizza un elenco di attributi predefiniti, come illustrato nell'immagine seguente: 

    ![Riquadro Attributi utente](common/edit-attribute.png)

    Oltre a quelli elencati in precedenza, l'applicazione NetSuite prevede il passaggio di altri attributi nella risposta SAML. 

1. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** seguire questa procedura per aggiungere l'attributo del token SAML illustrato nella tabella seguente:

    | Nome | Attributo di origine | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Selezionare **Aggiungi nuova attestazione** per aprire il riquadro **Gestisci attestazioni utente**.

    b. Nella casella **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Lasciare vuota la casella **Spazio dei nomi**.

    d. Nell'elenco a discesa **Origine** selezionare **Attributo**.

    e. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    f. Selezionare **OK**.

    g. Selezionare **Salva**.

    >[!NOTE]
    >Il valore dell'attributo account non è reale. È necessario aggiornarlo, come descritto più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** cercare il file **XML dei metadati della federazione**.

1. Selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura NetSuite** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD

In questa sezione viene creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

1. Selezionare **Nuovo utente** in alto nella schermata.

1. Nel riquadro **Proprietà utente** seguire questa procedura:

   a. Nella casella **Nome** immettere **B.Simon**.  
   b. Nella casella **Nome utente** immettere un valore per username@companydomain.extension, ad esempio B.Simon@contoso.com.  
   c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.  
   d. Selezionare **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abilita l'utente B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a NetSuite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **NetSuite**.
1. Nel riquadro della panoramica cercare la sezione **Gestione** e quindi selezionare il collegamento **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

    ![Pulsante "Aggiungi utente"](common/add-assign-user.png)

1. Nell'elenco a discesa **Utenti** del riquadro **Utenti e gruppi** selezionare **B.Simon** e quindi selezionare il pulsante **Seleziona** nella parte inferiore della schermata.
1. Se nell'asserzione SAML è previsto un valore di ruolo, seguire questa procedura:

   a. Nell'elenco a discesa del riquadro **Seleziona ruolo** selezionare il ruolo appropriato per l'utente.  
   b. Selezionare il pulsante **Seleziona** nella parte inferiore della schermata.
1. Nel riquadro **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

## <a name="configure-netsuite-sso"></a>Configurare l'accesso Single Sign-On di NetSuite

1. Aprire una nuova scheda nel browser e accedere al sito aziendale di NetSuite come amministratore.

2. Nella barra di spostamento in alto selezionare **Setup** (Configura) e quindi **Company** (Società)  > **Enable Features** (Abilita funzionalità).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Sulla barra degli strumenti al centro della pagina selezionare **SuiteCloud**.

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

4. In **Manage Authentication** (Gestisci autenticazione) selezionare la casella di controllo **SAML Single Sign-On** (Accesso SSO SAML) per abilitare l'opzione per l'acesso Single Sign-On SAML in NetSuite.

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Nella barra di spostamento in alto selezionare **Setup** (Configura).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

6. Nell'elenco **Setup Tasks** (Configura attività) selezionare **Integration** (Integrazione).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

7. In **Manage Authentication** (Gestisci autenticazione) selezionare **SAML Single Sign-on** (Accesso SSO SAML).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

8. Nella sezione **NetSuite Configuration** (Configurazione NetSuite) del riquadro **SAML Setup** (Configurazione SAML) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selezionare la casella di controllo **Primary Authentication Method** (Metodo di autenticazione primario).

    b. In **SAMLV2 Identity Provider Metadata** (Metadati provider di identità SAMLV2) selezionare **Upload IDP Metadata File** (Carica file di metadati IDP) e quindi **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    c. Selezionare **Submit** (Invia).

9. Nella barra di spostamento in alto di NetSuite selezionare **Setup** (Configura) e quindi **Company** (Società)  > **Company Information** (Informazioni sulla società).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Nel riquadro **Company Information** (Informazioni sulla società) copiare il valore di **Account ID** (ID account) nella colonna di destra.

    c. Incollare il valore di **Account ID** (ID account) copiato dall'account NetSuite nella casella **Valore attributo** in Azure AD. 

10. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che le autorizzazioni appropriate vengano assegnate agli utenti in NetSuite. Per assegnare queste autorizzazioni, seguire questa procedura:

    a. Nella barra di spostamento in alto selezionare **Setup** (Configura).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    b. Nel riquadro sinistro selezionare **Users/Roles** (Utenti/Ruoli) e quindi **Manage Roles** (Gestisci ruoli).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selezionare **New Role** (Nuovo ruolo).

    d. In **Name** (Nome) immettere un nome per il nuovo ruolo.

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selezionare **Salva**.

    f. Nella barra di spostamento in alto selezionare **Permissions** (Autorizzazioni). Selezionare quindi **Setup** (Configura).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. Selezionare **SAML Single Sign-on** (Accesso SSO SAML) e quindi **Add** (Aggiungi).

    h. Selezionare **Salva**.

    i. Nella barra di spostamento in alto selezionare **Setup** (Configura) e quindi **Setup Manager** (Configura responsabile).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. Nel riquadro sinistro selezionare **Users/Roles** (Utenti/Ruoli) e quindi **Manage Users** (Gestisci utenti).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selezionare un utente di test, scegliere **Edit** (Modifica) e quindi selezionare la scheda **Access** (Accesso).

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Nel riquadro **Roles** (Ruoli) assegnare il ruolo appropriato creato.

    ![Configura accesso Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selezionare **Salva**.

### <a name="create-the-netsuite-test-user"></a>Creare l'utente di test di NetSuite

In questa sezione viene creato un utente di nome B.Simon in NetSuite. NetSuite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in NetSuite, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di NetSuite nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di NetSuite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Provare NetSuite con Azure AD](https://aad.portal.azure.com/)


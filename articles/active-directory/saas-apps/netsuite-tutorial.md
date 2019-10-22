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
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438503"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Esercitazione: Integrazione dell'accesso Single Sign-On (SSO) di Azure Active Directory con NetSuite

Questa esercitazione descrive come integrare NetSuite con Azure Active Directory (Azure AD). Integrando NetSuite con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a NetSuite.
* Abilitare gli utenti per l'accesso automatico a NetSuite con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di NetSuite abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NetSuite supporta l'accesso SSO avviato da **IdP**

* NetSuite supporta il provisioning utenti **JIT**

* NetSuite supporta il [provisioning utenti automatico](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="adding-netsuite-from-the-gallery"></a>Aggiunta di NetSuite dalla raccolta

Per configurare l'integrazione di NetSuite in Azure AD, è necessario aggiungere NetSuite dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **NetSuite** nella casella di ricerca.
1. Selezionare **NetSuite** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurare e testare l'accesso Single Sign-On di Azure AD per NetSuite

Configurare e testare l'accesso SSO di Azure AD con NetSuite usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in NetSuite.

Per configurare e testare l'accesso SSO di Azure AD con NetSuite, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di NetSuite](#configure-netsuite-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di NetSuite](#create-netsuite-test-user)** : per avere una controparte di B.Simon in NetSuite collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **NetSuite** del [portale di Azure](https://portal.azure.com/) individuare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    Nella casella di testo **URL di risposta** digitare un URL nel formato seguente:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di risposta effettivo. Per ottenere il valore contattare il [team di supporto di NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. L'applicazione NetSuite prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Fare clic su **Modifica** per aprire la finestra di dialogo Attributi utente.

    ![image](common/edit-attribute.png)

1. Oltre quelli elencati in precedenza, l'applicazione NetSuite prevede il passaggio di altri attributi nella risposta SAML. Nella sezione Attestazioni utente della finestra di dialogo Attributi utente eseguire la procedura seguente per aggiungere l'attributo del token SAML come illustrato nella tabella seguente:

    | Nome | Attributo di origine | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    1. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    1. Lasciare vuota la casella **Spazio dei nomi**.

    1. Per Origine selezionare **Attributo**.

    1. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    1. Fare clic su **OK**.

    1. Fare clic su **Save**.

    >[!NOTE]
    >Il valore dell'attributo account non è reale. È necessario aggiornarlo, come descritto più avanti nell'esercitazione.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare il file **XML dei metadati della federazione** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura NetSuite** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a NetSuite.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **NetSuite**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-netsuite-sso"></a>Configurare l'accesso Single Sign-On di NetSuite

1. Aprire una nuova scheda nel browser e accedere al sito della società NetSuite come amministratore.

2. Sulla barra degli strumenti nella parte superiore della pagina, fare clic su **Setup** (Installazione), quindi passare a **Company** (Società) e fare clic su **Enable Features** (Attiva funzionalità).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Sulla barra degli strumenti al centro della pagina, fare clic su **SuiteCloud**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Nella sezione **Manage Authentication** (Gestisci autenticazione) selezionare **SAML SINGLE SIGN-ON** per abilitare l'opzione SAML SINGLE SIGN-ON in NetSuite.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Sulla barra degli strumenti nella parte superiore della pagina fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

6. Dall'elenco **SETUP TASKS** (Configura attività) selezionare **Integration** (Integrazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

7. Nella sezione **MANAGE AUTHENTICATION** (Gestione autenticazione) fare clic su **SAML Single Sign-on**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

8. Nella pagina **SAML Setup** (Configurazione SAML), nella sezione **NetSuite Configuration** (Configurazione NetSuite) seguire questa procedura:

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selezionare **PRIMARY AUTHENTICATION METHOD** (Metodo di autenticazione primario).

    b. Per il campo con etichetta **SAMLV2 IDENTITY PROVIDER METADATA** (Metadati provider identità SAMLV2) selezionare **UPLOAD IDP METADATA FILE** (Carica file di metadati IDP). Fare quindi clic su **Browse** (Sfoglia) per caricare il file di metadati scaricato dal portale di Azure.

    c. Fare clic su **Submit** (Invia).

9. In NetSuite fare clic su **Setup** (Configurazione) quindi passare a **Company** (Società) e fare clic su **Company Information** (Informazioni sulla società) dal menu di spostamento superiore.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    b. Nella pagina **Company Information** (Informazioni sulla società) sulla colonna destra copiare **ID ACCOUNT**.

    c. Incollare l'**ID account** copiato dall'account NetSuite nel campo **Valore attributo** in Azure AD. 

10. Per consentire l'accesso Single Sign-On a NetSuite, è prima di tutto necessario che le autorizzazioni appropriate vengano assegnate agli utenti in NetSuite. Per assegnare le autorizzazioni, seguire le istruzioni seguenti.

    a. Dal menu di navigazione principale fare clic su **Setup** (Configurazione).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    b. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Roles** (Gestisci ruoli).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Fare clic su **New Role**.

    d. Digitare un **nome** per il nuovo ruolo.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. Fare clic su **Save**.

    f. Scegliere **Permissions**dal menu disponibile nella parte superiore. Fare quindi clic su **Setup**.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. Selezionare **SAML Single Sign-on**, quindi fare clic su **Aggiungi**.

    h. Fare clic su **Save**.

    i. Scegliere **Setup** (Configurazione) dal menu di navigazione principale, quindi fare clic su **Setup Manager** (Configura gestore).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. Scegliere **Users/Roles** (Utenti/Ruoli) dal menu di navigazione a sinistra, quindi fare clic su **Manage Users** (Gestisci utenti).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selezionare un utente di test. Quindi fare clic su **Edit** (Modifica) e passare alla scheda **Access** (Accesso).

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Nella finestra di dialogo Roles (Ruoli) selezionare il ruolo creato.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. Fare clic su **Save**.

### <a name="create-netsuite-test-user"></a>Creare l'utente di test di NetSuite

In questa sezione viene creato un utente di nome Britta Simon in NetSuite. NetSuite supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in NetSuite, ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di NetSuite nel pannello di accesso, si dovrebbe accedere automaticamente all'istanza di NetSuite per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Provare NetSuite con Azure AD](https://aad.portal.azure.com/)


---
title: 'Esercitazione: Integrazione di Azure Active Directory con Amazon Business | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Amazon Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496566"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Esercitazione: Integrare Amazon Business con Azure Active Directory

Questa esercitazione descrive come integrare Amazon Business con Azure Active Directory (Azure AD). Integrando [Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Amazon Business.
* Abilitare gli utenti per l'accesso automatico ad Amazon Business con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Accesso Single Sign-On alle applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere una versione di valutazione gratuita per un mese [qui](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di Amazon Business abilitata per l'accesso Single Sign-On (SSO). Passare alla pagina [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) per creare un account Amazon Business.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un account Amazon Business esistente.

* Amazon Business supporta l'accesso SSO avviato da **SP e IDP**
* Amazon Business supporta il provisioning utenti **JIT**

## <a name="adding-amazon-business-from-the-gallery"></a>Aggiunta di Amazon Business dalla raccolta

Per configurare l'integrazione di Amazon Business in Azure AD, è necessario aggiungere Amazon Business dalla raccolta all'elenco di app SaaS gestite.

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Amazon Business** nella casella di ricerca.
1. Selezionare **Amazon Business** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

Configurare e testare l'accesso Single Sign-On di Azure AD con Amazon Business usando un utente di test di nome **B.Simon**.

Per configurare e testare l'accesso SSO di Azure AD con Amazon Business, completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Amazon Business](#configure-amazon-business-sso)** : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B. Simon.
4. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B. Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di Amazon Business](#create-amazon-business-test-user)** : per avere una controparte di B.Simon in Amazon Business collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **Amazon Business** del [portale di Azure](https://portal.azure.com/) trovare la sezione **Gestione** e selezionare **Single Sign-On**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona Modifica (la penna) relativa a **Configurazione SAML di base** per modificare le impostazioni.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    1. Nella casella di testo **Identificatore (ID entità)** digitare un URL in uno dei formati seguenti:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. Nella casella di testo **URL di risposta** digitare un URL in uno dei formati seguenti:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > Il valore di URL di risposta non è reale. è necessario aggiornare questo valore con l'URL di risposta effettivo. Si otterrà il valore `<idpid>` dalla sezione Configurazione SSO di Amazon Business, illustrato più avanti nell'esercitazione. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare un URL: `https://www.amazon.com/`

1. Lo screenshot seguente mostra l'elenco degli attributi predefiniti. Modificare gli attributi facendo clic sull'icona **Modifica** nella sezione **Attributi utente e attestazioni**.

    ![Attributi](media/amazon-business-tutorial/map-attribute3.png)

1. Modificare gli attributi e copiare il valore di **Spazio dei nomi** di questi attributi nel Blocco note.

    ![Attributi](media/amazon-business-tutorial/map-attribute4.png)

1. Oltre quelli elencati in precedenza, l'applicazione Amazon Business prevede il passaggio di qualche altro attributo nella risposta SAML. Nella sezione **Attributi utente e attestazioni** della finestra di dialogo **Attestazioni dei gruppi** seguire questa procedura:

    a. Fare clic sulla **penna** accanto a **Gruppi restituiti nell'attestazione**.

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Selezionare **Tutti i gruppi** dall'elenco dei pulsanti di opzione.

    c. Selezionare **ID gruppo** come **Attributo di origine**.

    d. Selezionare la casella di controllo **Personalizza il nome dell'attestazione basata su gruppo** e immettere il nome del gruppo in base alle esigenze dell'organizzazione.

    e. Fare clic su **Save**.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **XML dei metadati** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/metadataxml.png)

1. Nella sezione **Configura Amazon Business** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Configurare l'accesso SSO di Amazon Business

1. In un'altra finestra del Web browser accedere al sito aziendale di Amazon Business come amministratore.

1. Fare clic su **User Profile** (Profilo utente) e selezionare **Business settings** (Impostazioni aziendali).

    ![Profilo utente](media/amazon-business-tutorial/user-profile.png)

1. Nella procedura guidata **System integrations** (Integrazioni di sistema) selezionare **Single Sign-On (SSO)** .

    ![Accesso Single Sign-On (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Nella procedura guidata **Set up SSO** (Configura accesso SSO) selezionare il provider in base alle esigenze dell'organizzazione e fare clic su **Next** (Avanti).

    ![Gruppo predefinito](media/amazon-business-tutorial/default-group1.png)

1. Nella procedura guidata **New user account defaults** (Impostazioni predefinite nuovo account utente) selezionare **Default Group** (Gruppo predefinito) e quindi selezionare **Default Buying Role** (Ruolo di acquisto predefinito) in base al ruolo utente nell'organizzazione, infine fare clic su **Next** (Avanti).

    ![Gruppo predefinito](media/amazon-business-tutorial/dafault-group2.png)

1. Nella procedura guidata **Upload your metadata file** (Carica file di metadati) fare clic su **Browse** (Sfoglia) per caricare il file **XML dei metadati** scaricato dal portale di Azure e fare clic su **Upload** (Carica).

    ![Dati della connessione](media/amazon-business-tutorial/connection-data1.png)

1. Dopo il caricamento dei file di metadati scaricato, i campi nella sezione **Connection data** (Dati della connessione) verranno compilati automaticamente. A questo punto, fare clic su **Next** (Avanti).

    ![Dati della connessione](media/amazon-business-tutorial/connection-data2.png)

1. Nella procedura guidata **Upload your Attribute statement** (Carica dichiarazione degli attributi) fare clic su **Skip** (Ignora).

    ![Attributi](media/amazon-business-tutorial/map-attribute1.png)

1. Nella procedura guidata **Attribute mapping** (Mapping attributi) aggiungere i campi richiesti facendo clic sull'opzione **+ Add a field** (+ Aggiungi un campo). Aggiungere i valori degli attributi, incluso lo spazio dei nomi copiato dalla sezione **Attributi utente e attestazioni** del portale di Azure nel campo **SAML AttributeName** (Nome attributo SAML) e fare clic su **Next** (Avanti).

    ![Attributi](media/amazon-business-tutorial/map-attribute2.png)

1. Nella procedura guidata **Amazon connection data** (Dati della connessione ad Amazon) fare clic su **Next** (Avanti).

    ![Connessione](media/amazon-business-tutorial/amazon-connect.png)

1. Selezionare lo **stato** dei passaggi configurati e fare clic su **Start testing** (Avvia il test).

    ![Connessione](media/amazon-business-tutorial/sso-connection1.png)

1. Nella procedura guidata **Test SSO Connection** (Testa connessione SSO) fare clic su **Test** (Esegui test).

    ![Connessione](media/amazon-business-tutorial/sso-connection2.png)

1. Nella procedura guidata **IDP initiated URL** (URL avviato da IDP), prima di fare clic su **Activate** (Attiva), copiare il valore assegnato a **idpid** e incollarlo nel parametro **idpid** nell'**URL di risposta** nella sezione **Configurazione SAML di base** del portale di Azure.

    ![Connessione](media/amazon-business-tutorial/sso-connection3.png)

1. Nella procedura guidata **Are you ready to switch to active SSO?** (Passare all'accesso SSO attivo?) selezionare la casella di controllo **I have fully tested SSO and am ready to go live** (L'accesso SSO è stato testato ed è possibile accedere) e fare clic su **Switch to active** (Passa ad attivo).

    ![Connessione](media/amazon-business-tutorial/sso-connection4.png)

1. Infine, nella sezione **SSO Connection details** (Dettagli della connessione SSO) **Status** (Stato) è impostato su **Active** (Attivo).

    ![Connessione](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

> [!NOTE]
> Gli amministratori devono creare gli utenti di test nei propri tenant, se necessario. La procedura seguente descrive come creare un utente di test.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Create**(Crea).

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Creare un gruppo di sicurezza di Azure AD nel portale di Azure

1. Fare clic su **Azure Active Directory > Tutti i gruppi**.

    ![Creare un gruppo di sicurezza di Azure AD](./media/amazon-business-tutorial/all-groups-tab.png)

1. Fare clic su **Nuovo gruppo**:

    ![Creare un gruppo di sicurezza di Azure AD](./media/amazon-business-tutorial/new-group-tab.png)

1. Compilare **Tipo di gruppo**, **Nome gruppo**, **Descrizione gruppo**, **Tipo di appartenenza**. Fare clic sulla freccia per selezionare i membri e quindi cercare o fare clic sul membro che si desidera aggiungere al gruppo. Fare clic su **Seleziona** per aggiungere i membri selezionati e quindi fare clic su **Crea**.

    ![Creare un gruppo di sicurezza di Azure AD](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Amazon Business.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Amazon Business**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.

   ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Collegamento Aggiungi utente](common/add-assign-user.png)

1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

    >[!NOTE]
    > Se non si assegnano gli utenti in Azure AD, viene visualizzato l'errore seguente.

    ![Collegamento Aggiungi utente](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Assegnare il gruppo di sicurezza di Azure AD nel portale di Azure

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Amazon Business**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Amazon Business**.

    ![Collegamento di Amazon Business nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic su **Aggiungi utente**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Cercare il gruppo di sicurezza da usare e quindi fare clic sul gruppo per aggiungerlo alla sezione per la selezione dei membri. Fare clic su **Seleziona** e quindi su **Assegna**.

    ![Ricerca del gruppo di sicurezza](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Controllare le notifiche nella barra dei menu per ricevere una notifica in cui si specifica che il gruppo è stato assegnato correttamente all'applicazione aziendale nel portale di Azure.

### <a name="create-amazon-business-test-user"></a>Creare l'utente di test di Amazon Business

In questa sezione viene creato un utente di nome B.Simon in Amazon Business. Amazon Business supporta il provisioning utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già un utente in Amazon Business, ne viene creato uno nuovo dopo l'autenticazione.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Amazon Business nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Amazon Business per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

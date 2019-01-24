---
title: 'Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Mimecast Admin Console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.openlocfilehash: 9ad9be1d1573bd3c57a3462f1f6ab53152a4469d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809735"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Esercitazione: Integrazione di Azure Active Directory con Mimecast Admin Console

Questa esercitazione descrive come integrare Mimecast Admin Console con Azure Active Directory (Azure AD).
L'integrazione di Mimecast Admin Console con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a Mimecast Admin Console.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a Mimecast Admin Console con gli account Azure AD personali.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Mimecast Admin Console, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione di Mimecast Admin Console abilitata per l'accesso Single Sign-On

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Mimecast Admin Console supporta l'accesso SSO avviato da **SP**

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Aggiunta di Mimecast Admin Console dalla raccolta

Per configurare l'integrazione di Mimecast Admin Console in Azure AD, è necessario aggiungere Mimecast Admin Console dalla raccolta all'elenco delle app SaaS gestite.

**Per aggiungere Mimecast Admin Console dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Mimecast Admin Console**, selezionare **Mimecast Admin Console** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![Mimecast Admin Console nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Mimecast Admin Console usando un utente di test di nome **Britta Simon**.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Mimecast Admin Console.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Mimecast Admin Console, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare l'accesso Single Sign-On di Mimecast Admin Console](#configure-mimecast-admin-console-single-sign-on)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare un utente di test di Mimecast Admin Console](#create-mimecast-admin-console-test-user)**: per avere una controparte di Britta Simon in Mimecast Admin Console collegata alla rappresentazione dell'utente in Azure AD.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con Mimecast Admin Console, seguire questa procedura:

1. Nella pagina di integrazione dell'applicazione **Mimecast Admin Console** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Mimecast Admin Console](common/sp-signonurl.png)

    Nella casella di testo **URL accesso** digitare l'URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > L’URL di accesso è specifico dell’area geografica.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il **Certificato (Base64)** dalle opzioni specificate in base ai propri requisiti e salvarlo nel computer in uso.

    ![Collegamento di download del certificato](common/certificatebase64.png)

6. Nella sezione **Configura Mimecast Admin Console** copiare gli URL appropriati in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore Azure AD

    c. URL di chiusura sessione

### <a name="configure-mimecast-admin-console-single-sign-on"></a>Configurare l'accesso Single Sign-On per Mimecast Admin Console

1. In un'altra finestra del Web browser accedere a Mimecast Admin Console come amministratore.

2. Passare a **Services \> Application**.

    ![Servizi](./media/mimecast-admin-console-tutorial/ic794998.png "Servizi")

3. Fare clic su **Authentication Profiles**.

    ![Profili di autenticazione](./media/mimecast-admin-console-tutorial/ic794999.png "Profili di autenticazione")
    
4. Fare clic su **New Authentication Profile**.

    ![Nuovi profili di autenticazione](./media/mimecast-admin-console-tutorial/ic795000.png "Nuovi profili di autenticazione")

5. Nella sezione **Authentication Profile** , eseguire la procedura seguente:

    ![Profilo di autenticazione](./media/mimecast-admin-console-tutorial/ic795015.png "Profilo di autenticazione")
    
    a. Nella casella di testo **Description** digitare un nome per la configurazione.
    
    b. Selezionare **Enforce SAML Authentication for Mimecast Admin Console**.
    
    c. Come **Provider** selezionare **Azure Active Directory**.
    
    d. Incollare il valore di **Identificatore Azure AD**, copiato dal portale di Azure, nella casella di testo **URL autorità di certificazione**.
    
    e. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **URL di accesso**.

    f. Incollare il valore di **URL di accesso**, copiato dal portale di Azure, nella casella di testo **URL disconnessione**.
    
    >[!NOTE]
    >Il valore di Login URL e il valore Logout URL per Mimecast Admin Console sono identici.
    
    g. Aprire il certificato con codifica Base 64 scaricato dal portale di Azure nel Blocco note, rimuovere la prima riga ("*--*") e l'ultima riga ("*--*"), copiare il contenuto rimanente negli Appunti e quindi incollarlo nella casella di testo **Identity Provider Certificate (Metadata)** (Certificato provider di identità - Metadati).
    
    h. Selezionare **Allow Single Sign On**.
    
    i. Fare clic su **Save**.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel campo **Nome utente** digitare **brittasimon@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure tramite la concessione dell'accesso a Mimecast Admin Console.

1. Nel portale di Azure selezionare **Applicazioni aziendali**, quindi **Tutte le applicazioni** e infine **Mimecast Admin Console**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni digitare e selezionare **Mimecast Admin Console**.

    ![Collegamento Mimecast Admin Console nell'elenco Applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-mimecast-admin-console-test-user"></a>Creare l'utente di test di Mimecast Admin Console

Per consentire agli utenti di Azure AD di accedere a Mimecast Admin Console, è necessario eseguirne il provisioning in Mimecast Admin Console. Nel caso di Mimecast Admin Console, il provisioning è un’attività manuale.

* Per poter creare gli utenti è necessario registrare un dominio.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. Accedere a **Mimecast Admin Console** come amministratore.

2. Accedere a **Directories \> Internal**.
   
    ![Directory](./media/mimecast-admin-console-tutorial/ic795003.png "Directory")

3. Fare clic su **Register New Domain**.
   
    ![Registra nuovo dominio](./media/mimecast-admin-console-tutorial/ic795004.png "Registra nuovo dominio")

4. Dopo aver creato il nuovo dominio, fare clic su **New Address**.
   
    ![Nuovo indirizzo](./media/mimecast-admin-console-tutorial/ic795005.png "Nuovo indirizzo")

5. Nella finestra di dialogo del nuovo indirizzo, seguire questa procedura:
   
    ![Salvare](./media/mimecast-admin-console-tutorial/ic795006.png "Salvare")
   
    a. Nelle caselle di testo corrispondenti digitare gli attributi **indirizzo di posta elettronica**, **nome globale**, **password** e **conferma password** di un account Azure AD valido di cui si intende effettuare il provisioning.

    b. Fare clic su **Save**.

>[!NOTE]
>È possibile usare qualsiasi altro strumento di creazione di account utente di Mimecast Admin Console o le API fornite da Mimecast Admin Console per effettuare il provisioning degli account utente di Azure AD. 

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di Mimecast Admin Console nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Mimecast Admin Console per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 'Esercitazione: Integrazione di Azure Active Directory con iProva | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6576366df214304bb30c46fa65a4bc8bc783cedf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56182103"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Esercitazione: Integrazione di Azure Active Directory con iProva

Questa esercitazione descrive come integrare iProva con Azure Active Directory (Azure AD).
L'integrazione di iProva con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a iProva.
* È possibile abilitare gli utenti per l'accesso automatico (Single Sign-On) a iProva con gli account Azure AD.
* È possibile gestire gli account da una posizione centrale, il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS (Software as a Service) con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iProva sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese nel sito Web di [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di iProva abilitata per l'accesso Single Sign-On.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test:

* iProva supporta l'accesso SSO avviato da SP.

## <a name="add-iprova-from-the-gallery"></a>Aggiungere iProva dalla raccolta

Per configurare l'integrazione di iProva in Azure AD, aggiungere iProva dalla raccolta all'elenco di app SaaS gestite.

Per aggiungere iProva dalla raccolta, seguire questa procedura:

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **iProva**. Selezionare **iProva** nel pannello dei risultati e quindi selezionare **Aggiungi** per aggiungere l'applicazione.

     ![iProva nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione nomi configurato e testato l'accesso Single Sign-On di Azure AD con iProva usando un utente di test di nome Britta Simon.
Per il corretto funzionamento dell'accesso Single Sign-On, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iProva.

Per configurare e testare l'accesso Single Sign-On di Azure AD con iProva, completare le procedure di base seguenti:

- [Recuperare le informazioni di configurazione da iProva](#retrieve-configuration-information-from-iprova): operazioni preliminari per i passaggi successivi.
- [Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on) per consentire agli utenti di usare questa funzionalità.
- [Configurare l'accesso Single Sign-On per iProva](#configure-iprova-single-sign-on): per configurare le impostazioni di Single Sign-On sul lato applicazione.
- [Creare un utente di test di Azure AD](#create-an-azure-ad-test-user) per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
- [Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user) per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
- [Creare un utente di test di iProva ](#create-an-iprova-test-user): per avere una controparte di Britta Simon in iProva collegata alla rappresentazione dell'utente in Azure AD.
- [Testare l'accesso Single Sign-On](#test-single-sign-on) per verificare se la configurazione funziona.

### <a name="retrieve-configuration-information-from-iprova"></a>Recuperare le informazioni di configurazione da iProva

In questa sezione si recuperano da iProva le informazioni per configurare l'accesso Single Sign-On di Azure AD.

1. Aprire un Web browser e passare alla pagina **SAML2 info** (Informazioni su SAML2) in iProva usando l'URL nel formato seguente:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Visualizzare la pagina delle informazioni SAML2 in iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Lasciare aperta la scheda del browser mentre si procede con i passaggi successivi in un'altra scheda del browser.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare l'accesso Single Sign-On di Azure AD con iProva, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione [iProva](https://portal.azure.com/) del **portale di Azure** selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Icona Modifica in Configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura.

    a. Compilare la casella **Identificatore** con il valore visualizzato con l'etichetta **EntityID** (ID entità) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    b. Compilare la casella **URL di risposta** con il valore visualizzato con l'etichetta **Reply URL** (URL di risposta) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    c. Compilare la casella **URL di accesso** con il valore visualizzato con l'etichetta **Sign-on URL** (URL di accesso) nella pagina **iProva SAML2 info** (Informazioni SAML2 iProva). Questa pagina è ancora aperta nell'altra scheda del browser.

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iProva](common/sp-identifier-reply.png)

5. L'applicazione iProva prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Modifica** per aprire la finestra di dialogo **Attributi utente**.

    ![Finestra di dialogo Attributi utente](common/edit-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente** configurare l'attributo del token SAML come illustrato nell'immagine precedente. Seguire questa procedura.

    | NOME | Attributo di origine| Spazio dei nomi |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Selezionare **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![Attestazioni utente](common/new-save-attribute.png)

    ![Finestra di dialogo Gestisci attestazioni utente](common/new-attribute-details.png)

    b. Nella casella **Nome** immettere il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Spazio dei nomi** immettere il valore dello spazio dei nomi indicato per la riga.

    d. Selezionare l'opzione **Origine** come valore di **Attributo**.

    e. Nell'elenco **Attributo di origine** immettere il valore dell'attributo indicato per la riga.

    f. Selezionare **OK**.

    g. Selezionare **Salva**.

7. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** selezionare l'icona **Copia** per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Configurare l'accesso Single Sign-On per iProva

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

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

In questa sezione viene creato un utente di test di nome Britta Simon nel portale di Azure.

1. Nel riquadro di sinistra del portale di Azure selezionare **Azure Active Directory** > **Utenti** > **Tutti gli utenti**.

    ![Collegamenti Utenti e gruppi e Tutti gli utenti](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. Nella finestra di dialogo **Utente** seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nella casella **Nome** immettere un nome, ad esempio **Britta Simon**.
  
    b. Nella casella **Nome utente** immettere *yourname@yourcompanydomain.extension*. 
    Un esempio è BrittaSimon@contoso.com.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Selezionare **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione, l'utente Britta Simon viene abilitato per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a iProva.

1. Nel portale di Azure selezionare **Applicazioni aziendali** > **Tutte le applicazioni** > **iProva**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **iProva**.

    ![Collegamento di iProva nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento Utenti e gruppi](common/users-groups-blade.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Finestra di dialogo Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco **Utenti** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco. Scegliere **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** selezionare **Assegna**.

### <a name="create-an-iprova-test-user"></a>Creare un utente di test di iProva

1. Accedere a iProva usando l'account **Administrator**.

2. Aprire il menu **Go to** (Vai a).

3. Selezionare **Application management** (Gestione applicazioni).

4. Selezionare **Users** (Utenti) nel pannello **Users and user groups** (Utenti e gruppi di utenti).

5. Selezionare **Aggiungi**.

6. Nella casella **Username** (Nome utente) immettere *brittasimon@yourcompanydomain.extension*. 
    Un esempio è BrittaSimon@contoso.com.

7. Nella casella **Full name** (Nome completo) immettere il nome completo, ad esempio **Britta Simon**.

8. Selezionare l'opzione **No password (use single sign-on)** (Nessuna password - usa Single Sign-On).

9. Nella casella **E-mail address** (Indirizzo di posta elettronica) immettere *yourname@yourcompanydomain.extension*. 
   Un esempio è BrittaSimon@contoso.com.

10. Scorrere fino alla fine della pagina e selezionare **Finish** (Fine).

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si seleziona il riquadro di iProva nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione iProva per cui si è configurato l'accesso SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva - Come configurare l'accesso Single Sign-On SAML2](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)

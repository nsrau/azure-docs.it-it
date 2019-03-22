---
title: 'Esercitazione: integrazione di Azure Active Directory con ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e ON24 Virtual Environment SAML Connection.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: f95420397a502af8e939c8a53fe5635b82dc0f39
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893646"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Esercitazione: integrazione di Azure Active Directory con ON24 Virtual Environment SAML Connection

Questa esercitazione descrive come integrare ON24 Virtual Environment SAML Connection con Azure Active Directory (Azure AD).
L'integrazione di ON24 Virtual Environment SAML Connection con Azure AD offre i vantaggi seguenti:

* È possibile controllare in Azure AD chi può accedere a ON24 Virtual Environment SAML Connection.
* È possibile abilitare gli utenti di essere automaticamente connesso a ON24 virtuale SAML connessione all'ambiente (Single Sign-On) con i propri account Azure AD.
* È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con ON24 Virtual Environment SAML Connection, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si dispone di un ambiente Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/)
* Sottoscrizione abilitata per connessione ON24 virtuale ambiente SAML single sign-on

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Supporta la connessione SAML all'ambiente virtuale ON24 **SP** e **IDP** SSO avviato dal

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Aggiunta di ON24 Virtual Environment SAML Connection dalla raccolta

Per configurare l'integrazione di ON24 Virtual Environment SAML Connection in Azure AD, è necessario aggiungere ON24 Virtual Environment SAML Connection dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere ON24 Virtual Environment SAML Connection dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca, digitare **ON24 Virtual Environment SAML Connection**, selezionare **ON24 Virtual Environment SAML Connection** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

     ![ON24 virtuale SAML connessione all'ambiente nell'elenco risultati](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato single sign-on di Azure con ON24 virtuali ambiente di connessione SAML usando un utente test chiamato **Britta Simon**.
Per single sign-on funzioni, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in connessione SAML all'ambiente virtuale ON24.

Per configurare e testare l'accesso Single Sign-On di Azure AD con ON24 Virtual Environment SAML Connection è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Configurare ON24 virtuale ambiente connessione Single Sign-On SAML](#configure-on24-virtual-environment-saml-connection-single-sign-on)**  : per configurare le impostazioni di Single Sign-On sul lato applicazione.
3. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Creare l'utente di test di connessione SAML all'ambiente virtuale ON24](#create-on24-virtual-environment-saml-connection-test-user)**  : per avere una controparte di Britta Simon in ON24 virtuali ambiente di connessione SAML collegata alla rappresentazione in Azure AD dell'utente.
6. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure.

Per configurare single sign-on di Azure con connessione ON24 virtuale ambiente SAML, seguire i passaggi seguenti:

1. Nella pagina di integrazione dell'applicazione **ON24 Virtual Environment SAML Connection** del [portale di Azure](https://portal.azure.com/) selezionare **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On](common/select-sso.png)

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare il Single Sign-On.

    ![Selezione della modalità Single Sign-On](common/select-saml-option.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![URL e dominio connessione SAML ON24 virtuale ambiente informazioni single sign-on](common/idp-relay.png)

    a. Nella casella di testo **Identificatore** digitare un URL:

     **URL ambiente di produzione**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL ambiente di controllo di qualità**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Nella casella di testo **URL di risposta** digitare un URL:

     **URL ambiente di produzione**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL ambiente di controllo di qualità**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Fare clic su **Impostare URL aggiuntivi**. 

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Per configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    ![URL e dominio connessione SAML ON24 virtuale ambiente informazioni single sign-on](common/both-signonurl.png)

    Nella casella di testo **URL accesso** digitare un URL nel formato seguente: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Poiché questi non sono i valori reali, Aggiornarli con l'URL di stato dell'inoltro e Sign-on effettivo. Contattare [team di supporto clienti di ON24 virtuale ambiente SAML connessione](https://www.on24.com/contact-us/) per ottenere questi valori. È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

4. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nel **configurare connessione SAML all'ambiente virtuale ON24** sezione, copiare l'URL appropriato in base alle proprie esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configurare ON24 ambiente virtuale connessione Single Sign-On SAML

Per configurare l'accesso single sign-on nei **connessione SAML all'ambiente virtuale ON24** , è necessario inviare il file **XML dei metadati di federazione** e appropriato degli URL copiato dal portale di Azure per [ Il team di supporto di connessione SAML all'ambiente virtuale ON24](https://www.on24.com/about-us/support/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD 

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](common/users.png)

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Pulsante Nuovo utente](common/new-user.png)

3. In Proprietà utente seguire questa procedura.

    ![Finestra di dialogo Utente](common/user-properties.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel **nome utente** tipo di campo **brittasimon\@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Fare clic su **Create**(Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a ON24 Virtual Environment SAML Connection.

1. Nel portale di Azure, selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **connessione SAML all'ambiente virtuale ON24**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **ON24 Virtual Environment SAML Connection**.

    ![Il collegamento di connessione SAML all'ambiente virtuale ON24 nell'elenco delle applicazioni](common/all-applications.png)

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"](common/users-groups-blade.png)

4. Fare clic sul pulsante **Aggiungi utente** e quindi selezionare **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione](common/add-assign-user.png)

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Se si prevede un valore di ruolo nell'asserzione SAML, nella finestra di dialogo **Selezionare un ruolo** selezionare il ruolo appropriato per l'utente dall'elenco, quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.

7. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Creare l'utente di test di connessione SAML all'ambiente virtuale ON24

In questa sezione viene creato un utente chiamato Britta Simon in ON24 Virtual Environment SAML Connection. Collaborare con il  [team di supporto clienti di withON24 Virtual Environment SAML Connection](https://www.on24.com/about-us/support/) per aggiungere gli utenti alla piattaforma ON24 Virtual Environment SAML Connection. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro di connessione SAML all'ambiente virtuale ON24 nel Pannello di accesso, dovrebbe automaticamente accedere ON24 virtuale ambiente SAML connessione per il quale configurare SSO. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Che cos'è l'accesso condizionale in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


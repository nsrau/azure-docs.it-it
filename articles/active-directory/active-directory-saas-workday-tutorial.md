---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workday | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jeedes
ms.openlocfilehash: 1dfe319e708e6a4e815413da1a7bf635f4d0a53d
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Esercitazione: Integrazione di Azure Active Directory con Workday

Questa esercitazione descrive come integrare Workday con Azure Active Directory (Azure AD).

L'integrazione di Workday con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workday.
- È possibile abilitare gli utenti per l'accesso automatico a Workday (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Workday, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Workday abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Workday dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-workday-from-the-gallery"></a>Aggiunta di Workday dalla raccolta
Per configurare l'integrazione di Workday in Azure AD, è necessario aggiungere Workday dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workday dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Workday**, selezionare **Workday** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Workday nell'elenco dei risultati](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workday in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Workday che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workday.

Per stabilire la relazione di collegamento, in Workday assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workday, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Workday](#create-a-workday-test-user)**: per avere una controparte di Britta Simon in Workday collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On con l'applicazione Workday.

**Per configurare l'accesso Single Sign-On di Azure AD con Workday, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Workday** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. Nella sezione **URL e dominio Workday** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Nella casella di testo **Identificatore** digitare un URL: `http://www.workday.com`

4. Selezionare **Mostra URL impostazioni avanzate** ed eseguire l'operazione seguente:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. L'URL di risposta deve avere un sottodominio, ad esempio www, wd2, wd3, wd3-impl, wd5, wd5-impl. Usare qualcosa come "*http://www.myworkday.com*" funziona mentre "*http://myworkday.com*" non funziona. Per ottenere tali valori, contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html).  

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. Nella sezione **Configurazione di Workday** fare clic su **Configura Workday** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Workday](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

9. Nella **casella di ricerca** in alto a sinistra della home page eseguire una ricerca di **Edit Tenant Setup - Security** (Modifica configurazione tenant - Sicurezza).
   
    ![Edit Tenant Security](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

10. Nella sezione **Redirection URLs** seguire questa procedura:
   
    ![Redirection URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")
   
    a. Fare clic su **Aggiungi riga**.
   
    b. Nelle caselle di testo **URL di reindirizzamento dell'accesso** e **URL di reindirizzamento dispositivi mobili** digitare l'**URL di accesso** immesso nella sezione **URL e dominio Workday** del portale di Azure.
   
    c. Nella finestra **Configura accesso** del portale di Azure copiare l'**URL di accesso** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.

    d. Nella casella di testo **Used for Environments** (Usato per gli ambienti) selezionare il nome dell'ambiente.  

    >[!NOTE]
    > Il valore dell'attributo Environment è collegato al valore dell'URL del tenant:  
    >-Se il nome di dominio dell'URL tenant di Workday inizia con impl (ad esempio *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), l'attributo **Environment** deve essere impostato su Implementation.  
    >-Se il nome di dominio inizia con altro, è necessario contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) per ottenere il valore **Environment** corrispondente.

11. Nella sezione **SAML Setup** seguire questa procedura:
   
    ![Configurazione SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configurazione SAML")
   
    a.  Selezionare **Enable SAML authentication**.
   
    b.  Fare clic su **Aggiungi riga**.

12. Nella sezione **SAML Identity Providers** (Provider di identità SAML) seguire questa procedura:
   
    ![SAML Identity Providers](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    a. Nella casella di testo **Identity Provider Name** (Nome provider di identità) digitare il nome del provider, ad esempio *SPInitiatedSSO*.
   
    b. Nella finestra **Configura accesso** del portale di Azure copiare l'**ID entità SAML** e incollarlo nella casella dell'**autorità emittente**.

    ![SAML Identity Providers](./media/active-directory-saas-workday-tutorial/IC7829271(1).png "SAML Identity Providers")
   
    c. Selezionare **Enable Workday Initiated Logout** (Abilita disconnessione avviata da Workday).
   
    d. Nella finestra **Configura accesso** del portale di Azure copiare l'**URL di accesso** e incollarlo nella casella di testo dell'**URL di risposta di disconnessione**.

    e. Nella finestra **Configura accesso** del portale di Azure copiare l'**URL del servizio Single Sign-On SAML** e quindi incollarlo nella casella dell'**URL del servizio SSO IdP**.

    f. Nella casella di testo **Used for Environments** (Usato per gli ambienti) selezionare il nome dell'ambiente.

    g. Fare clic su **Certificato di chiave pubblica del provider di identità** e quindi su **Crea**. 

    ![Creare](./media/active-directory-saas-workday-tutorial/IC782928.png "Creare")

    h. Fare clic su **Crea chiave pubblica x509**. 

    ![Creare](./media/active-directory-saas-workday-tutorial/IC782929.png "Creare")

13. Nella sezione **Visualizza chiave pubblica x509** eseguire la procedura seguente: 
   
    ![View x509 Public Key](./media/active-directory-saas-workday-tutorial/IC782930.png "View x509 Public Key") 
   
    a. Nella casella di testo **Name** digitare un nome per il certificato, ad esempio *PPE\_SP*.
   
    b. Nella casella di testo **Valid From** digitare il valore dell'attributo di inizio validità del certificato.
   
    c.  Nella casella di testo **Valid To** digitare il valore dell'attributo di fine validità del certificato.
   
    > [!NOTE]
    > Per individuare la data di inizio e di fine validità, fare doppio clic sul certificato scaricato.  Le date sono elencate nella scheda **Details** .
    > 
    >
   
    d.  Aprire il certificato con codifica Base 64 nel Blocco note e quindi copiarne il contenuto.
   
    e.  Nella casella di testo **Certificate** incollare il valore copiato negli Appunti.
   
    f.  Fare clic su **OK**.

14. Eseguire la procedura seguente: 
   
    ![SSO configuration](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")
   
    a.  Nella casella di testo **Service Provider ID** digitare **http://www.workday.com**.
   
    b. Selezionare **Do Not Deflate SP-initiated Authentication Request**.
   
    c. In **Authentication Request Signature Method** selezionare **SHA256**. 
   
    ![Authentication Request Signature Method](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 
   
    d. Fare clic su **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-workday-test-user"></a>Creare un utente di test di Workday

In questa sezione viene creato un utente chiamato Britta Simon in Workday. Collaborare con il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) per aggiungere gli utenti nella piattaforma Workday. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workday.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Workday, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Workday**.

    ![Collegamento Workday nell'elenco delle applicazioni](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Workday nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Workday.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png


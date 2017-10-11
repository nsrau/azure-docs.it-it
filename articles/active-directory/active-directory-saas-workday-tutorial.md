---
title: 'Esercitazione: Integrazione di Azure Active Directory con Workday | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Esercitazione: Integrazione di Azure Active Directory con Workday

Questa esercitazione descrive come integrare Workday con Azure Active Directory (Azure AD).

L'integrazione di Workday con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Workday
- È possibile abilitare gli utenti per l'accesso automatico a Workday (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Workday, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Workday abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Workday dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-workday-from-the-gallery"></a>Aggiunta di Workday dalla raccolta
Per configurare l'integrazione di Workday in Azure AD, è necessario aggiungere Workday dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Workday dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Workday**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. Nel pannello dei risultati selezionare **Workday** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Workday in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Workday che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Workday.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Workday.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Workday, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Workday](#creating-a-workday-test-user)**: per avere una controparte di Britta Simon in Workday collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On con l'applicazione Workday.

**Per configurare l'accesso Single Sign-On di Azure AD con Workday, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Workday** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. Nella sezione **URL e dominio Workday** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. Nella casella di testo **URL di accesso** digitare il valore come: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso e l'URL di risposta effettivi. L'URL di risposta deve avere un sottodominio, ad esempio www, wd2, wd3, wd3-impl, wd5, wd5-impl. Usare qualcosa come "*http://www.myworkday.com*" funziona mentre "*http://myworkday.com*" non funziona. Per ottenere tali valori, contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html). 
 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Workday** fare clic su **Configura Workday** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurare l'accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. In un'altra finestra del Web browser accedere al sito aziendale di Workday come amministratore.

8. Passare a **Menu \> Workbench**.
   
    ![Workbench](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

9. Passare a **Account Administration**.
   
    ![Account Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

10. Passare a **Edit Tenant Setup – Security**.
   
    ![Edit Tenant Security](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

11. Nella sezione **Redirection URLs** seguire questa procedura:
   
    ![Redirection URLs](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")
   
    a. Fare clic su **Aggiungi riga**.
   
    b. Nelle caselle di testo **URL di reindirizzamento dell'accesso** e **URL di reindirizzamento dispositivi mobili** digitare l'**URL di accesso** immesso nella sezione **URL e dominio Workday** del portale di Azure.
   
    c. Nella finestra **Configura accesso** del portale di Azure copiare l'**URL di accesso** e incollarlo nella casella di testo **URL di reindirizzamento disconnessione**.
   
    d.  Nella casella di testo **Environment** digitare il nome dell'ambiente.  

    >[!NOTE]
    > Il valore dell'attributo Environment è collegato al valore dell'URL del tenant:  
    >-Se il nome di dominio dell'URL tenant di Workday inizia con impl (ad esempio *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), l'attributo **Environment** deve essere impostato su Implementation.  
    >-Se il nome di dominio inizia con altro, è necessario contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) per ottenere il valore **Environment** corrispondente.

12. Nella sezione **SAML Setup** seguire questa procedura:
   
    ![Configurazione SAML](./media/active-directory-saas-workday-tutorial/IC782926.png "Configurazione SAML")
   
    a.  Selezionare **Enable SAML authentication**.
   
    b.  Fare clic su **Aggiungi riga**.

13. Nella sezione SAML Identity Providers seguire questa procedura:
   
    ![SAML Identity Providers](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    a. Nella casella di testo Identity Provider Name digitare il nome del provider, ad esempio *SPInitiatedSSO*.
   
    b. Nella finestra **Configura accesso** del portale di Azure copiare l'**ID entità SAML** e incollarlo nella casella dell'**autorità emittente**.
   
    c. Selezionare **Enable Workday Initiated Logout** (Abilita disconnessione avviata da Workday).
   
    d. Nella finestra **Configura accesso** del portale di Azure copiare l'**URL di accesso** e incollarlo nella casella dell'**URL di richiesta disconnessione**.

    e. Fare clic su **Certificato di chiave pubblica del provider di identità** e quindi su **Crea**. 

    ![Creare](./media/active-directory-saas-workday-tutorial/IC782928.png "Creare")

    f. Fare clic su **Crea chiave pubblica x509**. 

    ![Creare](./media/active-directory-saas-workday-tutorial/IC782929.png "Creare")


14. Nella sezione **Visualizza chiave pubblica x509** eseguire la procedura seguente: 
   
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

15. Eseguire la procedura seguente: 
   
    ![SSO configuration](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")
   
    a.  Abilitare **x509 Private Key Pair**.
   
    b.  Nella casella di testo **Service Provider ID** digitare **http://www.workday.com**.
   
    c.  Selezionare **Abilita autenticazione SAML SP initiated**.
   
    d.  Nella finestra **Configura accesso** del portale di Azure copiare l'**URL del servizio Single Sign-On SAML** e quindi incollarlo nella casella dell'**URL del servizio SSO IdP**.
   
    e. Selezionare **Do Not Deflate SP-initiated Authentication Request**.
   
    f. In **Authentication Request Signature Method** selezionare **SHA256**. 
   
    ![Authentication Request Signature Method](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 
   
    g. Fare clic su **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
>

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-workday-test-user"></a>Creazione di un utente test di Workday

Per eseguire il provisioning di un utente test in Workday, è necessario contattare il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html).
Il [team di supporto clienti di Workday](https://www.workday.com/en-us/partners-services/services/support.html) provvederà a creare l'utente.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Workday.

![Assegna utente][200] 

**Per assegnare Britta Simon a Workday, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Workday**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Per testare le impostazioni di Single Sign-On, aprire il pannello di accesso. Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-workday-inbound-tutorial.md)



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


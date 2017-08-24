---
title: 'Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 85723eeb9c030dc51bdad47ea8e0996ff7868336
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con SAP Business Object Cloud

Questa esercitazione descrive come integrare SAP Business Object Cloud con Azure Active Directory (Azure AD).

L'integrazione di SAP Business Object Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a SAP Business Object Cloud
- È possibile consentire agli utenti di accedere automaticamente a SAP Business Object Cloud (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con SAP Business Object Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di SAP Business Object Cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di SAP Business Object Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Aggiunta di SAP Business Object Cloud dalla raccolta
Per configurare l'integrazione di SAP Business Object Cloud in Azure AD, è necessario aggiungere SAP Business Object Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere SAP Business Object Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **SAP Business Object Cloud**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. Nel pannello dei risultati selezionare **SAP Business Object Cloud** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud con un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di SAP Business Object Cloud che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in SAP Business Object Cloud.

Per stabilire la relazione di collegamento, in SAP Business Object Cloud assegnare il valore di **nome utente** in Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di SAP Business Object Cloud](#creating-an-sap-business-object-cloud-test-user)**: per avere una corrispondenza di Britta Simon in SAP Business Object Cloud collegata alla relativa rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione SAP Business Object Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con SAP Business Object Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **SAP Business Object Cloud** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. Nella sezione **SAP Business Object Cloud Domain and URLs** (URL e dominio SAP Business Object Cloud) seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<sub-domain>.projectorca.cloud/`.

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `<sub-domain>.projectorca.cloud`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Contattare il [team di supporto del client SAP Business Object Cloud](https://www.sap.com/product/analytics/cloud-analytics.support.html) per ottenere l'URL di accesso. Sarà quindi possibile ottenere l'identificatore scaricando i metadati di SAP Business Object Cloud dalla console di amministrazione che è illustrata più avanti nell'esercitazione. 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di SAP Business Object Cloud come amministratore.

7. Passare a **Menu > Sistema > Amministrazione**
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. In **Sicurezza** fare clic sulla scheda **[Modifica-icona della penna]**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. Selezionare **SAML Single Sign-On (SSO)** come metodo di autenticazione.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. Fare clic su **Download** per scaricare i metadati del Provider di servizi. Recuperare il valore **entityID** dal file e incollarlo nella casella di testo **Identificatore** nella sezione **SAP Business Object Cloud Domain and URLs** (URL e dominio SAP Business Object Cloud) nel portale di Azure.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. Nel passaggio **Upload Identity Provider metadata** (Carica metadati del provider di identità) fare clic sul pulsante **Caricamento...** per caricare il file di metadati scaricato dal portale di Azure. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. Selezionare l'**attributo utente** appropriato che si desidera usare per l'implementazione dall'elenco per eseguire il mapping al provider di identità. Usare l'opzione "Custom SAML Mapping" (Mapping SAML personalizzato) per specificare un attributo personalizzato della pagina utenti o selezionare "Email" (Indirizzo di posta elettronica) o "USER ID" (ID UTENTE) come attributo utente. Ad esempio, è selezionato **Email** poiché è stato eseguito il mapping di attestazione dell'ID utente con l'attributo userprincipalname nella sezione **"Attributi utente"** nel portale di Azure, che fornisce un indirizzo di posta elettronica univoco dell'utente che viene inviato all'applicazione SAP Business Object Cloud in ogni risposta SAML corretta.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. Immettere il messaggio di posta elettronica nella casella di testo **Login Credential (Email)** (Credenziali di accesso (Email)) e fare clic sul pulsante **Verifica account** per consentire al sistema di aggiungere le credenziali di accesso al proprio account.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. Fare clic sull'icona di **salvataggio**

    ![Salva](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>Creazione di un utente di test di SAP Business Object Cloud

Per consentire agli utenti di Azure AD di accedere a SAP Business Object Cloud, è necessario eseguirne il provisioning in SAP Business Object Cloud.  
Nel caso di SAP Business Object Cloud, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di SAP Business Object Cloud come amministratore.

2. Passare a **Menu > Sicurezza > Utenti**

    ![Aggiungere un dipendente](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. Nella pagina **Utenti** fare clic su **"+"** per aggiungere nuovi dettagli utente e seguire questa procedura:

    ![Invita persone](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. Nella casella di testo **USER ID** (ID UTENTE) digitare l'ID dell'utente, ad esempio, Britta.

    b. Digitare il nome dell'utente, ad esempio Britta, nella casella di testo **FIRST NAME** (NOME).

    c. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **LAST NAME** (COGNOME).

    d. Nella casella di testo **DISPLAY NAME** (NOME VISUALIZZATO) digitare il nome completo dell'utente, ad esempio Britta Simon.

    e. Nella casella di testo **E-MAIL** (INDIRIZZO DI POSTA ELETTRONICA) digitare l'indirizzo di posta elettronica dell'utente, ad esempio brittasimon@contoso.com.

    f. Selezionare il ruolo appropriato per l'utente.

      ![Ruolo](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. Fare clic sull'icona di **salvataggio**. 


### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata all'uso di Single Sign-On di Azure attraverso la concessione dell'accesso a SAP Business Object Cloud.

![Assegna utente][200] 

**Per assegnare Britta Simon a SAP Business Object Cloud, eseguire i seguenti passaggi:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **SAP Business Object Cloud**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro SAP Business Object Cloud nel pannello di accesso, si accederà automaticamente all'applicazione SAP Business Object Cloud.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png



---
title: 'Esercitazione: Integrazione di Azure Active Directory con CloudPassage | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 094740e20570665e975dec1a591989e411f90c16
ms.contentlocale: it-it
ms.lasthandoff: 06/22/2017

---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Esercitazione: Integrazione di Azure Active Directory con CloudPassage

Questa esercitazione descrive come integrare CloudPassage con Azure Active Directory (Azure AD).

L'integrazione di CloudPassage con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a CloudPassage.
- È possibile abilitare gli utenti per l'accesso automatico a CloudPassage (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con CloudPassage, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di CloudPassage abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di CloudPassage dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-cloudpassage-from-the-gallery"></a>Aggiunta di CloudPassage dalla raccolta
Per configurare l'integrazione di CloudPassage in Azure AD, è necessario aggiungere CloudPassage dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere CloudPassage dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **CloudPassage**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. Nel pannello dei risultati selezionare **CloudPassage** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con CloudPassage mediante un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di CloudPassage corrisponde a un determinato utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in CloudPassage.

Per stabilire la relazione di collegamento, in CloudPassage assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con CloudPassage, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di CloudPassage](#creating-a-cloudpassage-test-user)**: per avere una controparte di Britta Simon in CloudPassage collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel nuovo portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione CloudPassage.

**Per configurare Single Sign-On di Azure AD con CloudPassage, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **CloudPassage** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. Nella sezione **URL e dominio CloudPassage** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://portal.cloudpassage.com/saml/init/accountid`.

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://portal.cloudpassage.com/saml/consume/accountid`. È possibile ottenere il valore di questo attributo facendo clic su **SSO Setup documentation** (Documentazione della configurazione SSO) nella sezione **Single Sign-on Settings** (Impostazioni Single Sign-On) del portale di CloudPassage.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori contattare il [team di supporto clienti di CloudPassage](https://www.cloudpassage.com/company/contact/). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. L'applicazione CloudPassage prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. La schermata seguente illustra un esempio relativo a questa operazione.
   
   ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:

    | Nome attributo | Valore attributo |
    | --- | --- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | email |user.mail |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_400.png)
    
8. Nella sezione **Configurazione di CloudPassage** fare clic su **Configura CloudPassage** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. In un'altra finestra del Web browser accedere al sito aziendale di CloudPassage come amministratore.

10. Nel menu in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
    ![Configura accesso Single Sign-On][12]

11. Fare clic sulla scheda **Authentication Settings** (Impostazioni autenticazione). 
   
    ![Configura accesso Single Sign-On][13]

12. Nella sezione **Single Sign-On Settings** (Impostazioni Single Sign-On) seguire questa procedura: 
   
    ![Configura accesso Single Sign-On][14]

    a. Selezionare la casella di controllo **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Abilita Single Sign-On (SSO) (Documentazione di configurazione di SSO).
    
    b. Incollare **SAML Entity ID** (ID entità SAML) nella casella di testo **SAML issuer URL** (URL autorità di certificazione SAML).
  
    c. Incollare **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) nella casella di testo **SAML endpoint URL** (URL endpoint SAML).
  
    d. Incollare **Sign-Out URL** (URL di disconnessione) nella casella di testo **Logout landing page** (Pagina di destinazione disconnessione).
  
    e. Aprire il certificato scaricato nel Blocco note, copiare il contenuto del certificato negli Appunti e quindi incollarlo nella casella di testo **Certificato X.509**.
  
    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Creazione di un utente test CloudPassage

Questa sezione descrive come creare un utente chiamato Britta Simon in CloudPassage.

**Per creare un utente test denominato Britta Simon in CloudPassage, seguire questa procedura:**

1. Accedere al sito aziendale di **CloudPassage** come amministratore. 

2. Nella barra degli strumenti in alto fare clic su **Settings** (Impostazioni) e quindi su **Site Administration** (Amministrazione sito). 
   
   ![Creazione di un utente test CloudPassage][22] 

3. Fare clic sulla scheda **Users** (Utenti) e quindi fare clic su **Add New User** (Aggiungi nuovo utente). 
   
   ![Creazione di un utente test CloudPassage][23]

4. Nella sezione **Aggiungi nuovo utente** eseguire la procedura seguente: 
   
   ![Creazione di un utente test CloudPassage][24]
    
    a. Nella casella di testo **Nome** digitare Britta. 
  
    b. Nella casella di testo **Cognome** digitare Simon.
  
    c. Nelle caselle di testo **Username** (Nome utente), **Email** (Indirizzo di posta elettronica) e **Retype Email** (Digitare nuovamente l'indirizzo di posta elettronica) digitare il nome utente di Britta in Azure AD.
  
    d. In **Access Type** (Tipo di accesso) selezionare **Enable Halo Portal Access** (Abilita accesso portale Halo).
  
    e. Fare clic su **Aggiungi**.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a CloudPassage.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Amazon Web Service (AWS), seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **CloudPassage**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Questa sezione descrive come testare la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro CloudPassage nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione CloudPassage.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_203.png



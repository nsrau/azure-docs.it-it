---
title: 'Esercitazione: Integrazione di Azure Active Directory con Bonusly | Documentazione Microsoft'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Esercitazione: Integrazione di Azure Active Directory con Bonusly

Questa esercitazione descrive come integrare Bonusly con Azure Active Directory (Azure AD).

L'integrazione di Bonusly con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Bonusly
- È possibile abilitare gli utenti per l'accesso automatico a Bonusly (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Bonusly, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Bonusly abilitata per l'accesso Single Sign-On


> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.


A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Bonusly dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD


## <a name="adding-bonusly-from-the-gallery"></a>Aggiunta di Bonusly dalla raccolta
Per configurare l'integrazione di Bonusly in Azure AD, è necessario aggiungerla dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Bonusly dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Bonusly**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. Nel pannello dei risultati selezionare **Bonusly** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Bonusly usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Bonusly che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Bonusly.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Bonusly.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Bonusly, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Bonusly](#creating-a-bonusly-test-user)**: per avere una controparte di Britta Simon in Bonusly collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Bonusly.

**Per configurare Single Sign-On di Azure AD con Bonusly, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Bonusly** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. Nella sezione **URL e dominio Bonusly**, se si vuole configurare l'applicazione in **modalità avviata da IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. Nella casella di testo **Identificatore** digitare il valore `bonusly`
    
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://bonus.ly/saml/<APP-ID>/consume`
    
4. Per configurare l'applicazione in **modalità avviata da SP**, nella sezione **URL e dominio Bonusly** seguire questa procedura:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. Fare clic sull'opzione **Mostra impostazioni URL avanzate**

    b. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://bonus.ly/saml/<your_subdomain>/consume`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'URL di accesso, l'ID e l'URL di risposta effettivo. In questo caso è consigliabile usare in Identificatore il valore univoco della stringa. Per ottenere questi valori contattare il [team di supporto di Bonusly](mailto:sales@easyterritory.com).

5. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. Nella finestra di dialogo **Crea nuovo certificato** fare clic sull'icona del calendario e selezionare una **data di scadenza**. Fare quindi clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. Nella sezione **Certificato di firma SAML** selezionare **Rendi attivo il certificato nuovo** e fare clic sul pulsante **Salva**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. Nella finestra popup **Certificato di rollover** fare clic su **OK**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. Nella sezione **Configurazione di Bonusly** fare clic su **Configura Bonusly** per aprire la finestra **Configura accesso**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. In un'altra finestra del Web browser accedere al sito aziendale di Bonusly come amministratore.

12. Nella barra degli strumenti in alto fare clic su **Settings** (Impostazioni) e quindi selezionare **Integrations and apps** (Integrazioni e app).

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. In **Single Sign-On** selezionare **SAML**.

14. Nella pagina della finestra di dialogo **SAML** eseguire la procedura seguente:

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. Nella casella di testo **IdP SSO target URL** (URL di destinazione SSO IdP) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    b. Nella casella di testo **IdP Login URL** (URL di accesso IdP) inserire il valore di **SAML Single Sign-On Service URL** (URL servizio Single Sign-On SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    c. Nella casella di testo **IdP Issuer** (Autorità di certificazione IDP) inserire il valore di **SAML Entity ID** (ID entità SAML) dalla finestra di configurazione dell'applicazione di Azure AD.

    d. Copiare il valore di Identificazione personale dal certificato scaricato e incollarlo nella casella di testo **Cert Fingerprint** (Impronta digitale del certificato).

    e. Fare clic su **Save**.

    > [!NOTE] 
    > Per informazioni dettagliate, vedere il video che illustra [come recuperare il valore di identificazione personale di un certificato](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be).



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** eseguire la procedura seguente:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 



### <a name="creating-a-bonusly-test-user"></a>Creazione di un utente test di Bonusly

Per consentire agli utenti di Azure AD di accedere a Bonus.ly, è necessario eseguirne il provisioning in Bonusly.
Nel caso di Bonusly, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, seguire questa procedura:

1. Accedere al sito aziendale di Bonusly come amministratore.

2. Fare clic su **Impostazioni**.

    ![Nuovo utente](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "Nuovo utente")

3. Fare clic sulla scheda **Users and bonuses** .

    ![Nuovo utente](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "Nuovo utente")

4. Fare clic su **Gestisci utenti**.

    ![Nuovo utente](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "Nuovo utente")

5. Fare clic su **Aggiungi utente**.

    ![Nuovo utente](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "Nuovo utente")

6. Nella sezione **Add User** seguire questa procedura:

    ![Nuovo utente](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "Nuovo utente")

    a. Nella casella di testo **Nome** digitare **Britta**.  

    b. Nella casella di testo **Cognome** digitare **Simon**.

    c. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.

    d. Fare clic su **Save**.

    > [!NOTE] 
    > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo. È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da Bonus.ly per eseguire il provisioning degli account utente di Azure AD.



### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, concedendole l'accesso a Bonusly.

![Assegna utente][200] 

**Per assegnare Britta Simon a Bonusly, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione applicazioni, passare alla visualizzazione directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Bonusly**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    


### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Bonusly nel pannello di accesso, si accederà automaticamente all'applicazione Bonusly.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png

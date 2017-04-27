---
title: 'Esercitazione: Integrazione di Azure Active Directory con Adobe Creative Cloud | Microsoft Docs'
description: Informazioni su come configurare l&quot;accesso Single Sign-On tra Azure Active Directory e Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9ba1171e-56b1-4475-b308-58637d35e5a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 3d13608612c77236346b0e98551d7fc427d602e1
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con Adobe Creative Cloud

Questa esercitazione descrive come integrare Adobe Creative Cloud con Azure Active Directory (Azure AD).

L'integrazione di Adobe Creative Cloud con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Adobe Creative Cloud
- È possibile abilitare gli utenti per l'accesso automatico ad Adobe Creative Cloud (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account da una posizione centrale: il portale di gestione di Azure

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Adobe Creative Cloud, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Adobe Creative Cloud abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione, a meno che non sia necessario.
- Se non è disponibile un ambiente di prova di Azure AD, è possibile ottenere una versione di prova di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Adobe Creative Cloud dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Aggiungere Adobe Creative Cloud dalla raccolta
Per configurare l'integrazione di Adobe Creative Cloud in Azure AD, è necessario aggiungere Adobe Creative Cloud dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Adobe Creative Cloud dalla raccolta, seguire questa procedura:**

1. Nel **[portale di gestione di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Aggiungi** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Adobe Creative Cloud**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_000.png)

5. Nel pannello dei risultati selezionare **Adobe Creative Cloud** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud con un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Adobe Creative Cloud che corrisponde a un utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Adobe Creative Cloud.

La relazione di collegamento viene stabilita assegnando il valore del **nome utente** di Azure AD come valore di **Username** in Adobe Creative Cloud.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'uso di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di Adobe Creative Cloud](#creating-an-adobe-creative-cloud-test-user)**: per avere una controparte di Britta Simon in Adobe Creative Cloud collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di gestione di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Adobe Creative Cloud.

**Per configurare l'accesso Single Sign-On di Azure AD con Adobe Creative Cloud, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Adobe Creative Cloud** del portale di gestione di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** in **Modalità** selezionare **Accesso basato su SAML** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_01.png)

3. Nella sezione **URL e dominio Adobe Creative Cloud**, se si vuole configurare l'applicazione in modalità avviata da **IDP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url1.png)

    a. Nella casella di testo **Identificatore** digitare il valore `https://www.okta.com/saml2/service-provider/<token>`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE] 
    > Si noti che questi non sono i valori reali. È necessario aggiornare questi valori con l'identificatore e l'URL di risposta effettivi. In questo caso è consigliabile usare in Identificatore il valore univoco della stringa. Per creare un utente manualmente, è necessario contattare il team di supporto di Adobe Creative Cloud.

4. Nella sezione **URL e dominio Adobe Creative Cloud**, se si vuole configurare l'applicazione in modalità avviata da **SP**, seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_url2.png)

    a. Fare clic sull'opzione **Mostra impostazioni URL avanzate**

    b. Nella casella di testo **URL di accesso** digitare il valore come: `https://adobe.com`

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_05.png) 

6. Nella sezione **Configurazione di Adobe Creative Cloud** fare clic su **Configura Adobe Creative Cloud** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** e l'**URL del servizio SSO SAML** dalla sezione Riferimento rapido.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_06.png) 

7. In un'altra finestra del Web browser accedere al tenant Adobe Creative Cloud come amministratore.

8.  Passare a **Identity** (Identità) nel riquadro di spostamento a sinistra e scegliere il dominio. Eseguire i passaggi descritti di seguito nella sezione di **configurazione dell'accesso Single Sign-On**.

    ![Impostazioni](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_001.png "Impostazioni")

9. Fare clic su **Browse** (Sfoglia) per caricare il certificato scaricato da Azure AD in **IDP Certificate** (Certificato IDP).

10. Nella casella di testo **IDP issuer** (Autorità di certificazione IDP) inserire il valore dell'**ID entità SAML** copiato in precedenza dalla sezione **Configura accesso** del portale di Azure.

11. Nella casella di testo **IDP Login URL** (URL di accesso IDP) inserire il valore dell'**URL del servizio SSO SAML** copiato in precedenza dalla sezione **Configura accesso** del portale di Azure.

12. Selezionare **HTTP - Redirect** (HTTP - Reindirizzamento) per **IDP Binding** (Binding IDP).

13. Selezionare **Email Address** (Indirizzo di posta elettronica) per **User Login Setting** (Impostazione accesso utente).
 
14. Fare clic sul pulsante **Salva** .

15. Il dashboard visualizza ora il file XML **"Download Metadata"**, che contiene l'URL EntityDescriptor e l'URL AssertionConsumerService di Adobe. Aprire il file e configurare gli URL nell'applicazione Azure AD.

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_002.png)

    ![Configurazione accesso Single Sign-On sul lato app](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Usare il valore di EntityDescriptor indicato da Adobe per l'**identificatore** nella finestra di dialogo **Configurare le impostazioni dell'app**.

    b. Usare il valore di AssertionConsumerService specificato da Adobe per **URL di risposta** nella finestra di dialogo **Configurare le impostazioni dell'app**.
 
### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test chiamato Britta Simon nel portale di gestione di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di gestione di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_01.png) 

2. Andare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**. 

### <a name="creating-an-adobe-creative-cloud-test-user"></a>Creare un utente test di Adobe Creative Cloud

Per consentire agli utenti di Azure AD di accedere ad Adobe Creative Cloud, è necessario eseguirne il provisioning in Adobe Creative Cloud.  
Nel caso di Adobe Creative Cloud il provisioning è un'attività manuale.

**Per effettuare il provisioning di un account utente, seguire questa procedura:**

1. Accedere al sito aziendale di Adobe Creative Cloud come amministratore.

2. Fare clic su **Persone**.

    ![Persone](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_001.png "Persone")

3. Fare clic su **Invite User**.

    ![Invitare utenti](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_002.png "Invitare utenti")

4. Nella finestra di dialogo **Invite People** (Invita persone) seguire questa procedura:

    ![Invitare persone](./media/active-directory-saas-adobe-creative-cloud-tutorial/create_aaduser_003.png "Invitare persone")

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica dell'account di Britta Simon.
    
    b. Fare clic su **Invita**.

    > [!NOTE]
    > Il titolare dell'account Azure Active Directory riceverà un messaggio di posta elettronica con un collegamento da selezionare per confermare l'account e attivarlo.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione viene concesso a Britta Simon l'accesso ad Adobe Creative Cloud per consentirle di usare l'accesso Single Sign-On di Azure.

![Assegna utente][200] 

**Per assegnare Britta Simon ad Adobe Creative Cloud, seguire questa procedura:**

1. Nel portale di gestione di Azure aprire la visualizzazione con le applicazioni e quindi passare alla visualizzazione con le directory e andare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Adobe Creative Cloud**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_50.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Adobe Creative Cloud nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Adobe Creative Cloud.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-creative-cloud-tutorial/tutorial_general_203.png

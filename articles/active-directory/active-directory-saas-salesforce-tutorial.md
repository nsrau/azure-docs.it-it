---
title: 'Esercitazione: Integrazione di Azure Active Directory con Salesforce | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.contentlocale: it-it
ms.lasthandoff: 06/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Esercitazione: Integrazione di Azure Active Directory con Salesforce

Questa esercitazione descrive come integrare Salesforce con Azure Active Directory (Azure AD).

L'integrazione di Salesforce con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Salesforce
- È possibile abilitare gli utenti per l'accesso automatico a Salesforce (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Salesforce, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Salesforce abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Salesforce dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-salesforce-from-the-gallery"></a>Aggiunta di Salesforce dalla raccolta
Per configurare l'integrazione di Salesforce in Azure AD, è necessario aggiungere Salesforce dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Salesforce dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **Salesforce**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. Nel pannello dei risultati selezionare **Salesforce** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Salesforce in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Salesforce che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Salesforce.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in Salesforce.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Salesforce, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di Salesforce](#creating-a-salesforce-test-user)**: per avere una controparte di Britta Simon in Salesforce collegata alla rappresentazione in Azure AD dell'utente.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Salesforce.

**Per configurare Single Sign-On di Azure AD con Salesforce, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Salesforce** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Nella sezione **URL e dominio Salesforce** seguire questa procedura:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    Nella casella di testo **URL di accesso** digitare il valore usando il modello seguente: 
   * Account aziendale: `https://<subdomain>.my.salesforce.com`
   * Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di accesso effettivo. Per ottenere questi valori, contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Salesforce** fare clic su **Configura Salesforce** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.** 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  Aprire una nuova scheda del browser e accedere all'account di amministratore di Salesforce.

8.  Nel pannello di navigazione **Administrator** fare clic su **Security Controls** per espandere la sezione correlata. Fare quindi clic su **Single Sign-On Settings**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  Nella pagina **Single Sign-On Settings** fare clic su **Edit**.
    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Se non si è in grado di abilitare le impostazioni dell'accesso Single Sign-On per l'account Salesforce, potrebbe essere necessario contattare il [team di supporto clienti di Salesforce](https://help.salesforce.com/support). 

10. Selezionare **SAML Enabled**, quindi fare clic su **Save**.

      ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. Per configurare le impostazioni dell'accesso Single Sign-On SAML, fare clic su **New**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. Nella pagina **SAML Single Sign-On Setting Edit** effettuare le configurazioni seguenti:

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. Nel campo **Name** specificare un nome descrittivo per la configurazione. Se si specifica un valore per **Name**, verrà popolata automaticamente la casella di testo **API Name**.

    b. Incollare il valore **SMAL Entity ID** nel campo **Issuer** in Salesforce.

    c. Nella casella di testo **Entity Id**immettere il nome di dominio di Salesforce nel formato seguente:
      
      * Account aziendale: `https://<subdomain>.my.salesforce.com`
      * Account sviluppatore: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Fare clic su **Browse** o **Choose File** per aprire la finestra di dialogo **Choose File to Upload**, selezionare il certificato Salesforce, quindi fare clic su **Open** per caricarlo.

    e. In **SAML Identity Type** selezionare **Assertion contains User's salesforce.com username**.

    f. In **SAML Identity Location** selezionare **Identity is in the NameIdentifier element of the Subject statement**

    g. Incollare **URL servizio Single Sign-On** nel campo **URL di accesso provider di identità** in Salesforce.
    
    h. Per **Service Provider Initiated Request Binding** selezionare **HTTP Redirect**.
    
    i. Fare infine clic su **Save** per applicare le impostazioni di SAML Single Sign-On.

13. Nel pannello di navigazione sinistro in Salesforce fare clic su **Domain Management** per espandere la sezione correlata, quindi fare clic su **My Domain**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. Scorrere verso il basso fino alla sezione **Authentication Configuration**, quindi fare clic su **Edit**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. Nella sezione **Authentication Service** selezionare il nome descrittivo della configurazione SAML Single Sign-On, quindi fare clic su **Save**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Se vengono selezionati più servizi di autenticazione, agli utenti viene richiesta la selezione del servizio di autenticazione da usare per l'accesso quando si tenta di avviare l'accesso Single Sign-On all'ambiente di Salesforce. Se non si vuole visualizzare la richiesta, è consigliabile **lasciare deselezionati tutti gli altri servizi di autenticazione**.
<CE>    
> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD)


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel riquadro di spostamento sinistro del **portale di Azure** fare clic sull'icona di **Azure Active Directory**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-salesforce-test-user"></a>Creazione di un utente test di Salesforce

In questa sezione si crea un utente di nome Britta Simon in Salesforce. Salesforce supporta il provisioning JIT (Just-In-Time) che è abilitato per impostazione predefinita.
Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in Salesforce, ne viene creato uno nuovo quando si tenta di accedere a Salesforce.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Salesforce.

![Assegna utente][200] 

**Per assegnare Britta Simon a Salesforce, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Salesforce**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

Per testare le impostazioni dell'accesso Single Sign-On, aprire il pannello di accesso all'indirizzo [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere all'account di test e fare clic su **Salesforce**.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png



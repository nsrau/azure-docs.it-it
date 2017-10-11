---
title: 'Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 29c99fdf39d366df90abc070f7b836320935035c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Esercitazione: Integrazione di Azure Active Directory con DocuSign

Questa esercitazione descrive come integrare DocuSign con Azure Active Directory (Azure AD).

L'integrazione di DocuSign con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a DocuSign
- È possibile abilitare gli utenti per l'accesso automatico a DocuSign (Single Sign-On) con i propri account Azure AD
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con DocuSign, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Una sottoscrizione di DocuSign abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non si dispone di un ambiente di prova di Azure AD, è possibile ottenere una versione di valutazione di un mese [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di DocuSign dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-docusign-from-the-gallery"></a>Aggiunta di DocuSign dalla raccolta
Per configurare l'integrazione di DocuSign in Azure AD, è necessario aggiungere DocuSign dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere DocuSign dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Applicazioni][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo.

    ![Applicazioni][3]

4. Nella casella di ricerca digitare **DocuSign**.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. Nel pannello dei risultati selezionare **DocuSign** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e test dell'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con DocuSign in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di DocuSign che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in DocuSign.

La relazione di collegamento viene stabilita assegnando il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente) in DocuSign.

Per configurare e testare l'accesso Single Sign-On di Azure AD con DocuSign, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione dell'accesso Single Sign-On di Azure AD](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente test di DocuSign](#creating-a-docusign-test-user)**: per avere una controparte di Britta Simon in DocuSign collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione DocuSign.

**Per configurare Single Sign-On di Azure AD con DocuSign, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **DocuSign** del portale di Azure fare clic su **Single Sign-On**.

    ![Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. Nella sezione **Configurazione DocuSign** del portale di Azure, fare clic su **Configura DocuSign** per aprire la finestra Configura accesso. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. In un'altra finestra del Web browser accedere al **portale di amministrazione di DocuSign** come amministratore.

6. Nel menu di navigazione a sinistra fare clic su **Domains**(Domini).
   
    ![Configurazione dell'accesso Single Sign-On][51]

7. Nel riquadro di destra fare clic su **Richiedi dominio**.
   
    ![Configurazione dell'accesso Single Sign-On][52]

8. Nella finestra di dialogo **Richiedi dominio** digitare il dominio aziendale nella casella di testo **Nome di dominio** e quindi fare clic su **Richiedi**. Verificare il dominio e che lo stato sia attivo.
   
    ![Configurazione dell'accesso Single Sign-On][53]

9. Nel riquadro di spostamento a sinistra fare clic su **Identity Providers**  
   
    ![Configurazione dell'accesso Single Sign-On][54]
10. Nel riquadro destro fare clic sul pulsante **Add Identity Provider**(Aggiungi provider di identità). 
   
    ![Configurazione dell'accesso Single Sign-On][55]

11. Nella pagina **Identity Provider Settings** (Impostazioni provider di identità) eseguire questa procedura:
   
    ![Configurazione dell'accesso Single Sign-On][56]

    a. Nella casella di testo **Name** (Nome) digitare un nome per la configurazione. Non usare spazi.

    b. Incollare l'**ID entità SAML** nella casella dell'**autorità di certificazione del provider di identità**.

    c. Incollare l'**URL del servizio Single Sign-On SAML** nella casella dell'**URL di accesso del provider di identità**.

    d. Incollare l'**URL di accesso** nella casella dell'**URL di disconnessione del provider di identità**.

    e. Selezionare **Sign AuthN Request**(Firma richiesta di autenticazione).

    f. Per **Invia richiesta di autenticazione da** selezionare **POST**.

    g. Per **Invia richiesta di disconnessione da** selezionare **GET**.

12. Nella sezione **Custom Attribute Mapping** (Mapping attributi personalizzati) scegliere il campo da associare all'attestazione di Azure AD. In questo esempio, viene eseguito il mapping dell'attestazione **emailaddress** con il valore di **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Si tratta del nome di attestazione predefinito di Azure AD per l'attestazione basata su posta elettronica. 
   
    > [!NOTE]
    > Usare l' **Identificatore utente** appropriato per associare l'utente da Azure AD al mapping degli utenti di DocuSign. Selezionare il campo corretto e immettere il valore appropriato in base alle impostazioni dell'organizzazione.
          
    ![Configurazione dell'accesso Single Sign-On][57]

13. Nella sezione **Certificato provider di identità** fare clic su **Aggiungi certificato** e quindi caricare il certificato scaricato dal portale di Azure.   
   
    ![Configurazione dell'accesso Single Sign-On][58]

14. Fare clic su **Salva**.

15. Nella sezione **Provider di identità** fare clic su **Azioni** e quindi su **Endpoint**.   
   
    ![Configurazione dell'accesso Single Sign-On][59]
 
16. Nella sezione **View SAML 2.0 Endpoints** (Visualizza endpoint SAML 2.0) del **portale di amministrazione di DocuSign** seguire questa procedura:
   
    ![Configurazione dell'accesso Single Sign-On][60]
   
    a. Copiare l'**URL dell'autorità di certificazione del provider di servizi**e incollarlo nella casella di testo **Identificatore** della sezione **URL e dominio DocuSign** del portale di Azure usando questo modello: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Copiare l'**URL di accesso del provider di servizi**e incollarlo nella casella di testo **URL di accesso** della sezione **URL e dominio DocuSign** del portale di Azure usando questo modello: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Fare clic su **Close**
    
17. Nel portale di Azure fare clic su **Salva**.
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="creating-a-docusign-test-user"></a>Creazione di un utente test di DocuSign

L'applicazione supporta il **provisioning dell'utente just-in-time** e dopo l'autenticazione gli utenti vengono automaticamente creati nell'applicazione.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione si abilita Britta Simon per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a DocuSign.

![Assegna utente][200] 

**Per assegnare Britta Simon a DocuSign, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **DocuSign**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro DocuSign nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione DocuSign.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Configura provisioning utenti](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png


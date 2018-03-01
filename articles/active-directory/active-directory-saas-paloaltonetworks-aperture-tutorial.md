---
title: 'Esercitazione: integrazione di Azure Active Directory con Palo Alto Networks - Aperture | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Palo Alto Networks - Aperture.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 75633cbf13756b4b2be3e4be055b12021cc396d2
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Esercitazione: integrazione di Azure Active Directory con Palo Alto Networks - Aperture

Questa esercitazione descrive come integrare Palo Alto Networks - Aperture con Azure Active Directory, ovvero Azure AD.

L'integrazione di Palo Alto Networks - Aperture con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Palo Alto Networks - Aperture.
- È possibile abilitare gli utenti per l'accesso automatico, ovvero per il Single Sign-On, a Palo Alto Networks - Aperture con gli account di Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Palo Alto Networks - Aperture, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione di Palo Alto Networks - Aperture abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Palo Alto Networks - Aperture dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Aggiunta di Palo Alto Networks - Aperture dalla raccolta
Per configurare l'integrazione di Palo Alto Networks - Aperture in Azure AD, è necessario aggiungere Palo Alto Networks - Aperture dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Palo Alto Networks - Aperture dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Palo Alto Networks - Aperture**, selezionare **Palo Alto Networks - Aperture** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Palo Alto Networks - Aperture nell'elenco dei risultati](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Aperture usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Palo Alto Networks - Aperture corrispondente all'utente di Azure AD. In altre parole deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Palo Alto Networks - Aperture.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Aperture, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Palo Alto Networks - Aperture](#create-a-palo-alto-networks---aperture-test-user)**: per avere una controparte di Britta Simon in Palo Alto Networks - Aperture collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Palo Alto Networks - Aperture.

**Per configurare l'accesso Single Sign-On di Azure AD con Palo Alto Networks - Aperture, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Palo Alto Networks - Aperture** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Nella sezione **URL e dominio Palo Alto Networks - Aperture** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Palo Alto Networks - Aperture](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support). 

5. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. Nella sezione **Configurazione di Palo Alto Networks - Aperture** fare clic su **Configure Palo Alto Networks - Aperture** (Configurare Palo Alto Networks - Aperture) per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla **sezione di riferimento rapido.**

    ![Collegamento di configurazione](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. In un'altra finestra del Web browser accedere a Palo Alto Networks - Aperture come amministratore.

9. Nella barra dei menu superiore fare clic su **SETTINGS** (IMPOSTAZIONI).

    ![Scheda Settings (Impostazioni)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Passare alla sezione **APPLICATION** (APPLICAZIONE) e fare clic sul modulo **Authentication** (Autenticazione) dal menu a sinistra.

    ![Scheda di autenticazione](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. Nella pagina **Authentication** (Autenticazione) seguire questa procedura:
    
    ![Scheda Authentication (Autenticazione)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Spuntare **Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)** (Abilitare Single Sign-On: i provider SSP supportati sono Okta, Onelogin) nel campo **Single Sign-On**.

    b. Nella casella di testo **Identity Provider ID** (ID provider di identità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    c. Fare clic su **Choose File** (Scegli file) per caricare il certificato scaricato da Azure AD nel campo **Identity Provider Certificate** (Certificato del provider di identità).

    d. Nella casella di testo **Identity provider SSO URL** (URL SSO provider di identità) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Esaminare le informazioni del provider di identità nella sezione **Aperture Info** (Informazioni di Aperture) e scaricare il certificato dal campo **Aperture Key** (Chiave di Aperture).

    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Creare un utente di test di Palo Alto Networks - Aperture

In questa sezione viene creato un utente di nome Britta Simon in Palo Alto Networks - Aperture. Contattare il [team di supporto clienti di Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) per aggiungere gli utenti alla piattaforma Palo Alto Networks - Aperture. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Palo Alto Networks - Aperture.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Palo Alto Networks - Aperture, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Palo Alto Networks - Aperture**.

    ![Collegamento di Palo Alto Networks - Aperture nell'elenco delle applicazioni](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Palo Alto Networks - Aperture nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Palo Alto Networks - Aperture.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Citrix ShareFile.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 8473c262f98e77708f01d17419e935979a533307
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix ShareFile

Questa esercitazione descrive come integrare Citrix ShareFile con Azure Active Directory (Azure AD).

L'integrazione di Citrix ShareFile con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Citrix ShareFile.
- È possibile abilitare gli utenti per l'accesso automatico a Citrix ShareFile (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Citrix ShareFile, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Citrix ShareFile abilitata per l'accesso Single Sign-On.

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiungere Citrix ShareFile dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-citrix-sharefile-from-the-gallery"></a>Aggiungere Citrix ShareFile dalla raccolta
Per configurare l'integrazione di Citrix ShareFile in Azure AD, è necessario aggiungere Citrix ShareFile dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Citrix ShareFile dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Citrix ShareFile**, selezionare **Citrix ShareFile** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Citrix ShareFile nell'elenco risultati](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Citrix ShareFile in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Citrix ShareFile che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Citrix ShareFile.

Per stabilire la relazione di collegamento, in Citrix ShareFile assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Citrix ShareFile, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Citrix ShareFile](#create-a-citrix-sharefile-test-user)**: per avere una controparte di Britta Simon in Citrix ShareFile collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Citrix ShareFile.

**Per configurare Single Sign-On di Azure AD con Citrix ShareFile, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Citrix ShareFile** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. Nella sezione **Citrix ShareFile Domain and URLs** (URL e dominio Citrix ShareFile) seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. Nel **Citrix ShareFile Configuration** (Configurazione di Citrix ShareFile) fare clic su **Configure Citrix ShareFile** (Configura Citrix ShareFile) per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di **Citrix ShareFile** come amministratore.

8. Nel barra degli strumenti in alto fare clic su **Admin**.

9. Nel riquadro di spostamento sinistro selezionare **Configure Single Sign-On**.
   
    ![Account amministratore](./media/active-directory-saas-sharefile-tutorial/ic773627.png "Account amministratore")

10. In **Basic Settings** della finestra di dialogo **Single Sign-On/ SAML 2.0 Configuration** seguire questa procedura:
   
    ![Single Sign-On](./media/active-directory-saas-sharefile-tutorial/ic773628.png "Single Sign-On")
   
    a. Fare clic su **Enable SAML**.
    
    b. Nella casella di testo **Your IDP Issuer/ Entity ID** (Autorità di certificazione IdP/ID entità) incollare il valore **SAML Entity ID** (ID entità SAML) copiato dal portale di Azure.

    c. Fare clic su **Change** (Cambia) accanto al campo **X.509 Certificate** (Certificato X.509) e quindi caricare il certificato scaricato dal portale di Azure.
    
    d. Nella casella di testo **URL di accesso** incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
    
    e. Nella casella di testo **Logout URL** (URL di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.

11. Fare clic su **Save** nel portale di gestione di Citrix ShareFile.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Creare un utente test di Citrix ShareFile

Per consentire agli utenti di Azure AD di accedere a Citrix ShareFile, è necessario eseguirne il provisioning in Citrix ShareFile. Nel caso di Citrix ShareFile, il provisioning è un'attività manuale.

**Per eseguire il provisioning di un account utente, seguire questa procedura:**

1. Accedere al tenant di **Citrix ShareFile** .

2. Fare clic su **Manage Users \> Manage Users Home \> + Create Employee** (Gestisci utenti > Gestisci home utenti > + Crea dipendente).
   
   ![Crea dipendente](./media/active-directory-saas-sharefile-tutorial/IC781050.png "Crea dipendente")

3. Nella sezione **Informazioni di base** seguire questa procedura:
   
   ![Informazioni di base](./media/active-directory-saas-sharefile-tutorial/IC799951.png "Informazioni di base")
   
   a. Nella casella di testo **Email Address** (Indirizzo di posta elettronica) digitare l'indirizzo di posta elettronica di Britta Simon come **brittasimon@contoso.com**.
   
   b. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente come **Britta**.
   
   c. Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente come **Simon**.

4. Fare clic su **Add User**.
  
   >[!NOTE]
   >Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica e selezionerà un collegamento per confermare il proprio account prima che venga attivato. È possibile usare qualsiasi altro strumento di creazione di account utente Citrix ShareFile o qualsiasi API fornita da Citrix ShareFile per effettuare il provisioning degli account utente di Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure, tramite la concessione dell'accesso a Citrix ShareFile.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Citrix ShareFile, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Citrix ShareFile**.

    ![Collegamento di Citrix ShareFile nell'elenco delle applicazioni](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Citrix ShareFile nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Citrix ShareFile.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png


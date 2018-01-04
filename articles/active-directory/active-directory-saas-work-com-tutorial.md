---
title: 'Esercitazione: Integrazione di Azure Active Directory con Work.com | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 7aa04faab5da9ee7dae977be3a8c040c3aed0b9c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Esercitazione: Integrazione di Azure Active Directory con Work.com

Questa esercitazione descrive come integrare Work.com con Azure Active Directory (Azure AD).

L'integrazione di Work.com con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Work.com
- È possibile abilitare gli utenti per l'accesso automatico a Work.com (Single Sign-On) con gli account Azure AD personali
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Work.com, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Work.com abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiungere Work.com dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-workcom-from-the-gallery"></a>Aggiungere Work.com dalla raccolta
Per configurare l'integrazione di Work.com in Azure AD, è necessario aggiungere Work.com dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere Work.com dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Work.com**, selezionare **Work.com** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Aggiungere dalla raccolta](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Work.com usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di Work.com corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Work.com.

Per stabilire la relazione di collegamento, in Work.com assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Work.com, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Work.com](#create-a-workcom-test-user)**: per avere una controparte di Britta Simon in Work.com collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Work.com.

>[!NOTE]
>Per configurare l'accesso Single Sign-On, è necessario configurare un nome di dominio personalizzato Work.com. È necessario definire almeno un nome di dominio, testare il nome di dominio e distribuirlo in tutta l'organizzazione.

**Per configurare l'accesso Single Sign-On di Azure AD con Work.com, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Work.com** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Accesso basato su SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_samlbase.png)

3. Nella sezione **URL e dominio Work.com** seguire questa procedura:

    ![Sezione URL e dominio Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_url.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Work.com](https://help.salesforce.com/articleView?id=000159855&type=3). 

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Sezione Certificato di firma SAML](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante per il salvataggio](./media/active-directory-saas-work-com-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Work.com** fare clic su **Configura Work.com** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Sezione Configurazione di Work.com](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_configure.png) 
7. Accedere al tenant di Work.com come amministratore.

8. Passare a **Setup**.
   
    ![Installazione](./media/active-directory-saas-work-com-tutorial/ic794108.png "Installazione")

9. Nella sezione **Administer** (Amministra) del riquadro di spostamento sinistro fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
   
    ![My Domain](./media/active-directory-saas-work-com-tutorial/ic767825.png "My Domain")

10. Per verificare che il dominio sia stato configurato correttamente, verificare che sia presente in "**Step 4 Deployed to Users**" (Passaggio 4 Distribuzione agli utenti) e quindi verificare le selezioni in "**My Domain Settings**" (Impostazioni dominio personale).
   
    ![Dominio distribuito all'utente](./media/active-directory-saas-work-com-tutorial/ic784377.png "Dominio distribuito all'utente")

11. Accedere al tenant di Work.com.

12. Passare a **Setup**.
    
    ![Installazione](./media/active-directory-saas-work-com-tutorial/ic794108.png "Installazione")

13. Espandere il menu **Security Controls** (Controlli di sicurezza) e quindi fare clic su **Single Sign-On Settings** (Impostazioni Single Sign-On).
    
    ![Single Sign-On Settings](./media/active-directory-saas-work-com-tutorial/ic794113.png "Single Sign-On Settings")

14. Nella finestra di dialogo **Single Sign-On Settings** seguire questa procedura:
    
    ![SAML Enabled](./media/active-directory-saas-work-com-tutorial/ic781026.png "SAML Enabled")
    
    a. Selezionare **Abilitato SAML**.
    
    b. Fare clic su **Nuovo**.

15. Nella sezione **SAML Single Sign-On Setting** seguire questa procedura:
    
    ![SAML Single Sign-On Setting](./media/active-directory-saas-work-com-tutorial/ic794114.png "SAML Single Sign-On Setting")
    
    a. Nella casella di testo **Name** digitare un nome per la configurazione.  
       
    > [!NOTE]
    > Se si specifica un valore per **Name** (Nome) verrà popolata automaticamente la casella di testo **API Name** (Nome API).
    
    b. Nella casella di testo **Issuer** (Autorità emittente) incollare il valore dell'**ID entità SAML** copiato dal portale di Azure.
    
    c. Per caricare il certificato scaricato dal portale di Azure, fare clic su **Browse** (Sfoglia).
    
    d. Nella casella di testo **Entity Id** (ID entità) digitare `https://salesforce-work.com`.
    
    e. In **SAML Identity Type** (Tipo di identità SAML) selezionare **Assertion contains the Federation ID from the User object** (L'asserzione contiene l'ID federazione dell'oggetto utente).
    
    f. In **SAML Identity Location** (Percorso identità SAML) selezionare **Identity is in the NameIdentfier element of the Subject statement** (L'identità è nell'elemento NameIdentfier dell'istruzione Subject).
    
    g. Nella casella di testo **Identity Provider Login URL** (URL di accesso provider di identità) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    h. Nella casella di testo **Identity Provider Logout URL** (URL di disconnessione provider di identità) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
    
    i. In **Service Provider Initiated Request Binding** (Binding richiesta avviato dal provider di servizi) selezionare **HTTP Post**.
    
    j. Fare clic su **Save**.

16. Nel pannello di navigazione a sinistra del portale di Work.com classico fare clic su **Domain Management** (Gestione dominio) per espandere la sezione correlata e quindi fare clic su **My Domain** (Dominio personale) per aprire la pagina **My Domain** (Dominio personale). 
    
    ![My Domain](./media/active-directory-saas-work-com-tutorial/ic794115.png "My Domain")

17. Nella sezione **Login Page Branding** (Personalizzazione pagina di accesso) della pagina **My Domain** (Dominio personale) fare clic su **Edit** (Modifica).
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/ic767826.png "Login Page Branding")

14. Nella sezione**Authentication Service** (Servizio autenticazione) della pagina **Login Page Branding** (Personalizzazione pagina di accesso) viene visualizzato il nome delle **impostazioni SAML SSO**. Selezionarlo e quindi fare clic su **Save**.
    
    ![Login Page Branding](./media/active-directory-saas-work-com-tutorial/ic784366.png "Login Page Branding")

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-work-com-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Utenti e gruppi -> Tutti gli utenti](./media/active-directory-saas-work-com-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Add](./media/active-directory-saas-work-com-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Finestra di dialogo Utente](./media/active-directory-saas-work-com-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-workcom-test-user"></a>Creare un utente di test di Work.com
Per consentire l'accesso agli utenti di Azure Active Directory, è necessario che eseguano il provisioning a Work.com. Nel caso di Work.com, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di Work.com come amministratore.

2. Passare a **Setup**.
   
    ![Installazione](./media/active-directory-saas-work-com-tutorial/IC794108.png "Installazione")
3. Passare a **Manage Users (Gestisci utenti) \> Users (Utenti)**.
   
    ![Gestione utenti](./media/active-directory-saas-work-com-tutorial/IC784369.png "Gestione utenti")

4. Fare clic su **Nuovo utente**.
   
    ![Tutti gli utenti](./media/active-directory-saas-work-com-tutorial/IC794117.png "Tutti gli utenti")

5. Nella sezione relativa alle modifiche utente eseguire i passaggi seguenti inserendo nelle caselle di testo correlate gli attributi di un account Azure AD valido di cui si vuole effettuare il provisioning:
   
    ![User Edit](./media/active-directory-saas-work-com-tutorial/ic794118.png "User Edit")
   
    a. Nella casella di testo **First Name** (Nome) digitare il **nome** dell'utente **Britta**.
    
    b. Nella casella di testo **Last Name** (Cognome) digitare il **cognome** dell'utente **Simon**.
    
    c. Nella casella di testo **Alias** digitare il **nome** dell'utente **BrittaS**.
    
    d. Nella casella di testo **Email** (Posta elettronica) digitare l'**indirizzo e-mail** dell'utente **Brittasimon@contoso.com**.
    
    e. Nella casella di testo **User Name** (Nome utente) digitare un nome utente, ad esempio **Brittasimon@contoso.com**.
    
    f. Nella casella di testo **Nick Name** (Nome alternativo) digitare un **nome alternativo** dell'utente **Simon**.
    
    g. Selezionare **Role** (Ruolo), **User License**(Licenza utente) e **Profile** (Profilo).
    
    h. Fare clic su **Save**.  
      
    > [!NOTE]
    > Il titolare dell'account Azure AD riceverà un messaggio di posta elettronica con un collegamento per confermare l'account e attivarlo.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Work.com.

![Assegna utente][200] 

**Per assegnare Britta Simon a Work.com, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Work.com**.

    ![Work.com nell'elenco delle app](./media/active-directory-saas-work-com-tutorial/tutorial_work-com_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Work.com nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Work.com.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-work-com-tutorial/tutorial_general_203.png


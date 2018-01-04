---
title: 'Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure | Documentazione Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e TOPdesk - Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: ca3362bc3f966adaf9940f6eb4bec5235c6ea7d8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Esercitazione: Integrazione di Azure Active Directory con TOPdesk - Secure

In questa esercitazione apprendere come integrare TOPdesk - Secure con Azure Active Directory (Azure AD).

L'integrazione di TOPdesk - Secure con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a TOPdesk - Secure.
- È possibile consentire agli utenti di automaticamente ottenere firmato-on per TOPdesk - Secure (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con TOPdesk - Secure, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Sottoscrizione di TOPdesk - Secure abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di TOPdesk - Secure dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-topdesk---secure-from-the-gallery"></a>Aggiunta di TOPdesk - Secure dalla raccolta
Per configurare l'integrazione di TOPdesk - Secure in Azure AD, è necessario aggiungere TOPdesk - Secure dalla raccolta all'elenco di SaaS gestito app.

**Per aggiungere TOPdesk - Secure dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **TOPdesk - Secure**selezionare **TOPdesk - Secure** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![TOPdesk - Secure nell'elenco dei risultati](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione configurare e testare Azure AD single sign-on con TOPdesk - sicuro basato su un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in TOPdesk - Secure a un utente in Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente AD Azure e l'utente correlato in TOPdesk - Secure.

In TOPdesk - Secure, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure AD single sign-on con TOPdesk - Secure, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un TOPdesk - utente test sicuro](#create-a-topdesk---secure-test-user)**  : per hanno un equivalente di Britta Simon in TOPdesk - Secure collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on in TOPdesk - applicazione protetta.

**Per configurare Azure AD single sign-on con TOPdesk - Secure, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **TOPdesk - Secure** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Nel **TOPdesk - Secure dominio e gli URL** sezione, eseguire la procedura seguente:

    ![TOPdesk - URL e dominio protetto di single sign-on, informazioni](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.topdesk.net`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. URL di risposta è illustrato più avanti nell'esercitazione. Contatto [TOPdesk - team di supporto Client Secure](http://www.topdesk.com/us/support) per ottenere questi valori. 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. Nel **TOPdesk - Secure configurazione** fare clic su **configurare TOPdesk - Secure** per aprire **Configura sign-on** finestra. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![TOPdesk - configurazione protetta](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Accedere al sito aziendale di **TOPdesk - Secure** come un amministratore.

8. Nel menu **TOPdesk** fare clic su **Settings** (Impostazioni).

    ![Impostazioni](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "Impostazioni")

9. Fare clic su **Login Settings**.

    ![Login Settings](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "Login Settings")

10. Espandere il menu **Login Settings** (Impostazioni accesso) e quindi fare clic su **General** (Generale).

    ![General](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "General")

11. Nell'area **Secure** (Sicura) della sezione di configurazione **SAML login** (Accesso SAML) seguire questa procedura:

    ![Technical Settings](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "Technical Settings")
   
    a. Fare clic su **Download** per scaricare il file di metadati pubblici e quindi salvarlo in locale nel computer.
   
    b. Aprire il file dei metadati e quindi individuare il nodo **AssertionConsumerService** .
    
    ![Assertion Consumer Service](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "Assertion Consumer Service")
   
    c. Copia il **AssertionConsumerService** valore, incollare il valore nella casella di testo URL di risposta in **TOPdesk - Secure dominio e gli URL** sezione.

12. Per creare un file del certificato, seguire questa procedura:
    
    ![Certificate](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "Certificate")
    
    a. Aprire il file di metadati scaricato dal portale di Azure.

    b. Espandere il nodo **RoleDescriptor** con **xsi:type** corrispondente a **fed:ApplicationServiceType**.

    c. Copiare il valore del nodo **X509Certificate** .

    d. Salvare il valore copiato di **X509Certificate** in un file locale nel computer.

13. Nell'area **Public** (Pubblica) fare clic su **Add** (Aggiungi).
    
    ![Add](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "Add")

14. Nella pagina **SAML configuration assistant** seguire questa procedura:
    
    ![SAML Configuration Assistant](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Per caricare il file di metadati scaricato dal portale di Azure, in **Metadati della federazione** fare clic su **Sfoglia**.

    b. Per caricare il file del certificato, in **Certificate (RSA)** (Certificato RSA) fare clic su **Browse** (Sfoglia).

    c. Per caricare il file del logo ottenuto dal team di supporto del team di TOPdesk, in **Logo icon** (Icona logo) fare clic su **Browse** (Sfoglia).

    d. Nella casella di testo **User name attribute** (Attributo nome utente) digitare `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Nella casella di testo **Display name** digitare un nome per la configurazione.

    f. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Creare un TOPdesk - utente di prova protetto

Per consentire agli utenti di Azure AD di accedere a TOPdesk - Secure, è necessario eseguirne il provisioning in TOPdesk - Secure.  
Nel caso di TOPdesk - Secure, il provisioning è un'attività manuale.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning utente, eseguire la procedura seguente:
1. Accedere al sito aziendale di **TOPdesk - Secure** come amministratore.
2. Nel menu in alto fare clic su **TOPdesk \> New (Nuovo) \> Support Files (File di supporto) \> Operator (Operatore)**.
   
    ![Operator](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "Operator")

3. Nella finestra di dialogo **New Operator** seguire questa procedura:
   
    ![New Operator](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "New Operator")
   
    a. Fare clic su di **generale** scheda.
   
    b. Nel **Surname** casella di testo, digitare il cognome dell'utente like **Simon**.
   
    c. Nella sezione **Location** (Località) selezionare un sito dalla casella **Site** (Sito).
   
    d. Nella casella di testo **Login Name** (Nome di accesso) della sezione **TOPdesk Login** (Accesso TOPdesk) digitare un nome di accesso per l'utente.
   
    e. Fare clic su **Save**.

> [!NOTE]
> È possibile usare qualsiasi altro strumento o API di creazione di account utente fornita da TOPdesk - Secure per eseguire il provisioning degli account utente Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso a TOPdesk - Secure.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a TOPdesk - Secure, eseguire la procedura seguente:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **TOPdesk - Secure**.

    ![Il TOPdesk - collegamento sicuro nell'elenco delle applicazioni](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si sceglie di TOPdesk - riquadro protetta nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on per l'applicazione TOPdesk - Secure.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png


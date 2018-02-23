---
title: 'Esercitazione: Integrazione di Azure Active Directory con Dome9 Arc | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 2ce4bb1be8b0124c69991765e18ce9922bd2f4a4
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Esercitazione: Integrazione di Azure Active Directory con Dome9 Arc

Questa esercitazione descrive come integrare Dome9 Arc con Azure Active Directory (Azure AD).

L'integrazione di Dome9 Arc con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Dome9 Arc.
- È possibile abilitare gli utenti per l'accesso automatico a Dome9 Arc (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Dome9 Arc, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Dome9 Arc abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Dome9 Arc dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-dome9-arc-from-the-gallery"></a>Aggiunta di Dome9 Arc dalla raccolta
Per configurare l'integrazione di Dome9 Arc in Azure AD, è necessario aggiungere Dome9 Arc dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Dome9 Arc dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Dome9 Arc**, selezionare **Dome9 Arc** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Dome9 Arc nell'elenco risultati](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Dome9 Arc usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di Dome9 Arc che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Dome9 Arc.

Per stabilire la relazione di collegamento, in Dome9 Arc assegnare il valore di **nome utente** di Azure AD come valore dell'attributo **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Dome9 Arc, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Dome9 Arc](#create-a-dome9-arc-test-user)**: per avere una controparte di Britta Simon in Dome9 Arc collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Dome9 Arc.

**Per configurare l'accesso Single Sign-On di Azure AD con Dome9 Arc, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Dome9 Arc** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

3. Nella sezione **URL e dominio Dome9 Arc** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL: `https://secure.dome9.com/`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Selezionare il valore del nome della società nel portale di amministrazione di Dome9 Arc illustrato più avanti nell'esercitazione.

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Dome9 Arc](https://dome9.com/about/contact-us/). 

5. L'applicazione Dome9 Arc si aspetta che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.

    ![Configurare l'attributo Single Sign-On](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo  | Valore attributo | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'attributo di aggiunta dell'accesso Single Sign-On](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9_04.png)

    ![Configurare l'attributo di modifica dell'accesso Single Sign-On](./media/active-directory-saas-dome9arc-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-dome9arc-tutorial/tutorial_general_400.png)
    
9. Nella sezione **Configurazione di Dome9 Arc** fare clic su **Configura Dome9 Arc** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_configure.png) 

10. In un'altra finestra del Web browser accedere al sito aziendale di Dome9 Arc come amministratore.

11. Fare clic su **Impostazioni del profilo** nell'angolo superiore destro e quindi fare clic su **Impostazioni account**. 

    ![Configurazione di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure1.png)

12. Passare a **SSO** e quindi fare clic su **Abilita**.

    ![Configurazione di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure2.png)

13. Nella sezione di configurazione di SSO eseguire la procedura seguente:

    ![Configurazione di Dome9 Arc](./media/active-directory-saas-dome9arc-tutorial/configure3.png)

    a. Immettere il nome della società nella casella di testo **ID account**. Questo valore viene usato nell'URL di risposta riportato nella sezione URL del portale Azure.

    b. Nella casella di testo **Autorità emittente** incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.

    c. Nella casella di testo **IdP Endpoint URL** (URL endpoint IdP) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.

    d. Aprire il certificato con codifica Base64 scaricato nel Blocco note, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **X.509 certificate** (Certificato X.509).

    e. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-dome9arc-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-dome9-arc-test-user"></a>Creare un utente di test di Dome9 Arc

Per consentire agli utenti di Azure AD di accedere a Dome9 Arc, è necessario effettuarne il provisioning nell'applicazione. Dome9 Arc supporta il provisioning JIT, ma per garantire il corretto funzionamento di tale provisioning è necessario selezionare un **ruolo** specifico e assegnarlo all'utente.

   >[!Note] 
   >Per la creazione del **ruolo** e per altre informazioni, contattare il [team di supporto clienti di Dome9 Arc](https://dome9.com/about/contact-us/).

**Per eseguire il provisioning di un account utente manualmente, seguire questa procedura:**

1. Accedere al sito aziendale di Dome9 Arc come amministratore.

2. Fare clic su **Utenti e ruoli** e quindi su **Utenti**.

    ![Aggiungere un dipendente](./media/active-directory-saas-dome9arc-tutorial/user1.png)

3. Fare clic su **Aggiungi utente**.

    ![Aggiungere un dipendente](./media/active-directory-saas-dome9arc-tutorial/user2.png)

4. Nella sezione **Crea utente** , eseguire la procedura seguente:
    
    ![Aggiungere un dipendente](./media/active-directory-saas-dome9arc-tutorial/user3.png)

    a. Nella casella di testo **Email** (Posta elettronica) digitare l'indirizzo di posta elettronica di un utente, ad esempio Brittasimon@contoso.com.

    b. Digitare il nome dell'utente, ad esempio Britta, nella casella di testo **First Name** (Nome).

    c. Digitare il cognome dell'utente, ad esempio Simon, nella casella di testo **Last Name** (Cognome).

    d. Impostare l'opzione **SSO User** (Utente SSO) su **On** (Abilitato).

    e. Fare clic su **Create** (Crea).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso ad Dome9 Arc.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Dome9 Arc, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Dome9 Arc**.

    ![Collegamento di Dome9 Arc nell'elenco delle applicazioni](./media/active-directory-saas-dome9arc-tutorial/tutorial_dome9arc_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Dome9 Arc nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Dome9 Arc.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-dome9arc-tutorial/tutorial_general_203.png


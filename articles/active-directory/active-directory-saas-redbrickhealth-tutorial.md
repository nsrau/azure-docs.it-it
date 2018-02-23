---
title: 'Esercitazione: Integrazione di Azure Active Directory con RedBrick Health | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e RedBrick Health.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jeedes
ms.openlocfilehash: 598592d87cf6471a431dab89d19c5e8beb48e661
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Esercitazione: Integrazione di Azure Active Directory con RedBrick Health

Questa esercitazione descrive come integrare RedBrick Health con Azure Active Directory (Azure AD).

L'integrazione di RedBrick Health con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a RedBrick Health.
- È possibile abilitare gli utenti per l'accesso automatico a RedBrick Health (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con RedBrick Health, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di RedBrick Health abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di RedBrick Health dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-redbrick-health-from-the-gallery"></a>Aggiunta di RedBrick Health dalla raccolta
Per configurare l'integrazione di RedBrick Health in Azure AD, è necessario aggiungere RedBrick Health dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere RedBrick Health dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **RedBrick Health** selezionare **RedBrick Health** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![RedBrick Health nell'elenco dei risultati](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con RedBrick Health in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere qual è l'utente di RedBrick Health che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in RedBrick Health.

Per stabilire la relazione di collegamento, in RedBrick Health assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con RedBrick Health, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di RedBrick Health](#create-a-redbrick-health-test-user)**: per avere una controparte di Britta Simon in RedBrick Health collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione RedBrick Health.

**Per configurare l'accesso Single Sign-On di Azure AD con RedBrick Health, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **RedBrick Health** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_samlbase.png)

3. Nella sezione **URL e dominio RedBrick Health** seguire questa procedura:

    ![Informazioni sul Single Sign-On di URL e dominio di RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `http://www.redbrickhealth.com`
    
    b. Nella casella di testo **URL di risposta** digitare un URL: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`
    
    Per l'ambiente di produzione: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Fare clic su **Mostra impostazioni URL avanzate**.
    
    ![Informazioni sul Single Sign-On di URL e dominio di RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_url1.png)

    d. Nella casella di testo **Stato dell'inoltro** digitare un URL usando il modello seguente: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`
    
    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con lo stato dell'inoltro effettivo. Per ottenere tale valore, contattare il [team di supporto di RedBrick Health](https://home.redbrickhealth.com/contact/).

4. L'applicazione RedBrick Health prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Queste attestazioni sono specifiche del cliente e dipendono dai requisiti. Di seguito sono descritte alcune attestazioni facoltative che è possibile configurare per l'applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione.

    ![Configure Single Sign-On](./media/active-directory-saas-redbrickhealth-tutorial/attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:

    | Nome attributo | Valore attributo |
    | ---------------| ----------------|
    | principal name | ********** |
    | ID CLIENT | ********** |
    | participant id | ********** |
    
    > [!NOTE]
    > Questi valori sono solo a scopo di riferimento. È necessario definire gli attributi in base ai requisiti dell'organizzazione. Contattare il [team di supporto di RedBrick Health](https://home.redbrickhealth.com/contact/) per altre informazioni sulle attestazioni necessarie.
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_04.png)
    
    ![Configure Single Sign-On](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_400.png)

8. Nella sezione **Configurazione di RedBrick Health** fare clic su **Configura RedBrick Health** per aprire la finestra **Configura accesso**. Copiare l'**ID di entità SAML** dalla **sezione di riferimento rapido.**

    ![Configurazione di RedBrick Health](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_configure.png) 

9. Per configurare l'accesso Single Sign-On sul lato **RedBrick Health**, è necessario inviare il file **Certificato (Base64)** scaricato e l'**ID di entità SAML** al [team di supporto di RedBrick Health](https://home.redbrickhealth.com/contact/). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-redbrickhealth-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-redbrick-health-test-user"></a>Creare un utente di test di RedBrick Health

In questa sezione viene creato un utente chiamato Britta Simon in RedBrick Health. Rivolgersi al [team di supporto di RedBrick Health](https://home.redbrickhealth.com/contact/) per aggiungere gli utenti nella piattaforma RedBrick Health. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a RedBrick Health.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a RedBrick Health, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **RedBrick Health**.

    ![Collegamento di RedBrick Health nell'elenco delle applicazioni](./media/active-directory-saas-redbrickhealth-tutorial/tutorial_redbrickhealth_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro RedBrick Health nel pannello di accesso, verrà eseguito automaticamente l'accesso all'applicazione RedBrick Health.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-redbrickhealth-tutorial/tutorial_general_203.png


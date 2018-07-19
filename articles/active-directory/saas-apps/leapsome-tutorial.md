---
title: 'Esercitazione: Integrazione di Azure Active Directory con Leapsome | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: 4b2c23745a5e624bcf668dfbfe5d085392d7a583
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/14/2018
ms.locfileid: "39052452"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Esercitazione: Integrazione di Azure Active Directory con Leapsome

Questa esercitazione descrive come integrare Leapsome con Azure Active Directory (Azure AD).

L'integrazione di Leapsome con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Leapsome.
- È possibile abilitare gli utenti per l'accesso automatico a Leapsome (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Leapsome, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione Leapsome abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Leapsome dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-leapsome-from-the-gallery"></a>Aggiunta di Leapsome dalla raccolta
Per configurare l'integrazione di Leapsome in Azure AD, è necessario aggiungere l'app dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Leapsome dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Leapsome**, selezionare **Leapsome** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Leapsome nell'elenco risultati](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Leapsome in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Leapsome che corrisponde a un utente di Azure AD. Deve essere stabilita, in altre parole, una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Leapsome.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Leapsome, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Leapsome](#create-a-leapsome-test-user)**: per avere una controparte di Britta Simon in Leapsome collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Leapsome.

**Per configurare l'accesso Single Sign-On di Azure AD con Leapsome, seguire questa procedura:**

1. Nel portale di Azure, nella pagina di integrazione dell'applicazione **Leapsome** fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

3. Nella sezione **URL e dominio Leapsome** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Leapsome](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://www.leapsome.com`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di Leapsome](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > I precedenti valori di URL di risposta e URL di accesso non sono reali. È necessario sostituirli con i valori effettivi, come descritto più avanti nell'esercitazione.

5. L'applicazione Leapsome prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente mostra un esempio.
    
    ![Configure Single Sign-On](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente e seguire questa procedura:
    
    | Nome attributo | Valore attributo | Spazio dei nomi |
    | ---------------| --------------- | --------- |   
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user. jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | URL dell'immagine del dipendente | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > Il valore dell'attributo picture non è reale. Aggiornare questo valore con l'URL dell'immagine effettivo. Per ottenere questo valore, contattare il [team di supporto clienti di Leapsome](mailto:support@leapsome.com).
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configure Single Sign-On](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Nella casella di testo **Spazio dei nomi** digitare l'URI dello spazio dei nomi per la riga.
    
    e. Fare clic su **Ok**

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/leapsome-tutorial/tutorial_general_400.png)
    
9. Nella sezione **Configurazione di Leapsome** fare clic su **Configura Leapsome** per aprire la finestra **Configura accesso**. Copiare l'**URL servizio Single Sign-On SAML** dalla **sezione Riferimento rapido.**

    ![Configurazione di Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

10. In un'altra finestra del Web browser accedere a Leapsome come amministratore.

11. In alto a destra, fare clic sul logo Impostazioni e quindi fare clic su **Impostazioni di amministrazione**. 

    ![Set Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

12. Nella barra dei menu a sinistra fare clic su **Single Sign On (SSO)** e nella pagina **Accesso Single Sign-On basato su SAML** seguire questa procedura:
    
    ![SAML Leapsome](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selezionare **Enable SAML-based single sign-on** (Abilita accesso Single Sign-On basato su SAML).

    b. Copiare il valore di **URL di accesso (indirizzare gli utenti qui per accedere)** e incollarlo nella casella di testo **URL accesso** della sezione **Dominio e URL di Leapsome** del portale di Azure.

    c. Copiare il valore di **URL di risposta (riceve la risposta dal provider di identità)** e incollarlo nella casella di testo **URL di risposta** della sezione **Dominio e URL di Leapsome** del portale di Azure.

    d. Nella casella di testo **URL di accesso (fornito dal provider di identità)** incollare il valore di **URL servizio Single Sign-On SAML** copiato dal portale di Azure.

    e. Copiare il certificato che è stato scaricato dal portale di Azure senza i commenti --BEGIN CERTIFICATE e END CERTIFICATE-- e incollarlo nella casella di testo **Certificato (fornito dal provider di identità)**.

    f. Fare clic su **UPDATE SSO SETTINGS** (AGGIORNA IMPOSTAZIONI SSO).
    
### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/leapsome-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/leapsome-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/leapsome-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-leapsome-test-user"></a>Creare un utente test di Leapsome

In questa sezione viene creato un utente di nome Britta Simon in Leapsome. Rivolgersi al [team di supporto di Leapsome](mailto:support@leapsome.com) per aggiungere gli utenti o il dominio da includere nella piattaforma Leapsome. Se il dominio viene aggiunto dal team, verrà automaticamente eseguito il provisioning degli utenti nella piattaforma Leapsome. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On. 

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Leapsome.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Leapsome, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Leapsome**.

    ![Collegamento di Leapsome nell'elenco delle applicazioni](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Leapsome nel pannello di accesso, si accede automaticamente tramite SSO all'applicazione Leapsome.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png


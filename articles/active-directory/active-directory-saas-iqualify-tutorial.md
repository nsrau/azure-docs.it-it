---
title: 'Esercitazione: Integrazione di Azure Active Directory con iQualify LMS | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 3eb68cba15e89c455d7d33be1ec0bf596df5f3b7
ms.openlocfilehash: cab1ce3694372c137667e0179caf8d3523147f7a
ms.contentlocale: it-it
ms.lasthandoff: 09/01/2017

---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Esercitazione: Integrazione di Azure Active Directory con iQualify LMS

Questa esercitazione descrive come integrare iQualify LMS con Azure Active Directory (Azure AD).

L'integrazione di iQualify LMS con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a iQualify LMS.
- È possibile abilitare gli utenti per l'accesso automatico a iQualify LMS (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con iQualify LMS, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di iQualify LMS abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di iQualify LMS dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-iqualify-lms-from-the-gallery"></a>Aggiunta di iQualify LMS dalla raccolta
Per configurare l'integrazione di iQualify LMS in Azure AD, è necessario aggiungere iQualify LMS dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere iQualify LMS dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **iQualify LMS**, nel pannello dei risultati selezionare **iQualify LMS** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![iQualify LMS nell'elenco dei risultati](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con iQualify LMS usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere l'utente controparte di iQualify LMS corrispondente a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in iQualify LMS.

Per stabilire la relazione di collegamento, in iQualify LMS assegnare il valore del **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con iQualify LMS, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di iQualify LMS](#create-an-iqualify-lms-test-user)**: per avere una controparte di Britta Simon in iQualify LMS collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione iQualify LMS.

**Per configurare l'accesso Single Sign-On di Azure AD con iQualify LMS, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **iQualify LMS** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Nella sezione **URL e dominio iQualify LMS** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da IDP:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL usando il criterio seguente: 
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/`|
    | Ambiente di test: `https://<yourorg>.iqualify.io`|
    
    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: 
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Ambiente di test: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    Nella casella di testo **URL di accesso** digitare un URL usando il criterio seguente:
    | |
    |--|--|
    | Ambiente di produzione: `https://<yourorg>.iqualify.com/login` |
    | Ambiente di test: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'identificatore, l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di iQualify LMS](https://www.iqualify.com). 

5. L'applicazione iQualify LMS prevede che le asserzioni SAML (Security Assertion Markup Language) vengano visualizzate in un formato specifico. Configurare le attestazioni e gestire i valori degli attributi nella sezione **Attributi utente** della pagina di integrazione dell'applicazione iQualify, come illustrato nello screenshot seguente:
    
    ![Configura accesso Single Sign-On](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On**, per ogni riga illustrata nella tabella seguente, seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | --- | --- |    
    | email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "your attribute" | 

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.
    
    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **Ok**

    e. Ripetere i passaggi da "a" a "d" per le righe successive della tabella. 

    > [!Note]
    > La ripetizione dei passaggi da "a" a "d" per l'attributo **person_id** è **facoltativa**.

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Fare clic sul pulsante **Salva**.

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. Nella sezione **Configurazione di iQualify LMS** fare clic su **Configura iQualify LMS** per aprire la finestra **Configura accesso**. Copiare i valori **Sign-Out URL (URL di disconnessione) e SAML Single Sign-On Service URL (URL servizio Single Sign-On SAML)** dalla sezione **Quick Reference** (Riferimento rapido).

    ![Configurazione di iQualify LMS](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Aprire una nuova finestra del browser e quindi accedere all'ambiente di iQualify come amministratore.

11. Una volta connessi, fare clic sul proprio avatar in alto a destra e quindi fare clic su **Account settings** (Impostazioni account).

    ![Impostazioni dell'account](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. Nell'area delle impostazioni dell'account fare clic sul menu a sinistra sulla barra multifunzione e fare clic su **INTEGRATIONS** (INTEGRAZIONI).
    
    ![INTEGRAZIONI](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. In INTEGRATIONS (INTEGRAZIONI) fare clic sull'icona **SAML**.

    ![Icona SAML](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. Nella finestra di dialogo **SAML Authentication Settings** (Impostazioni di autenticazione SAML) eseguire questa procedura:

    ![Impostazioni di autenticazione SAML](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. Nella casella **SAML SINGLE SIGN-ON SERVICE URL** (URL SERVIZIO SINGLE SIGN-ON SAML) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dalla finestra di configurazione dell'applicazione di Azure AD.
    
    b. Nella casella **SAML LOGOUT URL** (URL DI DISCONNESSIONE SAML) incollare il valore dell'**URL di disconnessione** copiato dalla finestra di configurazione dell'applicazione di Azure AD.
    
    c. Aprire il file del certificato scaricato nel Blocco note, copiarne il contenuto e quindi incollarlo nella casella **PUBLIC CERTIFICATE** (CERTIFICATO PUBBLICO).
    
    d. In **LOGIN BUTTON LABEL** (ETICHETTA DEL PULSANTE DI ACCESSO) immettere il nome del pulsante da visualizzare nella pagina di accesso.
    
    e. Fare clic su **SAVE** (SALVA).

    f. Fare clic su **UPDATE** (AGGIORNA).

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** eseguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Creare un utente di test di iQualify LMS

In questa sezione viene creato un utente di nome Britta Simon in iQualify. iQualify LMS supporta il provisioning JIT (Just-In-Time), abilitato per impostazione predefinita.

Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste in iQualify, ne viene creato uno nuovo quando si prova ad accedere a iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a iQualify LMS.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a iQualify LMS, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **iQualify LMS**.

    ![Collegamento di iQualify LMS nell'elenco delle applicazioni](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro iQualify LMS nel pannello di accesso, dovrebbe venire visualizzata la pagina di accesso dell'applicazione iQualify LMS. 

   ![pagina di accesso](./media/active-directory-saas-iqualify-tutorial/login.png) 

Fare clic sul pulsante **Sign in with Azure AD** (Accedi con Azure AD) e si dovrebbe accedere automaticamente all'applicazione iQualify LMS.

Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png



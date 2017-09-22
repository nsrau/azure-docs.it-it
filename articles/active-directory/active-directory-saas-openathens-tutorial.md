---
title: 'Esercitazione: Integrazione di Azure Active Directory con OpenAthens | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: bbf74768542983be2e69ffb948baf35822610839
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Esercitazione: Integrazione di Azure Active Directory con OpenAthens

Questa esercitazione descrive come integrare OpenAthens con Azure Active Directory (Azure AD).

L'integrazione di OpenAthens con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a OpenAthens.
- È possibile abilitare gli utenti per l'accesso automatico a OpenAthens (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con OpenAthens, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione abilitata per l'accesso Single Sign-On a OpenAthens

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di OpenAthens dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-openathens-from-the-gallery"></a>Aggiunta di OpenAthens dalla raccolta
Per configurare l'integrazione di OpenAthens in Azure AD, è necessario aggiungere OpenAthens dalla raccolta all'elenco di app SaaS gestite.

**Per aggiungere OpenAthens dalla raccolta, seguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **OpenAthens**, selezionare **OpenAthens** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![OpenAthens nell'elenco risultati](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OpenAthens in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente controparte di OpenAthens che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OpenAthens.

Per stabilire la relazione di collegamento, in OpenAthens assegnare il valore di **nome utente** di Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con OpenAthens, è necessario completare le procedure di base seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test OpenAthens](#create-a-openathens-test-user)**: per avere una controparte di Britta Simon in OpenAthens collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OpenAthens.

**Per configurare l'accesso Single Sign-On di Azure AD con OpenAthens, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OpenAthens** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. Nella sezione **URL e dominio OpenAthens** immettere il valore: `https://login.openathens.net/saml/2/metadata-sp` nella casella di testo **Identificatore**.

    ![Informazioni sull'accesso Single Sign-On per URL e dominio di OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di OpenAthens come amministratore.

7. Selezionare **Connections** (Connessioni) nella scheda **Management** (Gestione). 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Selezionare **SAML 1.1/2.0** e quindi fare clic sul pulsante **Configure** (Configura).

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9.  Fare clic sul pulsante **Browse** (Sfoglia) per caricare il file di metadati xml scaricato dal portale di Azure. Fare clic su **Add** (Aggiungi) per aggiungere la configurazione.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Nella scheda **Details** (Dettagli) seguire questa procedura.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. In **Display name mapping** (Mapping nome visualizzato) selezionare **Use Attribute** (Usa attributo).

    b. Nella casella di testo **Display name attribute** (Attributo nome visualizzato) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
    
    c. In **Unique user mapping** (Mapping utente univoco) selezionare **Use Attribute** (Usa attributo).

    d. Nella casella di testo **Unique user attribute** (Attributo utente univoco) immettere il valore `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    e. In **Status** (Stato) selezionare tutte le tre caselle di controllo.

    f. In **Create local accounts** (Crea account locali) selezionare **automatically** (automaticamente).

    g. Fare clic su **Salva modifiche**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-a-openathens-test-user"></a>Creare un utente test OpenAthens

OpenAthens supporta il provisioning JIT e gli utenti vengono creati automaticamente dopo l'autenticazione. In questa sezione non è necessario eseguire alcuna azione.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OpenAthens.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a OpenAthens, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **OpenAthens**.

    ![Collegamento OpenAthens nell'elenco delle applicazioni](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OpenAthens nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione OpenAthens.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png



---
title: 'Esercitazione: Integrazione di Azure Active Directory con Questetra BPM Suite | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: e2515d571aa1615f14b597ef82b1087df3827f82
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Esercitazione: Integrazione di Azure Active Directory con Questetra BPM Suite

Questa esercitazione descrive come integrare Questetra BPM Suite con Azure Active Directory (Azure AD).

L'integrazione di Questetra BPM Suite con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Questetra BPM Suite.
- È possibile abilitare gli utenti per l'accesso automatico a Questetra BPM Suite (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Questetra BPM Suite, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per l'accesso Single Sign-On a Questetra BPM Suite

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Questetra BPM Suite dalla raccolta
2. Configurare e testare l'accesso Single Sign-On di Azure AD

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Aggiunta di Questetra BPM Suite dalla raccolta
Per configurare l'integrazione di Questetra BPM Suite in Azure AD, è necessario aggiungere Questetra BPM Suite dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Questetra BPM Suite dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![APPLICAZIONI][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![APPLICAZIONI][3]

4. Nella casella di ricerca digitare **Questetra BPM Suite**, selezionare **Questetra BPM Suite** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Aggiungere dalla raccolta](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD
In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Questetra BPM Suite in base a un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Questetra BPM Suite che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Questetra BPM Suite.

Per stabilire la relazione di collegamento, in Questetra BPM Suite assegnare il valore di **nome utente** in Azure AD come valore di **Username** (Nome utente).

Per configurare e testare l'accesso Single Sign-On di Azure AD con Questetra BPM Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test di Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**: per avere una controparte di Britta Simon in Questetra BPM Suite collegata alla relativa rappresentazione in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Questetra BPM Suite.

**Per configurare Single Sign-On di Azure AD con Questetra BPM Suite, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Questetra BPM Suite** del portale di Azure fare clic su **Single Sign-On**.

    ![Configure Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Accesso basato su SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. Nella sezione **URL e dominio di Questetra BPM Suite** seguire questa procedura:

    ![Sezione URL e dominio di Questetra BPM Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. È possibile ottenere questi valori nella sezione **SP Information** (Informazioni SP) del sito aziendale di **Questetra BPM Suite**, come illustrato più avanti nell'esercitazione, oppure tramite il [team di supporto di clienti di Questetra BPM Suite](https://www.questetra.com/contact/). 
 
4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (Base64)** e quindi salvare il file del certificato nel computer.

    ![Sezione Certificato di firma SAML](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante per il salvataggio](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. Nella sezione **Configurazione di Questetra BPM Suite** fare clic su **Configura Questetra BPM Suite** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Sezione Configurazione di Questetra Business Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. In un'altra finestra del Web browser accedere al sito aziendale di **Questetra BPM Suite** come amministratore.

8. Nel menu in alto fare clic su **System Settings**(Impostazioni di sistema). 
   
    ![Single Sign-On di Microsoft Azure AD][10]

9. Per aprire la pagina **SingleSignOnSAML** fare clic su **SSO (SAML)**. 
   
    ![Single Sign-On di Microsoft Azure AD][11]

10. Nella sezione **SP Information** (Informazioni SP) del sito aziendale di **Questetra BPM Suite** seguire questa procedura:

    a. Copiare il valore di **ACS URL**e quindi incollarlo nella casella di testo **URL di accesso** nella sezione **URL e dominio di Questetra BPM Suite** nel portale di Azure.
    
    b. Copiare il valore di **Entity ID** (ID entità) e quindi incollarlo nella casella di testo **Identificatore** della sezione **URL e dominio di Questetra BPM Suite** nel portale di Azure.

11. Nel sito aziendale di **Questetra BPM Suite** seguire questa procedura: 
   
    ![Configure Single Sign-On][15]
   
    a. Selezionare **Enable Single Sign-On**.
   
    b. Nella casella di testo **Entity Id** (ID entità) incollare il valore dell'**ID di entità SAML** copiato dal portale di Azure.
    
    c. Nella casella di testo **Sign-in page URL** (URL pagina di accesso) incollare il valore dell'**URL del servizio Single Sign-On SAML** copiato dal portale di Azure.
    
    d. Nella casella di testo **Sign-out page URL** (URL pagina di disconnessione) incollare il valore dell'**URL di disconnessione** copiato dal portale di Azure.
    
    e. Nella casella di testo **NameID format** (Formato ID nome) digitare `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Aprire nel Blocco note il certificato con codifica **Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e quindi incollarlo nella casella di testo **Validation certificate** (Certificato di convalida). 

    g. Fare clic su **Save**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD
Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

![Creare un utente di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel **portale di Azure** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Passare a **Utenti e gruppi** e fare clic su **Tutti gli utenti** per visualizzare l'elenco di utenti.
    
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Nella parte superiore della finestra di dialogo fare clic su **Aggiungi** per aprire la finestra di dialogo **Utente**.
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. Nella pagina della finestra di dialogo **Utente** seguire questa procedura:
 
    ![Creazione di un utente test di Azure AD](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Nella casella di testo **Nome** digitare **BrittaSimon**.

    b. Nella casella di testo **Nome utente** digitare l'**indirizzo di posta elettronica** di BrittaSimon.

    c. Selezionare **Mostra password** e prendere nota del valore della **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Creare un utente test di Questetra BPM Suite

Questa sezione descrive come creare un utente chiamato Britta Simon in Questetra BPM Suite.

**Per creare un utente test denominato Britta Simon in Questetra BPM Suite, seguire questa procedura:**

1. Accedere al sito aziendale di Questetra BPM Suite come amministratore.
2. Scegliere **Impostazioni di sistema > Elenco utenti > Nuovo utente**. 
3. Nella finestra di dialogo New User (Nuovo utente) seguire questa procedura: 
   
    ![Creare un utente test][300] 
   
    a. Nella casella di testo **Name** (Nome) digitare il **nome** dell'utente **britta.simon@contoso.com**.
   
    b. Nella casella di testo **Email** (Indirizzo di posta elettronica) digitare l'**indirizzo di posta elettronica** dell'utente **britta.simon@contoso.com**
   
    c. Nella casella di testo **Password** digitare una **password** per l'utente.
    
    d. Fare clic su **Add new user**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione si abilita Britta Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Questetra BPM Suite.

![Assegna utente][200] 

**Per assegnare Britta Simon a Questetra BPM Suite, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Questetra BPM Suite**.

    ![Questetra BPM Suite nell'elenco delle applicazioni](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202] 

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Questetra BPM Suite nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Questetra BPM Suite.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 


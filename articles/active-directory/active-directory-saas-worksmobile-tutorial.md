---
title: 'Esercitazione: Integrazione di Azure Active Directory con riga WORKS | Documenti Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e il funzionamento di riga.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 725f32fd-d0ad-49c7-b137-1cc246bf85d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jeedes
ms.openlocfilehash: b15f5d02a7baff0bc56f2928f73822214c595ab0
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-line-works"></a>Esercitazione: Integrazione di Azure Active Directory con il funzionamento di riga

In questa esercitazione si informazioni su come integrare il funzionamento di riga con Azure Active Directory (Azure AD).

Integrazione di riga funziona con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso al funzionamento di riga.
- È possibile consentire agli utenti di automaticamente ottenere firmato-on per il funzionamento di riga (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con il funzionamento di riga, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per il funzionamento di riga single sign-on

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di funzionamento di riga dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-line-works-from-the-gallery"></a>Aggiunta di funzionamento di riga dalla raccolta
Per configurare l'integrazione di riga funziona in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite WORKS riga dalla raccolta.

**Per aggiungere il funzionamento di riga dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **WORKS riga**selezionare **WORKS riga** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![FUNZIONAMENTO di riga nell'elenco dei risultati](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con il funzionamento di riga in base a un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in linea funziona a un utente in Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente AD Azure e il relativo utente in linea funziona.

In linea funziona, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure single sign-on AD con il funzionamento di riga, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di prova di funzionamento di riga](#create-a-line-works-test-user)**  : per hanno un equivalente di Britta Simon in linea funziona collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione riga WORKS.

**Per configurare Azure AD single sign-on con il funzionamento di riga, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **WORKS riga** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_samlbase.png)

3. Nel **riga funzionamento del dominio e gli URL** sezione, eseguire la procedura seguente:

    ![RIGA di funzionamento del dominio e gli URL single sign-on, informazioni](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://auth.worksmobile.com/d/login/{domain}/?userId={ID@domain}`

    b. Nella casella di testo **Identificatore** digitare il valore: `worksmobile.com`

    > [!NOTE] 
    > Poiché non è reale, è necessario aggiornare questo valore con l'URL di accesso Sign-On effettivo. Contatto [team di supporto Client di riga funziona](mailto:dl_ssoinfo@worksmobile.com) per ottenere questo valore.

4. Nella sezione **Certificato di firma SAML** fare clic su **Certificato (base)** e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-lineworks-tutorial/tutorial_general_400.png)

6. Nel **riga WORKS configurazione** fare clic su **configurare WORKS riga** per aprire **Configura sign-on** finestra. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di funzionamento di riga](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_configure.png) 

7. Per configurare l'accesso single sign-on in **WORKS riga** lato, è necessario inviare scaricato **certificato file, URL Sign-Out, ID entità SAML e SAML Single Sign-On Service URL** a [supporta il funzionamento di riga Team](mailto:dl_ssoinfo@worksmobile.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-lineworks-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-lineworks-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-lineworks-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-lineworks-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-line-works-test-user"></a>Creare un utente di prova di funzionamento di riga

In questa sezione si crea un utente denominato Britta Simon in linea funziona. Rivolgersi [team di supporto di riga funziona](mailto:dl_ssoinfo@worksmobile.com) per aggiungere gli utenti nella piattaforma di funzionamento di riga.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso per il funzionamento di riga.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a riga, eseguire i passaggi seguenti:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **WORKS riga**.

    ![Il collegamento di funzionamento di riga nell'elenco delle applicazioni](./media/active-directory-saas-lineworks-tutorial/tutorial_lineworks_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si sceglie la tessera di riga funziona nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione di funzionamento di riga.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lineworks-tutorial/tutorial_general_203.png


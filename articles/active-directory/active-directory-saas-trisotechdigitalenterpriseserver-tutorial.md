---
title: 'Esercitazione: Integrazione di Azure Active Directory con Trisotech Digital Enterprise Server | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jeedes
ms.openlocfilehash: 82e88b0b2b7f04f2849bf5c3a780df3c8f1c9849
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Esercitazione: Integrazione di Azure Active Directory con Trisotech Digital Enterprise Server

Questa esercitazione descrive come integrare Trisotech Digital Enterprise Server con Azure Active Directory (Azure AD).

L'integrazione di Trisotech Digital Enterprise Server con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Trisotech Digital Enterprise Server.
- È possibile abilitare l'accesso automatico degli utenti a Trisotech Digital Enterprise Server (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Trisotech Digital Enterprise Server, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Trisotech Digital Enterprise Server abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Trisotech Digital Enterprise Server dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Aggiunta di Trisotech Digital Enterprise Server dalla raccolta
Per configurare l'integrazione di Trisotech Digital Enterprise Server in Azure AD, è necessario aggiungere Trisotech Digital Enterprise Server dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Trisotech Digital Enterprise Server dalla raccolta, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Trisotech Digital Enterprise Server**, selezionare **Trisotech Digital Enterprise Server** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Trisotech Digital Enterprise Server nell'elenco dei risultati](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server usando un utente di test di nome "Britta Simon".

Per il corretto funzionamento dell'accesso Single Sign-On, Azure AD deve poter identificare quale sia la controparte di un utente di Azure AD in Trisotech Digital Enterprise Server. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Trisotech Digital Enterprise Server.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test per Trisotech Digital Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)**: per avere una controparte di Britta Simon in Trisotech Digital Enterprise Server collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e l'accesso viene quindi configurato nell'applicazione Trisotech Digital Enterprise Server.

**Per configurare l'accesso Single Sign-On di Azure AD con Trisotech Digital Enterprise Server, eseguire queste operazioni:**

1. Nella pagina di integrazione dell'applicazione **Trisotech Digital Enterprise Server** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

3. Nella sezione **URL e dominio Trisotech Digital Enterprise Server** eseguire queste operazioni:

    ![Informazioni su URL e dominio di Trisotech Digital Enterprise Server per l'accesso Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<companyname>.trisotech.com`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'identificatore e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team del supporto clienti di Trisotech Digital Enterprise Server](mailto:support@trisotech.com). 

4. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

5. Per generare l'URL dei **metadati**, seguire questa procedura:

    a. Fare clic su **Registrazioni per l'app**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_appregistrations.png)
   
    b. Fare clic su **Endpoint** per aprire la finestra di dialogo **Endpoint**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_endpointicon.png)

    c. Fare clic sul pulsante Copia per copiare l'URL del **DOCUMENTO METADATI FEDERAZIONE** e incollarlo nel Blocco note.
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_endpoint.png)
     
    d. Passare ora alla pagina delle proprietà di **Trisotech Digital Enterprise Server**, copiare il valore di **ID applicazione** usando il pulsante **Copia** e incollarlo nel Blocco note.
 
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_appid.png)

    e. Generare l'**URL dei metadati** usando il modello seguente: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. In un'altra finestra del Web browser accedere al sito aziendale di configurazione di Trisotech Digital Enterprise Server come amministratore.

7. Fare clic sull'**icona Menu** e quindi selezionare **Administration** (Amministrazione).

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user1.png)

8. Selezionare **User Provider** (Provider utenti).

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user2.png)

9. Nella sezione **User Provider Configurations** (Configurazioni provider utenti) eseguire queste operazioni:

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Selezionare **Secured Assertion Markup Language 2 (SAML 2)** nell'elenco a discesa **Authentication Method** (Metodo di autenticazione).

    b. Nella casella di testo **Metadata URL** (URL metadati) incollare il valore di **URL metadati** copiato dal portale di Azure.

    c. Nella casella di testo **Application ID** (ID applicazione) digitare l'URL usando il modello seguente: `https://<companyname>.trisotech.com`.

    d. Fare clic su **Save**

    e. Immettere il nome di dominio nella casella di testo **Allowed Domains (empty means everyone)** (Domini consentiti - Se vuoto significa tutti i domini). Vengono automaticamente assegnate le licenze per gli utenti che corrispondono ai domini consentiti

    f. Fare clic su **Save**

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Creare un utente di test per Trisotech Digital Enterprise Server

Questa sezione descrive come creare un utente di nome Britta Simon in Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a Trisotech Digital Enterprise Server.
>[!Note]
>Per creare un utente manualmente, contattare il [team del supporto clienti di Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Trisotech Digital Enterprise Server.

![Assegnare il ruolo utente][200] 

**Per aggiungere Britta Simon a Trisotech Digital Enterprise Server, eseguire queste operazioni:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni selezionare **Trisotech Digital Enterprise Server**.

    ![Collegamento Trisotech Digital Enterprise Server nell'elenco delle applicazioni](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Trisotech Digital Enterprise Server nel pannello di accesso, viene automaticamente eseguito l'accesso all'applicazione Trisotech Digital Enterprise Server.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png


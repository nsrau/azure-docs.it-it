---
title: 'Esercitazione: Integrazione di Azure Active Directory con Software di gestione della Privacy OneTrust | Documenti Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e il Software di gestione di OneTrust sulla Privacy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 89d657e754f6908a3afbb615a2158c9d7f3380f9
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Esercitazione: Integrazione di Azure Active Directory con Software di gestione della Privacy OneTrust

In questa esercitazione si informazioni su come integrare i Software di gestione della Privacy OneTrust con Azure Active Directory (Azure AD).

Integrazione di Software di gestione della Privacy OneTrust con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso al Software di gestione di OneTrust sulla Privacy.
- È possibile abilitare gli utenti per automaticamente ottenere firmato OneTrust sulla Privacy di Software di gestione (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Software di gestione della Privacy OneTrust, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Un Software di gestione della Privacy OneTrust single sign-sottoscrizione abilitata

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Software di gestione della Privacy OneTrust dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Aggiunta di Software di gestione della Privacy OneTrust dalla raccolta
Per configurare l'integrazione di Software di gestione della Privacy OneTrust in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite Software di gestione della Privacy OneTrust dalla raccolta.

**Per aggiungere il Software di gestione Privacy OneTrust dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Software di gestione della Privacy OneTrust**selezionare **Software di gestione della Privacy OneTrust** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere il applicazione.

    ![Software di gestione di Privacy OneTrust nell'elenco dei risultati](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con il Software di gestione OneTrust sulla Privacy in base a un utente di test denominato "Britta Simon".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente nel Software di gestione della Privacy OneTrust a un utente in Azure AD. In altre parole, una relazione di collegamento tra un utente AD Azure e il relativo utente in Software di gestione della Privacy OneTrust deve essere stabilito.

Nel Software di gestione OneTrust sulla Privacy, assegnare il valore del **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure AD single sign-on con Software di gestione della Privacy OneTrust, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test del Software di gestione della Privacy OneTrust](#create-a-onetrust-privacy-management-software-test-user)**  - avere un equivalente di Britta Simon nel Software di gestione di Privacy OneTrust collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione OneTrust Software di gestione della Privacy.

**Per configurare Azure AD single sign-on con Software di gestione della Privacy OneTrust, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **Software di gestione della Privacy OneTrust** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_samlbase.png)

3. Nel **OneTrust Privacy dominio del Software di gestione e gli URL** sezione, eseguire la procedura seguente se si desidera configurare l'applicazione in **IDP** modalità iniziata da:

    ![Dominio del Software di gestione OneTrust sulla Privacy e gli URL single sign-on, informazioni](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://www.onetrust.com/saml2`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.onetrust.com/auth/consumerservice`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Dominio del Software di gestione OneTrust sulla Privacy e gli URL single sign-on, informazioni](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, Aggiornare questi valori con l'URL di risposta e l'URL di accesso effettivo. Contatto [team di supporto di Client del Software di gestione Privacy OneTrust](mailto:support@onetrust.com) per ottenere questi valori. 

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-onetrust-tutorial/tutorial_general_400.png)

7. Per configurare l'accesso single sign-on in **Software di gestione della Privacy OneTrust** lato, è necessario inviare scaricato **Metadata XML** a [team di supporto di Software di gestione della Privacy OneTrust](mailto:support@onetrust.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-onetrust-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-onetrust-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-onetrust-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-onetrust-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Creare un utente di test del Software di gestione della Privacy OneTrust

L'obiettivo di questa sezione consiste nel creare un utente denominato Britta Simon in OneTrust Software di gestione della Privacy. Software di gestione della Privacy OneTrust supporta il provisioning di just-in-time, che è per impostazione predefinita abilitato. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accedere a Software di gestione della Privacy OneTrust se non esiste ancora, viene creato un nuovo utente.

>[!Note]
>Se è necessario creare manualmente un utente, contattare [team di supporto di Software di gestione della Privacy OneTrust](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso al Software di gestione di OneTrust sulla Privacy.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon OneTrust Privacy software di gestione, eseguire i passaggi seguenti:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Software di gestione della Privacy OneTrust**.

    ![Il collegamento di Software di gestione della Privacy OneTrust nell'elenco delle applicazioni](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Software di gestione della Privacy OneTrust nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione OneTrust Software di gestione della Privacy.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_203.png


---
title: 'Esercitazione: Integrazione di Azure Active Directory con OneTrust Privacy Management Software | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e OneTrust Privacy Management Software.
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
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Esercitazione: Integrazione di Azure Active Directory con OneTrust Privacy Management Software

Questa esercitazione descrive come integrare OneTrust Privacy Management Software con Azure Active Directory (Azure AD).

L'integrazione di OneTrust Privacy Management Software con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a OneTrust Privacy Management Software.
- È possibile abilitare gli utenti per l'accesso automatico a OneTrust Privacy Management Software (Single Sign-On) con gli account Azure AD personali.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con OneTrust Privacy Management Software, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Una sottoscrizione abilitata per l'accesso Single Sign-On a OneTrust Privacy Management Software

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di OneTrust Privacy Management Software dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Aggiunta di OneTrust Privacy Management Software dalla raccolta
Per configurare l'integrazione di OneTrust Privacy Management Software in Azure AD, è necessario aggiungere OneTrust Privacy Management Software dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere OneTrust Privacy Management Software dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **OneTrust Privacy Management Software**, selezionare **OneTrust Privacy Management Software** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![OneTrust Privacy Management Software nell'elenco dei risultati](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con OneTrust Privacy Management Software usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di OneTrust Privacy Management Software che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in OneTrust Privacy Management Software.

Per stabilire la relazione di collegamento, in OneTrust Privacy Management Software assegnare il valore del **nome utente** di Azure AD come valore dell'attributo **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con OneTrust Privacy Management Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di OneTrust Privacy Management Software](#create-a-onetrust-privacy-management-software-test-user)**: per avere una controparte di Britta Simon in OneTrust Privacy Management Software collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione OneTrust Privacy Management Software.

**Per configurare l'accesso Single Sign-On di Azure AD con OneTrust Privacy Management Software, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **OneTrust Privacy Management Software** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_samlbase.png)

3. Nella sezione **URL e dominio OneTrust Privacy Management Software** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni sull'accesso Single Sign-On a URL e dominio di OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://www.onetrust.com/saml2`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.onetrust.com/auth/consumerservice`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni sull'accesso Single Sign-On a URL e dominio di OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di OneTrust Privacy Management Software](mailto:support@onetrust.com). 

5. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_certificate.png) 

6. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-onetrust-tutorial/tutorial_general_400.png)

7. Per configurare l'accesso Single Sign-On sul lato **OneTrust Privacy Management Software**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di OneTrust Privacy Management Software](mailto:support@onetrust.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

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
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Creare un utente di test di OneTrust Privacy Management Software

Questa sezione descrive come creare un utente di nome Britta Simon in OneTrust Privacy Management Software. OneTrust Privacy Management Software supporta il provisioning just-in-time, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Durante un tentativo di accesso a OneTrust Privacy Management Software verrà creato un nuovo utente, se non esiste già.

>[!Note]
>Se è necessario creare manualmente un utente, contattare [team di supporto di OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a OneTrust Privacy Management Software.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a OneTrust Privacy Management Software, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **OneTrust Privacy Management Software**.

    ![Collegamento di OneTrust Privacy Management Software nell'elenco delle applicazioni](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro OneTrust Privacy Management Software nel pannello di accesso, si accederà automaticamente all'applicazione OneTrust Privacy Management Software.
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


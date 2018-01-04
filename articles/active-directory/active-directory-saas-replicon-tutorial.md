---
title: 'Esercitazione: Integrazione di Azure Active Directory con Replicon | Documentazione Microsoft'
description: Informazioni su come configurare single sign-on tra Azure Active Directory e Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 130e13b0bb801c498f6de6e4bfd61bd3d2c3bf00
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Esercitazione: Integrazione di Azure Active Directory con Replicon

In questa esercitazione informazioni su come integrare Replicon con Azure Active Directory (Azure AD).

Integrazione di Replicon con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD che ha accesso a Replicon.
- È possibile consentire agli utenti di automaticamente ottenere firmato a Replicon (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Replicon, è necessario quanto segue:

- Sottoscrizione di Azure AD
- Sottoscrizione abilitata per Replicon single sign-on

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Replicon dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-replicon-from-the-gallery"></a>Aggiunta di Replicon dalla raccolta
Per configurare l'integrazione di Replicon in Azure AD, è necessario aggiungere all'elenco di App SaaS gestite Replicon dalla raccolta.

**Per aggiungere Replicon dalla raccolta, eseguire la procedura seguente:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca, digitare **Replicon**selezionare **Replicon** dal pannello risultati quindi fare clic su **Aggiungi** pulsante per aggiungere l'applicazione.

    ![Nell'elenco dei risultati replicon](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione, configurare e testare Azure AD single sign-on con Replicon in base a un utente di test denominato "Laura Giussani".

Per single sign-on a funzionare, Azure AD deve conoscere l'utente corrispondente in Replicon a un utente in Azure AD. In altre parole, è necessario stabilire una relazione di collegamento tra un utente AD Azure e l'utente correlato in Replicon.

In Replicon, assegnare il valore della **nome utente** in Azure AD come valore della **Username** per stabilire la relazione di collegamento.

Per configurare e testare Azure AD single sign-on con Replicon, è necessario completare i seguenti blocchi predefiniti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente test Replicon](#create-a-replicon-test-user)**  - disporre di un equivalente di Britta Simon in Replicon collegato per la rappresentazione di Azure AD dell'utente.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione, si abilita Azure AD single sign-on nel portale di Azure e configurare l'accesso single sign-on nell'applicazione Replicon.

**Per configurare Azure single sign-on AD con Replicon, eseguire la procedura seguente:**

1. Nel portale di Azure, sul **Replicon** pagina di integrazione dell'applicazione, fare clic su **Single sign-on**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_samlbase.png)

3. Nel **Replicon dominio e gli URL** sezione, eseguire la procedura seguente:

    ![URL e replicon dominio single sign-on, informazioni](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_url.png)

    a. Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://na2.replicon.com/<companyname>/saml2/sp-sso/post`

    b. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://global.replicon.com/<companyname>`

    c. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://global.replicon.com/!/saml2/<companyname>/sso/post`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, aggiornarli con l'URL di accesso, l'identificatore e l'URL di risposta effettivi. Contatto [team di supporto Client di Replicon](https://www.replicon.com/customerzone/contact-support) per ottenere questi valori. 

4. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_certificate.png) 

5. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-replicon-tutorial/tutorial_general_400.png)

6. In un'altra finestra del Web browser accedere al sito aziendale di Replicon come amministratore.

7. Per configurare SAML 2.0, eseguire la procedura seguente:
   
    ![Abilita autenticazione SAML](./media/active-directory-saas-replicon-tutorial/ic777805.png "autenticazione SAML abilitare")
    
    a. Per visualizzare il **EnableSAML Authentication2** finestra di dialogo, aggiungere quanto segue all'URL, dopo la chiave dell'azienda:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
    
    * Di seguito è illustrato lo schema dell'URL completo:  
   `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`
   
   b. Fare clic su **+** per espandere la sezione **v20Configuration**.

   c. Fare clic su **+** per espandere la sezione **metaDataConfiguration**.

   d. Fare clic su **Scegli file** per selezionare il file XML dei metadati del provider di identità, quindi scegliere **Invia**.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).
> 

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-replicon-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-replicon-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-replicon-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-replicon-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-a-replicon-test-user"></a>Creare un utente test Replicon

Per consentire agli utenti di Azure AD di accedere a Replicon, è necessario eseguirne il provisioning in Replicon. Nel caso di Replicon, il provisioning è un'attività manuale.

**Per configurare il provisioning utenti, seguire questa procedura:**

1. In una finestra del Web browser accedere al sito aziendale di Replicon come amministratore.

2. Passare ad **Amministrazione \> Utenti**.
   
    ![Utenti](./media/active-directory-saas-replicon-tutorial/ic777806.png "Utenti")

3. Fare clic su **+Aggiungi utente**.
   
    ![Aggiungere un utente](./media/active-directory-saas-replicon-tutorial/ic777807.png "Aggiungere un utente")

4. Nella sezione **Profilo utente** , eseguire la procedura seguente:
   
    ![Profilo utente](./media/active-directory-saas-replicon-tutorial/ic777808.png "Profilo utente")
    
    a. Nel **nome account di accesso** casella di testo, digitare l'indirizzo di posta elettronica dell'utente di Azure AD a cui si desidera eseguire il provisioning come  **BrittaSimon@contoso.com** .
    
    b. In **Tipo di autenticazione** selezionare **SSO**.
    
    c. Nella casella di testo **Reparto** , digitare il reparto dell'utente.

    d. In **Tipo di dipendente** selezionare **Amministratore**.

    e. Fare clic su **Salva profilo utente**.

>[!NOTE]
>È possibile usare qualsiasi altro Replicon utente account strumento di creazione o le API fornite da Replicon per eseguire il provisioning degli account utente di Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione è abilitare Britta Simon utilizzare single sign-on Azure concedendo l'accesso a Replicon.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Replicon, eseguire i passaggi seguenti:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Replicon**.

    ![Il collegamento di Replicon nell'elenco delle applicazioni](./media/active-directory-saas-replicon-tutorial/tutorial_replicon_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Replicon nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione di Replicon.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-replicon-tutorial/tutorial_general_203.png


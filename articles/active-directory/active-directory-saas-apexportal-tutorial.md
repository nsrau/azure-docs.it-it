---
title: 'Esercitazione: Integrazione di Azure Active Directory con Apex Portal | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Apex Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db204a46-6460-4ace-bdbb-4353846723ad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: 69e918f3f8826d7e79f405320676f542e346af7c
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-apex-portal"></a>Esercitazione: Integrazione di Azure Active Directory con Apex Portal

Questa esercitazione descrive come integrare Apex Portal con Azure Active Directory (Azure AD).

L'integrazione di Apex Portal con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere ad Apex Portal.
- È possibile consentire agli utenti di accedere automaticamente ad Apex Portal (Single Sign-On) con i loro account Azure AD.
- È possibile gestire gli account da una posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con Apex Portal, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD.
- Sottoscrizione di Apex Portal abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede i due blocchi predefiniti seguenti:

1. Aggiunta di Apex Portal dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-apex-portal-from-the-gallery"></a>Aggiunta di Apex Portal dalla raccolta
Per configurare l'integrazione di Apex Portal in Azure AD, è necessario aggiungere Apex Portal dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Apex Portal dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Apex Portal**, selezionare **Apex Portal** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Apex Portal nell'elenco risultati](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Apex Portal usando un utente test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve sapere quale utente di Apex Portal è la controparte di un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Apex Portal.

Per stabilire la relazione di collegamento, in Apex Portal assegnare il valore del **nome utente** di Azure AD come valore di **Username**.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Apex Portal, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Apex Portal](#create-an-apex-online-test-user)**: per avere una controparte di Britta Simon in Apex Portal collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Apex Portal.

**Per configurare l'accesso Single Sign-On di Azure AD con Apex Portal, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Apex Portal** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento per la configurazione dell'accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_samlbase.png)

3. Nella sezione **URL e dominio Apex Portal** seguire questa procedura:

    ![Informazioni sull'accesso Single Sign-On a URL e dominio di Apex Portal](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_url.png)

    a. Nella casella di testo **Identificatore** digitare l'URL adottando il modello seguente: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<customer name>.apexanalytix.com/saml/sso.aspx`

    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'identificatore e l'URL di risposta effettivi. Per ottenere tali valori, contattare il [team di supporto di Apex Portal](mailto:support@apexanalytix.com).
 
4. L'applicazione Apex Portal prevede un formato specifico per le asserzioni SAML. È quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione "**Attributi utente**" nella pagina di integrazione dell'applicazione. 

    ![Configura accesso Single Sign-On](./media/active-directory-saas-apexportal-tutorial/attribute.png)

5. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come indicato nell'immagine precedente ed eseguire i passaggi descritti di seguito:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | FIRSTNAME | user.givenname |
    | LASTNAME | user.surname |
    | MAIL | user.mail |    

    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configura accesso Single Sign-On](./media/active-directory-saas-apexportal-tutorial/tutorial_attribute_04.png)

    ![Configura accesso Single Sign-On](./media/active-directory-saas-apexportal-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.
    
    d. Fare clic su **OK**.

6. Nella sezione **Certificato di firma SAML** fare clic su **XML di metadati** e quindi salvare il file dei metadati nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_certificate.png) 

7. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva di Configura accesso Single Sign-On](./media/active-directory-saas-apexportal-tutorial/tutorial_general_400.png)

8. Per configurare l'accesso Single Sign-On sul lato **Apex Portal**, è necessario inviare il file **XML metadati** scaricato al [team di supporto di Apex Portal](mailto:support@apexanalytix.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

> [!TIP]
> Un riepilogo delle istruzioni è disponibile all'interno del [portale di Azure](https://portal.azure.com) durante la configurazione dell'app.  Dopo aver aggiunto l'app dalla sezione **Active Directory > Applicazioni aziendali** è sufficiente fare clic sulla scheda **Single Sign-On** e accedere alla documentazione incorporata tramite la sezione **Configurazione** nella parte inferiore. Altre informazioni sulla funzione di documentazione incorporata sono disponibili in [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985) (Documentazione incorporata di Azure AD).

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-apexportal-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-apexportal-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-apexportal-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-apexportal-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
  
### <a name="create-an-apex-portal-test-user"></a>Creare un utente di test Apex Portal

L'obiettivo di questa sezione è descrivere come creare un utente chiamato Britta Simon in Apex Portal. Apex Portal supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se ancora non esiste un nuovo utente, viene creato durante un tentativo di accesso ad Apex Portal.
 
> [!NOTE]
> Per creare un utente manualmente, è necessario contattare il [team di supporto di Apex Portal](mailto:support@apexanalytix.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione a Britta Simon viene concesso l'accesso ad Apex Portal per abilitarla all'uso dell'accesso Single Sign-On di Azure.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon ad Apex Portal, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Apex Portal**.

    ![Collegamento di Apex Portal nell'elenco delle applicazioni](./media/active-directory-saas-apexportal-tutorial/tutorial_apexonline_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Apex Portal nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Apex Portal.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-apexportal-tutorial/tutorial_general_203.png


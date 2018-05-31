---
title: 'Esercitazione: Integrazione di Azure Active Directory con Infogix Data3Sixty Govern | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Infogix Data3Sixty Govern.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 99b55311293c04905d005604ce20d6816919b31c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341958"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Esercitazione: Integrazione di Azure Active Directory con Infogix Data3Sixty Govern

Questa esercitazione descrive come integrare Infogix Data3Sixty Govern con Azure Active Directory (Azure AD).

L'integrazione di Infogix Data3Sixty Govern con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a Infogix Data3Sixty Govern.
- È possibile abilitare gli utenti per l'accesso automatico a Infogix Data3Sixty Govern (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>prerequisiti

Per configurare l'integrazione di Azure AD con Infogix Data3Sixty Govern, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di Infogix Data3Sixty Govern abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di Infogix Data3Sixty Govern dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Aggiunta di Infogix Data3Sixty Govern dalla raccolta
Per configurare l'integrazione di Infogix Data3Sixty Govern in Azure AD, è necessario aggiungere Infogix Data3Sixty Govern dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere Infogix Data3Sixty Govern dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **Infogix Data3Sixty Govern**, selezionare **Infogix Data3Sixty Govern** nel pannello dei risultati, quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Infogix Data3Sixty Govern nell'elenco risultati](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con Infogix Data3Sixty Govern usando un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di Infogix Data3Sixty Govern che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Infogix Data3Sixty Govern.

Per configurare e testare l'accesso Single Sign-On di Azure AD con Infogix Data3Sixty Govern, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-single-sign-on)**: per consentire agli utenti di usare questa funzionalità.
2. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)**: per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creare un utente di test di Infogix Data3Sixty Govern](#create-an-infogix-data3sixty-govern-test-user)**: per avere una controparte di Britta Simon in Infogix Data3Sixty Govern collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnare l'utente test di Azure AD](#assign-the-azure-ad-test-user)**: per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Testare l'accesso Single Sign-On](#test-single-sign-on)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurare l'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione Infogix Data3Sixty Govern.

**Per configurare l'accesso Single Sign-On di Azure AD con Infogix Data3Sixty Govern, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **Infogix Data3Sixty Govern** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Single Sign-On** selezionare **Accesso basato su SAML** per **Modalità** per abilitare l'accesso Single Sign-On.
 
    ![Finestra di dialogo Single Sign-On](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_samlbase.png)

3. Nella sezione **URL e dominio Infogix Data3Sixty Govern** seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **IDP**:

    ![Informazioni su URL e dominio per Single Sign-On di Infogix Data3Sixty Govern](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_url.png)

    a. Nella casella di testo **Identificatore** digitare un URL: `https://data3sixty.com/ui`

    b. Nella casella di testo **URL di risposta** digitare l'URL usando il modello seguente: `https://<subdomain>.data3sixty.com/sso/acs`

4. Selezionare **Mostra impostazioni URL avanzate** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    ![Informazioni su URL e dominio per Single Sign-On di Infogix Data3Sixty Govern](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_url1.png)

    Nella casella di testo **URL di accesso** digitare l'URL usando il modello seguente: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'URL di risposta e l'URL di accesso effettivi. Per ottenere questi valori, contattare il [team di supporto clienti di Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

5. L'applicazione Infogix Data3Sixty Govern prevede che le asserzioni SAML abbiano un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente** nella pagina di integrazione dell'applicazione. La schermata seguente illustra un esempio relativo a questa operazione.
    
    ![Configurare l'attributo Single Sign-On](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_attribute.png)
    
6. Nella sezione **Attributi utente** della finestra di dialogo **Single Sign-On** configurare l'attributo del token SAML come illustrato nell'immagine e seguire questa procedura:
    
    | Nome attributo | Valore attributo |
    | ------------------- | -------------------- |    
    | firstname           | user.givenname |
    | lastname        | user.surname |
    | username       | user.mail    |
    
    a. Fare clic su **Aggiungi attributo** per aprire la finestra di dialogo **Aggiungi attributo**.

    ![Configurare l'aggiunta del Single Sign-On](./media/active-directory-saas-infogix-tutorial/tutorial_attribute_04.png)

    ![Configurare l'aggiunta attributo Single Sign-On](./media/active-directory-saas-infogix-tutorial/tutorial_attribute_05.png)

    b. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    c. Nell'elenco **Valore** digitare il valore dell'attributo indicato per la riga.

    d. Lasciare vuota la casella **Spazio dei nomi**.
    
    e. Fare clic su **OK**.

7. Nella sezione **Certificato di firma SAML** fare clic su **Certificate (Raw)** (Certificato (base)) e quindi salvare il file del certificato nel computer.

    ![Collegamento di download del certificato](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_certificate.png)

8. Fare clic sul pulsante **Salva** .

    ![Pulsante Salva per la configurazione dell'accesso Single Sign-On](./media/active-directory-saas-infogix-tutorial/tutorial_general_400.png)
    
9. Nella sezione **Configurazione di Infogix Data3Sixty Govern** fare clic su **Configura Infogix Data3Sixty Govern** per aprire la finestra **Configura accesso**. Copiare l'**URL di disconnessione, l'ID di entità SAML e l'URL del servizio Single Sign-On SAML** dalla sezione **Riferimento rapido.**

    ![Configurazione di Infogix Data3Sixty Govern](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_configure.png) 

10. Per configurare l'accesso Single Sign-On sul lato **Infogix Data3Sixty Govern**, è necessario inviare il **certificato (base) scaricato, l'URL di disconnessione, l'ID entità SAML e l'URL del servizio Single Sign-On SAML** al [team di supporto di Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-an-azure-ad-test-user"></a>Creare un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

   ![Creare un utente test di Azure AD][100]

**Per creare un utente test in Azure AD, eseguire la procedura seguente:**

1. Nel portale di Azure fare clic sul pulsante **Azure Active Directory** nel riquadro sinistro.

    ![Pulsante Azure Active Directory](./media/active-directory-saas-infogix-tutorial/create_aaduser_01.png)

2. Per visualizzare l'elenco di utenti, passare a **Utenti e gruppi** e quindi fare clic su **Tutti gli utenti**.

    ![Collegamenti "Utenti e gruppi" e "Tutti gli utenti"](./media/active-directory-saas-infogix-tutorial/create_aaduser_02.png)

3. Per aprire la finestra di dialogo **Utente** fare clic su **Aggiungi** nella parte superiore della finestra di dialogo **Tutti gli utenti**.

    ![Pulsante Aggiungi](./media/active-directory-saas-infogix-tutorial/create_aaduser_03.png)

4. Nella finestra di dialogo **Utente** seguire questa procedura:

    ![Finestra di dialogo Utente](./media/active-directory-saas-infogix-tutorial/create_aaduser_04.png)

    a. Nella casella **Nome** digitare **BrittaSimon**.

    b. Nella casella **Nome utente** digitare l'indirizzo di posta elettronica dell'utente Britta Simon.

    c. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.

    d. Fare clic su **Crea**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Creare un utente di test di Infogix Data3Sixty Govern


L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Infogix Data3Sixty Govern. Infogix Data3Sixty Govern supporta il provisioning JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se non esiste già, un nuovo utente viene creato durante un tentativo di accesso a Infogix Data3Sixty Govern.

>[!Note]
>Se è necessario creare un utente manualmente, contattare il [team di supporto di Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a Infogix Data3Sixty Govern.

![Assegnare il ruolo utente][200] 

**Per assegnare Britta Simon a Infogix Data3Sixty Govern, seguire questa procedura:**

1. Nel portale di Azure aprire la visualizzazione delle applicazioni e quindi la visualizzazione delle directory e passare ad **Applicazioni aziendali**, quindi fare clic su **Tutte le applicazioni**.

    ![Assegna utente][201] 

2. Nell'elenco di applicazioni selezionare **Infogix Data3Sixty Govern**.

    ![Collegamento Infogix Data3Sixty Govern nell'elenco di applicazioni](./media/active-directory-saas-infogix-tutorial/tutorial_infogix_app.png)  

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Collegamento "Utenti e gruppi"][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Riquadro Aggiungi assegnazione][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti.

6. Fare clic sul pulsante **Seleziona** nella finestra di dialogo **Utenti e gruppi**.

7. Fare clic sul pulsante **Assegna** nella finestra di dialogo **Aggiungi assegnazione**.
    
### <a name="test-single-sign-on"></a>Testare l'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro Infogix Data3Sixty Govern nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione Infogix Data3Sixty Govern.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al Pannello di accesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-infogix-tutorial/tutorial_general_203.png


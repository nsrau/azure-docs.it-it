---
title: 'Esercitazione: Integrazione di Azure Active Directory con TextMagic | Microsoft Docs'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a91d6878f509f47f029a3b8aaf7bbbe4e6f35435
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57869796"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Esercitazione: Integrazione di Azure Active Directory con TextMagic

Questa esercitazione descrive come integrare TextMagic con Azure Active Directory (Azure AD).

L'integrazione di TextMagic con Azure AD offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi può accedere a TextMagic.
- È possibile abilitare gli utenti per l'accesso automatico a TextMagic (Single Sign-On) con i propri account Azure AD.
- È possibile gestire gli account in un'unica posizione centrale: il portale di Azure.

Per altre informazioni sull'integrazione di app SaaS con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure AD con TextMagic, sono necessari gli elementi seguenti:

- Sottoscrizione di Azure AD
- Sottoscrizione di TextMagic abilitata per l'accesso Single Sign-On

> [!NOTE]
> Non è consigliabile usare un ambiente di produzione per testare i passaggi di questa esercitazione.

A questo scopo, è consigliabile seguire le indicazioni seguenti:

- Non usare l'ambiente di produzione a meno che non sia necessario.
- Se non è disponibile un ambiente di valutazione di Azure AD, è possibile [ottenere una versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione viene eseguito il test dell'accesso Single Sign-On di Azure AD in un ambiente di test. Lo scenario descritto in questa esercitazione prevede le due fasi fondamentali seguenti:

1. Aggiunta di TextMagic dalla raccolta
2. Configurazione e test dell'accesso Single Sign-On di Azure AD

## <a name="adding-textmagic-from-the-gallery"></a>Aggiunta di TextMagic dalla raccolta

Per configurare l'integrazione di TextMagic in Azure AD, è necessario aggiungere TextMagic dalla raccolta al proprio elenco di app SaaS gestite.

**Per aggiungere TextMagic dalla raccolta, seguire questa procedura:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro. 

    ![Pulsante Azure Active Directory][1]

2. Passare ad **Applicazioni aziendali**. Andare quindi a **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali][2]
    
3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione][3]

4. Nella casella di ricerca digitare **TextMagic**, nel pannello dei risultati selezionare **TextMagic** e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![TextMagic nell'elenco risultati](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurare e testare l'accesso Single Sign-On di Azure AD

In questa sezione viene configurato e testato l'accesso Single Sign-On di Azure AD con TextMagic in base a un utente di test di nome "Britta Simon".

Per il funzionamento dell'accesso Single Sign-On, Azure AD deve conoscere qual è l'utente di TextMagic che corrisponde a un utente di Azure AD. In altre parole, deve essere stabilita una relazione di collegamento tra un utente di Azure AD e l'utente correlato in TextMagic.

Per configurare e testare l'accesso Single Sign-On di Azure AD con TextMagic, è necessario completare i blocchi predefiniti seguenti:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : per abilitare gli utenti all'utilizzo di questa funzionalità.
2. **[Creazione di un utente test di Azure AD](#creating-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente Britta Simon.
3. **[Creazione di un utente di test di TextMagic](#creating-a-textmagic-test-user)**: per avere una controparte di Britta Simon in TextMagic collegata alla rappresentazione dell'utente in Azure AD.
4. **[Assegnazione dell'utente test di Azure AD](#assigning-the-azure-ad-test-user)** : per abilitare Britta Simon all'uso dell'accesso Single Sign-On di Azure AD.
5. **[Test dell'accesso Single Sign-On](#testing-single-sign-on)**: per verificare se la configurazione funziona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione dell'accesso Single Sign-On di Azure AD

In questa sezione viene abilitato l'accesso Single Sign-On di Azure AD nel portale di Azure e viene configurato l'accesso Single Sign-On nell'applicazione TextMagic.

**Per configurare Single Sign-On di Azure AD con TextMagic, seguire questa procedura:**

1. Nella pagina di integrazione dell'applicazione **TextMagic** del portale di Azure fare clic su **Single Sign-On**.

    ![Collegamento Configura accesso Single Sign-On][4]

2. Nella finestra di dialogo **Selezionare un metodo di accesso Single Sign-On** fare clic su **Seleziona** per la modalità **SAML** per abilitare l'accesso Single Sign-On.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona **Modifica** per aprire la finestra di dialogo **Configurazione SAML di base**.

    ![Configure Single Sign-On](common/editconfigure.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    ![Informazioni su URL e dominio per l'accesso Single Sign-On di TextMagic](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Nella casella di testo **Identificatore** digitare un URL: `https://my.textmagic.com/saml/metadata`

5. L'applicazione TextMagic prevede un formato specifico per le asserzioni SAML. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori di questi attributi dalla sezione **Attributi utente e attestazioni** nella pagina di integrazione dell'applicazione. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante **Modifica** per aprire la finestra di dialogo **Attributi utente e attestazioni**.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. Nella sezione **Attestazioni utente** della finestra di dialogo **Attributi utente e attestazioni** configurare l'attributo del token SAML come mostrato nell'immagine precedente e seguire questa procedura:

    | NOME  | Source Attribute  | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. Fare clic sull'icona **Modifica** per modificare **Valore identificatore nome** da **user.userprinicipalname** a **user.mail**.

    ![Attributo di TextMagic](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Fare clic su **Aggiungi nuova attestazione** per aprire la finestra di dialogo **Gestisci attestazioni utente**.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. Nella casella di testo **Nome** digitare il nome dell'attributo indicato per la riga.

    d. Immettere il valore **Nome spazio dei nomi**.

    e. Per Origine selezionare **Attributo**.

    f. Nell'elenco **Attributo di origine** selezionare il valore dell'attributo indicato per la riga.

    g. Fare clic su **OK**.

    h. Fare clic su **Save**. 

7. Nella pagina **Certificato di firma SAML**, nella sezione **Certificato di firma SAML** fare clic su **Scarica** per scaricare **Certificato (Base64)** e salvare il file di certificato nel computer in uso.

    ![Collegamento di download del certificato](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. Nella sezione **Configura TextMagic** copiare l'URL appropriato in base alle esigenze.

    a. URL di accesso

    b. Identificatore di Azure AD

    c. URL di chiusura sessione

    ![Configurazione di TextMagic](common/configuresection.png)

9. In un'altra finestra del Web browser accedere al sito aziendale di TextMagic come amministratore.

10. Selezionare **Account settings** (Impostazioni account) sotto il nome utente.

    ![Configurazione di TextMagic](./media/textmagic-tutorial/config1.png)

11. Fare clic sulla scheda **Single Sign-On (SSO)** e completare i campi seguenti:  
    
    ![Configurazione di TextMagic](./media/textmagic-tutorial/config2.png)

    a. Nella casella di testo  **Identity provider Entity ID**  (ID entità del provider di identità) incollare il valore di  **Identificatore di Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo  **Identity provider SSO URL:**  (URL SSO del provider di identità) incollare il valore di  **URL di accesso** copiato dal portale di Azure.

    c. Nella casella di testo  **Identity provider SLO URL:**  (URL SLO provider di identità) incollare il valore di  **URL disconnessione** copiato dal portale di Azure.

    d. Aprire nel Blocco note il **certificato con codifica Base 64** scaricato dal portale di Azure, copiarne il contenuto negli Appunti e incollarlo nella casella di testo  **Public x509 certificate**  (Certificato X509 pubblico).

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente test di Azure AD

Questa sezione descrive come creare un utente test denominato Britta Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure, selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.

    ![Creare un utente di Azure AD][100]

2. Selezionare **Nuovo utente** in alto nella schermata.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_01.png) 

3. In Proprietà utente seguire questa procedura.

    ![Creazione di un utente test di Azure AD](common/create_aaduser_02.png)

    a. Nel campo **Nome** immettere **BrittaSimon**.
  
    b. Nel **nome utente** , digitare **brittasimon\@yourcompanydomain.extension**  
    Ad esempio: BrittaSimon@contoso.com

    c. Selezionare **Proprietà**, selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella Password.

    d. Selezionare **Create**.
  
### <a name="creating-a-textmagic-test-user"></a>Creare un utente di test di TextMagic

L'applicazione supporta il **provisioning utenti JIT** e dopo l'autenticazione gli utenti verranno automaticamente creati nell'applicazione. È necessario compilare le informazioni una sola volta al primo accesso per attivare l'account secondario nel sistema.
Non è necessario alcun intervento dell'utente in questa sezione.

### <a name="assigning-the-azure-ad-test-user"></a>Assegnazione dell'utente test di Azure AD

In questa sezione Britta Simon viene abilitata per l'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a TextMagic.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Assegna utente][201]

2. Nell'elenco delle applicazioni selezionare **TextMagic**.

    ![Configure Single Sign-On](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. Scegliere **Utenti e gruppi** dal menu a sinistra.

    ![Assegna utente][202]

4. Fare clic sul pulsante **Aggiungi**. Selezionare quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.

    ![Assegna utente][203]

5. Nella finestra di dialogo **Utenti e gruppi** selezionare **Britta Simon** nell'elenco Utenti e quindi fare clic sul pulsante **Seleziona** in basso nella schermata.

6. Nella finestra di dialogo **Aggiungi assegnazione** selezionare il pulsante **Assegna**.

### <a name="testing-single-sign-on"></a>Test dell'accesso Single Sign-On

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD usando il pannello di accesso.

Quando si fa clic sul riquadro TextMagic nel pannello di accesso, si dovrebbe accedere automaticamente all'applicazione TextMagic.
Per altre informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](tutorial-list.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
